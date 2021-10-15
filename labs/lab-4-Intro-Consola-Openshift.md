#  Introducción a la consola de Openshift
## Lo que aprenderas de este lab:
Con este laboratorio aprenderás sobre las experiencias que ofrece la consola de Openshift. 
 - [x] Como acceder desde IBM Cloud a la consola de OpenShift
 - [x] Cambiar entre vistas de Administrador y Desarrollador
 - [x] Crear diferentes recursos a través de Operadores y o la opción de agregar recursos. 
 - [x] Revisar diferentes componentes sus respectivos YAML y Logs
## Lo que necesitarás
  - [ ] Una cuenta de IBM Cloud
  - [ ] Un Cluster de Openshift
## Video a seguir

## Pasos a seguir

### 1- Accediendo a la consola de Openshift
1. Dentro del portal de [IBM Cloud](https://cloud.ibm.com) puedes ingresar a la lista de recursos para localizar
tu cluster de Openshift (recuerda acceder al cluster que se te asignó, puedes consultar las asignaciones[aquí](https://github.com/joeg1307/bootcamp#asignaciones-de-espacios-y-puertos).
2. Da click en tu cluster y serás redireccionado a una vista como la siguiente:
  ![lab-3-OpenshiftIBMCloud.png](images/lab-3-OpenshiftIBMCloud.png)
  En esta sección podrás ver el cluster de Openshift pero desde la perspectiva de IBM Cloud, por lo que verás más información relacionada a la
  infraestructura del mismo cluster y la salud en general del mismo cluster.
3. En la parte superior derecha verás una opción que dice "Consola Web de OpenShift" da click en ella para entrar a la `consola del cluster`.

### 2- Conociendo las experiencias para Administrador y para desarrollador
Openshift ofrece 2 experiencias principales para sus usuarios; una para desarrolladores y otra para administradores.
![vistahome](images/lab-3-OpenshiftIHome.png)
Puedes intercambiar entre ambas al dar click en el botón de la esquina superior izquierda:
![changeExperience](images/lab-3-OpenshiftchangeExperience.png)

#### Las prinicipales diferencias 
Como podrás observar que la principal diferencia es que tienen menús diferentes, esto es debido a que cada tipo de usuario tiene tareas diferentes que realizar. 
OpenShift busca facilitar realizar dichas tareas, por lo que para la experiencia de desarrollador podrás encontrar vistas para. monitorerar tus despliegues, 
revisar la topología de tu aplicación, todo enfocado a tu aplicación. 
![developerExperience](images/lab-3-OpenshiftDeveloper.png)

En cuanto a la perspectiva de administrador podrás encontrar vistas más enfocadas a realizar tareas de administración y mantenimiento del propio cluster.
Tareas como instalación de operadores, almancemanmiento, manejo de usuarios, etc.
![changeLab](images/lab-3-OpenshiftAdmin.png)

### 3- Creando recursos
Dentro de OpenShift se pueden crear diferentes tipos de recursos ya sean de Kuberentes o de OpenShift. 
Una forma de crearlos es a través del `icono de más` en la parte superior e ingresando en YAML del recurso a crear.
Otra forma es través de la línea de comandos, la cual puedes utilizar y autenticarte a través del comando que muestra en la consola en la sección de tu usuario.
Desde la versión 4 de OpenShift se introdujeron los `Operators` u operadores en español. Prácticamente los operadores nos permiten crear Operadores, los cuales
funcionan como plantillas, para crear instancias dentro del cluster. Muy similar al catálogo de IBM Cloud que vimos en el laboratorio anterior. 

Vamos a crear un recurso. 
## Conclusiones

## Links de interes
