## Descripción del proyecto.
El proyecto pretende definir las bases de configuración y despliegue de aplicaciones web que utilizan Laravel como framework de desarrollo y una base de datos relacional de postgreSQL.
## Estructura del repositorio
| Directorio/Archivo  | Descripción|
| ------------- |:-------------:|
| [api.yaml](https://github.com/janckos/k8s/blob/master/api.yaml)   | Contiene la definición de los endpoints de la API.     |
| [Dockerfile](https://github.com/janckos/cloud/blob/master/Dockerfile)   | Contiene las instrucciones para configurar un contenedor con php/apache y librerías necesarias para una aplicación/laravel.     |
| [src/](https://github.com/janckos/cloud/tree/master/src)      | Contiene el código fuente de la aplicación/laravel y está vinculado a la ruta de despliegue de aplicaciones de apache.     |
[resources/manifests/](https://github.com/janckos/k8s/tree/master/resources/manifests)      |Contiene los artefactos yaml de Kubernetes: tareas, pipelines y triggers.     |
## Guía de despliegue
Utiliza el comando kubectl apply para aplicar los archivos YAML (resources/manifests). Esto desplegará la aplicación en el clúster de Kubernetes:

### Database (PostgreSQL)
Deploy the Persistent Volume:\
``
kubectl apply -f db-persistent-volume.yaml
``

Deploy the Persistent Volume Claim:\
``
kubectl apply -f db-volume-claim.yaml
``

Deploy the environment variables needed by the cluster:\
``
kubectl apply -f db-configmap.yaml
``

Create the deployment:\
``
kubectl apply -f db-deployment.yaml
``

Run the service to expose the cluster:\
``
kubectl apply -f db-service.yaml
``
### Laravel app



Create the deployment:\
``
kubectl apply -f laravel-app-deployment.yaml
``


Run the service to expose the cluster:\
``
kubectl apply -f laravel-app-service.yaml
``

## Instrucciones para ejecutar

Ingresar al pod de la aplicación y establecer permisos adecuados a carpetas específicas: 
```shell
chmod 775 vendor/ -R
chmod 775 public/ -R
chmod 777 storage/ -R
```
Ejecutar las migraciones de laravel, necesarias para construir las relaciones en la base de datos: 
```shell
php artisan migrate
```

## Pruebas del servicio, con ejemplos de respuesta del servicio.
Para la realización de pruebas, crear un Pod temporal en el clúster y usar curl para acceder al servicio y verificar que esté funcionando correctamente.
```shell
kubectl run curl --image=radial/busyboxplus:curl -i --tty --rm
```
Dentro del Pod de depuración, ejecuta los siguientes comandos para validar los endpoints.

### `GET` /api/tasks – obtener todas las tareas
Execute the next `curl` command to validate the deploy of this endpoint.
```shell
curl laravel-app-service/public/api/tasks
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
	laravel-app-service/public/api/tasks \
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
curl laravel-app-service/public/api/tasks/2 
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
	laravel-app-service/public/api/tasks \
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
curl -X DELETE laravel-app-service/public/api/tasks/4
```
The expected result should looks like:
```shell
{
	"message": "Task deleted successfully!"
}
```

## Pipeline
Ejecución manual de la Pipeline.

``
kubectl apply -f docker-secret.yaml -n user11
``

``
kubectl apply -f service-account.yaml -n user11
``

``
kubectl apply -f pipeline-clone-ka.yaml -n user11
``

``
kubectl apply -f pipelinerun-clone-ka.yaml -n user11
``

## CI/CD
Ejecución de Pipeline usando un trigger configurado con GitHub-WebHook.

RBAC:\
``
kubectl apply -f trigger-rbac.yaml -n user11
``

Create the trigger template:\
``
kubectl apply -f trigger-template.yaml -n user11
``

Create the trigger binding:\
``
kubectl apply -f trigger-binding.yaml -n user11
``

Create the event listener:\
``
kubectl apply -f event-listener.yaml -n user11
``

Create Ing; or in OCP, expose the event listener svc as route:\
``
oc expose svc/el-tekton-event-listener -n user11
``
## Referencias al proyecto
Imágenes de Docker Hub: 
- [How to Deploy PostgreSQL Instance to Kubernetes](https://sweetcode.io/how-to-deploy-postgresql-instance-to-kubernetes/ )
- [php:8.1.0-apache](https://hub.docker.com/layers/library/php/8.1.0-apache/images/sha256-0ebdfb1aff16a9ccb4b4f0613023cad6f5a237a8ed333a455502d9f78257125c?context=explore)
- [postgres:latest](https://hub.docker.com/layers/library/postgres/latest/images/sha256-3648b6c2ac30de803a598afbaaef47851a6ee1795d74b4a5dcc09a22513b15c9?context=explore)

> cafaray: Project revision
