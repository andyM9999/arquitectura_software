# Tutorial Orquestador de contenedores Docker (Kubernetes)

Hoy en día Docker, la compañía ofrece servicios empresariales y mantiene la versión de la comunidad, sin embargo desde el  2017 el proyecto cambió de nombre y ahora es the Moby Project.

Arquitecturalmente hablando Docker se compone de un servidor llamado  Docker Daemon), un  API y un  cliente.


![Image for post](https://miro.medium.com/max/2022/1*7YtVQEWI11ZsuLq1AgnQNA.png)


El cliente utiliza el API para comunicarse con el servidor que realiza las operaciones necesarias. El servidor está alojado en un Host y los contenedores se ejecutan a partir de las imágenes construidas. Cada imágen está compuesta por un conjunto de capas almacenadas en volúmenes que son equivalentes a un sistema de archivos. Docker optimiza el almacenamiento de estos volúmenes con un concepto de diferencias.


![Image for post](https://miro.medium.com/max/769/1*IRM9XcB-b9yDfR37WJlfZw.jpeg)


El concepto de diferencias es similar al de git, donde solamente se almacena en cada commit los cambios con respecto al estado anterior. En el caso de los contenedores, solamente se almacena en ellos las diferencias con respectos a una base común.

Bajo este concepto el espacio requerido por cada contenedor se reduce considerablemente y lo ideal sería tener una sola capa común que pueda ser compartida por muchos contenedores.

Para la instalación de Docker, recomendamos visitar la  [página oficial](https://docs.docker.com/install/) para los detalles específicos de cada sistema operativo.

Cada imágen está descrita en un archivo llamado  `Dockerfile`  dentro de este archivo se encuentran todas las etapas necesarias para construir la imágen. Mostramos un ejemplo de una  [aplicación sencilla en django](https://github.com/contraslash/con-la-ballena-a-la-nube/tree/master/ejemplo-django)
```
# Especificamos la imágen de la que se heredará**FROM** python:3.6-alpine# Definimos algunas variables de ambiente**ENV** LIBRARY_PATH=/lib:/usr/lib**ENV** PYTHONUNBUFFERED 1# Ejecutamos algunos comandos para aprovisionar la imágen**RUN** mkdir /code**WORKDIR** /code**ADD** requirements.txt /code/**RUN** pip install -r requirements.txt# Copiamos el código**ADD** . /code/# Exponemos el puerto por donde se interactuará con la aplicación**EXPOSE** 8000# Definimos el comando de entrada a la aplicaciónCMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```
La especificación del archivo  `Dockerfile`  la encontramos en la  [documentación oficial](https://docs.docker.com/engine/reference/builder/#usage).

Para tener un contenedor ejecutando esta especificación, contruimos la imágen con el comando docker build
```
docker build -t contraslash/ejemplo-django .
```
Verificamos la creación de esta imágen con el comando
```
docker images
```
La ejecución de la imágen construida se realiza por medio de
```
docker run -p 8000:8000 contraslash/ejemplo-django
```
Hasta este punto tenemos una imágen construida y un contenedor con esa imágen ejecutándose en nuestra máquina local, sin embargo la potencia de los contenedores se presenta a la hora de realizar despliegues con ellos. Para esto añadimos un nuevo concepto llamado registro.

[Docker registry](https://docs.docker.com/registry/)  es un repositorio de artefactos donde podemos almacenar imágenes de docker. La versión comercial se encuentra en  [hub.docker.com](https://hub.docker.com/), pero es posible tener nuestro propio registro a partir de la  [imágen oficial del registro](https://docs.docker.com/registry/deploying/)  de docker.

Antes de desplegar una imágen al registro, se hace útil diferenciar una construcción de otra. Siguiendo el concepto de versiones, docker añade un nuevo concepto llamado etiquetas.

Las etiquetas se usan para diferenciar una construcción de otra. Se denominan  [tags](https://docs.docker.com/engine/reference/commandline/tag/)  que representan una versión de construcción de una imágen. Por defecto la etiqueta de cada imágen al realizar el proceso de construcción es  `latest`

Para interactuar con las etiquetas existe el comando  `docker tag`

docker tag contraslash/ejemplo-django:latest contraslash/ejemplo-django:v1

Cuando hemos marcado nuestra versión actual por medio de una etiqueta podemos desplegar esta imágen a nuestro registro.
```
# Nos autenticamos en el registro, si no se especifica un servidor se utilizará hub.docker.com  
docker login [https://registry.contraslash.com](https://registry.contraslash.com/)

# Luego desplegamos la versión v1 a nuestro registro  
docker push contraslash/ejemplo-django:v1
```
El proceso de obtener una imágen de un registro se realiza por medio del comando  `docker pull`
```
docker pull contraslash/ejemplo-django:v1
```
Hasta ahora hemos creado imágenes y contenedores con docker, pero ¿cómo se manejan estas imágenes? son procesos, así que ¿quién los inicia? si el sistema operativo los mata, ¿quién los reinicia? La respuesta a todas estas preguntas es: Un orquestador

## ¿Qué es un orquestador?

Los orquestadores llevan mucho tiempo entre nosotros y hacen referencia a esa figura de director de orquesta que se encarga que todo suene bien. Creo que la definición mas precisa la encontré en  [Search IT Operations](https://searchitoperations.techtarget.com/definition/cloud-orchestrator): ‘Un orquestador es un programa de software que maneja las interconexiones e interacciones entre cargas de trabajo en nubes públicas y privadas. Conecta las tareas automatizadas en un flujo de trabajo cohesivo para cumplir las metas, con vigilancia de permisos y aplicación de políticas’.

Entre las labores de un orquestador está la labor de levantar y aprovisionar servidores, monitorear y configurar la red, escalar servicios, volúmenes y todas las demás configuraciones necesarias para que las aplicaciones estén siempre disponibles.

La idea detrás de todo esto es automatizar al máximo las operaciones, para minimizar el factor humano y tener un control mas holístico de toda la infraestructura de red.

Un concepto clave que me encanta mencionar es que los servidores deben ser tratados como  [rebaños y no como mascotas](http://cloudscaling.com/blog/cloud-computing/the-history-of-pets-vs-cattle/)  lo que nos atrae a los  [servidores inmutables](https://martinfowler.com/bliki/ImmutableServer.html), trayendo la referencia siempre práctica de que los servidores deben ser mas que  [copos de nieve](https://martinfowler.com/bliki/SnowflakeServer.html) unos  [fénix](https://martinfowler.com/bliki/PhoenixServer.html)  que puedan morir y renacer en la medida de lo necesario sin que tengamos que realizar una configuración única a cada uno de ellos.

Entendiendo ahora que nuestra aplicación no pertenece a un mundo donde ella es el centro del mundo, requiriendo cuidados especiales, y que mas allá de eso podemos delegarle el cuidado y mantenimiento a una herramienta automática podemos hablar de  [Kubernetes](https://kubernetes.io/).

Kubernetes nace como una iniciativa de código abierto a  [Borg](https://ai.google/research/pubs/pub43438), el maestro de marionetas detrás de la infraestructura de Google. Su idea principal es independizar los servicios de tal manera que no existan conflictos entre ellos, creando capas de abstracción para que el mantenimiento sea simple y no requiera interacción directa de un humano.

El concepto báse de Kubernetes es el  [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/), un conjunto de contenedores que estarán ejecutándose en el mismo anfitrión con una ip única y que comparten recursos. Todos los contenedores dentro del pod se pueden comunicar entre ellos a través de su interfaz de red común  `localhost`
```
**apiVersion: v1  
kind: Pod  
metadata:  
  name: myapp-pod  
  labels:  
    app: myapp  
spec:  
  containers:  
  - name: myapp-container  
    image: busybox  
    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']**
```
Los pods viven y mueren, la manera de controlar este comportamiento es a través de un  [Replica Set](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/), un mecanismo de autoescalamiento y control de pods para mantener servicios disponibles.

También es importante garantizar que se mantenga información persistente generada o procesada por un pod, y dado que viven y mueren y cada contenedor al interior de cada pod tiene un sistema de archivos propio que se pierde con cada muerte, el concepto de  [Volumen](https://kubernetes.io/docs/concepts/storage/volumes/)  viene al rescate, permitiendo que la información se mantenga en el tiempo

Como se mencionó anteriormente, cada pod tiene su propia dirección IP, y asegurar que un pod siempre tendrá una dirección única no es posible, por tal motivo existen los  [Servicios](https://kubernetes.io/docs/concepts/services-networking/service/), que dan un nivel de abstracción de comunicación entre pods, definiendo puertos nombrados a través de etiquetas.
```
**kind: Service  
apiVersion: v1  
metadata:  
  name: my-service  
spec:  
  selector:  
    app: MyApp  
  ports:  
  - protocol: TCP  
    port: 80  
    targetPort: 9376**
```
Los  [despliegues](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)  son una manera declarativa de especificar pods con replica sets.

Kubernetes es un servicio muy popular y es ofrecido por distintos proveedores de nube como:

-   [Azure Kubernetes Service](https://azure.microsoft.com/es-es/services/kubernetes-service/)
-   [Amazon Kubernetes Service](https://aws.amazon.com/es/eks/)
-   [Google Kubernetes Engine](https://cloud.google.com/kubernetes-engine/)
-   [OpenShift](https://www.openshift.com/)
-   [IBM Kubernetes Service](https://www.ibm.com/cloud/container-service)

Y muchos otros, no es mi deber compararlos a ningún nivel, por el contrario, ilustraré brevemente el proceso de creación de un cluster de K8s con  [Kops](https://github.com/kubernetes/kops), para ello resumimos el  [tutorial](https://kubernetes.io/docs/setup/custom-cloud/kops/)  de la página oficial de k8s.

Como pre requisito se solicita la instalación de  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/), la herramienta de línea de comandos de K8s y por supuesto  [kops](https://github.com/kubernetes/kops/releases).

En  [Route53](https://aws.amazon.com/es/route53/)  creamos una nueva zona alojada y luego creamos un registro registro NS apuntando a la zona recién creada.

Luego creamos un nuevo bucket en  [S3](https://aws.amazon.com/es/s3)  y en nuestra línea de comandos almacenamos una variable de entorno que apunte a este bucket.
```
export KOPS_STATE_STORE=s3://clusters.dev.example.com
```
Recordamos que es recomendable nombrar al bucket de una manera clara porque ahí se almacenará toda la información relacionada con el cluster.

Kops permite configurar primero el cluster sin crearlo, lo cual permite una revisión previa a la creación de recursos
```
kops create cluster --zones=us-east-1c useast1.dev.example.com
```
Para crear efectivamente el cluster
```
kops update cluster --zones=us-east-1c useast1.dev.example.com --yes
```
Con esto podremos ver en nuestra consola de EC2 que se han creado nuevos recursos, grupos de seguridad, y subredes relacionadas con este cluster.

Podemos verificar el proceso con
```
kubectl get nodes
```
El punto de toda esta configuración es tener nuestra aplicaciones manejadas por K8s, y como primera instancia debemos configurar nuestro registro de docker con las credenciales apropiadas. Para esto seguimos la  [guía oficial](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-in-the-cluster-that-holds-your-authorization-token)
```
kubectl create secret docker-registry regcred --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email>
```
A continuación crearemos un despliegue con dos replicas de nuestra aplicación, un contenedor instancia del ejemplo anterior, un puerto de comunicación con otros contenedores.

Llamamos a este archivo  `deployment.yml`
```
apiVersion: apps/v1 kind:  Deployment  
metadata:  
  name: ejemplo-django  
spec:  
  selector:  
    matchLabels:  
      app: ejemplo-django  
  replicas: 2  
  template:  
	  metadata:
		  labels:  
	        app:ejemplo-django  
   spec:  
      containers:
      - name ejemplo-django-container  
        image: contraslash/ejemplo-django:v1  
        ports: 
	        - name: ed-port  
	          containerPort: 8000  
	        env:  
**        - name: DEBUG  
          value: "False"  
  
      imagePullSecrets:
      -  name: regcred
```
Reflejaremos los cambios en nuestro servidor utilizando
```
kubectl apply -f deployment.yml
```
Y verificamos con
```
kubectl get nodes
```
Nuestra aplicación está corriendo en el cluster, pero no tenemos un mecanismo para acceder a ella, para esto usamos un servicio de tipo  [LoadBalancer](https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/)  que definimos en  `service.yml`  en donde definiremos un puerto de que enlace los puertos de nuestro despliegue a una URL que se asignará por medio de  [ELB](https://aws.amazon.com/es/elasticloadbalancing), también le añadiremos un certificado SSL por medio de  [ACM](https://aws.amazon.com/es/certificate-manager/). Una descripción un poco mas precisa de como utilizar certificados SSL con Servicios está en  [este post](https://medium.com/@jkinkead/external-http-https-server-on-kubernetes-in-aws-9b182328fff1).
```
apiVersion: v1  
kind: Service  
metadata:  
  name: ejemplo-django-service  
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-backend-protocol: http  				service.beta.kubernetes.io/aws-load-balancer-ssl-cert: arn:aws:acm:{region}:{user id}:certificate/{id}  service.beta.kubernetes.io/aws-load-balancer-ssl-ports: "https"  
spec:  
  ports: - port: 80  
    name: http  
    targetPort: ed-port  
    protocol: TCP  
  - port: 443  
    name: https  
    targetPort: ed-port  
    protocol: TCP  
  selector:  
    app: ejemplo-django  
  type: LoadBalancer
```
Ejecutamos
```
kubectl apply -f service.yml
```
Verificamos con
```
kubectl get services
```
Y para obtener la URL del balanceador de carga
```
kubectl describe service ejemplo-django-service
```
La url estará en el parámetro  `LoadBalancer Ingress`
