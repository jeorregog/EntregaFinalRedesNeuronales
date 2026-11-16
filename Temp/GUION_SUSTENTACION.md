# Guion de sustentación — defensa del proyecto

Documento de estudio para la exposición oral. Para cada decisión: **qué** se hizo, **por qué**, y **por qué no la alternativa**. El profesor valora entender y justificar, no solo que el modelo funcione.

---

## 1. ¿Por qué deep learning y no machine learning clásico (Random Forest, SVM)?

**Qué:** usamos una red neuronal convolucional (CNN), no un modelo clásico tipo Random Forest o SVM.

**Por qué:** el problema es clasificación de **imágenes**. Un Random Forest o SVM necesita que tú le des *features* (características) ya extraídas; no "miran" la imagen, trabajan sobre números tabulares. Para que funcionaran habría que diseñar manualmente descriptores (color, texturas, bordes, HOG, SIFT...), un proceso laborioso y que captura mal la complejidad visual de la comida.

**Por qué no clásico:** una CNN **aprende las características por sí sola** directamente de los píxeles, en capas: primero bordes y colores, luego texturas, luego formas de ingredientes, hasta reconocer el plato completo. En imágenes, las CNN superan ampliamente a RF/SVM. Con 34 clases y comidas visualmente parecidas, un modelo clásico daría una precisión muy baja.

**Frase para decir:** "Elegimos una CNN porque es el estándar en visión por computador: aprende las características visuales automáticamente de los píxeles, mientras que un Random Forest o SVM requeriría extraer features a mano y rinde mucho peor en imágenes."

---

## 2. ¿Por qué transfer learning y no entrenar una CNN desde cero?

**Qué:** no entrenamos una red desde cero; partimos de EfficientNet-B0 **ya preentrenada en ImageNet** y la ajustamos (*fine-tuning*).

**Por qué:** entrenar una CNN desde cero necesita **millones de imágenes** y mucho cómputo. Nosotros tenemos 34.000 imágenes y una GPU gratuita. ImageNet tiene 1,2 millones de imágenes de 1.000 categorías, así que el modelo preentrenado **ya sabe reconocer formas, texturas y patrones visuales generales**. Solo hay que reenseñarle a aplicar ese conocimiento a comida.

**Por qué no desde cero:** con solo 34.000 imágenes, una red desde cero sobreajustaría o no aprendería bien; tardaría mucho más y daría peor resultado. El transfer learning reutiliza conocimiento y converge en pocas épocas (nos bastaron 8).

**Frase para decir:** "Usamos transfer learning porque entrenar desde cero exige millones de imágenes; aprovechar una red preentrenada en ImageNet nos da características visuales ya aprendidas y alcanzamos 88% en pocas épocas."

---

## 3. ¿Por qué EfficientNet-B0 y no ResNet, VGG u otra?

**Qué:** elegimos EfficientNet-B0 como arquitectura base.

**Por qué:** EfficientNet ofrece el **mejor balance precisión / coste** de su generación. Su idea central, el *compound scaling*, escala de forma equilibrada tres dimensiones (profundidad, ancho y resolución), logrando alta precisión con **muchos menos parámetros** (~4 M en B0) que modelos previos.

**Por qué no otras:**
- **VGG16/19:** muy pesada (~138 M parámetros), lenta y hoy desactualizada. Mismo o peor rendimiento con mucho más cómputo.
- **ResNet50:** buena y clásica (~25 M parámetros), pero EfficientNet-B0 logra precisión comparable o mejor con ~6× menos parámetros, ideal para la GPU limitada de Kaggle.
- **EfficientNet-B3/B7 o Vision Transformers:** más potentes pero más pesados y lentos; para 34 clases y nuestro presupuesto, B0 es suficiente y entrena en ~37 min.

**Frase para decir:** "Escogimos EfficientNet-B0 por su eficiencia: con solo ~4 millones de parámetros alcanza una precisión comparable a ResNet50, que tiene seis veces más, gracias al compound scaling. Para nuestros recursos en Kaggle era la opción más equilibrada."

---

## 4. ¿Por qué Food-101 y 34 clases (no las 101)?

**Qué:** usamos Food-101 pero un subconjunto de 34 clases populares.

**Por qué el dataset:** Food-101 es un estándar público, bien etiquetado, balanceado (1.000 imágenes por clase) y con comida real fotografiada por usuarios (no de estudio), lo que lo hace realista.

**Por qué 34 y no 101:** (1) acotar el alcance para un proyecto individual viable en Kaggle; (2) elegimos las comidas **más populares/cotidianas** (hamburguesa, pizza, sopas...), coherente con el objetivo de registro dietético diario; (3) con menos clases el modelo acierta más y el análisis de errores es más claro y defendible.

**Frase para decir:** "Trabajamos con 34 clases de comidas populares en lugar de las 101 para ajustar el alcance a los recursos disponibles y centrarnos en platos cotidianos, que es lo que tiene sentido para un asistente nutricional."

---

## 5. ¿Por qué estos preprocesamientos?

- **Resize a 224×224:** es el tamaño de entrada con el que EfficientNet-B0 fue preentrenada; hay que respetarlo.
- **Normalización con media/desviación de ImageNet:** el modelo preentrenado "espera" imágenes en esa escala; si no normalizas igual, las predicciones se degradan.
- **Data augmentation (flip, rotación, color jitter) solo en train:** genera variaciones artificiales para que el modelo no memorice las fotos exactas (reduce sobreajuste) y generalice mejor. **No se aplica en validación/test** porque ahí queremos medir el desempeño sobre imágenes reales sin alterar.

**Frase para decir:** "Normalizamos con los valores de ImageNet porque el modelo preentrenado los espera, y aplicamos aumento de datos solo en entrenamiento para reducir el sobreajuste sin contaminar la evaluación."

---

## 6. ¿Por qué estos hiperparámetros?

- **Learning rate diferenciado (1e-4 backbone, 1e-3 cabeza):** el backbone ya sabe mucho, así que se toca poco (lr bajo) para no destruir lo aprendido; la cabeza es nueva y debe aprender rápido (lr más alto).
- **Optimizador AdamW:** adaptativo y con *weight decay* (regularización), estándar actual para fine-tuning; converge rápido y estable.
- **CrossEntropy:** función de pérdida natural para clasificación multiclase.
- **Scheduler coseno:** baja el learning rate suavemente a lo largo del entrenamiento, ayudando a afinar al final.
- **Batch 64:** aprovecha la memoria de la GPU T4 sin desbordarla.
- **8 épocas:** la validación se estabiliza alrededor de la época 6-7; más épocas solo aumentarían el sobreajuste.
- **AMP (precisión mixta):** acelera el entrenamiento y usa menos memoria sin perder precisión relevante.

---

## 7. ¿Por qué estas métricas (accuracy y F1 macro)?

- **Accuracy:** proporción de aciertos; intuitiva. Suficiente aquí porque el dataset está **balanceado** (150 imágenes por clase en test).
- **F1 macro:** promedia el F1 de cada clase por igual, sin que las clases grandes dominen. Lo incluimos para detectar si alguna clase va mal aunque la accuracy global se vea bien. Que accuracy y F1 coincidan (0,88) confirma el balance.

**Por qué no solo accuracy:** en un dataset desbalanceado, accuracy puede engañar; F1 macro lo complementa y es buena práctica reportarlo.

---

## 8. ¿Por qué evaluar en datos externos? ¿Qué demostró?

**Qué:** probamos el modelo con 5 fotos externas, ajenas a Food-101.

**Por qué:** el test interno viene del mismo dataset; evaluar con fotos de otra fuente mide la **generalización real**, que es lo que importa en una aplicación de verdad. Es un requisito explícito del profesor.

**Qué demostró:** acertó 3/5 (60%), menos que el 88% interno. Esa caída revela la **brecha de dominio**: fotos con otra iluminación, ángulo y encuadre. Los fallos fueron en imágenes difíciles (platos compuestos / varias comidas a la vez) y ocurrieron con **baja confianza**, lo que indica que el modelo "dudaba" en vez de equivocarse con falsa seguridad.

---

## 9. ¿Por qué la red NO calcula la nutrición?

**Qué:** la red solo clasifica el plato; los macros vienen de una tabla de USDA FoodData Central.

**Por qué:** estimar calorías/porción a partir de una foto es un problema abierto y muy difícil (hay que estimar volumen, porción, ingredientes ocultos). En cambio, una vez identificado el plato, consultar sus macros en una **base de datos oficial** es objetivo y confiable. Así el dato nutricional es verificable y no una invención del modelo.

**Frase clave:** "Separamos las responsabilidades: la red reconoce el plato y una fuente oficial (USDA) aporta los nutrientes. Así el valor nutricional es objetivo y defendible."

---

## 10. Limitaciones (sé honesto, el profe lo valora)

- Entrenamos sobre **un solo dataset**, por eso baja en fotos externas.
- **Sobreajuste leve** desde la época 5.
- La nutrición es **por 100 g**, no estima la porción real de la foto.
- No maneja bien imágenes con **varios platos** a la vez.

## Mejoras futuras

- Entrenar con imágenes de **más dominios/fuentes** para reducir la brecha.
- **Early stopping** y más regularización.
- Probar **EfficientNet-B3** (más capacidad).
- Añadir **estimación de porción** para una nutrición más precisa.

---

## Preguntas rápidas que te pueden hacer (y respuesta corta)

- **¿Qué es una época?** Una pasada completa por todo el conjunto de entrenamiento.
- **¿Qué es un batch?** Un grupo de imágenes que el modelo procesa junto antes de actualizar sus pesos (usamos 64).
- **¿Qué es el F1?** Media armónica entre precisión y recall; penaliza tanto falsos positivos como falsos negativos.
- **¿Qué es overfitting (sobreajuste)?** Cuando el modelo memoriza el entrenamiento y rinde peor en datos nuevos (train sube, validación se estanca).
- **¿Qué es fine-tuning?** Reentrenar un modelo preentrenado ajustando sus pesos a tu tarea específica.
- **¿Por qué la matriz de confusión?** Muestra con qué clases se confunde cada una; aquí los errores se concentran entre platos parecidos (pastas al horno, platos de huevo).
- **¿Cuántos parámetros tiene?** ~4 millones (EfficientNet-B0).
- **¿Qué GPU usaste y cuánto tardó?** Tesla T4 en Kaggle, ~37,6 minutos para 8 épocas.
