# Pre-entrega M10 — Modelado y evaluación de Churn

Este proyecto entrena un modelo de clasificación para identificar clientes de telecomunicaciones con riesgo de abandonar el servicio. El notebook separa los datos en entrenamiento y prueba, calcula las métricas solicitadas y explica por qué es importante detectar falsos negativos.

## Dataset

- **Nombre:** Telco Customer Churn.
- **Archivo local:** `data/WA_Fn-UseC_-Telco-Customer-Churn.csv`.
- **Filas y columnas:** 7043 clientes y 21 columnas.
- **Objetivo:** `Churn` (`Yes` si el cliente abandonó; `No` si permanece).
- **Fuente:** [Kaggle — Telco Customer Churn, BlastChar](https://www.kaggle.com/datasets/blastchar/telco-customer-churn). El CSV incluido es una copia local del dataset.

`customerID` se excluye porque identifica al cliente, pero no describe su comportamiento. `TotalCharges` se convierte a número antes de construir el preprocesamiento; las celdas vacías resultantes se imputan dentro del pipeline.

## Ejecutar el notebook

Desde esta carpeta, en Windows:

```powershell
py -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe -m notebook
```

En macOS o Linux:

```bash
python3 -m venv .venv
.venv/bin/python -m pip install -r requirements.txt
.venv/bin/python -m notebook
```

En Jupyter, abre `modelado_metricas.ipynb` y ejecuta **Run All** desde la primera celda. También se puede comprobar la ejecución completa con:

```powershell
.\.venv\Scripts\python.exe -m nbconvert --to notebook --execute --inplace modelado_metricas.ipynb
```

## Modelo y evaluación

Se utiliza Regresión Logística con pesos balanceados para no ignorar la clase minoritaria. El split es 80/20, estratificado y reproducible (`random_state=42`). Las transformaciones de variables numéricas y categóricas se ajustan dentro de un pipeline y solo con los datos de entrenamiento.

El notebook calcula accuracy, precision, recall, F1 y matriz de confusión sobre el conjunto de prueba. También muestra los resultados concretos de la ejecución y una interpretación de los errores. La **métrica prioritaria para retención es Recall de `Churn=Yes`**: un falso negativo es un cliente que sí abandona, pero que el modelo no identificó para una posible acción de retención. Un mayor recall puede aumentar los falsos positivos —contactar clientes que iban a permanecer—, así que la decisión implica comparar el costo de ambas clases de error.

### Resultados de esta ejecución

Con la partición estratificada (5634 registros de entrenamiento y 1409 de prueba), la Regresión Logística obtuvo:

| Métrica | Resultado |
|---|---:|
| Accuracy | 0.738 |
| Precision (`Churn=Yes`) | 0.504 |
| Recall (`Churn=Yes`) | 0.783 |
| F1-Score (`Churn=Yes`) | 0.614 |

La matriz de confusión fue `[[747, 288], [81, 293]]` (filas reales `No/Yes`, columnas predichas `No/Yes`). El modelo dejó sin detectar **81** clientes que abandonaron (falsos negativos) y señaló a **288** clientes que permanecieron (falsos positivos). Estos resultados muestran el intercambio entre captar más clientes en riesgo y el costo de ofrecer acciones de retención innecesarias.

## Estructura

```text
pre-entregamd10/
├── consignamd10.md
├── data/
│   └── WA_Fn-UseC_-Telco-Customer-Churn.csv
├── modelado_metricas.ipynb
├── requirements.txt
├── README.md
└── .gitignore
```
