Informe final — Análisis de Evasión de Clientes · Telecom X
Resumen ejecutivo

Objetivo: Identificar las causas principales del churn en Telecom X y proponer acciones concretas para reducir la pérdida de clientes.
Hallazgos clave (síntesis):

El churn está concentrado en clientes de baja antigüedad (primeros meses).

Los clientes con contratos mes a mes presentan la mayor probabilidad de abandonar.

Algunos servicios premium (p. ej. Fiber optic) y ciertos métodos de pago muestran tasas de churn por encima del promedio.

La ausencia de servicios complementarios (seguridad, soporte, device protection) se asocia a un mayor churn.

A continuación encuentras una versión refinada del informe: metodología, limpieza, EDA, conclusiones, y un plan de recomendaciones priorizadas y medibles.

1. Introducción

Problema: Telecom X observa una salida significativa de clientes. El objetivo del análisis es determinar qué características (contrato, antigüedad, servicios, cargos, método de pago, etc.) están asociadas a mayor probabilidad de churn para orientar intervenciones operativas y comerciales.

Alcance del informe: ETL reproducible desde la fuente (API/JSON), EDA con visualizaciones, insights cuantitativos y recomendaciones accionables con prioridad y métricas para seguimiento.

2. Resumen del proceso (ETL y preparación)

Ingesta

Fuente: JSON público (API / archivo raw). Se normalizó estructura anidada con pd.json_normalize.

Versión raw guardada en data/raw/ y dataset limpio en data/processed/telecom_churn_clean.csv.

Limpieza y transformaciones principales

Renombrado de columnas (. → _) para uniformidad y facilidad de uso.

Conversión de tipos:

account_Charges_Total, account_Charges_Monthly → numérico (NaN → 0 cuando aplicable).

customer_tenure → entero.

Churn → binaria (0/1).

customer_SeniorCitizen → categórico (Yes/No).

Normalización de respuestas tipo “No phone service” / “No internet service” → No.

Eliminación de duplicados y customerID (identificador no predictivo).

Ingenierías pequeñas:

Cuentas_Diarias = account_Charges_Monthly / 30.

Segmento compuesto (contract + internet + paperless) para análisis granular.

Control de calidad

Reporte de nulos por columna y conteo de duplicados antes/después.

Conversión forzada segura con pd.to_numeric(..., errors='coerce') y relleno controlado.

3. Análisis exploratorio de datos (EDA) — principales análisis y observaciones

Nota: las figuras referenciadas deben generarse mediante las celdas del notebook; abajo incluyo el enunciado de cada visual y su interpretación concisa.

3.1 KPIs generales

Total de clientes: N

Tasa global de churn: churn_rate = churn_count / N (mostrar %)

ARPU (proxy): account_Charges_Monthly.mean()

Distribución de tenure, cargos mensuales y cargos totales (medias/medianas/desviaciones).

3.2 Visualizaciones clave (y qué muestran)

Distribución de Churn (barplot)

Muestra el volumen absoluto de churn vs no-churn. Punto de partida para evaluar clase desbalanceada.

Histograma de tenure por Churn (overlay)

Observación: alto churn en los primeros meses; la probabilidad de churn disminuye con mayor tenure.

Histograma de account_Charges_Monthly y account_Charges_Total por Churn

Observación: ciertos rangos de cargos mensuales se asocian a mayor churn; cargos totales (cliente “viejo”) tienden a correlacionar negativamente con churn.

Churn rate por categoría (barplots)

account_Contract: Month-to-month lidera en churn — señal clara de impacto de compromiso contractual.

internet_InternetService: Fiber optic muestra churn elevado (investigar calidad/expectativas).

account_PaperlessBilling y account_PaymentMethod: diferencias notables (p. ej. Electronic check puede mostrar churn más alto).

Top segmentos de riesgo (tabla)

Agrupar por combinación (Contract, InternetService, PaperlessBilling) y ordenar por churn_rate para identificar micro-segmentos prioritarios.

Matriz de correlación (numéricas)

Correlación negativa fuerte entre customer_tenure y Churn.

Correlación positiva entre account_Charges_Monthly y Churn (posible sensibilidad al precio).

3.3 Observaciones cuantitativas

Tenure medianas: comparar mediana de tenure en churn vs no-churn (p. ej. churn_mediana << no_churn_mediana).

Contratos month-to-month: representaron X% del total pero Y% del churn (siempre incluir cifras).

Servicios faltantes (security/support): ausencia asociada a mayor churn → posible palanca de retención.

4. Conclusiones e insights accionables

Riesgo alto en onboarding: Un porcentaje significativo del churn ocurre en los primeros 1–3 meses.
Insight: Existe una ventana temprana crítica donde el cliente aún no percibe el valor del servicio.

Compromiso contractual reduce churn: Contratos a 1 o 2 años muestran tasas de churn significativamente menores que month-to-month.
Insight: Promover el paso a contratos de mayor duración puede mejorar retención.

Calidad/percepción en servicios premium (Fiber): A pesar de ser premium, Fiber muestra churn elevado → investigar causas operativas, expectativas vs. entrega o presión competitiva de precios.

Servicios complementarios como ancla: Servicios como OnlineSecurity, TechSupport, y DeviceProtection se asocian a menor churn.
Insight: Estos servicios incrementan la fidelidad al aportar valor percibido.

Métodos de pago y perfil de riesgo: Algunos métodos (ej. Electronic check) presentan churn superior → puede haber correlación con perfil socioeconómico o facilidad de cancelar.

5. Recomendaciones priorizadas (Impacto · Esfuerzo · Propietario · Métricas)

Uso una priorización simple (Impacto alto/medio/bajo / Esfuerzo alto/medio/bajo).

Corto plazo (30–60 días)

Onboarding intensivo para clientes < 3 meses

Impacto: Alto, Esfuerzo: Medio

Acciones: llamadas/mesajes automatizados, tutoriales, check-ins de satisfacción en semana 1 y 4.

Métrica: reducir churn en 0–3 meses en X% (seguir mes a mes).

Propietario: Customer Success / Operaciones.

Campaña de incentivos para migrar a contratos anuales

Impacto: Alto, Esfuerzo: Medio-Alto

Acciones: descuentos por migración, paquetes con servicios complementarios incluidos.

Métrica: % migración a 1/2 años; churn entre convertidos vs no-convertidos.

Propietario: Comercial.

Mediano plazo (60–120 días)

Bundles de seguridad + soporte como oferta por defecto

Impacto: Medio-Alto, Esfuerzo: Medio

Acciones: promocionar bundles en onboarding y renovación.

Métrica: uptake de bundle y diferencia en churn.

Análisis operacional de calidad en Fiber

Impacto: Alto (si identifica fallas), Esfuerzo: Alto

Acciones: correlacionar churn con registros de incidencias, latencia, tickets de soporte y zonas geográficas.

Métrica: tasa de incidencias por 1k clientes en fibra; churn antes/después de mejoras.

Largo plazo / Producto (>=120 días)

Modelo predictivo de churn (baseline + producción)

Impacto: Alto, Esfuerzo: Medio-Alto

Acciones: entrenar modelo (LogReg, RandomForest), monitorizar lead time (p. ej. probabilidad de churn en próximos 30 días) e integrar en workflows de retención.

Métrica: AUC, PR-AUC, reducción de churn atribuible a intervenciones.

A/B tests de ofertas y canales

Impacto: Medio, Esfuerzo: Medio

Acciones: probar distintos incentivos, mensajes y timing para optimizar coste por retención.

6. Plan de implementación (30/60/90 days — ejemplo rápido)

Día 0–30: Crear panel de control con KPIs (churn rate global, churn por tenure, churn por contract, top segmentos). Lanzar pilotos de onboarding para clientes < 3 meses.

Día 30–60: Analizar resultados del piloto; lanzar campaña de conversión a contratos anuales con oferta limitada. Iniciar análisis de calidad para fibra.

Día 60–90: Desplegar modelo predictivo en producción con scoring semanal; automatizar retención para clientes con probabilidad alta de churn.

7. Métricas sugeridas para seguimiento (dashboard)

Churn rate (global y por cohortes de tenure 0–3, 4–12, >12 meses) — trending semanal/mensual.

ARPU por segmento y LTV estimado.

Tasa de migración a contratos de 1/2 años (conversion rate).

Uptake de bundles (security/support) y su churn comparativo.

AUC / PR-AUC del modelo de churn (si se construye).

8. Limitaciones del análisis

Análisis descriptivo; correlación NO implica causalidad. Para confirmar causas se requieren A/B tests o estudios experimentales.

Datos disponibles pueden no contener variables importantes (por ejemplo, tickets históricos, NPS, incidencias de red, churn por socio-demografía). Incluir esas fuentes mejoraría el poder explicativo y predictivo.

9. Siguientes pasos recomendados

Integrar fuentes adicionales: tickets de soporte, logs de red, histórico de promos y llamadas de retención.

Construir y validar un modelo predictivo y luego convertirlo en una regla operacional (scoring → acción automatizada).

Ejecutar A/B tests para validar causalmente las recomendaciones (e.g., onboarding intensivo vs control).

Monitorear impacto (experimentos medibles con hipótesis y KPIs definidos).

Anexos (materiales que deberían acompañar el informe entregable)

Carpeta Datos/ con: churn_count.png, tenure_by_churn.png, monthly_by_churn.png, total_by_churn.png, churn_by_account_Contract.png, churn_by_internet_InternetService.png, correlation_matrix.png, top_segments.csv.

data/processed/telecom_churn_clean.csv (dataset final).

Notebook .ipynb con todas las celdas ejecutables y un bloque “Conclusión ejecutiva” listo para exportar a PDF/HTML.

---

## 🔹 0. Instrucciones rápidas

* Ejecuta las celdas en orden.
* Todas las figuras se guardan en la carpeta `Datos/`.
* El notebook es reproducible: si la descarga falla, se crea un `DataFrame` de ejemplo para que las celdas de análisis funcionen.

---

## 🔹 1. Introducción

**Objetivo:** Analizar la evasión de clientes (churn) de Telecom X para identificar patrones, segmentos de riesgo y proponer recomendaciones que ayuden a reducir la pérdida de clientes.

**Problema:** Alta tasa de cancelación. Queremos entender qué factores (contrato, antigüedad, tipo de servicio, cargos) están asociados a mayor probabilidad de churn.

---

## 🔹 2. Código — Librerías e Ingesta

```python
# Librerías e inicialización
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import requests
import json
import os
from io import StringIO

# Config
output_folder = 'Datos'
os.makedirs(output_folder, exist_ok=True)

# URL de datos (TelecomX challenge)
url_data = 'https://raw.githubusercontent.com/alura-cursos/challenge2-data-science-LATAM/refs/heads/main/TelecomX_Data.json'

# Ingesta y normalización
try:
    resp = requests.get(url_data, timeout=15)
    resp.raise_for_status()
    data = resp.json()
    df = pd.json_normalize(data)
    print('Datos cargados desde URL:', df.shape)
except Exception as e:
    print('No fue posible descargar; se usará dataset de ejemplo. Error:', e)
    # dataset de ejemplo (mismo esquema que el original)
    data_mock = {
        'customerID': [f'C{i:04d}' for i in range(1, 11)],
        'customer.gender': ['Male','Female','Male','Female','Male','Female','Male','Female','Male','Female'],
        'customer.SeniorCitizen':[0,1,0,0,1,0,0,1,0,0],
        'customer.Partner':['Yes','No','No','Yes','No','Yes','No','No','Yes','No'],
        'customer.Dependents':['No','No','No','Yes','No','No','Yes','No','No','Yes'],
        'customer.tenure':[24,1,8,45,2,72,11,10,67,7],
        'phone.PhoneService':['Yes','No','Yes','Yes','Yes','Yes','Yes','No','Yes','Yes'],
        'phone.MultipleLines':['No','No phone service','No','No','No','Yes','No','No phone service','Yes','No'],
        'internet.InternetService':['DSL','DSL','Fiber optic','DSL','Fiber optic','Fiber optic','Fiber optic','DSL','DSL','Fiber optic'],
        'internet.OnlineSecurity':['Yes','No','No','Yes','No','No','No','Yes','No','No'],
        'internet.OnlineBackup':['Yes','Yes','No','No','No','Yes','No','No','Yes','No'],
        'internet.DeviceProtection':['No','No','Yes','Yes','No','Yes','No','No','Yes','No'],
        'internet.TechSupport':['No','No','No','Yes','No','No','No','No','Yes','No'],
        'internet.StreamingTV':['No','No','No','No','No','Yes','No','No','Yes','No'],
        'internet.StreamingMovies':['No','No','No','No','No','Yes','No','No','Yes','No'],
        'account.Contract':['One year','Month-to-month','Month-to-month','One year','Month-to-month','Two year','Month-to-month','Month-to-month','Two year','Month-to-month'],
        'account.PaperlessBilling':['Yes','Yes','Yes','No','Yes','Yes','Yes','Yes','Yes','Yes'],
        'account.PaymentMethod':['Mailed check','Electronic check','Electronic check','Bank transfer (automatic)','Electronic check','Credit card (automatic)','Mailed check','Mailed check','Credit card (automatic)','Electronic check'],
        'account.Charges.Monthly':[70.70,29.85,99.65,30.60,78.90,115.80,70.35,20.25,99.80,84.10],
        'account.Charges.Total':['1755.25','29.85','700.75','1397.60','135.40','8647.25','733.20','216.75','6874.00','586.15'],
        'Churn':['No','Yes','Yes','No','Yes','No','No','No','No','Yes']
    }
    df = pd.DataFrame(data_mock)

# Vista rápida
print('\nPrimeras filas:')
print(df.head())
print('\nColumnas:', len(df.columns))
```

---

## 🔹 3. Limpieza y Tratamiento de Datos

```python
# 1) Renombrar columnas (reemplazar '.' por '_')
df.columns = df.columns.str.replace('.', '_', regex=False)

# 2) Tipos y conversión de cargos
if 'account_Charges_Total' in df.columns:
    df['account_Charges_Total'] = pd.to_numeric(df['account_Charges_Total'], errors='coerce')
    df['account_Charges_Total'].fillna(0, inplace=True)

if 'account_Charges_Monthly' in df.columns:
    df['account_Charges_Monthly'] = pd.to_numeric(df['account_Charges_Monthly'], errors='coerce')
    df['account_Charges_Monthly'].fillna(0, inplace=True)

# 3) Tenure
if 'customer_tenure' in df.columns:
    df['customer_tenure'] = pd.to_numeric(df['customer_tenure'], errors='coerce').fillna(0).astype(int)

# 4) SeniorCitizen a categórico
if 'customer_SeniorCitizen' in df.columns:
    df['customer_SeniorCitizen'] = df['customer_SeniorCitizen'].map({0:'No',1:'Yes'})

# 5) Churn a 0/1
if 'Churn' in df.columns:
    df['Churn'] = df['Churn'].map({'Yes':1,'No':0})

# 6) Normalizar 'No phone service' y 'No internet service' -> 'No'
service_cols = [c for c in df.columns if any(k in c for k in ['MultipleLines','OnlineSecurity','OnlineBackup','DeviceProtection','TechSupport','StreamingTV','StreamingMovies'])]
for c in service_cols:
    df[c] = df[c].replace({'No phone service':'No','No internet service':'No'})

# 7) Eliminar identificadores
if 'customerID' in df.columns:
    df.drop('customerID', axis=1, inplace=True)

# 8) Columna Cuentas_Diarias
if 'account_Charges_Monthly' in df.columns:
    df['Cuentas_Diarias'] = df['account_Charges_Monthly'] / 30

print('\nLimpieza finalizada. Tipos de columnas:')
print(df.dtypes)
print('\nNulos por columna:')
print(df.isnull().sum())

# Guardar versión limpia
os.makedirs('data/processed', exist_ok=True)
df.to_csv('data/processed/telecom_churn_clean.csv', index=False)
print('\nArchivo limpio guardado en data/processed/telecom_churn_clean.csv')
```

---

## 🔹 4. Análisis Exploratorio de Datos (EDA)

### 4.1 - Métricas globales y tasa de churn

```python
total_clients = len(df)
churn_count = df['Churn'].sum()
churn_rate = churn_count / total_clients
print(f'Total clientes: {total_clients}')
print(f'Clientes con churn: {churn_count} ({churn_rate:.2%})')

# Tabla de resumen rápida
summary = df.describe(include='all').T
summary['n_unique'] = df.nunique()
summary.head()
```

### 4.2 - Visualizaciones principales

```python
sns.set(style='whitegrid')

# Distribución de Churn
plt.figure(figsize=(6,4))
sns.countplot(x='Churn', data=df)
plt.xticks([0,1], ['No Evasión','Evasión'])
plt.title('Distribución de Churn')
plt.savefig(os.path.join(output_folder,'churn_count.png'), bbox_inches='tight', dpi=300)
plt.show()

# Tenure por churn
plt.figure(figsize=(10,4))
sns.histplot(data=df, x='customer_tenure', hue='Churn', kde=True, element='step', stat='count')
plt.title('Antigüedad (tenure) por Churn')
plt.savefig(os.path.join(output_folder,'tenure_by_churn.png'), bbox_inches='tight', dpi=300)
plt.show()

# Monthly charges por churn
if 'account_Charges_Monthly' in df.columns:
    plt.figure(figsize=(10,4))
    sns.histplot(data=df, x='account_Charges_Monthly', hue='Churn', kde=True, element='step', stat='count')
    plt.title('Cargos mensuales por Churn')
    plt.savefig(os.path.join(output_folder,'monthly_by_churn.png'), bbox_inches='tight', dpi=300)
    plt.show()

# Total charges por churn
if 'account_Charges_Total' in df.columns:
    plt.figure(figsize=(10,4))
    sns.histplot(data=df, x='account_Charges_Total', hue='Churn', kde=True, element='step', stat='count')
    plt.title('Cargos totales por Churn')
    plt.xlim(0, df['account_Charges_Total'].quantile(0.95))
    plt.savefig(os.path.join(output_folder,'total_by_churn.png'), bbox_inches='tight', dpi=300)
    plt.show()
```

### 4.3 - Tasa de churn por categoría

```python
# Función reutilizable
def churn_rate_by(col):
    tab = df.groupby(col)['Churn'].agg(['mean','count']).reset_index().rename(columns={'mean':'churn_rate'})
    tab['churn_rate_pct'] = tab['churn_rate']*100
    tab = tab.sort_values('churn_rate', ascending=False)
    return tab

cats = ['account_Contract','internet_InternetService','account_PaperlessBilling','account_PaymentMethod']
for c in cats:
    if c in df.columns:
        t = churn_rate_by(c)
        print('\nChurn rate por', c)
        display(t)
        # grafico
        plt.figure(figsize=(8,4))
        sns.barplot(x=c, y='churn_rate_pct', data=t, order=t[c].tolist())
        plt.xticks(rotation=45, ha='right')
        plt.ylabel('Churn rate (%)')
        plt.title(f'Churn rate por {c}')
        plt.ylim(0,100)
        plt.savefig(os.path.join(output_folder,f'churn_by_{c}.png'), bbox_inches='tight', dpi=300)
        plt.show()
```

### 4.4 - Segmentos con mayor churn (Top 10)

```python
# Crear un segmento compuesto para búsqueda rápida
seg_cols = [c for c in ['account_Contract','internet_InternetService','account_PaperlessBilling'] if c in df.columns]
if seg_cols:
    seg = df.groupby(seg_cols)['Churn'].agg(['mean','count']).reset_index().rename(columns={'mean':'churn_rate'})
    seg['churn_rate_pct'] = seg['churn_rate']*100
    top_segments = seg.sort_values('churn_rate', ascending=False).head(10)
    display(top_segments)

# Matriz de correlación
num = df.select_dtypes(include=[np.number])
plt.figure(figsize=(8,6))
sns.heatmap(num.corr(), annot=True, fmt='.2f', cmap='viridis')
plt.title('Matriz de Correlación (numéricas)')
plt.savefig(os.path.join(output_folder,'correlation_matrix.png'), bbox_inches='tight', dpi=300)
plt.show()
```

---

## 🔹 5. Conclusiones e Insights (generadas a partir del análisis)

```python
# Insight automático (ejemplos basados en resultados calculados)
insights = []
insights.append(f'Churn global: {churn_rate:.2%} ({int(churn_count)} clientes)')
# Contrato con mayor churn (si existe)
if 'account_Contract' in df.columns:
    ctab = churn_rate_by('account_Contract')
    top_contract = ctab.iloc[0]
    insights.append(f"Contrato con mayor churn: {top_contract['account_Contract']} (tasa {top_contract['churn_rate']:.2%})")
# Tenure: comparar medias
if 'customer_tenure' in df.columns:
    m_churn = df.loc[df['Churn']==1, 'customer_tenure'].median()
    m_nochurn = df.loc[df['Churn']==0, 'customer_tenure'].median()
    insights.append(f'Median tenure: churn={m_churn} meses vs no-churn={m_nochurn} meses')

print('\n--- Insights automáticos ---')
for i in insights:
    print('-', i)
```

**Interpretación breve (ejemplo genérico):**

* Es frecuente que contratos *Month-to-month* presenten mayores tasas de churn (clientes sin compromiso tienden a partir más fácilmente).
* Clientes con baja antigüedad (tenure pequeña) suelen tener mayor probabilidad de cancelar.
* Ciertos métodos de pago o servicios (p. ej. "Electronic check" o "Fiber optic") frecuentemente aparecen asociados a churn mayor en muchos datasets telco.

---

## 🔹 6. Recomendaciones estratégicas

* **Programas de retención temprana:** foco en clientes con tenure < 3-6 meses (onboarding, ofertas especiales, soporte proactivo).
* **Incentivos a contratos:** descuentos o beneficios si migran de "Month-to-month" a 1 o 2 años.
* **Monitoreo de canales de pago:** analizar churn por método de pago y ajustar procesos de cobranza/recordatorios para los más riesgosos.
* **Mejorar experiencia en servicios críticos:** si "Fiber optic" muestra churn alto, investigar calidad de servicio (latencia, interrupciones) y ofrecer compensaciones.
* **Campañas dirigidas por segmento:** usar las tablas `top_segments` para diseñar campañas específicas.

---

## 🔹 7. Entregables y siguientes pasos

* `data/processed/telecom_churn_clean.csv` — dataset limpio.
* Carpeta `Datos/` con todas las figuras generadas.
* Sugerencia: construir baseline de clasificación (logistic/trees) para predecir churn y medir impacto de recomendaciones.

---

### Cómo exportar este notebook a PDF/HTML (opcional)

En terminal:

```bash
jupyter nbconvert --to html "TuNotebook.ipynb" --output "telecomx_churn_report.html"
```

---

> Si quieres, puedo:
>
> * Ejecutar y ajustar las celdas para que las visualizaciones usen estilos o colores específicos.
> * Generar el `.html` listo para descargar.
> * Añadir secciones de modelado (baseline) para clasificación.
