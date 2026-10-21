# CLAUDE.md — Vacaciones del personal (vacaciones)

Las reglas generales de trabajo están en el repo privado `lives-instrucciones` (CLAUDE.md). Este archivo solo describe esta herramienta.

## Qué hace
`index.html` es un **tablero de solo lectura** del control de períodos vacacionales por trabajador: días ganados sin gozar, urgentes, por cumplir y adelantos. No registra solicitudes ni aprobaciones, no sube Excel y no tiene calendario ni solapamientos por área.
- El estado de cada período y su valor pendiente **los calcula la base** (funciones `vac_periodos` y `vac_estado`); la pantalla solo agrega y dibuja. Regla de la base: 30 días menos lo gozado según el reporte del sistema.
- Estados: URGENTE (venció el plazo, un año después de cumplido el período), CUMPLIO (ganado y aún a tiempo), VA_A_CUMPLIR (se completa este año), ADELANTO (días gozados antes de ganar el período), PAGADO y EN_CURSO.
- Vistas: "Calculado" (una columna por período `año-año+1` y "Pendiente ganado"; solo muestra los años donde algún trabajador filtrado tiene un período con pendiente o adelanto; las celdas se colorean por estado) y "Reporte del sistema" (días gozados por año, tal cual vienen).
- Filtros: búsqueda (sin tildes ni mayúsculas, sobre nombre y código), centro de costo, tipo de trabajador (obrero o empleado; se oculta si hay uno solo) y estado.
- KPIs (4 tarjetas; al tocar una, la tabla muestra solo a quienes están en ella), calculados sobre búsqueda, centro de costo y tipo, **sin** el filtro de estado:
  - Días ganados sin gozar = suma de los días pendientes (períodos URGENTE y CUMPLIO).
  - Urgentes = trabajadores con algún período URGENTE.
  - Van a cumplir este año = trabajadores con algún período VA_A_CUMPLIR.
  - Con adelanto = trabajadores con algún período ADELANTO.
- Gráfico de barras apiladas "Días pendientes por centro de costo" (urgente, cumplió, va a cumplir; ordenado por el total) y lista "Próximos en vencer" (los 10 períodos CUMPLIO con la fecha límite más cercana).
- Ficha por trabajador: resumen y tabla por período (días gozados, pendiente, estado, fecha en que se cumple y fecha límite).
- Regla 500 en la tabla (con "Mostrar más"). Imprime en A4 horizontal.
- Excel LIVES: Resumen (KPIs, por centro de costo y próximos en vencer) y Detalle (un período por año, con colores y leyenda); incluye la vista del reporte del sistema.

## Datos personales
Por ser datos de personas, el Excel y las capturas se manejan solo para el área que corresponde. No hay datos de personas dentro del código: llegan en el momento desde la base.

## Base de datos (Supabase lives-datos)
- Una sola puerta: `vac_api(p_accion, p)`; la pantalla usa solo la acción `datos`, que devuelve trabajadores, períodos con su estado, días gozados por año, años mínimo y máximo y la fecha de hoy.
- Tablas: `vac_trabajadores` (código, tipo, nombre, centro de costo, ocupación, fecha de ingreso), `vac_dias` (días gozados por trabajador y año), `vac_cargas` (cada carga: archivo, años, cantidad de trabajadores y de celdas) y `vac_ajustes` (parámetros).
- Funciones de cálculo: `vac_periodos`, `vac_estado` y `vac_aniversario`.
- No usa Storage ni escribe nada.

## Quién la usa
No aparece en el registro de entradas del portal de los últimos 60 días (al 21/10/2026).

## Quién puede ver
Solo lectura; la pantalla no tiene permisos propios.

## Pendientes vistos al escribir esto (21/10/2026)
- Los datos se cargaron a la base desde el reporte del sistema de personal (queda constancia en `vac_cargas`), pero el proceso de carga no está en este repo. Para el paso a SQL Server, ese reporte debería salir directo del sistema de personal.
- Los KPI y el gráfico se calculan sobre filtros distintos (los KPI no usan el estado; el gráfico sí): puede parecer que no coinciden al filtrar por estado.
