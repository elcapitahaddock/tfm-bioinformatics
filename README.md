# Identificación y validación de firmas génicas mediante modelos de Machine Learning en datos de expresión génica

## Descripción

Este proyecto forma parte del Trabajo de Fin de Máster (TFM) en Bioinformática y Bioestadística.

El objetivo es identificar firmas génicas candidatas (biomarcadores) mediante modelos de machine learning aplicados a datos de expresión génica, y evaluar su capacidad de generalización en cohortes independientes.

El enfoque combina selección de variables, modelos de clasificación, validación cruzada y validación externa entre datasets y plataformas distintas.


## Datasets utilizados

Se utilizan varios datasets públicos del repositorio GEO:

### GSE2034 (exploración inicial)
- Microarray (Affymetrix)  
- ~286 muestras  
- ~22.000 genes  
- Problema: metástasis sí/no  

Utilizado para construir y validar el pipeline inicial.

---

### GSE55348 (dataset principal de entrenamiento)
- Microarray (Illumina DASL)  
- 53 muestras  
- ~29.000 probes  
- Tipo de muestra: FFPE  
- Subtipo: cáncer de mama HER2+  
- Variable objetivo: recaída (event)

Utilizado para entrenamiento del modelo final y selección de biomarcadores.

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

Carga de datos desde GEO  
Construcción de matriz de expresión (muestras × genes)  
Transformación logarítmica (log2(x + 1))  
Extracción de variable objetivo desde metadata  
Validación cruzada (Stratified K-Fold)  
Pipeline de sklearn con:
- filtrado por varianza  
- normalización (StandardScaler)  
- selección de variables  
- modelo de clasificación  

Evaluación de modelos  
Selección de mejor configuración  
Entrenamiento final en GSE55348  
Extracción de genes mediante regularización L1  
Mapping de probes a genes  
Validación externa en GSE58984  

---

## Modelos utilizados

- Logistic Regression  
- Support Vector Machine (RBF)  
- Random Forest  

---

## Métodos de selección de variables

- Variance Threshold  
- SelectKBest (ANOVA)  
- SelectKBest (Mutual Information)  
- RFE (Recursive Feature Elimination)  
- L1 (Lasso)  

---

## Evaluación

Se utiliza validación cruzada estratificada (5-fold).

Métricas evaluadas:
- ROC-AUC (principal)  
- F1-score  
- Accuracy  

La AUC se utiliza como métrica principal por su capacidad de evaluar el ranking de predicciones y su robustez frente a desbalance de clases.

---

## Validación externa

El modelo final se entrena exclusivamente en GSE55348 y se evalúa en GSE58984 sin reentrenamiento.

Pasos:
- selección de genes en entrenamiento  
- mapping a símbolos génicos en ambos datasets  
- intersección de genes comunes  
- aplicación directa del modelo  

Resultado:
- AUC ≈ 0.38  
- AUC invertido ≈ 0.62  

Esto indica que la señal predictiva se mantiene, pero la dirección del efecto cambia entre datasets.

---

## Limitaciones

- Tamaño muestral reducido en el dataset de entrenamiento  
- Diferencias técnicas entre plataformas  
- Variables objetivo no completamente equivalentes  
- Posible pérdida de información en el mapping de probes a genes  

---

## Reproducibilidad

Pasos para ejecutar el proyecto:

1. Clonar el repositorio  
2. Crear entorno con conda:
   conda env create -f environment.yml  
   conda activate tfm_bio  

3. Ejecutar Jupyter Notebook  
4. Abrir:
   notebooks/01_pipeline_baseline-hipotesisalt.ipynb  

---

## Estructura del proyecto

tfm/  
├── data/  
├── notebooks/  
├── results/  
├── environment.yml  
└── README.md  

---

## Autor

Pedro Valenzuela  
TFM Bioinformática y Bioestadística
