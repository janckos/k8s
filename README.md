## Descripción del proyecto
El proyecto pretende definir las bases de configuración y despliegue de aplicaciones web que utilizan Laravel como framework de desarrollo y una base de datos relacional de postgreSQL.
## Estructura del repositorio
| Directorio/Archivo  | Descripción|
| ------------- |:-------------:|
| [docker-compose.yaml](https://github.com/janckos/cloud/blob/master/docker-compose.yaml)      | Contiene las instrucciones para la configuración de servicios/imágenes.    |
| [data/](https://github.com/janckos/cloud/tree/master/data)      |Volumen: Contiene la data de postgres.     |
| [Dockerfile](https://github.com/janckos/cloud/blob/master/Dockerfile)   | Contiene las instrucciones para configurar un contenedor con php/apache y librerías necesarias para una aplicación/laravel.     |
| [src/](https://github.com/janckos/cloud/tree/master/src)      | Volumen: Contiene el código fuente de la aplicación/laravel y está vinculado a la ruta de despliegue de aplicaciones de apache.     |
## Guía de despliegue
Clonar el repositorio en un directorio local:\
``
git clone https://github.com/janckos/cloud.git
``

Dentro del repositorio local, construir la imagen:\
``
docker compose build
``

Ejecutar los servicios en modo desatendido:\
``
docker compose up -d
``

## Instrucciones para ejecutar
Instalar dependencias del la aplicación dentro del contenedor vía Composer:
```shell
docker compose exec phpa composer update
```

Generar la llave de la aplicación/laravel:
```shell
docker compose exec phpa php artisan key:generate
```

Ejecutar las migraciones en la base de datos:
```shell
docker compose exec phpa php artisan migrate
```

## Pruebas del servicio, con ejemplos de respuesta del servicio.

### `GET` /api/tasks – obtener todas las tareas
Execute the next `curl` command to validate the deploy of this endpoint.
```shell
curl http://localhost:9000/public/api/tasks
```
The expected result should looks like:
```shell
{
	“data”:[]
}
```

### `POST` /api/tasks – crear una nueva tarea
Execute the next `curl` command to validate the deploy of this endpoint.
```shell
curl -X POST \
	'http://localhost:9000/public/api/tasks' \
	-H "Content-Type: application/json" \
	-d ‘{
		"title":"New Record",
		"description":"This is a new record"
	}’
```
The expected result should looks like:
```shell
{
	"message":"Task created successfully!",
	"data": {"title":"New Record", "description": "This i s a new record",
	"updated_at":"2023-10-18T01:03:13.000000","created_at":"2023-10-18T01:03: 13.000000Z", "id":1}
}
```

### `GET` /api/tasks/{id} – obtener una tarea específica
Execute the next `curl` command to validate the deploy of this endpoint.
```shell
curl http://localhost:9000/public/api/tasks/1 
```
The expected result should looks like:
```shell
{
	"data":{
		"id":1,
		"title":"New Record",
		"description": "This is a new record",
		"created_at":"20 23-10-18T01:03:13.0888882",
		"updated_at":"2023-10-18T01:03:13.008000Z"
		}
}
```

### `PUT` /api/tasks/{id} – actualizar una tarea específica
Execute the next `curl` command to validate the deploy of this endpoint.
```shell
curl -X PUT \
	'http://localhost:9000/public/api/tasks/1' \
	-H "Content-Type: application/json" \
	-d ‘{
		"title":"First Record",
		"description":"First record has been updated"
	}’  
```
The expected result should looks like:
```shell
{
	"message":"Task updated successfully!",}
	"data":{
		"id":1,
		"title":"First Record",
		"description":"First record has been updated",
		"created_at":"2023-10-18T01:03:13.000000Z",
		"updated_at":"2023-10-18T16:12:29.000000Z"
	}
}
```

### `DELETE` /api/tasks/{id} – eliminar una tarea específica
Execute the next `curl` command to validate the deploy of this endpoint.
```shell
curl -X DELETE
	'http://localhost:9000/public/api/tasks/4'
```
The expected result should looks like:
```shell
{
	"message": "Task deleted successfully!"
}
```

*El presente contenido irá cambiando a lo largo del diplomado para incorporar lo aprendido.*
## Referencias al proyecto
Imágenes de Docker Hub:
- [php:8.1.0-apache](https://hub.docker.com/layers/library/php/8.1.0-apache/images/sha256-0ebdfb1aff16a9ccb4b4f0613023cad6f5a237a8ed333a455502d9f78257125c?context=explore)
- [postgres](https://hub.docker.com/_/postgres)
- [dpage/pgadmin4](https://hub.docker.com/r/dpage/pgadmin4)

Herramientas:
- [Compose](https://docs.docker.com/compose/)
- [Dockerfile](https://docs.docker.com/engine/reference/builder/)

