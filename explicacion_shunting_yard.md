# Algoritmo de Shunting Yard aplicado a expresiones regulares

## ¿Qué hace?

El algoritmo de Shunting Yard (creado por Edsger Dijkstra) convierte una
expresión en notación **infix** (donde los operadores van entre los
operandos, como el álgebra normal) a notación **postfix** o notación
polaca inversa (RPN), donde los operadores van *después* de sus operandos.

La notación postfix es útil porque no necesita paréntesis ni reglas de
precedencia para evaluarse: se procesa de izquierda a derecha con una
simple pila.

## ¿Por qué sirve para expresiones regulares?

Una expresión regular como `a(a|b)*b` es en el fondo una expresión
algebraica donde:

- La **concatenación** (poner un símbolo junto a otro) es un operador
  implícito (no se escribe, pero existe).
- `|` es el operador de **alternancia** (equivalente a la suma `+` en álgebra).
- `*`, `+`, `?` son operadores de **repetición** (unarios, se escriben
  después del operando).
- `(` y `)` agrupan sub-expresiones, igual que en álgebra.

Convertir la expresión a postfix es el primer paso clásico para luego
construir un autómata (Thompson NFA) a partir de la regex, ya que la
postfix se puede recorrer con una pila para ir armando los fragmentos del
autómata sin preocuparse por precedencia ni paréntesis.

## Precedencia de operadores (de menor a mayor)

1. `|` (alternancia) — precedencia más baja
2. `·` (concatenación, implícita — no se escribe en la expresión original)
3. `*`, `+`, `?`, `{n,m}` (repetición) — precedencia más alta

Todos son **asociativos por la izquierda**.

## Pasos del algoritmo

1. **Insertar concatenación explícita.** Como la concatenación no tiene
   símbolo propio, primero se recorre la expresión y se inserta un
   operador de concatenación "invisible" entre dos símbolos que están
   pegados y deberían concatenarse (por ejemplo entre `a` y `(` en `a(b)`,
   o entre `)` y `b` en `(a)b`).

2. **Recorrer token por token** la expresión (ya con la concatenación
   insertada), usando una **pila de operadores** y una **lista de salida**
   (la postfix):

   - Si el token es un **operando** (una letra, un carácter escapado, una
     clase de caracteres `[..]`, el comodín `.`), se agrega directo a la
     salida.
   - Si el token es `(`, se empuja a la pila.
   - Si el token es `)`, se sacan operadores de la pila hacia la salida
     hasta encontrar el `(` correspondiente, y se descarta ese `(`.
   - Si el token es un **operador** (`|`, `·`, `*`, `+`, `?`, `{n,m}`), se
     sacan de la pila hacia la salida todos los operadores del tope que
     tengan **precedencia mayor o igual** a la del operador actual, y
     luego se empuja el operador actual a la pila.

3. **Al terminar de leer la expresión**, se sacan todos los operadores que
   queden en la pila y se agregan a la salida, en orden.

El resultado final en la lista de salida es la expresión en postfix.

## Ejemplo rápido

Expresión infix: `a(a|b)*b`

Con concatenación explícita: `a · ( a | b ) * · b`

Postfix resultante: `a a b | * · b ·`

## Detalles adicionales implementados

- **Caracteres escapados (`\`)**: cualquier `\` debe ir seguido de un
  carácter válido para escapar; si `\` aparece al final de la línea o sin
  nada después, se reporta como error de expresión mal formada (no se
  procesa como operando).
- **Clases de caracteres `[...]`**: todo lo que está entre `[` y `]` se
  trata como un solo operando atómico (no se le aplican reglas de
  precedencia internas).
- **Cuantificadores `+` y `?`**: se tratan igual que `*`, como operadores
  unarios postfijos de la más alta precedencia.
