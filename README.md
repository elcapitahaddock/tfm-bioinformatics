# Comparación de métodos de selección de biomarcadores y clasificación en datos de expresión génica mediante Python

## Descripción

Este proyecto forma parte del Trabajo de Fin de Máster (TFM) en Bioinformática y Bioestadística.

El objetivo es comparar distintos métodos de selección de variables (biomarcadores) y modelos de clasificación aplicados a datos de expresión génica, evaluando su rendimiento y capacidad de generalización en cohortes independientes.

El enfoque combina selección de variables, modelos de clasificación, validación cruzada y validación externa entre datasets y plataformas distintas.

---

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
2. Construcción de matriz de expresión (muestras × genes)  
3. Transformación logarítmica (log2(x + 1))  
4. Extracción de variable objetivo desde metadata  
5. Validación cruzada (Stratified K-Fold)  
6. Definición de pipelines de sklearn con:
   - filtrado por varianza  
   - normalización (StandardScaler)  
   - selección de variables  
   - modelo de clasificación  
7. Comparación sistemática de combinaciones de:
   - métodos de selección de variables  
   - modelos de clasificación  
8. Evaluación de modelos  
9. Selección de la mejor configuración  
10. Entrenamiento final en GSE55348  
11. Mapping de probes a genes  
12. Validación externa en GSE58984  

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

Se comparan distintas combinaciones de métodos de selección y modelos.  
El modelo final seleccionado utiliza **ANOVA (SelectKBest) + Logistic Regression**.

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

- AUC ≈ 0.65  

El modelo muestra capacidad predictiva moderada en validación externa, a pesar de las diferencias entre plataformas y cohortes.

---

## Consideraciones clínicas

En problemas de cáncer es crítico minimizar los falsos negativos (pacientes enfermos clasificados como sanos).

Por ello, además de la AUC, se analizan sensibilidad y especificidad para distintos thresholds de decisión. Se observa el compromiso entre ambas métricas:

- thresholds bajos → mayor sensibilidad  
- thresholds altos → mayor especificidad  

Este análisis permite interpretar el modelo en un contexto clínico real.

---

## Limitaciones

- Tamaño muestral reducido en el dataset de entrenamiento  
- Diferencias técnicas entre plataformas (Illumina vs Affymetrix)  
- Variables objetivo no completamente equivalentes entre datasets  
- Pérdida de información en el mapping de probes a genes  
- Posibles problemas de calibración del modelo en validación externa  

---

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

Estructura del proyecto
tfm-bioinformatics/
├── Pipeline.ipynb
├── README.md
├── environment.yml

Autor

Pedro Valenzuela
TFM Bioinformática y Bioestadística
```
