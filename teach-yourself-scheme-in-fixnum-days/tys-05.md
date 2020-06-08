# Teach Yourself Scheme in Fixnum Days - Capítulo 5 - Variables léxicas

Las variables en Scheme tienen alcance léxico, o sea, son visibles sólo para las formas dentro de cierta región contigua del texto del programa. Las variables *globales* que hemos visto hasta ahora no son la excepción: su alcance es todo el texto del programa, el cual ciertamente es contiguo.

También hemos visto algunos ejemplos de variables *locales*.  En particular los parámetros de la forma `lambda`,
que se *ligan* o cada vez que se invoca el procedimiento, y cuyo alcance es el cuerpo del procedimiento.  Por ejemplo:

```scheme
(define x 9)
(define add2 (lambda (x) (+ x 2)))

x        =>  9

(add2 3) =>  5
(add2 x) =>  11

x        =>  9
```

Aquí vemos que hay una variable global `x` y una variable local `x`, esta última introducida por el procedimiento `add2`.  La variable global siempre vale 9.  La `x` local se liga a 3 en la primer llamada a `add2` y a 9 (el valor de la variable global `x`) en la segunda llamada a `add2`. Cuando el procedimiento retorna, la variable global `x` continúa valiendo 9.

La forma `set!` modifica la ligadura léxica de una variable:

```scheme
(set! x 20)
```

modifica la ligadura global de `x` de 9 a 20, porque ésa es la ligadura de `x` que es visible para `set!`.  Si `set!` estuviera dentro del cuerpo de `add2`, hubiera modificado el valor local de `x`:

```scheme
(define add2
  (lambda (x)
    (set! x (+ x 2))
    x))
```

El `set!` aquí suma 2 a la variable local `x` y el procedimiento retorna el nuevo valor de la `x` local.  (En términos de efectos, este programa es indistinguible del anterior.) Podemos invocar `add2` sobre la `x` global, como antes:

```scheme
(add2 x) =>  22
```

(Recuerde que la `x` global vale 20 ahora, no 9.)

El `set!` dentro de `add2` afecta solamente a la variable local que usa `add2`.  Aunque la variable local obtuvo su liga a partir del valor de la variable global `x`, la global no es afectada por el `set!` a la local.

```scheme
x =>  20
```

Debe notar que tenemos toda esta discusión porque utilizamos el mismo identificador para una variable local y una gloabl.  En cualquier texto, un identificador llamado `x` se refiere a la variable `x` más cercana en términos léxicos.  Esta ocurrencia va a *hacer sombra* (*shadow*) a cualquier `x` global.  Por ejemplo, la `x` de `add2` hace sombra a la `x` global.

El cuerpo de un procedimiento puede acceder y modificar variables del alcance que lo rodea, siempre que los parámetros del procedimiento no les hagan sombra.  Esto puede dar lugar a programas interesantes:

```scheme
define contador 0)

(define inc-contador
  (lambda ()
    (set! contador (+ contador 1))
    contador))
```

El procedimiento `inc-contador` no tiene ningún argumento (también llamados *thunk* https://en.wikipedia.org/wiki/Thunk). No introduce ninguna variable local, y por lo tanto no puede hacer sombra a ninguna global.  Cada vez que se lo invoca, modifica la variable *global* `contador` --la incrementa en 1-- y retorna su valor actual.  A continuación se muestra una sucesión de llamadas a `inc-contador`:

```scheme
(inc-contador) => 1
(inc-contador) => 2
(inc-contador) => 3
```

## 5.1. `let` y `let*`

Se pueden crear variables locales sin necesidad de crear explícitamente un procedimiento.  La forma especial `let` introduce una lista de variables para utilizar dentro de su cuerpo:

```scheme
(let ((x 1)
      (y 2)
      (z 3))
  (list x y z))
=>  (1 2 3)
```

Así como con `lambda`, dentro del cuerpo del `let`, la `x` local (ligada a 1) hace sombra a la `x` global (que está ligada a 20).

Las inicializaciones de variables locales --`x` a 1; `y` a 2; `z` a 3-- no se consideran parte del cuerpo del `let`.  Por lo tanto, una referencia a `x` en la inicialización se va a resolver con la `x` global, no la local:

```scheme
(let ((x 1)
      (y x))
  (+ x y))
=>  21
```

Esto es así porque `x` se liga a 1, y la variable `y` se liga a la `x` *global*, que vale 20.

Algunas veces resulta conveniente que la lista de variables del `let` se introduzca en secuencia, de tal manera que la inicialización de variables que aparecen después puedan usar el alcance léxico de las precedentes.  La forma especial `let*` hace justamente eso:

```scheme
(let* ((x 1)
       (y x))
  (+ x y))
=>  2
```

La `x` del `let*` se liga a 1.  La `y` del `let*` usa el valor de `x` recién asignado.  Se puede usar `let` para conseguir ese efecto con `let` anidados:

```scheme
(let ((x 1))
  (let ((y x))
    (+ x y)))
=>  2
```

En efecto, `let*` es una abreviatura conveniente para esos `let` anidados.

Los valores ligados a las variables léxicas pueden ser procedimientos:

```scheme
(let ((cons (lambda (x y) (+ x y))))
  (cons 1 2))
=>  3
```

Dentro del cuerpo del `let`, el `cons` suma sus argumentos, fuera continúa creando pares.

