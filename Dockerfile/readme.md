## Dockerfile y Buenas Practicas





docker build -t aledc/php



Para un buen dockerfile es fundamental concatenar las instrucciones haciendo uso del doble ampersand __&&__  
De esta manera es posible concatenar todas las instrucciones RUN lo cual nos creará una imágen mas ligera.
La barra invertida '\' nos sirve para realizar los saltos de linea y así no tener una sola linea.

## Instrucción FROM
La instrucción FROMes la primer instrucción que escribimos dentro de un Dockerfile y nos indica cuál es la imagen que se va a tomar como base para crear nuestra imagen.


## Instrucción RUN
La instrucción RUN va a ejecutar todos los comandos que necesitemos para poder crear nuestra imagen, estos son comandos que normalmente podemos utilizar en una terminal, tomando como base la imagen base, es decir, si nuestro archivo Dockerfile está tomando como base una imagen de Debian podremos hacer uso de comandos que normalmente se encuentran en bash, pero si utilizamos una imagen base de Alpine, los  comandos de bash no estarán disponibles ya que esta imagen no contiene bash, en su lugar utiliza otro shell más simple llamado sh por lo que los comandos dentro de esta instrucción deberán corresponder a este shell.

## Instrucción CMD
Esta instrucción se utiliza para especificar el comando por defecto que se va a utilizar al crear un contenedor con esta imagen. Normalmente es utilizada para indicar qué comando va a iniciar la tarea para la que está hecha la imagen. Por ejemplo, si nuestra imagen está pensada para ejecutar Nginx, esta instrucción deberá contener el comando necesario para ejecutar Nginx.

En caso de que no indiquemos una instrucción CMD, la imagen va a utilizar la instrucción proveniente de la imagen base

Es recomendable encadenar los comandos dentro de esta instrucción ya que así podremos reducir el número de capas en la imagen resultante de este Dockerfile.

Otra buena práctica es encadenar alfabéticamente los parámetros en caso de que algún comando los requiera, como pueden ser la instalación de paquetes. Esto puede ayudar a la legibilidad del archivo y a evitar la duplicación de parámetros.

## Docker Cache
Cuando creamos una imagen, todas las instrucciones del Dockerfile estarán añadiendo una nueva capa a la imagen, por lo que Docker va a almacenar estas instrucciones en caché para poder hacer el proceso de construcción de la imagen más rápido si se requiere volver a construirla.

Si una instrucción cambia, Docker va a dejar de utilizar el caché a partir de la instrucción que fue modificada.

## Instrucción COPY
Nos sirven para poder copiar archivos y directorios que tengamos en el contexto del build hacia el contenedor. Estos archivos deben existir en el contexto, de lo contrario el proceso de construcción de la imagen se verá interrumpido y obtendremos un error.

Los archivos que indiquemos en esta instrucción se copiarán al filesystem de la imagen para poder ser utilizados en los contenedores que se creen a partir de esta imagen.

## Instrucción ADD
Al igual que la instrucción COPY, esta sirve para para agregar archivos al filesystem de la imagen, pero tiene la ventaja de que nos permite obtener archivos desde internet, además de que nos permite desempaquetar archivos comprimidos de manera automática.



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


