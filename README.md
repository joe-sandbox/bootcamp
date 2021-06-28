
# Cheatsheet- Cloud-Native Bootcamp

Aqui encontrarás un resumen de los comandos más utilizados durante el bootcamp.

[Comandos Generales](/#comandos-generales-ibm-cloud-cli)

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



## Comandos Generales - IBM Cloud CLI
Comandos relacionado al CLI de IBM Cloud
