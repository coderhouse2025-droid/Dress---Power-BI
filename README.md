# 👗 Dress — Análisis de Indumentaria Urbana y Deportes de Acción

> Dashboard de Business Intelligence desarrollado en Power BI para el análisis de ventas, rentabilidad y comportamiento comercial de **Dress**, una marca de indumentaria urbana y deportes de acción con presencia en múltiples sucursales del Gran Buenos Aires.

---

## 📌 Descripción del Proyecto

Este proyecto corresponde a la **Entrega 3** del curso de Power BI en Coderhouse. Se trata de un reporte interactivo que integra datos de ventas, productos, vendedores, métodos de pago y sucursales, permitiendo un análisis comparativo entre los distintos puntos de venta de la marca.

El informe fue construido sobre un modelo de datos relacional con tabla calendario personalizada y un conjunto de medidas DAX para el cálculo de KPIs clave.

---

## 📊 Páginas del Reporte

| Página | Descripción |
|---|---|
| **Portada** | Presentación visual de la marca y navegación al reporte |
| **Tablero General** | Vista consolidada de ventas, facturación y rentabilidad |
| **DOT** | Análisis de la sucursal DOT |
| **Nordelta** | Análisis de la sucursal Nordelta |
| **Distrito Arcos** | Análisis de la sucursal Distrito Arcos |
| **Unicenter** | Análisis de la sucursal Unicenter |
| **Conclusión** | Síntesis de hallazgos y recomendaciones |
| **Conclusión (bis)** | Análisis complementario y cierre |

---

## 🗄️ Modelo de Datos

El modelo está compuesto por las siguientes tablas:

**Tablas de dimensión:**
- `Producto` — Catálogo de productos con talle, color y marca
- `Sucursal` — Puntos de venta con logo asociado
- `Vendedor` — Datos del equipo de ventas
- `Género` — Segmentación por género (con logo)
- `Método de Pago` — Tipos de pago y cuotas
- `Entidad` — Entidades financieras
- `Marca` — Marcas comercializadas

**Tabla de hechos:**
- `Ventas` — Operaciones con fecha, cantidad, precio unitario, facturado y rentabilidad

**Tabla de tiempo:**
- `Tabla Calendario` — Calendario con año, mes, trimestre, día y jerarquía de fechas

---

## 📐 Medidas DAX Principales

```dax
-- Facturación Total
Facturación Total = SUM(Ventas[Facturado])

-- Cantidad de Ventas
Cant. de Ventas = COUNTROWS(Ventas)

-- Rentabilidad Total
Rentabilidad = SUM(Ventas[Rentabilidad])

-- Facturación Promedio
Facturación Promedio = AVERAGE(Ventas[Facturado])

-- Variación (período vs período anterior)
Variación = [ver fórmula en el modelo]
```

---

## 🛠️ Tecnologías y Herramientas

- **Power BI Desktop** — Desarrollo del reporte
- **DAX** — Lenguaje de medidas y columnas calculadas
- **Power Query (M)** — Transformación y limpieza de datos
- **Custom Visual: ChicletSlicer** — Segmentador visual avanzado

---

## 📁 Estructura del Repositorio

```
├── Entrega 3 - ORELLANA (F).pbit   # Plantilla del reporte Power BI
├── Entrega 3 - ORELLANA (F).pdf    # Vista exportada del reporte
├── ENTREGA 1 - ORELLANA.pdf        # Entrega previa 1
├── ENTREGA 2 - ORELLANA.pdf        # Entrega previa 2
└── README.md
```

> ⚠️ El archivo `.pbit` es una **plantilla** de Power BI. Al abrirlo, deberás conectar tu fuente de datos para que se cargue el modelo completo.

---

## 🚀 Cómo usar este proyecto

1. Cloná o descargá el repositorio
2. Abrí el archivo `Entrega 3 - ORELLANA (F).pbit` con **Power BI Desktop**
3. Conectá la fuente de datos cuando se solicite
4. Explorá el reporte utilizando los filtros y segmentadores de cada página

---

## 👤 Autor

**F. Orellana**  
Curso Power BI — Coderhouse 2025  
📍 Buenos Aires, Argentina

---

## 📄 Licencia

Este proyecto fue desarrollado con fines académicos en el marco del curso de Power BI de Coderhouse.
