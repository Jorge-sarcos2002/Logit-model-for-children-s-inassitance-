# Análisis: asistencia escolar en Los Ríos – ENEMDU 2024

Este repositorio contiene el análisis en **Python** del archivo HTML `Determinantes_deserción_LosRíos (1).html`.  
El objetivo del estudio es modelar la **probabilidad de que un menor de entre 5 y 17 años asista a clases** (variable `p07`) en la **provincia de Los Ríos**, usando datos de la Encuesta Nacional de Empleo, Desempleo y Subempleo (ENEMDU) 2024.  
El enfoque es **cuantitativo y econométrico**, utilizando un **modelo logit** para estimar probabilidades y calcular efectos marginales, complementado con métricas de clasificación para evaluar la capacidad predictiva del modelo.

---

##  Datos y preparación

1. **Fuente:**  
   - Se empleó la base de datos ENEMDU 2024 (personas, anual), la cual contiene variables socioeconómicas, demográficas y laborales de los encuestados.  
   - El archivo original estaba separado por `;` (punto y coma) y se cargó con `pandas`.

2. **Filtro territorial:**  
   - Se filtraron únicamente los registros correspondientes a los **códigos de ciudad de la provincia de Los Ríos**.  
   - El DataFrame resultante (`df_rios`) contiene **6,479 observaciones** y **139 variables**.

3. **Población objetivo:**  
   - De ese subconjunto, se seleccionaron solo los menores con edad (`p03`) entre 5 y 17 años inclusive.  
   - Esto genera el DataFrame `df_menores`, sobre el cual se realiza todo el análisis posterior.

4. **Recodificaciones:**  
   - Varias variables se transformaron de valores categóricos a binarios para que pudieran ser usadas directamente en un modelo logit:  
     - `p07` (asistencia escolar): 1 → 1 (asiste), 2 → 0 (no asiste).  
     - `p20` (trabajó semana pasada): 1 → 1 (sí), 2 → 0 (no).  
     - `area`: 1 → 1 (urbana), 2 → 0 (rural).
   - Estas recodificaciones permiten que el modelo interprete estas variables como dummies.

5. **Revisión de datos faltantes:**  
   - Se comprobó que ni las variables explicativas (`X`) ni la dependiente (`y`) contienen valores faltantes, por lo que no se requirió imputación.

6. **Distribución de la variable dependiente (`p07`):**  
   - 1 (asiste): **1,269 casos**.  
   - 0 (no asiste): **112 casos**.  
   - Esto indica que la gran mayoría de menores asisten a clases, lo cual es relevante para interpretar la precisión y recall del modelo.

---

##  Variables del modelo

- **Dependiente (`y`):**  
  `p07` – indica si el menor asiste a clases (1) o no (0).

- **Explicativas (`X`):**
  - `area`: Zona urbana (1) o rural (0).
  - `p02`: Sexo, codificado como 0 = hombre, 1 = mujer.
  - `p03`: Edad en años.
  - `p04`: Parentesco con el jefe de hogar.
  - `p20`: Trabajó la semana pasada (1 sí, 0 no).
  - `pobreza`: Condición de pobreza por ingresos.
  - `epobreza`: Condición de pobreza extrema por ingresos.

Estas variables recogen factores demográficos, laborales y socioeconómicos que la literatura identifica como potencialmente asociados a la asistencia escolar.

---

##  Modelo estimado

- **Tipo de modelo:**  
  Se utilizó `statsmodels.Logit` con una constante, lo que significa que se estimó un modelo de regresión logística binaria.  
  Este tipo de modelo predice la probabilidad de un evento binario (en este caso, asistir a clases) en función de varias variables explicativas.

- **Resultados clave:**
  - Número de observaciones: **1,377**.
  - Pseudo R² = **0.1650**, lo que indica que el modelo explica una proporción moderada de la variabilidad en la asistencia escolar.
  - **Variables significativas (p<0.05):**
    - `p03` (edad): Coeficiente **−0.1773**, p<0.001 → A mayor edad, menor probabilidad de asistir a clases.
    - `p20` (trabajó semana pasada): Coeficiente **−3.3328**, p<0.001 → Los menores que trabajaron la semana pasada tienen una probabilidad sustancialmente menor de asistir.
  - **Variables no significativas** en este modelo:
    `area`, `p02`, `p04`, `pobreza`, `epobreza`.

---

##  Efectos marginales

- **Interpretación:**  
  Los efectos marginales muestran el cambio promedio en la probabilidad de asistir a clases ante un cambio unitario en la variable explicativa, manteniendo las demás constantes.

- **Resultados principales:**
  - `p03` (edad): **−0.0109** → Cada año adicional reduce la probabilidad de asistir en 1.09 puntos porcentuales.
  - `p20` (trabajó semana pasada): **−0.2058** → Trabajar reduce la probabilidad de asistir en 20.6 puntos porcentuales.
  - El resto de las variables muestran cambios pequeños y no significativos.

---

##  Evaluación del desempeño del modelo

- **Curva ROC y AUC:**
  - El Área Bajo la Curva (AUC) es **0.7584**, lo que indica un buen poder de discriminación entre quienes asisten y quienes no.

- **Selección de umbral óptimo:**
  - En lugar de usar el umbral estándar de 0.5, se calculó el umbral que maximiza el **F1-score** (equilibrio entre precision y recall).
  - Este umbral se determinó usando `precision_recall_curve` de `sklearn`.

- **Métricas finales con el umbral óptimo:**
  - **Precisión (Precision):** 0.9683 → El 96.8% de los que el modelo predice como asistentes realmente lo son.
  - **Cobertura (Recall):** 0.8207 → El modelo identifica correctamente al 82% de los asistentes reales.
  - **F1-score:** 0.8884 → Buen equilibrio entre precisión y recall.

---

##  Visualizaciones generadas

1. **Curva ROC:**  
   Muestra la relación entre TPR (sensibilidad) y FPR (1 - especificidad) para distintos umbrales.
2. **Curva Precision-Recall con F1-score:**  
   Permite visualizar cómo cambian precision, recall y F1-score según el umbral.

---

##  Tecnologías utilizadas

- `pandas` y `numpy` para manipulación y limpieza de datos.
- `statsmodels` para estimar el modelo logit y calcular efectos marginales.
- `scikit-learn` para métricas de clasificación y curvas ROC / PR.
- `matplotlib` para gráficos.

---

> **Nota:** Este análisis refleja únicamente lo realizado en el archivo HTML `Determinantes_deserción_LosRíos (1).html` y no añade pasos externos o inventados. Los resultados están limitados a los datos y variables procesadas en ese documento.
