# Lab 2 - Despliegue del Microservicio de Customer y creación de Base de Datos Cloudant

## Lo que aprenderas de este lab:

En este laboratorio aprenderas y desplegaras un Microservicio en Openshift uniendolo a un servicio de base de datos NoSQL llamado Cloudant.

## Lo que necesitarás

- Una ventana de shell con el contenedor con la imagen del [lab 3](https://github.com/joeg1307/bootcamp/blob/main/labs/lab-3-Intro-Imagen-Bootcamp.md) corriendo
  
- Tener tu propio namespace dentro del cluster de OpenShift
  
- Tener el token de login para acceder a OpenShift desde el shell
  
- Tener las credenciales de IBM Cloud para hacer login desde el shell
  
- Tener un servicio de CR (Container Registry) en IBM Cloud
  
- Tener un ApiKey para hacer login desde Docker al CR (Container Registry)
  

​

## Video a seguir

[Link a video](https://ibm.ent.box.com/file/869503443926)

## Pasos a seguir

​

## He salido del contenedor como puedo volver a entrar?

Si saliste o reiniciaste tu computadora puedes seguir trabajando con el contenedor e iniciarlo de esta manera:

```bash
docker start -i cnbtools
```

​

### 1. Configura la terminal de IBM Cloud CLI - (Terminal 1)

Antes de ejecutar el comando asegurate de cambiar los valores `&lt;usuario&gt;`y `&lt;password&gt;`por tu usuario y contraseña de IBM Cloud.

```bash
ibmcloud login -u &lt;usuario&gt; -p &lt;password&gt; -r us-south
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
oc login --token=&lt;token&gt; --server=&lt;server&gt;
```

Nos movemos a nuestro proyecto.

```bash
oc login --token=&lt;token&gt; --server=&lt;server&gt;
```

​

​

​

### 3. Configurar login del CR en Docker - (Terminal 2)

Antes de ejecutar el comando asegurate de abrir una nueva terminal de shell en tu computadora.

```bash
docker login -u iamapikey -p &lt;apikey&gt; us.icr.io/&lt;namespace con nuestras iniciales&gt;
```

La respuesta que esperamos despues de correr el comando anterior es

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.

Login Succeeded
```

​

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

​

### 5. Crear base de datos de Cloudant - (Terminal 1)

Ahora deberemos crear una base de datos para poder conectarla a nuestro microservicio. Antes, revisaremos las instancias que tenemos creadas en nuestra cuenta, esto simplemente lo haremos para saber que existe un comando para mostrar los recursos que tenemos en la nube.

```bash
ibmcloud resource service-instances
```

Despues de ejecutar este comando podríamos recibir varias respuestas entre ellas podríamos recibir la lista de los recursos por ejemplo:

```bash
Retrieving instances with type service_instance in all resource groups in all locations under account &lt;cuenta-ibm-cloud&gt;&#39;s Account as &lt;nuesto-usuario&gt;...

OK

Name                                                 Location   State    Type

Cloudant-db                                          us-south   active   service_instance
```

El paso anterior no es necesario para crear la instancia de base de datos, simplemente es ilustrativo para mostrar las instancias de servicios en nuestra cuenta de cloud.

El siguiente comando nos servirá para crear nuestra base de datos en la nube, necesitamos modificar nuestras `&lt;iniciales&gt;` en el comando.

```bash
ibmcloud resource service-instance-create cloudantdb-&lt;iniciales&gt; cloudantnosqldb Standard us-south -p &#39;{&quot;legacyCredentials&quot;: true}&#39;
```

El resultado que esperamos obtener de ese comando es el siguiente.

```bash
Creating service instance cloudantdb-&lt;iniciales&gt; in resource group Default of account IBM as &lt;usuario&gt;...

OK

Service instance cloudantdb was created.

​

Name:             cloudantdb-&lt;iniciales&gt;

ID:               crn:v1:bluemix:public:cloudantnosqldb:us-south:a/&lt;id&gt;:&lt;guid&gt;::

GUID:             &lt;guid&gt;

Location:         us-south

State:            provisioning

Type:             service_instance

Sub Type:

Allow Cleanup:    false

Locked:           false

Created at:       2021-10-14T15:58:13Z

Updated at:       2021-10-14T15:58:15Z

Last Operation:

                  Status    create in progress

                  Message   Started create instance operation
```

Despues de obtener ese resultado basicamente tendríamos creada nuestra instancia de Cloudant en la nube.

​

​

### 6. Crear credenciales y vincularlas con el cluster de OpenShift - (Terminal 1)

Despues de haber creado la base de datos necesitamos asignarle credenciales de tal manera que podamos vincularlas con el cluster y asi poder tener la conexión con nuestro microservicio.

El comando para crear las crendenciales es el siguiente, recuerda cambiar el nombre de la instacia modificando tus `&lt;iniciales&gt;`:

```bash
ibmcloud resource service-key-create cloudant-creds --instance-name cloudantdb-&lt;iniciales&gt; 
```

Si recibimos algun error por ejemplo el siguiente, puede ser porque aun se está creando la base de datos, debemos esperar 30 segundos y volver a internar el comando.

```Bash
Creating service key of service instance cloudantdb-mavg under account &lt;cuenta&gt;&#39;s Account as &lt;usuario&gt;...

FAILED

Error response from server. Status code: 400; description: 400 The resource instance is removed/invalid.
```

El resultado que esperamos es el resumen de las credenciales creadas por IBM Cloud para nuestra instancia de Cloudant.

```Bash
Creating service key of service instance cloudantdb-&lt;iniciales&gt; under account IBM as &lt;cuenta&gt;...

OK

Service key crn:v1:bluemix:public:cloudantnosqldb:us-south:a/&lt;id&gt;:&lt;id&gt;:resource-key:&lt;resource-key&gt; was created.

​

Name:          cloudant-creds

ID:            crn:v1:bluemix:public:cloudantnosqldb:us-south:a/&lt;id&gt;:&lt;id&gt;:resource-key:&lt;resource-key&gt;

Created At:    Thu Oct 14 16:03:20 UTC 2021

State:         active

Credentials:

               apikey:                   &lt;api_key&gt;

               host:                     &lt;host&gt;.cloudantnosqldb.appdomain.cloud

               iam_apikey_description:   Auto-generated for key &lt;id&gt;

               iam_apikey_name:          cloudant-creds

               iam_role_crn:             crn:v1:bluemix:public:iam::::serviceRole:Writer

               iam_serviceid_crn:        crn:v1:bluemix:public:iam-identity::a/&lt;id&gt;::serviceid:ServiceId-&lt;service-id&gt;

               password:                 &lt;password&gt;

               port:                     443

               url:                      https://&lt;user-name&gt;:&lt;password&gt;@&lt;host&gt;.cloudantnosqldb.appdomain.cloud

               username:                 &lt;user-name&gt;

​
```

Ahora que tenemos las credenciales creadas podemos vincular la base de datos con el cluster de Openshift, el siguiente comando nos ayuda a conectar las credenciales con el cluster para poder acceder a la base de datos, recordemos cambiar `&lt;nombre-cluster&gt;`, `&lt;proyecto-openshift&gt;` e `&lt;iniciales&gt;` en el comando.

```bash
ibmcloud oc cluster service bind --cluster &lt;nombre-cluster&gt; --namespace &lt;proyecto-openshift&gt; --service cloudantdb-&lt;iniciales&gt;
```

El resultado que esperamos obtener es el siguiente, que nos indicará que las credenciales fueron vinculadas con el cluster.

```bash
Binding service instance to namespace...

OK

​

Namespace:     &lt;proyecto-openshift&gt;

Secret Name:   binding-cloudantdb-&lt;iniciales&gt;
```

Si volvieramos a necesitar las credenciales de nuestro servicio de Cloudant, podemos ejecutar el siguiente comando.

```bash
​ibmcloud resource service-key cloudant-creds
```

El resultado sería las credenciales de nuestro servicio

```Bash
Retrieving service key cloudant-creds in resource group &lt;grupo-de-recursos&gt; under account &lt;cuenta&gt;&#39;s Account as &lt;usuario&gt;...

​

Name:          cloudant-creds

ID:            crn:v1:bluemix:public:cloudantnosqldb:us-south:a/&lt;id&gt;:&lt;id&gt;:resource-key:&lt;resource-key&gt;

Created At:    Thu Oct 14 16:03:20 UTC 2021

State:         active

Credentials:

 apikey:                   &lt;api_key&gt;

 host:                     &lt;host&gt;.cloudantnosqldb.appdomain.cloud

 iam_apikey_description:   Auto-generated for key &lt;id&gt;

 iam_apikey_name:          cloudant-creds

 iam_role_crn:             crn:v1:bluemix:public:iam::::serviceRole:Writer

 iam_serviceid_crn:        crn:v1:bluemix:public:iam-identity::a/&lt;id&gt;::serviceid:ServiceId-&lt;service-id&gt;

 password:                 &lt;password&gt;

 port:                     443

 url:                      https://&lt;user-name&gt;:&lt;password&gt;@&lt;host&gt;.cloudantnosqldb.appdomain.cloud

 username:                 &lt;user-name&gt;

​
```

Para verificar que hemos hecho el binding correctamente a nuestro cluster correremos el siguiente comando el cual nos regresará los secrets que ahora nuestro cluster contiene en nuestro proyecto de OpenShift

```bash
oc get secrets
```

### 7. Movernos de carpeta - (Terminal 1 y Terminal 2)

A continuación vamos a desplegar el siguiente micro-servicio, por lo que nos moveremos a su respectivo directorio: `customer` con el siguiente comando (Ambas terminales deben estar en el mismo directorio).

```bash
cd ../../customer
```

### 8. Construir el Microservicio - (Terminal 1)

El siguiente comando nos servirá para construir el microservicio, va a entre 1 a 2 minutos en ejecutarse al final deberíamos de recibir un mensaje que contenga `BUILD SUCCESSFUL`.

```bash
./gradlew build -x test
```

Respuesta final esperada.

```bash
:compileJava
Download https://repo1.maven.org/maven2/org/springframework/cloud/spring-cloud-starter-parent/Angel.SR4/spring-cloud-starter-parent-Angel.SR4.pom
Download https://repo1.maven.org/maven2/com/cloudant/cloudant-client/2.7.0/cloudant-client-2.7.0.pom
Download https://repo1.maven.org/maven2/com/nimbusds/nimbus-jose-jwt/4.23/nimbus-jose-jwt-4.23.pom
Download https://repo1.maven.org/maven2/com/google/code/gson/gson/2.3.1/gson-2.3.1.pom
Download https://repo1.maven.org/maven2/commons-codec/commons-codec/1.6/commons-codec-1.6.pom
Download https://repo1.maven.org/maven2/org/apache/commons/commons-parent/22/commons-parent-22.pom
Download https://repo1.maven.org/maven2/org/apache/apache/9/apache-9.pom
Download https://repo1.maven.org/maven2/com/cloudant/cloudant-http/2.7.0/cloudant-http-2.7.0.pom
Download https://repo1.maven.org/maven2/com/github/stephenc/jcip/jcip-annotations/1.0-1/jcip-annotations-1.0-1.pom
Download https://repo1.maven.org/maven2/net/minidev/json-smart/1.3.1/json-smart-1.3.1.pom
Download https://repo1.maven.org/maven2/net/minidev/parent/1.3.1/parent-1.3.1.pom
Download https://repo1.maven.org/maven2/commons-io/commons-io/2.11.0/commons-io-2.11.0.pom
Download https://repo1.maven.org/maven2/org/apache/commons/commons-parent/52/commons-parent-52.pom
Download https://repo1.maven.org/maven2/org/apache/apache/23/apache-23.pom
Download https://repo1.maven.org/maven2/org/junit/junit-bom/5.7.2/junit-bom-5.7.2.pom
Download https://repo1.maven.org/maven2/org/yaml/snakeyaml/1.14/snakeyaml-1.14.pom
Download https://repo1.maven.org/maven2/com/cloudant/cloudant-client/2.7.0/cloudant-client-2.7.0.jar
Download https://repo1.maven.org/maven2/com/nimbusds/nimbus-jose-jwt/4.23/nimbus-jose-jwt-4.23.jar
Download https://repo1.maven.org/maven2/com/google/code/gson/gson/2.3.1/gson-2.3.1.jar
Download https://repo1.maven.org/maven2/commons-codec/commons-codec/1.6/commons-codec-1.6.jar
Download https://repo1.maven.org/maven2/com/cloudant/cloudant-http/2.7.0/cloudant-http-2.7.0.jar
Download https://repo1.maven.org/maven2/com/github/stephenc/jcip/jcip-annotations/1.0-1/jcip-annotations-1.0-1.jar
Download https://repo1.maven.org/maven2/net/minidev/json-smart/1.3.1/json-smart-1.3.1.jar
Download https://repo1.maven.org/maven2/commons-io/commons-io/2.11.0/commons-io-2.11.0.jar
Download https://repo1.maven.org/maven2/org/yaml/snakeyaml/1.14/snakeyaml-1.14.jar
:processResources
:classes
:findMainClass
:jar
:bootRepackage
:assemble
:check
:build

BUILD SUCCESSFUL

Total time: 27.077 secs
```

Ahora construiremos el archivo de docker y de igual manera obtendremos el mismo mensaje como resultado.

```bash
./gradlew docker
```

Resultado esperado.

```bash
:docker

BUILD SUCCESSFUL

Total time: 10.017 secs
```

### 9. Construir y subir la imagen de Docker - (Terminal 2)

Para construir la imagen de docker debemos movernos de carpeta con el siguiente comando.

```bash
cd docker 
```

Ahora construiremos la imagen de nuestro contenedor y la subiremos a nuestro repositorio de CR (Container Registry).
Recuerden debemos modificar el `&lt;namespace&gt;` y ponerle nuestras iniciales a nuestro repo `&lt;iniciales&gt;` y no olvidemos incluir el punto del final.

```bash
docker build -t us.icr.io/&lt;namespace&gt;/customer-&lt;iniciales&gt; .
```

El comando va a tardar aproximadamente 1 minuto en ejecutarse, prácticamente estamos construyendo nuestra imagen de docker y poniéndole un tag que haga referencia a nuestro repositorio de imágenes en Container Registry.
El resultado que debemos obtener es el siguiente (pueden variar varias líneas pero nos debe de decir `(11/11) FINISHED`).

```bash
[+] Building 8.4s (11/11) FINISHED                                                                         
 =&gt; [internal] load build definition from Dockerfile                                                  0.0s
 =&gt; =&gt; transferring dockerfile: 247B                                                                  0.0s
 =&gt; [internal] load .dockerignore                                                                     0.0s
 =&gt; =&gt; transferring context: 2B                                                                       0.0s
 =&gt; [internal] load metadata for docker.io/library/openjdk:8-jre-alpine                               3.2s
 =&gt; [auth] library/openjdk:pull token for registry-1.docker.io                                        0.0s
 =&gt; CACHED [1/5] FROM docker.io/library/openjdk:8-jre-alpine@sha256:f362b165b870ef129cbe730f29065ff3  0.0s
 =&gt; [internal] load build context                                                                     0.6s
 =&gt; =&gt; transferring context: 16.37MB                                                                  0.6s
 =&gt; [2/5] ADD app.jar app.jar                                                                         0.1s
 =&gt; [3/5] RUN apk --no-cache update && apk add jq bash bc                                             3.3s
 =&gt; [4/5] RUN bash -c &#39;touch /app.jar&#39;                                                                0.3s
 =&gt; [5/5] COPY startup.sh startup.sh                                                                  0.0s 
 =&gt; exporting to image                                                                                0.6s 
 =&gt; =&gt; exporting layers                                                                               0.6s 
 =&gt; =&gt; writing image sha256:675b9d9e2ace157faff5ebafb3effa65de62a529634bf208f40846279d2aa8f2          0.0s 
 =&gt; =&gt; naming to us.icr.io/cnb-bootcamp/customer-alma                                                 0.0s 

Use &#39;docker scan&#39; to run Snyk tests against images to find vulnerabilities and learn how to fix them
```

```bash
docker images
```

Y deberíamos de poder ver la imagen que acabamos de construir (de no ser el caso debemos revisar si la construcción se ejecutó correctamente).

```bash
REPOSITORY                                          TAG            IMAGE ID       CREATED         SIZE
us.icr.io/&lt;namespace&gt;/customer-&lt;iniciales&gt;           latest         a8e46ce7c4ba   2 minutes ago   448MB
```

Una vez que estamos seguros de que mi imagen se construyó correctamente debemos subir la imagen a nuestro CR (Container Registry) por lo tanto corremos el siguiente comando, recordemos modificar los campos de `&lt;namespace&gt;` e `&lt;iniciales&gt;`.

```bash
docker push us.icr.io/&lt;namespace&gt;/customer-&lt;iniciales&gt;
```

Debemos recibir el siguiente resultado que nos indica que se subió correctamente la imagen a nuestro repositorio.

```bash
Using default tag: latest
The push refers to repository [us.icr.io/&lt;namespace&gt;/customer-&lt;iniciales&gt;
1d004f8992a3: Pushed
b5dd8d049af1: Pushed
aad17c1cbde0: Pushed
5677749c5065: Pushed
edd61588d126: Pushed
9b9b7f3d56a0: Pushed
f1b5933fe4b5: Pushed
latest: digest: sha256:cee5d20c9ca894d353d19c156d5774a39c4af4235316fb1346bd2dc869694c32 size: 1789
```

### 10. Imagen subida a CR - (Terminal 1)

Corroboremos que la imagen se subió correctamente a nuestro Container Registry.
Debemos modificar el `&lt;namespace&gt;` y ponerle nuestras iniciales a nuestro repo `&lt;iniciales&gt;` y no olvidemos incluir el punto del final.

```bash
ic cr image-inspect us.icr.io/&lt;namespace&gt;/customer-&lt;iniciales&gt;:latest
```

Este comando nos permite pedirle información a nuestro CR sobre la imagen que acabamos de subir deberíamos recibir algo por el estilo.

```bash
Inspecting image &#39;us.icr.io/&lt;namespace&gt;/customer-&lt;iniciales&gt;:latest&#39;...

{
    &quot;Id&quot;: &quot;sha256:b09d36ec122b4fffd1fce55f7558318071f62e594bb619e318f2e06fc3a9ad0c&quot;,    
    &quot;Parent&quot;: &quot;&quot;,
    &quot;Comment&quot;: &quot;&quot;,
    &quot;Created&quot;: &quot;2021-10-14T17:20:25.7806097Z&quot;,
    &quot;Container&quot;: &quot;&quot;,
    &quot;ContainerConfig&quot;: {},
    &quot;DockerVersion&quot;: &quot;&quot;,
    &quot;Author&quot;: &quot;&quot;,
    &quot;Config&quot;: {},
    &quot;Architecture&quot;: &quot;amd64&quot;,
    &quot;Os&quot;: &quot;linux&quot;,
    &quot;Size&quot;: 154790406,
    &quot;VirtualSize&quot;: 154790406,
    &quot;RootFS&quot;: {
        &quot;Type&quot;: &quot;layers&quot;,
        &quot;Layers&quot;: [
            &quot;sha256:f1b5933fe4b5f49bbe8258745cf396afe07e625bdab3168e364daf7c956b6b81&quot;,
            ...
            &quot;sha256:1d004f8992a331664e12946646decd2ab90178f10ceeb86ab9c1e3a28ecf72f1&quot;
        ]
    },
    &quot;ManifestType&quot;: &quot;application/vnd.docker.distribution.manifest.v2+json&quot;
}

OK
```

Con este comando confirmamos que nuestra imagen ya se encuentra en la nube en nuestra instancia de CR.

### 11. Modifiquemos el YAML - (Terminal 2)

Una vez que tenemos nuestra imagen en el CR podemos ahora desplegar esa imagen en OpenShift, para eso necesitamos movernos primero de carpeta y modificar unas líneas de código que tenemos en el YAML.

```bash
cd ../kubernetes
```

Abrimos el archivo catalog.yml en una ventana de VSCode o escribimos el comando para abrirlo directamente desde la consola.

```bash
code .
```

Una vez con el archivo abierto podemos comenzar a modificar primero la línea **18**, pondremos la liga a nuestra imagen de catalog (recuerden cambiar el `&lt;namespace&gt;` e `&lt;inciales&gt;`).

```bash
image: &quot;us.icr.io/&lt;namespace&gt;/customer-&lt;iniciales&gt;:latest&quot;
```

Y en la línea **32** vamos a colocar el nombre del binding con la base de datos de cloudant.

```bash
secretName: &quot;binding-cloudant-&lt;iniciales&gt;&quot;
```

Para corroborar que nombre le dimos al bining podemos usar el siguiente comando:

```bash
oc get secrets
```

Guardamos el archivo y continuamos con el despliegue de nuestro servicio en OpenShift

### 12. Despleguemos en OpenShift - (Terminal 1)

Cuando tengamos el archivo YAML modificado podremos continuar con el despliegue del servicio, es necesario estar en la terminal 1 (dentro del contenedor).
Primero nos movemos a la carpeta kubenetes, que es la carpeta que contiene nuestro archivo catalog.yml

```bash
cd kubernetes
```

Ahora podemos correr el comando que desplegará nuestro servicio dentro del cluster.

```bash
oc create -f customer.yml
```

Si o si debemos de recibir este mensaje como salida.

```bash
deployment.apps/customer-lightblue-deployment created
service/customer-lightblue-service created
```

El próximo reto es verificar que nuestro pod se creó correctamente, tenemos un comando el cual nos arrojará la información de nuestro proyecto, no mostrará los pods, servicios, replicas set y despliegues. El comando es el siguiente.

```bash
oc get all
```

Deberíamos recibir una respuesta como la siguiente.

```bash
NAME                                                 READY   STATUS    RESTARTS   AGE
pod/catalog-lightblue-deployment-bdc9947f6-ftx5n     1/1     Running   0          28h
pod/customer-lightblue-deployment-84ddc845c6-7qgrn   1/1     Running   0          8d
pod/mysql-lightblue-deployment-74c7f8c7f8-bd9cp      1/1     Running   0          28h

NAME                                 TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/catalog-lightblue-service    NodePort   172.21.237.92   &lt;none&gt;        8081:30113/TCP   28h
service/customer-lightblue-service   NodePort   172.21.105.2    &lt;none&gt;        8080:30111/TCP   8d
service/mysql-lightblue-service      NodePort   172.21.69.11    &lt;none&gt;        3306:30007/TCP   28h

NAME                                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/catalog-lightblue-deployment    1/1     1            1           28h
deployment.apps/customer-lightblue-deployment   1/1     1            1           8d
deployment.apps/mysql-lightblue-deployment      1/1     1            1           28h

NAME                                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/catalog-lightblue-deployment-bdc9947f6     1         1         1       28h
replicaset.apps/customer-lightblue-deployment-84ddc845c6   1         1         1       8d
replicaset.apps/mysql-lightblue-deployment-74c7f8c7f8      1         1         1       28h
```

Tenemos que ver principalmente el estatus de nuestro pod, debe de estar **Running** en caso de no tener el estatus debemos revisar el detalle del pod con el siguiente comando.
Debemos de editar `&lt;nombre-pod&gt;` el cual se encuentra en la parte de arriba y es el que viene en `pod/&lt;este-es-el-nombre-del-pod&gt;`, no deben de incluir el `pod/`

```bash
oc describe pod &lt;nombre-pod&gt;
```

El error mas frecuente es `ErrImagePull` el cual nos aparecería de esta manera.

```bash
Events:
  Type     Reason          Age                From               Message
  ----     ------          ----               ----               -------
  Warning  Failed          27s (x2 over 43s)  kubelet            Error: ErrImagePull
  Normal   BackOff         12s (x2 over 42s)  kubelet            Back-off pulling image &quot;s.icr.io/&lt;namespace&gt;/catalog-&lt;iniciales&gt;&quot;
  Warning  Failed          12s (x2 over 42s)  kubelet            Error: ImagePullBackOff
  Normal   Pulling         0s (x3 over 43s)   kubelet            Pulling image &quot;s.icr.io/&lt;namespace&gt;/catalog-&lt;iniciales&gt;&quot;
```

Si este es el error encontrado hay que verificar que solamente editamos la línea **18** y **32** en el YAML y asegurarnos que la URL de nuestra imagen dentro del CR es correcta. En el ejemplo anterior faltó poner una `u` en la URL debería de ser `us.icr.io/&lt;namespace&gt;/catalog-&lt;iniciales&gt;`. Después de corregir nuestro YAML necesitamos correr el siguiente comando.

```bash
oc apply -f customer.yml
```

Con este comando reconfiguramos nuestro despliegue y automáticamente trata de crear otro pod con la nueva configuración, después de correr el comando podemos volver a correr el comando siguiente para asegurarnos que ahora si está corriendo nuestro servicio.

```bash
oc get all
```

### 13. Exponer el microservicio con una ruta - (Terminal 1)

Ahora que tenemos nuestro microservicio desplegado en OpenShift necesitamos exponerlo para hacer una prueba y verificar que está funcionando correctamente.
**Con el siguiente comando permitimos el trafico de afuera del clúster a nuestro microservicio** por lo tanto será necesario que después de hacer la prueba borremos la ruta para que de nuevo solo sea accesible dentro del clúster. 
Primero debemos de encontrar el nombre de nuestro servicio y para eso usaremos el siguiente comando.

```bash
oc get services
```

El cual nos arrojará los servicios dentro de nuestro proyecto. Debemos de copiar el nombre de nuestro servicio de customer `customer-lightblue-service`.

```bash
NAME                         TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
catalog-lightblue-service    NodePort   172.21.237.92   &lt;none&gt;        8081:30113/TCP   28h
customer-lightblue-service   NodePort   172.21.105.2    &lt;none&gt;        8080:30111/TCP   8d
mysql-lightblue-service      NodePort   172.21.69.11    &lt;none&gt;        3306:30007/TCP   28h
```

Ahora correremos el siguiente comando para exponer ese servicio en especifico.

```bash
oc expose service &lt;nombre-del-servicio&gt;
```

Obtendremos una respuesta como la siguiente.

```bash
route.route.openshift.io/&lt;nombre-del-servicio&gt; exposed
```

Eso nos indica que OpenShift le asignó una ruta a nuestro microservicio y ya es accesible desde afuera del clúster, para saber la URL debemos de poner el siguiente comando.

```bash
oc get routes
```

Nos arrojará las rutas que tenemos en nuestro proyecto.

```bash
NAME                        HOST/PORT                       PATH    SERVICES                    PORT   TERMINATION   WILDCARD
customer-lightblue-service   &lt;url-de-nuestro-microservicio&gt;         customer-lightblue-service   8081                 None
```

### 14. Probar nuestro microservicio - (Terminal 2)

Ahora probaremos si nuestro microservicio tiene la conexión a la base de datos de Cloudant, haremos una petición POST que insertará un registro de usuario de ejemplo, para eso necesitamos `&lt;url-de-nuestro-microservicio&gt;` y la vamos a sustituir en el siguiente comando.

```bash
curl  -i -X POST -H &quot;Content-Type: application/json&quot; -H &quot;Accept: application/json&quot; -d &#39;{&quot;username&quot;: &quot;foo&quot;, &quot;password&quot;: &quot;bar&quot;, &quot;firstName&quot;: &quot;foo&quot;, &quot;lastName&quot;: &quot;bar&quot;, &quot;email&quot;: &quot;foo@bar.com&quot;}&#39; &quot;http://&lt;url-de-nuestro-microservicio&gt;:&lt;customer-node-port&gt;/micro/customer&quot;
```

Con esto estamos haciendo una petición a nuestro microservicio el cual va a insertar un registro en la base de datos y nos confirma con el siguiente mensaje:

```bash
HTTP/1.1 201 Created
Date: Sat, 23 Oct 2021 01:59:57 GMT
X-Application-Context: customer-microservice:8080
Location: http://customer-lightblue-service-bootcamp-ana.garage-apps-5c5540dc84399e3b5dd482adf70d013d-0000.us-south.containers.appdomain.cloud:30111/micro/customer/1e6db9cf81f74626a0b315fa83a18b5c
Content-Length: 0
Server: Jetty(9.2.13.v20150730)
```

Ahora vamos a realizar una petición a el endpoint para obtener la información insertada de la siguiente manera:

```bash
curl &quot;http://&lt;url-de-nuestro-microservicio&gt;:&lt;customer-node-port&gt;/micro/customer/search?username=foo&quot;
```

Deberiamos ver un resultado como este:

```bash
[{&quot;username&quot;:&quot;foo&quot;,&quot;password&quot;:&quot;bar&quot;,&quot;firstName&quot;:&quot;foo&quot;,&quot;lastName&quot;:&quot;bar&quot;,&quot;email&quot;:&quot;foo@bar.com&quot;,&quot;imageUrl&quot;:null,&quot;customerId&quot;:&quot;1e6db9cf81f74626a0b315fa83a18b5c&quot;}]
```

Esto nos indica que nuestro microservicio está funcionando bien y tenemos la conexión a la base de datos.

clea

### 15. Borrar la ruta - (Terminal 1)

Ahora necesitamos borrar la ruta para que el flujo que venga desde afuera del clúster no tenga acceso directo a nuestro microservicio y con eso a nuestra base de datos, por lo tanto, necesitamos correr el siguiente comando. Recuerden sustituir `&lt;nombre-de-la-ruta&gt;` por el nombre real de su ruta.

```bash
oc delete route &lt;nombre-de-la-ruta&gt;
```

Por ejemplo

```bash
oc delete route customer-lightblue-service
```

De esta manera estaríamos borrando la ruta por lo tanto si intentamos volver a pedir información con el `curl` de arriba nos arrojaría que no existe la URL.

Con esto terminamos el primer laboratorio.
