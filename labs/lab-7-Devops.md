# Lab 7 - Despliegue de Light Blue Compute a través de un DevOps Toolchain

## Lo que aprenderas de este lab:

En este laboratorio aprenderas y desplegaras 3 de los microservicios de Light Blue Compute usando un pipeline de DevOps en Openshift.

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

##

## Pasos a seguir​

## He salido del contenedor como puedo volver a entrar?

Si saliste o reiniciaste tu computadora puedes seguir trabajando con el contenedor e iniciarlo de esta manera:

```bash
docker start -i cnbtools
```

​

### 1. Configura la terminal de IBM Cloud CLI

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

### 2. Configurar login de openshift

Antes de ejecutar el comando asegurate obtener el token desde la interfaz de usuario de openshift.

```bash
oc login --token=&lt;token&gt; --server=&lt;server&gt;
```

Nos movemos a nuestro proyecto.

```bash
oc login --token=&lt;token&gt; --server=&lt;server&gt;
```

​

### 3. Revisar los pre-requisitos

El próximo reto es verificar que contamos con los pre-requisitos correctos para que funcione este lab, tenemos un comando el cual nos arrojará la información de nuestro proyecto, no mostrará los pods, servicios, replicas set y despliegues. El comando es el siguiente.

```bash
oc get all
```

Deberíamos recibir una respuesta como la siguiente: (pod, servicio, deployment y replicaset de mysql, así como la ruta de webapp)

```bash
NAME                                              READY   STATUS    RESTARTS   AGE
pod/mysql-lightblue-deployment-74c7f8c7f8-bd9cp   1/1     Running   0          12d

NAME                              TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/mysql-lightblue-service   NodePort   172.21.69.11   &lt;none&gt;        3306:30007/TCP   12d

NAME                                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/mysql-lightblue-deployment   1/1     1            1           12d

NAME                                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/mysql-lightblue-deployment-74c7f8c7f8   1         1         1       12d

NAME                                                HOST/PORT                                                                                                                     PATH   SERVICES                   PORT   TERMINATION   WILDCARD
route.route.openshift.io/webapp-lightblue-service   webapp-lightblue-service-bootcamp-ana.garage-apps-5c5540dc84399e3b5dd482adf70d013d-0000.us-south.containers.appdomain.cloud          webapp-lightblue-service   8000                 NoneTenemos que ver principalmente el estatus de nuestro pod, debe de estar **Running** en caso de no tener el estatus debemos revisar el detalle del pod con el siguiente comando.
Debemos de editar `&lt;nombre-pod&gt;` el cual se encuentra en la parte de arriba y es el que viene en `pod/&lt;este-es-el-nombre-del-pod&gt;`, no deben de incluir el `pod/`
```

## 4. Creación del toolchain

[Link a plantilla](https://github.com/joeg1307/lightblue-devops-kube)

Una vez configurado el toolchain y terminado el despliegue, podemos confirmar la creación de nuestros pods:

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
service/catalog-lightblue-service    NodePort   172.21.237.92   &lt;none&gt;        8081:30113/TCP   3d22h
service/customer-lightblue-service   NodePort   172.21.156.72   &lt;none&gt;        8080:30111/TCP   2d15h
service/mysql-lightblue-service      NodePort   172.21.69.11    &lt;none&gt;        3306:30007/TCP   3d23h
service/webapp-lightblue-service     NodePort   172.21.42.48    &lt;none&gt;        8000:30131/TCP   17s

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
Debemos de editar `&lt;nombre-pod&gt;` el cual se encuentra en la parte de arriba y es el que viene en `pod/&lt;este-es-el-nombre-del-pod&gt;`, no deben de incluir el `pod/`

```bash
oc describe pod &lt;nombre-pod&gt;
```

## 5. Edición del archivo en Eclipse Orion IDE

```bash
public/resources/components/views/home.html
```

## Posibles comandos requeridos:

Para saber el nombre del binding con la base de datos de cloudant:

```bash
oc get secrets
```

Nos arrojará las rutas que tenemos en nuestro proyecto.

```bash
NAME                                                                TYPE                                  DATA   AGE
all-icr-io                                                          kubernetes.io/dockerconfigjson        1      19d
binding-cloudantdb-&lt;inciales&gt;                                       Opaque                                11     7d20h
...​
```

​

Para saber la URL de webapp podemos usar el siguiente comando:

```bash
oc get routes
```

Nos arrojará las rutas que tenemos en nuestro proyecto.

```bash
NAME                        HOST/PORT                       PATH    SERVICES                    PORT   TERMINATION   WILDCARD
webapp-lightblue-service   &lt;url-de-nuestro-microservicio&gt;         webapp-lightblue-service   8081                 None
```

​
