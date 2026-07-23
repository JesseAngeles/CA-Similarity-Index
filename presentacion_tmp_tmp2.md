# Similitud entre autómatas celulares elementales — resultados de tmp.nb y tmp2.nb

Documento de apoyo para la presentación (preparado 2026-07-13).

---

## 1. Explicación sin contexto (el "elevator pitch")

**El problema.** Un autómata celular elemental es una regla que toma una fila de
celdas 0/1 y produce la fila siguiente mirando cada celda y sus dos vecinas. Hay
256 reglas posibles (las $r_{2,3}$: 2 estados, vecindad de 3). La pregunta del
proyecto es: **¿cuándo dos reglas distintas son "la misma" en el fondo, y cuándo
son solo parecidas?** Y si son parecidas, ¿podemos medir *qué tanto*?

**La idea.** En vez de mirar los dibujos que produce cada regla (que es subjetivo),
miramos su **grafo de transición**: para un anillo de $n$ celdas hay $2^n$
configuraciones posibles, y la regla manda cada configuración a exactamente una
siguiente. Eso es una función $f:\{1,\dots,2^n\}\to\{1,\dots,2^n\}$, es decir, un
**grafo funcional**: un dibujo de "quién va a dónde" que captura *toda* la
dinámica de la regla en ese tamaño, sin importar cómo se ven sus patrones.

**Qué hicimos.**

1. *Equivalencia exacta (lo discreto).* Dos reglas son equivalentes si sus grafos
   de transición son **isomorfos** (el mismo grafo salvo renombrar los nodos).
   Primero lo hicimos con `IsomorphicGraphQ` de Mathematica (tmp.nb), que se
   vuelve lento; en tmp2.nb lo reemplazamos por una **forma canónica** propia
   (`CanonFuncGraph`): a cada grafo funcional le calculamos una "huella digital"
   única, y agrupar por huella es instantáneo. Con eso clasificamos las 256
   reglas en anillo de tamaño 13 (8192 configuraciones cada una).

2. *Contraste con la teoría algebraica.* Del trabajo previo (report.nb) ya
   teníamos relaciones de herencia entre reglas (inversa, espejo, etc.) que
   predicen qué reglas deben comportarse igual. Comparamos esa partición
   algebraica contra la partición topológica de los grafos: la topológica es más
   gruesa — hay reglas que el álgebra no relaciona pero cuyos grafos de
   transición son isomorfos (p. ej. {15, 85, 170, 240} colapsan en una sola
   clase). Es decir, **la dinámica revela equivalencias que la herencia
   algebraica no captura**.

3. *Similitud continua (lo gradual).* Para reglas que NO son equivalentes,
   definimos una medida de similitud en [0,1]. A cada grafo funcional le
   extraemos su **perfil período/transitorio**: para cada configuración, cuánto
   tarda en caer a un ciclo (transitorio) y de qué longitud es ese ciclo
   (período). Dos reglas son similares si sus perfiles, ordenados, se parecen,
   promediando sobre varios tamaños de anillo. La similitud final es
   $\exp(-\lambda \cdot D)$ con $\lambda = 5$.

**En una frase:** convertimos "¿estas dos reglas se comportan igual?" en una
pregunta sobre grafos, la resolvemos exactamente con una forma canónica, y para
los casos no idénticos damos un número de qué tan parecidas son.

---

## 2. Resultados que puedes mostrar

- **Clasificación exacta**: las 256 reglas $r_{2,3}$ agrupadas en clases de
  isomorfismo de su grafo de transición (`gr23s13`, anillo 13).
- **Refinamiento**: la partición algebraica (inverse/mirror, 88 componentes de
  `wkr23`) refina la topológica; verificado con `RefinaQ` en tmp.nb.
- **Fusiones**: clases algebraicas distintas que la topología une — el ejemplo
  {15, 85, 170, 240} (shift izquierdo/derecho y sus negaciones) y el caso de la
  celda "fusions" en tmp2 que une una clase de 2 con una de 4.
- **Matriz de similitud** continua entre representantes de clase
  (`Dtotal` → `Similitud`), visualizable con `MatrixPlot`.
- **Escalabilidad**: `CanonFuncGraph` clasifica en anillo 13 (8192 nodos por
  grafo) donde `IsomorphicGraphQ` ya era impráctico.

---

## 3. Fixes aplicados hoy (2026-07-13) — hay que re-evaluar antes de presentar

Se modificaron `tmp.nb` y `tmp2.nb` (backups en `tmp.nb.bak`, `tmp2.nb.bak`):

1. **tmp.nb — verificación firma espectral vs. isomorfismo.** La celda decía
   `gbf == gbf` (compara una variable consigo misma: siempre `True`, la
   verificación nunca corrió). Ahora compara las particiones reales:
   `Sort[Sort/@gbg[[;;,2]]] == Sort[Sort/@Values[gbf]]`.
   ⚠️ El output `True` que está guardado debajo es del código viejo: **no es
   evidencia**. Hay que re-evaluar.

2. **tmp2.nb — escala de los transitorios en `PerfilPT`.** El perfil usaba
   `Log2[per]` pero `trans` crudo. El transitorio crece como $2^n$ y el
   log-período como $n$, así que en anillos grandes los transitorios dominaban
   toda la distancia. Ahora el perfil devuelve `Log2[trans+1]`, simétrico con
   los períodos. **Toda matriz `Dtotal`/`Similitud` calculada antes cambia y
   debe recalcularse.**

3. **tmp2.nb — colisión de nombres.** La celda `r23 = gr23s13` sobrescribía la
   lista de reglas con la lista de clases; el pipeline final iteraba `{r, r23}`
   esperando reglas y solo funcionaba en cierto orden de evaluación. Ahora es
   `clases23 = gr23s13` y `clases = clases23`. El notebook se puede evaluar de
   arriba a abajo.

4. **tmp2.nb — limpieza.** Se eliminó la celda suelta `PerfilPT[]` del final.

---

## 4. Qué hay que probar (checklist antes de la presentación)

- [ ] Abrir `tmp2.nb` en Mathematica y **evaluar de arriba a abajo** sin errores
      (verifica el fix #3). Si el front end se queja del caché del notebook,
      aceptar la reparación — se editó como texto.
- [ ] Re-evaluar en `tmp.nb` la celda de `gbg`/`gbf` (espacio 7) y ver si da
      `True`: ¿la firma espectral separa exactamente igual que el isomorfismo
      en las 256 reglas? Si da `False`, es un hallazgo (grafos coespectrales no
      isomorfos) y también vale la pena mencionarlo.
- [ ] Recalcular `perfiles`, `Dtotal` y `Similitud` con el `PerfilPT` corregido
      y regenerar el `MatrixPlot` para las láminas.
- [ ] **Estabilidad de las clases**: verificar que `gr23s13` (anillo 13) no
      cambia al cruzarlo con otro tamaño, p. ej. anillo 11 o 12:
      `GroupBy` por `{CanonFuncGraph[TransMap[#,13]], CanonFuncGraph[TransMap[#,11]]}`
      y comparar número de clases. Isomorfismo en un solo tamaño no garantiza
      equivalencia en todos; 13 es primo (bien elegido), pero hay que
      documentar la verificación.
- [ ] **Sanity check de `PerfilPT`** contra casos conocidos: regla 0 (todo cae a
      0: períodos 1, transitorio ≤ 1), regla 204 (identidad: todo período 1,
      transitorio 0), regla 170 (shift: ciclos de longitud divisor de n).
- [ ] **Sanity check de `CanonFuncGraph`**: pares que deben coincidir (170 vs
      240, espejo; 15 vs 85) y pares que no (30 vs 110).
- [ ] Probar sensibilidad de λ (=5) en `Similitud`: mostrar que el *ranking* de
      similitud no depende de λ, solo la escala.

## 5. Tareas pendientes (después de la presentación)

- [ ] Integrar a `report.nb` la sección nueva: `TransMap` + `CanonFuncGraph` +
      clasificación `gr23s13`, el resultado de fusiones vs. partición
      algebraica, y la matriz de similitud. Con eso `tmp.nb` queda obsoleto
      (su enfoque `IsomorphicGraphQ`/`FirmaEspectral` fue superado).
- [ ] Decidir el rol de las firmas espectrales: con la forma canónica exacta,
      relegarlas solo a la parte continua o eliminarlas.
- [ ] Justificar (o reemplazar) la normalización `/nn` en `DistanciaPT` — sigue
      siendo heurística.
- [ ] Extender la clasificación a más tamaños de anillo y estudiar si las
      clases se refinan al crecer n (¿converge la partición?).
- [ ] Limpiar typos de la narrativa en `report.nb` ("derechoa", "l oque",
      "sol oqeu") antes de compartirlo.
- [ ] Borrar `tmp.nb.bak` / `tmp2.nb.bak` cuando se confirme que todo evalúa, y
      hacer commit (hay cambios sin commitear desde "1D").
