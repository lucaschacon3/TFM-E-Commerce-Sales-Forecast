# E-Commerce Sales Forecast: Análisis Predictivo y Segmentación de Clientes

Pipeline de Data Science aplicado al dataset transaccional de Kaggle "E-Commerce Sales Forecast" (UK retailer, 2010-2011). El flujo cubre limpieza de datos, predicción de ventas diarias con modelos de regresión y segmentación de clientes mediante clustering.

---

## Dataset

**Origen:** [Kaggle E-Commerce Sales Forecast](https://www.kaggle.com/datasets/chirag19/online-sales)

- **Transacciones originales:** 541,909
- **Transacciones tras limpieza:** 392,693
- **Periodo:** Dic 2010 – Dic 2011
- **Clientes únicos:** 4,338

---

## Pipeline

### 01.preprocesado_datos
Limpieza: eliminación de filas sin `CustomerID`, cancelaciones (cantidades negativas), y creación de variables derivadas (`TotalVenta`, `Fecha`, `Mes`, `DiaSemana`). Se genera `dataset_limpio.csv`.

### 02.regresion_prediccion_ventas
Predicción de ventas diarias con ventana deslizante de 7 días:

- **Features:** `lag_1`, `lag_7`, `media_7` + one-hot encoding del día de la semana
- **División temporal:** Train (hasta Sep 2011) / Val (Oct 2011) / Test (Nov-Dic 2011)
- **Modelos:** RandomForest, XGBoost, LightGBM (parámetros por defecto)
- **LightGBM** seleccionado como mejor modelo

#### Métricas en validación (Oct 2011)

| Modelo | MAE | RMSE |
|---|---|---|
| RandomForest | 12,016 | 15,474 |
| XGBoost | 13,875 | 18,734 |
| **LightGBM** | **11,079** | **15,397** |

### 03.segmentacion_clientes
Segmentación basada en comportamiento de compra:

- **Variables por cliente:** TotalGastado, NumCompras, ProductosDistintos, TicketMedio
- **Outliers:** Isolation Forest (5% de contaminación → 217 eliminados)
- **Escalado:** RobustScaler
- **Reducción:** PCA (1 componente, 62.63% varianza explicada)
- **Clustering:** K-Means (k=3 según método del codo)
- **Silhouette Score:** 0.6184

#### Perfiles de cliente

| Segmento | Clientes | Gasto medio | Compras | Productos |
|---|---|---|---|---|
| VIP / Premium | 402 | ~4,179 € | ~10 | ~145 |
| Recurrentes | 1,132 | ~1,624 € | ~4.6 | ~80 |
| Ocasionales | 2,587 | ~408 € | ~1.7 | ~26 |

---

## Stack Tecnológico

- **Lenguaje:** Python 3.12
- **Entorno:** Jupyter Notebook / VS Code
- **Librerías:** pandas, numpy, scikit-learn, xgboost, lightgbm, matplotlib, seaborn, ydata-profiling

---

## Instalación y Uso

```bash
git clone https://github.com/lucaschacon3/TFM-E-Commerce-Sales-Forecast.git
cd TFM-E-Commerce-Sales-Forecast
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Ejecutar los notebooks en orden numérico: `01` → `02` → `03`.

---

## Power BI

Dos dashboards para visualización de resultados:

- **Dashboard de Regresión**: Comparativa de ventas reales vs predicciones, evolución temporal y errores por modelo.
- **Dashboard de Segmentación**: Distribución de clientes por clúster, perfiles de gasto/frecuencia y análisis por segmento (VIP, Recurrente, Ocasional).

---

## Salidas Generadas

- `dataset_limpio.csv` — datos preprocesados
- `ventas_{Modelo}.csv` — predicciones diarias por modelo (Nov-Dic 2011)
- `clientes_con_cluster.csv` — clientes con segmento asignado
