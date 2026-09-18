
# 🔄 Traductor Universal: Scala ↔ Python — Chuleta de Examen

> **Cómo usar esto en el examen:** Si te piden resolver un problema en un lenguaje y luego traducirlo al otro, **NO pienses en la lógica de cero**. La lógica matemática y de negocio es exactamente la misma. Usa este documento para traducir la sintaxis línea por línea, prestando especial atención a la sección de **"Trampas Mortales"**.

---

# 1. Matriz de Equivalencias Estructurales

Busca la estructura que tienes escrita y mira su equivalente directo en el otro lenguaje.

### 1.1 Variables, Tipos y Operadores lógicos
| Concepto | En Scala (Estático)[cite: 1, 4, 6] | En Python (Dinámico) |
| :--- | :--- | :--- |
| **Constante / Inmutable** | `val x = 10`[cite: 1, 4, 6] | `x = 10` (no hay protección nativa) |
| **Variable / Mutable** | `var total = 0`[cite: 1, 4, 6] | `total = 0` |
| **Forzar tipo decimal** | `val pi: Double = 3.14` o `3.14f`[cite: 1, 4, 6] | `pi = 3.14` |
| **Operador AND** | `if (a > 0 && b > 0)`[cite: 1, 4, 6] | `if (a > 0) and (b > 0):` (o `&` en Pandas) |
| **Operador OR** | `if (a > 0 \|\| b > 0)`[cite: 1, 4, 6] | `if (a > 0) or (b > 0):` (o `\|` en Pandas) |
| **Operador NOT** | `if (!activo)`[cite: 1, 4, 6] | `if not activo:` (o `~` en Pandas) |

### 1.2 Estructuras de Control (Condicionales y Bucles)
| Concepto | En Scala[cite: 1, 4, 6] | En Python |
| :--- | :--- | :--- |
| **Condicional múltiple** | `if (x>0) A else if (x<0) B else C`[cite: 1, 4, 6] | `if x>0: A` $\rightarrow$ `elif x<0: B` $\rightarrow$ `else: C` |
| **If como asignación** | `val res = if (x > 0) 1 else 0`[cite: 1, 4, 6] | `res = 1 if x > 0 else 0` |
| **Bucle While** | `while (i < 10) { i += 1 }`[cite: 1, 4, 6] | `while i < 10: i += 1` |
| **Bucle For / Foreach** | `lista.foreach { x => println(x) }`[cite: 1, 4, 6] | `for x in lista: print(x)` |

### 1.3 Colecciones (Arrays y Listas)
| Concepto | En Scala[cite: 1, 4, 6] | En Python |
| :--- | :--- | :--- |
| **Crear Array/Lista** | `Array(1, 2, 3)` o `List(1, 2)`[cite: 1, 4, 6] | `np.array([1, 2, 3])` o `[1, 2]` |
| **Acceso a elementos** | `coleccion(0)` (**PARÉNTESIS**)[cite: 1, 4, 6] | `coleccion[0]` (**CORCHETES**) |
| **Añadir al inicio** | `"A" :: lista` (crea nueva)[cite: 1, 4, 6] | `["A"] + lista` o `lista.insert(0, "A")` |
| **Concatenar dos listas** | `listaA ::: listaB`[cite: 1, 4, 6] | `listaA + listaB` o `listaA.extend(listaB)` |
| **Tamaño de colección** | `coleccion.length` (propiedad)[cite: 1, 4, 6] | `len(coleccion)` (función global) |

### 1.4 Funciones Puras
| Concepto | En Scala[cite: 1, 4, 6] | En Python |
| :--- | :--- | :--- |
| **Definición obligatoria** | `def f(a: Int): Int = { ... }`[cite: 1, 4, 6] | `def f(a): ...` |
| **Retornar valor** | `a + b` (última línea sin `return`)[cite: 1, 4, 6] | `return a + b` |

---

# 2. 🚨 TRAMPAS MORTALES DE TRADUCCIÓN (Revisar siempre)

Si tu código traducido da error, el 99% de las veces será por uno de estos cinco motivos:

### Trampa 1: El acceso a índices (El error más común)
*   **De Python a Scala:** Cambiar `lista[0]` por `lista(0)`[cite: 1, 4, 6]. Scala usa paréntesis porque acceder a un elemento es llamar a un método oculto (`.apply()`). Si dejas los corchetes, Scala pensará que estás definiendo un tipo genérico.
*   **De Scala a Python:** Cambiar `lista(0)` por `lista[0]`. Python usa estrictamente corchetes para el *slicing* e indexación.

### Trampa 2: La cláusula `return`
*   **De Python a Scala:** ¡Bórralo! En Scala, la última expresión evaluada es el retorno[cite: 1, 4, 6].
*   **De Scala a Python:** ¡Añádelo! Si pasas una función pura de Scala a Python y te olvidas de escribir `return`, tu función en Python devolverá `None` y romperá todos los cálculos matemáticos.

### Trampa 3: Los tipos en los parámetros de la función
*   **De Python a Scala:** En Scala **es obligatorio** decir qué tipo de variable entra y qué tipo sale: `def mi_func(x: Int, y: Double): Boolean = { ... }`[cite: 1, 4, 6].
*   **De Scala a Python:** Bórralos. Python es dinámico: `def mi_func(x, y): ...`

### Trampa 4: `else if` frente a `elif`
*   **De Python a Scala:** Tienes que escribir las dos palabras completas y separadas: `else if (condicion)`[cite: 1, 4, 6].
*   **De Scala a Python:** Es una sola palabra clave fusionada: `elif condicion:`.

### Trampa 5: Operadores Lógicos
*   **De Python a Scala:** Cambia `and` por `&&`, `or` por `||`, y `not` por `!`[cite: 1, 4, 6].
*   **De Scala a Python:** Cambia `&&` por `and`, `||` por `or`, y `!` por `not` (OJO: si en Python estás usando máscaras de Pandas/NumPy, vuelve a usar `&`, `|`, `~`).

---

# 3. Ejemplos Reales Completos

### Caso 1: Lógica de negocio con condicionales (Juego Twenty-One / Evaluador)

**Contexto:** Tienes que evaluar si una mano supera 21 y compararla con otra[cite: 1, 4, 6].

**Código en SCALA:**
```scala
def bust(puntuacion: Int): Boolean = {
  puntuacion > 21
}

def ganador(handA: Int, handB: Int): Int = {
  if (bust(handA) && bust(handB)) 0
  else if (bust(handA)) handB
  else if (bust(handB)) handA
  else if (handA > handB) handA
  else handB
}

```

**Traducción literal a PYTHON:**

```python
def bust(puntuacion):
    return puntuacion > 21  # ⚠️ Añadido 'return' explícito

def ganador(hand_a, hand_b):
    if bust(hand_a) and bust(hand_b): # ⚠️ '&&' pasa a 'and'
        return 0                      # ⚠️ Añadido 'return'
    elif bust(hand_a):                # ⚠️ 'else if' pasa a 'elif'
        return hand_b
    elif bust(hand_b):
        return hand_a
    elif hand_a > hand_b:
        return hand_a
    else:
        return hand_b

```

---

### Caso 2: Iteración sobre colecciones (Listas / Arrays)

**Contexto:** Recorrer una lista de puntuaciones, aplicar un cálculo y detenerse o continuar.

**Código en SCALA:**

```scala
val manos = Array(17, 24, 21, 19, 26)
var i = 0
var activos = 0

// Opción 1: While
while (i < manos.length) {
  if (!bust(manos(i))) {
    activos += 1
  }
  i += 1
}

// Opción 2: Foreach funcional
manos.foreach { mano =>
  println(mano)
}

```

**Traducción literal a PYTHON:**

```python
manos = [17, 24, 21, 19, 26] # ⚠️ Se convierte en lista estándar o np.array
i = 0
activos = 0

# Opción 1: While
while i < len(manos):        # ⚠️ Parentesis del while fuera, len() en vez de .length
    if not bust(manos[i]):   # ⚠️ '!' pasa a 'not', paréntesis () pasan a corchetes []
        activos += 1
    i += 1

# Opción 2: For equivalente a Foreach
for mano in manos:           # ⚠️ La lambda 'mano =>' se convierte en sintaxis 'for in'
    print(mano)              # ⚠️ println pasa a print

```

