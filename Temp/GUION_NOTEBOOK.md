# Guion del notebook — qué hace cada celda (para explicarlo en vivo)

Explicación en lenguaje natural de cada celda de código de `ProyectoFood.ipynb`. La idea es que, al mostrar el notebook, puedas decir con tus palabras qué hace y por qué, sin leer línea por línea.

---

### Celda 1 — Imports y configuración
**Qué hace:** importa las librerías (PyTorch para el modelo, torchvision para imágenes, sklearn para métricas, matplotlib/seaborn para gráficas), fija una semilla aleatoria (SEED=42) y detecta si hay GPU.
**Cómo decirlo:** "Aquí cargo las librerías, fijo una semilla para que los resultados sean reproducibles, y detecto la GPU. Si hay CUDA disponible, entreno en GPU."

### Celda 2 — Configuración del proyecto
**Qué hace:** define la lista de las 34 clases y los hiperparámetros base: tamaño de imagen 224, batch 64, 8 épocas, learning rate 1e-3.
**Cómo decirlo:** "Defino las 34 clases que voy a clasificar y los hiperparámetros principales. El tamaño 224 es el que espera EfficientNet."

### Celda 3 — Localizar el dataset
**Qué hace:** busca automáticamente dónde está Food-101 dentro de `/kaggle/input`; si no lo encuentra, lo descargaría con torchvision.
**Cómo decirlo:** "Esta celda localiza la carpeta del dataset en Kaggle de forma robusta, probando la ruta conocida y, si falla, buscándola o descargándola."

### Celda 4 — Filtrar a 34 clases
**Qué hace:** carga el dataset completo con ImageFolder, verifica que las 34 clases existan como carpetas, y guarda los índices de solo esas 34 (el dataset trae 101).
**Cómo decirlo:** "Food-101 tiene 101 clases; aquí me quedo solo con las 34 que elegí, filtrando por nombre de carpeta."

### Celda 5 — Clase Dataset personalizada (Food34)
**Qué hace:** define una clase que, para cada imagen, la abre, le aplica las transformaciones y devuelve la imagen + su etiqueta re-mapeada al rango 0–33.
**Cómo decirlo:** "Creo un Dataset propio de PyTorch que carga cada imagen bajo demanda, le aplica el preprocesamiento y le asigna su etiqueta entre las 34 clases."
**Si preguntan por qué una clase propia:** "Porque ImageFolder trae las 101 clases; necesito re-mapear las etiquetas a mis 34 y aplicar transformaciones distintas a train y a test."

### Celda 6 — División train / val / test
**Qué hace:** separa los datos en 70% entrenamiento, 15% validación y 15% prueba, de forma **estratificada** (misma proporción de cada clase en cada parte).
**Cómo decirlo:** "Divido los datos en tres: entreno con el 70%, ajusto/elijo el mejor modelo con el 15% de validación, y mido el resultado final con el 15% de test que el modelo nunca ve."
**Por qué estratificado:** "Para que cada clase esté igual de representada en los tres conjuntos y la evaluación sea justa."

### Celda 7 — Transformaciones (preprocesamiento + augmentation)
**Qué hace:** define dos pipelines. En **train**: recorte aleatorio, volteo, rotación y color jitter (aumento de datos). En **val/test**: solo resize y normalización. Ambos normalizan con los valores de ImageNet. Luego crea los DataLoaders.
**Cómo decirlo:** "En entrenamiento aplico aumento de datos para que el modelo vea variaciones y no memorice; en validación y test no, para medir de forma realista. Normalizo con los valores de ImageNet porque el modelo preentrenado los espera."

### Celda 8 — Gráfica de distribución de clases
**Qué hace:** cuenta cuántas imágenes hay por clase y lo grafica.
**Cómo decirlo:** "Confirmo que el dataset está balanceado: 1.000 imágenes por clase. Eso es importante porque justifica usar accuracy como métrica."

### Celda 9 — Ejemplos visuales por clase
**Qué hace:** muestra una imagen de ejemplo de las primeras 12 clases.
**Cómo decirlo:** "Muestro ejemplos para ver con qué tipo de imágenes trabajo: son fotos reales de comida, con fondos y ángulos variados."

### Celda 10 — Cargar el modelo (EfficientNet-B0)
**Qué hace:** carga EfficientNet-B0 preentrenada en ImageNet y **reemplaza la última capa** (la que daba 1000 clases de ImageNet) por una nueva de 34 salidas.
**Cómo decirlo:** "Cargo EfficientNet-B0 ya preentrenada y le cambio solo la capa final para que prediga mis 34 clases en vez de las 1000 de ImageNet. Esto es el transfer learning."

### Celda 11 — Pérdida, optimizador y función de entrenamiento
**Qué hace:** define CrossEntropy (pérdida), AdamW con learning rate diferenciado (bajo en el backbone, alto en la cabeza), el scheduler coseno, la precisión mixta (AMP) y una función `run_epoch` que hace una pasada completa calculando pérdida y métricas.
**Cómo decirlo:** "Uso CrossEntropy porque es clasificación. Con AdamW doy un learning rate bajo al backbone preentrenado para no dañarlo y uno más alto a la capa nueva. La función run_epoch ejecuta una época de entrenamiento o evaluación."

### Celda 12 — Bucle de entrenamiento
**Qué hace:** entrena 8 épocas; en cada una entrena y valida, guarda las métricas en `history`, y **guarda el mejor modelo** según el F1 de validación.
**Cómo decirlo:** "Aquí entreno 8 épocas. En cada una mido en validación y guardo el modelo que mejor F1 obtiene, para quedarme con el mejor y no con el de la última época."

### Celda 13 — Curvas de aprendizaje
**Qué hace:** grafica loss, accuracy y F1 de train y validación a lo largo de las épocas.
**Cómo decirlo:** "Estas curvas muestran cómo aprende el modelo. Se ve que converge y que desde la época 5 hay un sobreajuste leve: el train sigue mejorando pero la validación se estanca."

### Celda de texto — Análisis del entrenamiento (markdown, va justo después de las curvas)
**Qué hace:** no es código; es la interpretación escrita de las curvas. Resume que el F1 de validación sube de 0,818 a 0,882 y se estabiliza (convergencia); que desde la época 5 hay sobreajuste leve (F1 de train 0,916→0,942 vs. validación estancada en ~0,88, y la *val loss* deja de bajar); que no hay subajuste; y que el augmentation y el weight decay contienen el sobreajuste, por lo que parar en 8 épocas y quedarse con el mejor modelo por F1 de validación es razonable.
**Cómo decirlo:** "Aquí no solo muestro la gráfica, la interpreto: el modelo converge, la validación se estabiliza en 0,88 y desde la época 5 hay un sobreajuste leve y esperable en fine-tuning. Lo contengo con data augmentation y weight decay, y por eso corto en 8 épocas y me quedo con el mejor modelo según validación, no con el de la última época."
**Por qué importa:** el profe dijo que "presentar una gráfica no equivale a analizarla"; esta celda es justo el análisis que valora.

### Celda 14 — Evaluación en test
**Qué hace:** carga el mejor modelo guardado y lo evalúa sobre el conjunto de test; imprime accuracy, F1 y el reporte por clase.
**Cómo decirlo:** "Cargo el mejor modelo y lo evalúo en el test, que son imágenes que nunca vio. Da 88% de accuracy y 0,88 de F1 macro."

### Celda 15 — Matriz de confusión
**Qué hace:** calcula y grafica la matriz de confusión normalizada.
**Cómo decirlo:** "La matriz muestra con qué se confunde cada clase. La diagonal fuerte indica buenos aciertos; los errores se concentran entre platos parecidos, como pastas al horno o platos de huevo."

### Celda de texto — Análisis de resultados en test (markdown, va justo después de la matriz)
**Qué hace:** no es código; es la interpretación escrita de las métricas. Explica que accuracy y F1 macro coinciden (88,0% / 0,880) porque el test está balanceado (150 imágenes por clase); lista las mejores clases (onion_rings 0,946, miso_soup 0,934, spaghetti_carbonara 0,932, dumplings 0,924, pizza/french_fries 0,918), que son platos visualmente distintivos; y las peores (omelette 0,782, lasagna 0,797, french_toast 0,806, chicken_curry 0,813, grilled_cheese_sandwich 0,820), cuyas confusiones tienen sentido visual (lasaña↔pastas al horno, omelette↔platos de huevo, french toast↔pancakes/waffles).
**Cómo decirlo:** "Interpreto el reporte y la matriz: el modelo acierta en platos con forma, color o textura muy característicos, y falla justo en comidas que se parecen entre sí. Los errores no son dispersos, se concentran entre clases visualmente cercanas, lo que confirma que el modelo aprendió rasgos visuales coherentes."
**Por qué importa:** vuelve a ser análisis crítico, no solo mostrar la figura; conecta los errores con una explicación visual.

### Celda 16 — Tabla nutricional (USDA)
**Qué hace:** define el diccionario con los macros por 100 g de cada clase, obtenidos de la API de USDA y cacheados.
**Cómo decirlo:** "Aquí están los valores nutricionales de cada plato, sacados de USDA FoodData Central. Los dejé cacheados para que el notebook corra sin depender de internet."

### Celda 17 — Función de consulta nutricional
**Qué hace:** función que, dada una clase, devuelve su texto nutricional formateado.
**Cómo decirlo:** "Esta función toma la clase predicha y devuelve sus calorías y macros."

### Celda 18 — (Opcional) Refrescar desde la API de USDA
**Qué hace:** incluye la función que consulta la API de USDA en vivo, por si se quiere actualizar los valores.
**Cómo decirlo:** "Esta celda es opcional: permite volver a consultar la API de USDA en vivo. Normalmente no hace falta porque los datos ya están cacheados."

### Celda 19 — Predicción en datos externos + nutrición
**Qué hace:** define `predecir()`, que toma una foto, la clasifica, muestra el top-3 de probabilidades y su información nutricional; luego la aplica a las 5 fotos externas.
**Cómo decirlo:** "Esta es la demo final: le paso una foto externa, el modelo la clasifica, muestra las 3 clases más probables con su confianza, y le asocio la información nutricional. Aquí se ve la prueba de generalización."

---

## Consejos para mostrar el notebook en vivo

- **No leas el código línea por línea.** Di qué *hace* la celda y *por qué*, en una o dos frases.
- **Resalta las 4 celdas clave:** modelo (10), entrenamiento (12), evaluación (14) y la demo de predicción (19). Son las que el profe querrá ver.
- Si una celda es "de plomería" (imports, rutas, dataset personalizado), resúmela rápido: "esto prepara/carga los datos" y sigue.
- Ten a mano los números: 34 clases, 34.000 imágenes, 88% accuracy, 37,6 min en T4.
- Si preguntan por una línea concreta, respira y explícala con calma; conoces el código.
