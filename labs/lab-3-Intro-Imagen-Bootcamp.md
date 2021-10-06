# Introducción a la Imagen de laboratorios
## Lo que aprenderas de este lab:
En este laboratorio aprenderas a configurar y utilizar la imagen de docker precargada con las herramientas necesarias para poder realizar los laboratorios.
## Lo que necesitarás
- Docker instalado
- Haber terminado el Lab 2 de Introducción a Docker.
## Video a seguir
[Link al video](https://ibm.ent.box.com/file/869516249153)
## Pasos a seguir
### 1. Abre una terminal (En caso de windows si no cuentas con una consola con Bash te recomiendo uses PowerShell) y ejecuta los siguientes comandos:
Para windows:
```bash
cd %USERPROFILE%
mkdir mybootcamp
cd mybootcamp
```
Para MacOS:
```bash
cd ~
mkdir mybootcamp
cd mybootcamp
```
### 2. Abre VSCode con el directorio de myboootcamp pre-cargado.
Desde la terminal:
```bash
code .
```
Desde la aplicación:
File>Open... (Buscar directorio mybootcamp)
Archivo>Abrir... (Buscar directorio mybootcamp)

### 3. Ejecuta el contenedor
Para que este paso sea exitoso asegurate de estar dentro del directorio de mybootcamp.
```bash
docker run -it --name cnbtools --mount type=bind,source="$(pwd)",target=/bootcamp hjosef13/cnb-tools:2021.3.1
```
### 4. Descarga los repositorios y archivos necesarios
```bash
git clone https://github.com/IBM/container-service-getting-started-wt.git 
git clone https://github.com/ibm-cloud-academy/LightBlueCompute 
curl https://bootcamp-gradle-build.mybluemix.net/ms/catalog --output catalog.jar 
curl https://bootcamp-gradle-build.mybluemix.net/ms/customer --output customer.jar
```

### 5. Configura la terminal de IBM Cloud CLI
Antes de ejecutar el comando asegurate de cambiar los valores `<usuario>`y `<password>`por tu usuario y contraseña de IBM Cloud.
```bash
ibmcloud login -u <usuario> -p <password> -r us-south
```

Todo lo listo para empezar a trabajar en los laboratorios!

## He terminado como salgo de esto?
Para salir del contenedor basta con ingresar `exit` para salir. 


## He salido del contenedor como puedo volver a entrar? 
Si saliste o reiniciaste tu computadora puedes seguir trabajando con el contenedor e iniciarlo de esta manera:
```bash
docker start -i cnbtools
```

Si por alguna extraña razon no funcionara porque extrañamente sigue ejecutandose el contenedor intenta con el siguiente comando:
```bash
docker exec -it cnbtools bash
```


## Conclusiones
Hemos aprendido como utilizamos la imagen de docker para aprovechar un ambiente contenerizado y así mismo partir de un ambiente común.
Además nos introdujimos al mundo de volumenes y el como nos beneficia.
## Links de interes
[Documentación sobre Volumenes de Docker](https://docs.docker.com/storage/volumes/)
