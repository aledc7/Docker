# Docker - Manual de Bolsillo

[![aledc.com](https://github.com/aledc7/Scrum-Certification/blob/master/recursos/aledc.com.svg)](https://aledc.com)
[![License](https://github.com/aledc7/Scrum-Certification/blob/master/recursos/mit-license.svg)](https://aledc.com)
[![GitHub release](https://github.com/aledc7/Scrum-Certification/blob/master/recursos/release.svg)](https://aledc.com)
[![Dependencies](https://github.com/aledc7/Scrum-Certification/blob/master/recursos/dependencias-none.svg)](https://aledc.com)

Este documento contiene una recopilacion de conceptos, comandos y buenas prácticas a la hora de trabajar con Dockerfile y Docker Compose.

Así mismo, cada carpeta en este repositorio contiene info detallada sobre los temas:
- Dockerfile
- Docker Volume
- Docker Link
- Docker Network
- Docker-compose

Las mismas fueron recopiladas durante la capacitación que tomé sobre Docker.

- [x] AleDC


_______________________________________________________________________________________________________________________

### Correr docker sin usar sudo

Para esto es necesario crear el grupo __docker__ y luego asignar tu usuario a ese grupo, para finalmente cerrar sesion y abrir nuevamente.

Agrego el grupo a mi sistema ubuntu
```
sudo groupadd docker
```

agrego el usuario al grupo docker
```
sudo usermod -aG docker mi_usuario
```




### Info completa del docker instalada
```
docker info
```
### Version del docker
```
docker version
```

### Listar imágenes descargadas
```
docker images
```
### Descargar una imágen a nuestro server
```
docker pull nombre_imagen
```
### Buscar imgáenes en la nube
```
docker search nombre_imagen
```

### Listar dockers que esten corriendo
```
docker ps

docker ps -q (muestra las ids de todos los dockers corriendo)
```
### Listar todos los dockers 
```
docker ps -a
```
### Correr un docker
```
docker run
```
### Correr un docker interactivo y dar acceso a su bash
```
docker run -it ubuntu /bin/bash
```
### Correr un docker interactivo y en background (-d 'detach')
```
docker run -d -it ubuntu /bin/bash
```

###  Arrancar, detener,reinciar docker
```
docker start/stop/restart
```

### Monitorear todo lo que sucede con un contenedor con Docker Attach
```
docker attach id_docker
```

### acceder a un contenedor en ejecucion
```
docker exec -it nombre_contenedor bash
```


### Borrar un docker (Atención: Proceso irreversible)
```
docker rm
```

### Borrar una imágen descargada
```
docker rmi 
```

### Acceder al bash de docker como root
```
docker exec -u 0 -it nombre_docker /bin/bash
```

## Acceder al basj de docker como root en caso que el docker no tenga bash
```
docker exec -u 0 -it nombre_docker /busybox ash (en caso de que el docker no tenba bash)
```
### Listar redes del docker
```
docker network
```

### Crear una nueva imágen a partir de un container

Esto es útil en los casos que personalicemos una imágen base, por ejemplo de Ubuntu, y se le agregan las herramientas básicas de monitoreo y otras utilidades como el nano. Luego queremos generar una nueva imágen de este ubuntu, para usarlo de base la próxima vez que queramos instanciar un docker. Esto nos ahorrará tiempo en tener que volver a instalar las herramientas previamente instaladas.
aquí el comando:
```
docker commit id_docker nombre_nueva_imagen
```
otra variante de creación de una imágen poniendole un tag a la imágen a crear, en este caso el tag será __2.1.1__
```
docker commit id_docker nombre_nueva_imagen:2.1.1
```
### Para poder subir imgáenes a docker hub es necesario que la imágen a subir tenga etiquetas.
```
docker tag
```

### Para poder subir imgáenes a Dockerhun primero debemos loguearnos
```
docker login
```

### Con este comando subo mi imágen a docker hub.

### Previamente debo estar logueado y haber hecho el docker tag.
```
docker push   user/imagen
```

### Monitorear todos los losg e todos los dockers
```
journalctl -fu docker.service
```

### Ver los logs de un docker específico
(si el docker no tiene nombre, deberá indicarsele su ID)
```
docker logs nombre_docker
```

### Monitorear un log en tiempo real
```
docker logs -f -t nombre_docker
```


### Ubicacion del docker en el servidor
```
/var/lib/docker/  
```

### Entrar al docker en modo log para ver lo que sucede
```
docker attach
```


### Manejo de puertos
### el primer número(22022) es el puerto externo expuesto a internet, el segundo número (22) es el puerto del docker interno.

```
-p  22022:22    
```


### Logs dentro de los dockers
```
/var/lib/docker/comntainers/97734sdvxsd...
```


### Vaciar todos los logs
```
truncate -s 0 /var/lib/docker/containers/*/*-json.log
```
### Si solo se quisiera vaciar el log de un contenedor específico, hay que reemplazar el primer asterizco por el nombre de la 
carpeta del container. Todos los Dockers tienen su carpeta propia en:
```
/var/lib/docker/containers
```

## Volúmenes en Docker


Un volumen permite compartir un directorio desde nuestro HOST al contenedor, lo cual es muy útil al momento del desarrollo.

```js
docker run --name aledc-nginx -v /home/aledc/loQueQuieroCompartir:/usr/share/nginx/html
```
en el ejemplo de arriba, luego del -v primero se hace referencia a la ruta de nuestro host, luego de los dos puntos se pone la referencia de la ruta dentro del contenedor.

Es posible correr ese comando, indicando la ruta actual en donde nos encontremos en nuestro host mediante el comando 'pwd', esto evitará tener que escribir la ruta... aquí el ejemplo de esto:
```js
docker run --name aledc-nginx -v $(pwd):/usr/share/nginx/html
```
Aquí todos los comandos referidos a los volúmenes.
```
docker volume ls        # Lista todos los volumenes
docker volume create    # Crea un nuevo volumen
docker volume inspect   # Hay que pasarle como parámetro el nombre del volumen a inspeccionar, devolverá un resumen.
docker volume rm        # Borrar un volumen específico
docker volume prune     # Borra todos los volumenes que no tengan asociados ningún container.
```




 Con el comando -v /nombre se está creando un volumen que se le asignará al container que se vaya a correr.
 Si se accede a este docker se puede observar la carpeta nombre_volumen creada arriba con el comando -v /nombre

```
docker run -it --name ale1 -v /nombre_volumen ubuntu:1810 bash
```

### Donde se crea el volumen?
El volumen se crea fuera del docker y permanecerá incluso luego de que el docker sea eliminado.
La ubicación es la siguiente:
```
/var/lib/docker/volumes
```

### Ver las capas de una imágen de docker
```
docker history nombre_imagen
```
El comando de arriba mostrará todos los cambios que se realizaron hasta obtener la imágen.


# Dockerfile

La mejor manera de crear imágenes en docker es a traves de Dockerfile.

Dockerfile es un documento de texto simple, que contiene todas las instrucciones necesarias para poder crear una imágen.
De esta manera es posible crear una imágen personalizada y completamente automatizada.
Este permite establecer variables de entorno, instalar software, copiar contenido hacia el contenedor, o establecer el comando que se va a ejecutar predeterminadamente.
Cada instruccion en el dockerfile añadirá una nueva capa a la imágen, mientras mas instrucciones tenga el dockerfile mas capas tendrá, por ende mas pesado será.
Es importante en este punto recordar el hecho de que la ideología de docker es que cada container sea para proveer un unico servicio, ya que no se trata de maquinas virtuales.

Primeramente se debe crear un archivo llamado __Dockefile__  con la primera letra mayscula.

La primer instruccion será __FROM__ para indicar cual será la imágen base a utilizar para crear nuestra imágen personalizada.
El parametro **-y** es necesario para que al indicar una tarea no solicite confirmación.

```
FROM debian:strech
```

docker build: este comando crea la imágen basandose en el archivo Dockerfile que se encuentre en la ubicación en donde se está ejecutando el comando.

### Contexto de docker
Se define como contexto la carpeta en donde se encuentra el archivo Dockerfile, por ende en donde se ejecutará el comando docker build.
El comando docker build necesita que se le especifique una ruta para el contexto del build como un argumento.
Al iniciarse el proceso de build, docker copiará dentro de la imagen todos los archivos que se encuentren en el contexto.
Para indicar la ruta actual en donde se encuentre el archivo Dockerfile se indica con un punto **.**

Tengase en cuenta de que en el contexto unicamente deben encontrarse solo los documentos que queremos incluir en el docker.


el comando más básico para ejecutar un docker file creado es el que se muestra abajo, el parámetro **-t** es necesario para indicar el nombre de la imágen, luego debe indicarse la ruta del cotexto con un punto **.**
Luego se le puede dar un tag a la imágen a crear.
Si bien el tag es opcional, luego para poder subir la imágen a dockerhub es obligatorio que la imágen cuente con un tag.
```
docker build -t nombre_que_tendra_la_imagen .
```

### Buenas prácticas para crear un Dockerfile

Cada vez que se indique una instrucción RUN en el dockerfile crea una nueva imágen, por lo que es recomendable concatenar todas las insrtrucciones RUN con el fin de reducir la cantidad de imagenes a crear. Para concatenar dentro de un Dockerfile se usa **&&**  para separar los comandos que se van a ejecutar.
el caracter de la barra ' \ ' sirve para hacer el comando multi linea, para que sea mas fácil de leer y mantener.
Concatenar las instrucciones RUN también es muy útil para aprovechar el cache de docker y prevenir el asi llamado "Aggressive Caching"

Ejemplo de la creación de una imágen de ubuntu instalando en una sola linea varios comandos. Observese que se usa una unica vez el comando RUN.

Es una buena práctica ordenar los paquetes alfabeticamente

```
FROM ubuntu

RUN apt update \
 && apt install -y \
 git \
 htop \
 iputils-ping\
 mc \
 nano \
 net-tools \
 sysstat \
 tzdata \
 vim
# seteo la zona horaria para Argentina
RUN ln -fs /usr/share/zoneinfo/America/Argentina/Buenos_Aires /etc/localtime && dpkg-reconfigure -f noninteractive tzdata


# COPY aleprueba.txt /src/aleprueba.txt

# CMD ["echo", "Pruebas del comando CMD dentro del Dockerfile]



 
```


### Instruccion CMD 

Las instrucciones CMD van a indicar que comando queremos que se ejecute cuando se inicie el contenedor. Estas instrucciones no se ejecutan al construir la imagen, solo se ejecutan cuando se instancie e inicie un contenedor. las instrucciones pueden ser en fomrato EXEC o formato SHELL
```
CMD ["echo" , "Hola Mundo"]
```

### Instruccion COPY

Las instrucciones COPY es utilizada para copiar archivos que estén en el contexto del build y los agrega al filesystem del contenedor.
Primeramente se debe especificar el nombre del archivo, que debe encontrase en el contexto, luego la ruta en el docker en donde querramos copiar el archivo...
```
COPY nombre_archivo_en_contexto.txt /ruta/nombre_en_docker.txt
```

### Instruccion ADD

Es similar a la instruccion COPY con el agregado agregado de que permite descargar archivos desde Internet. 
También permite descomprimir archivos descargados de forma automática.



### Subiendo Imágenes a DockerHUB

Para subir una imágen que tengamos localmente y querramos tener disponible en dockerhub estos son los pasos
Prerequisitos: tener una cuenta de DockerHub.

1 - Se le debe poner a la imágen el tag con nuestro usuario de dockerhub con el siguiente comando
```
docker tag id_de_imagen usuario_de_dockerhub/nombre_de_la_imagen

# aca un ejemplo con el id de una de mis imagenes, lugo mi usuario, y el nombre que le quiero dar.
docker tag f694e1b3c573 aledc/ubuntu_with_tools
```
2 - ahora si ejecutamos __docker images__ deberíamos ver la imágen con nuestro usuario de dockerhub.
El paso siguiente es hacer login desde la terminal con nuestras credenciales de dockerhub:
``` 
docker login
```
se solicitará user y pass... si todo fue bien deberíamos ver __Login Succeeded__

3 - el último paso es subir la imagen con el comando docker push seguido del nombre del  usuario/imagen

```
docker push aledc/ubuntu_with_tools
```
En el paso anterior comenzará a subir la imágen , si todo termina bien, en el últmo paso de la subida mostrará el SHA de la imágen subida.
```
latest: digest: sha256:88720c53dd13aa676b485c7f7fbb8bc9f1d8e10221cfcc81af701259274208d0 size: 1574
```

### Linkeando dos contenedores


Para que dos o mas contenedores puedan verse y comunicarse entre ellos, existen varias posibilidades, una de ellas bastante simple consiste en usar el comando  __--link nombre_container_a_enlazar__ a la hora de hacer el docker run, aquí un ejemplo:
```
docker run  -d --name nginx -v $(pwd)/application:/var/www/html -p 3001:80 --link php aledc/nginx
```
en el ejemplo de arriba, estoy linkeando el contenedor que estoy por lanzar conotro contenedor existente llamado __php__
En este caso el enlace lo hago en la parte que pongo __--link php__  y con eso será suficiente para que ambos contenedores puedan verse e intercambiar informacion



















 


