# Teach Yourself Scheme in Fixnum Days - Capítulo 3 - Formas

El lector puede advertir que los programas Scheme que vimos hasta ahora son también *s-expressions*.  Esto es cierto para todos los programas Scheme: *los programas son datos*.


Por ello, el dato de tipo carácter `#\c` es un programa, o una *forma*.  Usaremos el término más general *forma* en vez de *programa*, de manera de poder hablar también de fragmentos de programas.

Scheme evalúa la forma `#\c` al valor `#\c`, porque `#\c` es autoevaluante.  No todas las *s-expressions* son autoevaluantes.  Por ejemplo el símbolo `xyz` evalúa al valor que referencia la *variable* `xyz`.  La *s-expression* tipo lista `(string‑>number "16")` evalúa al número 16.

No todas las *s-expressions* son programas válidos.  Si tratamos de evaluar la *s-expression* tipo par `(1 . 2)` obtendremos un error.

Scheme evalúa las formas lista de la siguiente manera: examina el primer elemento o *cabeza* de la forma.  Si la cabeza *evalúa* a un procedimiento, se *evalúa* el resto de la forma para obtener los argumentos del procedimiento, y luego se *aplica* el procedimiento a los argumentos.

Si la cabeza de la forma es una *forma especial*, la evaluación procede de una manera ideosincrática de esa forma en particular.  Hemos visto algunas formas especiales como `begin`, `define` y `set!`.  La forma `begin` hace que sus subformas se evalúen en orden, y el resultado de toda la forma es el resultado de la última subforma.  La forma `define` introduce e inicializa una variable.  La forma `set!` cambia el valor asociado a una variable.

## 3.1. Procedimientos

Hemos visto unos cuantos procedimientos primitivos de Scheme: `cons`, `string‑>list`, etc.  Los usuarios pueden crear sus propios procedimientos mediante la forma especial `lambda`.  Por ejemplo, la siguiente expresión define un procedimiento que suma 2 a su argumento:

```scheme
(lambda (x) (+ x 2))
```

La primera subforma `(x)` es la lista de parámetros.  Las restantes subformas constituyen el cuerpo del procedimiento.  El procedimiento se puede invocar con un argumento, igual que si fuera un procedimiento primitivo:

```scheme
((lambda (x) (+ x 2)) 5)
=>  7
```

Si necesitamos invocar el procedimiento más de una vez, podemos crear réplicas mediante `lambda` cada vez que lo necesitamos, pero hay una manera más conveniente: podemos utilizar una variable para referenciar el valor de tipo procedimiento.

```scheme
(define add2
  (lambda (x) (+ x 2)))
```

Podemos usar la variable `add2` cada vez que necesitamos invocar el procedimiento que suma 2 a su argumento:

```scheme
(add2 4) =>  6
(add2 9) =>  11
```


### 3.3.3 Parámetros del procedimiento

Los parámetros de un procedimiento `lambda` se especifican en la primera subforma (la forma que sigue inmediatamente a la cabeza, que es el símbolo `lambda`).  El procedimiento `add2` es un procedimiento *unario*: tiene un solo argumento, y por lo tanto la lista de parámetros tiene un solo elemento, `(x)`.  El símbolo `x` actúa como una variable que referencia el argumento del procedimiento.  Cada ocurrencia de `x` en el cuerpo del procedimiento se refiere al argumento del procedimiento.  Se dice que la variable `x` es *local* al cuerpo del procedimiento.

Podemos utilizar listas de dos elementos para procedimientos de dos argumentos, y en general, listas de *n* elementos para procedimientos de *n* argumentos.  El que sigue es un procedimiento de dos argumentos que calcula el área de un rectángulo.  Sus dos argumentos son el ancho y alto del rectángulo.

```scheme
(define area
  (lambda (ancho alto)
    (* ancho alto)))
```

Ahora bien, este procedimiento lo único que hace es multiplicar sus argumentos, que es justamente lo que hace el procedimiento primitivo `*`.  Entonces, podríamos haberlo escrito simplemente como:

```scheme
(define area *)
```

### 3.1.2. Cantidad variable de argumentos

Algunos procedimientos se pueden llamar unas veces con cierta cantidad de argumentos, y otras veces con otra cantidad.  PAra lograr esto, la lista de parámetros de la forma `lambda` se reemplaza por un único símbolo.  Este símbolo actúa como variable y se le asocia la lista de parámetros efectivamente pasados al procedimiento en la invocación.

En general, la lista de parámetros de `lambda` puede ser una lista de la forma `(x ...)`, un símbolo, o un par de la forma `(x ...  . z)`.  En el caso el par, todas las variables antes del punto se ligan a los correspondientes argumentos en la llamada al procedimiento, y la variable que queda después el punto recoge el resto de los argumentos de la invocación en una lista.

## 3.2. Apply

El procedimiento `apply` nos permite aplicar un procedimiento a una lista de argumentos:

```scheme
(define x '(1 2 3))

(apply + x)
=>  6
```

En general, `apply` toma un procedimiento, seguido por una cantidad indeterminada de otros argumentos, el último de los cuales debe ser una lista.  Para construir la lista de argumentos al procedimiento, `apply` concatena los argumentos sueltos con la lista del último.  Luego retorna el resultado de llamar al procedimiento con la lista de los argumentos. Ejemplo:

```scheme
(apply + 1 2 3 x)
=>  12
```

## 3.3. Secuencias


Hemos usado `begin` para agrupar subformas que debían evaluarse en secuencia.  Muchas formas Scheme tienen `begin` *implícitos*. Por ejemplo, vamos a definir un procedimiento que muestra sus tres argumentos, con espacios entre medio.  Una definición posible es:


```scheme
(define display3
  (lambda (arg1 arg2 arg3)
    (begin
      (display arg1)
      (display " ")
      (display arg2)
      (display " ")
      (display arg3)
      (newline))))
```

En Scheme, los cuerpos de las formas `lambda` son `begin` implícitos. Por lo tanto, no es necesario el `begin` en el cuerpo de `display3`.  No es un error usar el `begin`, pero se puede escribir `display3` de manera más simple:


```scheme
(define display3
  (lambda (arg1 arg2 arg3)
    (display arg1)
    (display " ")
    (display arg2)
    (display " ")
    (display arg3)
    (newline)))
```
