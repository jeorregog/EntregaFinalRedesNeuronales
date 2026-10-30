# TODO — Proyecto Final Deep Learning

Checklist de todo lo que hay que hacer para el proyecto final, derivado de `README.md` y `00. Projects.pdf`.
Pensado para ejecutarse en **Google Colab** (no en máquina local). Proyecto, dataset y modelo **por definir** (abierto).

Convención: `[ ]` pendiente · `[x]` hecho. Vamos tachando a medida que avanzamos.

---

## Fase 0 — Decisiones de diseño (hacer PRIMERO, lo definimos nosotros)

| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 0.1 | Elegir el **tipo de problema** | [x] | Clasificación de imágenes de comida (visión por computador) |
| 0.2 | Definir el **dataset** (origen, tamaño, nº de clases) | [x] | **Food-101** (subconjunto de **34 clases** de comidas populares). Fuente nutricional: **USDA FoodData Central** |
| 0.3 | Elegir la **arquitectura del modelo** | [x] | CNN preentrenada con *transfer learning* (candidatas: ResNet50 / EfficientNet). Decidir cuál al implementar |
| 0.4 | Redactar **título del proyecto** | [x] | Ver `EntregaPreliminar.md` |
| 0.5 | Redactar **objetivo general** (preciso y medible) | [x] | Ver `EntregaPreliminar.md` |
| 0.6 | Redactar **2-3 objetivos específicos** (alineados con la metodología) | [x] | 3 objetivos, en `EntregaPreliminar.md` |
| 0.7 | Verificar factibilidad en Colab (tamaño del dataset vs tiempo de sesión / VRAM) | [ ] | Tener plan B de submuestreo si hace falta. Food-101 ≈ 5 GB (101k imágenes); con 34 clases ≈ 34k imágenes |

### Nota sobre la descarga del dataset en Colab (importante)

Food-101 **no requiere Kaggle ni descarga manual**. Se trae directo al notebook por código:
- **Opción A (torchvision):** `torchvision.datasets.Food101(root=..., split="train"/"test", download=True)` — descarga y descomprime solo. Trae un split train/test oficial.
- **Opción B (HuggingFace):** `load_dataset("food101")` — también descarga directo al entorno.
- Luego se **filtran las 34 clases** elegidas (el dataset trae las 101).
- Los datos viven en el disco temporal de la sesión de Colab (se borran al cerrar). Si molesta re-descargar, se puede montar Google Drive y cachear ahí.
- Evitamos la API de Kaggle (sin tokens ni `kaggle.json`).

### Definición del proyecto (decidido)

**Concepto:** el usuario toma una foto de un plato; una red neuronal (transfer learning sobre Food-101) **clasifica el plato** y, a partir de esa clase, se muestra su **información nutricional** (calorías y macros) consultada en una tabla objetiva basada en **USDA FoodData Central**.

> Importante: la red **solo clasifica la imagen**; los macros NO los predice la red, vienen de una base de datos nutricional reconocida. Esto mantiene el criterio nutricional objetivo y defendible (no subjetivo como "saludable/no saludable").

**Las 34 clases seleccionadas** (comidas populares, con contraste nutricional):

1. hamburger · 2. pizza · 3. hot_dog · 4. french_fries · 5. club_sandwich · 6. grilled_cheese_sandwich · 7. nachos · 8. onion_rings · 9. steak · 10. fried_rice · 11. spaghetti_bolognese · 12. spaghetti_carbonara · 13. lasagna · 14. grilled_salmon · 15. tacos · 16. sushi · 17. ramen · 18. pad_thai · 19. dumplings · 20. chicken_curry · 21. chicken_wings · 22. pancakes · 23. waffles · 24. french_toast · 25. omelette · 26. caesar_salad · 27. greek_salad · 28. guacamole · 29. ice_cream · 30. donuts · 31. cheesecake · 32. hot_and_sour_soup · 33. miso_soup · 34. french_onion_soup

---

## Fase 1 — Entrega preliminar (título y objetivos)

| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 1.1 | Consolidar título + objetivo general + objetivos específicos | [x] | Listo en `EntregaPreliminar.md` (texto plano para copiar-pegar) |
| 1.2 | Enviar por el medio indicado (buzón / correo del profesor) | [ ] | **Fecha límite: viernes 30 oct 2026**. Antes: preguntar formato al profe |

---

## Fase 2 — Desarrollo técnico en Colab (el notebook)

### 2.1 Problema y justificación
| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 2.1.1 | Escribir descripción clara del problema | [ ] | |
| 2.1.2 | Escribir contexto y relevancia (¿por qué importa?) | [ ] | |

### 2.2 Datos
| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 2.2.1 | Cargar el dataset en Colab (por código, reproducible) | [ ] | |
| 2.2.2 | Describir dataset: tamaño, clases, distribución | [ ] | Con gráficas de distribución |
| 2.2.3 | División train / validación / test | [ ] | Justificar proporciones |
| 2.2.4 | Preprocesamiento (normalización, resize, tokenización, etc.) | [ ] | Según el tipo de dato |
| 2.2.5 | Data augmentation (si aplica) | [ ] | Justificar cada técnica |
| 2.2.6 | Justificar cada decisión de preprocesamiento | [ ] | No basta aplicarlas |

### 2.3 Modelo
| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 2.3.1 | Implementar la arquitectura elegida | [ ] | |
| 2.3.2 | Documentar la referencia (si es de la literatura) | [ ] | |
| 2.3.3 | Describir las modificaciones hechas al modelo original | [ ] | |
| 2.3.4 | Justificar por qué la arquitectura es apropiada | [ ] | Demostrar que entendemos cómo funciona |

### 2.4 Entrenamiento y configuración experimental
| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 2.4.1 | Definir función de pérdida, optimizador y estrategia | [ ] | Justificar |
| 2.4.2 | Definir y justificar hiperparámetros | [ ] | |
| 2.4.3 | Entrenar y registrar **curvas de aprendizaje** (loss y métricas, train y validación) | [ ] | Guardar las gráficas |
| 2.4.4 | Analizar comportamiento: sobreajuste / subajuste / convergencia | [ ] | Interpretación crítica, no solo mostrar la gráfica |
| 2.4.5 | Registrar recursos computacionales (CPU/GPU/RAM) y tiempo de entrenamiento | [ ] | En Colab: anotar tipo de GPU (T4/otra) |

### 2.5 Resultados y evaluación
| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 2.5.1 | Elegir y calcular métricas (accuracy, F1, etc.) | [ ] | Acordes al problema |
| 2.5.2 | Presentar resultados cuantitativos + visualizaciones | [ ] | p.ej. matriz de confusión |
| 2.5.3 | **Evaluación en datos externos / no vistos** (generalización) | [ ] | Requisito explícito del PDF |
| 2.5.4 | Analizar si el rendimiento se mantiene o se degrada y por qué | [ ] | |
| 2.5.5 | Identificar fortalezas, limitaciones y fallas del modelo | [ ] | |
| 2.5.6 | Responder: ¿los resultados resuelven el problema planteado? | [ ] | |

### 2.6 Conclusiones
| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 2.6.1 | Conclusiones ligadas a los objetivos y respaldadas por evidencia | [ ] | Evitar afirmaciones genéricas |
| 2.6.2 | Limitaciones y oportunidades de mejora | [ ] | |

---

## Fase 3 — Informe final (PDF)

| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 3.1 | Redactar informe en PDF, **máx. 3 páginas** | [ ] | Síntesis clara y técnica |
| 3.2 | Sección: Objetivos (problema + general + específicos) | [ ] | |
| 3.3 | Sección: Base de datos (origen, características, preproc., splits) | [ ] | |
| 3.4 | Sección: Modelo (arquitectura, justificación, decisiones) | [ ] | |
| 3.5 | Sección: Resultados (métricas, visualizaciones, datos externos) | [ ] | |
| 3.6 | Sección: Conclusiones (hallazgos, objetivos cumplidos, limitaciones) | [ ] | |
| 3.7 | Seleccionar cuidadosamente tablas/figuras (por el límite de 3 págs) | [ ] | |

---

## Fase 4 — Presentación / sustentación oral

| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 4.1 | Armar presentación (15 min máx) siguiendo el orden del PDF | [ ] | problema → objetivos → modelo → datos → entrenamiento → resultados → conclusiones |
| 4.2 | Repartir secciones entre integrantes (todos exponen) | [ ] | |
| 4.3 | Preparar para preguntas (cualquiera responde cualquier parte) | [ ] | 5 min de preguntas |
| 4.4 | Ensayar tiempos | [ ] | |

---

## Fase 5 — Entrega final

| # | Tarea | Estado | Notas |
|---|-------|:------:|-------|
| 5.1 | Entregar **presentación + informe final** | [ ] | **Fecha límite: miércoles 11 nov 2026** |
| 5.2 | Confirmar medio de entrega (buzón interactiva / correo del profesor) | [ ] | No se aceptan versiones posteriores a la fecha |

---

## Notas y restricciones clave

- **Entorno:** todo el desarrollo corre en **Google Colab** (GPU T4 gratis, con límites de tiempo de sesión y VRAM). Elegir dataset/modelo que quepan en ese presupuesto.
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
