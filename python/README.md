# Python — Data Science & Scripting — Chuleta de examen

> **Cómo usar esto en el examen:** 1) busca la técnica que necesitas en la matriz de abajo → 2) salta a la chuleta rápida o al ejercicio correspondiente → 3) mira el enunciado y adapta el patrón al problema de tu examen. No lo leas de arriba a abajo, úsalo como diccionario de rescate.

---

# 1. Matrices de referencia por tipo de técnica

Divididas en subíndices — busca primero el bloque (Listas, Pandas, Numpy...) y luego la fila.

### 1.1 Variables, Tipos y Listas
| Técnica | Para qué sirve | Resuelta en |
|---|---|---|
| Slicing con paso (`[::paso]`) | Desdoblar listas planas intercaladas (pares/impares) | Ejercicio 1 |
| Búsqueda dinámica (`.index()`, `[-n:]`) | Localizar posiciones sin hardcodear índices y extraer colas | Ejercicio 1, 4 |
| Copias vs referencias (`.copy()`, `[:]`) | Duplicar listas sin mutar la original por asignación de puntero | Ejercicio 2, 3 |
| Ordenación (`sort` vs `sorted`) | Modificar la lista en sitio vs crear una lista nueva ordenada | Ejercicio 4 |

### 1.2 Funciones, Métodos y Paquetes
| Técnica | Para qué sirve | Resuelta en |
|---|---|---|
| Funciones (`def`, `return`) | Empaquetar lógica, asignar valores por defecto y devolver tuplas | Ejercicio 5 |
| Métodos de string (`.strip()`, `.upper()`) | Limpiar texto, normalizar mayúsculas y desglosar códigos SKU | Ejercicio 6 |
| Módulos y `sys.path` | Crear e importar funciones de soporte desde un script externo | Ejercicio 7 |

### 1.3 NumPy y Álgebra Vectorial
| Técnica | Para qué sirve | Resuelta en |
|---|---|---|
| Arrays y Vectorización | Operaciones elemento a elemento a nivel de C sin bucles `for` | Ejercicio 8 |
| Máscaras booleanas | Filtrar arrays completos mediante condiciones lógicas directas | Ejercicio 9 |
| Matrices 2D y `axis` | Agrupaciones marginales por fila/columna y expansión con `.vstack()` | Ejercicio 10 |
| Semillas y Estadística | `random.seed()`, correlaciones, medias y detección de *outliers* | Ejercicio 11 |

### 1.4 Diccionarios y Pandas (Estructuras)
| Técnica | Para qué sirve | Resuelta en |
|---|---|---|
| Diccionarios anidados | Modelar configuraciones jerárquicas con clave-valor (ej. tarifas) | Ejercicio 12 |
| Creación de DataFrame | Convertir diccionarios a tablas y asignar identificadores como índice | Ejercicio 13 |
| Selección por etiqueta (`.loc`) | Seleccionar filas y columnas por su nombre o ID (incluye extremos) | Ejercicio 14 |
| Selección por posición (`.iloc`) | Extraer subconjuntos por índice entero posicional (excluye extremo) | Ejercicio 14 |

### 1.5 Lógica, Control de Flujo y Filtrado
| Técnica | Para qué sirve | Resuelta en |
|---|---|---|
| Operadores de comparación | `>`, `<`, `==`, `!=` y evaluación booleana estructurada | Ejercicio 16 |
| Estructuras condicionales | `if`, `elif`, `else` para bifurcación de lógica de negocio | Ejercicio 17 |
| Transformación `.apply()` | Ejecutar funciones custom fila a fila sobre un DataFrame | Ejercicio 17, 20 |
| Filtrado Múltiple (`&`, `|`, `~`) | Filtros complejos en Pandas (exigen usar paréntesis por bloque) | Ejercicio 18 |
| Filtrado con `.query()` | Expresar filtros mediante cadenas legibles en contexto del DF | Ejercicio 18 |

### 1.6 Bucles e Iteración
| Técnica | Para qué sirve | Resuelta en |
|---|---|---|
| Bucle `while` | Repetir ejecución hasta que se cumpla una condición de corte | Ejercicio 19 |
| Desempaquetado e `.items()` | Recorrer diccionarios o tuplas extrayendo clave y valor a la vez | Ejercicio 19 |
| Recorrido con `.iterrows()` | Iterar filas de un DataFrame accediendo por nombre de columna | Ejercicio 20 |
| Columnas calculadas directas | Crear métricas vectorizadas sin usar bucles (mucho más rápido) | Ejercicio 15, 20 |

---

# 2. Chuleta de sintaxis rápida (Estilo DataCamp)

## 2.1 Python Basics: Variables, Strings y Tipos
```python
# Actualización de variables (Updating variables)
ahorros = 100
ahorros = ahorros * 1.10   # Reasignación
ahorros += 50              # Atajo (Suma y asigna)

# Tipos de datos comunes y verificación (Checking data types)
type(3.14)      # float
type(True)      # bool
type("hola")    # str

# Strings multilínea y modificación
texto_largo = """Primera línea
Segunda línea"""
texto = "hola"
# texto[0] = "H"  # ERROR: los strings son INMUTABLES
nuevo_texto = "H" + texto[1:]  # Creación de nuevo string: "Hola"
```

---

## 2.2 Listas: Construcción, Subsetting y Manipulación

```python
# Creación y Subsetting (Subsetting lists)
x = ["a", "b", "c", "d", "e"]
x[0]       # "a" (Primer elemento)
x[-1]      # "e" (Último elemento)
x[1:4]     # ["b", "c", "d"] (Slicing: excluye el extremo derecho)
x[:3]      # ["a", "b", "c"] (Desde el principio hasta índice 2)
x[2:]      # ["c", "d", "e"] (Desde índice 2 hasta el final)
x[::-1]    # Invertir lista
x[0::2]    # Slicing con paso 2 (elementos pares)

# Listas de listas (List of lists)
matriz = [[1, 2], [3, 4]]
matriz[1][0]  # Acceso anidado: fila 1 (segunda), columna 0 -> devuelve 3

# Manipulación de elementos (Manipulating Lists)
x[1] = "z"          # Reemplaza un elemento: ["a", "z", "c", "d", "e"]
x[0:2] = ["x", "y"] # Reemplazo múltiple por slicing

x.append("f")       # Añade "f" al final (NO devuelve nueva lista, altera en sitio)
x.extend(["g","h"]) # Añade varios elementos al final
x.insert(0, "w")    # Inserta "w" en la posición 0 desplazando el resto

del x[0]            # Delete list elements (Borra por índice)
item = x.pop()      # Extrae y borra el último elemento

# Inner workings (Referencias vs Copias)
y = x               # ADVERTENCIA: Copia el puntero. Si cambias 'y', cambias 'x'.
y_segura = list(x)  # Copia real en memoria
y_segura = x[:]     # Copia real mediante slicing
```

---

## 2.3 Diccionarios: Creación y Acceso 

```python
# Construcción y acceso (Building a dictionary)
poblacion = {"madrid": 3.2, "bcn": 1.6}
poblacion["madrid"]       # Acceso al valor: 3.2
poblacion.get("sevilla", 0) # Devuelve 0 si la clave no existe

# Manipulación (Dictionary Manipulation)
poblacion["valencia"] = 0.8  # Añade nueva clave-valor
poblacion["madrid"] = 3.3    # Actualiza el valor existente
del poblacion["bcn"]         # Elimina la clave

# Dictionariception (Diccionarios dentro de diccionarios)
red = {"ES": {"capital": "madrid", "pob": 47}}
red["ES"]["pob"]             # Acceso anidado -> 47
```

---

## 2.4 NumPy: Arrays 1D/2D, Subsetting, Aritmética y Estadística

```python
import numpy as np

# 1. Your First NumPy Array (Creación)
np_peso = np.array([65, 72, 80])
np_altura = np.array([1.7, 1.8, 1.9])

# 2. Subsetting NumPy Arrays (Filtrado con máscaras booleanas)
es_alto = np_altura > 1.75          # Crea array de booleanos: [False, True, True]
altos = np_altura[es_alto]          # Filtra y devuelve solo los que son True
# Forma rápida: np_altura[np_altura > 1.75]

# 3. 2D NumPy Arrays (Matrices de filas y columnas)
np_2d = np.array([[1, 2, 3], 
                  [4, 5, 6]])
np_2d.shape                         # Devuelve (2, 3): 2 filas y 3 columnas

# 4. Subsetting 2D NumPy Arrays (Extrayendo datos de matrices)
# Sintaxis siempre es: array[fila, columna]
np_2d[0, 2]                         # Fila 0, columna 2 -> devuelve el 3
np_2d[:, 1:3]                       # Todas las filas (:), columnas 1 y 2 (excluye la 3)
np_2d[1, :]                         # Toda la fila 1 (la segunda fila completa)

# 5. 2D Arithmetic (Matemáticas directas sin bucles for)
np_2d * 2                           # Multiplica TODOS los elementos por 2 al instante
np_peso / np_altura ** 2            # Calcula el IMC cruzando dos arrays de golpe

# 6. NumPy: Basic Statistics (Average versus median)
np.mean(np_peso)                    # Media aritmética (Cuidado: muy afectada por valores atípicos/outliers)
np.median(np_peso)                  # Mediana (Valor central, mucho más seguro si hay outliers)
np.std(np_peso)                     # Desviación típica (Cuánto varían los datos de la media)
np.corrcoef(np_peso, np_altura)     # Matriz de correlación (Relación matemática entre dos arrays)
np.sum(np_2d, axis=0)               # axis=0 -> Suma verticalmente (colapsa por columnas)
np.sum(np_2d, axis=1)               # axis=1 -> Suma horizontalmente (colapsa por filas)
```

## 2.5 Lógica, Control de Flujo y Bucles
```python
# 1. Comparison Operators (Operadores de comparación básicos)
x = 10
x == 5   # ¿Es igual a 5? -> False
x != 5   # ¿Es distinto de 5? -> True
x >= 5   # ¿Mayor o igual? -> True

# 2. Boolean Operators (and, or, not) -> PARA VARIABLES SUELTAS
(x > 5) and (x < 15)  # True (Se cumplen AMBAS condiciones)
(x == 1) or (x == 10) # True (Se cumple UNA de las dos)
not (x == 5)          # True (Invierte el resultado, x no es 5)

# 3. Boolean operators with NumPy/Pandas (OBLIGATORIO: &, |, ~ y usar paréntesis)
# Usar 'and', 'or', 'not' dará ERROR con arrays y DataFrames.
mascara = (np_peso > 70) & (np_altura < 1.9) # AND matricial (Ambas)
invertida = ~(np_peso > 70)                  # NOT matricial (Invierte)

# 4. If, elif, else (Rutas de decisión)
if x > 10:
    print("Alto")       # Si es mayor a 10
elif x > 5:
    print("Medio")      # Si NO es mayor a 10, pero SÍ es mayor a 5
else:
    print("Bajo")       # Si NO se cumple absolutamente NINGUNA de las anteriores

# 5. While loops (Bucle condicional)
stock = 0
while stock < 10:
    stock += 2          # Aumenta de 2 en 2 en cada vuelta
    if stock == 8:
        break           # 'break' detiene el bucle forzosamente, sin llegar a 10

# 6. For loops (Bucles sobre listas y estructuras)
# Looping through a list (Elemento a elemento)
for item in ["a", "b", "c"]:
    print(item)

# Indexes and values (enumerate) -> Saca el índice numérico y el valor a la vez
for idx, val in enumerate(["a", "b", "c"]):
    print(f"En la posición {idx} está la letra {val}")

# Loop over list of lists (Desempaquetando variables)
inventario = [["Mesa", 10], ["Silla", 20]]
for producto, cantidad in inventario:
    print(f"Tenemos {cantidad} unidades de {producto}")
```

---

## 2.6 Pandas: Diccionarios a DF, loc/iloc, Filtrado y .iterrows()

### ¿Qué es Pandas y por qué se utiliza?

**Pandas** es una librería de Python diseñada específicamente para trabajar con **datos tabulares** (el equivalente a manejar una hoja de cálculo tipo Excel o una tabla de base de datos SQL directamente en código). Está construida sobre **NumPy**, pero resuelve las limitaciones que tiene NumPy al procesar datos del mundo real.

---

#### Diferencias clave: NumPy frente a Pandas

| Característica | NumPy (`ndarray`) | Pandas (`DataFrame`) |
| --- | --- | --- |
| **Tipos de datos** | **Homogéneos:** Todo el array debe ser del mismo tipo (`int`, `float`). Si mezclas números con texto, convierte todo a texto (*upcasting*). | **Heterogéneos:** Cada columna tiene su propio tipo (una columna con fechas, otra con texto, otra con números y otra booleana). |
| **Acceso a datos** | Solo por **posición numérica** (`array[0, 2]`), obligando a memorizar qué variable ocupaba cada índice. | Por **etiqueta/nombre de columna** (`df['precio']`) o identificador de fila (`df.loc['ID_101', 'cliente']`). |
| **Valores ausentes** | Limitado; operar con `np.nan` suele propagar errores matemáticos en los cálculos. | Métodos nativos integrados para detectar, rellenar o descartar nulos (`isna()`, `fillna()`, `dropna()`). |
| **Propósito** | Operaciones numéricas y álgebra matricial pura de alta velocidad. | Limpieza, filtrado, transformación, cruce y análisis de tablas de datos. |

---

### Las 2 estructuras principales

* **`Series` (1D):** Representa una columna individual de datos. Funciona de manera similar a un array unidimensional de NumPy, pero cuenta con un índice explícito de etiquetas asociado a cada fila.
* **`DataFrame` (2D):** Representa la tabla completa en filas y columnas. Se puede entender conceptualmente como un diccionario donde cada clave es el nombre de la columna y cada valor es una `Series`.

---

### Casos de uso habituales

* **Carga y exportación de archivos:** Permite leer y guardar datos en múltiples formatos con una sola línea de código (`pd.read_csv()`, `pd.read_excel()`, `pd.read_sql()`).
* **Filtrado condicional:** Permite realizar selecciones sobre filas aplicando condiciones lógicas:
```python
df[(df['edad'] >= 18) & (df['ciudad'] == 'Madrid')]

```

* **Agrupación y agregación (`GROUP BY`):** Cuenta con el método `.groupby()` para resumir indicadores por categorías:
```python
df.groupby('categoria')['ventas'].sum()

```

* **Cruce de tablas (`JOIN` / `MERGE`):** Combina DataFrames a través de identificadores comunes mediante `pd.merge()`.
* **Columnas calculadas vectorizadas:** Ejecuta transformaciones elemento a elemento de forma directa sin usar bucles `for`:
```python
df['importe_total'] = df['unidades'] * df['precio_unitario']

```

---

### Sintaxis Rápida de Pandas

```python
import pandas as pd

# 1. Dictionary to DataFrame (Convertir diccionario en tabla)
datos = {"ciudad": ["Madrid", "Barcelona"], "ventas": [10, 20]}
df = pd.DataFrame(datos)            
df.index = ["M", "B"]               # Modifica el nombre/etiqueta de las filas

# 2. CSV to DataFrame (Lectura de archivos)
# index_col=0 fuerza a que la primera columna del CSV sea la etiqueta de las filas
df = pd.read_csv("datos.csv", index_col=0) 

# 3. Square Brackets (Extracción de columnas)
df["ventas"]                        # Devuelve una Serie 1D (una columna aislada)
df[["ciudad", "ventas"]]            # Devuelve un DataFrame 2D (varias columnas, USAR DOBLE CORCHETE)

# 4. loc and iloc (Selección cruzada de Filas y Columnas)
# .loc (Por ETIQUETA / Nombre real) -> ¡El extremo final ESTÁ INCLUIDO!
df.loc["M"]                         # Fila completa etiquetada como "M"
df.loc[:, "ventas"]                 # Todas las filas (:), solo de la columna "ventas"
df.loc["M":"B", ["ciudad"]]         # Rango desde la fila "M" hasta "B" (ambas incluidas)

# .iloc (Por POSICIÓN NUMÉRICA / Como una lista normal) -> ¡El extremo final está EXCLUIDO!
df.iloc[0:2, 1]                     # Saca filas 0 y 1 (el 2 se ignora), columna índice 1

# 5. Filtering pandas DataFrames (Filtros lógicos)
filtro_simple = df[df["ventas"] > 15]
# OBLIGATORIO: Paréntesis alrededor de cada condición y usar & / |
filtro_multiple = df[(df["ventas"] > 10) & (df["ciudad"] == "Madrid")] 

# 6. Add column (Crear derivadas)
# Pandas operará sobre toda la columna a la vez de forma matemática
df["total_iva"] = df["ventas"] * 1.21   

# 7. Loop over DataFrame (Iterrows)
# Recorre el DataFrame fila a fila (sólo para inspección visual, no para cálculos pesados)
for etiqueta, fila in df.iterrows():
    print(f"Fila {etiqueta}: La ciudad {fila['ciudad']} vendió {fila['ventas']} unidades")

```

---

# 3. Ejercicios Práctica 3

### Ejercicio 1 — Catálogo de productos como lista

Una tienda gestiona su catálogo en una lista plana que alterna nombre de producto y precio. Trabaja con esta estructura sin convertirla todavía a diccionario.

```python
catalogo = [
    "Auriculares BT", 59.90,
    "Smartwatch S2", 149.00,
    "Tablet 10", 219.00,
    "Cargador USB-C", 18.50,
    "Cafetera Expres", 189.00,
    "Robot Aspirador", 279.00,
]

```

**Se pide:**
1. Imprime el número total de elementos de la lista y deduce cuántos productos hay.
2. Usando *slicing con paso*, crea `nombres` con todos los nombres y `precios` con todos los precios.
3. Obtén el precio del `"Robot Aspirador"` **sin escribir el índice a mano**: localiza su posición con `.index()` y usa esa posición para llegar al precio.
4. Crea `ultimos_tres_nombres` con los tres últimos productos usando índices negativos.
5. Calcula e imprime el precio medio del catálogo redondeado a dos decimales, usando únicamente `sum()`, `len()` y `round()`.

**Comprobación:** `nombres` debe tener 6 elementos y `precios` debe sumar 914,40.

> **Pista:** `lista[inicio:fin:paso]`. Con paso 2 desde el índice 0 obtienes las posiciones pares, desde el índice 1 las impares.
> 
> 

* **Técnicas:** `len()`, división entera `//`, *slicing* con paso `[inicio:fin:paso]`, `.index()`, índices negativos `[-n:]`, `sum()`, `round()`.

```python
catalogo = [
    "Auriculares BT", 59.90,
    "Smartwatch S2", 149.00,
    "Tablet 10", 219.00,
    "Cargador USB-C", 18.50,
    "Cafetera Expres", 189.00,
    "Robot Aspirador", 279.00,
]

# 1. Total de elementos y deducción del número de productos
total_elementos = len(catalogo)
num_productos = total_elementos // 2
print(f"Total elementos en la lista: {total_elementos}")
print(f"Número de productos: {num_productos}")

# 2. Desdoble mediante slicing con paso
nombres = catalogo[0::2]   # Índices pares: 0, 2, 4, 6, 8, 10
precios = catalogo[1::2]   # Índices impares: 1, 3, 5, 7, 9, 11
print(f"Nombres ({len(nombres)}): {nombres}")
print(f"Precios ({len(precios)}): {precios}")

# 3. Localización dinámica del precio del "Robot Aspirador"
pos_robot = nombres.index("Robot Aspirador")
precio_robot = precios[pos_robot]
print(f"Precio del Robot Aspirador (índice {pos_robot}): {precio_robot} €")

# 4. Tres últimos productos con índices negativos
ultimos_tres_nombres = nombres[-3:]
print(f"Últimos tres productos: {ultimos_tres_nombres}")

# 5. Precio medio del catálogo redondeado a dos decimales
precio_medio = round(sum(precios) / len(precios), 2)
print(f"Suma total de precios: {round(sum(precios), 2)} €")
print(f"Precio medio: {precio_medio} €")
```

![Ejercicio 1 en JupyterLab](images/e1.png)

**Explicación:**
* Calculamos la cantidad total de elementos con `len(catalogo)` y aplicamos división entera `// 2` para deducir el volumen de artículos del catálogo considerando la paridad fija de datos.
* Desdoblamos la lista plana aplicando *slicing* con paso 2: `catalogo[0::2]` extrae los elementos en posiciones pares (nombres) y `catalogo[1::2]` los de posiciones impares (precios).
* Localizamos dinámicamente la posición del artículo con `nombres.index("Robot Aspirador")` e indexamos con ese valor sobre la lista `precios` para obtener su coste sin escribir índices a mano.
* Extraemos la cola del catálogo con la rebanada negativa `nombres[-3:]`, que toma desde el antepenúltimo elemento hasta el final de la colección.
* Obtenemos el precio medio combinando `sum(precios)` entre `len(precios)` y acotando a dos decimales con `round(..., 2)`.
* **Tip (*Slicing* con paso y listas paralelas):** El *slicing* `[inicio:fin:paso]` no modifica la lista original, genera una nueva sublista en memoria y mantiene la correspondencia posicional $1:1$ entre listas homólogas (`nombres[i]` siempre corresponde a `precios[i]`).
* ⚠️ **Trampa técnica:** Intentar localizar el precio sumando 1 al índice original sobre la lista cruda (`catalogo[catalogo.index("Robot Aspirador") + 1]`) fallará si la lista pierde su paridad o se agregan atributos adicionales; desdoblar previamente en dos listas independientes garantiza que las búsquedas mediante `.index()` sean deterministas y seguras.

**Comentario:**
Identifiqué que la alternancia uniforme de nombres y precios permitía desacoplar la estructura plana en dos vectores paralelos mediante *slicing* con paso 2 (`[0::2]` y `[1::2]`), evitando bucles manuales. Para obtener el precio del robot aspirador utilicé `.index()` sobre la lista de nombres para recuperar de forma dinámica su posición ordinal y cruzarla sobre la lista de precios. Empleé índices negativos `[-3:]` para aislar los últimos registros con independencia del tamaño de la lista y calculé la media combinando `sum()`, `len()` y `round(..., 2)`.

--- 





