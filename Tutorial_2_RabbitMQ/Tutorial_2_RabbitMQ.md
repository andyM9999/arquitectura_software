# Tutorial RabbitMQ

## ¿Qué es RabbitMQ?

RabbitMQ es un broker de mensajería de código abierto, distribuido y escalable, que sirve como intermediario para la comunicación eficiente entre productores y consumidores.

RabbitMQ implementa el protocolo mensajería de capa de aplicación AMQP (Advanced Message Queueing Protocol), el cual está enfocado en la comunicación de mensajes asíncronos con garantía de entrega, a través de confirmaciones de recepción de mensajes desde el broker al productor y desde los consumidores al broker.

En una forma simplificada,  **en RabbitMQ se definen colas que van a almacenar los mensajes que envían los productores hasta que las aplicaciones consumidoras obtienen el mensaje y lo procesan.**  Esto nos permite diseñar e implementar sistemas distribuidos, en los cuales un sistema se divide en módulos independientes que se comunican entre sí a través de mensajes.

Podemos decir entonces que  **RabbitMQ en su rol de intermediario, tiene como labor asegurarse de que los mensajes que un productor envía, se enruten al consumidor correcto.**

## ¿Qué componentes podemos encontrar en RabbitMQ?

#### 1. Exchange

Este componente es el encargado de recibir los mensajes enviados al broker por un productor y depositarlos en la cola adecuada de acuerdo a una llave de enrutamiento (routing key). Esto significa que  **el productor no envía los mensajes directamente a la cola, sino que los envía a un exchange con una llave de enrutamiento.**

De esta forma, si un productor quiere enviar un mensaje a diversas colas, no tiene que enviarlo a cada una de ellas, sino que el exchange se encarga de distribuir este mensaje a cada una de las colas. Conozcamos ahora los diferentes tipos de exchange: d**irect, topic y fanout.**

-   El exchange direct: toma la llave de enrutamiento que viene en el mensaje y lo lleva a la cola que está asociada a este exchange y a esta llave de enrutamiento.

-   El exchange topic: lleva el mensaje a las colas que cumplan con un patrón en la llave de enrutamiento. Por ejemplo, si tenemos la cola Q1 asociada al exchange EXCH1 con la llave de enrutamiento  **solicitud.credito.consumo**  y la cola Q2 asociada al mismo exchange con la llave de enrutamiento  **solicitud.credito.libranza**, un mensaje que es enviado al exchange EXCH1 con llave de enrutamiento  **solicitud.credito.***  será enviado a ambas colas.

-   El exchange fanout: envía el mensaje a todas las colas asociadas con el exchange, sin importar la llave de enrutamiento.

#### 2. Routing Key

Es la llave que utiliza el exchange para saber a donde enrutar un mensaje, y a su vez es la misma que usa la cola para asociarse con un exchange.

#### 3. Cola

Es el componente que guarda los mensajes provenientes de los exchange y los envía a los consumidores que están escuchando por estos mensajes.

#### 4. Binding

Es la asociación entre una cola y un exchange a través de una llave de enrutamiento.

#### 5. Virtual Host

División lógica de los componentes de servidor de RabbitMQ en la cual estos se agrupan para simplificar su administración y control. Debemos tener en cuenta que no pueden existir colas con el mismo nombre en un mismo virtual host, pero sí en diferentes virtual host.

Ahora veamos un ejemplo de la cotidianidad para entender estos conceptos. Imaginemos los casilleros donde suelen dejar la correspondencia de los apartamentos de un edificio. Cada uno de los casilleros tiene una marcación con el número del apartamento en la que el portero del edificio deposita las encomiendas cuando alguien se las entrega. Para nuestro ejemplo  **los casilleros representan las colas, el número del apartamento es la llave de enrutamiento, el portero hace las veces de exchange y la persona que entrega la encomienda es el productor**.

Teniendo en cuenta el ejemplo, un exchange directo se da cuando una encomienda va para el apartamento 010, el productor se la entrega al portero (exchange directo), quien revisa la etiqueta de la encomienda para establecer a qué apartamento va destinado y depositarlo en su respectivo casillero.
##### En la siguiente imagen se presenta el envío de un mensaje a un exchange directo:

![1 mensaje exchange directo](https://www.pragma.com.co/hs-fs/hubfs/blog/RabbitMQ/1mensaje_exchange_directo.-.jpg?width=1224&name=1mensaje_exchange_directo.-.jpg)

Por otro lado, un exchange de tipo topic se da cuando el administrador del edificio desea enviar una carta solo a los apartamentos del último piso del edificio (601,602), el portero (exchange) recibe las cartas y las depositas en los casilleros (cola) de los apartamentos del último piso (601, 602).

##### En la siguiente imagen se presenta el envío de un mensaje a un exchange de tipo topic:

![2 mensaje exchange tipo topic](https://www.pragma.com.co/hs-fs/hubfs/blog/RabbitMQ/2mensaje_exchange_tipo_topic.jpg?width=1224&name=2mensaje_exchange_tipo_topic.jpg)

Un exchange de tipo fanout, se da cuando el administrador del edificio envia un comunicado a todos los apartamentos. En este caso el portero (exchange) deposita el mensaje en cada casillero sin preocuparse en revisar el número del apartamento, sino en que cada casillero reciba una copia del mensaje.

##### En la siguiente imagen se presenta el envío de un mensaje a un exchange de tipo fanout.

![3 mensaje exchange tipo fanout](https://www.pragma.com.co/hs-fs/hubfs/blog/RabbitMQ/3mensaje_exchange_tipo_fanout.-.jpg?width=1224&name=3mensaje_exchange_tipo_fanout.-.jpg)

## Introducción a RabbitMQ con Python

La última versión de  [AMQPStorm](https://github.com/eandersson/amqpstorm)  está disponible en  [pypi](https://pypi.python.org/pypi/AMQPStorm)  o puede instalarla usando  [pip](https://pip.pypa.io/en/stable/)

```
pip install amqpstorm

```

###  Cómo consumir mensajes de RabbitMQ

Comience con la importación de la biblioteca.

```
from amqpstorm import Connection

```

Al consumir mensajes, primero debemos definir una función para manejar los mensajes entrantes. Esta puede ser cualquier función que se pueda llamar y tiene que tomar un objeto de mensaje o una tupla de mensaje (según el parámetro  `to_tuple`  definido en  `start_consuming`  ).

Además de procesar los datos del mensaje entrante, también tendremos que Reconocer o Rechazar el mensaje. Esto es importante, ya que necesitamos informar a RabbitMQ que recibimos y procesamos el mensaje correctamente.

```
def on_message(message):
    """This function is called on message received.

    :param message: Delivered message.
    :return:
    """
    print("Message:", message.body)

    # Acknowledge that we handled the message without any issues.
    message.ack()

    # Reject the message.
    # message.reject()

    # Reject the message, and put it back in the queue.
    # message.reject(requeue=True)

```

A continuación, debemos configurar la conexión al servidor RabbitMQ.

```
connection = Connection('127.0.0.1', 'guest', 'guest')

```

Después de eso tenemos que configurar un canal. Cada conexión puede tener múltiples canales y, en general, al realizar tareas de subprocesos múltiples, se recomienda (pero no es obligatorio) tener uno por subproceso.

```
channel = connection.channel()

```

Una vez que tengamos configurado nuestro canal, debemos informarle a RabbitMQ que queremos comenzar a consumir mensajes. En este caso, usaremos nuestra función  `on_message`  previamente definida para manejar todos nuestros mensajes consumidos.

La cola que escucharemos en el servidor RabbitMQ será  `simple_queue`  , y también le estamos diciendo a RabbitMQ que reconoceremos todos los mensajes entrantes una vez que hayamos terminado con ellos.

```
channel.basic.consume(callback=on_message, queue='simple_queue', no_ack=False)

```

Finalmente, necesitamos iniciar el bucle IO para comenzar a procesar los mensajes entregados por el servidor RabbitMQ.

```
channel.start_consuming(to_tuple=False)

```

### Cómo publicar mensajes a RabbitMQ[#](https://sodocumentation.net/es/python/topic/3373/introduccion-a-rabbitmq-utilizando-amqpstorm#como-publicar-mensajes-a-rabbitmq)

Comience con la importación de la biblioteca.

```
from amqpstorm import Connection
from amqpstorm import Message

```

Luego necesitamos abrir una conexión al servidor RabbitMQ.

```
connection = Connection('127.0.0.1', 'guest', 'guest')

```

Después de eso tenemos que configurar un canal. Cada conexión puede tener múltiples canales y, en general, al realizar tareas de subprocesos múltiples, se recomienda (pero no es obligatorio) tener uno por subproceso.

```
channel = connection.channel()

```

Una vez que tengamos configurado nuestro canal, podemos comenzar a preparar nuestro mensaje.

```
# Message Properties.
properties = {
    'content_type': 'text/plain',
    'headers': {'key': 'value'}
}

# Create the message.
message = Message.create(channel=channel, body='Hello World!', properties=properties)

```

Ahora podemos publicar el mensaje simplemente llamando a  `publish`  y proporcionando una  `routing_key`  . En este caso, vamos a enviar el mensaje a una cola llamada  `simple_queue`  .

```
message.publish(routing_key='simple_queue')

```

### Cómo crear una cola retrasada en RabbitMQ[#](https://sodocumentation.net/es/python/topic/3373/introduccion-a-rabbitmq-utilizando-amqpstorm#como-crear-una-cola-retrasada-en-rabbitmq)

Primero debemos configurar dos canales básicos, uno para la cola principal y otro para la cola de demora. En mi ejemplo al final, incluyo un par de marcas adicionales que no son necesarias, pero hacen que el código sea más confiable; tales como  `confirm delivery`  ,  `delivery_mode`  y  `durable`  . Puede encontrar más información sobre estos en el  [manual de](http://www.rabbitmq.com/tutorials/amqp-concepts.html)  RabbitMQ.

Después de configurar los canales, agregamos un enlace al canal principal que podemos utilizar para enviar mensajes desde el canal de retardo a nuestra cola principal.

```
channel.queue.bind(exchange='amq.direct', routing_key='hello', queue='hello')

```

A continuación, debemos configurar nuestro canal de retardo para reenviar los mensajes a la cola principal una vez que hayan caducado.

```
delay_channel.queue.declare(queue='hello_delay', durable=True, arguments={
    'x-message-ttl': 5000,
    'x-dead-letter-exchange': 'amq.direct',
    'x-dead-letter-routing-key': 'hello'
})

```

-   [x-message-ttl](https://www.rabbitmq.com/ttl.html)  _(Mensaje - Time To Live)_
    
    Normalmente se usa para eliminar automáticamente los mensajes antiguos en la cola después de una duración específica, pero al agregar dos argumentos opcionales podemos cambiar este comportamiento y, en cambio, hacer que este parámetro determine en milisegundos el tiempo que los mensajes permanecerán en la cola de demora.
    
-   [x-dead-letter-routing-key](http://www.rabbitmq.com/dlx.html)
    
    Esta variable nos permite transferir el mensaje a una cola diferente una vez que han caducado, en lugar del comportamiento predeterminado de eliminarlo por completo.
    
-   [x-dead-letter-exchange](http://www.rabbitmq.com/dlx.html)
    
    Esta variable determina qué Exchange usó para transferir el mensaje de hello_delay a hello queue.
    

**Publicación en la cola de retardo**

Cuando hayamos terminado de configurar todos los parámetros básicos de Pika, simplemente envíe un mensaje a la cola de demora utilizando la publicación básica.

```
delay_channel.basic.publish(exchange='',
                            routing_key='hello_delay',
                            body='test',
                            properties={'delivery_mod': 2})

```

Una vez que haya ejecutado el script, debería ver las siguientes colas creadas en su módulo de administración RabbitMQ.  ![introduzca la descripción de la imagen aquí](http://i.stack.imgur.com/jWEDR.png)

**Ejemplo.**

```
from amqpstorm import Connection

connection = Connection('127.0.0.1', 'guest', 'guest')

# Create normal 'Hello World' type channel.
channel = connection.channel()
channel.confirm_deliveries()
channel.queue.declare(queue='hello', durable=True)

# We need to bind this channel to an exchange, that will be used to transfer
# messages from our delay queue.
channel.queue.bind(exchange='amq.direct', routing_key='hello', queue='hello')

# Create our delay channel.
delay_channel = connection.channel()
delay_channel.confirm_deliveries()

# This is where we declare the delay, and routing for our delay channel.
delay_channel.queue.declare(queue='hello_delay', durable=True, arguments={
    'x-message-ttl': 5000, # Delay until the message is transferred in milliseconds.
    'x-dead-letter-exchange': 'amq.direct', # Exchange used to transfer the message from A to B.
    'x-dead-letter-routing-key': 'hello' # Name of the queue we want the message transferred to.
})

delay_channel.basic.publish(exchange='',
                            routing_key='hello_delay',
                            body='test',
                            properties={'delivery_mode': 2})

print("[x] Sent")
```


