# CA-Similarity-Index

Investigación para definir un índice de similitud entre reglas de autómatas celulares basado en su evolución. Proyecto de la Indian Summer School on Cellular Automata 2026 (mentor: Sukanta Das). Reporte final en `Paper/` (LNCS, máximo 12 páginas, entrega 2026/10/09).

## Métodos

1. Relaciones: inverse (permutación de estados), mirror, reducción de estados, reducción de vecindad. `gAll` = clases con las cuatro (81 grupos en ECA); `gAAll` = solo inverse y mirror (88 grupos).
2. Grafo de transición con n células: isomorfismo / forma canónica (binario).
3. Eigenvalores de la matriz de adyacencia + distancia coseno (solo mismo espacio).
4. Momentos espectrales: fracción de configuraciones con F^k(x) = x para k = 1..K. Compara entre dominios.

## Estado

- Resultados de periodicidad de histogramas de ciclos: `resultados_periodicidad_histogramas.md`.
- Pendientes y la idea de momentos espectrales con masa de atractor (para separar reglas 0 y 8 de ECA): `TODO.md`.
- Mathematica (y la GPU para los cálculos grandes) solo está en la PC principal; en la computadora Windows del trabajo no se pueden correr los notebooks.
