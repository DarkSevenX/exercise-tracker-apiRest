
# Documentación del API de Ejercicios

Este API permite registrar usuarios, agregar ejercicios, y visualizar los registros de ejercicios. Utiliza **Express**, **Mongoose**, y **MongoDB** para manejar las peticiones y el almacenamiento de datos.

## Dependencias
- **express**: Framework web para manejar las solicitudes HTTP.
- **body-parser**: Middleware para procesar datos del cuerpo de las solicitudes.
- **cors**: Middleware para permitir solicitudes desde diferentes dominios.
- **mongoose**: ODM para interactuar con MongoDB.

## Conexión a la base de datos
Se realiza la conexión a una base de datos MongoDB utilizando la URI definida en `process.env.MONGO_URI`. Mongoose maneja la conexión con las opciones `useNewUrlParser` y `useUnifiedTopology`.

```javascript
mongoose.connect(process.env.MONGO_URI, { useNewUrlParser: true, useUnifiedTopology: true });
```

## Middlewares utilizados
1. **CORS**: Permite solicitudes desde cualquier origen.
   ```javascript
   app.use(cors());
   ```

2. **body-parser**: Procesa los datos en formato URL encoded y JSON.
   ```javascript
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(bodyParser.json());
   ```

3. **Archivos estáticos**: Se sirve el contenido estático desde la carpeta `public`.
   ```javascript
   app.use('/public', express.static(process.cwd() + '/public'));
   ```

## Esquemas de la base de datos

### Usuarios de ejercicio
```javascript
var exerciseUsersSchema = new Schema({
  username: { type: String, unique: true, required: true }
});
```

### Ejercicios
```javascript
var exercisesSchema = new Schema({
  userId: { type: String, required: true },
  description: { type: String, required: true },
  duration: { type: Number, min: 1, required: true },
  date: { type: Date, default: Date.now }
});
```

## Endpoints del API

### Crear usuario (`POST /api/users`)
Crea un nuevo usuario con un `username` único. Retorna un error si el nombre de usuario está vacío o ya existe.
- **Request Body**: `{ "username": "nombre_de_usuario" }`
- **Response**: `{ "_id": "id_del_usuario", "username": "nombre_de_usuario" }`

### Listar usuarios (`GET /api/users`)
Devuelve una lista de todos los usuarios registrados.
- **Response**: `[{ "_id": "id_del_usuario", "username": "nombre_de_usuario" }]`

### Agregar ejercicio (`POST /api/users/:_id/exercises`)
Añade un ejercicio para un usuario existente. Se requiere la descripción, duración y opcionalmente la fecha (si no se proporciona, se usará la fecha actual).
- **Request Body**: `{ "description": "ejemplo", "duration": 30, "date": "YYYY-MM-DD" }`
- **Response**: Devuelve los detalles del ejercicio registrado.

### Ver registro de ejercicios (`GET /api/users/:_id/logs`)
Devuelve el historial de ejercicios de un usuario. Se puede filtrar por fecha (`from`, `to`) y limitar el número de resultados (`limit`).
- **Response**: Devuelve los ejercicios del usuario en el formato:
  ```json
  {
    "_id": "id_del_usuario",
    "username": "nombre_de_usuario",
    "log": [
      { "description": "ejemplo", "duration": 30, "date": "Fecha" }
    ],
    "count": total_ejercicios
  }
  ```

## Manejo de errores
- **404 - Not Found**: Middleware para manejar rutas no encontradas.
- **Errores de validación**: Si ocurre un error de validación en Mongoose, se devuelve un error `400`.

```javascript
app.use((err, req, res, next) => {
  let errCode = err.status || 500;
  let errMessage = err.message || 'Internal Server Error';
  res.status(errCode).type('txt').send(errMessage);
});
```

## Inicio del servidor
El servidor escucha en el puerto definido en `process.env.PORT` o en el puerto `3000` por defecto.

```javascript
const listener = app.listen(process.env.PORT || 3000, () => {
  console.log('Your app is listening on port ' + listener.address().port);
});
```
``` 

Este archivo Markdown explica los conceptos clave del código, desde la configuración inicial, los esquemas de la base de datos, hasta las rutas y su funcionamiento.
