# 🔍 Fase 1: Análisis Exploratorio de Datos (EDA) en SQL Server

Este documento contiene las consultas T-SQL ejecutadas para responder a las preguntas de negocio iniciales planteadas en el proyecto. Se analiza la base de datos `TelcoDB` buscando patrones de deserción (*churn*) y oportunidades comerciales directas.

---

## 📌 Pregunta 1: Impacto del Soporte Técnico en la Retención
> **Pregunta de Negocio:** ¿Existe una relación directa entre no contar con soporte técnico (`TechSupport`) y el riesgo de fuga?

<details>
<summary>👉 Ver consulta T-SQL</summary>

```sql
-- Analisis de tasa de fuga segun contratacion de Soporte Tecnico
SELECT 
    TechSupport,
    COUNT(*) AS TotalClientes,
    SUM(CASE WHEN Churn = 'Yes' THEN 1 ELSE 0 END) AS ClientesFugados,
    CAST(ROUND(SUM(CASE WHEN Churn = 'Yes' THEN 1.0 ELSE 0.0 END) / COUNT(*) * 100, 2) AS DECIMAL(5,2)) AS TasaChurn_Pct
FROM TelcoCustomerChurn
GROUP BY TechSupport
ORDER BY TasaChurn_Pct DESC;
