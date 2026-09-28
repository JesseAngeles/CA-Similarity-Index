# TODO

## Momentos espectrales con masa de atractor (idea del 2026-09-28, pendiente de implementar)

Requiere Mathematica (hacerlo en la PC, en `eigenvalues_method.nb`).

### Problema

Los momentos espectrales (fracción de configuraciones con F^k(x) = x) clasifican la regla 0 y la regla 8 de ECA como idénticas: ambas tienen como único atractor el punto fijo de puros ceros.

No es un error del método sino de cualquier método espectral: el grafo de transición es un grafo funcional (cada configuración tiene un solo sucesor). Su espectro sale solo de los ciclos (un ciclo de longitud p aporta las p raíces p-ésimas de la unidad) y los árboles transitorios solo aportan eigenvalor 0. Por eso `tr(A^k)/N` ve los atractores pero no las cuencas. El método de eigenvalores y el histograma de ciclos tienen la misma ceguera.

- Regla 0: todo llega a cero en 1 paso.
- Regla 8: cada bloque de unos de longitud ≥ 2 deja un 1 en su extremo izquierdo, que muere en el paso siguiente. Altura máxima 2.

### Propuesta

Para cada configuración x: h(x) = altura (pasos para llegar al ciclo) y p(x) = periodo del ciclo al que llega. En el paso k, x cuenta si:

    p(x) divide a k   y   h(x) <= k - p(x)

Es decir, x cuenta a partir de la siguiente activación de su ciclo después de haber llegado. El vector se normaliza por N = |S|^n.

Ejemplo, regla 30 con 5 células (grafo en `r30_5celdas_grafo.png`), 32 configuraciones:
- Punto fijo 0 con cuenca {0, 31}.
- Ciclo de 5 (14 → 25 → 7 → 28 → 19) con 5 cadenas de 5 nodos: cuenca de 30.
- Vector: {1, 2, 2, 2, 7, 2, 2, 2, 2, 32, 2, 2, 2, 2, 32, ...} / 32.
- Si una configuración tardara 7 pasos en llegar al ciclo de 5, entraría hasta k = 15 (tercera activación).

La altura queda agrupada en intervalos del tamaño del periodo: h = 0 entra en k = p; h = 1..p en k = 2p; h = p+1..2p en k = 3p.

### Por qué debería funcionar

- Los nodos con h = 0 entran en la primera activación, igual que en los momentos espectrales actuales; lo nuevo es que los transitorios entran después.
- Separa 0 y 8: con p = 1 los intervalos miden 1 paso. Regla 0: {1/N, 1, 1, ...}; regla 8: {1/N, m, 1, ...} con m < 1.
- Sigue comparando entre dominios: el vector está indexado por pasos y normalizado por N, así que su largo no depende de |S|, |N| ni n. Inverse y mirror siguen dando distancia 0 (grafo isomorfo).
- Es mejor que la alternativa sin retraso (contar x si F^{2k}(x) = F^k(x), es decir, p | k y h <= k): esa mezcla los nodos del ciclo con los transitorios (en el ejemplo daría 32 en k = 5).

### Limitaciones a revisar

- La resolución de la altura depende del periodo: con periodos largos (regla 30 con n grande) h = 1 y h = p caen en el mismo intervalo.
- Ventana K: un ciclo con p > K nunca se activa.
- Reducción de estados: las configuraciones con el estado eliminado agregan transitorios de altura 1 y cambian la normalización. Revisar si los pares de gAll unidos solo por reducción de estados dan distancia 0 (probablemente tampoco la dan con los momentos actuales).
- Coseno sobre vectores que saturan: considerar otras distancias si discrimina poco.

### Implementación

- p(x) ya sale de `cycleHistogramC`. Falta h(x): calcularlo en tiempo lineal sobre el arreglo de sucesores (`SuccessorArrayGPU`), marcando la distancia al ciclo durante el recorrido.
- Con h y p: sumar 1 al vector en k = p·j para toda j >= 1 + ceil(h/p).
- Con cociente por rotaciones también funciona (F conmuta con la rotación, todas las configuraciones de una clase tienen la misma altura), ponderando por el tamaño de la clase.

### Experimento

256 ECA, n = 14, cociente por rotaciones:
1. Número de vectores distintos (con los momentos espectrales solos salieron 63).
2. Verificar que 0 y 8 se separen y que los pares inverse / mirror / vecindad de gAll queden en 0.
3. Silhouette del clustering contra el 0.254 actual.
