# Ejemplos de como linkear varios contenedores con el comando --link

```js
# Iniciar contenedor de Redis
docker run -d \
    --name redis \
    redis:alpine
 
# Iniciar contenedor de MySQL
docker run -d \
    --name=mysql \
    -e MYSQL_ROOT_PASSWORD=root \
    -e MYSQL_DATABASE=styde \
    -e MYSQL_USER=styde \
    -e MYSQL_PASSWORD=secret \
    mysql:5.7
 
# Iniciar el contenedor de PHP-FPM
docker run -d \
    --name php \
    -v $(pwd)/application:/var/www/html \
    --link redis \
    --link mysql \
    stydedocker/php
 
# Iniciar el contenedor de Nginx
docker run -d \
    --name nginx \
    -p 80:80 \
    -v $(pwd)/application:/var/www/html \
    --link php \
    stydedocker/nginx
```

luego es posible verificar si los contenedores están enlazados poniendo este código en el archivo index.php que ejecutará el servidor de nginx

```php

<?php
echo "Hostname 'redis' can be found at: " . gethostbyname('redis')."\n";

$hostname='mysql';
$username='app-user';
$password='app-pass';
$dbname='my-app';

try {
    $dbh = new PDO("mysql:host=$hostname;dbname=$dbname", $username, $password, [
        PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION
    ]);
    echo "Connected to the database at hostname 'mysql': " . gethostbyname('mysql') . "\n";
} catch(Exception $e) {
    echo $e->getMessage();
}
```
