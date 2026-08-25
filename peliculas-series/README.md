# Tema 1 - Peliculas / Series

Dos sistemas de IA para el mismo dominio: recomendar que ver.

## Punto 1 - Sistema experto (`sistema_experto_clips.ipynb`)

- **Libreria:** `clipspy` (motor CLIPS embebido en Python).
- **Hechos de entrada:** `genero`, `compania`, `tiempo` (disponible) y `animo`.
- **Motor de inferencia:** se cargan las reglas con `env.build(...)`, se insertan los hechos con `env.assert_string(...)` y se dispara el encadenamiento con `env.run()`. El resultado se lee de nuevo como hechos (`recomendacion`, `sugerencia_genero`, `advertencia`, `formato`).
- **Reglas (15 en total, minimo pedido = 5):**
  1. `filtro_familiar` - si la compania es la familia, filtra contenido apto para todos.
  2. `advertencia_terror_familia` - si ya se filtro para familia y el genero es terror, advierte.
  3. `sugerencia_animo_estresado` - si el animo es estresado, sugiere comedia.
  4. `formato_poco_tiempo` - poco tiempo disponible -> recomienda pelicula, no serie.
  5. `formato_maraton` - mucho tiempo + animo relajado -> recomienda maraton de serie.
  6. `recomendar_terror_solo` - terror + solo -> titulo concreto.
  7. `recomendar_accion_amigos` - accion + amigos -> titulo concreto.
  8. `recomendar_comedia_pareja` - comedia + pareja -> titulo concreto.
  9. `recomendar_scifi_aventurero` - ciencia ficcion + animo aventurero -> titulo concreto.
  10. `recomendar_drama_solo` - drama + solo -> titulo concreto.
  11. `recomendar_terror_general` - terror -> recomendacion general si no existe una recomendacion especifica.
  12. `recomendar_accion_general` - accion -> recomendacion general si no existe una recomendacion especifica.
  13. `recomendar_comedia_general` - comedia -> recomendacion general si no existe una recomendacion especifica.
  14. `recomendar_drama_general` - drama -> recomendacion general si no existe una recomendacion especifica.
  15. `recomendar_scifi_general` - ciencia ficcion -> recomendacion general si no existe una recomendacion especifica.
- **GUI:** `ipywidgets` dentro del notebook (funciona en Google Colab o Jupyter). Un `Dropdown` por variable de entrada, un boton "Obtener recomendacion" que ejecuta el motor (`env.reset()` -> `env.assert_string(...)` -> `env.run()`) y un `Output` con los hechos intermedios, las advertencias y la recomendacion final.

Abrir en Google Colab o Jupyter y ejecutar las celdas en orden: `sistema_experto_clips.ipynb`
(la instalacion de `clipspy` esta en la primera celda; la interfaz grafica aparece al ejecutar la ultima celda).

## Punto 2 - Sistema difuso (`sistema_difuso_fuzzy.ipynb`)

- **Libreria:** `scikit-fuzzy` (`skfuzzy` + `skfuzzy.control`).
- **Antecedentes (entradas difusas):**
  - `duracion` (0-240 min): corta / media / larga.
  - `tiempo_disponible` (0-300 min): poco / medio / mucho.
  - `interes_genero` (0-10): bajo / medio / alto.
- **Consecuente (salida difusa):** `compatibilidad` (0-100): baja / media / alta.
  Responde la pregunta: *"que tan compatible es esta pelicula/serie con la persona en este momento"*.
- **Reglas difusas (7, minimo pedido = 5):** combinan interes por el genero, duracion del contenido y tiempo libre (ver el arreglo `reglas` en el codigo). Ejemplos:
  - Si `interes_genero` es alto -> `compatibilidad` alta.
  - Si `duracion` es larga y `tiempo_disponible` es poco -> `compatibilidad` baja.
  - Si `duracion` es corta y `tiempo_disponible` es mucho -> `compatibilidad` alta.
- **Metodo de defuzzificacion:** el centroide por defecto de `skfuzzy.control`.
- **GUI:** `ipywidgets` dentro del notebook. Un `FloatSlider` por variable de entrada, un boton "Calcular" y un `Output` que imprime el resultado + categoria y dibuja `consecuente.view(sim=sim)` (el mismo metodo de visualizacion usado en el ejercicio del mesero visto en clase).

Abrir en Google Colab o Jupyter y ejecutar las celdas en orden: `sistema_difuso_fuzzy.ipynb`
(la instalacion de `scikit-fuzzy` esta en la primera celda; la interfaz grafica aparece al ejecutar la ultima celda).

## Como se sustenta

- El sistema experto demuestra encadenamiento de reglas (una regla puede activar hechos que disparan otras reglas, como `filtro_familiar` -> `advertencia_terror_familia`), igual que se vio en clase con el ejemplo del medico/vesicula.
- El sistema difuso demuestra fuzzificacion (conjuntos trapezoidales y triangulares), inferencia con reglas Mamdani (`&` = AND difuso, min/max) y defuzzificacion (centroide), igual que el ejercicio del mesero visto en clase pero aplicado a un dominio distinto y con una variable de salida interpretada como "score de compatibilidad".