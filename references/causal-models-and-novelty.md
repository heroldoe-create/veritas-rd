# Auditoría de explicaciones causales sobre modelos y novedad

Carga esta referencia solo cuando la tarea examine si los objetivos de entrenamiento, la decodificación o la distribución de datos afectan la producción o evaluación de ideas novedosas.

## Procedimiento

1. Formula por separado cada eslabón causal propuesto: objetivo de entrenamiento, distribución aprendida, política de decodificación, definición de novedad y resultado observado.
2. Busca en el corpus evidencia para cada eslabón y para el mecanismo que los conectaría. No sustituyas un mecanismo demostrado por una analogía matemática o verbal.
3. Distingue predicción de tokens, objetivo de entrenamiento, estrategia de generación y juicio humano de novedad. No los trates como una sola operación.
4. Evalúa si “novedad” se mide por rareza estadística, distancia respecto del corpus, combinación de ideas, evaluación experta u otro criterio. No asumas equivalencia entre esas definiciones.
5. Si falta evidencia directa del mecanismo, clasifica la conclusión como `insuficiente` con la causa aplicable. Si el usuario presenta la explicación como hipótesis nueva y el corpus es delimitado, añade `por-novedad-potencial`; esto no confirma originalidad ni causalidad.
6. Separa observaciones descriptivas (p. ej., salidas homogéneas) de afirmaciones causales (p. ej., una función objetivo las produce). No infieras la segunda solo a partir de la primera.

## Salida

Presenta el diagrama causal como hipótesis, si ayuda. Para cada flecha, identifica la evidencia directa, la evidencia que la limita y los datos o pruebas que faltan. No conviertas una explicación plausible en un hecho sin citas localizables.
