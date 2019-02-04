# Docker Compose


```js
version: '3'
services:
  nginx:
    build: nginx
    ports:
      - "8080:80"
    volumes:
      - ./application:/var/www/html
    networks:
      - stydenet
  php:
    build: php
    volumes:
      - ./application:/var/www/html
    networks:
      - stydenet
  mysql:
    image: mysql:5.7
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
networks:
  stydenet:
    driver: "bridge"
volumes:
  redisdata:
    driver: "local"
  mysqldata:
    driver: "local"
```
