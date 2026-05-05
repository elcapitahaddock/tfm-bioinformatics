# Comparación de métodos de selección de biomarcadores y clasificación en datos de expresión génica mediante Python

## Descripción

Este proyecto forma parte del Trabajo de Fin de Máster (TFM) en Bioinformática y Bioestadística.

El objetivo es comparar distintos métodos de selección de variables (biomarcadores) y modelos de clasificación aplicados a datos de expresión génica, evaluando su rendimiento, estabilidad y capacidad de generalización en cohortes independientes.

El trabajo se centra en un problema típico de alta dimensionalidad (p >> n), donde el número de genes es muy superior al número de muestras, lo que dificulta la construcción de modelos robustos y aumenta el riesgo de sobreajuste.

El enfoque combina selección de variables, modelos de clasificación, validación cruzada y validación externa entre datasets y plataformas distintas.

---

## Datasets utilizados

Se utilizan varios datasets públicos del repositorio GEO:

### GSE2034 (exploración inicial)

- Microarray (Affymetrix)
- ~286 muestras
- ~22.000 genes
- Problema: metástasis / recaída según configuración clínica

Utilizado para pruebas iniciales y exploración del pipeline.

---

### GSE55348 (dataset principal de entrenamiento)

- Microarray (Illumina DASL)
- 53 muestras
- ~29.000 probes
- Tipo de muestra: FFPE
- Subtipo: cáncer de mama HER2+
- Variable objetivo: recaída (event)

Utilizado para comparar métodos y entrenar el modelo final.

---

### GSE58984 (validación externa)

- Microarray (Affymetrix)
- 94 muestras
- ~54.000 probes (~23.000 genes tras mapping)
- Tipo de muestra: tejido fresco
- Subtipo: cáncer de mama HER2+
- Variable objetivo: metástasis a distancia

Utilizado para validación externa del modelo sin reentrenamiento.

---

## Pipeline

El flujo de trabajo es el siguiente:

1. Carga de datos desde GEO
2. Construcción de matriz de expresión (muestras × genes/probes)
3. Transformación logarítmica (log2(x + 1))
4. Extracción de variable objetivo desde metadata
5. Validación cruzada estratificada (Stratified K-Fold)
6. Definición de pipelines de sklearn con:
   - filtrado por varianza
   - normalización (StandardScaler)
   - selección de variables
   - modelo de clasificación
7. Comparación sistemática de métodos de selección de variables
8. Evaluación mediante métricas predictivas y clínicas
9. Selección de la mejor configuración
10. Entrenamiento final en GSE55348
11. Selección de biomarcadores mediante regularización L1
12. Análisis de estabilidad de la selección de variables
13. Mapping de probes a genes
14. Validación externa en GSE58984

Todas las transformaciones se integran dentro de pipelines de sklearn para evitar fugas de información (data leakage) durante la validación cruzada.

---

## Modelos utilizados

- Logistic Regression
- Random Forest

Se prioriza la regresión logística por su interpretabilidad y buen comportamiento en datasets de tamaño reducido.

---

## Métodos de selección de variables

- Variance Threshold
- SelectKBest (ANOVA)
- SelectKBest (Mutual Information)
- RFE (Recursive Feature Elimination)
- Regularización L1 (Lasso)

Se comparan distintas estrategias de selección de variables bajo un mismo marco experimental.

El modelo final seleccionado utiliza **Logistic Regression con regularización L1**, ya que permite obtener una firma génica reducida e interpretable mediante coeficientes no nulos.

---

## Evaluación

Se utiliza validación cruzada estratificada (5-fold).

Métricas evaluadas:

- ROC-AUC
- F1-score
- Accuracy
- Sensibilidad
- Especificidad
- Precision
- Balanced accuracy
- Matthews Correlation Coefficient (MCC)
- Falsos negativos (FN)

La AUC se utiliza como métrica principal para evaluar la capacidad de ranking del modelo. Además, se analizan métricas clínicas como sensibilidad, especificidad y falsos negativos, especialmente relevantes en un contexto biomédico.

---

## Selección de biomarcadores

Tras la comparación de modelos, se entrena el modelo final sobre el dataset principal completo (GSE55348) para obtener una firma génica candidata.

La selección se realiza mediante regularización L1:

- se eliminan probes de baja varianza
- se normalizan las variables
- se entrena una regresión logística penalizada
- se seleccionan probes con coeficientes distintos de cero
- se realiza mapping de probes a símbolos génicos
- se analiza la estabilidad de selección mediante folds

La estabilidad se evalúa contando cuántas veces aparece cada gen en las distintas particiones de validación cruzada.

---

## Validación externa

El modelo final se entrena exclusivamente en GSE55348 y se evalúa en GSE58984 sin reentrenamiento.

Pasos:

- selección de genes en entrenamiento
- mapping a símbolos génicos en ambos datasets
- intersección de genes comunes
- alineación del orden de columnas
- escalado ajustado solo en training
- aplicación directa del modelo sobre el dataset externo

La validación externa permite evaluar la capacidad de generalización en una cohorte independiente.

Durante este proceso se observan limitaciones importantes relacionadas con diferencias entre datasets, plataformas y escalas de expresión, lo que pone de manifiesto la dificultad de transferir firmas génicas entre cohortes independientes.

---

## Consideraciones clínicas

En problemas de cáncer es crítico minimizar los falsos negativos, es decir, pacientes con evento clínico clasificados como negativos.

Por ello, además de la AUC, se analizan sensibilidad y especificidad para distintos thresholds de decisión. Se observa el compromiso entre ambas métricas:

- thresholds bajos → mayor sensibilidad
- thresholds altos → mayor especificidad

Este análisis permite interpretar el modelo en un contexto clínico más realista.

---

## Limitaciones

- Tamaño muestral reducido en el dataset de entrenamiento
- Alta dimensionalidad (p >> n)
- Diferencias técnicas entre plataformas (Illumina vs Affymetrix)
- Heterogeneidad biológica entre cohortes
- Variables objetivo no completamente equivalentes entre datasets
- Pérdida de información en el mapping de probes a genes
- Posibles problemas de calibración del modelo en validación externa
- Dificultad de generalización entre datasets independientes

---

## Reproducibilidad

Pasos para ejecutar el proyecto:

```bash
git clone https://github.com/elcapitahaddock/tfm-bioinformatics.git
cd tfm-bioinformatics

conda env create -f environment.yml
conda activate tfm_bio

jupyter notebook


## Reproducibilidad

Pasos para ejecutar el proyecto:

```bash
git clone https://github.com/elcapitahaddock/tfm-bioinformatics.git
cd tfm-bioinformatics

conda env create -f environment.yml
conda activate tfm_bio

jupyter notebook

Abrir:
Pipeline.ipynb

tfm-bioinformatics/
├── Pipeline.ipynb
├── README.md
├── environment.yml

Autor

Pedro Valenzuela
TFM Bioinformática y Bioestadística
```
