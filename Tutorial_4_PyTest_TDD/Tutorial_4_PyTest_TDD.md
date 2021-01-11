
# Tutorial PyTest_TDD

## ¿Qué es el desarrollo dirigido por pruebas o TDD?

_**TDD**_ proviene de  **Test-Driven Development,** lo cual no es más que una técnica de diseño e implementación de software incluida dentro de la metodología ágil. TDD es una técnica para diseñar software que se centra en tres pilares fundamentales:

-   La implementación de las funciones justas que el cliente necesita y no más.
-   La minimización del número de defectos que llegan al software en fase de producción.
-   La producción de software modular, altamente reutilizable y preparado para el cambio.

Al dar los primeros pasos en este tema, muchos suelen caer en la idea equivocada de que TDD se utiliza para que el proyecto, o mejor aún, el código que se está escribiendo, termine cargado con una amplia cobertura de pruebas (casos de uso), y aunque esto no es del todo herrado, y siempre se agradece, la idea principal detrás de TDD es convertir a los programadores en verdaderos desarrolladores.

¿Qué quiero decir con esto? En principio, cualquier persona después de un tiempo de leer libros y ver videos en internet, puede escribir código, pero para considerarse un verdadero profesional, debes ir más allá, conocer metodologías, patrones de diseño de software, pruebas unitarias, etc. Y para lograr esto, TDD nos ayuda bastante.

Cuando por fin nos sentimos cómodos trabajando con TDD (desarrollo dirigido por pruebas) podemos contar con características que de otra manera, sería muy laborioso añadir a nuestro flujo de trabajo en el diseño de software.

## Ciclo de un desarrollo dirigido por pruebas

-   **Enfocarse en un requerimiento a la vez:**  Se elige de una lista de requerimientos, aquél que se cree que nos dará mayor conocimiento del problema y que a la vez sea fácilmente implementable.
-   **Escribir una prueba****:**  Se comienza escribiendo una prueba para el requisito. Para ello el programador debe entender claramente las especificaciones y los requisitos de la funcionalidad que está por implementar. Este paso nos orilla como programadores a tomar la perspectiva de un cliente considerando el código a través de sus  [interfaces](https://es.wikipedia.org/wiki/Interfaz "Interfaz").
-   **Verificar que la prueba falla:**  Si la prueba no falla es porque el requerimiento ya estaba implementado o porque la prueba no ha sido escrita correctamente.
-   **Escribir la implementación****:**  Escribir el código lo más sencillo posible, a modo de que haga que la prueba funcione. Se usa la expresión «Mantenlo simple, Es**.» («Keep It Simple, Stupid!»), conocida como  [principio_KISS](https://es.wikipedia.org/wiki/Principio_KISS "Principio KISS").
-   **Ejecutar las pruebas automatizadas****:**  Verificar que todas las pruebas escritas funcionan correctamente.
-   **Eliminación de duplicación de código****:**  El paso final es la  [refactorización](https://es.wikipedia.org/wiki/Refactorizaci%C3%B3n "Refactorización"), que se utilizará principalmente para eliminar  [código duplicado](https://es.wikipedia.org/wiki/C%C3%B3digo_duplicado "Código duplicado"). Se van añadiendo pequeños cambios al código a modo de iteraciones, siempre asegurandose de ejecutar las pruebas para asegurarse de que mantiene un correcto funcionamiento.
-   **Actualización de la lista de requisitos****:**  Se actualiza la lista de requerimientos tachando el requerimiento implementado. Asimismo se agregan requerimientos que se hayan visto como necesarios durante este ciclo y se agregan requerimientos de diseño, ya que puede darse el caso en que se encuentre un requerimiento que se encuentre ligado a uno ya establecido previamente.

Una de las mayores ventajas de escribir código utilizando TDD es que rara vez tienes que hacer uso de un depurador. Aunque como cualquier cosa tiene sus desventajas, la más importante, es que para llevar a cabo esta práctica correctamente se requiere de mucha paciencia y dedicación para que las pruebas (casos de uso) que establezcamos sean lo más adecuados posibles.

## Ejemplos de TDD (desarrollo dirigido por pruebas) con Python

### ¿Qué es Pytest?

Cómo lo describen en su página oficial:

> «The  `pytest`  framework makes it easy to write small tests, yet scales to support complex functional testing for applications and libraries.»

Y es que es verdad, existen bastantes opciones para desarrollar mediante TDD con Python, pero sólo con Pytest he podido encontrar esa flexibilidad y facilidad con la que me siento tan contento. Para utilizar pytest, basta con instalarlo como cualquier otro paquete
```
$ pip install pytest
```
Para escribir nuestras pruebas, debemos hacerlo utilizando archivos con el prefijo  **«test_».**

Realicemos un sencillo ejemplo del ciclo de desarrollo con TDD. Primero creamos un directorio de trabajo y nos posicionamos dentro del mismo.
```
$ mkdir [DIRECTORIO] && cd [DIRECTORIO]
```
No te olvides de reemplazar [DIRECTORIO] con el nombre que le quieras dar tu mismo, en mi caso será  **tdd_example.**  Ahora, en este directorio creamos el archivo donde escribiremos nuestro código, en mi caso se llamará  **test_add.py.**
```
$ touch test_add.py
```
Ahora, comencemos a escribir nuestro código siguiendo TDD.

-   Recordando el ciclo del desarrollo dirigido por pruebas, debemos elegir un requerimiento, en mi caso será  **sumar 2 números**.
-   Escribimos nuestra prueba.
```
# Prueba la función con los valores (4, 5)
# Resultado: 9 (True)
def test_add():
assert add(4, 5) == 9
```

-   Verificamos que la prueba implementada falle, ya que en este punto el requerimiento no se encuentra implementado.
```
$ pytest
```
Veremos como nos aparecen mensajes de error, diciendo que no se encuentra implementado el método add, justo lo que esperabamos.

-   El siguiente punto es escribir nuestra implementación.
```
def add(x, y):
return x + y
```
-   Ahora, volvemos a ejecutar la prueba para validar el correcto funcionamiento de nuestra implementación.
```
$ pytest
```
Nos dará una salida con información de la ejecución, y un mensaje diciendo que todo ha sido ejecutado correctamente.

Y bien, creo que es todo por ahora, aún faltaron algunos pasos, pero siendo un ejemplo tan básico, no lo vi necesario, así mismo, invito a todos a que realicen experimentos más complejos, y lean la documentación de pytest para que lo implementen en sus desarrollos del día a día.
