
# Tutorial_9_Crawling y Scraping de datos

¿Confundido sobre el Web Scraping y el Web Crawling? A muchos les resulta díficil identificar la diferencia entre uno y otro.

¿Por que la confusión?

Se debe a que el Scraping y Crawling Web, si no son absolutamente idénticos, son similares e incluso iguales en cierta medida.

Para aclarar las dudas relacionados a ambos, a continuación las definiciones.

## ¿Que es Web Crawler?

-   El termino de crawler viene de la forma en la cual se desplaza una araña. Es por eso que a un rastreador web también se le llama araña. Básicamente es un bot de internet que navega en forma sitemática en la Word Wide Web, generalmente con el proposito de indexar la web.
-   Se utiliza para indexar la información en la página usando bots también conocido como rastreadores.
-   Navegando por cada rincón y grieta de la World Wide Web, la araña localiza y recupera la información que se encuentra en las diferentes capas. Los rastreadores web o bots navegan a través de un montón de datos e información y obtienen lo que sea relevante para el proyecto.
-   Ejemplo de rastreo web:
    -   Lo que hace Google, Yahoo y Bing es un ejemplo sencillo de Web Crawler.
    
    
### ¿Cómo funciona el Web Crawler?

El proceso de web crawler sigue los siguientes pasos:

1.  Selecciona una URL inicial o URL iniciales
2.  Indicarlo como parte de la frontera
3.  Elija la URL de la frontera
4.  Obtener la página web correspondiente a esta URL
5.  Analice esta página web para encontrar nuevos enlaces URL
6.  Agregue todas las URL recien encontradas a la frontera
7.  Vaya al paso 3 y repita has que la frontera este vacía.

## ¿Que es Web Scraping?

-   El web scraping es básicamente extraer datos de sitios web de manera automatizada.
-   Está automatizado por que utiliza bots para obtener la información del sitio web.
-   Es un análisis programático de una página web para descargar información de ella.
-   El scraping de datos implica localizar los datos y luego extraerlos. No copia y pega, sino que, obtiene directamente los datos de manera precisa. No se limita a la web, los datos se pueden obtener prácticamente desde cualquier lugar donde se almacenan. Puede ser Internet u otra fuente de datos.
-   Ejemplo de web scraping:
    -   El web scraping implicaría obtener la información de una página web específica, por ejemplo, obtener la información de precios de determinados productos de las páginas de Amazon.
    
### ¿Cómo funciona el Web Scraping?

El proceso de web scraping sigue los siguientes pasos:

1.  Solicitud-respuesta
    -   El primer paso es solicitar al sitio web de destino el contenido de una URL específica.
    -   A cambio, el scraping obtiene la información solicitada en HTML.
2.  Analizar y extraer
    -   Cuando se trata de análisis, generalmente se aplica cualquier lenguaje de programación. Es el proceso de tomar el código como texto y producir una estructura en la memoria que la computadora pueda entender y trabajar.
3.  Descargar los datos
    -   La parte final es donde descarga y guarda los datos en un CSV, JSON o en una base de datos para que pueda recuperarse y se pueda utilizar.

¡Ahora comencemos nuestro viaje en web scraping usando Python!

**Paso 1: Importar la biblioteca de Python**

En este tutorial, le mostraremos cómo scrape las reseñas de Yelp. Utilizaremos dos bibliotecas:  [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)  en bs4 y  [request](https://realpython.com/python-requests/)  en urllib. Estas dos bibliotecas se usan comúnmente en la construcción de un rastreador web con Python. El primer paso es importar estas dos bibliotecas en Python para que podamos usar las funciones en estas bibliotecas.

![import library](https://www.octoparse.es/media/7096/import-library.png)

**Paso 2: Extraer el HTML de la página web**

Necesitamos extraer comentarios de  “[https://www.yelp.com/biz/milk-and-cream-cereal-bar-new-york?osq=Ice+Cream](https://www.yelp.com/biz/milk-and-cream-cereal-bar-new-york?osq=Ice+Cream)”.  Primero, guardemos la URL en una variable llamada URL. Entonces podríamos acceder al contenido de esta página web y guardar el HTML en "ourUrl"  utilizando la función _urlopen()  en la solicitud._

![request url](https://www.octoparse.es/media/7095/extract-html.png)

Luego aplicamos BeautifulSoup para analizar la página.

![beautifulsoup](https://www.octoparse.es/media/7094/beautifulsoup.png)

Ahora que tenemos la "soup", que es el HTML sin formato para este sitio web, podríamos usar una función llamada  _prettify()_  para limpiar los datos sin procesar e imprimirla para ver la estructura anidada de HTML en la "soup".

![prettify](https://www.octoparse.es/media/7093/prettify.png)

**Paso 3: Ubica y Scraping las reseñas**

A continuación, deberíamos encontrar las reseñas HTML en esta página web, extraerlas y almacenarlas. Para cada elemento en la página web, siempre tendrían una  “ID”  HTML única. Para verificar su  ID, necesitaríamos  INSPECT  en una página web.

![inspect element](https://www.octoparse.es/media/7147/inspect_element.png)

Después de hacer clic en "Inspeccionar elemento" (o "Inspeccionar", depende de diferentes navegadores), podemos ver el HTML de las revisiones.

![node](https://www.octoparse.es/media/7150/node.png)

En este caso, las revisiones se encuentran debajo de la etiqueta llamada  "p". Entonces, primero usaremos la función llamada find_all() para encontrar el nodo padre de estas revisiones. Y luego ubique todos los elementos con la etiqueta "p" debajo del nodo padre en un bucle. Después de encontrar todos los elementos "p", los almacenaríamos en una lista vacía llamada  “review”.

![review](https://www.octoparse.es/media/7092/review.png)

Ahora tenemos todas las reseñas de esa página. Veamos cuántas reseñas hemos extraído.

![len review](https://www.octoparse.es/media/7091/len-review.png)

**Paso 4: Limpia las reseñas**

Debe tener en cuenta que todavía hay algunos textos inútiles como  “_<p lang=’en’>_”  al comienzo de cada revisión,  “_<br/>_”  en el medio de las revisiones y  “_</p>_”  en Fin de cada revisión.

“_<br/>_”  representa un salto de línea simple. No necesitamos ningún salto de línea en las revisiones, por lo que tendremos que eliminarlos. Además,  “_<p lang=’en’>_” y “_</p>_”  son el principio y el final del HTML y también debemos eliminarlos.

![basically clean data](https://www.octoparse.es/media/7090/clean.png)

Finalmente, obtenemos con éxito todas las revisiones limpias con menos de 20 líneas de código.

Aquí hay solo una demostración para scraping 20 comentarios de Yelp. Pero en casos reales, es posible que tengamos que enfrentar muchas otras situaciones. Por ejemplo, necesitaremos pasos como la paginación para ir a otras páginas y extraer los comentarios restantes de esta tienda. O también tendremos que extraer otra información como el nombre del revisor, la ubicación del revisor, el tiempo de revisión, la calificación, el check-in ......

Para implementar la operación anterior y obtener más datos, necesitaríamos aprender más funciones y bibliotecas como el selenio o la expresión regular. Sería interesante pasar más tiempo profundizando en los desafíos del web scraping.
