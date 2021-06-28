
# Cheatsheet- Cloud-Native Bootcamp

Aqui encontrarás un resumen de los comandos más utilizados durante el bootcamp.

- [Comandos Generales](#comandos-generales-ibm-cloud-cli)
- [Construyendo imagenes](#construyendo-imagenes)
- [Comandos de Kubernetes](#comandos-kubernetes)

## Comandos Generales - IBM Cloud CLI
Comandos relacionado al CLI de IBM Cloud

#### Autenticarse en el CLI
Si tu empresa tiene habilitado el SSO.
```bash
 ibmcloud login --sso
```
Autenticación Generale
```bash
 ibmcloud login
```

**IMPORTANTE** aseguráte de seleccionar la cuenta que te haya indicado el facilitador.
#### Configuración de API
Si tu empresa tiene habilitado el SSO.
```bash
 ibmcloud target -g ${RESOURCE_GROUP} -r us-south
```
Donde **${RESOURCE_GROUP}** equivale al resource group que estén usando para tu taller.

#### Obtener configuración del cluster
```bash
ibmcloud ks cluster config --cluster ${CLUSTER_NAME}
```
Donde **${CLUSTER_NAME}** equivale al nombre del cluster de K8 que se te asignó al inicio del taller.

#### Obtener IP del workder node
```bash
ibmcloud ks workers --cluster ${CLUSTER_NAME}
```
Donde **${CLUSTER_NAME}** equivale al nombre del cluster de K8 que se te asignó al inicio del taller.

#### Instalar plugin container-registry
```bash
ibmcloud plugin install container-registry
```
#### Instalar plugin container-service
```bash
ibmcloud plugin install container-service
```
#### Ver instancias de Servicios.
```bash
ibmcloud resource service-instances
```



## Construyendo imagenes
Comandos relacionado al CLI de IBM Cloud a través del pluing de container-registry
#### Construir nuestra imagen y subirla al repositorio de imagenes.
```bash
ibmcloud cr build -t us.icr.io/${NAME_SPACE}/${IMAGE_NAME} .
```
- Donde **${NAME_SPACE}** equivale al nombre del namespace o espacio de imagenes de IBM Cloud. Será suministrado por el facilitador.
- Donde **${IMAGE_NAME}** equivale al nombre de la imágen.
#### Lista imagenes en el espacio de imagenes.
```bash
ibmcloud cr images --restrict ${NAME_SPACE}
```
Donde **${NAME_SPACE}** equivale al nombre del namespace o espacio de imagenes de IBM Cloud. Será suministrado por el facilitador.


## Comandos de Kubernetes
Comandos relacionado a `kubectl` con los que se interactua con el cluster de Kubernetes (K8s).
#### Obtener los pods creados en el namespace default
```bash
kubectl get pods
```
#### Describir el recurso de K8
```bash
kubectl describe ${RESOURCE}
```
Donde **${RESOURCE}** equivale recurso específico a describir ejemplos:
- pod/mypod-2i53eklwfng95
- service/myservice-2593tehjkg

#### Describir el recurso de K8
```bash
kubectl logs ${RESOURCE}
```
Donde **${RESOURCE}** equivale recurso específico a describir ejemplos:
- pod/mypod-2i53eklwfng95

#### Crear recursos desde un archivo
```bash
kubectl create -f ${MY_FILE}.yml
```
Donde **${MY_FILE}** es el nombre del archivo .yml

#### Actualizar recursos desde un archivo
```bash
kubectl apply -f ${MY_FILE}.yml
```
Donde **${MY_FILE}** es el nombre del archivo .yml


#### Eliminar recursos desde un archivo
```bash
kubectl delete -f ${MY_FILE}.yml
```
Donde **${MY_FILE}** es el nombre del archivo .yml


#### Ingresar al pod a través de un shell
```bash
kubectl exec -it ${POD_NAME} -- bash
```
Donde **${POD_NAME}** es el nombre del pod a revisar.
