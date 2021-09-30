# Introducción IBM Cloud
## Lo que aprenderas de este lab:
poner los objetivos del laboratorio
## Lo que necesitarás
- [Docker](https://www.docker.com) instalado.
## Video a seguir
## Pasos a seguir
1. Inicie un shell y confirme que Docker está instalado. El número de versión no es particularmente importante.
```bash
docker -v
```
2. Hagamos un *Hola Mundo*
```bash
docker run hello-world
```
Ejemplo de salida:
```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
b04784fba78d: Pull complete
Digest: sha256:f3b3b28a45160805bb16542c9531888519430e9e6d6ffc09d72261b0d26f f74f
Status: Downloaded newer image for hello-world:latest
Hello from Docker!
This message shows that your installation appears to be working correctly.
To generate this message, Docker took the following steps:
1. The Docker client contacted the Docker daemon.
2. The Docker daemon pulled the "hello-world" image from the
Docker Hub.
3. The Docker daemon created a new container from that image which
runs the
 executable that produces the output you are currently reading. 4. The Docker daemon streamed that output to the Docker client,
      which sent it
          to your terminal.
To try something more ambitious, you can run an Ubuntu container with:
$ docker run -it ubuntu bash
Share images, automate workflows, and more with a free Docker ID: https://cloud.docker.com/
For more examples and ideas, visit: https://docs.docker.com/engine/userguide
```
Observa el mensaje `Unable to find image 'hello-world:latest' locally`. 
Primero, verás que la imagen se descargó automáticamente sin ningún comando adicional. En segundo lugar, 
se agregó la versión `:latest` la más reciente al nombre de la imagen. No especificamos una versión para esta imagen.

3. Vuelve a ejecutar "hello-world" Observe que la imagen no se vuelve a tirar hacia abajo. Ya existe localmente, por lo que se ejecuta.
```bash
docker run hello-world
```
Verás que el resultado de la imagen es diferente ahora.

4. Para demostrar que existe de manera local:
```bash
docker images
```
Ejemplo de salida
```bash
REPOSITORY  TAG     IMAGE ID      CREATED       SIZE 
hello-world latest  1815c82652c0  2 months ago  1.84kB
```
5. Conociendo docker hub.
Hasta ahora lo que hemos visto es que el motor de Docker ha descargado la imagen de Docker desde un registro de imagenes público llamado Docker Hub.
Si accedes al acceso directo de la [imagen](https://hub.docker.com/_/hello-world/) veras más documentación sobre la misma imagen.

6. Ahora intentemos con una imagen más normal. Descarguemos una base de datos NoSQL de Documentos basada en Apache CouchDB.
```bash
docker rund -d couchdb:2
```
Observarás que la imagen se descargará automáticamente con el caso anterior. Además verás la bandera `-d` que sirve para ejcutar en segundo plano el contenedor. 
En cuanto al `:2`sirve para etiquetar que version del contenedor queremos ejecutar. 

7. Para revisar si el contenedor sigue corriendo docker nos ofrece el siguiente comando:
```bash
docker ps
```
Ejemplo de resultado:
```bash
CONTAINER ID      IMAGE       COMMAND                   CREATED         STATUS        PORTS       NAMES
2169c6b42e5c      couchdb     "tini -- /docker-e..."    8 minutes ago   Up 8 minutes  5984/tcp    nervous_poincare
```
8. Una imagen se puede ejectuar múltiples veces
```bash
docker rund -d couchdb:2
```
Ahora tenemos dos contenedores corriendo. volvamos a revisar su estado.
```bash
docker ps
```
```bash
CONTAINER ID      IMAGE       COMMAND                   CREATED         STATUS        PORTS       NAMES
f9885aaf0a96      couchdb     "tini -- /docker-e..."    2 minutes ago   Up 2 minutes  5984/tcp    brave_booth
2169c6b42e5c      couchdb     "tini -- /docker-e..."    8 minutes ago   Up 8 minutes  5984/tcp    nervous_poincare
```
9. Para detener un contenedor se puede detener usando su nombre o su id. Si optas por el id puedes usar los 3 primeros caracteres.
Tambien puedes especificar multiples ids separados por espacio.
```bash
docker stop f98 216
```
```bash
docker ps
```
```bash
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
```
10. Sin embargo si revisamos las imagenes veremos que todavía existe. Solo una, recuerda que el contenedor se instancia partiendo de la imagen:
```bash
docker images
``` bash
REPOSITORY      TAG       IMAGE ID      CREATED         SIZE
couchdb         latest    7f8923b03b7f  5 weeks ago     225MB
hello-world     latest    1815c82652c0  2 months ago    1.84kB
```
11. Si intentas eleminar la imagen verás que no podrás:
```bash
docker rmi couchdb
```
```bash
Error response from daemon: conflict: unable to remove repository reference "couchdb" 
(must force) - container 2169c6b42e5c is using its referenced image 7f8923b03b7f
```
12. Así como listamos archivos ocultos en linux podemos agregar la misma bandera en docker:
```bash
docker ps -a
```
```bash
CONTAINER ID      IMAGE       COMMAND                   CREATED         STATUS                NAMES
f9885aaf0a96      couchdb     "tini -- /docker-e..."    2 minutes ago   Exited 2 minutes ago  brave_booth
2169c6b42e5c      couchdb     "tini -- /docker-e..."    8 minutes ago   Exited 4 minutes ago  nervous_poincare
```
13. Ahora que podemos ver los IDs de esos contenedores detenidos podemos eleminarlos así como su respectiva imagen
```bash
docker rm f98 216
```
```bash
docker rmi couchdb
```
Ejemplo de resultado:
```bash
Untagged: couchdb:latest
Untagged: couchdb@sha256:eb463cca23b9e9370afbd84ae1d21c0274292aabd11b2e5b904d 4be2899141ff
Deleted: sha256:7f8923b03b7f807ffbd51ff902db3b5d2e2bbbc440d72bc81969c6b05631 7c8a
Deleted: sha256:d53bc50464e197cbe1358f44ab6d926d4df2b6b3742d64640a2523e46401 04c4
Deleted: sha256:851748835e9443fa6b8d84fbfada336dffd1ba851a7ed51a0152de3e3115 b693
Deleted: sha256:feb87fb4c017e2d01b5d22dee3f23db2b3f06a0111a941dda926139edc02 7c8e
Deleted: sha256:e00c9e10766f6a4d24eff37b5a1000a7b41c501f4551a4790348b94ff179 ca53
Deleted: sha256:b64ffebe7ca9cec184d6224d4546ccdfecce6ddf7f5429f8e82693a8372c f599
Deleted: sha256:f78934f92a8a0c822d4fc9e16a6785dc815486e060f60b876d2c4df19255 84d8
Deleted: sha256:491c6d0078fa4421d05690c79ffa4baf3cdeb5ead60c151ab64af4fb6d4d 93dc
Deleted: sha256:2c40c66f7667aefbb18f7070cf52fae7abbe9b66e49b4e1fd740544e7cea ebdc 
```
Volver a ejecutar `docker ps -a` y `docker images`para confirmar que si se hayan eleminado tanto el contenedor como la imagen.

14. Por último instanciaremos varias instancias de un servidor de aplicaciones de Liberty.
```bash
for i in 80 81 82 83 84; do docker run -d -p $i:9080 websphere-liberty:webProfile7 ; done
```
15. En vez de hacer un listado de contenedores veremos en tiempo real sus estadísticas:
```bash
docker stats
```
```bash
CONTAINER         CPU %     MEM USAGE / LIMIT     MEM %      NET I/O        BLOCK I/O     PIDS 
4fbf4b2d5440      68.96%    49.14MiB / 1.952GiB   2.46%      578B/0B        0B / 766kB    35
.
.
.
```
16. Ahora abre un navegador e intenta ingresar a una instancia del contenedor.
Primero intenta con `http://localhost:81` y verás la pantalla de bienvenida de Liberty.
Ahora intenta con `http://localhost:82`y verás una pantalla similar, puedes intentar con el resto de los puertos que asignamos en el ciclo for en el paso 14.

17. Hemos termino ahora puedes detener todos los contenedores, eliminarlos y tambien sus imagenes.
## Conclusiones

## Links de interes
- [Docker](https://www.docker.com)
- [Docker Hub](https://hub.docker.com)
