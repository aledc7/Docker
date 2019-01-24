# Docker - Comandos y usos frecuentes

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

### Para volver a conectarse al docker
```
docker attach id_docker
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
## Listar redes del docker
```
docker network
```

## Crear una nueva imágen a partir de un container

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




## Con el comando -v /nombre se está creando un volumen que se le asignará al container que se vaya a correr.
## Si se accede a este docker se puede observar la carpeta nombre_volumen creada arriba con el comando -v /nombre

```
docker run -it --name ale1 -v /nombre_volumen ubuntu:1810 bash
```

## Donde se crea el volumen?
El volumen se crea fuera del docker y permanecerá incluso luego de que el docker sea eliminado.
La ubicación es la siguiente:
```
/var/lib/docker/volumes
```

# Ver las capas de una imágen de docker
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

Contexto de docker:  El comando docker build necesita que se le especifique una ruta para el contexto del build como un argumento.
Al iniciarse el proceso de build, docker copiará dentro de la imagen todos los archivos que se encuentren en el contexto.
Para indicar la ruta actual en donde se encuentre el archivo Dockerfile se indica con un punto **.**


el comando más básico para ejecutar un docker file creado es el que se muestra abajo, el parámetro **-t** es necesario para indicar el nombre de la imágen, luego debe indicarse la ruta del cotexto con un punto **.**
Luego se le puede dar un tag a la imágen a crear.
Si bien el tag es opcional, luego para poder subir la imágen a dockerhub es obligatorio que la imágen cuente con un tag.
```
docker build -t nombre_que_tendra_la_imagen .
```

# Buenas prácticas para crear un Dockerfile

Cada vez que se indique una instrucción RUN en el dockerfile crea una nueva imágen, por lo que es recomendable concatenar todas las insrtrucciones RUN con el fin de reducir la cantidad de imagenes a crear. Para concatenar dentro de un Dockerfile se usa **&&**  para separar los comandos que se van a ejecutar.
el caracter de la barra **/** sirve para hacer el comando multi linea, para que sea mas fácil de leer y mantener.


















 


