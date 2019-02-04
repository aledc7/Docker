# Docker Network es el comando para el manejo de todo lo referido a redes dentro de Dockers

Predeterminadamente cuando se instala Docker, viene con 3 redes:  
- bridge  
- host  
- none  

### Es posible crear mas redes.

De esta manera podemos crear nuestra propia red, y asi asignar esta red a los contenedores que queremos que se vean entre si.

para crear una red solo basta con indicar este comando:

```
docker network create nombre_Red
```

luego al momento de lanzar un contenedor podemos vincularlo con al red anteriormente creada mediante el comando __--network nombre_red__ 

```
docker run -d --name redis redis:alpine --network aledc aledc/redis
```

De esta manera el contenedor de redis tiene asignada la red __aledc__ 

por consiguiente si corro otro navegador y le asigno la misma red, estos dos contenedores quedarán enlazados

```
docker run -d --name php --network aledc aledc/php
```

De esta manera ahora los contenedores __redis__ y __php__  estan enlazados y pueden comunicarse.

Para verificar esto, existen varias formas, en mi opinión la mas sencilla es inspeccionar la red que se le asignó al contenedor, y verificar que el contenedor figure en la descripcion de la red.  Es posible inspeccionar una red creada con el comando __docker inspect nombre_red__

```
docker inspect aledc
```

el resultaro será un array de objetos con la configuración de la red. 
Allí se deberá revisar que dentro del objeto __Containers__ se encuentren los contenedores que nos interese conectar.
Ademas de verificar a que contenedores pertenece la red, también nos puede interesar saber que dirección IP tiene cada contenedor, de manera de hacer un ping dentro de cada contenedor para saber si tengo conexion con otro contenedor.

```
[
    {
        "Name": "aledc",
        "Id": "584ef33ae06d0f3992b2eb468297c80c9839ba1ea36b2862c1bcd393eb9374c9",
        "Created": "2019-02-01T15:15:16.524014201-05:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.22.0.0/16",
                    "Gateway": "172.22.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "1ba3317ad3faa4df1921af517e576ccb72bfe911922be094b066287e4043219f": {
                "Name": "redis",
                "EndpointID": "db93837047e0f04021c6bf47ede77b59803e6f9efe20ebc912eb0c9930c418cd",
                "MacAddress": "02:42:ac:16:00:04",
                "IPv4Address": "172.22.0.4/16",
                "IPv6Address": ""
            },
            "ae2446cb12e077b75e12cd2f6861408cbdc2d6c1e2d9fef9dce9e0ae3e3ee833": {
                "Name": "mysql",
                "EndpointID": "316c730b16d9e042c21cd42df5ac970a6ea316d1dd8eaf7725262058753fa485",
                "MacAddress": "02:42:ac:16:00:05",
                "IPv4Address": "172.22.0.5/16",
                "IPv6Address": ""
            },
            "c30566fb50aa16ba2761ae3bd53ce4fdaedebdd6f3c7ae483b08e1b9c26c2ea5": {
                "Name": "php",
                "EndpointID": "b3ddf29cbb8777cf4a6d32f7bf73dee917dd6e318505e2b52e2e96e52ceb1c53",
                "MacAddress": "02:42:ac:16:00:02",
                "IPv4Address": "172.22.0.2/16",
                "IPv6Address": ""
            },
            "ffb6ec9976d4b52ec57f328fc79fb36b4ea54340899276a6b2f5d2158ebd5737": {
                "Name": "nginx",
                "EndpointID": "9536703af108513f858dfeed9654df79d2da8fbc88779667ab25fd60eb5b3bc4",
                "MacAddress": "02:42:ac:16:00:03",
                "IPv4Address": "172.22.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]

```

