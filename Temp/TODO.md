Convención: `[ ]` pendiente · `[x]` hecho. Vamos tachando a medida que avanzamos.

---

## Fase 0 — Decisiones de diseño (hacer PRIMERO, lo definimos nosotros)

| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 0.1 | Elegir el **tipo de problema** | [x] | Clasificación de imágenes de comida (visión por computador) |
| 0.2 | Definir el **dataset** (origen, tamaño, nº de clases) | [x] | **Food-101** (subconjunto de **34 clases** de comidas populares). Fuente nutricional: **USDA FoodData Central** |
| 0.3 | Elegir la **arquitectura del modelo** | [x] | **EfficientNet-B0** preentrenado (transfer learning) |
| 0.4 | Redactar **título del proyecto** | [x] | En `README.md` |
| 0.5 | Redactar **objetivo general** (preciso y medible) | [x] | En `README.md` |
| 0.6 | Redactar **2-3 objetivos específicos** (alineados con la metodología) | [x] | 3 objetivos, en `README.md` |
| 0.7 | Verificar factibilidad en Kaggle (tamaño del dataset vs tiempo de sesión / VRAM) | [x] | Comprobado: corrió en T4 con 34k imágenes en ~37.6 min, sin problemas de VRAM |

### Nota sobre el entorno y la descarga del dataset (importante)

**Entorno definitivo: Kaggle Notebooks** (GPU T4/P100, ~30 h/semana).

Food-101 ya existe como dataset público en Kaggle, así que **no hay que descargarlo por internet**:
- En el notebook de Kaggle: **Add Input → buscar "Food 101"** y añadir el dataset. Queda montado en `/kaggle/input/...` (solo lectura), sin re-descargas entre sesiones.
- Alternativa si no se usa el dataset de Kaggle: `torchvision.datasets.Food101(..., download=True)` o `load_dataset("food101")`, pero implica descargar ~5 GB cada sesión.
- Luego se **filtran las 34 clases** elegidas (el dataset trae las 101).
- Las fotos externas para la prueba de generalización se suben como un dataset propio de Kaggle o con *Upload*.

### Definición del proyecto (decidido)

**Concepto:** el usuario toma una foto de un plato; una red neuronal (transfer learning sobre Food-101) **clasifica el plato** y, a partir de esa clase, se muestra su **información nutricional** (calorías y macros) consultada en una tabla objetiva basada en **USDA FoodData Central**.

> Importante: la red **solo clasifica la imagen**; los macros NO los predice la red, vienen de una base de datos nutricional reconocida. Esto mantiene el criterio nutricional objetivo y defendible (no subjetivo como "saludable/no saludable").

**Las 34 clases seleccionadas** (comidas populares, con contraste nutricional):

1. hamburger · 2. pizza · 3. hot_dog · 4. french_fries · 5. club_sandwich · 6. grilled_cheese_sandwich · 7. nachos · 8. onion_rings · 9. steak · 10. fried_rice · 11. spaghetti_bolognese · 12. spaghetti_carbonara · 13. lasagna · 14. grilled_salmon · 15. tacos · 16. sushi · 17. ramen · 18. pad_thai · 19. dumplings · 20. chicken_curry · 21. chicken_wings · 22. pancakes · 23. waffles · 24. french_toast · 25. omelette · 26. caesar_salad · 27. greek_salad · 28. guacamole · 29. ice_cream · 30. donuts · 31. cheesecake · 32. hot_and_sour_soup · 33. miso_soup · 34. french_onion_soup

---

## Fase 1 — Entrega preliminar (título y objetivos)

| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 1.1 | Consolidar título + objetivo general + objetivos específicos | [x] | Publicados en el `README.md` (portada del repo) |
| 1.2 | Publicar en el **README del repo** (portada de GitHub) | [x] | Título + objetivos ya están en `README.md`. Lineamientos del profe movidos a `LINEAMIENTOS.md` |
| 1.3 | Hacer push del README al repositorio de GitHub | [x] | Ya subido a origin/main (repo jeorregog/EntregaFinalRedesNeuronales) |
| 1.4 | Confirmar con el profe si además hay que avisar por otro medio (buzón/correo) | [x] | Confirmado con el profe |

---

## Fase 2 — Desarrollo técnico en Kaggle (el notebook)

### 2.1 Problema y justificación
| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 2.1.1 | Escribir descripción clara del problema | [x] | En el informe (sección 1) y README |
| 2.1.2 | Escribir contexto y relevancia (¿por qué importa?) | [x] | En el informe (sección 1) |

> **Notebook definitivo corrido en Kaggle (Tesla T4):** `ProyectoFood.ipynb`. Resultados reales: **test accuracy 88.0% · F1 macro 88.0%**, entrenamiento ~37.6 min, 8 épocas, 0 errores, con análisis escrito y las 34 clases nutricionales de USDA cacheadas.

### 2.2 Datos
| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 2.2.1 | Cargar el dataset en Kaggle (Add Input → Food 101, montado en `/kaggle/input/`) | [x] | `kmader/food41` (K Scott Mader), 34.000 imágenes, 34 clases presentes |
| 2.2.2 | Describir dataset: tamaño, clases, distribución | [x] | Hecho en el notebook (gráfica de distribución + ejemplos). Falta redactarlo para el informe |
| 2.2.3 | División train / validación / test | [x] | 23.800 / 5.100 / 5.100, estratificado 70/15/15 |
| 2.2.4 | Preprocesamiento (resize, normalización ImageNet) | [x] | En el notebook, justificado |
| 2.2.5 | Data augmentation | [x] | Flip, rotación, color jitter (solo en train), justificado |
| 2.2.6 | Justificar cada decisión de preprocesamiento | [x] | En markdown del notebook |

### 2.3 Modelo
| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 2.3.1 | Implementar la arquitectura elegida | [x] | EfficientNet-B0 (~4.05M params), cabeza 1280→34 |
| 2.3.2 | Documentar la referencia (si es de la literatura) | [x] | Cita de EfficientNet (Tan & Le, 2019, ICML) en la sección 7 del notebook |
| 2.3.3 | Describir las modificaciones hechas al modelo original | [x] | Reemplazo del clasificador final por 34 clases |
| 2.3.4 | Justificar por qué la arquitectura es apropiada | [x] | En markdown (balance precisión/coste); ampliar en sustentación |

### 2.4 Entrenamiento y configuración experimental
| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 2.4.1 | Definir función de pérdida, optimizador y estrategia | [x] | CrossEntropy, AdamW (lr backbone/cabeza), Cosine scheduler, AMP |
| 2.4.2 | Definir y justificar hiperparámetros | [x] | 8 épocas, batch 64, lr 1e-3/1e-4; justificar en informe |
| 2.4.3 | Entrenar y registrar **curvas de aprendizaje** | [x] | Curvas loss/acc/F1 guardadas |
| 2.4.4 | Analizar comportamiento: sobreajuste / subajuste / convergencia | [x] | Redactado en `ProyectoFood.ipynb` (sobreajuste leve desde época 5) |
| 2.4.5 | Registrar recursos computacionales y tiempo | [x] | Tesla T4, 33.6 min |

### 2.5 Resultados y evaluación
| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 2.5.1 | Elegir y calcular métricas (accuracy, F1, etc.) | [x] | Test acc 88.0%, F1 macro 88.0% |
| 2.5.2 | Presentar resultados cuantitativos + visualizaciones | [x] | classification_report + matriz de confusión |
| 2.5.3 | **Evaluación en datos externos / no vistos** (generalización) | [x] | 5 fotos externas en `ProyectoFood.ipynb`: 3/5 aciertos (pizza, hamburguesa, onion_rings) |
| 2.5.4 | Analizar si el rendimiento se mantiene o se degrada y por qué | [x] | Redactado: 88%→60%, brecha de dominio |
| 2.5.5 | Identificar fortalezas, limitaciones y fallas del modelo | [x] | Redactado en análisis de resultados y conclusiones |
| 2.5.6 | Responder: ¿los resultados resuelven el problema planteado? | [x] | En conclusiones |

### 2.6 Conclusiones
| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 2.6.1 | Conclusiones ligadas a los objetivos y respaldadas por evidencia | [x] | Sección 13 de `ProyectoFood.ipynb` |
| 2.6.2 | Limitaciones y oportunidades de mejora | [x] | En conclusiones |

---

## Fase 3 — Informe final (PDF)

> Informe redactado en `INFORME.docx` (fuente editable) y `INFORME.md` (texto base). Incluye las 2 figuras (curvas y matriz de confusión).

| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 3.1 | Redactar informe, **máx. 3 páginas** | [x] | Contenido completo en `INFORME.docx` |
| 3.2 | Sección: Objetivos (problema + general + específicos) | [x] | |
| 3.3 | Sección: Base de datos (origen, características, preproc., splits) | [x] | |
| 3.4 | Sección: Modelo (arquitectura, justificación, decisiones) | [x] | Con justificación de hiperparámetros |
| 3.5 | Sección: Resultados (métricas, visualizaciones, datos externos) | [x] | 2 figuras + generalización + nutrición USDA |
| 3.6 | Sección: Conclusiones (hallazgos, objetivos cumplidos, limitaciones) | [x] | |
| 3.7 | Seleccionar cuidadosamente tablas/figuras (por el límite de 3 págs) | [x] | 2 figuras: curvas y matriz de confusión |
| 3.8 | Verificar que quepa en 3 páginas y exportar a PDF | [x] | Exportado a PDF |

---

## Fase 4 — Presentación / sustentación oral

> Trabajo individual: expones tú solo. Recuerda: es **obligatorio asistir a TODAS las exposiciones** (faltar penaliza tu nota) y calificar a los demás grupos.

| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 4.1 | Armar presentación/diapositivas (15 min máx) | [x] | `PRESENTACION.pptx`, 11 diapositivas con figuras. Falta darle estilo/tema (manual) |
| 4.2 | Preparar para preguntas (5 min) | [ ] | Dominar todas las partes del proyecto |
| 4.3 | Ensayar tiempos | [ ] | 15 min exposición |

---

## Fase 5 — Entrega final y sustentación

| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 5.1 | Entregar **presentación (diapositivas) + informe final (PDF)** | [ ] | **Fecha límite: miércoles 11 nov 2026** |
| 5.2 | Confirmar medio de entrega (buzón interactiva / correo del profesor) | [ ] | No se aceptan versiones posteriores a la fecha |
| 5.3 | Asistir a todas las exposiciones y **calificar a los demás grupos** | [ ] | Evaluación entre pares (sección 6 lineamientos); la nota debe ser objetiva, si difiere >0.5 de la del profe hay penalización |
| 5.4 | Sustentar el proyecto | [ ] | Semanas 17/18 (19 según el PDF) |

---

## Notas y restricciones clave

- **Entorno:** todo el desarrollo corre en **Kaggle Notebooks** (GPU T4/P100, ~30 h/semana). Food-101 se añade como dataset de Kaggle (sin re-descargas). Elegir modelo que quepa en ese presupuesto.
- **Fechas:**
  - Entrega preliminar (título + objetivos): **viernes 30 oct 2026**.
  - Presentación + informe final: **miércoles 11 nov 2026**.
  - Sustentaciones: semanas 17/18 (el PDF menciona también 19).
  - Sin entrega a tiempo → calificación **0.0**. No se aceptan correcciones posteriores.
- **Ponderación:** 20% de la nota del curso (15% oral + 5% informe).
- **Énfasis del profesor:** no basta una buena métrica — hay que **entender, justificar e interpretar críticamente**. "Presentar una gráfica no equivale a analizarla."
- **Generalización:** es obligatorio evaluar el modelo en **datos externos / no vistos** y analizar si se mantiene o degrada.
- **Asistencia (solo en el PDF):** inasistencia a exposiciones penaliza la nota del proyecto (hasta 3h tolerancia; 6h −5%; 9h −10%; 12h −20%).
- **Algoritmo de nota (solo en el PDF):** promedia nota del profesor y de estudiantes; si difieren >0.5 aplica penalización creciente.
