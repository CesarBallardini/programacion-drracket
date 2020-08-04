# Teach Yourself Scheme in Fixnum Days - Capítulo 6 - Recursión


El cuerpo de un procedimiento puede contener llamadas a otros procedimientos, también a sí mismo:

```scheme
(define factorial
  (lambda (n)
    (if (= n 0) 1
        (* n (factorial (- n 1))))))
```

Este procedimiento *recursivo* calcula el *factorial* de un número.  Si el número es 0, la respuesta es 1.  PAra cualquier otro número, el procedimiento se usa a sí mismo para calcular el factorial de *n-1*, multiplica el resultado por *n* y retorna el producto.

Se pueden escribir procedimientos mutuamente recursivos.  Los siguientes predicados para paridad e imparidad se usan entre sí:

```scheme
(define par?
  (lambda (n)
    (if (= n 0) #t
        (impar? (- n 1)))))

(define impar?
  (lambda (n)
    (if (= n 0) #f
        (par? (- n 1)))))
```

Estas definiciones se ofrecen como ejemplos de recursión mutua.  Scheme proporciona predicados primitivos `even?` y `odd?`  para comprobar si es respectivamente par o impar un número.

## 6.1. `letrec`

Si necesitamos los procedimientos anteriores como variables locales, podríamos hacer el intento de usar la forma especial `let`:

```scheme
(let ((local-par? (lambda (n)
                     (if (= n 0) #t
                         (local-impar? (- n 1)))))
      (local-impar? (lambda (n)
                    (if (= n 0) #f
                        (local-par? (- n 1))))))
  (list (local-par? 23) (local-impar? 23)))
```

Esto no funcionará. Las inicializaciones de `local-par?` y `local-impar?` no se refieren a las mismas variables léxicas.  Tampoco funcionará si usamos `let*` en lugar de `let`, porque si bien ahora el `local-par?` en el cuerpo de `local-impar?` apunta al procedimiento correcto, el `local-impar?` dentro de `local-par?` apunta a otro lado.

Para resolver los problemas de esta clase, Scheme proporciona la forma especial `letrec`:

```scheme
(letrec ((local-par? (lambda (n)
                      (if (= n 0) #t
                       (local-impar? (- n 1)))))
        (local-impar? (lambda (n)
                       (if (= n 0) #f
                        (local-par? (- n 1))))))
  (list (local-par? 23) (local-impar? 23)))
```

Las variables léxicas que introduce `letrec` no sólo son visibles en el cuerpo del `letrec`, sino también en todas las inicializaciones.  O sea, `letrec` está diseñado para definir procedimientos locales recursivos y mutuamente recursivos.


## 6.2. `let` nominado

Un procedimiento definido mediante `letrec` puede describir bucles.  Digamos, por ejemplo, que queremos contar en forma descendente desde 10:

```scheme
(letrec ((countdown (lambda (i)
                      (if (= i 0) 'despegue
                          (begin
                            (display i)
                            (newline)
                            (countdown (- i 1)))))))
  (countdown 10))
```

Esto muestra en la consola los números desde el 10 al 1 y retorna el resultado `despegue`

Scheme permite una variante de `let` llamado `let` *nominado* (*named* `let`) para escribir esta clase de bucles de manera más compacta:

```scheme
(let countdown ((i 10))
  (if (= i 0) 'despegue
      (begin
        (display i)
        (newline)
        (countdown (- i 1)))))
```

Debe advertir la presencia de la variable `i` que identifica el bucle, inmediatamente a continuación de `let`.  Este programa es equivalente al escrito con `letrec`.


## 6.3. Iteración

El procedimiento `countdown` definido más arriba es en realidad recursivo.  En Scheme los bucles sólo se pueden definir mediante la recursión.  No existen construcciones epeciales para bucles o iteración.

Sin importar esto, el bucle definido arriba es un bucle *genuino*, que funciona de la manera exacta en que funcionan los bucles en otros lenguajes de programación.  Scheme tiene en cuenta de manera especial estos casos de recursión para que no generen el trabajo extra de *call/return*.

El mecanismo que usa Scheme para lograr esto se denomina *eliminación de llamadas de cola* (*tail-call elimination*).  Si mira con atención el procedimiento  `countdown`, verá que la llamada recursiva se da en el cuerpo del procedimiento, pero es la *llamada de cola*, la última cosa que que se hace --cada llamada a `countdown o bien no se llama a sí mismo, o bien cuando lo hace, es la última cosa que hace.  Para la implementación de Scheme, esto hace que la recursión indistinguible de la iteración.  Así que ¡adelante!, escribe los bucles usando recursión. Es correcto.

Veamos otro ejemplo de un útil procedimiento recursivo de cola:

```scheme
(define posicion
  (lambda (o l)
    (let loop ((i 0) (l l))
      (if (null? l) #f
          (if (eqv? (car l) o) i
              (loop (+ i 1) (cdr l)))))))
```

`posicion` encuentra el índice de la primera ocurrencia del objeto `o` en la lista `l`.  Si el objeto no se encuentra en la lista, el procedimiento retorna `#f`.


Aquí hay otro procedimiento recursivo de cola, uno que invierte su argumento *in-situ*, o sea, mutando el contenido de la lista existente, sin generar una nueva:

```scheme
(define reverse!
  (lambda (s)
    (let loop ((s s) (r '()))
      (if (null? s) r
	  (let ((d (cdr s)))
            (set-cdr! s r)
	    (loop d s))))))
```


## 6.4. *Mapear* un procedimiento sobre una lista

Hay una clase especial de iteración que involucra repetir la misma acción en cada elemento de una lista.
Scheme proporciona dos procedimientos para este trabajo: `map` y `fo-each`.

El procedimiento `map` aplica un dado procedimiento a cada elemento de una lista dada, y retorna la lista de resultados:

```scheme
(map add2 '(1 2 3))
=>  (3 4 5)
```

El procedimiento `for-each` también aplica el procedimiento dado a cada elemento de la lista dada, pero retorna **void**.  La aplicación de este procedimiento se hace con la única finalidad de conseguir los efectos colaterales (*side-effects*) que puede provocar:

```scheme
(for-each display
  (list "one " "two " "buckle my shoe"))
```

tiene el efecto colateral de mostrar las cadenas (en el orden en el cual aparecen) en la consola.

No es necesario que los procedimientos aplicados por `map` y por  `for-each` sean de un solo argumento.
Por ejemplo, dado un procedimiento de *n* argumentos, `map` toma *n* listas y aplica el procedimiento a cada conjunto de *n* argumentos tomados uno de cada lista:

```scheme
(map cons '(1 2 3) '(10 20 30))
=>  ((1 . 10) (2 . 20) (3 . 30))

(map + '(1 2 3) '(10 20 30))
=>  (11 22 33)
```


