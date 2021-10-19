# Hola Mundo en OpenShift
## Lo que aprenderas de este lab:
En este laboratorio aprenderas y desplegaras tu primer aplicación en Openshift, creando el despliegue del aplicativo, el servicio que lo conectará y la ruta que nos dará acceso.
## Lo que necesitarás
- Una ventana de shell con el contenedor con la imagen del [lab 3](https://github.com/joeg1307/bootcamp/blob/main/labs/lab-3-Intro-Imagen-Bootcamp.md) corriendo
- Haber hecho el login en el shell para acceder a OpenShift
## Video a seguir
[Link a video](https://ibm.box.com/s/gy1ylcxkgygboxhrncvcc01k0cdjn1ip)
## Pasos a seguir
1. Descarguemos los archivos necesarios para desplegar nuestra aplicación.
```bash
git clone https://github.com/alexvigro92/HOLA-MUNDO-OC.git
```
Ejemplo de salida:
```
Clonando en 'HOLA-MUNDO-OC'...
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 5 (delta 0), reused 0 (delta 0), pack-reused 0
Recibiendo objetos: 100% (5/5), listo.
```
2. Podemos comprobar que todo se haya descargado corriendo el siguiente comando.
```bash
ls HOLA-MUNDO-OC
```
Salida esperada:
```
deployment.yml    route.yml    service.yml
```
Debemos ver que tenemos 3 archivos deployment.yml, route.yml y service.yml
3. Movamos la terminal a la carpeta descargada.
```bash
cd HOLA-MUNDO-OC/
```
4. Es necesario que modifiquemos unas cosas de los 3 archivos por lo tanto abriremos nuestro IDE VSCode.
```bash
code .
```
5. Primer modificaremos el archivo deployment.yml en donde dice <namespace> pondremos el nombre de nuestro proyecto/namespace.
```bash
namespace: <namespace>
```
el cual quedaría por ejemplo.
```bash
namespace: bootcamp
```
```bash
kind: Deployment
apiVersion: apps/v1
metadata:
  name: hola-mundo
  namespace: <namespace>
  labels:
    app: hola-mundo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hola-mundo
  template:
    metadata:
      labels:
        app: hola-mundo
    spec:
      containers:
        - name: hola-mundo
          image: docker.io/alexvigro92/hola_mundo_oc
          ports:
            - containerPort: 8080
              protocol: TCP
          resources: {}
          imagePullPolicy: Always
      restartPolicy: Always
      terminationGracePeriodSeconds: 30
      dnsPolicy: ClusterFirst
      securityContext: {}
      schedulerName: default-scheduler
```
6. Guardamos nuestro archivo y continuamos con el siguiente.
7. Ahora editaremos el archivo route.yaml y de igual manera modificaremos el campo donde diga <namespace>.
```bash
namespace: <namespace>
```
el cual quedaría por ejemplo.
```bash
namespace: bootcamp
```
```bash
kind: Route
apiVersion: route.openshift.io/v1
metadata:
  name: hola-mundo
  namespace: <namespace>
  labels:
    app: hola-mundo
spec:
  to:
    kind: Service
    name: hola-mundo
    weight: 100
  port:
    targetPort: 8080-tcp
  wildcardPolicy: None
```
8. De nuevo guardamos el archivo y continuamos con el siguiente.
9. Para finalizar editaremos el archivo service.yaml y de igual manera modificaremos el campo donde diga <namespace>.
```bash
namespace: <namespace>
```
el cual quedaría por ejemplo.
```bash
namespace: bootcamp
```
```bash
kind: Service
apiVersion: v1
metadata:
  name: hola-mundo
  namespace: bootcamp
  labels:
    app: hola-mundo
spec:
  ports:
    - name: 8080-tcp
      protocol: TCP
      port: 8080
      targetPort: 8080
  selector:
    app: hola-mundo
  type: ClusterIP
  sessionAffinity: None
```
10. Por ultimo guardamos el archivo.
11. Ya que modificamos los 3 archivos podemos continuar en la ventana de nuestro shell, a continuación subiremos nuestros archivos de configuración a nuestro cluster para que OpenShift se encargue de hacer el despligue de nuestra aplicación y de hacer las configuraciones necesarias.
12. El siguiente comando que correremos nos ayudará a hacer el despliegue de nuestro contenedor dentro de un pod.
```bash
oc create -f deployment.yml
```
Deberíamos recibir el siguiente mensaje comprobando que todo se configuró y se creó nuestro despligue.
```bash
deployment.apps/hola-mundo created
```
Podemos ejecutar el comando siguiente para revisar que nuestro pod y aplicación está levantada y corriendo.
```bash
oc get deployment
```
El resultado debería de ser el siguiente si todo se ejecutó correctamente.
```bash
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
hola-mundo   1/1     1            1           2m
```
Podemos tambien revisar el estado de nuestro pod para ver si se levantó correctamente con el siguiente comando.
```bash
oc get pods
```
Deberíamos de ver que mi pod está en estado **Running**.
```bash
NAME                          READY   STATUS    RESTARTS   AGE
hola-mundo-86d8c64c6b-ps2br   1/1     Running   0          3m
```
Con estó nuestra aplicación se ha desplegado correctamente, lo siguiente que nos toca hacer es un [servicio de OpenShift](https://docs.openshift.com/enterprise/3.0/architecture/core_concepts/pods_and_services.html#services) el cual servirá como un valanceador de cargas interno.
13. El siguiente comando nos permitirá desplegar el servicio y conectarlo a nuestro despliegue y a su vez a nuestro pod.
```bash
oc create -f service.yml
```
Deberíamos obtener como respuesta lo siguiente.
```bash
service/hola-mundo created
```
Para comprobar si se generó correctamente podemos ejecutar el siguiente comando.
```bash
oc get services
```
Tenemos que ver el siguiente resultado.
```bash
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
hola-mundo   ClusterIP   172.21.192.227   <none>        8080/TCP   1m
```
Para finalizar debemos de crear la apertura que nos dará acceso a nuestra aplicación desde el exterior de nuestro cluster, a esto lo llamaremos route.
14. Para crear un route debemos de ejecutar el siguiente comando.
```bash
oc create -f route.yml
```
Deberíamos obtener una confirmación de esta manera.
```bash
route.route.openshift.io/hola-mundo created
```
De nuevo podemos corroborar que se creó correctamente con el siguiente comando.
```bash
oc get routes
```
Tenemos que ver el siguiente resultado.
```bash
NAME         HOST/PORT                                                        PATH   SERVICES     PORT       TERMINATION   WILDCARD
hola-mundo   hola-mundo-bootcamp.garage.us-south.containers.appdomain.cloud          hola-mundo   8080-tcp                 None
```
Al crear nuestro route automaticamente nos está generando el host de nuestra aplicación por lo tanto ahora podemos copiar y pegar el host en nuestro navegador de preferencia y podrémos ver el resultado de nuestro laboratorio.
En mi caso la ruta es la siguiente 
```bash
hola-mundo-bootcamp.garage.us-south.containers.appdomain.cloud
```
Podemos probar con algunos paths extras que nos mostraran otras cosas de nuestra aplicación
```bash
<url>/test?name=<tu-nombre>
<url>/home
<url>/hello
<url>/hello/fr
<url>/hello/es
```
Con esto completamos el despligue de nuestra primera aplicación, ahora procederemos a borrar todo lo que hemos hecho.
15. Ahora procederemos a borrar el despliegue, el servicio y la ruta. Comenzaremos borrando la ruta con el siguiente comando.
```bash
oc delete -f route.yml
```
El resultado que esperamos es el siguiente.
```bash
route.route.openshift.io "hola-mundo" deleted
```
16. Ahora procederemos a borrar el servicio.
```bash
oc delete -f service.yml
```
Si todo salió correcto deberías de ver el siguiente mensaje.
```bash
service "hola-mundo" deleted
```
17. Para finalizar borraremos el despliegue.
```bash
oc delete -f deployment.yml
```
Con esto estaríamos borrando nuestra aplicación junto con el pod.
```bash
deployment.apps "hola-mundo" deleted
```
18. Para corroborar que todo salio como esperabamos correremos el siguiente comando.
```bash
oc get all
```
El resultado que esperamos ver es el siguiente.
```bash
No resources found in bootcamp namespace.
```
