[![aledc.com](https://github.com/aledc7/Scrum-Certification/blob/master/recursos/aledc.com.svg)](https://aledc.com)
[![License](https://github.com/aledc7/Scrum-Certification/blob/master/recursos/mit-license.svg)](https://aledc.com)
[![GitHub release](https://github.com/aledc7/Scrum-Certification/blob/master/recursos/release.svg)](https://aledc.com)
[![Dependencies](https://github.com/aledc7/Scrum-Certification/blob/master/recursos/dependencias-none.svg)](https://aledc.com)

- [x] aledc.com
- [x] MIT License
- [x] Tested Code


# Docker Compose

Para hacer un buen uso de Docker Compose es altamente recomendable dominar los siguientes temas:

- Dockerfile
- Docker Volume
- Docker Networks


Si no tenemos claro estos temas, igual podremos manejar igual Docker Compose, pero no sabrémos bien que es lo que estamos haciendo y porqué lo hacemos.



Lo primero que debemos hacer es crear en el directorio raíz de nuestro proyecto un archivo con el nombre __Docker-compose.yml__

En este archivo debemos seguir la siguiente estructura, primeramente indicar la versión de Docker-compose, luego debemos indicar los 3 bloques principales que son:  
__services__  
__networks__   
__volumes__  

En el bloque __services:__ se especificarán los contenedores que se vana emplear para la creacion del docker-compose, junt con sus respectivas configuraciónes.
}



podémos usar esto como plantilla:
```js
version: '3'
services:
networks:
volumes:
```



El siguiente es un ejemplo completo de un dockerfile con comentarios sobre cada linea para ver que es lo que se está haciendo:


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
      - red_aledc
  
  # repito lo mismo para mysql, pero esta vez en vez de crear mi propia imágen, voy a usar la imágen ofcial de mysql
  # Esta se encuentra subida en Dockerhub y se descargará y usara al momento de correr el Docker-compose.
  # para usar una imágen ya subida a dockerhub se hace indicando image: nombre:version (image: mysql:5.7)
  
  mysql:
    image: mysql:5.7
    
    # este contenedor necesita de algunas variables de entorno, estas se especifican debajo de __environment:__
    environment:
      MYSQL_ROOT_PASSWORD: aledc
      MYSQL_DATABASE: aledc
      MYSQL_USER: aledc
      MYSQL_PASSWORD: aledc
    
    
    ports:
      - "33060:3306"
    volumes:
      - mysqldata:/var/lib/mysql
    networks:
      - red_aledc
  redis:
    image: redis:alpine
    volumes:
      - redisdata:/data
    networks:
      - red_aledc

# acá iran todas las redes que quiero que se creen, y que ya mencioné mas arriba para asignar a algunos contenedores.
networks:
  # este es el nombre de la red
  red_aledc:
    # este es el driver que le asigno a la red, siendo bridge el indicado para intercomunicar contenedores.
    # ese driver "bridge" es el que viene predeterminadamente, por lo que podemos no indicar nada y se asignará igual.
    driver: "bridge"

# acá iran todos los volumenes que se creen y que utilizamos mas arriba para algunos contenedores
volumes:
  # creo el volumen redisdata y abajo especifico el driver que este volumen usará (esto último no es necesario
  # ya que por defecto los volumenes vienen asignados con el driver 'local')
 
  redisdata:
    driver: "local"
  mysqldata:
    driver: "local"
```

Una vez que tenemos terminado nuestro archivo Docker-compose.yml tenemos que hacer que se generen las imágenes que indicamos en el mismo.  esto se hace con el siguiente comando
```js
docker-compose build
```
El comando de arriba creará todas las imágenes indicadas en el archivo, y necesarias para el paso final.
Esto lo podemos verificar con el comando __docker images__  y buscando que aparezcan las imágenes que indicamos en nuestro archivo.

Finalmente estamos en condiciones de levantar nuestra aplicacion escribiendo el siguiente comando en la carpeta en donde tengamos el archivo __Docker-compose.yml__

```js
docker-compose up
```
Este comando creará las redes, los volumenes, y los contenedores que hayamos indicado en nuestro archivo Docker-compose.yml

Podemos revisar que todo se haya creado correctamente haciendo uso del comando __docker-compose ps__  
```js
docker-compose ps
```

Este comando listará solamente todo lo relacionado al archivo .yml que se encuentre en donde hayamos corrido el comando.


En caso de que querramos detener todo lo que ha levantado la ejecucion de docker-compose up.... para esto existe el siguiente comando:

```js
docker-compose down
```


También puede darse el caso de que querramos ejecutar un comando en alguno de los contenedores levantados, esto lo hacemos mediante:
docker-compose exec nombre_container comando

```js
docker-compose exec php sh
```

#### Variables en Docker-compose

El hecho de incluir nombres de bases de datos, usuarios, contraseñas, puertos y otra información sensible directamente en el archivo Docker-compose.yml  no es una buena práctica, ya que esaramos vulnerando la seguridad al dejar esta información persistida y sin encriptación.  
 Una buena práctica para solucionar esto es crear un archivo oculto llamando __.env__   
 En este archivo podremos colocar toda la información sensible que queramos definir en el docker-compose.
 
Ahora bien para que esto funcione correctamente, en el archivo docker-compose debemos poner variables para así referenciar estas variables con los datos que escribamos en nuestro archivo .env

Las variables en el archivo docker-compose deben declararse de esta manera:  $(NOMBRE_VARIABLE)
Y dentro del archivo .env irán sin el signo peso ni parentesis:  NOMBRE_VARIABLE

Aquí un ejemplo:


```js
      # ESTA ES LA FORMA TRADICIONAL EN DONDE SE INCLUYEN LAS CREDENCIALES DIRECTAMENTE EN EL ARCHIVO docker-compose

    environment:
      MYSQL_ROOT_PASSWORD: aledc
      MYSQL_DATABASE: aledc
      MYSQL_USER: aledc
      MYSQL_PASSWORD: aledc
      
      
      
      
            # ESTA ES LA FORMA REEMPLAZADO POR VARIABLES

    environment:
      MYSQL_ROOT_PASSWORD: $(NOMBRE_VARIABLE_ROOT_PASSWORD)
      MYSQL_DATABASE: $(NOMBRE_BASE_DE_DATOS)
      MYSQL_USER: $(NOMBRE_USUARIO)
      MYSQL_PASSWORD: $(PASSWORD_USUARIO)

```

Luego solo restará crear un archivo oculto con nombre __.env__  el cual tendrá declaradas estas variables con sus valores asignados:

```js
NOMBRE_VARIABLE_ROOT_PASSWORD = password123
NOMBRE_BASE_DE_DATOS = basedato1
NOMBRE_USUARIO = aledc1
PASSWORD_USUARIO = aledc123
```
Luego de esto, si ejecutamos docker_compose up     todo deberá funcionar igual, con la diferencia de que ahora hemos logrado separar los datos sensibles de nuestro archivo .env


## LOGS

Mediante el comando __docker-compose logs  nombre_container__  se puede monitorear los logs de alguno de los contenedores creados por el docker-compose.  
Tener presente que el nombre que se le debe pasar es el mismo que el declarado en el archivo docker-compose  y no el nombre que figura cuando hacemos un docker ps.  

Por ejemplo, si quisieramos monitorear el contendor de redis deberiamos poner:

 ```js
 docker-compose logs redis
 ```
 
 
 ### Mostrar solo las últimas 100 lineas
 ```js
 docker-compose logs --tail 100 nombre_docker
 ```
 
 
 
 
 
 podemos también agregar el parametro -f  para dejar una terminal abierta y monitorear en tiempo real lo que suceda.
 
 
 ## Log seguidor (follow)
 
 ```js
 docker-compose logs -f redis
 ```
 
 
 ## Hacer Backup y Restaurar Bases de Datos de Contenedores Corriendo.
 
 ```js
 # Para Backup
docker exec CONTAINER /usr/bin/mysqldump -u root --password=mi_password nombre_base > backup.sql

# Aca igual que arriba pero poniendile al archivo el nombre con la fecha y hora
docker exec CONTAINER /usr/bin/mysqldump -u root --password=mi_password nombre_base > $(date "+%b_%d_%Y_%H_%M_%S").sql

# Para hacer Restore
cat backup.sql | docker exec -i Nombre_CONTAINER /usr/bin/mysql -u root --password=mi_password nombre_base
```


# Importando una base de datos en la terminal

1 - Primeramente se debe tener el archivo __.sql__ en la carpeta del volumen compartido del Docker
2 - Una vez con el archivo en esta carpeta, se debera entrar al docker que tenga la base de datos con este comando:
```php
docker-compose exec nombre_container bash
````
Una vez entremos en la terminal del Docker, se deberá primero acceder a la carpeta del volumen mapeado, en este ejemplo es BAK
```php
cd /bak/
````
3 - Luego dentro de la carpeta, verificar que se vea el archivo .sql  que queremos importar, y ejecutar el siguiente comando:

```php
mysql -u root --password=mi_password nombre_base_datos < archivo_con_la_base.sql


# otra opcion (esta pide la contraseña en la terminal)

mysql -u useuario -p nombre_base < nombre_backup.sql
````

Si todo fue bien, con estos pasos se debió haber importado la base de datos







Aunque no es correcto indicar un password en la linea de comandos, ya que queda registrado, lo correcto sería usar una variable en el archivo .env, como se muestra en el ejemplo de abajo.


```php
docker-compose exec nombre_container /usr/bin/mysqldump -u root --password="$MYSQL_PASSWORD" nombre_basededatos > archivo_backup.sql
```


Tener en cuenta que para que me funcione el ejemplop de arriba, debo tener un archivo .env con la variable definida MYSQL_PASSWORD = aca_va_la_contraseña


# Crear Script para ejecutar un Backup automatico con CRONTAB

En caso de que queramos automatizar un backup mediante CRONTAB, el script debe ser un poco diferente, ya que si queremos correr un script normal, crontab no lo ejecutará.

Ejemplo de SCRIPT para CRONTAB, probado y funcional:

```php
#!/bin/bash

# Para que tome el docker-compose es necesario incluir este path
PATH=/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/bin

# path en donde se encuentra el docker-compose.yml
cd /home/aledc/software/systems/

# Comnando que hace el backup de un conenedor en produccion y guarda la base de datos comprimida
# y le pone como nombre de archivo la fecha y hora

docker-compose exec -T db /usr/bin/mysqldump -u root --password=MiPa$word nombre_base | gzip -9 > /home/aledc/backups/$(date "+%b_%d_%Y_%H_%M_%S").sql.gz
````

Si por alguna razón, crontab no ejecuta el script a la hora indicada, esto puede solucionarse reiniciando el servicio

```php
sudo /etc/init.d/cron restart
````


 
 

