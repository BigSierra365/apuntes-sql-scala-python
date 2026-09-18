# Scala — Programación Funcional e Imperativa — Chuleta de examen

> **Cómo usar esto en el examen:** 1) busca la técnica que necesitas en la matriz de abajo → 2) salta a la chuleta rápida o al ejercicio correspondiente → 3) mira el enunciado y adapta el patrón al problema de tu examen. No lo leas de arriba a abajo, úsalo como diccionario de rescate.

---

# 1. Matrices de referencia por tipo de técnica

Divididas en subíndices — busca primero el bloque (Arrays, Listas, Funciones...) y luego la fila.

### 1.1 Variables, Tipos y Estilo (DataCamp Ch. 1)

| Técnica | Para qué sirve | Resuelta en |
| --- | --- | --- |
| Tipado estático e Inferencia | Scala verifica tipos al compilar, pero los deduce si se omiten para limpiar el código.

 | Ejercicio 1 |
| Inmutabilidad (`val`) vs Mutabilidad (`var`) | `val` previene reasignaciones (funcional). `var` permite reasignar (imperativo).

 | Ejercicio 1, 2 |
| Tipos Numéricos y Precisión | Modelar datos (`Int`, `Double`, `Float`, `Boolean`, `String`).

 | Ejercicio 3 |

### 1.2 Funciones y Efectos Secundarios (DataCamp Ch. 2 & 3)

| Técnica | Para qué sirve | Resuelta en |
| --- | --- | --- |
| Funciones puras (`def`) | Empaquetar lógica sin modificar variables externas. El último valor es el `return` automático.

 | Ejercicio 4 |
| `if` / `else` como expresiones | En Scala, `if` devuelve un valor directamente que puede ser asignado a un `val`.

 | Ejercicio 5, 6 |
| Efectos Secundarios (*Side Effects*) | Funciones que modifican estado externo (`var` globales). Rompen el paradigma funcional puro.

 | Ejercicio 14 |

### 1.3 Arrays (Estilo Imperativo - DataCamp Ch. 2)

| Técnica | Para qué sirve | Resuelta en |
| --- | --- | --- |
| Instanciación e indexación | `Array(...)` o `new Array[T](n)`. Indexación basada en paréntesis `()`, no en corchetes `[]`.

 | Ejercicio 7, 8 |
| Modificación *in-place* | Cambiar el valor de un índice existente `arr(0) = X` (permitido aunque el Array sea `val`).

 | Ejercicio 7 |
| Bucle `while` | Recorrer el array usando un contador `var i = 0` externo (enfoque imperativo).

 | Ejercicio 9, 13 |

### 1.4 Listas (Estilo Funcional - DataCamp Ch. 2)

| Técnica | Para qué sirve | Resuelta en |
| --- | --- | --- |
| Instanciación e Inmutabilidad | Colección inmutable. Modificarla exige crear una lista nueva en memoria compartiendo la base.

 | Ejercicio 10 |
| *Prepend* (`::` / Cons) y `Nil` | Añadir elementos al principio de la lista. `Nil` representa la lista vacía.

 | Ejercicio 10, 11 |
| Concatenación (`:::`) | Unir dos listas distintas generando una tercera.

 | Ejercicio 11 |
| Bucle `foreach` | Iterar sobre la colección pasando una función anónima, sin usar contadores.

 | Ejercicio 13 |

### 1.5 Lógica, Control de Flujo e Integración

| Técnica | Para qué sirve | Resuelta en |
| --- | --- | --- |
| Operadores relacionales/lógicos | `<`, `>`, `==`, `!=`, `&&` (AND de cortocircuito), ` |  |
| Programa Integrado (Torneo) | Combinar colecciones, bucles, lógicas complejas y funciones propias en un *script* estructurado.

 | Ejercicio 15 |

---

# 2. Chuleta de sintaxis rápida (Estilo DataCamp)

## 2.1 Scala Basics: Sistema de Tipos y Variables

```scala
// Tipado Estático vs Dinámico (DataCamp Ch. 3)[cite: 5]
// Scala verifica tipos al compilar, pero usa inferencia para ahorrar código.
val nombre = "Marta"            // Inferencia automática a String
val partidas: Int = 10          // Tipado estático explícito (obligatorio en parámetros de funciones)

// Inmutabilidad (val) vs Mutabilidad (var) (DataCamp Ch. 1)[cite: 5]
// Regla de oro: Usa 'val' SIEMPRE por defecto. Usa 'var' SOLO si es estrictamente necesario.
val inmutable = 100
// inmutable = 200              // ERROR FATAL: reassignment to val
var mutable = 10
mutable = 15                    // OK: var permite reasignar el puntero en memoria

// Tipos numéricos y literales
val decimalD: Double = 3.14159  // Precisión alta por defecto (64 bits)
val decimalF: Float = 3.14f     // Requiere 'f' al final para compilar (32 bits)

```

---

## 2.2 Funciones y Efectos Secundarios (Side Effects)

```scala
// Función Pura (DataCamp Ch. 2): Solo depende de sus inputs, no altera nada externo[cite: 5].
def bust(puntuacion: Int): Boolean = {
  puntuacion > 21               // En Scala no se usa 'return'. La última línea es el retorno implícito.
}

// Función Impura (Efecto Secundario): Modifica estado externo (Estilo imperativo).
var total = 0
def sumarAlTotal(valor: Int): Unit = {  // Unit equivale a 'void' en otros lenguajes
  total = total + valor         // Modifica 'total' fuera de su scope (SIDE EFFECT)
}

```

---

## 2.3 Estructuras de control (If/Else es una expresión)

```scala
// 'if' en Scala es una EXPRESIÓN, devuelve un valor que puede guardarse (DataCamp Ch. 3)[cite: 5].
val handA = 20
val handB = 18

val mejor = if (handA > 21 && handB > 21) 0
            else if (handA > handB) handA
            else handB          // Siempre debe tener un 'else' si se asigna a una variable

```

---

## 2.4 Arrays (Estilo Imperativo: Tamaño Fijo, Mutables)

```scala
// Creación e inicialización de golpe (DataCamp Ch. 2)[cite: 5]
val jugadores = Array("Alex", "Chen", "Marta")

// Crear y parametrizar un array vacío[cite: 5]
val puntuaciones = new Array[Int](4) // Crea [0, 0, 0, 0] por defecto (ceros para numéricos)

// Updating arrays (Mutabilidad interna)[cite: 5]
jugadores(0) = "Sindhu"         // OJO: Se usan PARÉNTESIS (). Válido aunque jugadores sea 'val'.
// jugadores(1) = 500           // ERROR: El sistema estático no deja meter Int en Array[String]

val longitud = jugadores.length // Propiedad, no método (sin paréntesis)

```

---

## 2.5 Listas (Estilo Funcional: Inmutables)

```scala
// Creación básica[cite: 5]
val estudiantes = List("Ana", "Luis", "Marta")

// Initialize a list using cons (::) and Nil (DataCamp Ch. 2)[cite: 5]
// 'Nil' es la lista vacía. El operador '::' lee de DERECHA a IZQUIERDA.
val listaManual = "Marta" :: "Luis" :: "Ana" :: Nil

// Prepend (Añadir al principio)
val nuevaLista = "Carlos" :: estudiantes // 'estudiantes' original NO se modifica en absoluto

// Concatenate Lists (:::)[cite: 5]
val grupoA = List(1, 2)
val grupoB = List(3, 4)
val unidos = grupoA ::: grupoB           // Resulta en List(1, 2, 3, 4)

```

---

## 2.6 Bucles y Estilo (Imperativo vs Funcional)

```scala
// while and the imperative style (DataCamp Ch. 3)[cite: 5]
// Requiere contadores mutables ('var') y gestión manual de índices. Propenso a bucles infinitos.
val manos = Array(17, 24, 21)
var i = 0
while (i < manos.length) {
  println(manos(i))
  i += 1                        // Scala no soporta i++
}

// foreach and the functional style (DataCamp Ch. 3)[cite: 5]
// Delega la iteración a la colección. Código limpio, inmutable y seguro.
manos.foreach { mano =>
  println(mano)
}

```

---

# 3. Ejercicios Práctica 1 (Parte 2)

## Sección 1. Variables, Tipos e Inferencia

### Ejercicio 1 — Variables, tipos e inferencia

Crea variables que representen la información de una partida de cartas (jugador, partidas, puntuación, % victorias, activo). Realiza una versión con declaración explícita y otra con inferencia.

* **Técnicas:** Inferencia de tipos, declaración estática explícita, `val` vs `var`.

**SOLUCIÓN:**

```scala
// Versión A: Declaración explícita
val nombreA: String = "Marta"
val partidasA: Int = 15
var puntuacionA: Int = 1250
var porcentajeVictoriasA: Double = 65.5
var sigueActivoA: Boolean = true

// Versión B: Inferencia de tipos
val nombreB = "Alex"
val partidasB = 8
var puntuacionB = 980
var porcentajeVictoriasB = 55.2
var sigueActivoB = false

println(s"Jugador A: $nombreA | Puntos: $puntuacionA")
println(s"Jugador B: $nombreB | Puntos: $puntuacionB")

```

**Explicación:**

* Scala deduce (`infiere`) que `nombreB` es de tipo `String` por las comillas, `partidasB` es `Int` por ser un entero, `porcentajeVictoriasB` es `Double` por el punto decimal, y `sigueActivoB` es `Boolean`.
* La versión A es rígida pero protege contra errores humanos; la B reduce el *boilerplate* visual y es el estándar de la industria.
* **Tip (`val` vs `var`):** Todo lo que represente un estado histórico inicial o identificador (nombre, partidas jugadas) debe ser `val`. Los contadores dinámicos que se alteran en la partida (puntuación) deben ser `var`.
* ⚠️ **Trampa técnica:** Intentar inferir un `Float` escribiendo `var porcentaje = 65.5` asignará un `Double` por defecto; para forzar `Float` por inferencia, debes añadir el sufijo `f`: `var porcentaje = 65.5f`.

**Comentario:**
Implementé dos bloques declarativos contrastando el tipado explícito frente a la inferencia automática del compilador. Utilicé `val` para atributos inmutables de solo lectura y `var` para aquellos expuestos a actualización en tiempo de ejecución.

---

### Ejercicio 2 — `val`, `var` y reasignación

Crea `val jugador = "Marta"` y `var puntuacion = 10`. Incrementa puntuación dos veces, muestra el resultado e intenta reasignar `jugador` para documentar el error.

* **Técnicas:** Operadores de incremento compuesto (`+=`), protección de reasignación con `val`.

**SOLUCIÓN:**

```scala
val jugador = "Marta"
var puntuacion = 10

puntuacion += 5
puntuacion += 3
println(s"Puntuación final de $jugador: $puntuacion")

// jugador = "Chen"  // Descomentar esta línea causa: "error: reassignment to val"

```

**Explicación:**

* `puntuacion` puede mutar porque se declaró como `var` (*variable*).
* `jugador` rechaza la mutación porque se declaró como `val` (*value*), lo que blinda su referencia en memoria.
* **Tip (Operador de incremento):** Scala no implementa `++` o `--` como C o Java; debes usar explícitamente `+=` o `-=`.

**Comentario:**
Demostré la mutabilidad sumando puntos al contador `var` y evidencié la protección inmutable de `val` que garantiza la pureza del estado inicial frente a reasignaciones accidentales.

---

### Ejercicio 3 — Tipos numéricos y precisión

Declara un número decimal largo usando `Double` y `Float`. Muestra ambos valores y explica qué tipo usarías para casos de negocio.

* **Técnicas:** Truncamiento de precisión IEEE 754, sufijos numéricos.

**SOLUCIÓN:**

```scala
val piLargoDouble: Double = 3.14159265358979323846264338327
val piLargoFloat: Float = 3.14159265358979323846264338327f

println(s"Double: $piLargoDouble") // Imprime: 3.141592653589793
println(s"Float : $piLargoFloat")  // Imprime: 3.1415927

val numeroEstudiantes: Int = 30
val haAprobado: Boolean = true
val nombreCurso: String = "Scala Basics"

```

**Explicación:**

* `Double` utiliza 64 bits y conserva una altísima precisión decimal; `Float` usa 32 bits y trunca agresivamente el número, perdiendo exactitud a partir del séptimo decimal.
* El número de estudiantes exige `Int` (naturaleza discreta), y el estado de aprobación exige `Boolean` (lógica binaria).

**Comentario:**
Analicé el descarte de precisión en memoria instanciando el mismo escalar matemático en arquitecturas de 64 bits (`Double`) y 32 bits (`Float`), prescribiendo `Int` y `Boolean` para la parametrización del negocio.

---

## Sección 2. Funciones Puras y Lógica de Negocio

### Ejercicio 4 — Función para determinar si una mano se pasa de 21

Define una función `bust` que reciba la puntuación como `Int` y devuelva `true` si es mayor que 21 o `false` en caso contrario. No modifiques variables externas.

* **Técnicas:** Definición de funciones (`def`), operador relacional `>`, retorno implícito.

**SOLUCIÓN:**

```scala
def bust(puntuacion: Int): Boolean = {
  puntuacion > 21
}

println(s"Mano 18: ${bust(18)}") // false
println(s"Mano 21: ${bust(21)}") // false
println(s"Mano 22: ${bust(22)}") // true
println(s"Mano 30: ${bust(30)}") // true

```

**Explicación:**

* La función es *pura*: su salida depende exclusivamente de la entrada `puntuacion` y no genera efectos secundarios en el estado global.
* **Tip (Retorno implícito):** En Scala se desaconseja el uso de `return`. La evaluación de la expresión `puntuacion > 21` se devuelve automáticamente al cerrarse el bloque de la función.
* ⚠️ **Trampa técnica:** Omitir el tipo de retorno `: Boolean` en la firma de la función `def bust(puntuacion: Int) = { ... }` obligará a Scala a inferirlo. Aunque es legal, en código de producción los tipos de retorno se escriben explícitamente para documentar la API.

**Comentario:**
Estructuré una función pura de evaluación lógica que comprueba el límite de rotura del juego delegando el valor de verdad en el retorno implícito de Scala.

---

### Ejercicio 5 — Comparación de dos manos

Define una función `maxHand` que reciba dos enteros y devuelva el mayor usando `if` y `else`.

* **Técnicas:** Estructuras condicionales como expresiones, parámetros múltiples.

**SOLUCIÓN:**

```scala
def maxHand(handA: Int, handB: Int): Int = {
  if (handA > handB) handA
  else handB
}

println(s"17 y 19 -> ${maxHand(17, 19)}")
println(s"20 y 18 -> ${maxHand(20, 18)}")
println(s"21 y 21 -> ${maxHand(21, 21)}") // Devuelve 21

```

**Explicación:**

* La estructura `if/else` en Scala se comporta de forma idéntica a un operador ternario en otros lenguajes: evalúa y retorna directamente el bloque ganador.
* Si hay empate (`handA == handB`), la condición lógica salta al `else` devolviendo `handB` (lo cual es matemáticamente correcto porque ambos valen lo mismo).

**Comentario:**
Resolví la extracción de máximos aprovechando la capacidad de Scala para tratar el bloque `if/else` como una expresión final de retorno, asegurando cobertura determinista ante escenarios de empate técnico.

---

### Ejercicio 6 — Decidir el ganador de una partida

Utiliza `bust`. Crea la función `ganador` que reciba dos manos y aplique las reglas de Twenty-One para devolver la puntuación vencedora o `0` si ambos se pasan.

* **Técnicas:** Invocación de funciones de orden inferior, condicionales en cascada (`if / else if / else`), conectores lógicos (`&&`).

**SOLUCIÓN:**

```scala
def ganador(handA: Int, handB: Int): Int = {
  if (bust(handA) && bust(handB)) 0
  else if (bust(handA)) handB
  else if (bust(handB)) handA
  else maxHand(handA, handB)
}

println(s"26 y 20 -> ${ganador(26, 20)}") // Cumple: Solo handA se pasa. Retorna 20.
println(s"18 y 22 -> ${ganador(18, 22)}") // Cumple: Solo handB se pasa. Retorna 18.
println(s"24 y 25 -> ${ganador(24, 25)}") // Cumple: Ambos se pasan. Retorna 0.
println(s"17 y 19 -> ${ganador(17, 19)}") // Cumple: Ninguno se pasa. Retorna 19.
println(s"21 y 20 -> ${ganador(21, 20)}") // Cumple: Ninguno se pasa. Retorna 21.

```

**Explicación:**

* Modularización: La función orquestadora `ganador` invoca internamente a las funciones puras auxiliares `bust` y `maxHand` previamente testeadas, manteniendo el código DRY (*Don't Repeat Yourself*).
* Las condiciones se declaran en orden de exclusión estricta: primero se descarta el doble fallo, luego los fallos individuales y finalmente se deriva al comparador de máximos.
* **Tip (`&&` de cortocircuito):** Scala evalúa `bust(handA) && bust(handB)`. Si el primer operador es falso, interrumpe la ejecución de esa línea y salta al `else if`, ahorrando cómputo.

**Comentario:**
Orquesté la lógica de negocio final construyendo una jerarquía condicional excluyente que reutiliza funciones atómicas puras para resolver la casuística cruzada de la partida de cartas.

---

## Sección 3. Arrays y Listas (Imperativo vs Funcional)

### Ejercicio 7 — Arrays y mutabilidad

Crea `val jugadores = Array("Alex", "Chen", "Marta")`. Sustituye "Alex" por "Sindhu", e intenta asignar el número `500` a una posición.

* **Técnicas:** Instanciación de `Array`, mutación por índice posicional, coerción estática de tipos.

**SOLUCIÓN:**

```scala
val jugadoresArray = Array("Alex", "Chen", "Marta")
println(jugadoresArray.mkString(", "))

jugadoresArray(0) = "Sindhu"
println(jugadoresArray.mkString(", "))

// jugadoresArray(1) = 500 // ERROR: type mismatch

```

**Explicación:**

* A diferencia de Python, Scala indexa con paréntesis `()` en lugar de corchetes `[]` para interactuar con colecciones, ya que internamente es una llamada al método `.apply()` o `.update()`.
* Los `Array` son colecciones de datos contiguos de tamaño fijo en la JVM; modificar una celda interna no reasigna la variable `val`, por lo que es válido.
* Scala es *Static Typed*: el array se infirió estrictamente como `Array[String]`, rechazando la entrada de un escalar entero `500`.

**Comentario:**
Comprobé el comportamiento de mutabilidad interna de los arrays de JVM mediante alteración de celdas indexadas y evidencié el bloqueo del compilador estático ante intentos de corrupción de tipos.

---

### Ejercicio 8 — Creación e inicialización de Arrays

Crea un array de 4 enteros usando `new Array[Int](4)`. Asigna `17, 24, 21, 19` manualmente y muestra su longitud.

* **Técnicas:** Instanciación parametrizada, *default values* de la JVM, propiedad `.length`.

**SOLUCIÓN:**

```scala
val scores = new Array[Int](4)
println(s"Array recién creado: ${scores.mkString(", ")}") // Devuelve 0, 0, 0, 0

scores(0) = 17
scores(1) = 24
scores(2) = 21
scores(3) = 19

println(s"Array llenado manualmente: ${scores.mkString(", ")}")
println(s"Longitud total: ${scores.length}")

```

**Explicación:**

* La sentencia `new Array[Int](4)` reserva 4 posiciones en memoria inicializadas por defecto a `0` para tipos numéricos primitivos (y a `null` para referencias a objetos como `String`).
* `.length` es una propiedad estática del array subyacente, no un método invocable, por lo que carece de paréntesis.

**Comentario:**
Exploré la parametrización explícita de estructuras de tamaño fijo instanciando un vector tipado en memoria vacía e inyectando posteriormente el estado del programa índice por índice.

---

### Ejercicio 9 — Recorrer un Array con `while`

Utiliza `val manos = Array(17, 24, 21, 19, 26)` y recórrelo usando `while`, imprimiendo el valor y si se pasa (con la función `bust`).

* **Técnicas:** Iteración condicional imperativa (`while`), estado mutable externo (`var i`), evaluación procedimental.

**SOLUCIÓN:**

```scala
val manosArray = Array(17, 24, 21, 19, 26)
var i = 0

while (i < manosArray.length) {
  val puntuacion = manosArray(i)
  println(s"Mano: $puntuacion | ¿Bust?: ${bust(puntuacion)}")
  i += 1
}

```

**Explicación:**

* El contador `i` debe ser obligatoriamente `var` porque su valor muta en cada iteración; si fuera `val`, no se podría incrementar.
* Si no incrementamos `i += 1`, la condición del `while` sería perpetuamente `true` ($0 < 5$), colapsando el entorno de ejecución en un bucle infinito.
* **Tip (Estilo Imperativo):** Depender de índices mutables e instrucciones paso a paso define el paradigma imperativo, propio de lenguajes como C o Java antiguos, que Scala soporta por retrocompatibilidad pero desaconseja.

**Comentario:**
Implementé un bucle procedimental gestionando un puntero índice mutable para iterar transversalmente el bloque de memoria del array e interrogar secuencialmente la lógica de la función `bust`.

---

### Ejercicio 10 — Listas e inmutabilidad

Crea `val jugadores = List("Alex", "Chen", "Marta")`. Añade "Sindhu" mediante `::`, comprueba la inmutabilidad de la original y muestra su versión invertida.

* **Técnicas:** Instanciación de `List`, *prepend* funcional con operador `Cons` (`::`), método funcional iterativo `.reverse`.

**SOLUCIÓN:**

```scala
val listaOriginal = List("Alex", "Chen", "Marta")
println(s"Original: $listaOriginal")

val listaAmpliada = "Sindhu" :: listaOriginal

println(s"Original tras '::': $listaOriginal") // Sigue intacta
println(s"Nueva combinada   : $listaAmpliada")
println(s"Longitud lista org: ${listaOriginal.length}")
println(s"Lista revertida   : ${listaOriginal.reverse}")

```

**Explicación:**

* La operación `::` asocia a la derecha (lee el elemento y lo engancha a la cabeza de la lista existente), creando un objeto `List` estructuralmente nuevo que comparte la cola en memoria sin sobreescribir la colección primigenia.
* A este patrón de protección de estado en memoria se le conoce como *persistencia inmutable*, piedra angular de la programación funcional y la concurrencia en Big Data (Spark).
* ⚠️ **Trampa técnica:** Escribir `listaOriginal :: "Sindhu"` arrojará un error de sintaxis; el operador Cons `::` exige que el elemento escalar se posicione a la izquierda y la lista a la derecha de la expresión.

**Comentario:**
Certifiqué el paradigma inmutable de las Listas de Scala ejecutando una expansión de cabecera con el operador `Cons` y corroborando que el puntero primitivo de datos se mantiene absolutamente incorrupto ante operaciones colaterales.

---

### Ejercicio 11 — Construcción y concatenación de listas

Construye una lista con `Nil` y `::` ("Ana", "Luis", "Marta"). Crea otra ("Pedro", "Sofia"). Concaténalas con `:::` y demuestra la inmutabilidad original.

* **Técnicas:** Constructor nulo `Nil`, operador funcional de apilamiento `::`, concatenación de colecciones `:::`.

**SOLUCIÓN:**

```scala
val grupoA = "Ana" :: "Luis" :: "Marta" :: Nil
val grupoB = "Pedro" :: "Sofia" :: Nil

val fusion = grupoA ::: grupoB

println(s"Grupo A original: $grupoA")
println(s"Grupo B original: $grupoB")
println(s"Grupos fusionados: $fusion")

```

**Explicación:**

* `Nil` inicializa explícitamente el final vacío de la lista encadenada. Las cadenas de texto se apilan retroactivamente sobre él de derecha a izquierda.
* El operador `:::` fusiona dos estructuras completas copiando en $\mathcal{O}(n)$ los elementos de la lista izquierda sobre la derecha sin desintegrar los contenedores originales.

**Comentario:**
Modelé estructuras encadenadas puras operando desde el componente nulo abstracto y fusioné conjuntos poblacionales empleando el operador vectorizado `:::`, salvaguardando la integridad paramétrica de las fuentes originales.

---

## Sección 4. Lógica Funcional y Proyectos

### Ejercicio 12 — Operadores relacionales y lógicos

Puntuaciones: `handA=18`, `handB=21`, `handC=25`. Resuelve y explica las 6 expresiones de evaluación booleanas.

* **Técnicas:** Operadores estandarizados `<, >, >=, ==, !=, &&, ||, !`.

**SOLUCIÓN:**

```scala
val handA = 18
val handB = 21
val handC = 25

val expr1 = handA > handB
val expr2 = handB == 21
val expr3 = handC != 21
val expr4 = (handA <= 21) && (handB <= 21)
val expr5 = (handA > 21) || (handC > 21)
val expr6 = !(handB > 21)

println(s"Expr1: $expr1 | Expr2: $expr2 | Expr3: $expr3")
println(s"Expr4: $expr4 | Expr5: $expr5 | Expr6: $expr6")

```

**Explicación Documentada:**

| Expresión | Resultado | Explicación |
| --- | --- | --- |
| `handA > handB` | `false` | 18 no es numéricamente superior a 21. |
| `handB == 21` | `true` | Igualdad exacta validada contra el literal. |
| `handC != 21` | `true` | Desigualdad correcta: 25 es distinto de 21. |
| `(A <= 21) && (B <= 21)` | `true` | El operador lógico AND exige que **ambas** sean verdad (18<=21 y 21<=21). |
| `(A > 21) || (C > 21)` | `true` | El operador lógico OR es verdad porque al menos una lo es (25>21). |
| `!(handB > 21)` | `true` | Niega que la mano se pase de 21 (invierte el `false` a `true`). |

---

### Ejercicio 13 — `foreach` y funciones como valores

Recorre `Array(17, 24, 21, 26, 18)` evaluando `bust` en dos versiones: con `while` (A) y con `foreach` (B).

* **Técnicas:** Transformación funcional sin estado, paso de funciones como valor (lambdas anónimas).

**SOLUCIÓN:**

```scala
val coleccionManos = Array(17, 24, 21, 26, 18)

// Versión A: While
println("--- Versión While ---")
var j = 0
while (j < coleccionManos.length) {
  println(s"${coleccionManos(j)} -> Bust: ${bust(coleccionManos(j))}")
  j += 1
}

// Versión B: Foreach (Estilo Funcional)
println("--- Versión Foreach ---")
coleccionManos.foreach { elemento =>
  println(s"$elemento -> Bust: ${bust(elemento)}")
}

```

**Explicación:**

* La versión A (`while`) requiere definir un índice de seguimiento y mutarlo manualmente mediante un efecto secundario en memoria (`j += 1`).
* La versión B (`foreach`) delega la carga computacional iterativa al método nativo del array, inyectando un bloque de código anónimo `elemento => ...` y prescindiendo de contadores.
* **Tip (Ventaja funcional):** El estilo funcional (`foreach`, `map`) no solo elimina el riesgo de bucles infinitos, sino que permite al compilador paralelizar operaciones en clústeres Spark de Big Data sin que aparezcan cuellos de botella por variables compartidas.

**Comentario:**
Contrasté el paradigma procedimental frente a la sintaxis funcional declarativa, evidenciando la mitigación del riesgo operativo al erradicar estados colaterales y bucles manuales de la lógica de procesamiento masivo.

---

### Ejercicio 14 — Efectos secundarios y estilo de programación

Analiza la modificación de un `var total = 0` externo desde dentro de una función, identifica el efecto secundario y construye una versión pura.

* **Técnicas:** Funciones impuras (void/Unit state mutation), funciones puras, inmutabilidad estructural.

**SOLUCIÓN:**

```scala
var total = 0

// Función Impura (Efecto Secundario)
def sumarAlTotal(valor: Int): Unit = {
  total = total + valor
}

sumarAlTotal(5)
sumarAlTotal(10)
println(s"Valor de variable externa mutada: $total")

// Función Pura (Estilo Funcional Seguro)
def sumar(a: Int, b: Int): Int = {
  a + b
}
val resultadoSeguro = sumar(5, 10)
println(s"Resultado de suma aislada: $resultadoSeguro")

```

**Explicación:**

| Característica | `sumarAlTotal` | `sumar` |
| --- | --- | --- |
| Modifica datos externos | SÍ (Altera el entorno global) | NO |
| Devuelve resultado calculado | NO (Retorna `Unit` vacío) | SÍ (Retorna un `Int` nuevo) |
| Utiliza efecto secundario | SÍ | NO |
| Estilo predominante | Imperativo | Funcional |

---

### Ejercicio 15 — Programa integrado: Torneo de Twenty-One

Combina todos los conceptos desarrollando un analizador completo de rondas que procese nombres, puntuaciones, cruces de evaluación e identifique las mejores métricas válidas.

* **Técnicas:** Colecciones cruzadas, iteradores funcionales, condicionales anidados, orquestación de funciones de negocio puras.

**SOLUCIÓN:**

```scala
// Datos iniciales fijos
val nombres = List("Alex", "Chen", "Marta", "Sindhu")
val manosR1 = Array(18, 24, 21, 20)
val manosR2 = Array(22, 19, 20, 21)

// Función analítica principal
def evaluarRonda(etiqueta: String, puntuaciones: Array[Int]): Unit = {
  var mejorValida = 0
  var activos = 0
  var eliminados = 0
  
  println(s"\n=== RESULTADOS: $etiqueta ===")
  
  // Procesamiento con contador mixto (zipWithIndex funcional emulado)
  var index = 0
  puntuaciones.foreach { puntos =>
    val jugadorActual = nombres(index)
    val sePasa = bust(puntos)
    
    if (sePasa) {
      println(s"$jugadorActual: $puntos puntos -> ELIMINADO (Bust)")
      eliminados += 1
    } else {
      println(s"$jugadorActual: $puntos puntos -> VÁLIDA")
      activos += 1
      mejorValida = maxHand(mejorValida, puntos)
    }
    index += 1
  }
  
  println(s"Resumen -> Sobreviven: $activos | Eliminados: $eliminados | MEJOR MANO: $mejorValida")
}

// Ejecución del pipeline de negocio
evaluarRonda("RONDA 1", manosR1)
evaluarRonda("RONDA 2", manosR2)

```

**Explicación / Informe Final Documentado:**

* **Partes inmutables:** Los catálogos persistentes de nombres y puntuaciones históricas están encapsulados con `val`. La estructura transversal jamás sufre sobreescrituras en sitio.
* **Partes mutables:** Dentro del encapsulamiento analítico de la función orquestadora, las métricas agregadas (`mejorValida`, `activos`, `eliminados`) se instancian con `var` como acumuladores controlados.
* **Paradigma Funcional vs Imperativo:** La travesía analítica delegada a `foreach` y la interrogación abstracta sobre `bust()` configuran el corazón declarativo y funcional del sistema; sin embargo, el rastreador ordinal `index += 1` emula imperativamente la carencia funcional avanzada (como `zipWithIndex` que no se ha estudiado), hibridando ambos mundos.

**Comentario:**
Consolidé los requisitos sistémicos en un *script* orquestador parametrizado, aislando el motor analítico `evaluarRonda()` y consumiéndolo modularmente para el primer y segundo parcial del torneo, computando la resiliencia agregada sin profanar el espacio vital inmutable de los catálogos de memoria.

---

# 4. Trampas generales — Repaso de últimos 2 minutos (Scala)

Guía rápida para evitar errores de compilación habituales.

* ⚠️ **Corchetes vs Paréntesis en colecciones:**
* **Mal:** `Array["A", "B"]` o `jugadores[0]` (Sintaxis de Python).
* **Bien:** `Array("A", "B")` (creación) y `jugadores(0)` (acceso por índice). En Scala, el acceso posicional exige **paréntesis**. Los corchetes `[]` operan **exclusivamente** para definir clases genéricas (`Array[Int]`).




* ⚠️ **El orden del operador `::` (Cons):**
* Funciona empujando elementos de izquierda a derecha sobre una lista preexistente.
* **Mal:** `lista :: "Elemento"` (Error de compilación de tipos).
* **Bien:** `"Elemento" :: lista`.




* ⚠️ **`if` sin evaluar todas las ramas:**
* En Scala, `if` es una expresión que devuelve valor. Si haces un `val x = if (a > b) a`, ¿qué pasa si es falso? Devolverá el tipo vacío `Unit()`. Siempre debes poner la rama `else` para asignar variables estructuradas.




* ⚠️ **Operadores lógicos con un solo símbolo (`&` vs `&&`):**
* Usa siempre los relacionales de cortocircuito lógico: `&&` y `||`. Los simples (`&`, `|`) son operaciones estrictas a nivel de bit y forzarán la evaluación inútil de ambas partes de la expresión colapsando código.




* ⚠️ **Variables de bucle en `foreach` vs `while`:**
* En `while` necesitas inicializar una `var i = 0` y forzar un `i += 1`. Si te olvidas del `i += 1`, crearás un **bucle infinito** mortal que devorará la RAM de Jupyter.
* El `foreach` no usa índices, inyecta directamente el elemento iterado por debajo, asegurando la finalización procedural.




* ⚠️ **Valores de retorno en funciones y bloque Unit:**
* No uses la palabra `return`. La última expresión materializada se exporta de forma automática.


* Si tu función usa `def nombre(): Unit = { ... }`, significa que devolverá "vació absoluto"; borrar `Unit` o sustituirlo por el tipo de verdad (`Int`, `String`) es necesario si quieres atrapar un resultado.



```

```
