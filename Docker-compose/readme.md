# Docker Compose

Para hacer un buen uso de Docker Compose es altamente recomendable dominar los siguientes temas:

- Dockerfile
- Docker Volume
- Docker Networks


Si no tenemos claro estos temas, claro que podremos manejar igual Docker Compose, pero no sabrémos bien que es lo que estamos haciendo y porqué lo hacemos.



Lo primero que debemos hacer es crear en el directorio raíz de nuestro proyecto un archivo con el nombre __Docker-compose.yml__

En este archivo debemos seguir la siguiente estructura, primeramente indicar la versión de Docker-compose, luego debemos indicar los 3 bloques principales que son __services__ , __networks__ , y __volumes__

en el bloque __services:__ se especificarán los contenedores que se vana emplear para la creacion del docker-compose, junt con sus respectivas configuraciónes.
}



podémos usar esto como plantilla:
```js
version: '3'
services:
networks:
volumes:
```



El siguiente es un ejemplo de un dockerfile con comentarios sobre cada linea para ver que es lo que se está haciendo


```js
# indico la version de Docker-compose
version: '3'

# en el bloque services: se deberan indicar todos los contenedores que se requieran usar, junto con sus configuraciones.
services:
  
  # pongo nginx: y luego indentado todas las configuraciones para este contenedor.
  
  nginx:
    
    # uso la opcion build ya que quiero hacer uso de mi propio dockerfile en vez de usar una imagen ya hecha.
    # para poder hacer esto debo tener una carpeta llamada nginx con un archivo Dockerfile adentro que tenga todas
    # las instrocciones para poder crear esta imágen.
    build: nginx
    
    
    # indico que el puerto externo expuesto es el 8080 y el interno del docker será el 80
    ports:
      - "8080:80"
    
    # indico que quiero que se cree un volumen que en mi host será partiendo de la carpeta actual './' y luego application:
    # por otro lado despues de los dos puntos indico la ruta dentro del contenedor, que será ':/var/www/html'
    
    volumes:
      - ./application:/var/www/html
   
   # indico que quiero asignarle a este contenedor la red red_aledc
   # como esta red aún no fue creada, debo crearla luego en la seccion del bloque __networks__
    networks:
      - red_aledc
  
  # repito los pasos de arriba pero ahora para el contenedor de php.
  # usando build:  le indico a docker-compose que quiero crear mi propia imágen, por lo que tendré que tener
  # el archivo Dockerfile correspondiente en una carpeta llamada php
  php:
    build: php
    volumes:
      - ./application:/var/www/html
    networks:
      - stydenet
  
  # repito lo mismo para mysql, pero esta vez en vez de crear mi propia imágen, voy a usar la imágen ofcial de mysql
  # Esta se encuentra subida en Dockerhub y se descargará y usara al momento de correr el Docker-compose.
  # para usar una imágen ya subida a dockerhub se hace indicando image: nombre:version (image: mysql:5.7)
  
  mysql:
    image: mysql:5.7
    
    # este contenedor necesita de algunas variables de entorno, estas se especifican debajo de __environment:__
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: styde
      MYSQL_USER: styde
      MYSQL_PASSWORD: secret
    
    
    ports:
      - "33060:3306"
    volumes:
      - mysqldata:/var/lib/mysql
    networks:
      - stydenet
  redis:
    image: redis:alpine
    volumes:
      - redisdata:/data
    networks:
      - stydenet

# acá iran todas las redes que quiero que se creen, y que ya mencioné mas arriba para asignar a algunos contenedores.
networks:
  # este es el nombre de la red
  stydenet:
    # este es el driver que le asigno a la red, siendo bridge el indicado para intercomunicar contenedores.
    # ese driver "bridge" es el que viene predeterminadamente, por lo que podemos no indicar nada y se asignará igual.
    driver: "bridge"
volumes:
  redisdata:
    driver: "local"
  mysqldata:
    driver: "local"
```
