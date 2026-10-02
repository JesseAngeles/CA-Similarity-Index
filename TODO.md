# TODO

## Experimentos de la masa de atractor y paper completo en español (hecho el 2026-10-01)

Código en `experimentos_masa_atractor.nb` (se evalúa completo en unos 30 s si existe `cross_domain_am_data.wl`; sin ese archivo recalcula unos 15 min). Resultados en la Sección 6 de `Paper/sections/experiments.tex`; figuras `attractor_mass_size_identification.png` y `attractor_mass_clusters_n12.png` en `exports/` y `Paper/figures/`.

- Relaciones entre dominios (n = 12): permutación y espejo con m impar dan d = 0; espejo con m = 2 y reducción de vecindad con desplazamiento solo dan 0 en el cociente; la reducción de estados nunca da 0 (mediana 0.083 completo, 0.010 cociente).
- Misma regla en distintos tamaños: se identifica como la más cercana en el 25% (completo) y 33% (cociente) de los pares de tamaños.
- d(0, 8) = 0.0072 con n = 12 y K = 60 (0.048 con K = 10).
- Agrupamiento: k-medoides con 4 grupos (punto fijo, periodo n, periodo 2, periodos largos), silueta 0.70.

Pendiente:
- La definición de reducción de estados del paper se cambió para que coincida con `DeleteState` (el estado eliminado es el más alto, nunca se produce y toda vecindad que lo contiene produce 0). La versión de Overleaf decía "a se comporta igual que b". Confirmar cuál se queda; con k = 2 ambas dan las mismas relaciones (solo la regla 0).
- `Paper_en/` no tiene todavía las secciones nuevas (resumen, experimentos, conclusiones) ni las correcciones de esta versión.
- El paper en español tiene 19 páginas (límite LNCS: 12).
- Idea abierta: normalizar la masa de atractor para que sea invariante ante la reducción de estados (los momentos espectrales simples sí lo son).

## Paper: combinar las figuras de completo y cociente (idea del 2026-09-30; ya implementada)

Requiere Mathematica (PC principal, con el MCP de Wolfram).

### Problema

En `Paper/sections/spectral_methods.tex` las figuras `fig:sm-classes` (momentos espectrales) y `fig:am-classes` (masa de atractor) tienen dos subfiguras, una para el grafo completo y otra para el cociente. En la versión de Overleaf cada subfigura está a `\textwidth` y juntas ocupan casi una página. Si se hacen más chicas, ya no se distinguen los valores.

### Qué hacer

Por cada método, generar **una sola gráfica** que junte ambos grafos:

1. Momentos espectrales: `spectral_moments_gpu_data.wl` (completo) + `spectral_moments_rot_gpu_data.wl` (cociente).
2. Masa de atractor: `attractor_mass_gpu_data.wl` (completo) + `attractor_mass_rot_gpu_data.wl` (cociente).

No hay que recalcular nada. Cada archivo es una asociación `n -> <|"n" -> n, "Classes" -> <|10 -> c, 20 -> c, 30 -> c, 60 -> c|>, "Groups" -> ...|>` con n = 1..29, y basta con usar `"Classes"`.

Estilo de cada gráfica:
- Se grafican las 4 ventanas K = 10, 20, 30, 60 con los mismos colores y marcadores que las gráficas actuales (`Paper/figures/spectral_moments_classes_gpu.png`): azul con círculo, naranja con cuadrado, verde con rombo y rojo con triángulo.
- El grafo completo va con **línea continua** y el cociente con **línea punteada**, del mismo color para cada K, como en `Paper/figures/all_methods_8_classes_gpu.png`.
- Se conservan las líneas de referencia 88 (state permutation, mirror) y 81 (4 inheritance), el eje "Space size n" y las mismas marcas del eje y.
- La leyenda tiene que ser compacta y no tapar datos. Sugerencia: poner debajo de la gráfica una fila con los colores de las K y otra con "solid = transition graph, dashed = rotation quotient".
- La gráfica tiene que ser legible a `width=\textwidth` en LNCS (unos 12 cm): fuentes grandes y figura más ancha que alta.
- Se exportan como PNG a `exports/` y se copian a `Paper/figures/` con los nombres `spectral_moments_classes_combined_gpu.png` y `attractor_mass_classes_combined_gpu.png`. Ya existen archivos con esos nombres, pero solo tienen K = 10 y el paper no los usa, así que se pueden sobrescribir.
- El código va en `eigenvalues_method.nb`, junto a las secciones de momentos espectrales y de masa de atractor, siguiendo el estilo de la gráfica de los ocho métodos (`all_methods_8_classes_gpu.png`).

Boceto sin probar:

```wolfram
full = Get["spectral_moments_gpu_data.wl"]; rot = Get["spectral_moments_rot_gpu_data.wl"];
ks = {10, 20, 30, 60};
series[d_, k_] := Table[{n, d[n]["Classes"][k]}, {n, 1, 29}];
cols = ColorData[97] /@ Range[4];
ListLinePlot[
  Join[series[full, #] & /@ ks, series[rot, #] & /@ ks],
  PlotStyle -> Join[cols, Directive[#, Dashed] & /@ cols],
  PlotMarkers -> Automatic, ...]
```

### Cambio en el LaTeX

Sustituir cada figura de dos subfiguras por una sola imagen. La versión que manda es la de Overleaf, que el 2026-09-30 se estaba editando y todavía no estaba en el repo; antes de empezar, verificar que `Paper/` ya esté sincronizado con Overleaf. Hay que conservar los labels que use esa versión: allá la figura de masa de atractor tenía `fig:aclasses` y en el repo tenía `fig:am-classes`. Luego revisar que las `\ref` coincidan. La figura de momentos espectrales tenía `\caption{}` vacío en Overleaf, así que hay que ponerle un caption.

```latex
\begin{figure}
\centering
\includegraphics[width=\textwidth]{spectral_moments_classes_combined_gpu.png}
\caption{Número de vectores de momentos espectrales distintos entre las 88 reglas representantes para $n = 1, \dots, 29$ y $K = 10, 20, 30, 60$, en el grafo completo (línea continua) y en el grafo cociente (línea punteada).}
\label{fig:sm-classes}
\end{figure}
```

La de masa de atractor queda igual, con `attractor_mass_classes_combined_gpu.png` y `\label{fig:am-classes}`. Después hay que compilar y revisar que el paper siga cabiendo en 12 páginas.

## Momentos espectrales con masa de atractor (idea del 2026-09-28; ya implementada en el commit 03a7db8)

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
