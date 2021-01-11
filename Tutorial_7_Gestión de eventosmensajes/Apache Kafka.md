
# Gestión de eventos/mensajes (Apache Kafka)

## 1. Introducción General

Hoy en día cualquier empresa que se considere "seria" se mueve y evoluciona gracias a los datos que maneja.

**¿A qué nos referimos cuando hablamos de datos?**

Los  **datos**  (_un dato es la mínima unidad semántica_) no son más que un  **conjunto discreto de valores**  sobre las "cosas" con las que trabajamos y que deberían de tener una  **serie de propiedades**  concretas:

-   _Completitud_
-   _Precisión_
-   _Consistencia (no lleven a contradicciones)_
-   _Unicidad_
-   _Temporalidad_

_Por ejemplo: el identificador utilizado para diferenciar un elemento del resto, la cantidad de unidades de un elemento, la fecha de entrega, el estado de un elemento, el nº de teléfono, etc._

> **Ayuda:**  Con el cumplimiento de la mayor parte de las anteriores propiedades por cada dato se consigue que su calidad sea mejor.

Hay que tener en cuenta que  **los datos inicialmente y por si solos no aportan mayor valor a la toma de decisiones**, simplemente muestran el aspecto al que representan (por ejemplo: el valor "7" sabemos que representa un nº entero, pero no conocemos nada más), es decir, no están preparados para explicar el porqué de las cosas, pero el  **análisis e interpretación**  de sus valores en conjunto y conociendo el contexto/utilidad  **aporta**  lo que se conoce como  **información**  (_conjunto de datos procesados que tienen un significado y que son de utilidad a la hora de tomar decisiones_) y como bien se sabe actualmente  **la información es poder**.

Hay que diferenciar: datos, información y conocimiento:

-   **Datos**  = Conjunto discreto de valores
-   **Información**  =  **Datos**  + Contexto + Utilidad
-   **Conocimiento**  =  **Información**  + Experiencia + Otros

_Por ejemplo: el valor "7" se puede entender de diversas maneras: como la cantidad de elementos de algo, como un valor de una tipología, como un identificador de un objeto, etc._

_Por ejemplo: una fecha sobre un elemento se puede corresponder a su "creación", su "modificación", su "devolución", etc. Depende del ámbito que representen, así podríamos ver que si varios elementos tiene muchas fechas de "devolución" es que algo no está funcionando para ese elemento pero para ello se requiere ubicarlo en un contexto de tiempo_.

Por lo tanto,  **disponer**  de los  **datos correctos**, con la  **calidad requerida**, en los  **sitios y momentos necesarios**  pasa a ser una  **prioridad crítica**  para  **cualquier compañía**  que quiera resultar competitiva hoy en día.

Hay una cita muy interesante que dice que  **"Cada byte de un dato tiene una historia muy interesante que contar"**  y al final va a tener toda la razón ;-).

> _Nota: todo esto es lo que se denomina  **pipeline del manejo de datos**  que se englobaría dentro del  **gobierno de datos**._

**¿Qué ocurre al utilizar cualquier aplicación?**

Cualquier persona que haya utilizado alguna  **aplicación**  en su vida sabe que usarla implica  **crear datos**  de alguna manera (voluntariamente o involuntariamente) en algún momento (como poco a lo mejor guarda el usuario y password para que se pueda volver a entrar :-)).

Algunas de las  **formas de almacenar la información**  más típicas son:

-   _Generar un fichero_
-   _Generar una entrada/registro en base de datos_

Estos datos  **aportan el valor necesario**  a la  **funcionalidad**  que cubren y se encuentran  **disponibles para su explotación**  de forma independiente o bien son datos consumidos por otro conjunto de aplicaciones: accediendo a la misma base de datos, lectura de ficheros, creación de ficheros de intercambio "intermedios", peticiones REST, etc.

Con esto podemos ser conscientes de que constantemente a la hora de utilizar aplicaciones se están produciendo las siguientes  **actividades**:

-   **_Obtener_** _la información desde diferentes orígenes (suelen ser muy variados y pueden requerir de diferentes mecanismos)._
-   **_Manipular_** _la información para adaptarla a las diferentes necesidades (a veces se requiere la ejecución de operativas para dejar la información en el formato y/o con el contenido requerido para poder utilizarlo)._
-   **_Analizar_** _la información con diferentes procedimientos y criterios._
-   **_Generar_** _la información de salida en base a lo anterior (para ayudar a esa toma de decisiones, aportar valor o bien servir de entrada para otra aplicación)._
-   **_Mover_** _la información entre localizaciones o bien entre aplicaciones (a veces utilizando la carga masiva entre sistemas, otras veces)._

De lo que se puede extraer que:  _"casi es tan importante la forma de obtener el dato, su manipulación y la forma que tenemos de moverlo (intercambiarlo) para su consumo."_

## 2. Patrón "Publish / Subscribe Messaging"

Antes de "meternos en harina" conviene aclarar este concepto para no generar ninguna duda a la hora entender cómo funciona Apache Kafka por dentro.

**¿Qué es?**

**Patrón de uso**  dentro de la tipología de  **arquitectura de "Cola de mensajes"**, utilizado para la  **comunicación entre aplicaciones**.

Por lo tanto, se engloba más en el  **movimiento de información**, aunque también puede cumplir aspectos de preparación o modificación.

También se denomina "publish / subscribe", "publicador / suscriptor" o "productor / consumidor".

**¿Cómo funciona?**

Existe un elemento  **"publisher"**  (publicador / remitente / emisor / productor) que al  **generar un dato**  (message / mensaje / record / registro)  **no**  lo  **dirige**  o referencia específicamente a un  **"subscriber"**  (receptor / subscriptor / suscriptor) en concreto, es decir, no lo envía de forma directa a la "dirección" del subscriber.

Para ello se  **dispone**  de  **listas de temas/topics**  publicados específicos y un  **conjunto de suscriptores**, el productor trata de clasificar el mensaje en base a una tipología, lo pone en la lista de un tema específico y el receptor se suscribe a la listas para recibir ese tipo de mensajes.

Para ayudar en su comprensión se van a mostrar varios diagramas conceptuales:

-   **Diagrama Conceptual:**  "Un único tema/topic y dos suscriptores"  ![](https://enmilocalfunciona.io/content/images/2018/08/PublishSubscribe.png)
    
-   **Diagrama Conceptual:**  "Dos temas/topics y un suscriptor por tema/topic"  ![](https://enmilocalfunciona.io/content/images/2018/08/PublishSubscribe2.png)
    
-   **Diagrama Conceptual:**  "Varios temas/topics y un suscriptor con varios temas/topics específicos"  ![](https://enmilocalfunciona.io/content/images/2018/08/PublishSubscribe3.png)
    
-   **Diagrama Conceptual:**  "Varios temas/topics y uno de ellos es compartido por varios suscriptores"  ![](https://enmilocalfunciona.io/content/images/2018/08/PublishSubscribe4.png)
    

**Detalles**

-   Este tipo de comunicación se utiliza sobre todo para la  **comunicación asíncrona.**
    
-   Permite  **diferentes tipos de configuración:**
    
    -   **Tradicional:**  Cada suscriptor está asociado a uno o varios topic en concreto. Existen muchas variaciones:
        -   Cada suscriptor está escuchando 1 topic propio.
        -   Cada suscriptor está escuchando X topics independientes.
        -   Cada suscriptor está escuchando X topics independientes y Y topics compartido.
    -   **Grupos de consumo:**  Los suscriptores se pueden agrupar por grupo, este grupo está escuchando un topic y sólo un miembro del grupo tendrá la capacidad de atender el mensaje.
    -   **Radio Difusión:**  Todos los suscriptores que están escuchando el topic reciben el mensaje (cada suscriptor es responsable de interpretar el mensaje de forma independiente).
-   Requiere de otra pieza que sirve de intermediario (broker) donde se publican los topics.
    
-   Proporciona un sistema centralizado que permite la publicación de tipos genéricos de datos y que puede evolucionar con el tiempo.
    

**Consideraciones**

A la hora de realizar una valoración de este tipo de enfoques habría que tener en cuenta los siguientes aspectos:

-   **Precisión (Correctness)**: Debería de definir alguno o una combinación de las siguientes estrategias (con/sin duplicados, con/sin orden y con/sin perdida de datos) que se encargan de establecer garantías de entrega y de ordenación
    -   Estrategias de  **entrega**:
        -   **"como máximo una vez" (at most once):**  Sólo se envía una vez, garantiza que no hay duplicados pero puede perderse.
        -   **"al menos una vez" (at least once):**  Garantiza que no hay perdida.
        -   **"exactamente una vez" (exactly once):**  Garantiza que no hay duplicados y que tampoco hay perdidas.
    -   Estrategias de  **ordenación**:
        -   **No hay ordenación (No ordering):**  No importa el orden de recepción y por lo tanto se puede incrementar el rendimiento.
        -   **Ordenación por partición (Partitioned ordering):**  Asegurar un orden por partición y por lo tanto tiene un coste extra.
        -   **Ordenación Global (Global Order):**  Se requiere un orden en los datos por lo tanto necesita un mayor coste extra de los recuross y posibles implicaciones en el rendimiento.
-   **Disponibilidad (Availability)**: Capacidad para estar el mayor tiempo posible trabajando o activo. En este punto hay que tener en cuenta los mantenimientos y la aparición de errores (detección + reparación).
-   **Transaccionalidad (Transaction)**: Capacidad para agrupar acciones o elementos en unidades atómicas. Es decir, "O se ejecutan todas como una única operación o bien no se ejecuta ninguna".
-   **Escalabilidad (Scalability)**: Capacidad de un elemento para evolucionar en un aspecto con el objetivo de poder dar soporte a una mayor cantidad de acciones o elementos -> Pueden existir diferentes dimensiones: mensajes, topics, productores o consumidores
    -   El objetivo es manejar la cantidad de tráfico.
    -   Suele entrar en conflicto con la eficiencia (filtrado complejo, enrutado complicado, etc. suele ser no escalable).
-   **Rendimiento (Throughput)**: Capacidad relacionada con la medida de la eficiencia en lo referente a la cantidad (nº de bytes) de elementos por unidad de tiempo con los que puede trabajar -> En algunos casos también se denomina ancho de banda (bandwidth).
    -   Se engloba dentro del concepto de "eficiencia"
-   **Latencia (Latency)**: Capacidad relacionada con la medida de la eficiencia basada en el pipeline requerido para su procesamiento, es decir, se suele considerar el tiempo requerido para procesar un elemento -> En algunos casos también se denomina tiempo de respuesta.
    -   Se engloba dentro del concepto de "eficiencia".
    -   Suele ser inversamente proporcional respecto al rendimiento
-   **Desacoplamiento (Decoupling)**: Capacidad que hace referencia a diferentes aspectos:
    -   **Entidad (Entity)**: Los clientes (productor y consumidores) no necesitan conocerse entre si
    -   **Tiempo (Time)**: Los clientes (productor y consumidores) no necesitan participar activamente
    -   **Sincronización (Synchronization)**: Si los hilos de ejecución o los clientes requieren algún tipo de bloqueo síncrono
-   **Enrutamiento Lógico (Routing logic)**: Capacidad por la que un dato que sale de un productor acaba en un consumidor concreto. Sería aplicar la lógica de negocio del caso de uso y se puede enfocar en base a:
    -   **Tema (Topic)**: Los datos se clasifican en temas y los suscriptores sólo reciben datos relacionados con esos temas. Una variante de este tema sería la especialización de los temas en base al tipo de objeto.
    -   **Contenidos (Content)**: Los datos se clasifican en base a uno o varias propiedades y los suscriptores puede establecer filtros o restricciones específicas sobre un grupo de datos.

## 3. Apache Kafka

[https://kafka.apache.org/](https://kafka.apache.org/)

La plataforma de Apache Kafka se trata de un sistema de mensajes  **"publish / subscribe"**  Open Source basado en una arquitectura P2P (arquitectura Peer to Peer).

Se entiende como un  **commit log**  que es  **_"log (registro) de commit (confirmación) distribuido"_**.

Un "commit log" tiene las siguientes  **características**:

-   _Estructura de datos ordenada y persistente._
-   _Es el core de Kafka._
-   _Sólo permite añadir por el final (anexar)._
-   _No se pueden modificar ni borrar registros._
    -   _Se pueden invalidar valores según ciertos criterios._
-   _Proporciona un procesamiento determinista._

Mejora la forma de trabajo con los datos en las aplicaciones a la hora de realizar comunicaciones y/o procesamientos con ellos.

Para ello proporciona un enfoque de  **bus de mensajes**  (con productores y consumidores que usan listas para trabajar con mensajes).

Destaca por:

-   _Plataforma para la nueva generación de aplicaciones distribuidas._
-   _Tiene mucha popularidad en la actualidad (muchas grandes empresas lo tienen incorporado en sus arquitecturas: Netflix, Microsoft, Bancos, aseguradoras, empresas ticketing, etc)._
-   _Tiene una orientación hacia el mundo "Big Data" aunque también destaca en otros ámbitos como la comunicación de aplicaciones, la ejecución de procesos batch, etc._
-   _Está escrito en Java y Scala._
-   _Fue desarrollado en sus inicios como herramienta de "apoyo" en LinkedIn pero posteriormente se hizo Open Source (Apache Community)._
-   _El nombre no tiene ninguna referencia "oculta" hacía el escritor más que la propia elección del nombre._
-   _Pertenece al ámbito "Distributed Messaging Queue"._
-   _Alternativa a JMS, AMQP y RabbitMQ cuando se maneja volúmenes importantes de datos/información y se requiere un gran capacidad de respuesta -> mejora sus características más importantes._
-   _Facilita el trabajo con otras tecnologías como: Flume / Flafka, Spark Streaming, Storm, HBase, Flink y Spark para ingerir, analizar y procesar datos en tiempo real._
    -   _Aplicaciones online: Apache Solr, Logstar._
    -   _Procesamiento streams: SAMZa, Storm, Flink, Wallarro._
    -   _Procesamiento offline: Hadoop._
-   _Dispone de un API de "Producer": Facilita que una aplicación publique una secuencia de mensajes en uno o más topics de diferentes formas._
-   _Dispone de un API de "Consumer": Facilita que una aplicación pueda suscribirse a uno o más topics y así poder procesar la secuencia de mensajes._
-   _Dispone de un API de "Streams": Facilita procesar un flujo consumiendo un flujo de entrada de uno o más topics y produciendo un flujo para uno o más topics de salida._
-   _Dispone de un API de "Connector": Facilita implementar/ejecutar productores o consumidores reutilizables con el objetivo de conectar topics con aplicaciones o sistemas de datos existentes._

## Tutorial para instalar Kafka, ZooKeeper y Java

En el anterior apartado de este tutorial de Kafka, explicamos los componentes de software necesarios para utilizarlo. A menos que ya lo tengas configurado en tu sistema, lo mejor es comenzar instalando  **Java Runtime Environment**. Muchas versiones recientes de las distribuciones de Linux, como  _Ubuntu_, el sistema operativo que nos sirve de ejemplo en este tutorial de Apache Kafka (versión 17.10), ya incluyen una implementación gratuita del JDK (Java Develompent Kit) en su  **repositorio de paquetes oficial**, llamada OpenJDK. Para instalar fácilmente el kit de Java a través de esta implementación, introduce el siguiente comando en el terminal:

```mixed
sudo apt-get install openjdk-8-jdk
```

Una vez hayas instalado Java, haz lo mismo con el servicio de sincronización de procesos  **_Apache ZooKeeper_**. El directorio de paquetes de Ubuntu también contiene un paquete listo para utilizar en este caso, que se ejecuta con el siguiente comando:

```mixed
sudo apt-get install zookeeperd
```

Con este otro comando, puedes verificar si el  **servicio de ZooKeeper está activo**:

```mixed
sudo systemctl status zookeeper
```

Si Apache ZooKeeper se está ejecutando, tendrías que ver algo así:

[![Terminal de Ubuntu: comentario del estado del servicio de ZooKeeper](https://www.ionos.es/digitalguide/fileadmin/DigitalGuide/Screenshots_2019/kafka-tutorial-1.jpg "Terminal de Ubuntu: comentario del estado del servicio de ZooKeeper")](https://www.ionos.es/digitalguide/fileadmin/DigitalGuide/Screenshots_2019/kafka-tutorial-1.jpg)

En la entrada «Active» puedes averiguar si ZooKeeper está activo y desde cuando

Si el servicio de sincronización no se está ejecutando, puedes iniciarlo en cualquier momento con este comando:

```mixed
sudo systemctl start zookeeper
```

A continuación, para asegurarte de que ZooKeeper se ejecute automáticamente cada vez que inicies el sistema, introduce un comando de inicio automático:

```mixed
sudo systemctl enable zookeeper
```

Finalmente, tendrás que crear un  **perfil de usuario de Kafka**  para volver a utilizar el servidor más adelante. Para ello, vuelve a abrir el terminal y escribe el siguiente comando:

```mixed
sudo useradd kafka -m
```

Mediante el administrador de contraseñas _passwd_, puedes asignar al usuario la contraseña que desees, escribiendo primero el comando y luego la contraseña:

```mixed
sudo passwd kafka
```

En el siguiente paso, le concederás derechos sudo al usuario «kafka»:

```mixed
sudo adduser kafka sudo
```

Con el perfil de usuario que acabas de crear, puedes iniciar sesión en cualquier momento:

```mixed
su &#x2013; kafka
```

Llegados a este punto del tutorial, ya podemos descargar e instalar Kafka. Existen muchas fuentes de descarga fiables que ofrecen versiones actuales y anteriores de este software de procesamiento de flujos. Por ejemplo, puedes obtener los  **archivos de instalación**  de primera mano en el  [directorio de descargas de Apache Software Foundation](http://www.apache.org/dist/kafka/ "Directorio de Kafka en apache.org"). Te recomendamos disponer de una  **versión actualizada de Kafka**, por lo que, al escribir el siguiente comando en el terminal, quizás tengas que adaptarlo a la nueva versión:

```mixed
wget http://www.apache.org/dist/kafka/2.1.0/kafka_2.12-2.1.0.tgz
```

El siguiente paso es descomprimir el archivo comprimido que te has descargado:

```mixed
sudo tar xvzf kafka_2.12-2.1.0.tgz --strip 1
```

Utiliza el parámetro «--strip 1» para asegurarte de que los archivos extraídos se almacenan directamente en el directorio «**~/Kafka**». De lo contrario, Ubuntu pondría todos los archivos en el directorio «**~/kafka/kafka_2.12-2.1.0**», según la versión utilizada en este tutorial de Kafka. El requisito es que hayas creado previamente un directorio llamado «Kafka» mediante mkdir y lo hayas cambiado con «cd Kafka».

## Kafka: tutorial para configurar el sistema de transmisión y mensajería

Ahora que has instalado Apache Kafka, Java Runtime Environment y ZooKeeper, en principio podrás ejecutar el servicio de Kafka en cualquier momento. Sin embargo, antes de hacerlo, debes llevar a cabo unas pequeñas  **configuraciones**  para que el software ejecute todas las tareas de manera óptima en el futuro.

### Desbloquear la eliminación de topics

En su  **configuración predeterminada**, Kafka  **no permite eliminar topics, es decir, las unidades de almacenamiento y categorización de un clúster de Kafka**, aunque esto puede modificarse fácilmente mediante el archivo de configuración  _server.properties_. Para abrir este archivo, que se encuentra en la carpeta «config», introduce el siguiente comando en el editor de texto  _nano_  estándar:

```mixed
sudo nano ~/kafka/config/server.properties
```

Después de este archivo de configuración, introduce una nueva entrada que  **permita eliminar los topics de Kafka**:

```mixed
delete.topic.enable=true
```

[![Archivo de configuración server.properties de Kafka en el editor nano](https://www.ionos.es/digitalguide/fileadmin/DigitalGuide/Screenshots_2019/kafka-tutorial-2.jpg "Archivo de configuración server.properties de Kafka en el editor nano")](https://www.ionos.es/digitalguide/fileadmin/DigitalGuide/Screenshots_2019/kafka-tutorial-2.jpg)

En el archivo de configuración también puedes modificar otros elementos, como el puerto TCP (predeterminado: 2181), que se utiliza para ejecutar ZooKeeper

Consejo

No te olvides de  **guardar**  la nueva entrada en el archivo de configuración de Kafka antes de cerrar el editor nano.

### Crear archivos .service para ZooKeeper y Kafka

El siguiente paso de este tutorial de Kafka es crear archivos Unit para ZooKeeper y Kafka que permitan realizar  **acciones habituales**  como iniciar, detener o reiniciar ambos servicios en consonancia con otros servicios de Linux. Para ello, es necesario crear y configurar los archivos  _.service_  para el administrador de sesiones  _systemd_  para ambas aplicaciones.

#### Cómo crear un archivo de ZooKeeper para el administrador de sesiones systemd de Ubuntu

Primero, crea el archivo para el servicio de sincronización de ZooKeeper introduciendo el siguiente comando en el terminal:

```mixed
sudo nano /etc/systemd/system/zookeeper.service
```

Con esto no solo crearás el archivo, sino que también lo abrirás en el editor nano. Introduce las siguientes líneas y, luego, guarda el archivo:

```mixed
[Unit]
Requires=network.target remote-fs.target
After=network.target remote-fs.target
[Service]
Type=simple
User=kafka
ExecStart=/home/kafka/kafka/bin/zookeeper-server-start.sh /home/kafka/kafka/config/zookeeper.properties
ExecStop=/home/kafka/kafka/bin/zookeeper-server-stop.sh
Restart=on-abnormal
[Install]
WantedBy=multi-user.target
```

Como resultado,  _systemd_  entenderá que ZooKeeper no puede iniciarse hasta que la  **red**  y el  **sistema de archivos**  estén  **listos**, como se define en la sección [Unit]. En [Service] se especifica que el administrador de sesión debe utilizar los archivos  _zookeeper-server-start.sh_  y  _zookeeper-server-stop.sh_  para  **iniciar**  y  **detener****_ZooKeeper_**. Además, se define un  **reinicio automático**  para los casos en los que el servicio se detenga de improviso. La entrada [Install] regula cuándo se inicia el archivo, estableciendo «multi-user.target» como valor predeterminado para un sistema multiusuario (por ejemplo, un servidor).

#### Cómo crear un archivo de Kafka para el administrador de sesiones systemd de Ubuntu

El archivo  _.service_  de  _Apache Kafka_  se puede crear escribiendo el siguiente comando en el terminal:

```mixed
sudo nano /etc/systemd/system/kafka.service
```

En el nuevo archivo que se abrirá en el editor nano, copia el siguiente contenido:

```mixed
[Unit]
Requires=zookeeper.service
After=zookeeper.service
[Service]
Type=simple
User=kafka
ExecStart=/bin/sh -c '/home/kafka/kafka/bin/kafka-server-start.sh /home/kafka/kafka/config/server.properties > /home/kafka/kafka/kafka.log 2>&1'
ExecStop=/home/kafka/kafka/bin/kafka-server-stop.sh
Restart=on-abnormal
[Install]
WantedBy=multi-user.target
```

En la sección [Unit] de este archivo, se especifica que  **el servicio de  _Kafka_  depende de  _ZooKeeper_**, lo que asegura que el servicio de sincronización también se inicie cuando se ejecute el archivo  _kafka.service_. Bajo [Service], se introducen los archivos del shell  _kafka-server-start.sh_  y  _kafka-server-stop.sh_  para  **iniciar**  o  **detener**  el  **servidor de Kafka**. En este archivo también se especifica el  **reinicio automático**  en caso de caída de la conexión, así como la entrada referente al **sistema multiusuario**.

[![Terminal de Ubuntu: el archivo kafka.service en el editor nano](https://www.ionos.es/digitalguide/fileadmin/DigitalGuide/Screenshots_2019/kafka-tutorial-3.jpg "Terminal de Ubuntu: el archivo kafka.service en el editor nano")](https://www.ionos.es/digitalguide/fileadmin/DigitalGuide/Screenshots_2019/kafka-tutorial-3.jpg)

Con comandos como ExecStart se especifican las órdenes para este servicio. En los archivos .service, se indican con mayúsculas (al principio y en medio de la palabra).

### Kafka: primer arranque y creación de una entrada de inicio automático

Una vez hayas creado con éxito las entradas del administrador de sesiones para  _Kafka_  y  _ZooKeeper_, puedes iniciar Kafka de la siguiente manera:

```mixed
sudo systemctl start kafka
```

De forma predeterminada, el programa  _systemd_  utiliza un protocolo central o journal en el que todos los  **mensajes de registro**  se escriben automáticamente. Gracias a esta característica, te será fácil comprobar si el servidor Kafka se ha iniciado como deseas:

```mixed
sudo journalctl -u kafka
```

El  **output**  debería tener este aspecto:

[![Terminal de Ubuntu: extracto del journal systemd](https://www.ionos.es/digitalguide/fileadmin/DigitalGuide/Screenshots_2019/kafka-tutorial-4.jpg "Terminal de Ubuntu: extracto del journal systemd")](https://www.ionos.es/digitalguide/fileadmin/DigitalGuide/Screenshots_2019/kafka-tutorial-4.jpg)

El administrador de sesiones systemd ha sido parte integrante del sistema operativo Linux desde Ubuntu 15.04, por lo que todos los componentes requeridos se instalan de forma predeterminada en las versiones actuales

Si el inicio manual de  _Apache Kafka_  funciona, puedes activar finalmente el  **inicio automático**  como parte del inicio del sistema:

```mixed
sudo systemctl enable kafka
```

## Apache Kafka: tutorial para dar los primeros pasos

Ha llegado el momento de probar Apache Kafka. Antes que nada, deberás procesar un primer mensaje utilizando la plataforma de mensajería. Para ello, necesitarás un  **productor**  y un  **consumidor**, es decir, una instancia que permita escribir y publicar datos en topics, así como una instancia que pueda leer los datos de los topics. En primer lugar, crearás un  **topic**, que se llamará  **TutorialTopic**  en este caso. Como se trata de un topic sencillo a modo de prueba, tan solo incluirá una partición y una réplica:

```mixed
&gt; bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic TutorialTopic
```

A continuación, crea un productor que inserte un primer mensaje de muestra (como «¡Hola, mundo!») en el topic que acabas de establecer. Para ello, utiliza el  **script de shell**_kafka-console-producer.sh_, que recibirá el nombre del host, el puerto del servidor (en el ejemplo: ruta predeterminada de Kafka) y el nombre del topic como argumentos:

```mixed
echo &quot;&#xA1;Hola, mundo!&quot; | ~/kafka/bin/kafka-console-producer.sh --broker-list localhost:9092 --topic TutorialTopic &gt; /dev/null
```

Utilizando el script _kafka-console-consumer.sh_, crearás un consumidor de Kafka que  **procesará y renderizará mensajes de TutorialTopic**. De nuevo, el nombre del host y el puerto del servidor de Kafka y el nombre del topic son necesarios como argumentos. Además, se añadirá el argumento «--from-beginning» para que el mensaje «¡Hola, mundo!», que en este caso se publicó antes de que el consumidor se iniciara, pueda ser procesado por este:

```mixed
&gt; bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic TutorialTopic --from-beginning
```

Como resultado, el  **mensaje «¡Hola, mundo!»**  aparece en el terminal, con el script ejecutándose y esperando a que se publiquen más mensajes en el topic de prueba. Por lo tanto, si  **introduces más datos**  en otra ventana del terminal mediante el productor, también deberías verlos en la ventana donde se ejecuta el script del consumidor.

[![Terminal de Ubuntu: arranque manual de un consumidor de Kafka](https://www.ionos.es/digitalguide/fileadmin/DigitalGuide/Screenshots_2019/kafka-tutorial-5.jpg "Terminal de Ubuntu: arranque manual de un consumidor de Kafka")](https://www.ionos.es/digitalguide/fileadmin/DigitalGuide/Screenshots_2019/kafka-tutorial-5.jpg)
