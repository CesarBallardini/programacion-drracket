# Teach Yourself Scheme in Fixnum Days - Capítulo 4 - Condicionales

Como todos los lenguajes, Scheme proporciona *condicionales*. La forma básica es el `if`:

```scheme
(if test-expression
    then-branch
    else-branch)
```

Si la `test-expression` evalúa a verdadero  (o sea, cualquier valor distinto de `#f`), entonces se
evalúa la *s-expression* `then-branch`.  Sino, se evalúa la *s-expression* `else-branch`. La rama "else" es opcional.

```scheme
(define p 80)

(if (> p 70) 
    'seguro
    'inseguro)
=>  seguro

(if (< p 90)
    'baja-presion) ;no hay rama "else"
=>  baja-presion
```

Scheme proporciona otras formas condicionales por su conveniencia. 

## 4.1. `when` y `unless`

Cuando se necesita sólo una de las ramas del `if` pueden resultar convenientes `when` y `unless`.

```scheme
(when (< (presion tubo) 60)
   (abre-valvula tubo)
   (conecta bomba-de-piso tubo)
   (pulsa bomba-de-piso 5)
   (desconecta bomba-de-piso tubo)
   (cierra-valvula tubo))
```

Si suponemos que la `presion` del `tubo` es menor a `60`, este condicional va a conectar la `bomba-de-piso` 
al `tubo` y la `pulsa` `5` veces.  (`conecta` y `pulsa` son procedimientos apropiados)

El mismo programa usando `if` sería:

```scheme
(if (< (presion tubo) 60)
  (begin
   (abre-valvula tubo)
   (conecta bomba-de-piso tubo)
   (pulsa bomba-de-piso 5)
   (desconecta bomba-de-piso tubo)
   (cierra-valvula tubo)))
```

Se advierte que la rama del `when` tiene un `begin` implícito, mientras que el `if` requiere un `begin`explícito, sino sus ramas tienen más de una forma.

El mismo comportamiento se puede expresar mediante `unless`:


```scheme
(unless (>= (presion tubo) 60)
   (abre-valvula tubo)
   (conecta bomba-de-piso tubo)
   (pulsa bomba-de-piso 5)
   (desconecta bomba-de-piso tubo)
   (cierra-valvula tubo))
```

No todos los Scheme proporcionan `when` y `unless`.  Racket los tiene.  Si el Scheme que usas no los tiene, se pueden definir mediante macros.

```scheme
if (char<? c #\c) -1
    (if (char=? c #\c) 0
        1))
```

se puede rescribir con `cond` como:

```scheme
(cond ((char<? c #\c) -1)
      ((char=? c #\c) 0)
      (else 1))
```

La forma `cond` es un condicional *multi-rama*.  Cada cláusula tiene una condición y una acción asociada.
La primera condición que da verdadero dispara su acción asociada.  La cláusula final `else` se selecciona
si ninguna de las conciones precedentes tuvo éxito.

Las acciones del `cond` tienen `begin` implícito.

##  4.3. `case`

Hay un caso especial de `cond` que se puede condensar en una expresión `case`: cuando todas las condiciones
son comprobaciones de pertenencia:

```scheme
(case c
  ((#\a) 1)
  ((#\b) 2)
  ((#\c) 3)
  (else 4))
=>  3
```

La cláusula seleccionada es aquella cuya cabeza contiene el valor de `c`.

## 4.4. `and` y `or`

Scheme proporciona formas especiales para la conjunción (*and*) y la disjunción (*or*).  Ya hemos visto que
el `not` es un procedimiento.

La forma especial `and` retorna verdadero si todas sus subformas son verdadero.  El valor retornado es el 
valor de la subforma final.  Si cualquiera de las subformas es falso, `and` retorna `#f`.

```scheme
(and 1 2)  =>  2
(and #f 1) =>  #f
```


La forma especial `or` retorna el valor de la primer subforma que devuelve verdadero.  Si todas sus subformas son  falso, `or` retorna `#f`.


```scheme
(or 1 2)  =>  1
(or #f 1) =>  1
```

Tanto `and` como `or` evalúan sus subformas de izquierda a derecha.  Tan pronto como se puede determinar el resultado, las restantes subformas serán ignoradas.


```scheme
(and 1 #f expression-guaranteed-to-cause-error)
=>  #f

(or 1 #f expression-guaranteed-to-cause-error)
=>  1
```
