# Resultados: gAll vs histogramas de ciclos y periodicidad en n

Sesión del 2026-09-26. Notebook de trabajo: `eigenvalues_method.nb`.
Datos calculados fuera del notebook: `ecaHist_26_28.wl` (ver sección 6).

## 1. Definiciones usadas

- **gAll**: `OnlyECA[GroupRelations[relations, All, ecaRules]]`. Clases de equivalencia usando las 4 relaciones (inverse, mirror, state, neighbor), agrupando primero y quitando reglas no ECA después. 81 grupos, cubre las 256 ECA. Las agrupaciones por reducción de estados se consideran válidas dentro de la misma clasificación.
- **gAAll**: `OnlyECA[GroupRelations[relations, {"inverse","mirror"}, ecaRules]]`. 88 grupos.
- **gEcahist / gEcahistRot**: reglas agrupadas por histograma de ciclos idéntico (`ecaHist[18]` y `ecaHistRot[22]` en el notebook).
- Las reglas 23, 77, 105, 150, 178 y 232 no aparecen en `relations` (son su propia inversa y su propio espejo); se añaden como grupos de un elemento con el tercer argumento `ecaRules`.
- **Comprobado**: reglas en el mismo grupo inverse/mirror tienen histograma idéntico para todo n de 1 a 25. Por eso basta calcular una regla por cada uno de los 88 grupos de gAAll.

## 2. Las cuatro categorías (CompararClasificaciones)

Se unen en un bloque todos los grupos de A = gAll y B = histograma que comparten reglas (componentes conexas del grafo de traslapes). Cada regla cae en exactamente una categoría y la suma siempre es 256.

| Categoría | Significado |
|---|---|
| Coinciden | 1 grupo de A = 1 grupo de B |
| A junta, B separa | 1 grupo de gAll partido en varios del histograma |
| A separa, B junta | varios grupos de gAll unidos en uno del histograma |
| Cruzados | varios de cada lado, traslape parcial |

Error que había antes: contar los cruzados solo desde gAll daba 252, porque dejaba fuera las reglas 4, 128, 223 y 254 (grupos {4,223} y {128,254} de gAll, contenidos en un grupo del histograma que a su vez cruza otro grupo de gAll).

Con n = 18 (gEcahist): Coinciden 160, A junta/B separa 12, A separa/B junta 56, Cruzados 28.

Bloques cruzados con n = 18:
- gAll {10,12,34,48,68,80,175,187,207,221,243,245} y {4,223} contra histograma {34,48,187,243}, {4,12,68,207,221,223}, {10,80,175,245}.
- gAll {15,51,85} y {170,204,240} contra histograma {15,85,170,240}, {51}, {204}.
- gAll {128,254} y {136,160,192,238,250,252} contra histograma {128,136,192,238,252,254}, {160,250}.

## 3. Conteo por tamaño (gAll contra cada histograma)

Columnas: Coinciden, A junta/B separa, A separa/B junta, Cruzados.

| n | ecaHist C | JS | SJ | X | ecaHistRot C | JS | SJ | X |
|---|---|---|---|---|---|---|---|---|
| 1 | 0 | 0 | 256 | 0 | 0 | 0 | 256 | 0 |
| 2 | 0 | 0 | 0 | 256 | 0 | 0 | 0 | 256 |
| 3 | 11 | 0 | 141 | 104 | 0 | 0 | 256 | 0 |
| 4 | 74 | 0 | 128 | 54 | 12 | 0 | 106 | 138 |
| 5 | 89 | 12 | 127 | 28 | 41 | 0 | 215 | 0 |
| 6 | 132 | 6 | 78 | 40 | 39 | 0 | 99 | 118 |
| 7 | 134 | 0 | 90 | 32 | 69 | 0 | 187 | 0 |
| 8 | 150 | 0 | 68 | 38 | 97 | 0 | 131 | 28 |
| 9 | 157 | 18 | 67 | 14 | 110 | 0 | 146 | 0 |
| 10 | 152 | 6 | 60 | 38 | 117 | 6 | 99 | 34 |
| 11 | 148 | 12 | 72 | 24 | 120 | 0 | 136 | 0 |
| 12 | 158 | 12 | 58 | 28 | 120 | 12 | 114 | 10 |
| 13 | 148 | 12 | 72 | 24 | 138 | 0 | 118 | 0 |
| 14 | 158 | 0 | 60 | 38 | 132 | 6 | 84 | 34 |
| 15 | 157 | 18 | 67 | 14 | 139 | 0 | 117 | 0 |
| 16 | 150 | 0 | 68 | 38 | 124 | 0 | 112 | 20 |
| 17 | 148 | 12 | 72 | 24 | 138 | 0 | 118 | 0 |
| 18 | 160 | 12 | 56 | 28 | 130 | 12 | 90 | 24 |
| 19 | 148 | 12 | 72 | 24 | 138 | 0 | 118 | 0 |
| 20 | 150 | 6 | 62 | 38 | 130 | 6 | 100 | 20 |
| 21 | 157 | 18 | 67 | 14 | 144 | 0 | 112 | 0 |
| 22 | 152 | 6 | 60 | 38 | 132 | 6 | 84 | 34 |
| 23 | 154 | 6 | 72 | 24 | 138 | 0 | 118 | 0 |
| 24 | 158 | 12 | 58 | 28 | 128 | 12 | 106 | 10 |
| 25 | 148 | 12 | 72 | 24 | 138 | 0 | 118 | 0 |
| 26 | 152 | 6 | 60 | 38 | — | — | — | — |
| 27 | 157 | 18 | 67 | 14 | — | — | — | — |
| 28 | 156 | 0 | 62 | 38 | — | — | — | — |

n = 26, 27 y 28 se calcularon en GPU solo para ecaHist (sin rotaciones). Número de grupos por histograma: 72 (n=26), 71 (n=27), 70 (n=28).

## 4. Periodicidad: la partición depende de gcd(n, 12)

> Ver la corrección de la sección 12: quitando las 16 reglas lineales y afines, la partición de las no lineales depende solo de gcd(n, 6). El gcd(n, 12) de esta sección describe la partición completa quitando solo 6 lineales.

Particiones exactamente iguales (mismos grupos, no solo mismos conteos), ecaHist, n de 1 a 28:
- 9, 15, 21, 27
- 11, 13, 17, 19, 25
- 8, 16
- 10, 22, 26
- 12, 24

Quitando las 6 reglas 60, 90, 102, 153, 165, 195, las familias quedan exactamente por gcd(n, 12):

| gcd(n,12) | Tamaños con la misma partición |
|---|---|
| 1 | 11, 13, 17, 19, 23, 25 |
| 2 | 10, 14, 22, 26 |
| 3 | 9, 15, 21, 27 |
| 4 | 8, 16, 20, 28 |
| 6 | 18 |
| 12 | 12, 24 |

n de 1 a 7 quedan cada uno aparte; el patrón empieza hacia n = 8.
Predicciones de 26 y 28 comprobadas: se cumplen.
Interpretación: las reglas no lineales terminan en patrones de periodo espacial corto (1, 2, 3, 4, con desplazamientos); un patrón de periodo p solo cabe si p divide a n.
Nota: la versión con rotaciones (ecaHistRot) no sigue exactamente este patrón (por ejemplo 15 y 21 difieren en los conteos). No se analizó.

## 5. Reglas lineales

Lineales: 0, 60, 90, 102, 150, 170, 204, 240. Afines (complementos): 255, 195, 165, 153, 105, 85, 51, 15.
- 60 = l XOR c, 102 = c XOR r, 90 = l XOR r, 150 = l XOR c XOR r. 195, 153, 165, 105 son sus complementos.
- Cada paso equivale a multiplicar por un polinomio en GF(2)[x]/(x^n + 1): 60 multiplica por 1 + x, 90 por x^-1 (1 + x)^2. Sus ciclos dependen de la potencia de 2 que divide a n y del orden de 2 módulo la parte impar de n (Martin, Odlyzko y Wolfram, 1984).

Comportamiento de las lineales en la partición (n de 8 a 28, 7 configuraciones distintas):
- {15,85} y {170,240} se juntan si n es par (periodo 2).
- {105} y {150} se juntan si 4 divide a n (periodo 4).
- Si n es potencia de 2 (8, 16), 60/90 y familia se juntan con la regla 0 (todo termina en cero).
- **60 y 90 se juntan si y solo si** n es potencia de 2 o el orden de 2 módulo la parte impar de n es impar. Verificado para n de 3 a 28: se juntan en 4, 7, 8, 14, 16, 23, 28 (parte impar 7 → orden 3; 23 → orden 11). No se juntan con 11, 13, 9, 27, etc. (órdenes pares). Explicación algebraica propuesta, no demostrada: con orden impar ninguna potencia de 2 es congruente con −1, y el cuadrado se deshace sin invertir la dirección.
- Esta condición NO es periódica en n (primos con orden impar: 7, 23, 31, 47, 71, 73...). Por eso no existe un mínimo común múltiplo con x^n + 1.

**Límite teórico**: para n ≥ 8 la clasificación queda determinada por (gcd(n,12), paridad del orden de 2 en la parte impar, n potencia de 2). Máximo del orden de 13 clasificaciones distintas. Finito, pero no periódico.
Pendiente de demostrar: que las no lineales dependen solo de gcd(n,12) (observado hasta 28). Las caóticas (30, 45, 106...) no rompen el patrón porque siempre quedan solas en su grupo.
Prueba con n = 31 hecha: ver sección 9.

## 6. Datos calculados y cómo recuperarlos

`ecaHist_26_28.wl` contiene `<|26 -> ..., 27 -> ..., 28 -> ...|>`, cada uno una lista de 256 histogramas en el mismo formato que `ecaHist[n]` (posición r+1 = regla r).

```wl
extra = Get["/home/rediax/Documents/Projects/CA-Similarity-Index/ecaHist_26_28.wl"];
ecaHist = Join[ecaHist, extra];   (* ecaHist pasa a tener claves 1..28 *)
```

Cómo se generaron (una regla por grupo inverse/mirror, con la GPU):

```wl
repDe = Association[Flatten[Thread[# -> First[#]] & /@ gruposInvMirECA]];
hN = AssociationMap[CycleHistogram[#, n] &, gruposInvMirECA[[All, 1]]];
ecaHistN = Table[hN[repDe[Subscript[r, 2, 3]]], {r, 0, 255}];
```

Tiempos en la RTX 5080 (88 reglas): n = 26 unos 2 min, 27 unos 4 min, 28 unos 8 min.

## 7. Límites de memoria (31 GB RAM, unos 25 libres; GPU 16 GB)

| n | Sin cocientar (12 B/config) | Con rotaciones (16 B/config) |
|---|---|---|
| 29 | 6 GB | 8 GB |
| 30 | 12 GB | 16 GB (justo) |
| 31 | 24 GB (probablemente no cabe) | 32 GB (no cabe) |
| 32 | desborde de índices Int32 | desborde |

- La versión con rotaciones no ahorra memoria: calcula arreglos completos de 2^n (sucesores y representantes) y solo ahorra recorrido.
- Con 31 células los índices caben (máximo 2^31 − 1); el límite es la memoria.
- Para n = 31: cambiar el arreglo de marcas `stamp` de `cycleHistogramC` a enteros de 32 bits deja unos 16 GB. Estimado unos 45 s por regla, alrededor de 1 hora para las 88.
- Más allá de 31: rediseñar la versión con rotaciones para enumerar solo representantes (unos 2^n / n) con una tabla de búsqueda.

## 8. Cambios hechos en eigenvalues_method.nb

- Sección «Agrupación de relaciones»: `GroupRelations`, `OnlyECA`, `ecaRules` y ejemplos.
- Celda de gAll y gAAll cambiada a `OnlyECA[gruposTodos]` y `OnlyECA[gruposInvMir]`.
- Subsección «Comparación de gAll contra los histogramas de ciclos»: `CompararClasificaciones`, `GruposHist`, `ConteoCategorias`, `PlotCategorias` y las dos gráficas (ecaHist y ecaHistRot contra gAll) con sus salidas guardadas.
- No agregados al notebook: `PlotRulesGraph` (grafos de transición para i células), `Descomponer`, los datos de n = 26 a 28 y el análisis de periodicidad.

## 9. Actualización 2026-09-27: n = 31

### Cambios en las funciones (ya aplicados en eigenvalues_method.nb, celdas de inicialización)

- `cycleHistogramC`: el arreglo de marcas `stamp` ahora es de 32 bits (`CArray` Integer32). El recorrido s se marca con s − 2^31 − 1 (rango [−2^31, −1]) porque s llega a 2^31, que no cabe como positivo. Las longitudes de ciclo se cuentan directo en un histograma (arreglo para longitudes ≤ 2^20 y lista aparte para las mayores) en vez de guardar una entrada por ciclo; antes la regla 204 generaba 2^n entradas.
- `SuccessorArrayGPU`: los ceros iniciales se arman con `ZerosInt32` (duplicando con `Join`) en vez de `ConstantArray` de 64 bits, y se descarga con `NumericArray[out]` sin tipo. `NumericArray[out, "Integer32"]` usaba 4 veces el tamaño del arreglo.
- `CycleHistogram` conserva la misma interfaz y formato de salida. Verificado idéntico a `ecaHist`/`ecaHistRot` guardados (varias reglas y tamaños, incluida la rama de ciclos > 2^20 con arreglos artificiales).
- Memoria medida con n = 31: pico de 19 GB en el kernel, unos 10 GB libres. Antes del cambio en la descarga el pico era 24 GB con 5 GB libres.
- Tiempo: 88 reglas en unos 65 min (18 a 60 s por regla). Respaldo del notebook previo en el scratchpad de la sesión.

### Datos

- `ecaHist_31.wl`: `<|31 -> lista de 256 histogramas|>`.
- `ecaHist_31_parcial.wl`: resultados crudos por regla representativa (`regla -> histograma`), escritos uno por uno durante el cálculo.

### Resultados con n = 31 (gcd(n,12) = 1, orden de 2 módulo 31 = 5, impar)

- **Predicción cumplida**: 60 y 90 se juntan (grupo {60, 90, 102, 153, 165, 195}).
- **Parte no lineal**: quitando las 16 lineales/afines, la partición de 31 es idéntica a la de 11, 13, 17, 19, 23 y 25. El patrón gcd(n,12) se mantiene.
- **Hallazgo nuevo**: {15, 85, 105} y {150, 170, 240} se juntan, es decir la regla 150 (l XOR c XOR r) tiene el mismo histograma que el desplazamiento 170: {{1, 2}, {31, 69273666}}. De n = 3 a 25 esto solo ocurre en n = 7. 7 y 31 son primos de Mersenne (2^3 − 1 y 2^5 − 1). Explicación en la sección 10.
- Grupos por histograma: 65. Conteo contra gAll: Coinciden 152, A junta/B separa 0, A separa/B junta 72, Cruzados 32 (con 23: 154, 6, 72, 24).
- Ejemplos: regla 90 {{1, 1}, {31, 34636833}}; regla 30 tiene 10 ciclos, el mayor de longitud 2841150.

## 10. Explicación e interpretación

### Cómo presentar el resultado

- **Resultado principal (limpio)**: las 240 reglas no lineales y no afines se agrupan igual según gcd(n, 6) (ver corrección en la sección 12; antes decía gcd(n, 12)). Verificado de n = 8 a 28 y en n = 31. Explicación intuitiva: las reglas terminan en patrones de periodo espacial corto (1, 2, 3, 4, con desplazamientos) y un patrón de periodo p solo cabe en un anillo de n células si p divide a n.
- **La complicación está aislada**: toda la irregularidad viene de las 16 reglas lineales y afines, cuyos ciclos dependen de la aritmética de n. Esto ya está estudiado (Martin, Odlyzko y Wolfram, 1984). En el reporte basta decir que se salen del patrón y citarlo.
- Propuesta de redacción: el patrón gcd(n, 12) como hallazgo principal, y las lineales en una nota aparte con 60 ~ 90 y 150 ~ 170 como ejemplos de condiciones de teoría de números.

### Por qué 150 y 170 coinciden con n = 7 y n = 31

1. Con n células, las reglas lineales equivalen a multiplicar polinomios módulo x^n + 1 (coeficientes binarios).
2. Si n es un primo de Mersenne, n = 2^k − 1, ese anillo se descompone en una copia de {0, 1} y varias copias del campo de 2^k elementos.
3. En ese campo los elementos distintos de cero forman un grupo con n elementos, y n es primo. Por lo tanto todo elemento distinto de 0 y 1 tiene orden exactamente n.
4. El desplazamiento 170 multiplica por x; la regla 150 multiplica por x^-1 + 1 + x. En cada copia del campo, ambos factores son distintos de 0 y de 1, así que ambos tienen orden n. En la copia de {0, 1} (x = 1) los dos valen 1.
5. La longitud del ciclo de un estado es el mínimo común múltiplo de los órdenes en las componentes donde el estado no es cero. Entonces las dos reglas dan ciclos de longitud n en exactamente las mismas configuraciones, y solo los 2 estados constantes quedan fijos. Sus histogramas son idénticos.

Condición para que 150 no sea 0 en el campo: x^-1 + 1 + x = 0 equivale a x^2 + x + 1 = 0, que solo tiene raíces en campos con k par. Los primos de Mersenne mayores que 3 tienen k primo e impar, así que se cumple. Con n = 3 = 2^2 − 1 (k = 2) falla, lo que explica que n = 3 no coincida y n = 7 y 31 sí.

Estado: argumento que explica los datos, no revisado formalmente. Conviene escribirlo con calma antes de usarlo en el reporte. No se ha verificado si la coincidencia también ocurre para algún n que no sea primo de Mersenne más allá de 31 (con los datos de 3 a 28 y de 31, solo ocurre en 7 y 31; 29 y 30 no se calcularon).

## 11. Mismo análisis para 2 estados y 2 vecinos (16 reglas)

### Cálculo

- Convención de `CellularAutomaton` con radio 1/2: la vecindad de la celda i es (i − 1, i). Con a = celda izquierda y b = celda propia: 12 = a (desplazamiento), 10 = b (identidad), 6 = a XOR b, 9 = NOT (a XOR b), 3 = NOT a, 5 = NOT b, 8 = AND, 14 = OR, 1 = NOR, 7 = NAND, 2 = NOT a AND b, 4 = a AND NOT b, 11 = NOT a OR b, 13 = a OR NOT b.
- El kernel de la GPU original solo acepta vecindades simétricas (−r..r). Se escribió `succKernelLH`, con vecindad de i + lo a i + hi (m impar: −r..r; m = 2: −1..0), y `SuccessorArrayGPULH`. Verificado contra el cálculo en CPU con `CaArray` para las 16 reglas y contra `ecaHist`/`ecaHistRot` con reglas de 3 vecinos. Solo existe en la sesión; no se agregó al notebook.
- Costo con 31 células: el mismo por regla que en ECA (2^31 configuraciones), pero solo 16 reglas. Sin cocientar de 1 a 31 células; con rotaciones de 1 a 29 (`cycleHistogramRotC` aún usa marcas de 64 bits). Unos 30 min en total.
- Datos: `hist22.wl` = `<|"raw" -> <|n -> 16 histogramas|>, "rot" -> ...|>` (posición r + 1 = regla r). Crudos: `hist22_parcial.wl`. Gráficas: `plot22_raw.png`, `plot22_rot.png`.

### gAll en este espacio

`gAll22` = grupos de `GroupRelations[relations, All, ...]` filtrados a reglas de 2 estados y 2 vecinos (después de agrupar): {2, 4, 11, 13}, {0, 15}, {1, 7}, {3, 5}, {6, 9}, {8, 14}, {10, 12}. Es igual al agrupamiento solo con inverse y mirror: state y neighbor conectan estas reglas con otros espacios, no entre sí.

### Diferencia importante con ECA: mirror no conserva el histograma

En ECA, reglas relacionadas por inverse o mirror siempre tienen histograma idéntico. Con 2 vecinos no: los grupos {10, 12}, {3, 5} y {2, 4, 11, 13} tienen histogramas distintos en todo n ≥ 2. Razón: la vecindad (i − 1, i) no es simétrica, así que el espejo de una regla usa la vecindad (i, i + 1). Expresado en la misma convención, el espejo equivale a la regla original compuesta con un desplazamiento. Ejemplo: 10 (identidad) y 12 (desplazamiento) son espejo una de la otra; con 31 células, 10 tiene 2^31 puntos fijos y 12 tiene ciclos de longitud 31. Con rotaciones, el desplazamiento desaparece y los histogramas vuelven a coincidir en todos los grupos.

### Periodicidad: mucho más simple que en ECA

Sin cocientar, solo hay 5 particiones distintas de 1 a 31 células:

| Familia | Tamaños | Partición |
|---|---|---|
| n impar ≥ 5 | 5, 7, ..., 31 | {3}, {5}, {10}, {12}, {0, 15}, {1, 7}, {2, 11}, {4, 13}, {6, 9}, {8, 14} |
| n par, no potencia de 2 | 6, 10, 12, 14, 18, ..., 30 | {5}, {10}, {0, 15}, {1, 7}, {2, 11}, {3, 12}, {4, 13}, {6, 9}, {8, 14} |
| potencias de 2 | 2, 4, 8, 16 | {5}, {10}, {1, 7}, {2, 11}, {3, 12}, {4, 13}, {8, 14}, {0, 6, 9, 15} |
| n = 1, n = 3 | cada uno aparte | |

Con rotaciones, de 5 a 29 células todos los tamaños que no son potencia de 2 dan la misma partición, y **esa partición es exactamente gAll22**. En potencias de 2 (4, 8, 16) se junta además {0, 15} con {6, 9}.

- El análogo de gcd(n, 12) aquí es la paridad de n: los patrones de periodo corto que importan son de periodo 1 y 2.
- Con n par se juntan 3 (NOT a) y 12 (a): el complemento de un desplazamiento tiene la misma estructura de ciclos que el desplazamiento cuando n es par.
- Reglas lineales/afines: 0, 6, 10, 12 y 15, 9, 5, 3. La única lineal no trivial es 6 = a XOR b, el análogo de la regla 60 de ECA (mismo histograma: con 31 células {{1, 1}, {31, 34636833}}). Solo se junta con 0 en potencias de 2, donde se vuelve nilpotente (todo termina en cero). Como no hay otra lineal no trivial con la cual compararla, desaparecen las condiciones no periódicas de ECA (60 ~ 90, 150 ~ 170). Queda solo la excepción de las potencias de 2.

### Conteo contra gAll22 (16 reglas)

| n | Sin cocientar: C, JS, SJ, X | Con rotaciones: C, JS, SJ, X |
|---|---|---|
| 1 | 0, 0, 16, 0 | 0, 0, 16, 0 |
| 2 | 4, 4, 4, 4 | 6, 0, 10, 0 |
| 3 | 6, 4, 0, 6 | 8, 0, 8, 0 |
| potencia de 2 (4, 8, 16) | 4, 4, 4, 4 | 12, 0, 4, 0 |
| impar ≥ 5 | 8, 8, 0, 0 | 16, 0, 0, 0 |
| par no potencia de 2 | 8, 4, 0, 4 | 16, 0, 0, 0 |

C = Coinciden, JS = gAll junta / histograma separa, SJ = gAll separa / histograma junta, X = Cruzados. Con 30 y 31 células solo sin cocientar (8, 4, 0, 4 y 8, 8, 0, 0).

- Sin cocientar, nunca hay coincidencia total: la mitad de las reglas coincide y la otra mitad la separa el histograma (los pares espejo que difieren por un desplazamiento).
- Con rotaciones, desde 5 células la coincidencia es total (16 de 16) salvo en potencias de 2. En este espacio, el histograma con rotaciones reproduce exactamente la clasificación por relaciones.

## 12. Corrección: las no lineales dependen de gcd(n, 6), no de gcd(n, 12)

La tabla de la sección 4 se hizo quitando solo las 6 reglas 60, 90, 102, 153, 165, 195. Quitando las 16 lineales y afines (0, 60, 90, 102, 150, 170, 204, 240, 255, 195, 165, 153, 105, 85, 51, 15), las familias con partición idéntica (n = 1 a 28 y 31) son:

| gcd(n, 6) | Tamaños con la misma partición |
|---|---|
| 1 | 11, 13, 17, 19, 23, 25, 31 |
| 2 | 8, 10, 14, 16, 20, 22, 26, 28 |
| 3 | 9, 15, 21, 27 |
| 6 | 12, 18, 24 |

- n de 1 a 7 quedan cada uno aparte.
- La separación entre gcd(n, 12) = 2 y 4 (y entre 6 y 12) venía de reglas lineales: {105} y {150} se juntan cuando 4 divide a n, y {15, 85} con {170, 240} cuando n es par.
- Interpretación: para las 240 reglas no lineales solo importan los patrones de periodo espacial 2 y 3.
- La afirmación de las secciones 9 y 10 sobre n = 31 sigue siendo cierta: sin las 16 lineales, 31 es igual a 11, 13, 17, 19, 23 y 25.
- Comparación con el espacio de 2 vecinos (sección 11): allí solo importa la paridad de n (periodo 2), más la excepción de las potencias de 2 que viene de la regla lineal 6.

## 13. Cómo retomar

- `eigenvalues_method.nb` tiene una sección nueva al final, «Periodicidad en el tamaño del espacio», con la carga de datos (`ecaHist` pasa a tener 1 a 28 y 31; `hist22.wl`), las funciones auxiliares (`PlotRulesGraph`, `repDe`, `ParticionECA`, `ParticionSinLineales`, `FamiliasSinLineales`), el kernel `succKernelLH` para vecindades no simétricas, el análisis del espacio de 2 vecinos y sus dos gráficas con las salidas guardadas.
- Orden: evaluar las celdas de inicialización (sesión CUDA), la sección «Ejecución» (carga `ecaHist`/`ecaHistRot`), «Agrupación de relaciones» y después la sección nueva.
- Archivos de datos en la carpeta del proyecto: `ecaHist_26_28.wl`, `ecaHist_31.wl`, `ecaHist_31_parcial.wl`, `hist22.wl`, `hist22_parcial.wl`. Gráficas: `plot22_raw.png`, `plot22_rot.png`.
- Nada de esto está en git todavía.
