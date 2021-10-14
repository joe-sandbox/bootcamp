
# Cloud-Native Bootcamp

Bienvenido a este cloud-native bootcamp!
A lo largo de estas sesiones estaremos aprendiendo e introduciendonos a este nuevo mundo basado en la nube.

En este repositorio encontrrás los laboratorios de cada tema así como material de apoyo.

## Requerimientos mínimos

- Acceso a una cuenta de IBM Cloud.
- Instalación de [Docker](https://www.docker.com/products/docker-desktop)
- Instalación de [VSCode](https://code.visualstudio.com)


## Requerimientos de tools de manera local
Estas son las herramientas que puedes instalar localmente en tu computadora en caso que desees tener las herramientas.
- Instalación de [Docker](https://www.docker.com/products/docker-desktop)
- Instalación de [IBM Clooud CLI](https://cloud.ibm.com/docs/cli?topic=cli-install-ibmcloud-cli)
- Instalación de [VSCode](https://code.visualstudio.com)


## Indice de laboratorios
| ID | Laboratorio |
| ------------- | ------------- |
| 1  | [Bienvenida a IBM Cloud](https://github.com/joeg1307/bootcamp/blob/main/labs/lab-1-Bienvenida-IBM-Cloud.md)   |
| 2  | [Intro a contenedores ](https://github.com/joeg1307/bootcamp/blob/main/labs/lab-2-Intro-Contenedores.md)  |
| 3  | [Intro a Imagen del Bootcamp](https://github.com/joeg1307/bootcamp/blob/main/labs/lab-3-Intro-Imagen-Bootcamp.md)  |
| 4  | [Intro a consola Openshift](https://github.com/joeg1307/bootcamp/blob/main/labs/lab-4-Intro-Consola-Openshift.md)  |
| 5  | [Lab hola mundo en Openshift ](https://github.com/joeg1307/bootcamp/blob/main/labs/lab-5-HolaMundo-Openshift.md)  |
| 6.0  | [Intro al Laboratorio ](https://github.com/joeg1307/bootcamp/blob/main/labs/lab-6-0-Intro-al-lab.md)  |
| 6.1  | [Parte 1 Deploy MS Productos+ BD ](https://github.com/joeg1307/bootcamp/blob/main/labs/lab-6-1-Deploy-MS-Productos-BD.md)  |
| 6.2  | [Parte 2 Deploy MS Customers+BD ](https://github.com/joeg1307/bootcamp/blob/main/labs/lab-6-2-Deploy-MS-Customers-BD.md)  |
| 6.3  | [Parte 3 Deploy MS WebAPP+ OC Route (Teardown)](https://github.com/joeg1307/bootcamp/blob/main/labs/lab-6-3-Deploy-MS-WebAPP-OCRoute.md)  |
| 7  | [DevOps](https://github.com/joeg1307/bootcamp/blob/main/labs/lab-7-Devops.md)  |
| 8  | [Functions](https://github.com/joeg1307/bootcamp/blob/main/labs/lab-8-Functions.md)  |


## Asignaciones de espacios y puertos
Al ser un mismo cluster para todos requerimos "repeartirnos" los puertos dispomibles para nuestros microservicios.
| ID-Participante | Container Registry Namespace | Openshift Project (Kurbenetes Namespace) | Puerto Servicio MySQL | Puerto Servicio Catalog | Puerto Servicio Customer | Puerto servicio Web App Lite
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 1 | bootcamp-01 | bootcamp-01 | 30006 | 30101 | 30201 | 30301 |
| 2 | bootcamp-02 | bootcamp-02 | 30007 | 30102 | 30202 | 30302 |
| 3 | bootcamp-03 | bootcamp-03 | 30008 | 30103 | 30203 | 30303 |
| 4 | bootcamp-04 | bootcamp-04 | 30009 | 30104 | 30204 | 30304 |
| 5 | bootcamp-05 | bootcamp-05 | 30010 | 30105 | 30205 | 30305 |
| 6 | bootcamp-06 | bootcamp-06 | 30011 | 30106 | 30206 | 30306 |
| 7 | bootcamp-07 | bootcamp-07 | 30012 | 30107 | 30207 | 30307 |
| 9 | bootcamp-08 | bootcamp-08 | 30013 | 30108 | 30208 | 30308 |
| 9 | bootcamp-09 | bootcamp-09 | 30014 | 30109 | 30209 | 30309 |
| 10 | bootcamp-10 | bootcamp-10 | 30015 | 30110 | 30210 | 30310 |
| 11 | bootcamp-11 | bootcamp-11 | 30016 | 30111 | 30211 | 30311 |
| 12 | bootcamp-12 | bootcamp-12 | 30017 | 30112 | 30212 | 30312 |
| 13 | bootcamp-13 | bootcamp-13 | 30018 | 30113 | 30213 | 30313 |
| 14 | bootcamp-14 | bootcamp-14 | 30019 | 30114 | 30214 | 30314 |
| 15 | bootcamp-15 | bootcamp-15 | 30020 | 30115 | 30215 | 30315 |
