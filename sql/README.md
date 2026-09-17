# SQL — PostgreSQL (Northwind) — Chuleta de examen

> **Cómo usar esto en el examen:** 1) busca la técnica que necesitas en la matriz de abajo → 2) salta directo al ejercicio que la usa → 3) mira el enunciado, el código y la captura, y adapta el patrón al enunciado nuevo. No lo leas de arriba a abajo, es para buscar y volver al trabajo.

---

# 1. Matrices de referencia por tipo de técnica

Divididas en subíndices — busca primero el bloque (JOINs, subconsultas...) y luego la fila.

### 1.1 Filtrado y agregación

| Técnica | Para qué sirve | Resuelta en |
|---|---|---|
| `WHERE`, `BETWEEN`, `IN`, `LIKE` | Filtrar filas antes de agrupar: por rango, lista o patrón de texto | 1, 3, 4 |
| `NULL` y `COALESCE()` | Sustituir un valor ausente por un valor por defecto para que no rompa cálculos ni desaparezca filas sin avisar | 3, 7, 9, 10, 19 |
| Funciones de agregación y `ROUND()` | Resumir muchas filas en un número (`SUM`, `COUNT`, `AVG`...) con precisión correcta | 1, 2, 6, 14 |
| `GROUP BY` y `HAVING` | Agrupar filas y filtrar sobre el resultado ya agregado (no sobre las filas originales) | 2, 6 |
| `CASE WHEN` | Columna calculada con lógica condicional tipo if/elif/else | 3, 10, 17, 20 |

### 1.2 JOINs (tablas / adiciones externas)

| Técnica | Para qué sirve | Resuelta en |
|---|---|---|
| `INNER JOIN`, alias, `USING` | Combinar tablas trayendo solo las filas que coinciden en ambas | 4, 5, 6 |
| `LEFT JOIN` | Combinar tablas conservando todas las filas de la izquierda aunque no haya match a la derecha | 7, 9 |
| `SELF JOIN` | Unir una tabla consigo misma para relacionar filas entre sí (jerarquías) | 8 |
| `CROSS JOIN` | Generar todas las combinaciones posibles entre dos conjuntos (rejillas sin huecos) | 9 |
| `FULL JOIN` | Conservar filas de ambos lados aunque no haya match | 10 |

### 1.3 Operadores de conjunto y semi/anti join

| Técnica | Para qué sirve | Resuelta en |
|---|---|---|
| `UNION ALL` | Apilar resultados de varias consultas con las mismas columnas, sin eliminar duplicados | 11 |
| `EXCEPT` | Filas de la primera consulta que NO están en la segunda | 12 |
| `INTERSECT` | Filas que están en ambas consultas a la vez | 12 |
| Semi join y anti join | Filtrar filas de una tabla según si existen (o no) en otra, sin traer columnas de la segunda | 12, 13 |

### 1.4 Subconsultas

| Técnica | Para qué sirve | Resuelta en |
|---|---|---|
| Subconsulta en `WHERE` | Usar el resultado de otra consulta como condición de filtro | 13, 14, 16 |
| Subconsulta en `SELECT` | Calcular un valor extra por fila con una subconsulta escalar | 14, 16 |
| Subconsulta en `FROM` | Tratar el resultado de una consulta como si fuera una tabla (necesita alias) | 15 |
| Subconsultas correlacionadas | Subconsulta que referencia una columna de la fila externa; se evalúa por cada fila | 13, 16 |

### 1.5 CTE y funciones de ventana

| Técnica | Para qué sirve | Resuelta en |
|---|---|---|
| CTE (`WITH`) | Nombrar una subconsulta para reutilizarla y encadenar pasos de forma legible | 17, 18, 19, 20 |
| `NTILE()` | Repartir filas ordenadas en N grupos de tamaño similar (cuartiles...) | 17 |
| `RANK()` / `ROW_NUMBER()` | Numerar filas según un orden; `RANK` deja huecos en empates, `ROW_NUMBER` no | 18 |
| `PARTITION BY` | Reinicia el cálculo de una función de ventana para cada grupo, sin colapsar filas | 18 |
| Marcos de ventana (`ROWS BETWEEN`) | Define exactamente qué filas entran en el cálculo (ej. media móvil) | 19 |
| `LAG()` | Trae el valor de una fila anterior en el mismo resultado, sin self join | 19 |

### 1.6 Pivotado, fechas y texto

| Técnica | Para qué sirve | Resuelta en |
|---|---|---|
| Pivotado y `ROLLUP` | Convierte filas en columnas y añade una fila de totales automática | 20 |
| Funciones de texto | Mayúsculas, concatenación, recorte de cadenas | 11 |
| Funciones de fecha (`EXTRACT`, `DATE_TRUNC`) | Extraer parte de una fecha o truncarla a un periodo (mes, año) | 9, 19, 20 |

---

# 2. Chuleta de sintaxis rápida

### 2.1 Filtrado y agregación
```sql
WHERE precio BETWEEN 10 AND 50          -- incluye ambos extremos
WHERE pais IN ('Italia','Francia')
WHERE nombre LIKE 'A%'                  -- empieza por A
ROUND(valor::numeric, 2)                -- castea ANTES de redondear (evita errores de real)
CASE WHEN stock = 0 THEN 'CRÍTICO' ELSE 'AVISO' END AS situacion
```
```sql
SELECT pais, COUNT(*) AS num, COUNT(DISTINCT ciudad) AS num_ciudades
FROM clientes
GROUP BY pais
HAVING COUNT(*) >= 5                    -- HAVING filtra DESPUÉS de agrupar
ORDER BY num DESC;
```

### 2.2 JOINs
```sql
-- INNER: solo lo que coincide en ambas
FROM a INNER JOIN b ON a.id = b.a_id
FROM a INNER JOIN b USING(id)           -- si la columna se llama igual en ambas tablas

-- LEFT: todo lo de la izquierda, aunque no haya match (NULL a la derecha)
FROM clientes c LEFT JOIN pedidos p ON p.cliente_id = c.id
-- OJO: COUNT(*) cuenta también las filas sin match → usa COUNT(p.id)

-- SELF JOIN: la misma tabla dos veces, con alias obligatorios
FROM empleados emp LEFT JOIN empleados jefe ON emp.reports_to = jefe.id

-- CROSS JOIN: producto cartesiano, para rejillas sin huecos
FROM categorias CROSS JOIN anios        -- luego LEFT JOIN con los datos reales

-- FULL JOIN: cuidado con la columna de unión, puede venir NULL por cualquier lado
SELECT COALESCE(a.pais, b.pais) AS pais FROM a FULL JOIN b ON a.pais = b.pais
```

### 2.3 Operadores de conjunto y semi/anti join
```sql
SELECT x FROM a
UNION ALL                               -- no elimina duplicados (UNION sí, y es más caro)
SELECT x FROM b;

SELECT pais FROM clientes
EXCEPT                                  -- lo que hay en la primera y no en la segunda
SELECT pais FROM proveedores;

SELECT pais FROM clientes
INTERSECT                               -- lo que hay en ambas
SELECT pais FROM proveedores;
```
```sql
-- NOT EXISTS: la forma segura (preferible)
WHERE NOT EXISTS (SELECT 1 FROM pedidos p WHERE p.cliente_id = c.id)

-- NOT IN: PELIGRO si la subconsulta puede devolver NULL → resultado vacío sin error
WHERE id NOT IN (SELECT cliente_id FROM pedidos WHERE cliente_id IS NOT NULL)
```

### 2.4 Subconsultas
```sql
-- Escalar en WHERE
WHERE precio > (SELECT AVG(precio) FROM productos)

-- Escalar en SELECT
SELECT precio, (SELECT AVG(precio) FROM productos) AS precio_medio FROM productos

-- En FROM: SIEMPRE necesita alias
FROM (SELECT cliente_id, SUM(importe) AS total FROM pedidos GROUP BY cliente_id) AS resumen

-- Correlacionada: referencia una columna de la fila externa
WHERE precio = (SELECT MAX(precio) FROM productos p2 WHERE p2.categoria_id = p1.categoria_id)
```

### 2.5 CTE y funciones de ventana
```sql
WITH paso1 AS (
    SELECT ...
),
paso2 AS (
    SELECT ... FROM paso1 ...           -- una CTE puede usar las anteriores
)
SELECT ... FROM paso2;
```
```sql
RANK()       OVER (PARTITION BY categoria ORDER BY facturacion DESC)  -- dejar huecos en empate
ROW_NUMBER() OVER (PARTITION BY categoria ORDER BY facturacion DESC)  -- nunca empata
NTILE(4)     OVER (ORDER BY facturacion DESC)                         -- cuartiles
SUM(x) OVER (ORDER BY mes)                                            -- acumulado (RANGE por defecto)
AVG(x) OVER (ORDER BY mes ROWS BETWEEN 2 PRECEDING AND CURRENT ROW)   -- media móvil 3 periodos, marco EXPLÍCITO
LAG(x)  OVER (ORDER BY mes)                                           -- valor de la fila anterior
-- OJO: no se puede filtrar una función de ventana en WHERE → mételo en una CTE y filtra fuera
```

### 2.6 Pivotado, fechas y texto
```sql
SUM(importe) FILTER (WHERE anio = 1997) AS f_1997     -- sintaxis moderna de PostgreSQL
GROUP BY ROLLUP(categoria)                             -- añade fila de totales, categoria queda NULL en esa fila
COALESCE(categoria, 'TOTAL GENERAL') AS categoria       -- para que la fila de totales se lea bien
```
```sql
EXTRACT(YEAR FROM fecha)
DATE_TRUNC('month', fecha)
UPPER(texto)  ||  ' - '  ||  otro_texto        -- concatenación con ||
```

---

# 3. Ejercicios
## Sección 1. Fundamentos: filtrado y agregación
### Pregunta 1 — Catálogo comercial activo

El equipo de ventas prepara la tarifa de la próxima campaña y necesita el catálogo depurado.

Obtén los productos que **no** están descatalogados y cuyo precio unitario esté entre 10 y 50 euros, ambos incluidos. Muestra el nombre del producto y su precio redondeado a dos decimales, ordenado de mayor a menor precio.

**Columnas esperadas:** `producto`, `precio`

- **Pista:** la columna `discontinued` es de tipo `integer`, no booleana. Un producto activo tiene valor 0.

* **Lo que se pide:** Productos no descatalogados con precio unitario entre 10 y 50€ (ambos incluidos). Nombre y precio redondeado a 2 decimales, ordenado de mayor a menor precio.

* **Técnicas:** `WHERE`, `BETWEEN`, `ROUND()`, alias de columna, `ORDER BY`

```sql
SELECT product_name AS producto,
       ROUND(unit_price::numeric, 2) AS precio
FROM products
WHERE discontinued = 0
  AND unit_price BETWEEN 10 AND 50
ORDER BY precio DESC;
```

**Explicación:**

* Filtramos los productos activos con la condición `discontinued = 0`, atendiendo a que el tipo de dato es un entero de control y no un booleano.

* Acotamos el rango comercial mediante `BETWEEN 10 AND 50`, operador inclusivo que contempla ambos extremos.

* **Tip (Casteo a `::numeric`):** Las columnas con valores de precio vienen declaradas en tipo `real`, por lo que es necesario castear con `::numeric` antes del redondeo para no propagar imprecisiones de coma flotante.

* **Tip (Alias en `ORDER BY`):** PostgreSQL permite reutilizar directamente el alias de columna asignado (`precio`) dentro de la cláusula `ORDER BY` para resolver la ordenación descendente.

**Comentario:**
Leyendo el enunciado identifiqué que se debían filtrar únicamente referencias comerciales activas, traduciéndolo en `discontinued = 0` según la pista técnica sobre el tipo entero. A continuación acoté el rango monetario solicitado mediante `BETWEEN 10 AND 50`, garantizando la inclusión de ambos extremos.Apliqué `ROUND(unit_price::numeric, 2)` con alias `precio` para cumplir con el formato monetario exigido y el casteo preventivo de coma flotante. Por último, utilicé `ORDER BY precio DESC` para presentar el catálogo ordenado de mayor a menor coste.

![Resultado pregunta 1](images/p01.png)

---

### Pregunta 2 — Concentración geográfica de la cartera

Dirección quiere saber en qué mercados está realmente concentrada la base de clientes antes de decidir dónde abrir delegación.

Cuenta cuántos clientes hay en cada país y muestra únicamente aquellos países con **5 o más clientes**, ordenados de mayor a menor. Indica también cuántas ciudades distintas hay en cada uno de esos países.

**Columnas esperadas:** `pais`, `num_clientes`, `num_ciudades`

- **Pista:** `HAVING` filtra después de agrupar; `WHERE` filtra antes. Aquí la condición se aplica sobre el resultado de un conteo, así que solo una de las dos cláusulas sirve.
- **Lo que se pide:** Países con 5 o más clientes, recuento total de clientes por país y recuento de ciudades únicas, ordenados descendentemente por volumen de clientes.
- **Técnicas:** `GROUP BY`, `COUNT()`, `COUNT(DISTINCT ...)`, `HAVING`


**Solución:**
```sql
SELECT country AS pais,
       COUNT(customer_id) AS num_clientes,
       COUNT(DISTINCT city) AS num_ciudades
FROM customers
GROUP BY country
HAVING COUNT(customer_id) >= 5
ORDER BY num_clientes DESC;
```

**Explicación:**
- Agrupamos la tabla customers por country para consolidar las métricas territoriales[cite: 1, 3].
- COUNT(customer_id) calcula el número de cuentas registradas en cada país[cite: 1, 3].
- COUNT(DISTINCT city) evita contar dos veces la misma ciudad cuando contiene varios clientes (como ocurre con Londres o São Paulo)[cite: 1, 3].
- Tip (WHERE vs HAVING): WHERE filtra registros individuales antes de agrupar; HAVING filtra métricas agregadas después de aplicar el GROUP BY. Al filtrar por un recuento (COUNT >= 5), WHERE arrojaría un error de sintaxis.
- Tip (COUNT vs COUNT DISTINCT): COUNT(columna) cuenta cuántos registros no nulos existen; COUNT(DISTINCT columna) evalúa el conjunto y descarta valores repetidos antes de sumar.
  
**Comentario:**
- Partí agrupando la tabla `customers` por `country` al requerir el enunciado métricas consolidadas por territorio.
- Calculé el volumen con `COUNT(customer_id)` y agregué DISTINCT sobre city para no duplicar ciudades con múltiples clientes.
- Para cumplir la condición de negocio de 5 o más clientes descarté WHERE y apliqué HAVING directamente sobre el agregado.
- Cerré con ORDER BY num_clientes DESC para ordenar de mayor a menor concentración geográfica como exigía la petición.

![Resultado pregunta 2](imgages/p02.png)

---

### Pregunta 3 — Alerta de reposición

Localiza los productos activos cuyas unidades en stock sean **inferiores o iguales** a su nivel de reposición. Muestra el nombre, las unidades en stock, el nivel de reposición, las unidades ya pedidas al proveedor y una columna de texto que indique `'CRÍTICO'` cuando el stock sea 0 y `'AVISO'` en el resto de casos.

**Columnas esperadas:** `producto`, `stock`, `nivel_reposicion`, `pedido_a_proveedor`, `situacion`

* **Lo que se pide:** Productos en riesgo de rotura de stock (`discontinued = 0` y `stock <= nivel_reposicion`) con una columna condicional de alerta.

* **Técnicas:** `WHERE` con comparación entre columnas, `CASE WHEN`

```sql
-- Productos activos en riesgo de stock y clasificación de alerta
SELECT product_name AS producto,
       units_in_stock AS stock,
       reorder_level AS nivel_reposicion,
       units_on_order AS pedido_a_proveedor,
       CASE
           WHEN units_in_stock = 0 THEN 'CRÍTICO'
           ELSE 'AVISO'
       END AS situacion
FROM products
WHERE discontinued = 0
  AND units_in_stock <= reorder_level;

```

**Explicación:**
* Comparamos dos columnas numéricas dentro del `WHERE` (`units_in_stock <= reorder_level`) junto al filtro de catálogo activo `discontinued = 0`.

* La estructura `CASE WHEN` bifurca el estado: evalúa primero la condición de stock nulo para etiquetar `'CRÍTICO'` y deriva cualquier otro valor residual a `'AVISO'`.

* **Tip (Lógica trivaluada y NULLs):** En SQL, cualquier comparación contra `NULL` resulta en `UNKNOWN` (falso para el `WHERE`), omitiendo la fila silenciosamente. Tras revisar `products`, estas columnas no contienen nulos; en tablas con nulos potenciales se blindaría usando `COALESCE(units_in_stock, 0) <= COALESCE(reorder_level, 0)`.

**Comentario:**
A partir del enunciado, filtré en `WHERE` las referencias activas mediante `discontinued = 0` y el riesgo de inventario comparando directamente `units_in_stock <= reorder_level`. Verifiqué previamente que ninguna de estas columnas contuviera valores nulos para evitar descartes accidentales por la lógica trivaluada de SQL. Seguidamente, categoricé la severidad mediante la estructura condicional `CASE WHEN units_in_stock = 0 THEN 'CRÍTICO' ELSE 'AVISO' END` asignándole el alias `situacion`. Finalmente, proyecté las cinco columnas solicitadas con sus correspondientes nombres comerciales de negocio.

![Resultado pregunta 2](imgages/p03.png)

---

## Sección 2. INNER JOIN

### Pregunta 4 — Ficha completa de producto

Para los productos suministrados por empresas de **Italia, Francia o España**, muestra el nombre del producto, el nombre de la categoría, el nombre del proveedor, su país y su ciudad. Ordena por país y, dentro de cada país, por nombre de producto.

**Columnas esperadas:** `producto`, `categoria`, `proveedor`, `pais`, `ciudad`

* **Lo que se pide:** Ficha de productos suministrados desde Italia, Francia o España con su categoría, nombre de proveedor y ubicación geográfica, ordenados por país y luego por producto.

* **Técnicas:** `INNER JOIN` de tres tablas, alias de tabla, `WHERE ... IN`


```sql
SELECT p.product_name AS producto,
       c.category_name AS categoria,
       s.company_name AS proveedor,
       s.country AS pais,
       s.city AS ciudad
FROM products p
INNER JOIN categories c ON p.category_id = c.category_id
INNER JOIN suppliers s ON p.supplier_id = s.supplier_id
WHERE s.country IN ('Italy', 'France', 'Spain')
ORDER BY s.country, p.product_name;

```

**Explicación:**
* Usamos `products` como tabla central para cruzar simultáneamente con `categories` (vía `category_id`) y con `suppliers` (vía `supplier_id`).

* Filtramos geográficamente en la tabla del proveedor mediante `WHERE s.country IN ('Italy', 'France', 'Spain')`, ya que `products` no contiene datos de localización.

* **Tip (`INNER JOIN` múltiple):** Cada cláusula `INNER JOIN` requiere su propia condición `ON`; se evalúan de forma acumulativa conservando únicamente las filas que coincidan en todas las tablas.

* **Tip (`IN` vs `OR`):** La cláusula `IN (...)` simplifica la lectura y optimización del código frente a encadenar comparaciones con `s.country = 'Italy' OR s.country = 'France'...`.

**Comentario:**
Al revisar el enunciado observé que la información geográfica no está en `products` sino en `suppliers`, lo que obligaba a cruzar tres tablas tomando `products` como eje central. Relacioné `products` con `categories` y `suppliers` mediante dos `INNER JOIN` sucesivos empleando sus respectivas claves ajenas y alias de tabla. Añadí el filtro `WHERE s.country IN ('Italy', 'France', 'Spain')` para limitar los países proveedores demandados de forma limpia. Por último, ordené por `s.country` y `p.product_name` para estructurar la salida alfabéticamente por territorio y catálogo como requería el enunciado.

![Resultado pregunta 4](img/p04.png)

---

### Pregunta 5 — Detalle valorizado de un pedido

Atención al cliente recibe una reclamación sobre el pedido **10248** y necesita reconstruir la factura línea a línea. Muestra, para ese pedido, el nombre del producto, el precio unitario aplicado, la cantidad, el descuento y el importe final de cada línea. Añade el nombre del cliente y la fecha del pedido.

**Columnas esperadas:** `cliente`, `fecha_pedido`, `producto`, `precio_unitario`, `cantidad`, `descuento`, `importe_linea`

* **Lo que se pide:** Detalle de líneas del pedido 10248 con datos del cliente, fecha del pedido, producto adquirido y cálculo del importe neto exacto por línea redondeado a 2 decimales.

* **Técnicas:** `INNER JOIN` con `USING`, aritmética entre columnas, `ROUND()`

```sql
-- Detalle de las líneas del pedido 10248 con importe neto calculado
SELECT c.company_name AS cliente,
       o.order_date AS fecha_pedido,
       p.product_name AS producto,
       od.unit_price AS precio_unitario,
       od.quantity AS cantidad,
       od.discount AS descuento,
       ROUND((od.unit_price::numeric) * od.quantity * (1 - od.discount::numeric), 2) AS importe_linea
FROM orders o
INNER JOIN customers c USING (customer_id)
INNER JOIN order_details od USING (order_id)
INNER JOIN products p USING (product_id)
WHERE o.order_id = 10248;

```

**Explicación:**
* Se vincula `orders` con `customers` para extraer el nombre de la empresa compradora y la fecha, con `order_details` para las unidades y precios facturados, y con `products` para obtener la denominación comercial de cada artículo.

* El importe neto de cada línea se calcula con la expresión estandarizada `precio * cantidad * (1 - descuento)`.

* **Tip (`USING`):** Cuando las columnas de enlace comparten el mismo nombre en ambas tablas (`order_id`, `product_id`, `customer_id`), la cláusula `USING (columna)` sustituye a `ON a.col = b.col`, evitando código redundante y duplicidad de columnas en el resultado.

* **Tip (Casteo a `::numeric`):** Dado que `unit_price` y `discount` se definen originalmente como tipo `real`, el casteo `::numeric` es obligatorio antes de operar y aplicar `ROUND(..., 2)` para suprimir inconsistencias de coma flotante.

**Comentario:**
A partir del enunciado identifiqué que la reconstrucción de la factura requería enlazar `orders`, `customers`, `order_details` y `products` para reunir la cabecera del pedido, los datos del cliente y el desglose de productos. Aproveché la coincidencia de nombres en las claves para aplicar `INNER JOIN ... USING(...)`, simplificando las uniones de las cuatro tablas. Implementé la fórmula acordada para `importe_linea` utilizando el casteo `::numeric` antes del `ROUND(..., 2)` para evitar fallos de precisión monetaria por el tipo `real`. Finalmente, restringí el resultado con `WHERE o.order_id = 10248` para aislar exclusivamente las líneas del pedido reclamado.

![Resultado pregunta 5](img/p05.png)

---

### Pregunta 6 — Ranking de categorías por facturación

Calcula la facturación total de cada categoría durante toda la historia de la compañía. Muestra el nombre de la categoría, el número de líneas de pedido que ha generado, el número de productos distintos vendidos y la facturación total. Incluye únicamente las categorías que superen los **100.000 euros** de facturación, ordenadas de mayor a menor.

**Columnas esperadas:** `categoria`, `num_lineas`, `num_productos`, `facturacion`

* **Lo que se pide:** Agrupar el rendimiento comercial por familia de producto calculando líneas totales, productos únicos y facturación acumulada superior a 100.000€, ordenado de mayor a menor.

* **Técnicas:** `INNER JOIN` de tres tablas, `GROUP BY`, `SUM()`, `COUNT(DISTINCT ...)`, `HAVING`, `ROUND()`

```sql
-- Facturación histórica agregada por categoría para familias que superan 100.000€
SELECT c.category_name AS categoria,
       COUNT(od.product_id) AS num_lineas,
       COUNT(DISTINCT od.product_id) AS num_productos,
       SUM(ROUND((od.unit_price::numeric) * od.quantity * (1 - od.discount::numeric), 2)) AS facturacion
FROM categories c
INNER JOIN products p USING (category_id)
INNER JOIN order_details od USING (product_id)
GROUP BY c.category_name
HAVING SUM(ROUND((od.unit_price::numeric) * od.quantity * (1 - od.discount::numeric), 2)) > 100000
ORDER BY facturacion DESC;

```

**Explicación:**
* Se enlazan `categories`, `products` y `order_details` para asociar cada línea de venta con su respectiva categoría.

* `COUNT(od.product_id)` cuenta el total de transacciones de línea generadas, mientras que `COUNT(DISTINCT od.product_id)` calcula cuántos artículos distintos de esa familia tuvieron ventas efectivas.

* Se agrega la facturación monetaria aplicando la fórmula estandarizada con casteo a `::numeric` para eliminar inconsistencias de tipo flotante.

* **Tip (`HAVING` vs alias):** Como PostgreSQL procesa `HAVING` antes de resolver los alias del `SELECT`, no se puede filtrar directamente por `HAVING facturacion > 100000`; es obligatorio repetir la función de agregación completa `SUM(...)` dentro del `HAVING`.

**Comentario:**
A partir del enunciado identifiqué que debía unir `categories`, `products` y `order_details` para vincular cada venta con su categoría comercial correspondiente. Agrupé los registros por `category_name`, extrayendo el volumen de líneas con `COUNT(od.product_id)` y la diversidad de catálogo vendido con `COUNT(DISTINCT od.product_id)`. Para obtener la facturación total acumulé las líneas con `SUM(...)` implementando el casteo monetario `::numeric` obligatorio de la práctica. Dado que el corte de 100.000€ actúa sobre el total agregado, apliqué `HAVING` repitiendo la expresión de suma y ordené el informe con `ORDER BY facturacion DESC`.

![Resultado pregunta 6](img/p06.png)

---

## Sección 3. Uniones externas, reflexivas y cruzadas

### Pregunta 7 — Clientes sin actividad comercial
**Enunciado:** Todos los clientes con su número de pedidos y fecha de último pedido. Los clientes sin pedidos aparecen igual, con 0 y `'SIN PEDIDOS'`. Inactivos primero.
**Técnicas:** `LEFT JOIN`, `COALESCE()`, `COUNT()` sobre columna
*(tienes una solución de referencia en el Apéndice si te atascas)*

```sql

```

![Resultado pregunta 7](img/p07.png)

**Explicación:**
-
-

---

### Pregunta 8 — Organigrama de la fuerza de ventas
**Enunciado:** Cada empleado con su nombre completo, cargo, nombre completo de su responsable y cargo del responsable. Quien no reporta a nadie: `'DIRECCIÓN GENERAL'`.
**Técnicas:** `SELF JOIN` con `LEFT JOIN`, alias obligatorios, concatenación de texto, `COALESCE()`

```sql

```

![Resultado pregunta 8](img/p08.png)

**Explicación:**
-
-

---

### Pregunta 9 — Rejilla de cobertura categoría × año
**Enunciado:** Las 24 combinaciones posibles de 8 categorías × 3 años, con su facturación (0 si no hay), sin huecos. Ordenado por categoría y año.
**Técnicas:** `CROSS JOIN` para la rejilla, `LEFT JOIN` contra datos reales, `COALESCE()`, `EXTRACT()`

```sql

```

![Resultado pregunta 9](img/p09.png)

**Explicación:**
-
-

---

### Pregunta 10 — Mapa de países: clientes frente a proveedores
**Enunciado:** Por cada país con presencia: nº clientes, nº proveedores y `tipo_presencia` (`'SOLO CLIENTES'`, `'SOLO PROVEEDORES'` o `'AMBOS'`).
**Técnicas:** `FULL JOIN` entre dos subconsultas agregadas, `COALESCE()`, `CASE WHEN`

```sql

```

![Resultado pregunta 10](img/p10.png)

**Explicación:**
-
-

---

### Pregunta 11 — Directorio unificado de contactos
**Enunciado:** Una sola tabla con contactos de clientes, proveedores y empleados: origen, nombre de contacto en mayúsculas, organización, ciudad y país. Ordenado por origen y país.
**Técnicas:** `UNION ALL`, `UPPER()`, concatenación con `||`, literales como columna

```sql

```

![Resultado pregunta 11](img/p11.png)

**Explicación:**
-
-

---

### Pregunta 12 — Mercados con desequilibrio
**Enunciado:** a) Países con clientes pero ningún proveedor. b) Países con clientes y proveedores a la vez. Dos consultas, ambas ordenadas alfabéticamente.
**Técnicas:** `EXCEPT`, `INTERSECT`

```sql

```

![Resultado pregunta 12](img/p12.png)

**Explicación:**
-
-

---

### Pregunta 13 — Clientes que nunca han comprado pescado
**Enunciado:** Clientes que nunca incluyeron un producto `'Seafood'` en ningún pedido. Cliente, país y nº de pedidos que sí ha realizado, de mayor a menor.
**Técnicas:** anti join con `NOT EXISTS`, subconsulta correlacionada
*(tienes una solución de referencia en el Apéndice si te atascas)*

```sql

```

![Resultado pregunta 13](img/p13.png)

**Explicación:**
-
-

---

### Pregunta 14 — Productos por encima de la media
**Enunciado:** Productos activos con precio superior a la media de todo el catálogo. Precio, precio medio general y diferencia, redondeados. Ordenado por diferencia descendente.
**Técnicas:** subconsulta escalar en `WHERE`, subconsulta escalar en `SELECT`, aritmética

```sql

```

![Resultado pregunta 14](img/p14.png)

**Explicación:**
-
-

---

### Pregunta 15 — Ticket medio por cliente
**Enunciado:** Para cada cliente: nº pedidos, importe total y ticket medio (calculado en dos niveles: importe por pedido, luego media por cliente). Los 15 con mayor ticket medio.
**Técnicas:** subconsulta en `FROM` (con alias obligatorio), agregación en dos niveles, `LIMIT`

```sql

```

![Resultado pregunta 15](img/p15.png)

**Explicación:**
-
-

---

### Pregunta 16 — El producto más caro de cada categoría
**Enunciado:** Para cada categoría, el producto con el precio más alto: categoría, producto, precio y precio medio de su categoría. Resuelto con subconsulta correlacionada.
**Técnicas:** subconsulta correlacionada en `WHERE`, subconsulta correlacionada en `SELECT`, `INNER JOIN`

```sql

```

![Resultado pregunta 16](img/p16.png)

**Explicación:**
-
-

---

### Pregunta 17 — Segmentación ABC de la cartera de clientes
**Enunciado:** Con CTEs: facturación total por cliente, repartida en cuartiles, etiquetada (`A - Estratégico`...`D - Marginal`). Por segmento: nº clientes, facturación total y % sobre el total.
**Técnicas:** CTEs encadenadas, `NTILE()`, `CASE WHEN`, agregación sobre CTE
*(tienes una solución de referencia en el Apéndice si te atascas)*

```sql

```

![Resultado pregunta 17](img/p17.png)

**Explicación:**
-
-

---

### Pregunta 18 — Los tres productos más vendidos de cada categoría
**Enunciado:** Los 3 productos con mayor facturación de cada categoría: categoría, posición en categoría, producto, unidades, facturación y posición global en la empresa.
**Técnicas:** `RANK()` con `PARTITION BY`, `RANK()` sin `PARTITION BY`, CTE para poder filtrar
*(tienes una solución de referencia en el Apéndice si te atascas)*

```sql

```

![Resultado pregunta 18](img/p18.png)

**Explicación:**
-
-

---

### Pregunta 19 — Evolución mensual con acumulado y media móvil
**Enunciado:** Para cada mes de 1997: facturación, acumulado desde enero, media móvil de 3 meses, facturación del mes anterior y variación % respecto al anterior.
**Técnicas:** `DATE_TRUNC()`, `SUM() OVER` acumulado, marco explícito `ROWS BETWEEN`, `LAG()`
*(tienes una solución de referencia en el Apéndice si te atascas)*

```sql

```

![Resultado pregunta 19](img/p19.png)

**Explicación:**
-
-

---

### Pregunta 20 — Cuadro de mando anual por categoría
**Enunciado:** Una fila por categoría con facturación de 1996/1997/1998 en columnas, más total, fila de totales generales, peso % sobre el total y tendencia 1997→1998.
**Técnicas:** pivotado con `FILTER`, `ROLLUP`, `COALESCE()`, `CASE WHEN`, funciones de ventana para el peso
*(tienes una solución de referencia en el Apéndice si te atascas)*

```sql

```

![Resultado pregunta 20](img/p20.png)

**Explicación:**
-
-

---

## 4. Trampas generales — repaso de últimos 2 minutos

- `NULL` en cualquier comparación (`=`, `>`, `<`) no es ni verdadero ni falso → la fila desaparece sin error.
- `NOT IN` con una subconsulta que puede traer algún `NULL` → el resultado entero sale vacío, sin avisar. Usa `NOT EXISTS`.
- `HAVING` filtra **después** de `GROUP BY`; `WHERE` filtra **antes**. Si la condición usa una función de agregación, es `HAVING`.
- Las funciones de ventana no se pueden filtrar en `WHERE` de la misma consulta donde se calculan → CTE y filtras fuera.
- Toda subconsulta en `FROM` necesita alias, aunque no lo uses (`subquery in FROM must have an alias`).
- `::numeric` en todo cálculo monetario con `unit_price`, `discount`, `freight` (están en `real`, arrastran error de redondeo).
- `USING(columna)` solo si la columna se llama igual en ambas tablas; si no, `ON a.col = b.col`.
- `COUNT(*)` cuenta filas (incluidas las rellenadas por un LEFT JOIN); `COUNT(columna)` ignora los NULL de esa columna.

---

## 5. Apéndice — soluciones de referencia (7, 13, 17, 18, 19, 20)

Estas seis ya están resueltas y explicadas en detalle, por si te atascas con las técnicas más nuevas (anti join, CTE, NTILE, RANK+PARTITION, LAG, FILTER+ROLLUP). Puedes copiarlas tal cual a la Sección 3 (solo te faltaría la captura), o usarlas como referencia y escribir tú la tuya.

### Pregunta 7
```sql
SELECT c.company_name AS cliente,
       c.country AS pais,
       COUNT(o.order_id) AS num_pedidos,
       COALESCE(MAX(o.order_date)::text, 'SIN PEDIDOS') AS ultimo_pedido
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
GROUP BY c.company_name, c.country
ORDER BY num_pedidos ASC;
```
- `LEFT JOIN` y no `INNER JOIN` → si fuera INNER, los clientes sin pedidos desaparecerían.
- `COUNT(o.order_id)` y no `COUNT(*)` → `COUNT(*)` contaría 1 también para los clientes sin pedidos.
- `COALESCE(..., 'SIN PEDIDOS')` → sustituye el NULL de `MAX(order_date)` cuando no hay pedidos.


### Pregunta 13
```sql
SELECT c.company_name AS cliente,
       c.country AS pais,
       COUNT(o.order_id) AS pedidos_realizados
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
WHERE NOT EXISTS (
    SELECT 1
    FROM orders o2
    INNER JOIN order_details od ON od.order_id = o2.order_id
    INNER JOIN products p ON p.product_id = od.product_id
    INNER JOIN categories cat ON cat.category_id = p.category_id
    WHERE o2.customer_id = c.customer_id
      AND cat.category_name = 'Seafood'
)
GROUP BY c.company_name, c.country
ORDER BY pedidos_realizados DESC;
```
- `NOT EXISTS` + subconsulta correlacionada (`o2.customer_id = c.customer_id`) → comprueba cliente a cliente si existe algún pedido con Seafood.
- `NOT EXISTS` y no `NOT IN` → si `NOT IN` trae algún `NULL`, el resultado sale vacío sin error visible.

### Pregunta 17
```sql
WITH facturacion_cliente AS (
    SELECT c.customer_id,
           c.company_name,
           SUM(ROUND((od.unit_price::numeric) * od.quantity * (1 - od.discount::numeric), 2)) AS facturacion
    FROM customers c
    INNER JOIN orders o ON o.customer_id = c.customer_id
    INNER JOIN order_details od ON od.order_id = o.order_id
    GROUP BY c.customer_id, c.company_name
),
clientes_segmentados AS (
    SELECT *,
           NTILE(4) OVER (ORDER BY facturacion DESC) AS cuartil
    FROM facturacion_cliente
),
clientes_etiquetados AS (
    SELECT *,
           CASE cuartil
               WHEN 1 THEN 'A - Estratégico'
               WHEN 2 THEN 'B - Consolidado'
               WHEN 3 THEN 'C - Ocasional'
               WHEN 4 THEN 'D - Marginal'
           END AS segmento
    FROM clientes_segmentados
)
SELECT segmento,
       COUNT(*) AS num_clientes,
       ROUND(SUM(facturacion), 2) AS facturacion_segmento,
       ROUND(100.0 * SUM(facturacion) / SUM(SUM(facturacion)) OVER (), 2) AS porcentaje_sobre_total
FROM clientes_etiquetados
GROUP BY segmento
ORDER BY segmento;
```
- CTE 1 reduce pedidos+líneas a un número por cliente; CTE 2 reparte en cuartiles con `NTILE(4)`; CTE 3 traduce el cuartil a etiqueta.
- `SUM(SUM(facturacion)) OVER ()` → el SUM interior agrega por segmento, el `OVER()` sin PARTITION suma ese resultado ya agregado sobre TODAS las filas → total general para el %.

### Pregunta 18
```sql
WITH ventas_producto AS (
    SELECT p.product_id,
           p.product_name,
           c.category_name,
           SUM(od.quantity) AS unidades,
           ROUND(SUM((od.unit_price::numeric) * od.quantity * (1 - od.discount::numeric)), 2) AS facturacion
    FROM products p
    INNER JOIN categories c ON c.category_id = p.category_id
    INNER JOIN order_details od ON od.product_id = p.product_id
    GROUP BY p.product_id, p.product_name, c.category_name
),
ranking AS (
    SELECT *,
           RANK() OVER (PARTITION BY category_name ORDER BY facturacion DESC) AS posicion_en_categoria,
           RANK() OVER (ORDER BY facturacion DESC)                            AS posicion_global
    FROM ventas_producto
)
SELECT category_name AS categoria,
       posicion_en_categoria,
       product_name AS producto,
       unidades,
       facturacion,
       posicion_global
FROM ranking
WHERE posicion_en_categoria <= 3
ORDER BY category_name, posicion_en_categoria;
```
- Dos `RANK()`: uno con `PARTITION BY category_name` (podio por familia), otro sin partición (ranking global).
- El filtro `posicion_en_categoria <= 3` va en el SELECT final sobre la CTE, porque una ventana no se puede filtrar en el WHERE donde se calcula.

### Pregunta 19
```sql
WITH facturacion_mensual AS (
    SELECT DATE_TRUNC('month', o.order_date) AS mes,
           ROUND(SUM((od.unit_price::numeric) * od.quantity * (1 - od.discount::numeric)), 2) AS facturacion
    FROM orders o
    INNER JOIN order_details od ON od.order_id = o.order_id
    WHERE o.order_date >= '1997-01-01' AND o.order_date < '1998-01-01'
    GROUP BY DATE_TRUNC('month', o.order_date)
)
SELECT mes,
       facturacion,
       SUM(facturacion) OVER (ORDER BY mes) AS acumulado,
       ROUND(AVG(facturacion) OVER (ORDER BY mes ROWS BETWEEN 2 PRECEDING AND CURRENT ROW), 2) AS media_movil_3m,
       LAG(facturacion) OVER (ORDER BY mes) AS mes_anterior,
       ROUND(100.0 * (facturacion - LAG(facturacion) OVER (ORDER BY mes))
             / LAG(facturacion) OVER (ORDER BY mes), 2) AS variacion_pct
FROM facturacion_mensual
ORDER BY mes;
```
- `SUM(...) OVER (ORDER BY mes)` sin marco → acumulado por defecto. `AVG(...) ROWS BETWEEN 2 PRECEDING AND CURRENT ROW` → marco explícito, obligatorio para la media móvil.
- `LAG(facturacion)` trae el valor de la fila anterior sin self join. La primera fila da NULL en `mes_anterior` y en `variacion_pct` (correcto).

### Pregunta 20
```sql
WITH facturacion_categoria_anio AS (
    SELECT c.category_name,
           EXTRACT(YEAR FROM o.order_date)::int AS anio,
           (od.unit_price::numeric) * od.quantity * (1 - od.discount::numeric) AS importe
    FROM categories c
    INNER JOIN products p ON p.category_id = c.category_id
    INNER JOIN order_details od ON od.product_id = p.product_id
    INNER JOIN orders o ON o.order_id = od.order_id
)
SELECT COALESCE(category_name, 'TOTAL GENERAL') AS categoria,
       ROUND(SUM(importe) FILTER (WHERE anio = 1996), 2) AS f_1996,
       ROUND(SUM(importe) FILTER (WHERE anio = 1997), 2) AS f_1997,
       ROUND(SUM(importe) FILTER (WHERE anio = 1998), 2) AS f_1998,
       ROUND(SUM(importe), 2) AS total,
       ROUND(100.0 * SUM(importe) / SUM(SUM(importe)) OVER (), 2) AS peso_pct,
       CASE
           WHEN SUM(importe) FILTER (WHERE anio = 1998) > SUM(importe) FILTER (WHERE anio = 1997) THEN 'CRECIÓ'
           WHEN SUM(importe) FILTER (WHERE anio = 1998) < SUM(importe) FILTER (WHERE anio = 1997) THEN 'DECRECIÓ'
           ELSE 'IGUAL'
       END AS tendencia
       -- OJO: 1996 solo tiene medio año (jul-dic) y 1998 solo hasta mayo, la tendencia no es comparación justa sin normalizar.
FROM facturacion_categoria_anio
GROUP BY ROLLUP(category_name)
ORDER BY category_name NULLS LAST;
```
- `SUM(importe) FILTER (WHERE anio = 1997)` es el pivotado: suma solo cuando se cumple la condición, una columna por año.
- `GROUP BY ROLLUP(category_name)` añade la fila de totales (con `category_name` en NULL); `COALESCE` la etiqueta como `'TOTAL GENERAL'`.
