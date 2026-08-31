# 📉 Telco Customer Churn: Segmentación Comercial y Detección de Fuga

## 📌 Objetivo del Proyecto
El objetivo principal de este proyecto es analizar el comportamiento de los clientes de una empresa de telecomunicaciones para reducir la tasa de abandono (*Churn Rate*) y detectar oportunidades comerciales. 

A través de un pipeline de datos construido con **SQL Server** y visualizado en **Power BI**, busqué pasar de datos crudos a insights accionables, creando segmentos de clientes listos para ser utilizados por un equipo de ventas o retención.

## ❓ Preguntas de Negocio (EDA)
Durante la fase exploratoria (EDA), busqué responder estas preguntas centrales para entender el problema:
1. **¿Cuál es el perfil demográfico y de consumo de los clientes que se dan de baja vs. los que se quedan?** (Ej. Tipo de contrato, antigüedad).
2. **¿Existe una relación directa entre el tipo de soporte técnico requerido y el riesgo de fuga?**
3. **¿Cuántos clientes estables (alto *tenure*) representan oportunidades de Upselling o Cross-selling?**

## 🛠️ Stack Tecnológico y Flujo de Trabajo
*   **Motor de Base de Datos:** SQL Server (SSMS).
*   **ETL & Lógica de Negocio:** Consultas T-SQL (`CASE WHEN`, CTEs, Agrupaciones) para limpiar los datos y crear la vista de segmentación.
*   **Visualización:** Power BI (Conexión DirectQuery/Import a la vista de SQL Server para crear el dashboard interactivo).

## 📊 Segmentación Implementada
Utilizando sentencias SQL, clasifiqué la base en tres grupos accionables:
*   🔴 **Grupo 1 (Riesgo de Fuga):** Clientes con contratos mensuales, alta facturación y sin servicios de retención (ej. TechSupport). 
*   🟡 **Grupo 2 (Oportunidad de Upselling):** Clientes leales con alto consumo pero en planes básicos.
*   🟢 **Grupo 3 (Oportunidad de Cross-selling):** Clientes que poseen línea móvil pero no tienen contratado Internet por fibra óptica.

## 📈 Dashboard en Power BI
*(Aquí tienes que insertar una imagen de tu dashboard. Ejemplo:)*
![Dashboard de Power BI](images/dashboard_captura.png)

## 💡 Conclusiones y Próximos Pasos
*   *(Aquí pondrás 2 o 3 conclusiones reales que saques cuando termines de armar los gráficos. Por ejemplo: "Se detectó que el 70% del churn proviene de contratos mes a mes...")*
*   El dashboard final incluye una "Base de Leads" filtrable, permitiendo al equipo comercial descargar listados específicos para ejecutar campañas telefónicas dirigidas.

---
*Proyecto desarrollado por [Tu Nombre / Ysaac Salvatierra]*
