# Tutorial Docker 

En esta ocasión vamos a hablar de una gran herramienta que se usa mucho en el día a día trabajando como desarrollador. Se llama Docker

1.  Introducción
2.  Despliegar un primer contenedor Docker básico en práctica
3.  Crear sus propias imágenes con el archivo  _Dockerfile_
4.  Combinar varias imágenes para construir software complejos con  _Docker-compose_
5. Conclusión 

## 1.Introduccion 


###  1.1¿Qué es Docker?

[Docker](https://www.docker.com/)  es un software gratuito desarrollado por Docker Inc. Se presentó al público general el 13 de marzo de 2013 y desde entonces se ha convertido en algo imprescindible en el mundo del desarrollo.

Permite a los usuarios crear entornos independientes y aislados para lanzar y desplegar sus aplicaciones. A estos entornos se les llama **contenedores.**

Esto permite al desarrollador ejecutar un contenedor en cualquier máquina.

Como puedes ver, con Docker ya no hay problemas de dependencias o compilación. Todo lo que tienes que hacer es lanzar tu contenedor y tu aplicación se lanzará inmediatamente.

### 1.2¿Es Docker una máquina virtual?

Esta es una de las preguntas más frecuentes acerca de Docker. La respuesta es ‘no exactamente’.

Puede parecer una máquina virtual al principio, pero la funcionalidad no es la misma.

A diferencia de Docker, una máquina virtual incluirá un sistema operativo completo. Trabajará de forma independiente y actuará como un ordenador.

Docker sólo comparte los recursos de la máquina anfitriona para ejecutar sus entornos.

### 1.3¿Por qué usar Docker como desarrollador?

Esta herramienta puede cambiar la vida diaria de un desarrollador. Para responder mejor a esta pregunta, he escrito una lista (no exhaustiva) de los beneficios que encontrarás:

-   Docker es rápido. A diferencia de una máquina virtual, tu aplicación arrancará en segundos y se parará igual de rápido.
-   Docker es multiplataforma. Puedes lanzar tu contenedor en cualquier sistema.
-   Los contenedores pueden construirse y destruirse más rápidamente que una máquina virtual.
-   Se acabaron las dificultades para crear tu entorno de trabajo. Una vez que Docker está configurado, nunca tendrás que reinstalar dependencias manualmente. Si cambias de ordenador o llega un nuevo empleado, sólo tienes que darle tu configuración.
-   Mantienes tu espacio de trabajo limpio, ya que tus entornos estarán aislados y puedes borrarlos en cualquier momento sin impactar al resto.
-   Facilita desplegar tu proyecto en tu servidor para ponerlo en línea.
- # Creemos nuestra primera aplicación

Ahora que ya sabes qué es Docker, es el momento de crear tu primera aplicación.

El propósito de este pequeño tutorial es crear una aplicación Python que muestre una frase. Está aplicación se lanzará mediante una fichero Docker (Dockerfile).

Verás que no es complicado una vez que comprendes el proceso.

_Nota: no es necesario instalar Python en tu ordenador. Es tarea del entorno de Docker contener Python para ejecutar tu código._

## 2. Desplegar un primer contenedor Docker

### 2.1Instalación 

-   _Para Ubuntu_

Primero, actualiza paquetes

$ sudo apt update

Después, instala Docker con apt-get

$ sudo apt install docker.io

Finalmente, verifica que Docker se ha instalado correctamente:

$ sudo docker run hello-world

-   _Para MacOSX:_ puedes seguir  [este enlace](https://docs.docker.com/docker-for-mac/install/)  (en inglés).
-   _Para Windows:_ puedes seguir  [este enlace](https://docs.docker.com/docker-for-windows/install/)  (en inglés).

### 2.2 Crea tu proyecto

Para crear tu primera aplicación de Docker, crea una nueva carpeta un tu ordenador. Debe contener estos dos ficheros:

-   Un fichero ‘main.py’ (el fichero python que contiene el código a ejecutar).
-   Un fichero ‘Dockerfile’ (que contiene las instrucciones necesarias para crear el entorno).

Normalmente, la carpeta quedaría así:

.  
├── Dockerfile  
└── main.py  
0 directories, 2 files

### 2.3 Edita el fichero Python

Puedes añadir el siguiente código en el fichero ‘main.py’:

#!/usr/bin/env python3print("Docker es mágico!")

Nada excepcional, pero cuando veas ‘Docker es mágico!’ en tu terminal sabrás que Docker está funcionando.

### 2.4 Edita el fichero Docker

Algo de teoría: lo primero que hay que hacer cuando creamos un Dockerfile es preguntarnos qué queremos hacer. Nuestro objetivo aquí es lanzar el código Python.

Para hacer esto, nuestro Docker debe contener todas las dependencias necesarias para lanzar Python. Un Linux (Ubuntu) con Python instalado debería ser suficiente.

El primer paso para crear un fichero Docker es acceder a la web  [DockerHub](https://hub.docker.com/). Esta página muchas imágenes prediseñadas para ahorrar tiempo (por ejemplo, todas las imágenes de Linux o lenguajes de programación).

En nuestro caso, escribiremos ‘Python’ en la barra de búsqueda. El primer resultado es  [la imagen oficial](https://hub.docker.com/_/python) creada para ejecutar Python. 

### 2.5 Crea la imagen Docker

Una vez que el código y el Dockerfile están listos, todo lo que tienes que hacer es crear la imagen que contiene tu aplicación.

$ docker build -t python-test .

La opción ‘-t’ permite definir el nombre de tu imagen. En nuestro caso, hemos elegido ‘python-test’ pero puedes poner lo que quieras.

### 2.6 Ejecuta la imagen Docker

Una vez que la imagen se ha creado, tu código esta listo para ejecutarse.

$ docker run python-test

Necesitas poner el nombre de tu imagen después de ‘docker run’.

Y eso es todo. Deberías ver ‘Docker es mágico!’ en tu terminal.


##  3. Crear sus propias imágenes con el archivo Dockerfile

### 3.1 ¿Qué es el archivo  _Dockerfile_?

Dockerfile es un archivo de textos que sirve para crear sus propias imágenes, y así configurar el funcionamiento de sus propios softwares.
3.2 Ejemplo: Escribir un Dockerfile

Para crear una imagen Docker, debe crear un archivo  **Dockerfile**. Es un archivo de texto plano con instrucciones y argumentos. Aquí está la descripción de las instrucciones que vamos a usar en nuestro próximo ejemplo:

**FROM**  - establece la imagen base  **RUN**  - ejecuta ciertos comandos al momento de construir la imágen  **ENV**  - define variables de entorno  **WORKDIR**  - define directorio de trabajo  **VOLUME**  - crea un punto de montaje para un volumen  **CMD**  - ejecuta ciertos comandos al momento de despliegar un contenedor

Puede consultar la referencia de  [Dockerfile](https://docs.docker.com/engine/reference/builder/)  para obtener más detalles.

Vamos a crear una imagen que obtendrá el contenido del sitio web con el comando  **curl**  y lo almacenará en un archivo de texto. Necesitamos pasar la URL del sitio web a través de la variable de entorno SITE_URL. El archivo resultante se colocará en un directorio, montado como un volumen.

Coloque el nombre de archivo Dockerfile en el directorio de ejemplos/curl con el siguiente contenido:

```
FROM ubuntu:latest  
RUN apt-get update   
    && apt-get install --no-install-recommends --no-install-suggests -y curl 
    && rm -rf /var/lib/apt/lists/*
ENV SITE_URL http://example.com/  
WORKDIR /data  
VOLUME /data  
CMD sh -c "curl -Lk $SITE_URL > /data/results"  

```

El archivo Dockerfile está listo. Es hora de construir la imagen real.

Vaya al directorio examples/curl y ejecute el siguiente comando para construir una imagen:

```
docker build . -t test-curl  

```

Salida del terminal:

```
Sending build context to Docker daemon  3.584kB  
Step 1/6 : FROM ubuntu:latest  
 ---> 113a43faa138
Step 2/6 : RUN apt-get update     && apt-get install --no-install-recommends --no-install-suggests -y curl     && rm -rf /var/lib/apt/lists/*  
 ---> Running in ccc047efe3c7
Get:1 http://archive.ubuntu.com/ubuntu bionic InRelease [242 kB]  
Get:2 http://security.ubuntu.com/ubuntu bionic-security InRelease [83.2 kB]  
...
Removing intermediate container ccc047efe3c7  
 ---> 8d10d8dd4e2d
Step 3/6 : ENV SITE_URL http://example.com/  
 ---> Running in 7688364ef33f
Removing intermediate container 7688364ef33f  
 ---> c71f04bdf39d
Step 4/6 : WORKDIR /data  
Removing intermediate container 96b1b6817779  
 ---> 1ee38cca19a5
Step 5/6 : VOLUME /data  
 ---> Running in ce2c3f68dbbb
Removing intermediate container ce2c3f68dbbb  
 ---> f499e78756be
Step 6/6 : CMD sh -c "curl -Lk $SITE_URL > /data/results"  
 ---> Running in 834589c1ac03
Removing intermediate container 834589c1ac03  
 ---> 4b79e12b5c1d
Successfully built 4b79e12b5c1d  
Successfully tagged test-curl:latest  


```

-   **docker build**  construye una nueva imagen localmente.
-   **-t**  establece la etiqueta de nombre en una imagen.

Ahora tenemos la nueva imagen, y podemos verla en la lista de imágenes existentes:

```
docker images  

```

Salida del terminal:

```
REPOSITORY  TAG     IMAGE ID      CREATED         SIZE  
test-curl   latest  5ebb2a65d771  37 minutes ago  180 MB  
nginx       latest  6b914bbcb89e  7 days ago      182 MB  
ubuntu      latest  0ef2e08ed3fa  8 days ago      130 MB  

```

Podemos crear y ejecutar el contenedor desde la imagen. Vamos a intentarlo con los parámetros por defecto:

```
docker run --rm -v $(pwd)/vol:/data/:rw test-curl  

```

Para ver los resultados en el archivo:

```
cat ./vol/results  

```

Probemos definiendo la variable de entorno, tomando un sitio real como ejemplo (por ejemplo Facebook.com):

```
docker run --rm -e SITE_URL=https://facebook.com/ -v $(pwd)/vol:/data/:rw test-curl   

```

Ver los resultados:

```
cat ./vol/results  

```
## 4. Combinar varias imágenes para construir software complejos con Docker-compose

###  Despliegue de una aplicación web Python en Docker
En esta entrada vamos a ver cómo desplegar una aplicación web desarrollada en Python, en este caso usando el framework Flask, dentro de un contenedor Docker con un servidor uWSGI.

### Aplicación web Python “Hola mundo”

La aplicación web Python que vamos a utilizar es extremadamente sencilla. Se trata de la típica aplicación “Hola mundo” que en este caso está desarrollada con  [Flask](http://flask.pocoo.org/).

El funcionamiento sería exactamente el mismo si la aplicación fuera más compleja o en lugar de Flask se hubiera utilizado otro framework, como  [Django](https://www.djangoproject.com/), para su desarrollo.

#### webapp.py
```

from flask import Flask

app  =  Flask(__name__)

@app.route("/")

def hello():

return  "Hello World!"

if  __name__  ==  "__main___":

app.run()
```
#### ¿Qué es WSGI?

WSGI (Web Server Gateway Interface) es un protocolo para que los servidores web como Nginx, lighttpd o Cherokee envíen peticiones a aplicaciones web desarrolladas en Python.

Para poder ejecutar una aplicación WSGI lo primero que se necesita es un servidor WSGI. WSGI es tanto un protocolo como un servidor de aplicaciones. El servidor de aplicaciones puede servir a los protocolos WSGI, FastCGI y HTTP.

#### El servidor uWSGI

Existen varios  [servidores WSGI](http://flask.pocoo.org/docs/1.0/deploying/wsgi-standalone/#deploying-wsgi-standalone%EF%BB%BF)  como Gunicorn,  [uWSGI](https://uwsgi-docs.readthedocs.io/en/latest/), Gevento o Twisted Web. Para este laboratorio he optado por uWSGI.

uWSGI es un servidor de aplicaciones escrito en C, rápido y muy configurable

#### Construcción de nuestra imagen con uWSGI

Como ya hemos comentado, para poder ejecutar nuestra aplicación web Python necesitaremos un servidor de aplicaciones WSGI como uWSGI. Por lo tanto, lo que vamos a hacer es construir una imagen Docker que contenga el servidor uWSGI y ejecutar nuestra aplicación web Python en él.

#### Dockerfile

```
FROM python:3.7.3

LABEL maintainer="Jose Arturo Fernandez <jarfernandez@aprenderdevops.com>"

# Se instala uWSGI y todas las librerias que necesita la aplicacion

COPY WebApp/requirements.txt requirements.txt

RUN pip install uwsgi  &&  pip install  -r  requirements.txt

# Puerto HTTP por defecto para uWSGI

ARG UWSGI_HTTP_PORT=8000

ENV UWSGI_HTTP_PORT=$UWSGI_HTTP_PORT

# Aplicacion por defecto para uWSGI

ARG UWSGI_APP=webapp

ENV UWSGI_APP=$UWSGI_APP

# Se crea un usuario para arrancar uWSGI

RUN useradd  -ms  /bin/bash admin

USER admin

# Se copia el contenido de la aplicacion

COPY WebApp  /WebApp

# Se copia el fichero con la configuración de uWSGI

COPY uwsgi.ini uwsgi.ini

# Se establece el directorio de trabajo

WORKDIR  /WebApp

# Se crea un volume con el contenido de la aplicacion

VOLUME  /WebApp

# Se inicia uWSGI

ENTRYPOINT  ["uwsgi",  "--ini",  "/uwsgi.ini"]

```
A continuación, explico las distintas líneas del Dockerfile:

-   En la línea 1 se indica la imagen base de la que se parte para construir la nueva imagen. En este caso partimos de la imagen oficial de Python en su versión 3.7.3.
-   En la línea 5 se copia el fichero requirements.txt que contiene las librerías Python necesarias para ejecutar la aplicación.
-   En la línea 6 se instala el servidor uWSGI y las librerías incluidas en el fichero requirements.txt. Para la instalación se utiliza pip. pip es un sistema de gestión de paquetes utilizado para instalar y administrar paquetes software escritos en Python.
-   En la línea 9 se define la variable UWSGI_HTTP_PORT. Esta variable puede ser modificada al hacer docker build con el flag –build-arg. Esta variable contendrá el puerto HTTP por defecto en el que escucha el servidor uWSGI.
-   En la línea 10 se define la variable de entorno UWSGI_HTTP_PORT que será igual al valor de la variable definida en la línea 9.
-   En las líneas 13 y 14 hacemos lo mismo que en las líneas 9 y 10, pero en este caso para la variable de entornos UWSGI_APP. Esta variable contendrá el nombre del fichero Python en el que se declara la aplicación que ejecutará el servidor uWSGI.
-   En la línea 17 se crea el usuario admin.
-   En la línea 18 se cambia la ejecución del proceso uwsgi al usuario admin, ya que puede ejecutarse sin privilegios, lo que proporciona mayor seguridad.
-   En la línea 21 se copia el contenido de la aplicación.
-   En la línea 24 se copia el fichero de configuración del servidor uWSGI.
-   En la línea 27 se establece el directorio de trabajo que será el directorio en el que se encuentra la aplicación (/WebApp).
-   En la línea 30 se crea un volumen para compartir el contenido de la aplicación desde el host al contenedor. De esta forma, si se modifica el código fuente de la aplicación en el host, los cambios se harán efectivos también dentro del directorio WebApp del contenedor. Para que el servidor uWSGI los refleje será necesario reiniciar el contenedor con docker restart.
-   En la línea 33 se inicia el servidor uWSGI. Se pasa como parámetro el fichero con la configuración que previamente se ha copiado en la línea 24.

#### requirements.txt
```

Click==7.0

Flask==1.1.1

itsdangerous==1.1.0

Jinja2==2.10.1

MarkupSafe==1.1.1

Werkzeug==0.15.5
```
El fichero requirements.txt contiene las librerías Python necesarias para ejecutar la aplicación. Este fichero se obtiene con el siguiente comando:

$  pip freeze  >  requirements.txt

#### uwsgi.ini
```
[uwsgi]

http  =  0.0.0.0:$(UWSGI_HTTP_PORT)

module  =  $(UWSGI_APP):app
```

El fichero uwsgi.ini contiene la configuración del servidor uWSGI.

En la línea 2 se indica la dirección IP y el puerto en el que el servidor uWSGI escuchará peticiones HTTP. La dirección establecida a 0.0.0.0 hará que se escuche por todas las interfaces de red.

En el caso del puerto, se especifica que se obtenga el valor de la variable de entorno UWSGI_HTTP_PORT. Si esta variable no se establece en la ejecución del contenedor con el flag –env o -e se le asignará el valor por defecto (8000).

El valor por defecto del puerto se puede cambiar al construir la imagen con docker build especificando otro valor. Por ejemplo, si queremos que el valor por defecto del puerto para la imagen sea el 9000, ejecutaremos el siguiente comando para construir la imagen:

$  docker build  --build-arg UWSGI_HTTP_PORT=9000  -t  aprenderdevops/uwsgi  .

Esta configuración se define en las líneas 9 y 10 del Dockerfile.

En la línea 3 se especifica el módulo a ejecutar por el servidor uWSGI. Se especifica que se obtenga el valor de la variable de entorno UWSGI_APP. Si esta variable no se establece en la ejecución del contenedor con el flag –env o -e se le asignará el valor por defecto (webapp).

Al igual que en el caso del puerto, el valor por defecto del módulo se puede cambiar al construir la imagen con docker build especificando otro valor mediante el flag –build-arg. Esta configuración se define en las líneas 13 y 14 del Dockerfile.

La utilización de variables de entorno que se pasan al fichero de configuración del servidor uWSGI nos proporciona mucha flexibilidad a la hora de utilizar el contenedor, ya que se puede iniciar el servidor uWSGI en el puerto que deseemos, siempre que no esté ya ocupado, y lo que es más importante, se puede cambiar el nombre del fichero Python en el que se declara la aplicación que ejecutará el servidor uWSGI.

#### Instrucciones

Para construir la imagen ejecutamos el siguiente comando:

$  docker build  -t  aprenderdevops/uwsgi  .

Para arrancar el contenedor ejecutamos el siguiente comando:

$  docker run  -d  -p  8080:8000  --restart unless-stopped  -v  $(pwd)/WebApp:/WebApp aprenderdevops/uwsgi

Con el flag -p indicamos que se rediriga el puerto 8000 del contenedor (puerto por defecto en el que escucha el servidor uWSGI) al puerto 8080 de nuestra máquina. Si el puerto 8080 estuviera ocupado por otro proceso habría que cambiarlo por otro.

Una vez arrancado el contenedor, abrimos un navegador web y accedemos a [http://localhost:8080](http://localhost:8080/). Nos debería abrir una página web con el texto “Hello World!”.

## Conclusion 

Docker se ha convertido en una herramienta importante de desarrollo de software, ademas se puede utilizar en todo tipo de proyectos independientemente de su tamaño y complejidad.
La encapsulación de su código en contenedores Docker le ayudará a crear procesos de integración más rápidos y eficientes y no hay persistencia de datos en los contenedores.


