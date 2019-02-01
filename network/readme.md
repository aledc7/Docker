# Docker Network sirve para manejar todo lo referido a redes en Dockers

Predeterminadamente cuando se instala Docker, viene con 3 redes; __bridge , host y none __  pero es posible crear mas redes.

De esta manera podemos crear nuestra propia red, y asi asignar esta red a los contenedores que queremos que se vean entre si.

para crear una red solo basta con indicar este comando:

```
docker network create nombre_Red
```

luego al lanzar un contenedor podemos vincular
