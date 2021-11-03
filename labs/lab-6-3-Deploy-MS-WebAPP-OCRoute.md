# Lab 3 - Despliegue del Microservicio Web

## Lo que aprenderas de este lab:

En este laboratorio aprenderas y desplegaras el Microservicio de web app en Openshift.

## Lo que necesitarás

- Una ventana de shell con el contenedor con la imagen del [lab 3](https://github.com/joeg1307/bootcamp/blob/main/labs/lab-3-Intro-Imagen-Bootcamp.md) corriendo

- Tener tu propio namespace dentro del cluster de OpenShift

- Tener el token de login para acceder a OpenShift desde el shell

- Tener las credenciales de IBM Cloud para hacer login desde el shell

- Tener un servicio de CR (Container Registry) en IBM Cloud

- Tener un ApiKey para hacer login desde Docker al CR (Container Registry)

​

## Video a seguir

[Link a video](https://ibm.box.com/s/mp1m1rub0wh4jlk2hbu9rej9qklsdgt1)

## Pasos a seguir

​

## He salido del contenedor como puedo volver a entrar?

Si saliste o reiniciaste tu computadora puedes seguir trabajando con el contenedor e iniciarlo de esta manera:

```bash
docker start -i cnbtools
```

​

### 1. Configura la terminal de IBM Cloud CLI - (Terminal 1)

Antes de ejecutar el comando asegurate de cambiar los valores `<usuario>`y `<password>`por tu usuario y contraseña de IBM Cloud.

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
​

### 2. Configurar login de openshift - (Terminal 1)

Antes de ejecutar el comando asegurate obtener el token desde la interfaz de usuario de openshift.

```bash
oc login --token=<token> --server=<server>
```

Nos movemos a nuestro proyecto.

```bash
oc login --token=<token> --server=<server>
```

​

### 3. Configurar login del CR en Docker - (Terminal 2)

Antes de ejecutar el comando asegurate de abrir una nueva terminal de shell en tu computadora.

```bash
docker login -u iamapikey -p <apikey> us.icr.io/<namespace con nuestras iniciales>
```

La respuesta que esperamos despues de correr el comando anterior es 

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.

Login Succeeded
```

​

### 4. Movamos nuestras terminales de carpeta - (Terminal 1 y Terminal 2)

**Esto lo necesitamos hacer en las 2 terminales que tenemos abiertas**

El siguiente paso es movernos a la carpeta necesaria para ejecutar nuestro Lab 1 y esto lo haremos con el siguiente comando.

Para windows:

```bash
cd %USERPROFILE%

cd mybootcamp

cd LightBlueCompute
```

Para MacOS:

```bash
cd ~

cd mybootcamp

cd LightBlueCompute
```

​

### 5. Movernos de carpeta - (Terminal 1 y Terminal 2)

A continuación vamos a desplegar el siguiente micro-servicio, por lo que nos moveremos a su respectivo directorio: `web-app-lite/config` con el siguiente comando (Ambas terminales deben estar en el mismo directorio).

```bash
cd ../../web-app-lite/config
```

Abrimos el archivo default.json en una ventana de VSCode o escribimos el comando para abrirlo directamente desde la consola.

```bash
code .
```

Una vez con el archivo abierto podemos comenzar a modificar primero la línea **18**, pondremos la liga a nuestra imagen de catalog (recuerden cambiar el `<namespace>` e `<iniciales>`).

​

### 6. Construir y subir la imagen de Docker - (Terminal 2)

Para construir la imagen de docker debemos movernos de carpeta con el siguiente comando.

```bash
cd ../
```

Ahora construiremos la imagen de nuestro contenedor y la subiremos a nuestro repositorio de CR (Container Registry).
Recuerden debemos modificar el `<namespace>` y ponerle nuestras iniciales a nuestro repo `<iniciales>` y no olvidemos incluir el punto del final.

```bash
docker build -t us.icr.io/<namespace>/webapp-<iniciales> .
```

El comando va a tardar aproximadamente 1 minuto en ejecutarse, prácticamente estamos construyendo nuestra imagen de docker y poniéndole un tag que haga referencia a nuestro repositorio de imágenes en Container Registry.
El resultado que debemos obtener es el siguiente (pueden variar varias líneas pero nos debe de decir `(11/11) FINISHED`).

```bash
[+] Building 8.4s (11/11) FINISHED                                                                         
 => [internal] load build definition from Dockerfile                                                  0.0s
 => => transferring dockerfile: 247B                                                                  0.0s
 => [internal] load .dockerignore                                                                     0.0s
 => => transferring context: 2B                                                                       0.0s
 => [internal] load metadata for docker.io/library/openjdk:8-jre-alpine                               3.2s
 => [auth] library/openjdk:pull token for registry-1.docker.io                                        0.0s
 => CACHED [1/5] FROM docker.io/library/openjdk:8-jre-alpine@sha256:f362b165b870ef129cbe730f29065ff3  0.0s
 => [internal] load build context                                                                     0.6s
 => => transferring context: 16.37MB                                                                  0.6s
 => [2/5] ADD app.jar app.jar                                                                         0.1s
 => [3/5] RUN apk --no-cache update && apk add jq bash bc                                             3.3s
 => [4/5] RUN bash -c 'touch /app.jar'                                                                0.3s
 => [5/5] COPY startup.sh startup.sh                                                                  0.0s 
 => exporting to image                                                                                0.6s 
 => => exporting layers                                                                               0.6s 
 => => writing image sha256:675b9d9e2ace157faff5ebafb3effa65de62a529634bf208f40846279d2aa8f2          0.0s 
 => => naming to us.icr.io/cnb-bootcamp/webapp-alma                                                 0.0s 

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
```

```bash
docker images
```

Y deberíamos de poder ver la imagen que acabamos de construir (de no ser el caso debemos revisar si la construcción se ejecutó correctamente).

```bash
REPOSITORY                                          TAG            IMAGE ID       CREATED         SIZE
us.icr.io/<namespace>/webapp-<iniciales>           latest         a8e46ce7c4ba   2 minutes ago   448MB
```

Una vez que estamos seguros de que mi imagen se construyó correctamente debemos subir la imagen a nuestro CR (Container Registry) por lo tanto corremos el siguiente comando, recordemos modificar los campos de `<namespace>` e `<iniciales>`.

```bash
docker push us.icr.io/<namespace>/webapp-<iniciales>
```

Debemos recibir el siguiente resultado que nos indica que se subió correctamente la imagen a nuestro repositorio.

```bash
Using default tag: latest
The push refers to repository [us.icr.io/<namespace>/webapp-<iniciales>
1d004f8992a3: Pushed
b5dd8d049af1: Pushed
aad17c1cbde0: Pushed
5677749c5065: Pushed
edd61588d126: Pushed
9b9b7f3d56a0: Pushed
f1b5933fe4b5: Pushed
latest: digest: sha256:cee5d20c9ca894d353d19c156d5774a39c4af4235316fb1346bd2dc869694c32 size: 1789
```
​

### 7. Imagen subida a CR - (Terminal 1)

Corroboremos que la imagen se subió correctamente a nuestro Container Registry.
Debemos modificar el `<namespace>` y ponerle nuestras iniciales a nuestro repo `<iniciales>` y no olvidemos incluir el punto del final.

```bash
ic cr image-inspect us.icr.io/<namespace>/webapp-<iniciales>:latest
```

Este comando nos permite pedirle información a nuestro CR sobre la imagen que acabamos de subir deberíamos recibir algo por el estilo.

```bash
Inspecting image 'us.icr.io/<namespace>/webapp-<iniciales>:latest'...

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
​

### 8. Modifiquemos el YAML - (Terminal 2)

Una vez que tenemos nuestra imagen en el CR podemos ahora desplegar esa imagen en OpenShift, para eso necesitamos movernos primero de carpeta y modificar unas líneas de código que tenemos en el YAML.

```bash
cd /kubernetes
```

Abrimos el archivo webapp.yml en una ventana de VSCode o escribimos el comando para abrirlo directamente desde la consola.

```bash
code .
```

Una vez con el archivo abierto podemos comenzar a modificar primero la línea **18**, pondremos la liga a nuestra imagen de catalog (recuerden cambiar el `<namespace>` e `<inciales>`).

```bash
image: "us.icr.io/<namespace>/webapp-<iniciales>:latest"
```
​

### 9. Despleguemos en OpenShift - (Terminal 1)

Cuando tengamos el archivo YAML modificado podremos continuar con el despliegue del servicio, es necesario estar en la terminal 1 (dentro del contenedor).
Primero nos movemos a la carpeta kubenetes, que es la carpeta que contiene nuestro archivo webapp.yml

```bash
cd kubernetes
```

Ahora podemos correr el comando que desplegará nuestro servicio dentro del cluster.

```bash
oc create -f webapp.yml
```

Si o si debemos de recibir este mensaje como salida.

```bash
deployment.apps/webapp-lightblue-deployment created
service/webapp-lightblue-service created
```

El próximo reto es verificar que nuestro pod se creó correctamente, tenemos un comando el cual nos arrojará la información de nuestro proyecto, no mostrará los pods, servicios, replicas set y despliegues. El comando es el siguiente.

```bash
oc get all
```

Deberíamos recibir una respuesta como la siguiente.

```bash
pod/catalog-lightblue-deployment-bdc9947f6-ftx5n     1/1     Running   0          3d22h
pod/customer-lightlue-deployment-7f5b97f566-qq6rc   1/1     Running   0          2d15h
pod/mysql-lightblue-deployment-74c7f8c7f8-bd9cp      1/1     Running   0          3d23h
pod/webapp-lightblue-deployment-695d5c7dd9-rgzg8     1/1     Running   0          55s

NAME                                 TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/catalog-lightblue-service    NodePort   172.21.237.92   <none>        8081:30113/TCP   3d22h
service/customer-lightblue-service   NodePort   172.21.156.72   <none>        8080:30111/TCP   2d15h
service/mysql-lightblue-service      NodePort   172.21.69.11    <none>        3306:30007/TCP   3d23h
service/webapp-lightblue-service     NodePort   172.21.42.48    <none>        8000:30131/TCP   17s

NAME                                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/catalog-lightblue-deployment    1/1     1            1           3d22h
deployment.apps/customer-lightblue-deployment   1/1     1            1           2d15h
deployment.apps/mysql-lightblue-deployment      1/1     1            1           3d23h
deployment.apps/webapp-lightblue-deployment     1/1     1            1           56s

NAME                                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/catalog-lightblue-deployment-bdc9947f6     1         1         1       3d22h
replicaset.apps/customer-lightblue-deployment-7f5b97f566   1         1         1       2d15h
replicaset.apps/mysql-lightblue-deployment-74c7f8c7f8      1         1         1       3d23h
replicaset.apps/webapp-lightblue-deployment-695d5c7dd9     1         1         1       56s
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

Si este es el error encontrado hay que verificar que solamente editamos la línea **18** y **32** en el YAML y asegurarnos que la URL de nuestra imagen dentro del CR es correcta. En el ejemplo anterior faltó poner una `u` en la URL debería de ser `us.icr.io/<namespace>/catalog-<iniciales>`. Después de corregir nuestro YAML necesitamos correr el siguiente comando.

```bash
oc apply -f webapp.yml
```

Con este comando reconfiguramos nuestro despliegue y automáticamente trata de crear otro pod con la nueva configuración, después de correr el comando podemos volver a correr el comando siguiente para asegurarnos que ahora si está corriendo nuestro servicio.

```bash
oc get all
```
​

### 10. Exponer el microservicio con una ruta - (Terminal 1)

Ahora que tenemos nuestro microservicio desplegado en OpenShift necesitamos exponerlo para hacer una prueba y verificar que está funcionando correctamente.
**Con el siguiente comando permitimos el trafico de afuera del clúster a nuestro microservicio** por lo tanto será necesario que después de hacer la prueba borremos la ruta para que de nuevo solo sea accesible dentro del clúster. 
Primero debemos de encontrar el nombre de nuestro servicio y para eso usaremos el siguiente comando.

```bash
oc get services
```

El cual nos arrojará los servicios dentro de nuestro proyecto. Debemos de copiar el nombre de nuestro servicio web `webapp-lightblue-service`.

```bash
NAME                                 TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/catalog-lightblue-service    NodePort   172.21.237.92   <none>        8081:30113/TCP   3d22h
service/customer-lightblue-service   NodePort   172.21.156.72   <none>        8080:30111/TCP   2d15h
service/mysql-lightblue-service      NodePort   172.21.69.11    <none>        3306:30007/TCP   3d23h
service/webapp-lightblue-service     NodePort   172.21.42.48    <none>        8000:30131/TCP   17sAhora correremos el siguiente comando para exponer ese servicio en especifico.
```

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
webapp-lightblue-service   <url-de-nuestro-microservicio>         webapp-lightblue-service   8081                 None
```
​

### 11. Probar nuestro microservicio - (Terminal 2)

Ahora probaremos si nuestro microservicio tiene la conexión a la base de datos de Cloudant, haremos una petición POST que insertará un registro de usuario de ejemplo, para eso necesitamos `<url-de-nuestro-microservicio>` y la vamos a sustituir en el siguiente comando.

​

​

## 12. CLEAN UP (Preparación para el siguiente Lab) - (Terminal 1)

## Video a seguir

[Link a video](https://ibm.box.com/s/x7obos3evh1tx192p37whnm7hrqmdrb9)

​

Debemos eliminar los pods de `customer`, `catalog` y `webapp` de nuestro cluster: (Hay que conservar únicamente lo correspondiente a `mysql`)

```bash
oc delete deployment customer-lightblue-deployment
oc delete service customer-lightblue-service
```

```bash
oc delete deployment catalog-lightblue-deployment
oc delete service catalog-lightblue-service
```

```bash
oc delete deployment webapp-lightblue-deployment
oc delete service webapp-lightblue-service 
```

Lo mismo con las imágenes del container registry (únicamente de `customer`, `catalog` y `webapp`)

```bash
ibmcloud cr image-rm us.icr.io/<namespace>/catalog-<iniciales>:latest
```

```bash
ibmcloud cr image-rm us.icr.io/<namespace>/customer-<iniciales>:latest
```

```bash
ibmcloud cr image-rm us.icr.io/<namespace>/webapp-<iniciales>:latest
```

Ahora necesitamos borrar las rutas que expusimos, tanto (únicamente de `customer` y `catalog`). Muy importante conservar únicamente la de `webapp`.

```bash
oc delete route customer-lightblue-service
oc delete route catalog-lightblue-service
```
