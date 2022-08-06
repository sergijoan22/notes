# Docker

## Contenedor

Paquete de software estandarizado, que permite aislar unos de otros. Comparten el mismo OS kernel. Los contenedores no tienen dentro un SO completo, ya que el core de este (el kernel) está fuera y lo permite compartir con todos los contenedores.

La diferencia entre un contenedor y una maquina virtual es que no hay un SO completo, como si en una MV. Por tanto, los contenedores son muy livianos, ya que la parte de SO de cada contenedor es muy liviana. En un contenedor podemos poner Alpine, que es un SO muy ligero, para aplicaciones que no requieran más. Así, se ahorra recursos. Los contendores pemiten usar más SO a la vez en una máquina.

Cada contenedor tiene un ID, y se pueden referenciar por este.

## Docker

Docker es una plataforma abierta para generar, enviar y correr contenedores.

Para contenedores con Linux, el kernel debe ser de Linux también. Para ello, docker crea una máquina virtual cuyo kernel usan los contenedores.

Docker desktop es un programa para usar Docker en Windows. Muestra los contenedores, imágenes y volúmenes y permite interactuar con ellos. 

Docker Hub es repositorio donde podemos descargar o subir imágenes.

Los contenedores podrían contener virus, pero lo que hay dentro no tiene protestad para ejecutar programas en el host, lo cual los hacen seguros.

## Imagen

Las imágenes son archivos que contienen una distribución de un SO (Sin el kernel ya que usa el del anfitrión), una aplicación y software para usarla. A partir de ella se pueden crear contenedores. Podemos crear la imagen o bajar una en Internet con lo que queramos y añadirle cosas. Las imágenes son un conjunto de capas. Al descargar una imagen aparecen las diferentes capas descargándose, cada una con una identificación. Puede que algunas ya estén en el PC por lo que no se vuelven a descargar. Cada imagen tiene un ID, y se pueden referenciar por este.

## Dockerfile

Archivo con una serie de instrucciones que definen una imagen a crear. El archivo se debe llamar Dockerfile, sin extensión. Cada una de las instrucciones crea una capa a la imagen.

```dockerfile
FROM <image_name> #Elegimos la imagen sobre la que nos vamos a basar
	RUN <command> # Comando que se ejecuta al construir la imagen creando una capa
	WORKDIR <directory> #Especificar directorio donde vamos a trabajar
	COPY <PC_directory> <Docker_directory> # Copiar archivos del PC al contenedor
	COPY . . #Copiar todo del directorio actual al directorio de Docker de WORKDIR
	ENV <variable_name> <variable_value> #Crear variable para el contenedor
	EXPOSE <port> #Abre un puerto del contenedor
	CMD ["<command>","<argument>"] #Comando que se ejecuta al correr un contenedor
```

## Volumen

Un volumen es un directorio del host al que uno o más contenedores tienen acceso. Se pueden modificar desde el host o el contenedor y los cambios se muestran en el otro. Esto permite trabajar con archivos en un contenedor y no perder el progreso al eliminar el contenedor.

Para acceder a los volúmenes siendo Windows el host, ir a la dirección:

```
\\wsl$\docker-desktop-data\version-pack-data\community\docker\volumes\
```

## Descargar una imagen

Podemos descargar una imagen de DockerHub al PC. Esto lo hace también el comando run, así que este comando sirve para asegurarse que se tiene la última versión de una imagen.

`docker pull <image_name>`

## Construir imagen a partir de un Dockerfile

Situarse en el directorio donde esta el Dockerfile.

`docker build -t <image_name> .`

## Crear un volumen

Se puede crear un volumen al usar el comando run o también podemos crearlo por separado.

`docker volume create <volume_name>`

Podemos comprobar las características del volumen.

`docker volume inspect <volume_name>`

## Crear un contenedor

Se crea a partir de una imagen local o si no encuentra una con ese nombre la busca y descarga en DockerHub.

`docker run -itd <image_name>`

Se puede definir la versión de la imagen que se descargue desde DockerHub, que por defecto será la última versión. En la página se muestran las versiones, siendo recomendable especificar una, además de con que distribución de Linux se creó la imagen (Alpine, Debian...). 

`docker run -itd <image_name>:<version>`

Para definir el nombre del contendor:

`docker run -itd --name <container_name> <image_name>`

Podemos conectar un puerto del PC con el puerto que usa el contenedor para establecer comunicación. Usar para cada cado un puerto del PC distinto. Podremos entonces desde un navegador al contenedor desde [http://localhost:<host_port>](http://localhost:<host_port>).

`docker run -itd -p <host_port>:<container_port> <image_name>`

Se puede asignar un volumen al contendor, especificando su nombre y directorio en el contenedor.

`docker run -itd -v <volume_name>:<volume_path>`

También podemos utilizar un volumen que ya existe.

`docker run -itd --mount source=<volume_name>,target=<volume_path> <image_name>`

## Parar y reanudar un contenedor

Para detener un contenedor:

`docker stop <container_name/ID>`

Para volverlo a arrancar un contenedor desde el estado en el que se dejó, como archivos que se crearon después de haber creado el contenedor.

`docker start <container_name/ID>`

## Ejecutar comandos en un contenedor

Para entrar en el terminal del contenedor y poder ejecutar dentro los comandos:

`docker exec -it <container_name/ID> bash`

Para ejecutar un único comando sin tener que entrar en él:

`docker exec -it <container_name/ID> <command>`

## Convertir contenedor en imagen

Teniendo un contenedor podemos crear una imagen a partir de ella. En el tag, si no queremos definir una versión, podemos poner latest.

`docker commit <container_name> <image_name>:<tag>`

## Eliminar una imagen, contenedor o volumen

`docker container rm <cont_name>`

`docker image rm <image_name>`

`docker volume rm <volume_name>`

## Mostrar contenedores, imágenes a volúmenes

Muestra los contenedores que se están corriendo

`docker ps`

Para mostrar los contenedores que se han corrido recientemente

`docker ps -a`

Para mostrar las imágenes que hay en el PC:

`docker images`

Para mostrar los volúmenes:

`docker volume ls`

## Subir una imagen

Para subir una imagen nuestra a nuestro repositorio de DockerHub, primero ponemos nuestro usuario y luego nos pedirá la contraseña.

`docker login --username=<DockerHub_username>`

Ahora definimos la imagen como la última versión.

`docker tag <image_name/ID> <image_DockerHub_name>:latest `

Ya podemos subir la imagen al repositorio.

`docker push <image_DockerHub_name>`

## Docker compose

Archivo llamado docker-compose.yml que permite definir contenedores conectados para crear una aplicación.

```dockerfile
version: "<Docker_compose_version>" #Versión de Docker compose (3.9 por ejemplo)
    
services: #Definimos los diferentes servicios con sus características
  <container1_name>: #Nombre del primer contenedor
    image: <image_name> #Imagen en la que se va a basar el contenedor
    volumes:  #Añade un volumen al servicio
      - <volume_1_name>:<volume_1_path>
    command: <command> #Comando que se va a ejecutar al iniciar el servicio
    expose: #Expone un puerto
      - '<port>'
    restart: always #Reinicia siempre el servicio
    environment: #Definir variables disponibles en el servicio
      <VARIABLE_1_NAME>=<variable_1_value>
      <VARIABLE_2_NAME>=<variable_2_value>
    
  <container2_name>: #Nombre del segundo contenedor
    depends_on: #Especificar que contenedores deben ejecutarse antes
      - <container1_name>
      - <container3_name>
    build: . #Crear a partir de un Dockerfile la imagen de referencia
    volumes:
      - <volume_2_name>:<volume_2_path>
    ports:
      - "<host_port>:<container_port>" #Conectar puerto del host y del contenedor
    restart: always
    environment:
      <VARIABLE_1_NAME>=<variable_1_value>
      <VARIABLE_2_NAME>=<variable_2_value>

volumes: #Creamos los volumes que vamos a usar en los servicios
  <volume_1_name>: {}
  <volume_2_name>: {}
```

 A partir de este archivo, para crear los contenedores:

`docker compose up -d`

## Docker con Google Cloud

Vamos a la consola de Google Cloud con nuestra cuenta. Vamos a compute engine -> instancias de VM.

Vamos a crear una MV, y dentro ejecutar docker y un contenedor. Vamos a crear uno predefinido para hacer pruebas. Para docker no vale cualquier MV.

Le damos a crear instancia: la zona la que sea mas barata. De máquina elegimos la e2-small, ya que 2gb de RAM son suficientes. Le tenemos que clicar a permitir tráfico HTTP y HTTPS (así permitimos tráfico entrante por el puerto 80, que es el de http). (Los cloud bloquean todo por defecto así que es importante marcar que queremos permitir).

Hay que cambiar el disco de arranque porque el de defecto no tiene docker. En las opciones, de las imágenes públicas ponemos en el SO el Container Optimized OS. En la versión, ponemos de LTS (long time support) la última versión, la 89.

Para pagar menos: Activar interrumpibilidad en el menú de crear la máquina. Son más baratas pero solo duran 24 horas y se pueden interrumpir de repente por demanda. Por ello, ni en este ni tampoco en otro caso es recomendable guardar datos dentro de la máquina.

Ahora ya podemos crear la máquina. En SSH le damos a abrir en otra ventana y ya entramos en el terminal. En esta tenemos docker porque hemos elegido una ya preparado, en la defecto no lo tiene pero se podria descargar. Una vez en el terminal, ya podremos aplicar comandos para usar docker.

Una vez creado en contenedor con un puerto redireccionado a un puerto del host, vamos a la página de Google Cloud y buscamos la IP externa de la instancia. Si por está IP entramos en el puerto del host conectado al contenedor, nos redireccionará al puerto del contenedor. Pegamos esta IP externa en el navegador. Se abre una página web. Ahora ya podemos cerrar la instancia. Podemos acceder a este contenedor de este modo desde cualquier máquina.

## Referencias

- [docker run](https://phoenixnap.com/kb/docker-run-command-with-examples)
- [docker exec](https://devconnected.com/docker-exec-command-with-examples/)
- [docker compose](https://coffeebytes.dev/docker-compose-tutorial-con-comandos-en-gnu-linux/)
