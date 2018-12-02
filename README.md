# Docker - Comandos 


### Este archivo contiene los comandos frecuentes que utilizo para la administracion de dockers



docker info

docker version

docker images

docker pull nombre_imagen

docker search nombre_imagen

docker ps

docker ps -a

docker run

docker run -it ubuntu /bin/bash

docker start/stop/restart

docker attach id_docker

docker rm

docker rmi


docker exec -u 0 -it nombre_docker /bin/bash

docker exec -u 0 -it nombre_docker /busybox ash (en caso de que el docker no tenba bash)

docker network

docker commit nombre_docker nombre_nueva_imagen

docker tag

docker login

docker push   user/imagen

journalctl -fu docker.service

/var/lib/docker/  # ubicacion del docker en el servidor

docker attach


docker logs -f -t nombre_docker


manejo de puertos

-p  22022:22    el primero es el externo expuesto a internet,el segundo es el del docker interno.



# Logs dentro de los dockers
/var/lib/docker/comntainers/97734sdvxsd...



vaciar todos los logs

truncate -s 0 /var/lib/docker/containers/*/*-json.log




