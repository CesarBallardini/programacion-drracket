# Teach Yourself Scheme in Fixnum Days - Capítulo 2 - Tipos de datos

Un *tipo de datos* es una colección de valores relacionados. 
Estas colecciones no necesitan ser disjuntas, y con frecuencia son jerárquicas.
Scheme tiene un rico conjunto de tipos de datos; algunos son tipos de datos simples (indivisibles)
y otros son tipos de datos compuestosque se construyen combinando otros tipos de datos.

## 2.1. Tipos de datos simples

Los tipos de datos simples en Scheme incluyen: booleanos, números, caracteres y símbolos.

### 2.1.1. Booleanos

En Scheme se indica a verdadero con `#t` y a falso con `#f`.  Existe un procedimiento predicado
que sirve para verificar si un dato dado es un boolean:

```scheme
(boolean? #t) => #t
(boolean? "Hola, Mundo!") => #f
```

El procedimiento `not` niega su argumento, considerándolo como booleano:

```scheme
(not #f)              =>  #t
(not #t)              =>  #f
(not "Hola, Mundo!")  =>  #f
```

La última expresión ilustra una facilidad especial de Scheme: en un contexto que requiere un booleano,
Scheme considera como verdadero a cualquier valor que no es `#f`.

### 2.1.2. Números

Los números en Scheme pueden ser:

* enteros (42)
* racionales (22/7)
* reales (3.1416)
* complejos (2+3i)

Un entero es un racional es un real es un complejo es un número.

Existen predicados para comprobar la *numericidad* de las diferentes clases:

```scheme
(number? 42)       =>  #t
(number? #t)       =>  #f

(complex? 2+3i)    =>  #t

(real? 2+3i)       =>  #f
(real? 3.1416)     =>  #t
(real? 22/7)       =>  #t
(real? 42)         =>  #t

(rational? 2+3i)   =>  #f
(rational? 3.1416) =>  #t
(rational? 22/7)   =>  #t

(integer? 22/7)    =>  #f
(integer? 42)      =>  #t
```

En Scheme, no es necesario que los enteros se especifiquen en decimal (base 10).  Se pueden especificar en binario mediante el prefijo `#b`. Así el `b1100` es el número doce.  El prefijo octal es `#o` y el prefijo hexadecimal es `#x`.  Hay un prefijo para los números decimales, el `#d`, pero es opcional: si un número no tiene prefijo, se lo supone decimal.

Los números se pueden comparar por igualdad mediante el predicado general `eqv?`:

```scheme
(eqv? 42 42)   =>  #t
(eqv? 42 #f)   =>  #f
(eqv? 42 42.0) =>  #f (note que no son números iguales)
```

Sin embargo, si ambos son números, se puede usar un predicado de comparación de igualdad esecífico para números, el `=` 

```scheme
(= 42 42)   =>  #t
(= 42 #f)   -->ERROR!!! sólo sirve para comparar números
(= 42 42.0) =>  #t (son números de igual valor)
```

Otras comparaciones numéricas permitidas: `<`, `<=`, `>`, `>=`.

```scheme
(< 3 2)    =>  #f
(>= 4.5 3) =>  #t
```

Los procedimientos artitméticos `+`, `‑`, `*`, `/`, `expt` exhiben el comportamiento esperado:

```scheme
(+ 1 2 3)    =>  6
(- 5.3 2)    =>  3.3
(- 5 2 1)    =>  2
(* 1 2 3)    =>  6
(/ 6 3)      =>  2
(/ 22 7)     =>  22/7
(expt 2 3)   =>  8
(expt 4 1/2) =>  2.0
```

Cuando se usan con un único argumento, `-` y `/` retornan la negación y el recíproco, respectivamente:

```scheme
(- 4) =>  -4
(/ 4) =>  1/4
```

Los procedimientos `max` y `min` retornan el máximo y el mínimo de los argumentos numéricos proporcionados.  Ambas funciones admiten cualquier número de alimentos:

```scheme
(max 1 3 4 2 3) =>  4
(min 1 3 4 2 3) =>  1
```

El procedimiento `abs` retorna el valor absoluto de su argumento.

```scheme
(abs  3) =>  3
(abs -4) =>  4
```

En Scheme están disponibles funciones aritméticas y trigonométricas. Para conocer todas ellas, se recomienda leer el "Revised^5 Report on the Algorithmic Language Scheme" en https://schemers.org/Documents/Standards/R5RS/HTML/r5rs.html


### 2.1.3. Caracteres

En Scheme los datos de tipo carácter se representan con el prefijo `#\`.  Entonces, el carácter **c** se representa con `#\c`.  Algunos caracteres tienen nombres descriptivos, como `#\newline` para el cambio de línea, `#\tab` para el tabulador.  Un espacio en blanco se puede representar con `#\ `, o de manera más legible con `#\space`.

El predicado que verifica si un dato es de tipo carácter es `char?`:

```scheme
(char? #\c) =>  #t
(char? 1)   =>  #f
(char? #\;) =>  #t
```

Para comparar caracteres usamos:

```scheme
(char=? #\a #\a)  =>  #t
(char<? #\a #\b)  =>  #t
(char>=? #\a #\b) =>  #f
```

Si deseamos comparaciones que no distingan mayúsculas de minúsculas (`ci` viene de *case-insensitive*), usamos:

```scheme
(char-ci=? #\a #\A) =>  #t
(char-ci<? #\a #\B) =>  #f
```

Podemos pasar a minúsculas con `char‑downcase` y a mayúsculas con `char‑upcase`:

```scheme
(char-downcase #\A) =>  #\a
(char-upcase #\a)   =>  #\A
```

### 2.1.4. Símbolos

Los tipos de datos simples que vimos hasta ahora son *auto-evaluadores*, o sea, si escribís un ejemplar de cualquiera de esos tipos de datos en el *listener* el resultado de la evaluación será el mismo valor que escribiste:

```scheme
#t  =>  #t
42  =>  42
#\c =>  #\c
```

Los símbolos no se comportan de esa manera.  Los símbolos se usan en Scheme como *identificadores*  para *variables*, y por lo tanto al evaluar un símbolo obtenemos el valor que la variable referencia.

Sin ambargo, los símbolos son tipos simples de datos, y son valores legítimos que se pueden usar en Scheme, junto a los demás números, caracteres y otros.

Para especificar un símbolo y que Scheme no lo trate como una variable, debemos indicarlo mediante la forma especial `quote`:

```scheme
(quote xyz)
=> xyz
```

Esta operación es tan frecuente que en la jerga inglesa `quote` se usa como verbo y como adjetivo al hablar de los programas Scheme. Ejemplo: *"you shouls quote the symbol"*; en castellano a veces usamos el término *quotear* en ese sentido.

Como la expresiones con `quote` son tan comunes en Scheme, hay una abreviatura para que sea más conveniente.  La expresión

```scheme
'E
```

en Scheme es equivalente a

```scheme
(quote E)
```

Los símbolos en Scheme se construyen con una secuencia de caracteres.  La única limitación en el nombre de un símbolo es que no debe confundirse con otros datos, por ejemplo, caracteres, booleanos o números o datos compuestos.

Unos ejemplos de símbolos válidos son:
* `esto-es-un-simbolo`
* `i18n`
* `<=>`
* `$!#*` 

Los siguientes no son símbolos:
* `16` (un número)
* `‑i` (un número complejo)
* `#t` (el booleano para verdadero)
* `"esto-es-un-string"` (una cadena de caracteres)
* `(barf)` (una lista)


El predicado para controlar si un dato es un símbolo es `symbol?`:

```scheme
(symbol? 'xyz) =>  #t
(symbol? 42)   =>  #f
```

En Scheme los símbolos normalmente no son difentes si sólo difieren en mayúsculas y minúsculas:

```scheme
(eqv? 'Calorie 'calorie) =>  #t
```

Para utilizar el símbolo `xyz` como una variable global, usamos la forma especial `define`:

```scheme
(define xyz 42)
```

Con esto indicamos que la variable `xyz` referencia el valor `42`. Si escribimos `xyz`
en el *listener*, el resultado que se muestra es el valor referenciado por la variable `xyz`:

```scheme
xyz => 42
```

Si una variable existe, por que la hemos definido con `define`, podemos cambiar su valor con `set!`:

```scheme
(set! xyz "hola")
```

Ahora

```scheme
xyz => "hola"
```

Los procedimientos que alteran o mutan un dato, se estila en Scheme que su nombre tenga un `!` para denotar esto.  De la misma manera, los procedimientos de tipo predicado, que devuelven booleanos, tienen su nombre terminado en `?`.


## 2.2. Tipos de datos compuestos

Los tipos de datos compuestos se construyen mediante la combinación de valores de otros tipos de datos de formas estructuradas.

### 2.2.1. Cadenas de caracteres (*Strings*)

Los *strings* son secuencias de caracteres.  Usaremos "*string*", "cadena" y "cadena de caracteres" para referirnos a este tipo de datos. No deben confundirse con los símbolos, que son tipos de datos simples, donde la secuencia de caracteres corresponde a su nombre. Se puede especificar una cadena al encerrar los caracteres que la constituyen entre comillas dobles.  Las cadenas evalúan a sí mismas:

```scheme
"Hola, mundo!"
=> "Hola, mundo!"
```

Definamos una variable global para el saludo:

```scheme
(define saludo "Hola; mundo!")
```

Note que el punto y coma dentro del string no produce un comentario.

Los caracteres individuales dentro de una cadena se pueden acceder y modificar.  El procedimiento `string-ref` toma un string y un índice --las posiciones se cuentan desde 0-- y retorna el carácter en la posición indicada:

```scheme
(string-ref saludo 0)
=>  #\H
```

Se pueden concatenar strings para obtener otro string como resultado:

```scheme
(string-append "E "
               "Pluribus "
               "Unum")
=>  "E Pluribus Unum"
```

Se puede crear un string  partir de una lista de caracteres:

```scheme
(string #\a #\space #\b #\space #\c)        =>  "a b c"
```


Se puede crear un string con una longitud dada, y luego más adelante llenar las posiciones con los caracteres deseados:

```scheme
(define un-string-de-long-3 (make-string 3))
```

El predicado para comprobar si un dato es un string, es `string?`.

Las cadenas retornadas por `string`, `make-string` y `string-append` son mutables.  El procedimiento
`string-set!` reemplaza el carácter que se encuentra en una dada posición:

```scheme
(define hey (string #\H #\o #\l #\a)) 
hola
=>  "Hola"

(string-set! hey 0 #\S)
Sola
=>  "Sola"
```

### 2.2.2. Vectores

Los vectores son secuencias como los strings, pero sus elementos pueden tener cualquier tipo, no sólo caracteres.  Los elementos pueden ser a su vez vectores, lo cual es una buena forma de disponer de vectores multidimensionales.

Veamos cómo crear un vector con los primeros cinco enteros:

```scheme
(vector 0 1 2 3 4) =>  #(0 1 2 3 4)
```

La forma de representar vectores que tiene Scheme es con el signo  `#` seguido del contenido del vector, encerrado entre paréntesis.

De manera análoga con `make-string`, el procedimiento `make-vector` construye un vector de una longitud dada:

```scheme
(define v (make-vector 5))
```

Los procedimientos:
* ` vector‑ref` accede a los elementos
*  `vector‑set!` modifica los elementos
* `vector?` retorna verdadero si su argumento es un vector.

### 2.2.3. Pares (*dotted pairs*) y listas

Un par (*dotted pair*) es un valor compuesto que se construye combinando dos valores arbitrarios en una tupla ordenada de dos elementos (dupla).  El primer elemento se denomina el `car` y el segundo elemento se denomina el `cdr`; el procedimiento que hace la combinación se denomina `cons`.

```scheme
(cons 1 #t) =>  (1 . #t)
```

Los pares no son autoevaluantes, y por lo tanto para especificarlos como dato necesitamos usar `quote` de manera explícita:

```scheme
'(1 . #t) =>  (1 . #t)

(1 . #t)  -->ERROR!!!
```

Los procedimientos de acceso son `car` y `cdr`:

```scheme
(define x (cons 1 #t))

(car x)
=>  1

(cdr x)
=>  #t
```

Los elementos de un par se pueden reemplazar mediante los mutadores `set-car!` y `set-cdr!`:

```scheme
(define x (cons 1 #t))
(set-car! x 2)
(set-cdr! x #f)

x
=>  (2 . #f)
```

iLos pares pueden contener otros pares:

```scheme
(define y (cons (cons 1 2) 3))

y
=>  ((1 . 2) . 3)
```

El `car` del `car` de esta lista es `1`; el `cdr` del `car` es `2`:

```scheme
(define y (cons (cons 1 2) 3))

(car (car y))
=>  1

(cdr (car y))
=>  2
```

Scheme proporciona abreviaturas para las cascadas de `car` y `cdr`.  Entonces `caar` es "el `car` del `car`" y `cdar` es "el `cdr` del `car`". Hasta cuatro cascadas están garantizadas que existen.

```scheme
(define y (cons (cons 1 2) 3))

(caar y)
=>  1

(cdar y)
=>  2
```

Cuando hay un anidamiento que se da en el segundo argumento, Scheme usa una notación especial:

```scheme
(cons 1 (cons 2 (cons 3 (cons 4 5))))
=>  (1 2 3 4 . 5)
```

O sea, `(1 2 3 4 . 5)` es una abreviatura para `(1 . (2 . (3 . (4 . 5))))`. El último `cdr` de esta expresión es `5`.

Hay una abreviatura adicional si el último `cdr` es un objeto especial llamado *lista vacía*, que se representa mediante un par de paréntesis: `()`.  La lista vacía no es autoevaluante, y cuando uno desea pasarla como argumento a un programa, debe usar `quote`:

```scheme
'() =>  ()
```

La abreviatura para un par de la forma `(1 . (2 . (3 . (4 . ()))))` es:

```text
(1 2 3 4)
```

Esta clase especial de expresión de par se denomina *lista*.  La lista particular más arriba es una que contiene cuatro elementos o de longitud 4.

Se podría haber creado mediante:

```scheme
(cons 1 (cons 2 (cons 3 (cons 4 '()))))
```

Sin embargo, Scheme proporciona un procedimiento denominado `list` que es más conveniente.  `list` toma cualquier cantidad de argumentos y retorna una lista con todos ellos:

```scheme
(list 1 2 3 4)
=>  (1 2 3 4)
```

De hecho, si conocemos todos los elementos de la lista, podemos usar `quote` para especificarla:a

```scheme
'(1 2 3 4)
=>  (1 2 3 4)
```

Los elementos de la lista se pueden acceder mediante un índice:

```scheme
(define y (list 1 2 3 4))

(list-ref y 0) =>  1
(list-ref y 3) =>  4

(list-tail y 1) =>  (2 3 4)
(list-tail y 3) =>  (4)
```

`list-tail` retorna la cola de la lista a partir del índice dado.

Los predicados `pair?`, `list?` y `null?` verifican si su argumento es un par, una lista o la lista vacía, respectivamente:

```scheme
(pair? '(1 . 2)) =>  #t
(pair? '(1 2))   =>  #t
(pair? '())      =>  #f

(list? '())      =>  #t
(null? '())      =>  #t

(list? '(1 2))   =>  #t
(list? '(1 . 2)) =>  #f
(null? '(1 2))   =>  #f
(null? '(1 . 2)) =>  #f
```

### 2.2.4. Conversión entre tipos de datos

Scheme proporciona muchos procedimientos para converti entre tipos de datos.

Podemos convertir entre enteros y caracteres.  El entero usualmente corresponde al código ASCII del carácter.

```scheme
(char->integer #\d) =>  100
(integer->char 50)  =>  #\2
```

Se puede convertir un string en una lista de caracteres:

```scheme
(string->list "hello") =>  (#\h #\e #\l #\l #\o)
```

Otros procedimientos de conversión del estilo son:

* `list‑>string`
* `vector‑>list`
* `list‑>vector`

Se pueden convertir números a atrings:

```scheme
(number->string 16) =>  "16"
```

Se pueden convertir strings a números; si el string no corresponde a un número se retorna `#f`:

```scheme
string->number "16")
=>  16

(string->number "Soy un numero?")
=>  #f
```

El procedimiento `string‑>number`toma un argumento adicional que es la base numérica:

```scheme
(string->number "16" 8) =>  14
```

porque `16` en base 8 es el número catorce (en decimal).

Los símbolos se pueden convertir en strings y viceversa:

```scheme
(symbol->string 'simbolo)
=>  "simbolo"

(string->symbol "cadena")
=>  cadena
```

## 2.3. Otros tipos de datos

Existen otros tipos de datoe en Scheme.  Uno es el **procedimiento** (*procedure*).  Ya nos hemos topado
con muchos procedimientos: ej. `display`, `+`, `cons`.  En realidad, esos son variables que
referencias los valores de tipo procedimiento. Los valores de los procedimiento no son visibles como los números y cadenas:

```scheme
cons
=>  <procedure>
```

Los procedimientos que hemos visto hasta ahora son procedimientos *primitivos*, y sus variables son
nombres globales estándar que los referencian.  Los usuarios pueden crear valores adicionales.

Otro tipo de dato es el **puerto** (*port*). Un puerto es el conducto a través del cual se realizan las operaciones de entrada y salida.  Los puertos se asocian usualmente con archivos y consolas.

En el programa "Hola, mundo!" usamos el procedimiento `display` para escribir el string en la consola.
El `display`puede tomar dos argumentos: el primero es el valor a mostrar, y el segundo el puerto de salida donde se debe mostrar.

En nuestro programa, el segundo argumento de `display` estaba implícito.  El puerto de salida predeterminado es el puerto de salida estándar.  Podemos averiguar cuál es el puerto estándar de salida mediante la llamada al procedimiento `(current‑output‑port)`.  Al escribir el `display`, podríamos haber sido más explícitos, con:

```scheme
(display "Hello, World!" (current-output-port))
```


## 2.4. *S-expressions*

Todos los tipos de datos que hemos abarcado hasta aquí se pueden poner como parte de un tipo de dato que los engloba a todos denominado *s-expression* (*s* de *symbolic*).

Entonces `42`, `#\c`, `(1 . 2)`, `#(a b c)`, `"Hola"`, `(quote xyz)`, `(string‑>number "16")`, y `(begin (display "Hola, mundo!") (newline))` son todos ellos *s-expressions*.


