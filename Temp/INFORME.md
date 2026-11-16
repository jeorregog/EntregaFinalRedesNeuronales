# Reconocimiento automático de platos de comida para la estimación de información nutricional

**Autor:** Juan Esteban Orrego · **Curso:** Deep Learning (Redes Neuronales y Aprendizaje Profundo)

---

## 1. Objetivos

**Problema.** El registro manual de la ingesta alimentaria es tedioso y poco preciso, lo que dificulta el seguimiento de hábitos nutricionales. Reconocer automáticamente el plato a partir de una fotografía puede apoyar aplicaciones de salud y control dietético. El reto aquí es de clasificación de imágenes: identificar, entre varias categorías de comida, cuál aparece en una foto.

**Objetivo general.** Desarrollar un modelo de clasificación de imágenes basado en redes neuronales convolucionales y *transfer learning*, capaz de identificar 34 categorías de platos de comida populares a partir de una fotografía, asociando cada predicción a su información nutricional de referencia (calorías y macronutrientes).

**Objetivos específicos.** (1) Preparar y analizar un subconjunto de 34 clases del dataset Food-101, con preprocesamiento, aumento de datos y división en entrenamiento/validación/prueba. (2) Entrenar y optimizar un modelo mediante *transfer learning* sobre una CNN preentrenada, evaluándolo con accuracy y F1-score. (3) Evaluar la generalización sobre imágenes externas no vistas y vincular cada predicción con su información nutricional (USDA FoodData Central).

## 2. Base de datos

Se utilizó **Food-101** (Bossard et al., 2014), dataset público de 101 categorías de comida con 1.000 imágenes cada una. Se seleccionó un subconjunto de **34 clases** de comidas populares y con contraste nutricional (p. ej. hamburguesa, pizza, sushi, ensaladas, sopas, postres), resultando en **34.000 imágenes**.

**Preprocesamiento.** Las imágenes se redimensionaron a 224×224 px (tamaño de entrada de la red) y se normalizaron con la media y desviación de ImageNet, requisito al usar un modelo preentrenado en ese dataset.

**Aumento de datos** (solo en entrenamiento): recorte aleatorio, volteo horizontal, rotación (±15°) y *jitter* de color. Aumenta la variabilidad y reduce el sobreajuste; no se aplica en validación/prueba para medir de forma realista.

**División.** Partición estratificada (misma proporción por clase): **70% entrenamiento (23.800), 15% validación (5.100) y 15% prueba (5.100)**. La validación se usa para seleccionar el mejor modelo y la prueba para la evaluación final sobre datos no vistos.

## 3. Modelo

Se empleó **EfficientNet-B0** (Tan & Le, 2019) preentrenado en ImageNet, mediante *transfer learning*. Esta arquitectura introduce el *compound scaling* (escalado equilibrado de profundidad, ancho y resolución), logrando alta precisión con pocos parámetros (~4 M), lo que la hace apropiada para entrenar con recursos limitados.

**Decisiones de implementación.** Se conservó el *backbone* convolucional preentrenado y se reemplazó únicamente la capa de clasificación final (de 1.000 clases de ImageNet a 34 de comida). El entrenamiento usó *learning rate* diferenciado (menor en el *backbone*, mayor en la cabeza), función de pérdida *CrossEntropy*, optimizador *AdamW* con *weight decay*, *scheduler* coseno y precisión mixta (AMP). Se entrenó 8 épocas con *batch* de 64, conservando el mejor modelo según el F1 de validación.

**Recursos.** Entrenamiento en Google Kaggle con GPU **Tesla T4**; tiempo total ~**37,6 minutos**.

## 4. Resultados

**Desempeño en prueba.** El modelo alcanzó **88,0% de accuracy** y **0,88 de F1 macro** sobre 5.100 imágenes no vistas. Que ambas métricas coincidan refleja el balance del dataset (150 imágenes por clase).

**Curvas de aprendizaje.** El F1 de validación sube de 0,82 a 0,88 y se estabiliza; a partir de la época 5 aparece un sobreajuste leve (F1 de entrenamiento 0,94 vs. validación 0,88), moderado y esperable en *fine-tuning*.

**Por clase.** Mejor desempeño en platos visualmente distintivos (onion_rings 0,95; miso_soup 0,93; spaghetti_carbonara 0,93). Peor desempeño en platos que se confunden con similares (omelette 0,78; lasagna 0,80; french_toast 0,81). La matriz de confusión concentra los errores entre comidas visualmente cercanas.

**Generalización (datos externos).** Se evaluó con 5 fotografías externas ajenas al dataset. El modelo acertó **3 de 5 (60%)**: pizza, hamburguesa y aros de cebolla, todos con alta confianza. Los dos errores (unas papas "cargadas" clasificadas como arroz frito, y una foto con varios hot dogs clasificada como omelette) ocurrieron con baja confianza y en imágenes compuestas o atípicas. La caída de 88% a 60% evidencia la **brecha de dominio**: las fotos externas difieren en iluminación, ángulo y encuadre respecto a Food-101.

**Información nutricional.** Cada predicción se vincula con sus macros por 100 g (kcal, proteína, grasa, carbohidratos) obtenidos de **USDA FoodData Central**. La red solo clasifica; los valores nutricionales provienen de una fuente oficial, manteniendo el criterio objetivo.

## 5. Conclusiones

Se cumplieron los objetivos: un clasificador de 34 platos con *transfer learning* que alcanza 88% de accuracy en prueba y se integra con información nutricional de referencia. El análisis muestra que los aciertos se concentran en platos distintivos y los errores en comidas visualmente similares, patrón coherente tanto en prueba como en datos externos. El modelo generaliza aceptablemente a imágenes claras, pero su desempeño se degrada ante platos compuestos o presentaciones atípicas, limitación propia de haber entrenado sobre un único dataset. Como mejoras futuras: ampliar los datos con dominios variados, aplicar regularización adicional o *early stopping*, explorar arquitecturas mayores (EfficientNet-B3) y estimar la porción para una estimación nutricional más precisa.

---

*Referencias:* Bossard, L., Guillaumin, M., & Van Gool, L. (2014). Food-101 – Mining Discriminative Components with Random Forests. ECCV. · Tan, M., & Le, Q. (2019). EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks. ICML.
