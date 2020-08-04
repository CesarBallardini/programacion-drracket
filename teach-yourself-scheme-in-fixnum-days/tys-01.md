# Teach Yourself Scheme in Fixnum Days - Capítulo 1 - Un poco de Scheme

El primer programa que uno escribe es el que dice `"Hola mundo!"`  en la consola.
Usa tu editor favorito y crea un archivo llamado `hola.scm` con el contenido siguiente:

```scheme
; El primer programa

(begin
  (display "Hola, mundo!")
  (newline))
```

La primer línea es un comentario.  Cuando Scheme ve un punto y coma lo ignora todo lo que sigue hasta el final de la línea.

La forma `begin` es la manera que tiene Scheme de introducir una secuencia de subformas.
En este caso hay dos subformas.
La primera es una llamada al procedimiento `display` que muestra su argumento (el string `"Hola, mundo!"`) en la consola o salida estándar.
Se sigue con una llamada a procedimiento `newline`, el cual hace que en la salida aparezca un cambio de línea.

Para correr este programa primero inicia el Scheme, que será Dr Racket. Usualmente para esto se hace clic en el icono del programa o en la consola de texto se escribe el nombre del programa `drracket` en caso por ejemplo de que esté utilizando un GNU/Linux.

```bash
drracket
```


Esto invoca al *listener* de Scheme, el cual lee la entrada, la evalúa, imprime el resultado, y espera por más entrada para leer.
Por esta razón al *listener* se lo denomina bucle *read-eval-print*.
Esto no es muy diferente de lo que hace la línea de comandos de su sistema operativo la cual también lee los mandatos, los ejecuta y luego espera por más.
Como en el sistema operativo, el *listener* de Scheme tiene su propio *prompt*, que en general es un signo `>`.

Por ejemplo, en el prompt podés cargar el archivo `hola.scm` mediante:

```scheme
(load "hola.scm")
```

Va a ejecutar los contenidos de `hola.scm` con lo cual va a escribirse en la salida `Hola, mundo!` seguido por un cambio de línea.
Luego de esto,  el *listener* otra vez espera que escribamos algo más. 

Como tenemos un *listener* tan bien dispuesto, no necesitás escribir los programas en un archivo y cargarlos.
Algunas veces, especialmente cuando estás en un modo de exploración, es posible  escribir directamente las expresiones en el prompt del *listener* y ver qué pasa.
Por ejemplo, al escribir en el *prompt* de Scheme la forma

```scheme
(begin
  (display "Hola, mundo!")
  (newline))
```

eso produce:

```text
Hola mundo!

```

En verdad, podrías haber escrito `"Hola, mundo!"` en el *listener*, y hubieras obtenido el mismo string resultante, porque `"Hola, mundo!"` es el resultado de evaluar `"Hola, mundo!"`.

Sin embargo hay una diferencia.  
En el primer caso (el del `begin`) la cadena no tiene comillas dobles alrededor, ni se obtiene ningún resultado en la evaluación sino que el `Hola, mundo!`  que vemos
proviene del efecto lateral del procedimiento `display` y el procedimiento `newline` en la salida estándar.
El en segundo caso es el *print* del *read-eval-print* el que pone `"Hola, mundo!"`en la salida estándar, y por eso lo vemos encerrado entre comillas dobles.
 
A partir de ahora utilizaremos la notación  `=>` para denotar evaluación. Por lo tanto:

```text
E => v
```

indica que la forma `E` evalúa a un resultado de valor `v`.  Por ejemplo,

```scheme
(begin
  (display "Hola, mundo!")
  (newline))
=>
```

(O sea nada), aunque tiene el efecto colateral de escribir en la salida estándar `Hola, mundo!`. Por otro lado,

```scheme
"Hola, mundo!"
=> "Hola, mundo!"
```

Vemos que hay un valor retornado por la evaluación de la expresión.

En cualquier caso, todavía quedamos dentro del *listener*.  Para salir de Scheme y volver al sistema operativo, evaluamos la expresión:

```scheme
(exit)
```


El *listener* es una manera conveniente de probar iteractivamente programas y fragmentos de programas.
Sin embargo no es imprescindible.
Puedo utilizar la manera tradicional de crear programas completos en archivos y hacer que Scheme los ejecute sin ningún *listener*.


El dialecto de Scheme que vamos a usar es Racket.  Racket tiene dos opciones para correr programas:
* desde la línea de mandatos del sistema operativo (`racket`)
* desde un entorno gráfico integrado de desarrollo y depuración (`drracket`)


En Racket se pueden correr programas Scheme desde la línea de comandos del sistema operativo con:

```bash
racket --script hola.scm
```

La prueba más arriba de cargar `hello.scm` se describió usando este mandato `racket`

Por otro lado, si deseas utilizar la interfaz gráfica, el programa se carga con:

```bash
drracket
```

O puedes abrir el DrRacket de la manera acostumbrada (haciendo clic en el ícono, buscarlo en la lista de programas, etc.).  
Luego procedes a usar la opción de cargar un fuente desde los menúes de la ventana de la aplicación.
En otro capítulo estudiaremos cómo es el entorno interactivo; por ahora avanzamos con el lenguaje.

En Racket se acostumbra usar la extensión "`.rkt`" para indicar que se trata de fuentes en ese lenguaje de programación.


