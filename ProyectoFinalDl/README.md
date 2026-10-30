# Reconocimiento automático de platos de comida para la estimación de información nutricional

Proyecto final del curso de Deep Learning (Redes Neuronales y Aprendizaje Profundo).

Clasificación de imágenes de comida mediante redes neuronales convolucionales y *transfer learning*: a partir de la fotografía de un plato, el modelo identifica de qué comida se trata y, con esa predicción, se consulta su información nutricional de referencia (calorías y macronutrientes).

> El documento con los lineamientos del curso está en [`LINEAMIENTOS.md`](LINEAMIENTOS.md).

---

## Objetivo general

Desarrollar un modelo de clasificación de imágenes basado en redes neuronales convolucionales y transfer learning, capaz de identificar 34 categorías de platos de comida populares a partir de una fotografía, asociando cada predicción a su información nutricional de referencia (calorías y macronutrientes) para apoyar el registro dietético.

## Objetivos específicos

1. Preparar y analizar un subconjunto de 34 clases del dataset Food-101, aplicando preprocesamiento y aumento de datos, y dividiéndolo en conjuntos de entrenamiento, validación y prueba.
2. Entrenar y optimizar un modelo de clasificación mediante transfer learning sobre una arquitectura CNN preentrenada, evaluando su desempeño con métricas como accuracy y F1-score.
3. Evaluar la generalización del modelo sobre imágenes externas no vistas y vincular cada predicción con su información nutricional de referencia (basada en USDA FoodData Central), analizando fortalezas y limitaciones.

---

## Descripción del proyecto

- **Problema:** el registro manual de lo que una persona come es tedioso y propenso a errores. Reconocer automáticamente el plato a partir de una foto puede apoyar el seguimiento nutricional, de interés en salud, control de peso y hábitos alimenticios.
- **Enfoque:** la red neuronal **solo clasifica la imagen** (identifica el plato). La información nutricional **no la predice la red**; se obtiene de una tabla de referencia basada en [USDA FoodData Central](https://fdc.nal.usda.gov/), lo que mantiene el criterio nutricional objetivo y verificable.
- **Dataset:** [Food-101](https://data.vision.ee.ethz.ch/cvl/datasets_extra/food-101/) — subconjunto de **34 clases** de comidas populares (de las 101 originales).
- **Modelo:** CNN preentrenada con *transfer learning* (candidatas: ResNet50 / EfficientNet).
- **Entorno de ejecución:** Google Colab (GPU).

### Clases consideradas (34)

`hamburger`, `pizza`, `hot_dog`, `french_fries`, `club_sandwich`, `grilled_cheese_sandwich`, `nachos`, `onion_rings`, `steak`, `fried_rice`, `spaghetti_bolognese`, `spaghetti_carbonara`, `lasagna`, `grilled_salmon`, `tacos`, `sushi`, `ramen`, `pad_thai`, `dumplings`, `chicken_curry`, `chicken_wings`, `pancakes`, `waffles`, `french_toast`, `omelette`, `caesar_salad`, `greek_salad`, `guacamole`, `ice_cream`, `donuts`, `cheesecake`, `hot_and_sour_soup`, `miso_soup`, `french_onion_soup`

---

## Estado del proyecto

- [x] Entrega preliminar: título y objetivos
- [ ] Desarrollo del modelo (notebook en Colab)
- [ ] Informe final (PDF, máx. 3 páginas)
- [ ] Presentación / sustentación

El seguimiento detallado de tareas está en [`TODO.md`](TODO.md).
