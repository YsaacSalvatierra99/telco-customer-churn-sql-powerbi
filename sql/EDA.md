# 🔍 Fase 1: Análisis Exploratorio de Datos (EDA) en SQL Server

Este documento consolida la fase de exploración analítica realizada sobre la base de datos `TelcoDB`. El objetivo es contrastar las hipótesis iniciales de deserción (*churn*) con métricas cuantificables extraídas mediante consultas T-SQL, identificando tanto los factores críticos de fuga como las oportunidades de monetización para el negocio.

---

## 1. Impacto del Soporte Técnico en la Retención de Clientes

### ❓ Pregunta de Negocio
¿Existe una relación directa entre no contar con soporte técnico (`TechSupport`) y el incremento en el riesgo de fuga de los clientes?

<details>
<summary>👉 Ver consulta T-SQL</summary>

```sql
-- Analisis de tasa de fuga segun contratacion de Soporte Tecnico
SELECT 
    TechSupport,
    COUNT(*) AS TotalClientes,
    SUM(CASE WHEN Churn = 'Yes' THEN 1 ELSE 0 END) AS ClientesFugados,
    CAST(ROUND(SUM(CASE WHEN Churn = 'Yes' THEN 1.0 ELSE 0.0 END) / COUNT(*) * 100, 2) AS DECIMAL(5,2)) AS TasaChurn_Pct
FROM [Telco-Customer-Churn]
GROUP BY TechSupport
ORDER BY TasaChurn_Pct DESC;
```

</details>

### 📊 Resultados Obtenidos
| TechSupport | TotalClientes | ClientesFugados | TasaChurn_Pct (%) |
| :--- | :--- | :--- | :--- |
| **No** | 3473 | 1446 | **41.64%** |
| **Yes** | 2044 | 310 | **15.17%** |
| **No internet service** | 1526 | 113 | **7.40%** |

### 💡 Insight de Negocio
Los clientes que tienen servicio de internet activo pero **carecen de soporte técnico** registran una tasa de fuga del **41.64%**, casi tres veces superior a la de aquellos que cuentan con el servicio contratado (**15.17%**). Esto valida la premisa operativa de Bain & Company: el soporte postventa actúa como una de las principales anclas de retención. Bonificar o incluir asistencia técnica durante los primeros meses reduciría drásticamente el volumen de bajas en este grupo.

---

## 2. Umbrales Críticos de Facturación y Tipo de Contrato

### ❓ Pregunta de Negocio
¿A partir de qué nivel de cargo mensual (`MonthlyCharges`) y bajo qué modalidad contractual se concentra el mayor volumen de deserción?

<details>
<summary>👉 Ver consulta T-SQL</summary>

```sql
-- Evaluacion de Churn cruzando Tipo de Contrato y Rango de Gasto Mensual
SELECT 
    Contract,
    CASE 
        WHEN MonthlyCharges < 35 THEN 'Bajo (<$35)'
        WHEN MonthlyCharges BETWEEN 35 AND 70 THEN 'Medio ($35-$70)'
        ELSE 'Alto (>$70)'
    END AS SegmentoGasto,
    COUNT(*) AS TotalClientes,
    SUM(CASE WHEN Churn = 'Yes' THEN 1 ELSE 0 END) AS ClientesFugados,
    CAST(ROUND(SUM(CASE WHEN Churn = 'Yes' THEN 1.0 ELSE 0.0 END) / COUNT(*) * 100, 2) AS DECIMAL(5,2)) AS TasaChurn_Pct
FROM [Telco-Customer-Churn]
GROUP BY 
    Contract,
    CASE 
        WHEN MonthlyCharges < 35 THEN 'Bajo (<$35)'
        WHEN MonthlyCharges BETWEEN 35 AND 70 THEN 'Medio ($35-$70)'
        ELSE 'Alto (>$70)'
    END
ORDER BY Contract DESC, TasaChurn_Pct DESC;
```

</details>

### 📊 Resultados Obtenidos
| Contract | SegmentoGasto | TotalClientes | ClientesFugados | TasaChurn_Pct (%) |
| :--- | :--- | :--- | :--- | :--- |
| **Month-to-month** | Alto (>$70) | 2097 | 1105 | **52.69%** |
| **Month-to-month** | Medio ($35-$70) | 1074 | 379 | **35.29%** |
| **Month-to-month** | Bajo (<$35) | 704 | 171 | **24.29%** |
| **One year** | Alto (>$70) | 717 | 125 | **17.43%** |
| **One year** | Medio ($35-$70) | 371 | 29 | **7.82%** |
| **One year** | Bajo (<$35) | 385 | 12 | **3.12%** |
| **Two year** | Alto (>$70) | 769 | 37 | **4.81%** |
| **Two year** | Medio ($35-$70) | 284 | 6 | **2.11%** |
| **Two year** | Bajo (<$35) | 642 | 5 | **0.78%** |

### 💡 Insight de Negocio
El cruce entre **contrato mes a mes** y una factura superior a **$70/mes** representa el principal foco de riesgo financiero: el **52.69%** de estos clientes abandonan la compañía (1.105 bajas). En contraste, los clientes con contratos a 2 años presentan una fuga inferior al 5%, aun pagando cargos elevados. El problema no es el precio aislado, sino la falta de un compromiso contractual que amortigüe la rotación ante cargos altos.

---

## 3. Ventana Crítica de Fuga Temprana (*Early Churn*)

### ❓ Pregunta de Negocio
¿En qué rango de antigüedad (`tenure`) es más vulnerable un cliente con contrato mensual a darse de baja?

<details>
<summary>👉 Ver consulta T-SQL</summary>

```sql
-- Distribucion de churn segun antiguedad en clientes con contrato mensual
SELECT 
    CASE 
        WHEN tenure <= 6 THEN '0 a 6 meses (Onboarding)'
        WHEN tenure BETWEEN 7 AND 12 THEN '7 a 12 meses'
        WHEN tenure BETWEEN 13 AND 24 THEN '1 a 2 años'
        ELSE 'Mas de 2 años'
    END AS VentanaAntiguedad,
    COUNT(*) AS TotalClientes,
    SUM(CASE WHEN Churn = 'Yes' THEN 1 ELSE 0 END) AS ClientesFugados,
    CAST(ROUND(SUM(CASE WHEN Churn = 'Yes' THEN 1.0 ELSE 0.0 END) / COUNT(*) * 100, 2) AS DECIMAL(5,2)) AS TasaChurn_Pct
FROM [Telco-Customer-Churn]
WHERE Contract = 'Month-to-month'
GROUP BY 
    CASE 
        WHEN tenure <= 6 THEN '0 a 6 meses (Onboarding)'
        WHEN tenure BETWEEN 7 AND 12 THEN '7 a 12 meses'
        WHEN tenure BETWEEN 13 AND 24 THEN '1 a 2 años'
        ELSE 'Mas de 2 años'
    END
ORDER BY TasaChurn_Pct DESC;
```

</details>

### 📊 Resultados Obtenidos
| VentanaAntiguedad | TotalClientes | ClientesFugados | TasaChurn_Pct (%) |
| :--- | :--- | :--- | :--- |
| **0 a 6 meses (Onboarding)** | 1413 | 780 | **55.20%** |
| **7 a 12 meses** | 581 | 244 | **42.00%** |
| **1 a 2 años** | 737 | 278 | **37.72%** |
| **Mas de 2 años** | 1144 | 353 | **30.86%** |

### 💡 Insight de Negocio
El **55.20%** de los clientes nuevos en contratos mes a mes abandonan la empresa durante el primer semestre de vida útil (*Onboarding*). A medida que el cliente supera los 12 meses, la probabilidad de fuga se estabiliza. Esto indica que el equipo de retención no debe esperar al final del ciclo de vida: las campañas de acompañamiento y fidelización deben activarse entre el mes 1 y el mes 3.

---

## 4. Cuantificación de Oportunidades Comerciales (Upselling y Cross-selling)

### ❓ Pregunta de Negocio
¿Cuántos clientes fidelizados y activos califican como oportunidades comerciales directas para migraciones de plan o venta de conectividad?

<details>
<summary>👉 Ver consulta T-SQL</summary>

```sql
-- Identificacion de volumen para estrategias comerciales
SELECT 
    'Upselling (DSL a Fibra)' AS Oportunidad,
    COUNT(*) AS LeadsPotenciales
FROM [Telco-Customer-Churn]
WHERE Churn = 'No' 
  AND tenure > 24 
  AND InternetService = 'DSL'

UNION ALL

SELECT 
    'Cross-selling (Venta de Internet)',
    COUNT(*)
FROM [Telco-Customer-Churn]
WHERE Churn = 'No' 
  AND tenure > 12 
  AND PhoneService = 'Yes' 
  AND InternetService = 'No';
```

</details>

### 📊 Resultados Obtenidos
| Oportunidad | LeadsPotenciales |
| :--- | :--- |
| **Upselling (DSL a Fibra)** | **1242** clientes |
| **Cross-selling (Venta de Internet)** | **992** clientes |

### 💡 Insight de Negocio
El análisis no solo detecta pérdida, sino potencial de crecimiento orgánico:
* **1.242 clientes** tienen más de dos años con tecnología DSL de menor velocidad; representan el segmento ideal para migrar a Fibra Óptica (*Upselling*), aumentando el ingreso medio por usuario (*ARPU*).
* **992 clientes** utilizan únicamente telefonía fija/móvil desde hace más de un año sin haber contratado internet (*Cross-selling*). Constituyen una base fidelizada lista para recibir ofertas de paquetes combinados (*bundle*) con baja fricción de adquisición.
