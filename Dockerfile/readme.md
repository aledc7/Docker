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

__docker build:__ este comando crea la imágen basandose en el archivo Dockerfile que se encuentre en la ubicación en donde se está ejecutando el comando.

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


Se copia el siguiente Dockerfile para crear una imágen de PHP con todo el código comentado en donde se explica que hace cada instruccion.



```js
# partimos de la imágen base de ubuntu 1604
FROM ubuntu:16.04

LABEL MAINTAINER="Ale DC"

RUN apt-get update \
 # instalo las dependencias para usar UTF8 ya que php lo usa.
  && apt-get install -y locales \
  && locale-gen en_US.UTF-8

# agrego estas variables de entorno con ENV y configuro UTF8  para idioma Ingles
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV LC_ALL=en_US.UTF-8

# instalo todos los paquetes que necesito para tener un buen container de PHP
RUN apt-get update \
   && apt-get install -y \
    curl \
    git \
    software-properties-common \
    unzip \
    zip \
   && add-apt-repository -y ppa:ondrej/php \
   && apt-get update \
   && apt-get install -y \
    php7.2-cli \
    php7.2-curl \
    php7.2-fpm \
    php7.2-gd \
    php7.2-imap \
    php7.2-mbstring \
    php7.2-mysql \
    php7.2-pgsql \
    php7.2-xml \
    php-memcached \
    # instalo composer dentro de PHP ya que lo voy  a usar
   && php -r "readfile('https://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
   # es necesario crear esta carpeta para los Process ID de docker
  && mkdir /run/php \
  # remuevo software properties  y demas paquetes para hacer una limpieza de archivos innecesarios
  && apt-get remove -y --purge software-properties-common \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
 # copio estos dos archivos que ya tengo en el contexto de PHP para lograr configurar PHP segun mis necesidades
# estos dos archivos estan disponibles aqui: https://github.com/aledc7/Docker/tree/master/context/php
COPY php-fpm.conf /etc/php/7.2/fpm/php-fpm.conf
COPY www.conf /etc/php/7.2/fpm/pool.d/www.conf

# expongo el puerto 9000
EXPOSE 9000
# este es el comando predeterminado que ejecutará la imágen al ser corrida co docker run
CMD ["php-fpm7.2"]

```





Imágen de PHP con modificaciones:
https://cloud.docker.com/u/aledc/repository/docker/aledc/php

Imágen de Nginx con modificaciones:
https://cloud.docker.com/repository/docker/aledc/nginx


