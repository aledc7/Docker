# Docker - Comandos 

- [x] AleDC


### Este archivo contiene los comandos frecuentes que utilizo para la administracion de Dockers


```
# info completa del docker instalada
docker info

# version del docker
docker version

# listar imagenes descargadas
docker images

# descargar una imágen a nuestro server
docker pull nombre_imagen

# buscar imagenes en la nube
docker search nombre_imagen

# listar dockers que esten corriendo
docker ps

# listar todos los dockers 
docker ps -a

# correr un docker
docker run
# correr un docker interactivo y dar acceso a su bash
docker run -it ubuntu /bin/bash

#  arrancar, detener,reinciar docker
docker start/stop/restart

# para volver a conectarse al docker
docker attach id_docker

# borrar un docker (Atención: Proceso irreversible)
docker rm

# borrar una imagen descargada
docker rmi 

# acceder al bash de docker como root
docker exec -u 0 -it nombre_docker /bin/bash

# acceder al basj de docker como root en caso que el docker no tenga bash
docker exec -u 0 -it nombre_docker /busybox ash (en caso de que el docker no tenba bash)

# listar redes del docker
docker network

# crear una nueva imagen a partir de un container
docker commit nombre_docker nombre_nueva_imagen

# para poder subir imagenes es necesario tener etiquetas
docker tag

# para poder subir imagenes a dockerhun primero debemos loguearnos
docker login

# con este comando subo mi imagen a docker hub.
# Previamente debo estar logueado y haber hecho el docker tag.
docker push   user/imagen

# monitorear todos los losg e todos los dockers
journalctl -fu docker.service


# ubicacion del docker en el servidor
/var/lib/docker/  

# entrar al docker en modo log para ver lo que sucede
docker attach


# monitorear un log en tiempo real
docker logs -f -t nombre_docker


# manejo de puertos
-p  22022:22    
# el primer número(22022) es el puerto externo expuesto a internet, el segundo número (22) es el puerto del docker interno.


# Logs dentro de los dockers
/var/lib/docker/comntainers/97734sdvxsd...


# vaciar todos los logs
truncate -s 0 /var/lib/docker/containers/*/*-json.log

# si solo se quisiera vaciar el log de un contenedor específico, hay que reemplazar el primer asterizco por el nombre de la carpeta del container. Todos los Dockers tienen su carpeta propia en:
/var/lib/docker/containers



```

