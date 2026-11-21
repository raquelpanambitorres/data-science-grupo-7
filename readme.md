# Proyecto de Data Science - Fase 3: Análisis Predictivo
## Grupo 7

Este repositorio contiene el desarrollo de la Fase 3 del proyecto de Data Science, enfocado en evaluar e identificar el impacto de las condiciones socioeconómicas en la educación y rezago escolar de los niños y jóvenes de Paraguay utilizando técnicas de inferencia estadística y aprendizaje supervisado.

---

## 1. Problema de Investigación
¿De qué manera el nivel socioeconómico de los hogares paraguayos afecta significativamente al nivel educativo y al rezago escolar de los niños y adolescentes?

Existe una brecha educativa histórica vinculada a factores de ingresos, localización geográfica y tipología del hogar. Este proyecto busca cuantificar dichas relaciones y construir un modelo predictivo capaz de identificar de manera temprana a menores en situación de vulnerabilidad educativa (rezago escolar).

---

## 2. Objetivos
* **Objetivo General:** Determinar y predecir el impacto de las condiciones socioeconómicas del hogar sobre el rendimiento y rezago escolar en niños y adolescentes paraguayos (6 a 17 años).
* **Objetivos Específicos:**
  1. Realizar pruebas de hipótesis no paramétricas (Spearman, Kruskal-Wallis, Dunn) para contrastar la correlación entre ingresos y nivel de estudios.
  2. Diseñar un indicador robusto de **Rezago Escolar** que actúe como variable objetivo, neutralizando el sesgo de la edad biológica.
  3. Desarrollar, optimizar y comparar modelos de clasificación supervisada para la detección del riesgo de rezago escolar.
  4. Serializar el modelo e implementar un script automatizado de inferencia.

---

## 3. Datos y Fuente
Los datos provienen de la **Encuesta Permanente de Hogares Continua (EPHC) 2025** de Paraguay.
Se emplearon e integraron tres conjuntos de datos clave mediante fusiones de bases de datos (`inner join`):

* **`INGREFAM` (Datos de Ingresos y Pobreza):** Contiene variables de ingresos familiares y categorización de pobreza total/extrema.
* **`REG01` (Vivienda):** Aporta características físicas del hogar, localización geográfica (departamento, área urbana/rural) y demografía interna.
* **`REG02` (Población):** Proporciona variables individuales como edad, parentesco, idiomas hablados, asistencia escolar y nivel máximo aprobado.

---

## 4. Tecnologías Utilizadas
* **Lenguaje:** Python 3
* **Manipulación de Datos:** `pandas`, `numpy`
* **Visualización:** `matplotlib`, `seaborn`
* **Inferencia Estadística:** `scipy.stats`, `scikit-posthocs`
* **Machine Learning:** `scikit-learn` (Pipelines, ColumnTransformer, HistGradientBoostingClassifier, RandomForestClassifier, GridSearchCV)
* **Serialización:** `joblib`

---

## 5. Instrucciones de Ejecución

### Prerrequisitos
Instala las dependencias necesarias ejecutando:
```bash
pip install pandas numpy matplotlib seaborn scipy scikit-learn scikit-posthocs joblib
```

### Flujo del Cuaderno
1. **Fase 1 (Limpieza e Integración):** Se descargan los datos directamente desde Google Drive y se realiza el casteo y renombrado de variables.
2. **Fase 2 (Inferencia Estadística):** Se validan supuestos de normalidad y se corren las pruebas de hipótesis.
3. **Fase 3 (Modelado Predictivo):**
   * Se ejecuta la celda que genera el dataset procesado `jovenes_ml`.
   * Se entrenan y comparan los algoritmos (`Dummy`, `LogisticRegression`, `RandomForest`, `HistGradientBoosting`).
   * Se ejecuta `GridSearchCV` para optimizar hiperparámetros.
   * Se genera localmente el modelo serializado `modelo_rezago_escolar.joblib` y el script funcional `inferencia.py`.

### Uso del Script de Inferencia (`inferencia.py`)
Una vez generado el script, puedes predecir nuevos datos en formato CSV llamando al método:
```python
from inferencia import cargar_y_predecir
resultados = cargar_y_predecir('tus_nuevos_datos.csv')
print(resultados[['edad', 'probabilidad_rezago', 'prediccion_rezago']])
```

---

## 6. Síntesis de Resultados

### Análisis Estadístico (Fase 2)
* El análisis bivariado simple arrojó un coeficiente de Spearman cercano a cero (`0.0057`, $p > 0.05$) debido a que la edad biológica actúa como variable de confusión en menores (avanzan de grado principalmente al cumplir años).
* La prueba de Kruskal-Wallis y post-hoc de Dunn demostraron que no hay diferencia significativa del grado alcanzado entre categorías de pobreza si no se segmenta o controla el análisis por edad.

### Análisis Predictivo de Machine Learning (Fase 3)
* **Definición del Target:** Se construyó la métrica de **rezago escolar** comparando la edad teórica de ingreso escolar contra los años de estudio reales aprobados.
* **Desempeño de Modelos:**
  * **Dummy Classifier (Línea Base):** ROC-AUC de `0.50`.
  * **Modelo Campeón (Gradient Boosting Optimizado):** Logró un **ROC-AUC del 0.91** y un **Accuracy del 82.4%**, con una excelente capacidad de generalización en validación cruzada.
* **Importancia de Variables:** El modelo determinó que la asistencia escolar actual (`asiste_educ_formal_actual`), la edad biológica (acumulación de rezago en adolescentes) y el ingreso familiar mensual (`ing_familiar`) son los tres factores más decisivos para predecir el riesgo educativo.