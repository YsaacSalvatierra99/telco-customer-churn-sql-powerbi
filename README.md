# 📉 Telco Customer Churn: Segmentación Comercial y Detección de Fuga

## 📌 Contexto del Proyecto y Dataset
En la industria de las telecomunicaciones, la saturación del mercado ha provocado que la retención de clientes sea una prioridad absoluta. Este proyecto utiliza un dataset transaccional y demográfico de una empresa Telco (Telecomunicaciones) para analizar el comportamiento de los usuarios, predecir la tasa de abandono (*Churn Rate*) y descubrir oportunidades comerciales.

El objetivo central es utilizar análisis de datos para segmentar la base de clientes y proporcionar a los equipos de ventas y retención listados accionables ("Leads") que permitan actuar de forma proactiva antes de que el cliente decida darse de baja.

### 📖 Diccionario de Datos
Para comprender el comportamiento de los usuarios, a continuación se detallan las variables principales del dataset original `telco-customer-churn.csv`:

| Columna | Descripción |
| :--- | :--- |
| **customerID** | Identificador único de cada cliente. |
| **gender** | Género del cliente. |
| **SeniorCitizen** | `1` : Es adulto mayor, `0` : No lo es. |
| **Partner / Dependents** | `Yes` : Tiene pareja / dependientes a cargo, `No` : No tiene. |
| **tenure** | Antigüedad del cliente en la empresa (expresado en meses). |
| **PhoneService / InternetService** | Tipo de servicio base contratado (Ej. DSL, Fiber optic, No). |
| **OnlineSecurity, TechSupport, etc.** | Servicios adicionales contratados (`Yes`, `No`, `No internet service`). |
| **Contract** | Tipo de contrato actual (`Month-to-month`, `One year`, `Two year`). |
| **MonthlyCharges** | Cargo mensual actual que paga el cliente. |
| **TotalCharges** | Cargos totales acumulados a lo largo de su historia con la empresa. |
| **Churn** | Variable objetivo (`Yes` : Abandonó la empresa, `No` : Sigue activo). |

---

## ❓ Preguntas de Negocio (EDA) fundamentadas en el mercado
Durante la fase exploratoria (EDA) en SQL Server, el análisis se guió por tres premisas de negocio respaldadas por consultoras líderes en la industria:

**1. ¿Cuál es la relación entre los canales de soporte técnico y la tasa de fuga?**
> *Fundamento:* Según un caso de estudio de **Bain & Company** enfocado en telecomunicaciones europeas, los problemas operativos en el servicio al cliente son una de las principales amenazas para el crecimiento. Al optimizar los puntos de contacto como los *call centers*, la consultora logró mejorar el Net Promoter Score (NPS) en más de 30 puntos porcentuales, reduciendo significativamente el churn. Por lo tanto, en este EDA buscaremos si la falta del servicio `TechSupport` dispara los niveles de abandono.

**2. ¿Podemos identificar umbrales críticos de consumo y antigüedad que predigan el abandono?**
> *Fundamento:* **McKinsey & Company** señala que aplicar analítica avanzada para descubrir variables ocultas (combinando el tipo de plan, el uso de datos y el historial de soporte) permite predecir con precisión la deserción. De hecho, un enfoque basado en datos puede reducir el churn hasta en un 15%. Exploraremos la relación entre `MonthlyCharges`, `Contract` y el riesgo inminente de fuga.

**3. ¿Cuántos clientes de alto valor pueden agruparse en micro-segmentos para campañas de Upselling y Cross-selling?**
> *Fundamento:* El mismo informe de **McKinsey** destaca que el verdadero valor de los datos se obtiene al dividir la base de clientes en decenas de micro-segmentos para personalizar ofertas con precisión. A su vez, **Bain** afirma que los clientes leales con buenas experiencias tienden a comprar más y quedarse más tiempo. Buscaremos clientes con alto `tenure` pero con servicios básicos para ofrecer mejoras de plan.

---

## 🛠️ Stack Tecnológico y Flujo de Trabajo (Pipeline)
Para este proyecto, decidí separar la capa de transformación de la capa de visualización:
*   **Motor de Base de Datos (ETL):** SQL Server (SSMS).
*   **Lógica de Negocio:** Consultas T-SQL (`CASE WHEN`, agrupaciones y CTEs) para limpiar los datos crudos y crear una vista materializada (`vw_SegmentacionClientes`).
*   **Visualización:** Power BI (Conexión directa a SQL Server para modelar el dashboard final).

## 📊 Segmentación Implementada
Utilizando sentencias SQL, clasifiqué la base en tres grupos comerciales:
*   🔴 **Grupo 1 (Riesgo de Fuga):** Clientes con contratos mensuales, alta facturación y sin servicios de retención (ej. TechSupport). 
*   🟡 **Grupo 2 (Oportunidad de Upselling):** Clientes leales con alto consumo pero en planes básicos.
*   🟢 **Grupo 3 (Oportunidad de Cross-selling):** Clientes que poseen línea móvil pero no tienen contratado Internet por fibra óptica.

## 📈 Dashboard en Power BI
*(Aquí insertaré la captura del tablero comercial definitivo)*
![Dashboard Telco Churn](images/tu_imagen_aqui.png)

## 💡 Conclusiones
*   *(Espacio reservado para agregar 2 o 3 insights clave descubiertos en Power BI)*
*   *(Ejemplo: El equipo comercial ahora dispone de una "Base de Leads" filtrable en el dashboard, priorizando a los clientes del Grupo 1 para campañas de retención inmediata).*

---
**Referencias de la industria utilizadas en el análisis:**
* [1] Bain & Company. *"Dialing up customer experience in telecommunications"*. [Enlace al artículo](https://www.bain.com/client-results/dialing-up-customer-experience-in-telecommunications/)
* [2] McKinsey & Company. *"Reducing churn in telecom through advanced analytics"*. [Enlace al artículo](https://www.mckinsey.com/industries/technology-media-and-telecommunications/our-insights/reducing-churn-in-telecom-through-advanced-analytics)

*Proyecto desarrollado por Ysaac Salvatierra.*
