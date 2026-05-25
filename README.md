# Gestión de Mantenciones Preventivas 2026 — HHHA

Aplicación HTML standalone para gestionar el programa de mantenciones preventivas (MP) de equipos biomédicos del Hospital Hernán Henríquez Aravena.

## Uso

1. Descargue `index.html` y ábralo con doble clic en cualquier navegador moderno (Chrome, Edge, Firefox).
   - No requiere servidor ni instalación.
   - Sí requiere conexión a internet la primera vez (carga Tailwind, SheetJS y Lucide desde CDN).
2. Haga clic en **Cargar archivo maestro** y seleccione `Programacion_MP_2026.xlsm`.
3. Verifique los conteos en la vista **Verificación de carga** y compare contra el archivo Excel original abierto en paralelo.
4. Navegue las distintas vistas usando el menú lateral.

## Vistas

| Vista | Descripción |
|---|---|
| **Programación PMP** | Tabla completa de PMP_2026 (solo lectura). Códigos `X`, `R`, `RA`, `PM` coloreados. Filtros por servicio, familia, clasificación y frecuencia. Buscador local. |
| **Registro de resultados** | Espejo de la hoja `Registro_MP-2026` con sub-columnas P (programación) y R (resultado). Tooltip con descripción de las causales C1–C8. |
| **Pendientes** | Listado global de pendientes con filtros por estado, ejecutor y vencidos. Botón para cerrar/reabrir. |
| **Verificación de carga** | Conteos de filas, equipos válidos, slots vacíos, distribución por familia, códigos PMP × mes, resultados × mes e inconsistencias. |

## Ficha del equipo

Al hacer clic sobre cualquier fila de las vistas PMP o Registro se abre la ficha del equipo, con:

- Datos de identificación.
- Tabla de programación y resultado por mes.
- Lista de registros asociados (mantenciones, envíos a ST, solicitudes, recepciones, reparaciones).
- Pendientes del equipo con checkbox para cerrar/reabrir.

Desde la ficha se pueden crear **Nuevos registros** y **Nuevos pendientes**.

## Tipos de evento

| Tipo | Campos clave |
|---|---|
| Mantención preventiva | fecha, resultado (Si/C1–C8/FS/Baja/NU), ejecutor, estado del equipo, observación |
| Envío a servicio técnico | fecha envío, N° envío, empresa, ejecutor, folio, estado, comentario |
| Solicitud de trabajo | fecha, ejecutor, folio, estado (no operativo), comentario |
| Recepción | fecha, N° envío, folio guía de despacho, estado |
| Reparación | fecha, estado del equipo |

## Persistencia y respaldo

- Los **eventos** y **pendientes** se guardan automáticamente en el `localStorage` del navegador (clave `mp_app_state_v1`).
- Los datos del Excel **no** se persisten; se carga en memoria en cada sesión.
- Use **Exportar JSON** para descargar un respaldo de eventos y pendientes.
- Use **Importar JSON** para restaurar un respaldo (reemplaza los datos actuales).

## Causales de reprogramación

- **C1** Imposibilidad de desocupar el equipo (indicación clínica).
- **C2** Equipo en servicio técnico.
- **C3** Equipo no operativo a la espera de repuestos.
- **C4** Equipo en préstamo a otro hospital o institución.
- **C5** No disponibilidad de horas SEC por carga laboral.
- **C6** No disponibilidad de horas del servicio técnico externo.
- **C7** Ausencia justificada del funcionario SEC > 15 días.
- **C8** Contingencia hospitalaria.

Reglas operativas (informativas): C2/C3/C4 → sin fecha, se registra en el mes real de ejecución. C1/C5/C6/C7/C8 → reprogramar dentro de 30 días.

## Notas técnicas

- Stack: HTML + JavaScript vanilla, Tailwind CSS (CDN), SheetJS (CDN), Lucide icons (CDN).
- Lee dos hojas: `PMP_2026` (encabezado fila 7) y `Registro_MP-2026` (encabezado fila 7, columnas T-AQ con sub-columnas P/R).
- Las columnas Q (Observación) y S (Responsable MP) del archivo maestro se ignoran a propósito.
- Se conservan los slots vacíos (filas con ID pero sin equipo).
- Las tablas usan virtualización para mantener fluidez con ~1000 filas.
