# Docker - Comandos 

- [x] AleDC


### Este archivo contiene los comandos frecuentes que utilizo para la administracion de Dockers



### Info completa del docker instalada
```
docker info
```
### Version del docker
```
docker version
```

### Listar imagenes descargadas
```
docker images
```
### Descargar una imágen a nuestro server
```
docker pull nombre_imagen
```
### Buscar imagenes en la nube
```
docker search nombre_imagen
```

### Listar dockers que esten corriendo
```
docker ps
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
###  Arrancar, detener,reinciar docker
```
docker start/stop/restart
```

### Para volver a conectarse al docker
```
docker attach id_docker
```
### Borrar un docker (Atención: Proceso irreversible)
```
docker rm
```

### Borrar una imagen descargada
```
docker rmi 
```

### Acceder al bash de docker como root
```
docker exec -u 0 -it nombre_docker /bin/bash
```

### Acceder al basj de docker como root en caso que el docker no tenga bash
```
docker exec -u 0 -it nombre_docker /busybox ash (en caso de que el docker no tenba bash)
```
### Listar redes del docker
```
docker network
```

### Crear una nueva imagen a partir de un container
```
docker commit nombre_docker nombre_nueva_imagen
```
### Para poder subir imagenes es necesario tener etiquetas
```
docker tag
```

### Para poder subir imagenes a dockerhun primero debemos loguearnos
```
docker login
```

### Con este comando subo mi imagen a docker hub.

### Previamente debo estar logueado y haber hecho el docker tag.
```
docker push   user/imagen
```

### Monitorear todos los losg e todos los dockers
```
journalctl -fu docker.service
```

### Ubicacion del docker en el servidor
```
/var/lib/docker/  
```

### Entrar al docker en modo log para ver lo que sucede
```
docker attach
```


### Monitorear un log en tiempo real
```
docker logs -f -t nombre_docker
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




