# ⚙️ Fase 2: Transformación y Modelado Relacional (Star Schema)

El objetivo de esta fase es preparar y estructurar los datos antes de su consumo en Power BI. En lugar de importar la tabla plana original (`Telco-Customer-Churn`) con sus 21 columnas mezcladas, se diseñó un **Modelo Estrella** directamente en SQL Server mediante vistas (`VIEW`). 

Este enfoque asegura la integridad de los datos crudos, optimiza el rendimiento del futuro dashboard y centraliza la lógica de negocio en el motor de la base de datos.

---

## Arquitectura del Modelo
La base de datos original fue normalizada en cuatro entidades distintas, conectadas lógicamente a través de la clave primaria `customerID`:

* **`vw_Dim_Cliente`**: Dimensión que aísla los atributos demográficos (género, estado civil, dependientes).
* **`vw_Dim_Servicios`**: Dimensión que agrupa el catálogo de servicios de conectividad y entretenimiento contratados.
* **`vw_Dim_Contrato`**: Dimensión enfocada en los detalles de facturación, métodos de pago y tipo de suscripción.
* **`vw_Fact_Transacciones`**: Tabla central de hechos que almacena las métricas cuantitativas (`tenure`, `MonthlyCharges`) y las nuevas categorizaciones de negocio.

---

## Ingeniería de Características (Feature Engineering)
Para aportar valor comercial inmediato, la creación de la tabla de hechos incluyó la generación de nuevas columnas calculadas al vuelo, evitando alterar el dataset original:

* **Segmento Comercial**: Utilizando sentencias `CASE WHEN`, se clasificó automáticamente a los clientes en tres grupos accionables (*Riesgo Fuga*, *Upselling* o *Cross-selling*) según sus umbrales críticos de gasto y antigüedad descubiertos en el EDA.
* **Índice de Retención (Total Servicios Extra)**: Se sumaron los servicios de valor agregado (`TechSupport`, `OnlineSecurity`, `DeviceProtection`, `OnlineBackup`) para obtener una métrica de 0 a 4 que permita medir la barrera de salida o nivel de anclaje (*stickiness*) de cada cliente.

---

## Código SQL de Implementación

<details>
<summary>👉 Ver script de creación de Vistas (ETL y Dimensiones)</summary>

```sql
USE TelcoDB;
GO

-- 1. CREACIÓN DE LA DIMENSIÓN CLIENTE
CREATE VIEW vw_Dim_Cliente AS
SELECT 
    customerID, 
    gender, 
    SeniorCitizen, 
    Partner, 
    Dependents
FROM [Telco-Customer-Churn];
GO


-- 2. CREACIÓN DE LA DIMENSIÓN SERVICIOS
CREATE VIEW vw_Dim_Servicios AS
SELECT 
    customerID, 
    PhoneService, 
    MultipleLines, 
    InternetService, 
    OnlineSecurity, 
    OnlineBackup, 
    DeviceProtection, 
    TechSupport, 
    StreamingTV, 
    StreamingMovies
FROM [Telco-Customer-Churn];
GO


-- 3. CREACIÓN DE LA DIMENSIÓN CONTRATO
CREATE VIEW vw_Dim_Contrato AS
SELECT 
    customerID, 
    Contract, 
    PaperlessBilling, 
    PaymentMethod
FROM [Telco-Customer-Churn];
GO


-- 4. CREACIÓN DE LA TABLA DE HECHOS (Con Feature Engineering)
CREATE VIEW vw_Fact_Transacciones AS
SELECT     
    customerID,
    tenure,
    MonthlyCharges,
    TRY_CONVERT(FLOAT, NULLIF(TotalCharges, ' ')) AS TotalCharges,
    Churn,
    CASE     
        WHEN Contract = 'Month-to-month' AND MonthlyCharges > 70 THEN 'Grupo 1 (Riesgo Fuga)'
        WHEN InternetService = 'DSL' AND tenure > 24 THEN 'Grupo 2 (Upselling)'
        WHEN PhoneService = 'Yes' AND InternetService = 'No' AND tenure > 12 THEN 'Grupo 3 (Cross-selling)'
        ELSE 'Estable / Sin clasificar'
    END AS SegmentoComercial,
    (CASE WHEN OnlineSecurity = 'Yes' THEN 1 ELSE 0 END +
     CASE WHEN OnlineBackup = 'Yes' THEN 1 ELSE 0 END +
     CASE WHEN DeviceProtection = 'Yes' THEN 1 ELSE 0 END +
     CASE WHEN TechSupport = 'Yes' THEN 1 ELSE 0 END) AS TotalServiciosExtra

FROM [Telco-Customer-Churn];
GO
```
</details>
