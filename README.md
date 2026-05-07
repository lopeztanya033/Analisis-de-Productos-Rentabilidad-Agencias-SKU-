# Analisis-de-Productos-Rentabilidad-Agencias-SKU-
Caso práctico - Se requiere un análisis que permita entender el desempeño por agencia y por SKU, evaluar la rentabilidad (precio vs costo) y proponer alertas tempranas para soportar la gestión comercial y operativa.

# Contexto
Se requiere un análisis que permita entender el desempeño por agencia y por SKU, evaluar la rentabilidad (precio vs costo) y proponer alertas tempranas para soportar la gestión comercial y operativa.
Objetivo del caso
Responder, con evidencia y claridad, las siguientes preguntas de negocio:
1.	¿Qué agencias y qué SKUs concentran el mayor aporte en unidades, ingresos y margen?
2.	¿Dónde existen riesgos de rentabilidad (por ejemplo, PVP por debajo del costo o margen negativo)?
3.	¿Qué alertas tempranas se pueden activar (meses en cero, caídas abruptas, picos atípicos, cambios de mix)?

   
# Alcance y supuestos
El candidato debe dejar por escrito los supuestos utilizados. Por ejemplo: qué precio se usa para calcular ingresos (PVP1 o PVP6), cómo interpreta unidades en cero y cómo trata valores faltantes.

# 📋 Documentación del Proyecto: Análisis de Ventas y Rentabilidad

## 1) Calidad de Datos y Transformación (ETL)

#### 🛠️ Tareas Mínimas (Obligatorias)
*   **Gestión de Duplicados:** Revisar registros con mismo `SKU` y `AgenciaFinal`. Indicar lógica de resolución.
*   **Normalización:** Validar consistencia entre `AGENCIA` y `AGENCIA_FINAL` para definir el campo maestro.
*   **Tipado y Limpieza:** Corregir tipos de datos (decimales, texto, nulos) y documentar decisiones tomadas.
*   **Transformación Unpivot:** Convertir la base de formato ancho (meses como columnas) a formato largo.

#### 📐 Estructura Esperada (Tabla Final)
| Campo | Descripción |
| :--- | :--- |
| **CODALTERNC** | Código alterno del producto (SKU) |
| **AGENCIA_FINAL** | Nombre de la agencia normalizada |
| **FechaMes** | Campo de fecha (formato fecha) |
| **Unidades** | Cantidad vendida |
| **COSTOEMPR** | Costo de la empresa |
| **PVP1 / PVP6** | Precios de venta al público |

---

## 2) Power BI (Modelo, DAX y Reporte)

#### 🏗️ Modelo de Datos
*   **Esquema:** Implementación de **Modelo en Estrella** (Fact Ventas + Dimensiones: Calendario, Agencia y Producto).
*   **Granularidad:** Tabla de hechos en formato largo con campo de fecha validado.
*   **Relaciones:** Cardinalidad 1:N y dirección de filtrado único (salvo excepciones justificadas).

#### 📊 Medidas Requeridas (DAX)
```dax
-- Cálculo de volumen
Unidades = SUM('Ventas'[Unidades])

-- Análisis financiero
Ingresos = SUMX('Ventas', 'Ventas'[Unidades] * 'Ventas'[PVP1]) 
Costo = SUMX('Ventas', 'Ventas'[Unidades] * 'Ventas'[COSTOEMPR])
Margen $ = [Ingresos] - [Costo]
Margen % = DIVIDE([Margen $], [Ingresos], 0)

-- Indicadores de rendimiento
Precio Promedio Ponderado = DIVIDE([Ingresos], [Unidades], 0)
Variación Mensual (MoM %) = 
    VAR MesAnterior = CALCULATE([Ingresos], DATEADD('Calendario'[Fecha], -1, MONTH))
    RETURN DIVIDE([Ingresos] - MesAnterior, MesAnterior, 0)

Participación % por Agencia = 
    DIVIDE([Ingresos], CALCULATE([Ingresos], ALL('Agencia')))

 
Página 1 - Resumen gerencial
•	KPIs: ingresos, margen %, unidades, top agencia y top SKU.
•	Tendencia mensual (ingresos y margen %, o unidades).
Página 2 - Desempeño por agencia
•	Ranking de agencias por ingresos y margen.
•	Agencia x Mes (o visual equivalente) para identificar patrones.
•	Top SKUs por agencia (tabla o matriz).
Página 3 - Rentabilidad y alertas
•	Tabla de alertas: PVP < costo, margen negativo, outliers de unidades.
•	Visual para identificar relación entre volumen y margen (por ejemplo, dispersión Unidades vs Margen %).

---

## 3) Excel (Análisis Rápido)

#### ✅ Requisitos Obligatorios
*   **Interactividad:** Implementar una **Tabla Dinámica** robusta que incluya segmentadores de datos (*Slicers*) para los campos de **Mes** y **AgenciaFinal**.
*   **Análisis de Inventario/Ventas:** Generar una **Clasificación ABC** por ingresos, asegurando que el criterio de segmentación (80/15/5 o similar) sea consistente y esté definido.
*   **Dashboard Ejecutivo:** Creación de una hoja denominada **"Resumen"** que visualice:
    *   Al menos **5 métricas clave** (KPIs).
    *   Ranking **Top 5 Agencias**.
    *   Ranking **Top 5 SKUs** (Productos).

> **Nota:** Se valorará la limpieza del formato, el uso de tablas oficiales de Excel y la facilidad de navegación entre hojas.

