# Manejo de Volumenes en Docker

En Docker tenemos a nuestra disposición el uso de volúmenes para poder reutilizar la información.
Un volumen es una carpeta o un archivo dentro de nuestro equipo local que podemos compartir con nuestros contenedores
para poder tenerlos a nuestra disposición cuando los necesitemos.

Existen dos tipos de volúmenes en Docker, los que son para almacenar y los que son para compartir.

## ejemplo de dos contenedores compartiendo el mismo volumen

```
# Crear contenedor de PHP
docker run -d \
--name php \
--network stydenet \
-v $(pwd)/application:/var/www/html \ # Compartir el directorio application de nuestro equipo local
stydedocker/php
 
# Crear contenedor de Nginx
docker run -d \
--name nginx \
--network stydenet \
-v $(pwd)/application:/var/www/html \ # Compartir el directorio application de nuestro equipo local
-p 8080:80 \
stydedocker/nginx
```

es posible listar los todos los volumenes en docker con el comando __docker volume ls__

```
docker volume ls
```

el resultado será parecido a este:

```
DRIVER              VOLUME NAME
local               2da27f9739f45816aab69a7caa078fae002c15b93478d31c79f40af2d7b29aa1
local               3715b0751a3cf254005216242c29b8d8bcb3adae11e3331a9ee8dfe7bfcb3006
local               38e8a95d457a0dfce388fa00e37f8f907981193ed9064d0452085f51f8bf5c34
local               5d337ef55c260bb02ca8be0a87755ac4df0ff43aa74fd9be81da4e6c525f60e3
local               c20832844f7e66dade05194b6d4bfe47cc66162d34ea395a37b36a1f74e3e5f1
local               daf8d6709aa166ae2774f5eccb9e0fdd9b41459d4ba0e1d6b806d413413a86d5
local               root_mariadb_data
local               root_suitecrm_data
```

Como puede verse, si no se le especifica un nombre al volumen, Docker le asignará uno muy poco intuitivo.
Es por esto que se le puede dar nombre a los volumenes:

```
docker volume create nombre_volumen
```
una vez tengamos creado el volumen, este debe ser montado a un contenedor, esto debe hacerse en el momento de crear el contenedor.

```
docker run -d \
--name mysql \
--network stydenet \
-v mysqldata:/var/lib/mysql \ # Indicar volumen nombrado
-p 33060:3306 \
-e MYSQL_ROOT_PASSWORD=root \
-e MYSQL_DATABASE=styde \
-e MYSQL_USER=styde \
-e MYSQL_PASSWORD=styde \
mysql:5.7
```

### Inspeccionar volúmenes
Una vez que el contenedor esté haciendo uso de un volumen, podemos revisar en dónde se encuentra montado dicho volumen.
Si ejecutamos __docker inspect nombre_del_volumen__ veremos un array con un objeto que nos brinda entre otras cosas la info de donde se encuentra montado el volumen (__"Mountpoint":__)

```
root: docker inspect root_mariadb_data 
[
    {
        "CreatedAt": "2018-12-19T18:38:42-05:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/root_mariadb_data/_data",
        "Name": "root_mariadb_data",
        "Options": null,
        "Scope": "local"
    }
]
```



