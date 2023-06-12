CABE RECALCAR QUE HABRÁ MEJOR MANERA PARA HACER ESTE TUTORIAL, PERO COMO ME HA SIDO MUY DIFÍCIL ENCONTRAR INFORMACIÓN PARA REALIZAR ESTE OBJETIVO, TE DEJO LOS PASOS QUE HICE PARA QUE FUNCIONARA.

En las carpetas cluster, deployment y services os indico que significa cada cosa (ya que es la misma sintaxis en cada caso), aqui simplemente os explico los pasos que hice en forma de tutorial.

# PASO 1. DESCARGAR APLICACIONES NECESARIAS#

Como he comentado en el fichero README.md, estás son las aplicaciones que he usado:
- Docker Desktop = Herramienta que permite a los desarrolladores crear y ejecutar contenedores de manera sencilla en su entorno de desarrollo local. Proporciona una interfaz gráfica intuitiva y fácil de usar para gestionar los contenedores, lo que facilita el desarrollo, la prueba y el despliegue de aplicaciones en contenedores. Además, Docker Desktop es compatible con diferentes sistemas operativos, lo que brinda flexibilidad a los desarrolladores en la elección de su entorno de desarrollo.
- Chocolatey = Herramienta de gestión de paquetes para Windows que simplifica la instalación, actualización y desinstalación de software en el sistema operativo. Con Chocolatey, los usuarios pueden buscar y descargar fácilmente una amplia variedad de paquetes de software populares y de código abierto desde un repositorio centralizado.
- Kind = Herramienta que crea clústeres de Kubernetes de manera rápida y sencilla en entornos locales. Utiliza contenedores Docker para ofrecer un entorno ligero y eficiente para el desarrollo y prueba de aplicaciones en Kubernetes. Es ampliamente utilizado por desarrolladores y equipos de DevOps para simplificar el proceso de configuración y gestión de clústeres Kubernetes.
- kubectl = herramienta de línea de comandos que permite interactuar con clústeres de Kubernetes. Se utiliza para ejecutar comandos en contenedores, gestionar recursos como pods y servicios, y realizar tareas de despliegue y configuración en un clúster de Kubernetes. Es una herramienta fundamental para administrar y controlar entornos de Kubernetes.
- Powershell = Herramienta esencial para administradores de sistemas y desarrolladores en entornos Windows, en este caso, para administrar contenedores docker en kubernetes + clústeres.
- docker compose = Herramienta para definir y administrar aplicaciones multi-contenedor en Docker. Permite especificar servicios, redes y volúmenes en un archivo YAML para desplegar y gestionar fácilmente múltiples contenedores. Simplifica el proceso de configuración y conexión de contenedores, facilitando el despliegue de aplicaciones complejas.
- Kompose = Kompose es una herramienta que permite convertir archivos Docker Compose en manifestos de Kubernetes. Facilita la migración de aplicaciones de Docker Compose a entornos de Kubernetes al generar automáticamente los recursos de Kubernetes necesarios para desplegar los servicios definidos en el archivo Compose. Kompose simplifica el proceso de adaptación de aplicaciones basadas en Docker a clústeres de Kubernetes.

# PASO 2. COMPROVAR DOCKER COMPOSE + FUNCIONAMIENTO DE ALFRESCO CON EL FICHERO docker-compose.yml

En este paso deberemos comprobar que el fichero llamado "docker-compose.yml", el cúal es el que tiene la aplicación de Alfresco, funciona correctamente sin ningún tipo de fallo. Para ello, deberemos guardar éste fichero en una carpeta con permisos para todos (sino no funcionará correctamente). Acto seguido deberemos activar docker compose de nuestro fichero "docker-compose.yml" para que, automáticamente, se generen los 8 contenedores que forman la aplicación Alfresco. Para ello deberemos realizar el siguiente comando:
```
docker-compose -f docker-compose.yml up
```
IMPORTANTE: NO CERRAR POWERSHELL MIENTRAS ESTÁ DOCKER COMPOSE ACTIVO, SINO LOS CONTENEDORES SE PARARÁN Y DEJARÁ DE FUNCIONAR ALFRESCO.

Si todo funciona correctamente, podremos entrar a nuestra aplicación de Alfresco local dándole al apartado llamado "PORTS" que generarán los contenedores. Para acceder a la aplicación escogeremos el puerto del contenedor llamado "PROXY", el cúal nos permitirá redirigir a los diferentes contenedores. Si no hay problema de permisos, podrás acceder a la indentación de Alfresco, y acto seguido, si le das a WebShare o Share, podremos acceder al gesto documental de Alfresco.

AVISO = USUARIO: admin, CONTRASEÑA: admin.

Una vez dentro podremos hacer lo que se nos plazca, eso si, si decides eliminar un contenedor no se volverá a generar, ya que de momento no hemos aplicado Kubernetes, simplemente verificamos que la aplicación funciona correctamente, ya que es el fichero que vamos a utilizar para implementarlo en kubernetes y si no nos funciona correctamente en éste paso, nos vamos a encontrar muchísimos problemas más adelante y será imposible continuar.


# PASO 3. TRADUCIR FICHERO docker-compose.yml A DEPLOYMENTS Y SERVICES AL FORMATO QUE ENTIENDE KUBERNETES
Si solo quieres hacer que te funcione sin saber como se hace, para ello deberás copiar todos los archivos de [kubernetes/deployments](https://github.com/rgrao428/acs-cluster-local/tree/main/kubernetes/deployments) y [kubernetes/services](https://github.com/rgrao428/acs-cluster-local/tree/main/kubernetes/services) en un directorio o carpeta concreta con permisos totales y vacía. Luego deberás seguir los pasos después del apartado OPCIONAL.

Si quieres saber como se realiza esta traducción se hace de la siguiente manera:

--------------------------------------------------------------------OPCIONAL------------------------------------------------------------------------------

Una vez comprobemos que Alfresco nos funciona correctamente en docker compose, podremos parar y eliminar los contenedores que se crean, ya que consumen mucho y ya no los volveremos a utilizar (lo haremos con kubernetes).

Como bien dice la entrada, deberemos traducir el fichero docker-compose.yml a un formato que kubernetes entienda, el cúal es un .yaml con deployments, services, persistent-volumes, etc.
Para ello, podemos traducir el fichero de forma manual o ayudarnos de un comando, ya que tendremos que generar 17 ficheros ".yaml" (deployments + services por cada contenedor) + 1 fichero de network (no influye mucho).

Cabe recalcar que ya no usaremos el fichero docker-compose.yml, usaremos el fichero que está dentro de la carpeta kubernetes, llamado "k-docker-compose.yml". Porque?, porque si has usado el docker-compose.yml al activar docker compose, habrás visto que hay 3 contenedores que no les aparecen puertos, esto es debido a que algunas de las imagenes utilizadas, ya saben que puerto necesitan (en este caso, los 3 contenedores usarán el puerto 8080). El problema es que kubernetes no tiene ni idea de que algunas imagenes tienen los puertos agregados internamente, y por tanto, al ahora de traducirlos te dará muchos problemas para poder hacer que funcionen, por ello, les tenemos que añadir a mano los puertos a los contenedores, en este caso a los contenedores: Alfresco, Share y Content-app. Una vez hecho esto, ya podremos empezar con la traducción de contenedores.

El comando que facilita la traducción se llama Kompose. para ello, deberemos descargarnos la versión de nuestro sistema operativo. Te dejo la URL por si lo quieres descargar en otro SO: https://kompose.io/installation/
En windows se descarga con el comando:
```
choco install kubernetes-kompose
```
Una vez lo tengamos descargados, deberemos dejar nuestro archivo "docker-compose.yml" en un directorio con permisos completos y vacío. Una vez hecho esto, deberemos ejecutar el siguiente comando:
```
Kompose convert -f docker-compose.yml
```
Este comando nos facilitará el trabajo a la hora de generar los 17 ficheros .yaml. Pero si observamos detenidamente, dentro de cada fichero hay mucha información basura que no nos interesa para nada o, incluso, no permite que funcione correctamente la aplicación en kubernetes. Para ello, tendremos que eliminar esa información de manera manual. He adjuntado mis ficheros .yaml por si os aburrís haciendo tantos .yaml. por si no sabeis como hacerlo o por si solo quereis que os funcione.

--------------------------------------------------------------------OPCIONAL------------------------------------------------------------------------------

Una vez tengamos los ficheros corregidos, deberemos de iniciar PowerShell y ubicarnos en el directorio donde estén los ficheros .yaml. EN MI CASO, se puede hacer con el siguiente comando:
```
cd "C:\Users\raulg\acs-kube"
```
Una vez estemos dentro del directorio haremos el siguiente comando para añadir nuestros ficheros .yaml a kubernetes:
```
kubectl apply -f .
```
Una vez se generen, podremos ver si están corriendo o si han tenido algún problema al crearse los contenedores. Esto se hace con el siguiente comando:
```
kubectl get all
```
Si quisieramos ver solo los pods, se haría tal que así:
```
kubectl get pods
```
Si quisieramos ver solo los deployments, se haría tal que así:
```
kubectl get deployments
```
Si quisieramos ver solo los servicios, se haría tal que así:
```
kubectl get svc
```
Si vemos que los pods tienen problemas para crearse, podemos ver los logs:
```
kubectl logs <nombre-del-pod>
```
Otra manera de verlo, sería abriendo una descripción detallada del pod escogido:
```
kubectl describe pod <nombre-del-pod>
```
Cabe recalcar que esto se puede hacer con cualquier contenedor, servicio, pv, pvc, etc.

Ahora sí, tendremos alfresco funcional con alta disponibilidad y replicidad, és decir, que si destruyes un pod, se volverá a generar sin tiempo de inactividad. Dependiendo de si le pones 1 o + replicas consumirá muchos más recursos, pero a cambio, tendrás mucha más seguridad y balanceo de carga.

AVISO IMPORTANTE: PROBABLEMENTE NO OS FUNCIONARÁ, YA QUE SI HABEIS COGIDO MIS FICHEROS, EL DEPLOYMENT POSTGRES (QUE ES LA BASE DE DATOS), TIENE UN VOLUMEN ASIGNADO, Y AL NO ASIGNARLE VOLUMEN SE PUEDE VOLVER LOCO, SE SOLUCIONA MÁS ADELANTE, (LUEGO DE GENERAR EL CLUSTER).

# PASO 4. AÑADIR CLUSTER A DOCKER DESKTOP
Una vez tenemos el alfresco traducido deberemos generar un cluster para poder hacer alta disponibilidad y balanceo de carga de manera bastante más segura e interesante.

Para ello, deberemos de realizar un fichero .yaml de un cluster donde le indicas la cantidad de nodos que tendrá. El fichero que te proporciono, te ofrece 3 nodos, 2 workers, que son los que tendrán los contenedores que harán funcionar la aplicación alfresco junto con las replicas, y 1 nodo control-panel o maestro, que se encargará de gestionar el balanceo de carga y la alta disponibilidad de los contenedores. 

Una vez tengamos el fichero .yaml del cluster, deberemos de generar el cluster, en mi caso, utilicé kind (Kubernetes in Docker), que te permite generar clústeres muy fácil con el siguiente comando:
```
kind create cluster --config cluster.yaml --name alfresco
```
El nombre es muy personal, yo le pusé alfresco, ya que es una aplicación de alfresco.

Una vez creado, nos mostrará un comando para usar ese contexto, ya que solo se puede usar un cluster cada vez, para ello, deberemos realizar el siguiente comando con tus datos:
```
kubectl config use-context kind-alfresco
```
Si quieres ver cuantos nodos tienes, deberás realizar el siguiente comando:
```
kubectl get nodes
```

Una vez tenemos el cluster implementado correctamente sin ningún error, deberemos repetir el PASO 2 con los ficheros ya traducidos, és decir, volveremos a repetir el comando:
```
kubectl apply -f .
```
Una vez aquí, si miramos de forma detallada los pods, podremos ver que cada  pod está asignado a un nodo worker concreto, esto se mira con el comando:
```
kubectl get pods -o wide
```

# PASO 5. HACER NFS Y PERSISTENT-VOLUMES (PV) Y PERSISTENT-VOLUMES-CLAIM (PVC)
Como bien dije antes, ya tenemos alta disponibilidad y balanceo de carga, pero si a alguien se le ocurre tirar la base de datos, nos molestaría mucho, ya que los datos no se guardarían y perderiamos datos muy valiosos como los usuarios en nuestra base de datos de alfresco.

Para ello deberemos hacer un NFS o SAMBA, para poder compartir carpetas entre equipos y poder enlazar estas carpetas compartidas a rutas concretas para que puedan acceder los contenedores que lo necesiten como la base de datos y aunque se caiga el pod, no se pierdan los datos nunca.

Para ello, deberemos realizar los siguientes pasos de manera similar a esta:
## NODO CONTROL-PANEL O MAESTRO
Desde el dashboard Docker Desktop entraremos en el nodo control-panel o maestro. Le daremos al apartado terminal y realizaremos los siguientes pasos:
1. Actualizar
```
apt update
```
2. Instalar ping para probar conectividad
```
apt-get install iputils-ping
```
3. Instalar nano para poder entrar en ficheros
```
apt install nano
```
4. Instalar nfs-kernel-server y nfs-common para poder compartir carpetas a otros equipos.
```
apt install nfs-kernel-server nfs-common
```
5. generar un archivo para poder compartirlo posteriormente, en mi caso /var/datos. Hacerlo en una ruta con permisos.
```
mkdir /var/datos
```
6. Añadiremos al /etc/exports el fichero a compartir, a que red irá ("*"=todas) y las opciones que tendrá la carpeta.
```
nano /etc/exports
```
```
/var/datos *(rw,sync,no_root_squash,no_subtree_check)
```
7. Reiniciamos el servicio nfs-kernel-server para que se apliquen los cambios.
```
systemctl restart nfs-kernel-server
```
8. Verificamos que se ha compartido la carpeta a todas las IP de la red.
```
showmount -e 127.0.0.1
```
## NODOS WORKERS
Desde el dashboard Docker Desktop entraremos en cada nodo worker. Le daremos al apartado terminal y realizaremos los siguientes pasos:
1. Actualizar
```
apt update
```
2. Instalar ping para probar conectividad
```
apt-get install iputils-ping
```
3. Instalar nano para poder entrar en ficheros
```
apt install nano
```
4. Instalar nfs-common para poder montar la carpeta compartida del nodo control-panel a la carpeta que queramos en cada nodo worker.
```
apt install nfs-common
```
5. generar un archivo para poder montarlo posteriormente, en mi caso /mnt/comp. Hacerlo en una ruta con permisos.
```
mkdir /mnt/comp
```
6. montar de manera temporal la carpeta compartida con la carpeta a montar. En mi caso la ip interna de mi nodo control-panel (donde está el nfs-kernel-server) es "172.19.0.4", la ruta hecha en el nodo donde esta el nfs-kernel (/var/datos) y finalmente, la ruta donde quieres compartir esa carpeta en el nodo worker (/mnt/comp).
```
mount -t nfs4 172.19.0.4:/var/datos /mnt/comp
```
Si no sabes tu ip interna, la puedas saber con el siguiente comando:
```
kubectl get nodes -o wide
```
7. Si no aparece ningún error de montaje, podremos montarlo de manera permantente entrando en /etc/fstab y pondremos lo siguiente:
```
nano /etc/fstab
```
```
172.19.0.4:/var/datos /mnt/comp/ default 0 0
```
8. Para comprobar que se ha montado correctamente, haremos el comando:
```
mount | grep nfs
```

### TROUBLESHOUTING CON NODOS WORKERS EN NFS
Si tienes problemas con nfs-common, tendrás que realizar los siguientes pasos cada vez que inicies el cluster, ya que el archivo de nfs-common se te redirige a /dev/null, ya que el nodo detecta que esta incompleto o corrupto.

1. borrar el fichero que se redirige a /dev/null.
```
rm /lib/systemd/system/nfs-common.service
```
3. Reiniciar el servicio que corre servicios (daemon).
```
systemctl daemon-reload
```
4. Iniciar nfs-common
```
systemctl status nfs-common
```

# PASO 6. PASAR DATOS DE LA BASE DE DATOS A LA CARPETA COMPARTIDA.
Ahora tenemos la carpeta compartida correctamente y preparada para hcer volumenes persistentes, pero hay un problema, no tenemos nada de información guardada en la carpeta compartida por nfs. Por ello deberemos de aplicar con "kubectl apply -f ." el alfresco y en este caso concreto, guardar los datos de la base de datos PostgresSQL.

1.Ubicar como se llama el pod postgresSQL de alfresco con el comando:
```
kubectl get pods
```
2. generar una carpeta en tu máquina fisica con privilegios totales a todos, una vez hecho esto, pasar los archivos que quieras guardar a esa carpeta en tu máquina física. Te recomiendo ponerte en el directorio que creaste antes de hacer el comando, ya que me dió problemas a la hora de escogerle la ruta de mi máquina física.
```
kubectl cp postgres-6796f4f6b6-6b6pt:/var/lib/postgresql/data .
```
Verifica que los datos se han pasado bien.
3. Pasar los archivos de tu máquina física al nodo control-panel, esto se hace con el siguiente comando:
```
scp -r root@172.19.0.4:/var/datos "C:\Users\raulg\dades"
```
Otra manera de hacerlo es:
```
scp -r "C:\Users\raulg\dades" 172.19.0.4:/var/datos
```
# PASO 7. REALIZAR VOLUMENES PERSISTENTES Y CLAIMS.
Bueno, si has llegado hasta aquí sin ningún problema, felicidades, solo te queda implementar el persistent-volume y el persitent-volume-claim de la ruta que quieres guardar, a que lo quieres montar, etc.

Te recomiendo que escojas mi fichero, ya que está configurado correctamente para funcionar.

Finalmente lo implementas a Kubernetes con:
```
kubectl apply -f .
```
Para ver los persistent-volumes que tienes:
```
kubectl get pv
```
Para ver los persistent-volumes-claim que tienes:
```
kubectl get pvc
```
IMPORTANTE: Tienes que verificar que el estado no esté en "AVAILABLE", sino en "BOUND", ya que sino no se vincula correctamente. 
Además, te aconsejo que primero generes los volumenes persistentes y luego los deployments + services, más que nada, para poder garantizar que los datos de los volumenes persistentes se aplican correctamente.


Una vez hecho esto, FELICIDADES!!!. Solo te queda entrar con los puertos que has seleccionado desde el dashboard DOCKER DESKTOP y a disfrutar. Que te tiren lo que quieran que seguirá estando.

Espero que te haya gustado este extenso tutorial, ya que me ha costado mucho buscar información sobre este proyecto que hice sobre Alfresco. Vuelvo a repetir que seguramente haya una manera muchísimo mejor de hacer que funcione, pero estoy bastante satisfecho con el trabajo completado después de 4 meses de investigación y de pruebas para que funcionara, ya que en mi vida sabía que eran dockers, ni contenedores. Muchas Gracias!!! y a disfrutar.
