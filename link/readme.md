# Ejemplos de como linkear varios contenedores con el comando --link

Para enlazar dos contenedores existen varias formas, una de ellas consiste en indicar en el momento de hacer el docker run el comando __--link__ seguido del nombre o id del contenedor que se quiera enlazar.
De esta manera se creará un enlace entre los contenedores y esto permitirá la comunicación entre estos

aquí alguns ejemplos de docker run utilizando __--link__



```js
# Iniciar contenedor de Redis
docker run -d \
    --name redis \
    redis:alpine
 
# Iniciar contenedor de MySQL
docker run -d \
    --name=mysql \
    -e MYSQL_ROOT_PASSWORD=aledc_Password \
    -e MYSQL_DATABASE=aledc \
    -e MYSQL_USER=aledc \
    -e MYSQL_PASSWORD=ale_password \
    mysql:5.7
 
# Iniciar el contenedor de PHP-FPM
docker run -d \
    --name php \
    -v $(pwd)/application:/var/www/html \
    --link redis \
    --link mysql \
    aledc/php
 
# Iniciar el contenedor de Nginx
docker run -d \
    --name nginx \
    -p 3001:80 \
    -v $(pwd)/application:/var/www/html \
    --link php \
    aledc/nginx
```

luego es posible verificar si los contenedores están enlazados poniendo este código en el archivo index.php que ejecutará el servidor de nginx

```php

<?php
echo "Hostname 'redis' pudo encontrarse en: " . gethostbyname('redis')."\n";

$hostname='poner_el_host';
$username='poner_el_user';
$password='poner_el_pass';
$dbname='poner_nombre_base';

try {
    $dbh = new PDO("mysql:host=$hostname;dbname=$dbname", $username, $password, [
        PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION
    ]);
    echo "Connected to the database at hostname 'mysql': " . gethostbyname('mysql') . "\n";
} catch(Exception $e) {
    echo $e->getMessage();
}
```

de esta manera vemos como linkear varios contenedores con el comando __--linm nombre_contenedor__
