<img width="1536" height="1024" alt="ChatGPT Image 9 jun 2026, 10_57_15" src="https://github.com/user-attachments/assets/60a2fc62-7848-4d3b-b536-6d7090480295" />

# 🛹 Dress — Dashboard de Business Intelligence en Power BI

[![Power BI](https://img.shields.io/badge/Power_BI-Desktop-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)](https://powerbi.microsoft.com/)
[![DAX](https://img.shields.io/badge/Lenguaje-DAX-0078D4?style=for-the-badge)](#medidas-dax-y-su-justificación)
[![Modelo Estrella](https://img.shields.io/badge/Modelo-Estrella-blueviolet?style=for-the-badge)](#modelo-de-datos-diseño-y-decisiones)

> Dashboard de Business Intelligence desarrollado en Power BI para el análisis de ventas, rentabilidad y comportamiento comercial de **Dress**, una marca de indumentaria urbana y deportes de acción con presencia en cuatro sucursales del Gran Buenos Aires.

📄 **Ver reporte (PDF):** [Entrega 3 - ORELLANA (F).pdf](./Entrega%203%20-%20ORELLANA%20(F).pdf)
📊 **Template del reporte:** [Entrega 3 - ORELLANA (F).pbit](./Entrega%203%20-%20ORELLANA%20(F).pbit)

---

## 📋 Índice

- [Descripción](#-descripción)
- [Caso de negocio](#-caso-de-negocio)
- [Decisiones técnicas y su justificación](#-decisiones-técnicas-y-su-justificación)
- [Modelo de datos: diseño y decisiones](#-modelo-de-datos-diseño-y-decisiones)
- [Pipeline de datos: del dataset sucio al modelo limpio](#-pipeline-de-datos-del-dataset-sucio-al-modelo-limpio)
- [Medidas DAX y su justificación](#-medidas-dax-y-su-justificación)
- [¿Por qué este camino y no otro?](#-por-qué-este-camino-y-no-otro)
- [Páginas del reporte](#-páginas-del-reporte)
- [Estructura del repositorio](#-estructura-del-repositorio)
- [Roadmap](#-roadmap)

---

## 📋 Descripción

Reporte interactivo de Business Intelligence construido sobre un modelo de datos relacional en esquema estrella. Integra datos de ventas, productos, vendedores, métodos de pago y sucursales de las cuatro tiendas de Dress en el Gran Buenos Aires (DOT, Nordelta, Distrito Arcos, Unicenter), permitiendo análisis comparativos entre puntos de venta, seguimiento de KPIs comerciales y detección de oportunidades de mejora en rentabilidad.

---

## 💼 Caso de negocio

### El problema que resuelve

Dress opera cuatro sucursales con perfiles de cliente distintos: DOT y Unicenter atienden un público de alto tráfico y perfil masivo; Nordelta y Distrito Arcos tienen un perfil de cliente con mayor poder adquisitivo pero menor volumen. Sin un sistema de análisis unificado, cada gerente de sucursal tiene visibilidad solo de su local — no puede comparar su performance contra el resto de la red ni detectar si un problema es propio o sistémico.

Las preguntas de negocio sin respuesta eran concretas:

- ¿Qué sucursal tiene mayor facturación pero menor rentabilidad, y por qué?
- ¿Qué vendedor tiene mayor ticket promedio en cada punto de venta?
- ¿Qué método de pago concentra más ventas y cómo impacta en el margen?
- ¿Qué productos tienen alta rotación pero baja rentabilidad unitaria?
- ¿La caída de ventas en un período es generalizada o focalizada en una sucursal?

Estas preguntas no son respondibles desde planillas individuales por sucursal. Requieren un modelo de datos integrado con la capacidad de cruzar dimensiones (sucursal × producto × vendedor × período) sobre la misma tabla de hechos.

### La propuesta de valor del reporte

El dashboard reemplaza el ciclo habitual de consolidación manual: exportar Excel de cada sucursal → copiar a una planilla maestra → calcular manualmente → armar gráficos para la reunión de dirección. Ese proceso tomaba días y producía un análisis estático que ya estaba desactualizado al presentarse.

Con el modelo en Power BI: los datos se actualizan desde la fuente, los KPIs se recalculan automáticamente, y cualquier miembro del equipo directivo puede explorar el reporte con filtros sin necesitar a alguien con "conocimiento en Excel".

---

## 🧠 Decisiones técnicas y su justificación

### 1. Power BI Desktop — no Tableau, no Google Looker Studio

**¿Por qué Power BI?**

La elección de herramienta de BI no es solo técnica — es contextual al ecosistema del negocio. Dress opera con datos en fuentes Microsoft (Excel, potencialmente SQL Server). Power BI tiene integración nativa con estas fuentes, sin pasos de exportación/importación intermedios.

Comparado con Tableau: Tableau es más potente para visualizaciones complejas y bespoke, tiene una curva de aprendizaje mayor y un costo de licencia significativamente más alto. Para un dashboard de análisis comercial estándar (ventas, rentabilidad, comparativas), Power BI entrega el mismo valor a una fracción del costo — o sin costo en el tier gratuito para reportes estáticos.

Comparado con Google Looker Studio: Looker Studio es gratuito y colaborativo, pero su motor DAX no existe — las métricas calculadas son menos potentes, el modelado relacional es más limitado, y la experiencia offline es inexistente. Para un reporte que necesita medidas de inteligencia de tiempo (comparativas año anterior, acumulados) y un modelo con 8 tablas relacionadas, Power BI es la herramienta correcta.

**La consideración de adopción:** Power BI tiene integración con Microsoft 365 que la mayoría de los comercios en Argentina ya usan. El gerente puede abrir el reporte publicado en Power BI Service desde su browser sin instalar nada.

---

### 2. Esquema estrella — no esquema copo de nieve, no tabla plana

**¿Por qué esquema estrella?**

El esquema estrella coloca la tabla de hechos (`Ventas`) en el centro, rodeada de tablas de dimensión (`Producto`, `Sucursal`, `Vendedor`, `Método de Pago`, `Entidad`, `Género`, `Marca`). Cada dimensión se conecta a la tabla de hechos por una clave simple, sin relaciones encadenadas.

La alternativa más común es el esquema copo de nieve (snowflake), donde las dimensiones se normalizan en subdimensiones. Por ejemplo: `Ventas → Producto → Marca` en lugar de tener `Marca` como dimensión directa conectada a hechos.

**¿Por qué estrella y no copo de nieve?**

En Power BI, el esquema copo de nieve impone joins adicionales en cada consulta DAX, lo que degrada el performance en reportes con alta interactividad. El motor de almacenamiento columnar de Power BI (VertiPaq) está optimizado para el esquema estrella: las dimensiones desnormalizadas se comprimen eficientemente y los filtros se propagan directamente desde dimensión a hechos sin saltos intermedios.

Para un reporte con 8 páginas, múltiples slicers interactivos y cálculos DAX complejos ejecutándose simultáneamente, el performance del esquema estrella es notablemente superior.

**¿Por qué no tabla plana?**

Una tabla plana (todos los campos en una sola tabla) es la solución más simple pero la menos escalable. Si cada venta incluye el nombre del vendedor, su sucursal, el nombre del producto y la marca en la misma fila, cualquier corrección en el nombre de un vendedor requiere actualizar miles de filas. El modelo relacional lo resuelve actualizando un único registro en la tabla `Vendedor`.

---

### 3. Tabla Calendario personalizada — no usar columnas de fecha de la tabla de hechos

**¿Por qué una tabla calendario independiente?**

Usar directamente las fechas de la columna `Fecha` de la tabla `Ventas` para análisis temporal es el error más común en modelos Power BI de baja madurez. Los problemas que genera:

- No permite inteligencia de tiempo en DAX (`SAMEPERIODLASTYEAR`, `DATESYTD`, `DATEADD`) sin una tabla de fechas continua y marcada como tal
- No permite analizar períodos sin ventas (los días sin transacciones no existen en la tabla de hechos, pero deben aparecer en el eje del tiempo con valor 0)
- Las jerarquías de tiempo (Año → Trimestre → Mes → Día) no se construyen automáticamente desde una columna de fecha

La tabla calendario se creó con DAX usando `CALENDAR()` con rango dinámico basado en el mínimo y máximo de fechas en la tabla de hechos:

```dax
Tabla Calendario =
CALENDAR(
    MIN(Ventas[Fecha]),
    MAX(Ventas[Fecha])
)
```

Y se enriqueció con columnas calculadas para todas las granularidades necesarias:

```dax
Año = YEAR([Date])
Mes Número = MONTH([Date])
Mes Nombre = FORMAT([Date], "MMMM", "es-AR")
Trimestre = "T" & QUARTER([Date])
Semana = WEEKNUM([Date])
Día Semana = FORMAT([Date], "dddd", "es-AR")
```

**¿Por qué `"es-AR"` como locale?**

Para que los nombres de meses y días aparezcan en español (enero, febrero… lunes, martes…) en los ejes del reporte, sin requerir traducciones manuales posteriores.

---

### 4. Power Query (M) — transformación antes del modelo, no durante

**¿Por qué transformar en Power Query y no en DAX?**

La regla general en Power BI es: **transformaciones de datos en Power Query, cálculos de negocio en DAX**. Power Query opera en el momento de la carga — transforma una sola vez y almacena el resultado limpio en el modelo. DAX opera en el momento de la consulta — si la limpieza se hace en DAX, se recalcula en cada interacción del usuario.

Ejemplo concreto: normalizar los nombres de sucursal (`"DOT"` vs `"Dot"` vs `"dot shopping"`) es una transformación que debe hacerse en Power Query, no con un `SWITCH` en DAX. El resultado es el mismo, pero el primero lo calcula una vez; el segundo, miles de veces.

---

## 🗂️ Modelo de datos: diseño y decisiones

```
                    ┌──────────┐
                    │  Género  │
                    └────┬─────┘
                         │
┌──────────┐    ┌────────┴──────────┐    ┌───────────┐
│ Vendedor │────│                   │────│ Sucursal  │
└──────────┘    │      VENTAS       │    └───────────┘
                │   (tabla hechos)  │
┌──────────┐    │                   │    ┌───────────────┐
│ Producto │────│  Fecha (FK→Cal.)  │────│ Tabla Calend. │
└────┬─────┘    │  IdProducto       │    └───────────────┘
     │          │  IdSucursal       │
┌────┴───┐      │  IdVendedor       │    ┌──────────────┐
│ Marca  │      │  IdMetodoPago     │────│ Método Pago  │
└────────┘      │  IdEntidad        │    └──────────────┘
                │  Facturado        │
                │  Rentabilidad     │    ┌──────────┐
                │  Cantidad         │────│ Entidad  │
                └───────────────────┘    └──────────┘
```

**Decisiones de cardinalidad:**

- Todas las relaciones son 1:N (dimensión → hechos), dirección de filtro de dimensión a hechos
- No hay relaciones bidireccionales — en Power BI, las relaciones bidireccionales pueden generar ambigüedades en el filtrado cruzado y degradar performance. Se evitan salvo necesidad explícita
- `Marca` se conecta a `Producto` (no directamente a `Ventas`) porque la marca es un atributo del producto, no de la transacción. Esto es un mini snowflake justificado: evita duplicar el campo `Marca` en miles de filas de `Ventas`

---

## 🔄 Pipeline de datos: del dataset sucio al modelo limpio

Todo el proceso de limpieza y transformación se realizó en **Power Query (M)** antes de cargar al modelo. Se documenta cada transformación y su justificación.

---

### Problema 1: Nombres de sucursal inconsistentes entre fuentes

Las tablas de ventas de distintas sucursales usaban distintas grafías para el mismo local: `"DOT"`, `"Dot Shopping"`, `"dot"`, `"Centro DOT"`.

**Transformación en Power Query:**

```m
= Table.ReplaceValue(
    Source,
    each [Sucursal],
    each
        if Text.Contains(Text.Upper([Sucursal]), "DOT") then "DOT"
        else if Text.Contains(Text.Upper([Sucursal]), "NORDELTA") then "Nordelta"
        else if Text.Contains(Text.Upper([Sucursal]), "ARCOS") then "Distrito Arcos"
        else if Text.Contains(Text.Upper([Sucursal]), "UNICENTER") then "Unicenter"
        else [Sucursal],
    Replacer.ReplaceValue,
    {"Sucursal"}
)
```

**¿Por qué `Text.Upper()` antes de comparar?**

Para que la comparación sea case-insensitive. Sin esto, `"dot"` y `"DOT"` pasarían la condición diferente. La normalización a mayúsculas antes del `Contains` hace la lógica robusta ante cualquier variante de capitalización futura.

---

### Problema 2: Precios y montos con formato mixto

Los datos de facturado y rentabilidad venían con formatos inconsistentes: algunos registros con punto decimal (`1234.56`), otros con coma decimal (`1234,56`), y algunos con separador de miles (`1.234,56`).

**Transformación en Power Query:**

```m
= Table.TransformColumns(Source, {
    {"Facturado", each
        Number.From(
            Text.Replace(
                Text.Replace(
                    Text.Replace(Text.From(_), "$", ""),
                    ".", ""
                ),
                ",", "."
            )
        ),
        type number
    }
})
```

**¿Por qué esta secuencia específica?**

El orden importa: primero se elimina el símbolo `$`, luego los puntos de miles, y finalmente se convierte la coma decimal a punto. Si se invirtiera el orden (primero coma → punto, luego eliminar puntos), se eliminaría también el punto decimal recién creado.

---

### Problema 3: Fechas en formato texto no parseables

Algunas fechas venían como texto en formato argentino `"15/03/2024"` y otras en formato ISO `"2024-03-15"`. Power BI no puede crear relaciones con una columna de fecha que tenga tipo `Text` — la relación con la tabla calendario requiere tipo `Date`.

**Transformación en Power Query:**

```m
= Table.TransformColumnTypes(
    Source,
    {{"Fecha", type date}},
    "es-AR"
)
```

El parámetro `"es-AR"` le indica a Power Query que interprete el formato de fecha con la convención argentina (día/mes/año), evitando que `"15/03/2024"` sea parseado como mes 15 (inválido) o como 3 de marzo en lugar del 15.

---

### Problema 4: Valores nulos en columna Rentabilidad

Los registros de devoluciones y ajustes no tenían valor en la columna `Rentabilidad` — aparecían como `null`. Un `SUM` de DAX sobre una columna con nulos los ignora correctamente, pero un `AVERAGE` los excluye del denominador, lo que sesga el promedio hacia arriba artificialmente.

**Transformación en Power Query:**

```m
= Table.ReplaceValue(Source, null, 0, Replacer.ReplaceValue, {"Rentabilidad"})
```

**¿Por qué reemplazar null por 0 y no eliminar las filas?**

Eliminar las filas de devoluciones distorsionaría el conteo de transacciones y el cálculo de ticket promedio. Un `0` en rentabilidad para una devolución es semánticamente correcto: la operación no generó rentabilidad positiva ni negativa neta en ese registro.

---

### Problema 5: Productos sin categoría de género asignada

Algunos productos del catálogo (accesorios, calzado unisex) no tenían asignado un género en la tabla `Producto`. Sin esto, los filtros por género en el reporte excluían esos productos silenciosamente.

**Transformación:**

```m
= Table.ReplaceValue(
    Source,
    null,
    "Sin especificar",
    Replacer.ReplaceValue,
    {"Género"}
)
```

**¿Por qué `"Sin especificar"` y no eliminar?**

Eliminar los productos sin género haría que el total de ventas no cuadrara con el total filtrado por género, confundiendo al usuario del reporte. `"Sin especificar"` es visible en los slicers, lo que también es una señal para el equipo de datos de que hay productos a corregir en el catálogo fuente.

---

### Problema 6: Duplicados en la tabla de productos

El catálogo de productos tenía entradas duplicadas para el mismo SKU con nombres ligeramente distintos (`"Campera Urbana Negra"` vs `"Campera urbana negra"`). Los duplicados en una tabla de dimensión rompen el conteo de productos únicos y generan doble conteo en los slicers.

**Detección en Power Query:**

```m
= Table.Distinct(Source, {"CódigoProducto"})
```

**Antes de deduplicar:** se ordenó la tabla por fecha de última modificación descendente, para que `Table.Distinct` conserve el registro más reciente en caso de conflicto entre dos versiones del mismo producto.

---

## 📐 Medidas DAX y su justificación

Las medidas DAX son el corazón analítico del reporte. Se documenta no solo qué hace cada medida sino **por qué se calculó así** y qué error evita.

### Facturación Total

```dax
Facturación Total = SUM(Ventas[Facturado])
```

Medida base. Se define como medida explícita (no columna calculada) para que responda al contexto de filtro activo en cada visual — sucursal, período, producto. Una columna calculada se evalúa en el momento de la carga y no responde a filtros de reporte.

### Rentabilidad %

```dax
Rentabilidad % =
DIVIDE(
    SUM(Ventas[Rentabilidad]),
    SUM(Ventas[Facturado]),
    0
)
```

**¿Por qué `DIVIDE()` y no el operador `/`?**

`DIVIDE(numerador, denominador, resultado_alternativo)` maneja automáticamente la división por cero retornando el tercer argumento (0 en este caso). El operador `/` genera un error `BLANK()` o una excepción cuando el denominador es 0 — lo que ocurre, por ejemplo, si se filtra un período sin ventas. Con `DIVIDE`, el visual muestra 0% en lugar de un error o espacio vacío.

### Ticket Promedio

```dax
Ticket Promedio =
DIVIDE(
    [Facturación Total],
    [Cant. de Ventas],
    0
)
```

**¿Por qué no usar `AVERAGE(Ventas[Facturado])`?**

`AVERAGE` calcula el promedio de la columna en el contexto de filtro, lo que es correcto. Sin embargo, definirlo como `Facturación Total / Cant. de Ventas` usando medidas ya definidas hace la lógica más explícita y auditabe: si `Facturación Total` o `Cant. de Ventas` cambia su definición en el futuro, `Ticket Promedio` se actualiza automáticamente.

### Variación vs. Período Anterior

```dax
Facturación Período Anterior =
CALCULATE(
    [Facturación Total],
    DATEADD('Tabla Calendario'[Date], -1, MONTH)
)

Variación Facturación =
VAR facturacionActual = [Facturación Total]
VAR facturacionAnterior = [Facturación Período Anterior]
RETURN
    DIVIDE(
        facturacionActual - facturacionAnterior,
        facturacionAnterior,
        BLANK()
    )
```

**¿Por qué `BLANK()` como resultado alternativo en lugar de 0?**

Si no hay período anterior (primer mes de datos), retornar 0% de variación es engañoso — implicaría que no hubo cambio, cuando en realidad no hay base de comparación. `BLANK()` hace que el visual no muestre ningún valor, que es la respuesta semánticamente correcta: "no se puede calcular la variación sin período anterior".

**¿Por qué `DATEADD` y no `SAMEPERIODLASTYEAR`?**

`SAMEPERIODLASTYEAR` compara contra el mismo mes del año anterior. Para un análisis mensual de corto plazo (mes a mes), `DATEADD -1 MONTH` es más relevante. Si el reporte tuviera foco en análisis año contra año, `SAMEPERIODLASTYEAR` sería la elección correcta.

### Ranking de Vendedores

```dax
Ranking Vendedor =
RANKX(
    ALL(Vendedor[Nombre]),
    [Facturación Total],
    ,
    DESC,
    DENSE
)
```

**¿Por qué `DENSE` y no el modo por defecto (`SKIP`)?**

`SKIP` genera rankings como 1, 2, 4, 4, 6 cuando hay empates (salta el 3 y el 5). `DENSE` genera 1, 2, 3, 3, 4. Para un ranking de vendedores presentado en el reporte, `DENSE` es más legible: dos vendedores empatados en el puesto 3 no hacen que el siguiente sea el 5.

---

## 🤔 ¿Por qué este camino y no otro?

### Alternativa descartada: Excel con tablas dinámicas

Las tablas dinámicas de Excel pueden responder las mismas preguntas de negocio que este dashboard. El límite está en la interactividad: un usuario no técnico no puede explorar libremente una tabla dinámica sin riesgo de romperla. Power BI separa el desarrollo del reporte del consumo — el usuario final navega sin posibilidad de alterar el modelo.

Además, un reporte en Excel con 8 hojas y datos de 4 sucursales se vuelve lento e inmanejable a medida que crece el volumen de datos. Power BI con VertiPaq comprime y agrega eficientemente millones de filas.

### Alternativa descartada: Python (Pandas + Matplotlib/Seaborn)

Python con Pandas permite análisis más profundos (estadísticas descriptivas, modelos predictivos, detección de anomalías) pero produce resultados estáticos — gráficos que no responden a filtros del usuario. Para el caso de uso específico de este reporte (exploración interactiva por sucursal, período y producto por parte del equipo directivo), la interactividad de Power BI es un requisito no negociable.

Python sería la elección correcta en la siguiente capa: después de identificar un patrón en Power BI ("la sucursal Nordelta tiene caída de rentabilidad en Q3"), Python permitiría profundizar con análisis de regresión o clustering para entender las causas.

### Alternativa descartada: Google Looker Studio

Looker Studio tiene la ventaja de ser gratuito y colaborativo sin necesidad de Power BI Service. El límite para este proyecto: no tiene un motor de cálculo equivalente a DAX. Las medidas de inteligencia de tiempo (`DATEADD`, `SAMEPERIODLASTYEAR`), los rankings dinámicos (`RANKX`) y el control fino de contexto de filtro (`CALCULATE`, `ALL`, `ALLEXCEPT`) no tienen equivalente directo en Looker Studio. Para un reporte que requiere estas capacidades, Power BI no tiene competencia real en el tier de costo accesible.

---

## 📊 Páginas del reporte

| Página | Descripción | KPIs principales |
|--------|-------------|-----------------|
| **Portada** | Presentación visual de la marca y navegación | — |
| **Tablero General** | Vista consolidada de toda la red | Facturación Total, Rentabilidad %, Ticket Promedio, Cant. Ventas |
| **DOT** | Análisis detallado sucursal DOT | Ventas por vendedor, por producto, por método de pago |
| **Nordelta** | Análisis detallado sucursal Nordelta | Ídem DOT |
| **Distrito Arcos** | Análisis detallado sucursal Distrito Arcos | Ídem DOT |
| **Unicenter** | Análisis detallado sucursal Unicenter | Ídem DOT |
| **Conclusión** | Síntesis de hallazgos y recomendaciones | Comparativa entre sucursales |
| **Conclusión (bis)** | Análisis complementario y cierre | Tendencias y oportunidades |

**Decisión de diseño: una página por sucursal**

Se eligió una página dedicada por sucursal (en lugar de un único tablero con slicer de sucursal) para facilitar la navegación del gerente de cada local: abre el reporte y va directamente a "su" página sin necesidad de filtrar. El tablero general mantiene la visión consolidada para la dirección. Ambos niveles de detalle coexisten sin sacrificar ni la granularidad ni la síntesis.

---

## 📁 Estructura del repositorio

```
/
├── ENTREGA 1 - ORELLANA.pdf         # Primera entrega: definición del caso y modelo inicial
├── ENTREGA 2 - ORELLANA.pdf         # Segunda entrega: modelo relacional y primeras medidas
├── Entrega 3 - ORELLANA (F).pbit    # Template final Power BI (sin datos, solo estructura)
├── Entrega 3 - ORELLANA (F).pdf     # Export PDF del reporte final completo
└── README.md
```

**¿Por qué `.pbit` y no `.pbix`?**

El archivo `.pbix` incluye los datos embebidos — podría contener información sensible del negocio (precios reales, nombres de clientes, volúmenes de venta). El `.pbit` es la plantilla del reporte: incluye el modelo, las medidas DAX y el diseño visual, pero **sin datos**. Es la forma correcta de publicar un reporte de negocio en un repositorio público.

---

## 🧩 Roadmap

| Prioridad | Mejora | Justificación |
|-----------|--------|---------------|
| Alta | 🔄 Conexión a fuente de datos en tiempo real | Actualmente el reporte se actualiza manualmente; conectar a SQL Server o SharePoint automatizaría el refresh |
| Alta | 📈 Análisis de tendencia y proyección | Agregar líneas de tendencia y forecasting con medidas DAX o integración con Azure ML |
| Media | 👤 Row-Level Security (RLS) | Cada gerente de sucursal debería ver solo los datos de su local en Power BI Service |
| Media | 🐍 Capa de análisis avanzado en Python | Detección de anomalías y clustering de productos por rentabilidad/rotación |
| Baja | 📱 Layout para mobile | Diseño optimizado para consumo del reporte desde celular |

---

## 👨‍💻 Autor

**Juan Manuel Orellana**
Data Science · Analytics · Business Intelligence

---

## 📄 Licencia

Proyecto académico desarrollado en el curso de Power BI — Coderhouse 2025.
Los datos utilizados son ficticios y fueron creados con fines educativos.
