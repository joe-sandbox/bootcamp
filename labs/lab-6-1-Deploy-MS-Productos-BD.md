# Lab 1 - Despliegue del Microservicio de Productos y Creación de Base de Datos 
## Lo que aprenderás de este lab:
En este laboratorio aprenderás y desplegaras tu primer Microservicio en Openshift uniéndolo a un servicio de base de datos NoSQL llamado Cloudant.
## Lo que necesitarás
- Una ventana de shell con el contenedor con la imagen del [lab 3](https://github.com/joeg1307/bootcamp/blob/main/labs/3-Intro-Imagen-Bootcamp.md) corriendo
- Tener el token de login para acceder a OpenShift desde el shell
- Tener las credenciales de IBM Cloud para hacer login desde el shell
- Tener un servicio de CR (Container Registry) en IBM Cloud
- Tener un ApiKey para hacer login desde Docker al CR (Container Registry)

## Desplegar la base de datos en el cluster

## Video a seguir
[Link a video](https://ibm.ent.box.com/file/869503443926)
## Pasos a seguir

## He salido del contenedor como puedo volver a entrar? 
Si saliste o reiniciaste tu computadora puedes seguir trabajando con el contenedor e iniciarlo de esta manera:
```bash
docker start -i cnbtools
```

### 1. Configura la terminal de IBM Cloud CLI - (Terminal 1)
Antes de ejecutar el comando asegúrate de cambiar los valores `<usuario>`y `<password>`por tu usuario y contraseña de IBM Cloud.
```bash
ibmcloud login -u <usuario> -p <password> -r us-south
```
Correremos el siguiente comando para saber a que grupo de recursos nos dirigiremos.
```bash
ibmcloud resource groups
```
Utilizaremos el grupo de recursos que diga `default` o `Default`.
```bash
ibmcloud target -g default
```

### 2. Configurar login de openshift - (Terminal 1)
Antes de ejecutar el comando asegúrate obtener el token desde la interfaz de usuario de openshift.
```bash
oc login --token=<token> --server=<server>
```
Nos movemos al proyecto default.
```bash
oc project default
```

### 3. Configurar login del CR en Docker - (Terminal 2)
El siguiente paso deberás crear un namespace en la UI de IBM Cloud dirigete al minuto ... del video para saber como hacer esta parte. 
Antes de ejecutar el comando asegúrate de abrir una nueva terminal de shell en tu computadora.
```bash
docker login -u iamapikey -p <apikey> us.icr.io/<namespace con nuestras iniciales>
```
La respuesta que esperamos después de correr el comando anterior es 
```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

### 4. Movamos nuestras terminales de carpeta - (Terminal 1 y Terminal 2)
**Esto lo necesitamos hacer en las 2 terminales que tenemos abiertas**
El siguiente paso es movernos a la carpeta necesaria para ejecutar nuestro Lab 1 y esto lo haremos con el siguiente comando.
Para windows:
```bash
cd %USERPROFILE%
cd mybootcamp
cd LightBlueCompute
cd mysql
```
Para MacOS:
```bash
cd ~
cd mybootcamp
cd LightBlueCompute
cd mysql
```

### 5. Crear imagen y subirla a CR - (Terminal 2)
El siguiente paso para poder ejecutar nuestro lab es construir la imagen de nuestro contenedor y subirla a nuestro repositorio de CR (Container Registry).
Debemos modificar el `<namespace>` y ponerle nuestras iniciales a nuestro repo `<iniciales>` y no olvidemos incluir el punto del final.
```bash
docker build -t us.icr.io/<namespace>/mysql-<iniciales> .
```
El comando va a tardar aproximadamente 1 minuto en ejecutarse, prácticamente estamos construyendo nuestra imagen de docker y poniéndole un tag que haga referencia a nuestro repositorio de imagenes en Container Registry.
El resultado que debemos obtener es el siguiente (pueden variar varias lineas pero nos debe de decir `(10/10) FINISHED`).
```bash
[+] Building 74.8s (10/10) FINISHED
 => [internal] load build definition from Dockerfile                                                                                                         0.1s
 => => transferring dockerfile: 181B                                                                                                                         0.0s
 => [internal] load .dockerignore                                                                                                                            0.0s
 => => transferring context: 2B                                                                                                                              0.0s
 => [internal] load metadata for docker.io/library/mysql:5.7                                                                                                 2.0s
 => [auth] library/mysql:pull token for registry-1.docker.io                                                                                                 0.0s
 => [1/4] FROM docker.io/library/mysql:5.7@sha256:b8814059bbd9c80b78fe4b2b0b70cd70fe3772b3c5d8ee1edfa46791db3224f9                                          71.5s
 => => resolve docker.io/library/mysql:5.7@sha256:b8814059bbd9c80b78fe4b2b0b70cd70fe3772b3c5d8ee1edfa46791db3224f9                                           0.0s
 => => sha256:f23cbf2ecc5d6ad68efaf326f8ff1c8b4adfab8faad61315440d21c396dd0160 1.73kB / 1.73kB                                                               0.5s
...
 => => sha256:3dc585bfc69349e8092a6e6e66c66562a344178408b2bd607cbb064e084894f7 121B / 121B                                                                  14.2s
 => => extracting sha256:b380bbd43752f83945df8b5d1074fef8dd044820e7d3aef33b655a2483e030c7                                                                    1.6s
...
 => => extracting sha256:3dc585bfc69349e8092a6e6e66c66562a344178408b2bd607cbb064e084894f7                                                                    0.0s
 => [internal] load build context                                                                                                                            0.2s
 => => transferring context: 12.96kB                                                                                                                         0.2s
 => [2/4] ADD scripts/load-data.sh load-data.sh                                                                                                              0.1s
 => [3/4] ADD scripts/load-data.sql load-data.sql                                                                                                            0.0s
 => [4/4] RUN chmod u+x load-data.sh                                                                                                                         0.5s
 => exporting to image                                                                                                                                       0.0s
 => => exporting layers                                                                                                                                      0.0s
 => => writing image sha256:a8e46ce7c4bacf5b864402cd27e75a18a44d3847fd015f3e8737cc47e425c2d2                                                                 0.0s
 => => naming to us.icr.io/cnb-bootcamp/mysql-<iniciales>                                                                                                    0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
```
Para comprobar que la construcción de nuestra imagen se hizo correctamente podemos correr el siguiente comando, el cual nos ayuda a listar las imágenes que tenemos en nuestra computadora.
```bash
docker images
```
Y deberíamos de poder ver la imagen que acabamos de construir (de no ser el caso debemos revisar si la construcción se ejecutó correctamente).
```bash
REPOSITORY                                          TAG            IMAGE ID       CREATED         SIZE
us.icr.io/<namespace>/mysql-<iniciales>             latest         a8e46ce7c4ba   2 minutes ago   448MB
```
Una vez que estamos seguros que mi imagen se construyó correctamente debemos subir la imagen a nuestro CR (Container Registry) por lo tanto corremos el siguiente comando, recordemos modificar los campos de `<namespace>` e `<iniciales>`.
```bash
docker push us.icr.io/<namespace>/mysql-<iniciales>
```
Debemos recibir el siguiente resultado que nos indica que se subió correctamente la imagen a nuestro repositorio.
```bash
Using default tag: latest
The push refers to repository [us.icr.io/<namespace>/mysql-<iniciales>
3a1268364f82: Pushed
43a342766087: Pushed
e72850563fd4: Pushed
ab3bfc52aeb6: Pushed
cf0cf35ea5aa: Pushed
5a3aaf958a91: Pushed
7b963bc4ebf4: Pushed
f8389e4e632e: Pushed
b58f3b77172b: Pushed
17e5430d2956: Pushed
e050d4242497: Pushed
8b73ecde66c0: Pushed
90e0289c2b06: Pushed
e81bff2725db: Pushed
latest: digest: sha256:c0bfa36a5b12fd370b3bfed68240ccb8bc174655f041997b44e24b6c2b16e376 size: 3243
```

### 6. Imagen subida a CR - (Terminal 1)
Corroboremos que la imagen se subió correctamente a nuestro Container Registry.
Debemos modificar el `<namespace>` y ponerle nuestras iniciales a nuestro repo `<iniciales>` y no olvidemos incluir el punto del final.
```bash
ic cr image-inspect us.icr.io/<namespace>/mysql-<iniciales>:latest
```
Este comando nos permite pedirle información a nuestro CR sobre la imagen que acabamos de subir deberíamos recibir algo por el estilo.
```bash
Inspecting image 'us.icr.io/<namespace>/mysql-<iniciales>:latest'...

{
    "Id": "sha256:a8e46ce7c4bacf5b864402cd27e75a18a44d3847fd015f3e8737cc47e425c2d2",
    "Parent": "",
    "Comment": "",
    "Created": "2021-10-14T17:20:25.7806097Z",
    "Container": "",
    "ContainerConfig": {},
    "DockerVersion": "",
    "Author": "",
    "Config": {},
    "Architecture": "amd64",
    "Os": "linux",
    "Size": 154790406,
    "VirtualSize": 154790406,
    "RootFS": {
        "Type": "layers",
        "Layers": [
            "sha256:e81bff2725dbc0bf2003db10272fef362e882eb96353055778a66cda430cf81b",
            ...
            "sha256:e72850563fd4c006fba7d0637148209fd08ea8f13a31fdf49e123a25e3455f27",
        ]
    },
    "ManifestType": "application/vnd.docker.distribution.manifest.v2+json"
}

OK
```
Con este comando confirmamos que nuestra imagen ya se encuentra en la nube en nuestra instancia de CR.

### 7. Modifiquemos el YAML - (Terminal 2)
Una vez que tenemos nuestra imagen en el CR podemos ahora desplegar esa imagen en OpenShift, para eso necesitamos movernos primero de carpeta y modificar unas líneas de código que tenemos en el YAML.
```bash
cd kubernetes
```
Abrimos el archivo mysql.yml en una ventana de VSCode o escribimos el comando para abrirlo directamente desde la consola.
```bash
code .
```
Una vez con el archivo abierto podemos comenzar a modificar primero la línea **18**, pondremos la liga a nuestra imagen de MySQL (recuerden cambiar el `<namespace>` e `<inciales>`).
```bash
image: "us.icr.io/<namespace>/mysql-<iniciales>"
```
Guardamos el archivo y continuamos con el despliegue de nuestro servicio en OpenShift

### 8. Despleguemos en OpenShift - (Terminal 1)
Cuando tengamos el archivo YAML modificado podremos continuar con el despliegue del servicio, es necesario estar en la terminal 1 (dentro del contenedor).
Primero nos movemos a la carpeta kubenetes, que es la carpeta que contiene nuestro archivo mysql.yml
```bash
cd kubernetes
```
Ahora podemos correr el comando que desplegará nuestro servicio dentro del cluster.
```bash
oc create -f mysql.yml
```
Si o si debemos de recibir este mensaje como salida.
```bash
deployment.apps/mysql-lightblue-deployment created
service/mysql-lightblue-service created
```
El próximo reto es verificar que nuestro pod se creó correctamente, tenemos un comando el cual nos arrojará la información de nuestro proyecto, no mostrará los pods, servicios, replicas set y despliegues. El comando es el siguiente.
```bash
oc get all
```
Deberíamos recibir una respuesta como la siguiente.
```bash
NAME                                              READY            STATUS        RESTARTS         AGE
pod/mysql-lightblue-deployment-86b9d8c564-fdf66   1/1              Running       0                2m47s

NAME                              TYPE            CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/mysql-lightblue-service   NodePort        <cluster-ip>     <none>        3306:30006/TCP   2m46s

NAME                                              READY            UP-TO-DATE    AVAILABLE        AGE
deployment.apps/mysql-lightblue-deployment        1/1              1             1                2m48s

NAME                                                    DESIRED    CURRENT       READY            AGE
replicaset.apps/mysql-lightblue-deployment-86b9d8c564   1          1             1                2m48s
```
Tenemos que ver principalmente el estatus de nuestro pod, debe de estar **Running** en caso de no tener el estatus debemos revisar el detalle del pod con el siguiente comando.
Debemos de editar `<nombre-pod>` el cual se encuentra en la parte de arriba y es el que viene en `pod/<este-es-el-nombre-del-pod>`, no deben de incluir el `pod/`
```bash
oc describe pod <nombre-pod>
```
El error mas frecuente es `ErrImagePull` el cual nos aparecería de esta manera.
```bash
Events:
  Type     Reason          Age                From               Message
  ----     ------          ----               ----               -------
  Warning  Failed          27s (x2 over 43s)  kubelet            Error: ErrImagePull
  Normal   BackOff         12s (x2 over 42s)  kubelet            Back-off pulling image "s.icr.io/<namespace>/mysql-<iniciales>"
  Warning  Failed          12s (x2 over 42s)  kubelet            Error: ImagePullBackOff
  Normal   Pulling         0s (x3 over 43s)   kubelet            Pulling image "s.icr.io/<namespace>/mysql-<iniciales>"
```
Si este es el error encontrado hay que verificar que solamente editamos la línea **18** en el YAML y asegurarnos que la URL de nuestra imagen dentro del CR es correcta. En el ejemplo anterior faltó poner una `u` en la URL debería de ser `us.icr.io/<namespace>/mysql-<iniciales>`. Después de corregir nuestro YAML necesitamos correr el siguiente comando.
```bash
oc apply -f mysql.yml
```
Con este comando reconfiguramos nuestro despliegue y automáticamente trata de crear otro pod con la nueva configuración, después de correr el comando podemos volver a correr el comando siguiente para asegurarnos que ahora si está corriendo nuestro servicio.
```bash
oc get all
```

### 9. Ingresemos a nuestro contenedor - (Terminal 1)
Una vez tengamos nuestra aplicación corriendo en el clúster lo siguiente es llenar la base de datos con información, para esto necesitamos acceder a nuestro contenedor que tenemos ya desplegado en nuestro clúster, para esto vamos a necesitar el nombre de nuestro pod el cual lo podemos obtener con el siguiente comando.
```bash
oc get pods
```
Este comando nos da el resumen de cuantos pods tenemos en nuestro proyecto de OpenShift, debemos copiar el nombre completo del pod que comienza con `mysql-lightblue`, la respuesta del comando anterior va a ser semejante al siguiente.
```bash
NAME                                          READY   STATUS    RESTARTS   AGE
mysql-lightblue-deployment-86b9d8c564-zrvkw   1/1     Running   0          5m52s
```
En este caso el nombre de mi pod es `mysql-lightblue-deployment-86b9d8c564-zrvkw`.
El siguiente comando nos servirá para entrar a nuestro contenedor, recordemos sustituir `<nombre-pod>` por el nombre del pod que nosotros obtuvimos con el comando anterior.
```bash
oc exec -it <nombre-pod> -- bash
```
Automaticamente el nombre de nuestra terminal cambiara a ser algo parecido a lo siguiente.
```bash
1000680000@mysql-lightblue-deployment-86b9d8c564-zrvkw:/$
```
Eso nos indica que estamos dentro de nuestro pod.

### 10. Llenar la base de datos - (Terminal 1)
Dentro del pod ahora correremos el comando para llenar la base de datos.
```bash
bash /load-data.sh
```
Debemos recibir un mensaje el cual nos podría causar confusión pero es importante notar que al final diga `Data loaded to invetorydb.items.`.
```bash
User not provided. Attempting container environment variable...
Password not provided. Attempting container environment variable...
Host not provided. Using localhost...
Port not provided. Using 3306...
Database not provided. Attempting container environment variable...
mysql: [Warning] Using a password on the command line interface can be insecure.

Data loaded to inventorydb.items.
```
Para corroborar que los datos fueron insertados correctamente, debemos correr el siguiente comando.
```bash
mysql -udbuser -pPass4dbUs3R -P30006
```
El cual nos dará el siguiente resultado.
```bash
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.7.35 MySQL Community Server (GPL)

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```
Y nuestra terminal volverá a cambiar y ahora veremos lo siguiente.
```bash
mysql>
```
Eso nos indica que ahora estamos dentro del cli de la base de datos, el siguiente comando que correremos es un Query a la base de datos para contar el numero de registros.
```bash
select count(*) from inventorydb.items;
```
Este comando nos debería regresar el conteo de datos dentro de la tabla.
```bash
+----------+
| count(*) |
+----------+
|       12 |
+----------+
1 row in set (0.00 sec)
```
Deberíamos tener 12 registros, si no tenemos 12 hubo un problema y debemos de volver al comando donde ejecutamos `bash /load-data.sh` para esto necesitamos salir del cli de la base de datos con el comando `exit`
Si tenemos 12 registros vamos ahora a salir del cli de la base de datos con el comando
```bash
exit
```
Después volvemos a correr el comando anterior para salir del contenedor de MySQL.
```bash
exit
```

## Desplegar Microservicio en el Clúster
Una vez que tenemos la base de datos en el clúster y tiene los datos almacenados, podemos comenzar con la siguiente etapa que es desplegar nuestro microservicio y después conectar la base de datos.

##### Video a seguir
[Link a video](https://ibm.ent.box.com/file/869503443926)


### 1. Movernos de carpeta - (Terminal 1 y Terminal 2)
Estando dentro de la carpeta kubernetes en las 2 terminales (como quedaron en el ejercicio de MySQL), tenemos que salir de estas carpetas y movernos a la carpeta `catalog` con el siguiente comando (El comando hay que ejecutarlo en las 2 terminales).
```bash
cd ../../catalog
```

### 2. Construir el Microservicio - (Terminal 1)
El siguiente comando nos servirá para construir el microservicio, va a entre 1 a 2 minutos en ejecutarse al final deberíamos de recibir un mensaje que contenga `BUILD SUCCESSFUL`.
```bash
./gradlew build -x test
```
Respuesta final esperada.
```bash
:processResources
:classes
:findMainClass
:jar
:bootRepackage
:assemble
:check
:build

BUILD SUCCESSFUL

Total time: 54.819 secs
```
Ahora construiremos el archivo de docker y de igual manera obtendremos el mismo mensaje como resultado. 
```bash
./gradlew docker
```
Resultado esperado.
```bash
:docker

BUILD SUCCESSFUL

Total time: 6.625 secs
```

### 3. Construir y subir la imagen de Docker - (Terminal 2)
Para construir la imagen de docker debemos movernos de carpeta con el siguiente comando.
```bash
cd docker 
```
Ahora construiremos la imagen de nuestro contenedor y la subiremos a nuestro repositorio de CR (Container Registry).
Recuerden debemos modificar el `<namespace>` y ponerle nuestras iniciales a nuestro repo `<iniciales>` y no olvidemos incluir el punto del final.
```bash
docker build -t us.icr.io/<namespace>/catalog-<iniciales> .
```
El comando va a tardar aproximadamente 1 minuto en ejecutarse, prácticamente estamos construyendo nuestra imagen de docker y poniéndole un tag que haga referencia a nuestro repositorio de imágenes en Container Registry.
El resultado que debemos obtener es el siguiente (pueden variar varias líneas pero nos debe de decir `(10/10) FINISHED`).
```bash
[+] Building 24.6s (11/11) FINISHED
 => [internal] load build definition from Dockerfile                                                                                                         0.0s
 => => transferring dockerfile: 265B                                                                                                                         0.0s
 => [internal] load .dockerignore                                                                                                                            0.0s
 => => transferring context: 2B                                                                                                                              0.0s
 => [internal] load metadata for docker.io/library/openjdk:8-jre-alpine                                                                                      2.0s
 => [auth] library/openjdk:pull token for registry-1.docker.io                                                                                               0.0s
 => [internal] load build context                                                                                                                            0.4s
 => => transferring context: 28.25MB                                                                                                                         0.4s
 => [1/5] FROM docker.io/library/openjdk:8-jre-alpine@sha256:f362b165b870ef129cbe730f29065ff37399c0aa8bcab3e44b51c302938c9193                               19.3s
 => => resolve docker.io/library/openjdk:8-jre-alpine@sha256:f362b165b870ef129cbe730f29065ff37399c0aa8bcab3e44b51c302938c9193                                0.0s
 => => sha256:f362b165b870ef129cbe730f29065ff37399c0aa8bcab3e44b51c302938c9193 1.64kB / 1.64kB                                                               0.0s
 ...
 => => sha256:b6abafe80f63b02535fc111df2ed6b3c728469679ab654e03e482b6f347c9639 54.94MB / 54.94MB                                                            17.7s
 => => extracting sha256:e7c96db7181be991f19a9fb6975cdbbd73c65f4a2681348e63a141a2192a5f10                                                                    0.2s
 ...
 => => extracting sha256:b6abafe80f63b02535fc111df2ed6b3c728469679ab654e03e482b6f347c9639                                                                    1.3s
 => [2/5] ADD app.jar app.jar                                                                                                                                0.1s
 => [3/5] RUN apk --no-cache update && apk add jq bash bc                                                                                                    2.4s
 => [4/5] RUN bash -c 'touch /app.jar'                                                                                                                       0.3s
 => [5/5] COPY startup.sh startup.sh                                                                                                                         0.0s
 => exporting to image                                                                                                                                       0.3s
 => => exporting layers                                                                                                                                      0.3s
 => => writing image sha256:b09d36ec122b4fffd1fce55f7558318071f62e594bb619e318f2e06fc3a9ad0c                                                                 0.0s
 => => naming to us.icr.io/cnb-bootcamp/catalog-mavg                                                                                                         0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
```
Para comprobar que la construcción de nuestra imagen se hizo correctamente podemos correr el siguiente comando, el cual nos ayuda a listar las imágenes que tenemos en nuestra computadora.
```bash
docker images
```
Y deberíamos de poder ver la imagen que acabamos de construir (de no ser el caso debemos revisar si la construcción se ejecutó correctamente).
```bash
REPOSITORY                                          TAG            IMAGE ID       CREATED         SIZE
us.icr.io/<namespace>/catalog-<iniciales>           latest         a8e46ce7c4ba   2 minutes ago   448MB
```
Una vez que estamos seguros de que mi imagen se construyó correctamente debemos subir la imagen a nuestro CR (Container Registry) por lo tanto corremos el siguiente comando, recordemos modificar los campos de `<namespace>` e `<iniciales>`.
```bash
docker push us.icr.io/<namespace>/catalog-<iniciales>
```
Debemos recibir el siguiente resultado que nos indica que se subió correctamente la imagen a nuestro repositorio.
```bash
Using default tag: latest
The push refers to repository [us.icr.io/<namespace>/catalog-<iniciales>
1d004f8992a3: Pushed
b5dd8d049af1: Pushed
aad17c1cbde0: Pushed
5677749c5065: Pushed
edd61588d126: Pushed
9b9b7f3d56a0: Pushed
f1b5933fe4b5: Pushed
latest: digest: sha256:cee5d20c9ca894d353d19c156d5774a39c4af4235316fb1346bd2dc869694c32 size: 1789
```

### 4. Imagen subida a CR - (Terminal 1)
Corroboremos que la imagen se subió correctamente a nuestro Container Registry.
Debemos modificar el `<namespace>` y ponerle nuestras iniciales a nuestro repo `<iniciales>` y no olvidemos incluir el punto del final.
```bash
ic cr image-inspect us.icr.io/<namespace>/catalog-<iniciales>:latest
```
Este comando nos permite pedirle información a nuestro CR sobre la imagen que acabamos de subir deberíamos recibir algo por el estilo.
```bash
Inspecting image 'us.icr.io/<namespace>/catalog-<iniciales>:latest'...

{
    "Id": "sha256:b09d36ec122b4fffd1fce55f7558318071f62e594bb619e318f2e06fc3a9ad0c",    
    "Parent": "",
    "Comment": "",
    "Created": "2021-10-14T17:20:25.7806097Z",
    "Container": "",
    "ContainerConfig": {},
    "DockerVersion": "",
    "Author": "",
    "Config": {},
    "Architecture": "amd64",
    "Os": "linux",
    "Size": 154790406,
    "VirtualSize": 154790406,
    "RootFS": {
        "Type": "layers",
        "Layers": [
            "sha256:f1b5933fe4b5f49bbe8258745cf396afe07e625bdab3168e364daf7c956b6b81",
            ...
            "sha256:1d004f8992a331664e12946646decd2ab90178f10ceeb86ab9c1e3a28ecf72f1"
        ]
    },
    "ManifestType": "application/vnd.docker.distribution.manifest.v2+json"
}

OK
```
Con este comando confirmamos que nuestra imagen ya se encuentra en la nube en nuestra instancia de CR.

### 5. Modifiquemos el YAML - (Terminal 2)
Una vez que tenemos nuestra imagen en el CR podemos ahora desplegar esa imagen en OpenShift, para eso necesitamos movernos primero de carpeta y modificar unas líneas de código que tenemos en el YAML.
```bash
cd ../kubernetes
```
Abrimos el archivo catalog.yml en una ventana de VSCode o escribimos el comando para abrirlo directamente desde la consola.
```bash
code .
```
Una vez con el archivo abierto podemos comenzar a modificar primero la línea **18**, pondremos la liga a nuestra imagen de catalog (recuerden cambiar el `<namespace>` e `<inciales>`).
```bash
image: "us.icr.io/<namespace>/catalog-<iniciales>:latest"
```
Guardamos el archivo y continuamos con el despliegue de nuestro servicio en OpenShift

### 6. Despleguemos en OpenShift - (Terminal 1)
Cuando tengamos el archivo YAML modificado podremos continuar con el despliegue del servicio, es necesario estar en la terminal 1 (dentro del contenedor).
Primero nos movemos a la carpeta kubenetes, que es la carpeta que contiene nuestro archivo catalog.yml
```bash
cd kubernetes
```
Ahora podemos correr el comando que desplegará nuestro servicio dentro del cluster.
```bash
oc create -f catalog.yml
```
Si o si debemos de recibir este mensaje como salida.
```bash
deployment.apps/catalog-lightblue-deployment created
service/catalog-lightblue-service created
```
El próximo reto es verificar que nuestro pod se creó correctamente, tenemos un comando el cual nos arrojará la información de nuestro proyecto, no mostrará los pods, servicios, replicas set y despliegues. El comando es el siguiente.
```bash
oc get all
```
Deberíamos recibir una respuesta como la siguiente.
```bash
NAME                                               READY   STATUS    RESTARTS   AGE
pod/catalog-lightblue-deployment-b4c5f97cd-b5xsl   1/1     Running   0          23s
pod/mysql-lightblue-deployment-86b9d8c564-zrvkw    1/1     Running   0          157m

NAME                                TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/catalog-lightblue-service   NodePort   <cluster-ip>    <none>        8081:30111/TCP   22s
service/mysql-lightblue-service     NodePort   <cluster-ip>    <none>        3306:30006/TCP   157m

NAME                                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/catalog-lightblue-deployment   1/1     1            1           24s
deployment.apps/mysql-lightblue-deployment     1/1     1            1           157m

NAME                                                     DESIRED   CURRENT   READY   AGE
replicaset.apps/catalog-lightblue-deployment-b4c5f97cd   1         1         1       24s
replicaset.apps/mysql-lightblue-deployment-86b9d8c564    1         1         1       157m
```
Tenemos que ver principalmente el estatus de nuestro pod, debe de estar **Running** en caso de no tener el estatus debemos revisar el detalle del pod con el siguiente comando.
Debemos de editar `<nombre-pod>` el cual se encuentra en la parte de arriba y es el que viene en `pod/<este-es-el-nombre-del-pod>`, no deben de incluir el `pod/`
```bash
oc describe pod <nombre-pod>
```
El error mas frecuente es `ErrImagePull` el cual nos aparecería de esta manera.
```bash
Events:
  Type     Reason          Age                From               Message
  ----     ------          ----               ----               -------
  Warning  Failed          27s (x2 over 43s)  kubelet            Error: ErrImagePull
  Normal   BackOff         12s (x2 over 42s)  kubelet            Back-off pulling image "s.icr.io/<namespace>/catalog-<iniciales>"
  Warning  Failed          12s (x2 over 42s)  kubelet            Error: ImagePullBackOff
  Normal   Pulling         0s (x3 over 43s)   kubelet            Pulling image "s.icr.io/<namespace>/catalog-<iniciales>"
```
Si este es el error encontrado hay que verificar que solamente editamos la línea **18** en el YAML y asegurarnos que la URL de nuestra imagen dentro del CR es correcta. En el ejemplo anterior faltó poner una `u` en la URL debería de ser `us.icr.io/<namespace>/catalog-<iniciales>`. Después de corregir nuestro YAML necesitamos correr el siguiente comando.
```bash
oc apply -f catalog.yml
```
Con este comando reconfiguramos nuestro despliegue y automáticamente trata de crear otro pod con la nueva configuración, después de correr el comando podemos volver a correr el comando siguiente para asegurarnos que ahora si está corriendo nuestro servicio.
```bash
oc get all
```

### 7. Exponer el microservicio con una ruta - (Terminal 1)
Ahora que tenemos nuestro microservicio desplegado en OpenShift necesitamos exponerlo para hacer una prueba y verificar que está funcionando correctamente.
**Con el siguiente comando permitimos el trafico de afuera del clúster a nuestro microservicio** por lo tanto será necesario que después de hacer la prueba borremos la ruta para que de nuevo solo sea accesible dentro del clúster. 
Primero debemos de encontrar el nombre de nuestro servicio y para eso usaremos el siguiente comando.
```bash

```
El cual nos arrojará los servicios dentro de nuestro proyecto. Debemos de copiar el nombre de nuestro servicio de catalog `catalog-lightblue-service`.
``bash
NAME                                TYPE        CLUSTER-IP        EXTERNAL-IP   PORT(S)          AGE
catalog-lightblue-service           NodePort    <ip-del-cluser>   <none>        8081:30111/TCP   58m
mysql-lightblue-service             NodePort    <ip-del-cluser>   <none>        3306:30006/TCP   3h35m
```
Ahora correremos el siguiente comando para exponer ese servicio en especifico.
```bash
oc expose service <nombre-del-servicio>
```
Obtendremos una respuesta como la siguiente.
```bash
route.route.openshift.io/<nombre-del-servicio> exposed
```
Eso nos indica que OpenShift le asignó una ruta a nuestro microservicio y ya es accesible desde afuera del clúster, para saber la URL debemos de poner el siguiente comando.
```bash
oc get routes
```
Nos arrojará las rutas que tenemos en nuestro proyecto.
```bash
NAME                        HOST/PORT                       PATH    SERVICES                    PORT   TERMINATION   WILDCARD
catalog-lightblue-service   <url-de-nuestro-microservicio>          catalog-lightblue-service   8081                 None
```

### 8. Probar nuestro microservicio - (Terminal 2)
Ahora probaremos si nuestro microservicio tiene la conexión a la base de datos de MySQL con la ruta que sacamos anteriormente, para eso necesitamos `<url-de-nuestro-microservicio>` y la vamos a sustituir en el siguiente comando.
```bash
curl "http://<url-de-nuestro-microservicio>/micro/items/13401"
```
Con esto estamos haciendo una petición a nuestro microservicio el cual va a buscar la información a la base de datos y después la regresa para nosotros poderla ver, si todo salió bien recibiríamos una respuesta como la siguiente.
```bash
{"id":13401,"name":"Dayton Meat Chopper","description":"Punched-card tabulating machines and time clocks were not the only products offered by the young IBM. Seen here in 1930, manufacturing employees of IBM's Dayton Scale Company are assembling Dayton Safety Electric Meat Choppers. These devices, which won the Gold Medal at the 1926 Sesquicentennial International Exposition in Philadelphia, were produced in both counter base and pedestal styles (5000 and 6000 series, respectively). They included one-quarter horsepower models, one-third horsepower machines (Styles 5113, 6113F and 6213F), one-half horsepower types (Styles 5117, 6117F and 6217F) and one horsepower choppers (Styles 5128, 6128F and 6228F). Prices in 1926 varied from admin80 to bluemix-sandbox-dal-9-portal.5.dblayer.com75. Three years after this photograph was taken, the Dayton Scale Company became an IBM division, and was sold to the Hobart Manufacturing Company in 1934.","price":4599,"img":"meat-chopper.jpg","stock":1000,"imgAlt":"Dayton Meat Chopper"}
```
Esto nos indica que nuestro microservicio está funcionando bien y tenemos la conexión a la base de datos.


### 9. Borrar la ruta - (Terminal 1)
Ahora necesitamos borrar la ruta para que el flujo que venga desde afuera del clúster no tenga acceso directo a nuestro microservicio y con eso a nuestra base de datos, por lo tanto, necesitamos correr el siguiente comando. Recuerden sustituir `<nombre-de-la-ruta>` por el nombre real de su ruta.
```bash
oc delete route <nombre-de-la-ruta>
```
Por ejemplo 
```bash
oc delete route catalog-lightblue-service
```
De esta manera estaríamos borrando la ruta por lo tanto si intentamos volver a pedir información con el `curl` de arriba nos arrojaría que no existe la URL.

Con esto terminamos el primer laboratorio.

