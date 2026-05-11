---
name: nomina-liquidacion
display_name: Liquidación de Nómina (Arsys)
owner: ferney
domain: Nómina
modulo: Liquidación de Nómina
ambito: Funcional + Técnico + Lógica de cálculo
fuente: Documento "LiquidacionNomina.pdf" (módulo Nómina, BD ArsysNominaORF)
descripcion: Skill empresarial consolidada para soporte funcional, técnico y de cálculo del módulo Liquidación de Nómina. Cubre el formulario, los estados (Borrador / Pasar Revisión / Definitiva), las tablas Nomina.Liquidacion, Nomina.LiquidacionDetalle y la tabla temporal Nomina.TmpLiquidacionNomina, y la secuencia de 10 procedimientos almacenados que componen el proceso de liquidación.
---

# Skill: Nomina - Liquidacion de Nomina

## Propósito

Servir como **fuente única de verdad** del módulo **Liquidación de Nómina** del sistema de Nómina de Arsys: documentar el formulario, los estados del ciclo (Borrador → Pasar Revisión → Definitiva), la convención de **periodo** (`AAAAMMNN`), el modelo de datos (`Nomina.Liquidacion`, `Nomina.LiquidacionDetalle`, `Nomina.TmpLiquidacionNomina`), y la **secuencia de 10 procedimientos almacenados** que ejecutan el cálculo de la nómina, así como las reglas legales aplicadas (Subsidio de Transporte ≤ 2 SMLV, Retención en la Fuente > 4 SMLV).

Esta Skill debe permitir a un agente IA (y a un humano) responder preguntas funcionales, técnicas, de seguridad y de cálculo sobre el proceso de liquidación, y guiar al usuario en su ejecución segura.

---

## Contexto funcional

### Propósito del módulo

Liquidar la **nómina** de los empleados de la organización por **tipo de nómina** (ej. `0001 - ADMINISTRACION`, Producción) para un **periodo de pago** específico, dando a conocer ese periodo en la próxima nómina de pago.

### Alcance

- Liquidar **toda la organización** o un **subconjunto de empleados** identificados por cédula.
- Manejar la liquidación en tres **estados**: Borrador, Pasar Revisión, Definitiva.
- Generar el **MOVDAT** contable y de tesorería a partir de la liquidación (pestañas adicionales del formulario).
- Visualizar **acumulados** del proceso.

### Objetivo de negocio

- Calcular el **pago real** que recibirá cada empleado en el periodo, considerando devengados (pagos) y deducciones (descuentos), novedades, créditos, vacaciones, subsidio de transporte y retención en la fuente.
- Asegurar **revisión fiscal** previa antes de pasar a definitiva.
- Garantizar **trazabilidad** y **reproducibilidad** del proceso.

### Actores involucrados

| Actor | Rol |
|---|---|
| Operador de Nómina (RRHH) | Ejecuta la liquidación en **Borrador**, valida resultados, pasa a **Revisión**. |
| Revisor Fiscal | Revisa los valores liquidados, da visto bueno para pasar a **Definitiva**. |
| Recursos Humanos (aprobador) | Junto con el Revisor Fiscal aprueba el paso a **Definitiva**. |
| Contabilidad | Consume el MOVDAT Contabilidad generado a partir de la liquidación. |
| Tesorería | Consume el MOVDAT Tesorería para la dispersión de pagos. |

### Procesos principales

1. **Preparar** el periodo (Tipo Nómina, fechas, dependencia, opcionalmente cédulas específicas).
2. **Liquidar en Borrador** → llena `Nomina.TmpLiquidacionNomina` (por usuario) ejecutando la secuencia de 10 procedimientos almacenados.
3. **Validar** resultados (pestaña **Mensajes Liquidación**).
4. **Pasar a Revisión** → revisor fiscal valida.
5. **Pasar a Definitiva** → datos se persisten desde la temporal a `Nomina.Liquidacion` / `Nomina.LiquidacionDetalle`.
6. **Generar MOVDAT** contable y de tesorería.
7. **Visualizar acumulados** del periodo.

### Ciclo de liquidación

```
   Preparar periodo
        │
        ▼
   [BORRADOR]  ── Recalcular ──► [BORRADOR]  (cuantas veces se necesite)
        │
        ▼
   [PASAR REVISIÓN]  ◄── Revisor fiscal evalúa
        │
        ▼
   [DEFINITIVA]  ── Generar MOVDAT Contabilidad / Tesorería ──► Pago
```

### Convención del Periodo

- Formato `AAAAMMNN` (8 caracteres). Ej. `20240301`.
- `AAAA` = año, `MM` = mes, `NN` = secuencia de quincena (`01` = primera, `02` = segunda).
- En `Nomina.Liquidacion`, **`periodo` (varchar(8))** y **`secuencia` (char(2))** documentan este concepto.

---

## Formularios y pantallas

### Pantalla principal — Liquidación Nómina

Encabezado: **Arsys Nómina**.
Barra superior: **Nuevo, Buscar, Refrescar, Manuales, Ayuda, Salir**.

#### Cabecera (cabecera fija de la pantalla)

| Campo | Tipo | Obligatorio | Editable | Regla | Observación |
|---|---|---|---|---|---|
| Unidad Negocio | Combo | Sí | Sí | | |
| Tipo Nómina | Combo | Sí | Sí | Ej. `0001 - ADMINISTRACION` | Distingue Administración vs. Producción, etc. |

#### Pestañas principales

| Pestaña | Propósito |
|---|---|
| **Liquidación Nómina** | Ejecutar y monitorear el proceso de liquidación. |
| **Generar MOVDAT Contabilidad** | Generar el archivo de movimientos contables a partir de la liquidación definitiva. |
| **Generar MOVDAT Tesorería** | Generar el archivo de movimientos de tesorería para dispersión. |
| **Visualizar Acumulados** | Consultar acumulados del periodo. |

#### Sección "Liquidación Nómina" (pestaña activa por defecto)

| Campo | Tipo | Obligatorio | Editable | Regla | Observación |
|---|---|---|---|---|---|
| Centro Económico | Combo | Sí | Sí | | |
| UEN Núm. Cancelado | Texto/Combo | [REQUIERE_VALIDACION] | Sí | | |
| Periodo | Fecha | Sí | Sí | Periodo de la quincena | Ej. `15/02/2024` |
| Dependencia | Combo | No | Sí | Si se llena, filtra a esa dependencia | |
| F. Inicio Nómina | Fecha | Sí | Sí | Fecha inicial del pago | Ej. `16/02/2024` |
| Fecha Final | Fecha | Sí | Sí | Fecha final del pago | Ej. `29/02/2024` |
| Estado | Combo | Sí | Sí (según permisos) | Valores: **Borrador**, **Pasar Revisión**, **Definitiva** | Es el estado de la liquidación |
| Mostrar Empleados | Radio | Sí | Sí | Sí / No | Si Sí, muestra el grid de empleados |
| Cédula | Texto multivalor | No | Sí | Lista de cédulas separadas por **punto y coma `;`** | Para liquidar solo a esos empleados (modo prueba). |
| Importar Datos Usuarios | Checkbox | No | Sí | | [REQUIERE_VALIDACION] sobre su semántica exacta |

#### Sub-pestañas dentro de "Liquidación Nómina"

| Sub-pestaña | Propósito |
|---|---|
| **Liquidación** | Grid con los empleados liquidados. |
| **Mensajes Liquidación** | Logs / errores / avisos del proceso. |

#### Grid "Liquidación"

Columnas visibles: **Cédula, Empleado, Unidad Negocio, Centro Económico, Dependencia**.

> El grid se llena tras ejecutar la liquidación; cada renglón representa un empleado liquidado en `Nomina.TmpLiquidacionNomina` (cuando está en Borrador) o `Nomina.Liquidacion` (Definitiva).

---

## Permisos y acciones

> **El PDF no documenta permisos atómicos por control para el módulo de Liquidación de Nómina.** A continuación se listan las acciones funcionales detectadas; los permisos formales que las gobiernan deben confirmarse contra el modelo de seguridad de la aplicación.

| Control / Acción | Acción funcional | Rol sugerido | Permiso |
|---|---|---|---|
| Botón **Nuevo** | Iniciar una nueva liquidación | Operador Nómina | [REQUIERE_VALIDACION] |
| Botón **Buscar** | Buscar liquidaciones existentes | Operador / Auditor | [REQUIERE_VALIDACION] |
| Botón **Refrescar** | Refrescar el grid de empleados liquidados | Operador | [REQUIERE_VALIDACION] |
| Cambiar Estado → **Borrador** | Recalcular en pruebas | Operador Nómina | [REQUIERE_VALIDACION] |
| Cambiar Estado → **Pasar Revisión** | Enviar a revisor fiscal | Operador Nómina | [REQUIERE_VALIDACION] |
| Cambiar Estado → **Definitiva** | Cerrar liquidación; persiste a tablas finales | RRHH + Revisor Fiscal | [REQUIERE_VALIDACION] |
| Pestaña **Generar MOVDAT Contabilidad** | Generar movimientos contables | Contabilidad | [REQUIERE_VALIDACION] |
| Pestaña **Generar MOVDAT Tesorería** | Generar movimientos de tesorería | Tesorería | [REQUIERE_VALIDACION] |
| Pestaña **Visualizar Acumulados** | Consultar acumulados | Auditoría / Contabilidad | [REQUIERE_VALIDACION] |

---

## Reglas de negocio

### Reglas explícitas (presentes en el PDF)

1. **La liquidación se ejecuta por Tipo de Nómina** (Administración, Producción, etc.).
2. **La liquidación da a conocer el periodo de pago** en la próxima nómina.
3. **Se puede liquidar como prueba** uno o varios empleados digitando sus **cédulas separadas por punto y coma `;`**.
4. **Estados válidos de la liquidación**:
   - **Borrador**: pruebas sobre las novedades del empleado.
   - **Pasar Revisión**: liquidación enviada al revisor fiscal para validación.
   - **Definitiva**: aprobada por RRHH y Revisor Fiscal; es lo que se pagará al empleado.
5. **La liquidación se realiza inicialmente por usuario en `Nomina.TmpLiquidacionNomina`** antes de pasar los datos a las tablas principales (`Nomina.Liquidacion` y `Nomina.LiquidacionDetalle`).
6. **Periodo** se compone de `AAAA + MM + NN` donde `NN` es la secuencia de quincena (`01` o `02`). Ej. `20240301`.
7. **Auxilio de transporte**: la bandera se lee de `Tercero.EmpleadoSociedadNomina.auxilioTransporte`. Si está en `1`, se ejecuta el cálculo de subsidio de transporte.
8. **Regla legal — Subsidio de Transporte**: si el Total de Pago del Empleado en el mes **supera los 2 SMLV**, **se resta** el Subsidio de Transporte ya pagado en la primera quincena (`spLiquidacionNominaRestarSubsidioTT`).
9. **Regla legal — Retención en la Fuente**: aplica al empleado siempre que su pago **supere los 4 SMLV** según ley (`spLiquidacionNominaRetencionFuente`).
10. **Ajuste de Febrero**: el procedimiento `spActualizarNovedadIncapacidadSegundaQuincenaFebrero` aplica a las quincenas de febrero para ajustar las diferencias entre los 28/29 días reales y los **30 días de pago en liquidación** establecidos. Se ejecuta para que se tome toda la quincena en las incapacidades.
11. **Créditos del empleado** (por libranzas) se recalculan antes de liquidar (`spRecalcularSaldosCreditosEmpleadosArsys`).
12. **Préstamos** del empleado con la organización (incluidos los relacionados con sus fondos) se verifican y recalculan durante la liquidación (`spLiquidacionNominaPrestamosArsys`).
13. **Días de vacaciones** dentro de la quincena se restan de los días laborados (`spLiquidacionNominaRestaDiasVacacionesArsys`).
14. **Los SPs retornan `9999`** ante error; el flujo se interrumpe (`IF @Retorno <> 0 BEGIN return 9999; END`).

### Reglas implícitas / inferidas (marcar para validación)

- **Un mismo `(idContrato, periodo, secuencia)` no debería poder liquidarse dos veces en Definitiva** — [REQUIERE_VALIDACION].
- **Un empleado con `Bloqueo Pago = Sí`** (en EmpleadoSociedadNomina) **no entra a la liquidación** — [REQUIERE_VALIDACION].
- **Un empleado sin contrato activo** no debería liquidarse — [REQUIERE_VALIDACION].
- **Una vez en Definitiva**, el periodo queda **cerrado** y no debería poder modificarse — [REQUIERE_VALIDACION].
- **Convención global de conceptos** del sistema (de Skills anexas): `P*` = pagos, `D*` = descuentos. **No aparece en este PDF**, pero es coherente con `LiquidacionDetalle.idConceptoNomina`.
- **Sincronización con CXP**: el campo `sincronizadoCXP (bit)` sugiere que la liquidación se sincroniza con Cuentas por Pagar — [REQUIERE_VALIDACION].

---

## Modelo de datos

### Tablas principales (esquema `Nomina`)

#### `Nomina.Liquidacion` (cabecera por contrato / periodo)

| Columna | Tipo | Nulos | Rol |
|---|---|---|---|
| `idLiquidacion` | `bigint` | NO | PK |
| `idContrato` | `int` | SÍ | FK → `Nomina.Contrato` |
| `idContratoDetalle` | `int` | SÍ | FK → `Nomina.ContratoDetalle` |
| `sueldoBase` | `numeric(18,0)` | NO | Sueldo base usado para el cálculo |
| `periodo` | `varchar(8)` | NO | Formato `AAAAMMNN` |
| `secuencia` | `char(2)` | NO | Secuencia de quincena (`01` / `02`) |
| `observaciones` | `varchar(50)` | SÍ | |
| `fechaInicial` | `date` | NO | Fecha inicial del periodo |
| `fechaFinal` | `date` | NO | Fecha final del periodo |
| `fechaInicioEmpleado` | `date` | SÍ | Inicio efectivo del empleado en el periodo |
| `fechaFinalEmpleado` | `date` | SÍ | Fin efectivo |
| `auxilioTransporte` | `bit` | NO | Indica si se paga subsidio de transporte |
| `fechaCreacion` | `datetime` | NO | Auditoría |
| `fechaModificacion` | `datetime` | NO | Auditoría |
| `idUsuarioCreacion` | `int` | NO | Auditoría |
| `idUsuarioModificacion` | `int` | NO | Auditoría |
| `idSesionUsuario` | `bigint` | NO | Auditoría |
| `idFormulario` | `int` | NO | Auditoría |
| `sincronizadoCXP` | `bit` | NO | Sincronización con Cuentas por Pagar — [REQUIERE_VALIDACION] |

> **Nota**: la columna `Estado` (Borrador / Pasar Revisión / Definitiva) **no es explícitamente visible** en el diagrama del PDF; debe estar persistida en alguna columna de esta tabla o en `TmpLiquidacionNomina` — [REQUIERE_VALIDACION].

#### `Nomina.LiquidacionDetalle` (detalle por concepto)

| Columna | Tipo | Nulos | Rol |
|---|---|---|---|
| `idLiquidacionDetalle` | `bigint` | NO | PK |
| `idLiquidacion` | `bigint` | NO | FK → `Liquidacion` |
| `idConceptoNomina` | `bigint` | NO | FK → `Nomina.ConceptoNomina` |
| `idNovedad` | `bigint` | SÍ | FK → `Nomina.Novedad` (si el detalle proviene de una novedad) |
| `cantidad` | `numeric(18,2)` | NO | Cantidad (horas, días, unidades) |
| `valor` | `numeric(18,2)` | NO | Valor del concepto |
| `valorBC` | `numeric(18,2)` | NO | Valor Base de Cálculo |
| `fechaCreacion` | `datetime` | NO | Auditoría |
| `fechaModificacion` | `datetime` | NO | Auditoría |
| `idUsuarioCreacion` | `int` | NO | Auditoría |
| `idUsuarioModificacion` | `int` | NO | Auditoría |
| `idSesionUsuario` | `bigint` | NO | Auditoría |
| `idFormulario` | `int` | NO | Auditoría |

#### `Nomina.TmpLiquidacionNomina` (temporal por usuario)

Tabla **temporal por usuario** usada como **borrador** del proceso de liquidación. Columnas detectadas en el código del PDF:

| Columna | Observación |
|---|---|
| `idTipoNomina` | Tipo de nómina que se está liquidando |
| `idEmpleadoSociedad` | Empleado liquidado |
| `idUsuario` | Usuario que ejecuta |
| `FechaCreacion` | Sello de tiempo de la última pasada |
| `CantidadTotalEmpleados` | Total a procesar |
| `EmpleadosProcesados` | Procesados |
| `Estado` | Texto del estado (ej. `'Borrador'`) |

> Otras columnas y la estructura completa **están Pendiente de documentar** — el PDF solo expone fragmentos en los `UPDATE`.

### Relaciones del diagrama

```
   Contrato (Nomina)            ──┐
                                  ├──► Nomina.Liquidacion ──1──N──► Nomina.LiquidacionDetalle
   ContratoDetalle (Nomina)     ──┤                                          │
                                  │                                          └──► Nomina.ConceptoNomina
   DependenciaEmpresaCargo      ──┤                                          │
                                  │                                          └──► Nomina.Novedad (opcional)
   Dependencia (Nomina)         ──┘

   Nomina.TmpLiquidacionNomina  (por usuario, alimenta a Liquidacion al pasar a Definitiva)
```

- `Liquidacion` **N ── 1** `Contrato`.
- `Liquidacion` **N ── 1** `ContratoDetalle`.
- `Liquidacion` **N ── 1** `DependenciaEmpresaCargo`.
- `Liquidacion` **N ── 1** `Dependencia`.
- `Liquidacion` **1 ── N** `LiquidacionDetalle`.
- `LiquidacionDetalle` **N ── 1** `ConceptoNomina`.
- `LiquidacionDetalle` **N ── 0..1** `Novedad`.

---

## Flujos funcionales

### Flujo 1 — Liquidar nómina en Borrador

**Entrada:**
- Unidad de Negocio, Tipo Nómina.
- Centro Económico, Dependencia (opcional).
- Periodo, F. Inicio Nómina, Fecha Final.
- Cédulas opcionales separadas por `;` (modo prueba).
- Estado = `Borrador`.

**Proceso:**
1. Operador llena la cabecera del formulario.
2. Botón **Nuevo** / acción de liquidar.
3. El sistema ejecuta la **secuencia de 10 SPs** (ver sección **Lógica de cálculo**) sobre `Nomina.TmpLiquidacionNomina` para el usuario actual.
4. Cada empleado del filtro se procesa; al final, `EmpleadosProcesados` queda igual a `CantidadTotalEmpleados` y `Estado = 'Borrador'`.

**Validaciones:**
- Periodo en formato `AAAAMMNN`.
- Fechas coherentes con el periodo.
- Si la quincena es de **Febrero**, se ejecuta el SP de ajuste por 28/29 días.

**Salida:**
- Datos en `Nomina.TmpLiquidacionNomina` con `Estado = 'Borrador'`.
- Mensajes de proceso en la sub-pestaña **Mensajes Liquidación**.

**Errores:**
- Si algún SP devuelve `<> 0`, el flujo retorna `9999` y la liquidación se interrumpe para ese empleado.

---

### Flujo 2 — Recalcular (volver a ejecutar Borrador)

**Entrada:** misma cabecera, o ajustes (cédulas distintas, dependencia distinta, nuevas novedades).

**Proceso:**
1. Ajustar novedades / créditos / vacaciones del empleado.
2. Volver a ejecutar la liquidación en Borrador → re-corre los 10 SPs.

**Salida:** `TmpLiquidacionNomina` actualizada con la nueva pasada (`FechaCreacion = FechaHoraActual`).

---

### Flujo 3 — Pasar a Revisión

**Entrada:** liquidación en Borrador validada por el operador.

**Proceso:**
1. Cambiar Estado → **Pasar Revisión**.
2. El revisor fiscal evalúa.

**Validaciones:** [REQUIERE_VALIDACION] (la fuente no detalla controles automáticos al pasar a Revisión).

**Salida:** liquidación visible para el revisor fiscal.

---

### Flujo 4 — Pasar a Definitiva

**Entrada:** liquidación en Pasar Revisión, aprobada por RRHH y Revisor Fiscal.

**Proceso:**
1. Cambiar Estado → **Definitiva**.
2. Los datos se **migran** desde `Nomina.TmpLiquidacionNomina` hacia `Nomina.Liquidacion` y `Nomina.LiquidacionDetalle`.

**Validaciones:**
- Aprobación por ambos roles — [REQUIERE_VALIDACION].
- Periodo no liquidado previamente para los mismos `idContrato / idContratoDetalle / periodo / secuencia` — [REQUIERE_VALIDACION].

**Salida:** registros oficiales en `Liquidacion` y `LiquidacionDetalle`. La liquidación queda lista para generar MOVDAT.

---

### Flujo 5 — Generar MOVDAT Contabilidad

**Entrada:** liquidación en Definitiva.

**Proceso:** pestaña **Generar MOVDAT Contabilidad** → genera el archivo de movimientos contables.

**Salida:** archivo MOVDAT contable. Detalle exacto **Pendiente de documentar**.

---

### Flujo 6 — Generar MOVDAT Tesorería

**Entrada:** liquidación en Definitiva.

**Proceso:** pestaña **Generar MOVDAT Tesorería** → genera el archivo para dispersión bancaria.

**Salida:** archivo MOVDAT tesorería. Detalle **Pendiente de documentar**.

---

### Flujo 7 — Visualizar Acumulados

**Entrada:** liquidaciones definitivas de un periodo / año.

**Proceso:** pestaña **Visualizar Acumulados**.

**Salida:** consulta consolidada de acumulados por empleado / concepto. Detalle exacto **Pendiente de documentar**.

---

### Flujo 8 — Anular liquidación / reapertura

> El PDF **no documenta** un flujo formal de anulación o reapertura. La sincronización con CXP (`sincronizadoCXP`) sugiere que una vez sincronizada, la liquidación está cerrada de hecho. [REQUIERE_VALIDACION].

---

## Lógica de cálculo

### Secuencia ordenada de 10 procedimientos almacenados

El proceso de liquidación se ejecuta con la siguiente **secuencia lógica** documentada en el PDF. Cada SP devuelve un código; si `@Retorno <> 0`, el flujo retorna `9999` y aborta.

| # | Procedimiento almacenado | Propósito |
|---|---|---|
| 1 | `Nomina.spActualizarNovedadIncapacidadSegundaQuincenaFebrero` | **Solo en quincenas de febrero**. Ajusta las incapacidades para que se tome toda la quincena, compensando que febrero tiene 28/29 días respecto a los 30 días de pago establecidos. |
| 2 | `Nomina.spRecalcularSaldosCreditosEmpleadosArsys` | Revisa los **créditos del empleado** con la organización (incluidas libranzas) y **recalcula** sus saldos para ajustar el valor que se descuenta. |
| 3 | `Nomina.spLiquidacionNominaConceptosAumentanFijosArsys` | Lee los **conceptos fijos que AUMENTAN el pago**, distintos del subsidio de transporte. Se invoca con `@SubsidioTransporte = 0`. |
| 4 | `Nomina.spLiquidacionNominaNovedadesArsys` | Procesa las **novedades** que aumentan o disminuyen el pago de nómina del empleado. |
| 5 | `Nomina.spLiquidacionNominaConceptosAumentanFijosArsys` (2ª invocación) | **Cálculo del subsidio de transporte**. Si `@PagaSubsidioTransporte = 1` (leído de `Tercero.EmpleadoSociedadNomina.auxilioTransporte`), se invoca el mismo SP con `@SubsidioTransporte = 1`. |
| 6 | `Nomina.spLiquidacionNominaPrestamosArsys` | Verifica y recalcula los **préstamos** del empleado con la organización (incluidos los relacionados con sus fondos). |
| 7 | `Nomina.spLiquidacionNominaConceptosDisminuyenFijosArsys` | Procesa los **conceptos fijos que DISMINUYEN** el valor de pago. |
| 8 | `Nomina.spLiquidacionNominaRestaDiasVacacionesArsys` | Disminuye los **días laborados** según los **días de vacaciones** del empleado dentro de la quincena. |
| 9 | `Nomina.spLiquidacionNominaRestarSubsidioTT` | **Resta el Subsidio de Transporte** ya pagado en la primera quincena si el **Total de Pago del Empleado en el mes supera los 2 SMLV**. Se ejecuta solo si `@PagaSubsidioTransporte = 1`. |
| 10 | `Nomina.spLiquidacionNominaRetencionFuente` | Verifica si se debe aplicar **Retención en la Fuente** al empleado. Aplica siempre que el pago **supere los 4 SMLV** según ley. Recibe una lista XML de empleados. |

### Parámetros comunes detectados

Los SPs 3–8 reciben (con variaciones) los siguientes parámetros:

- `@idEmpleadoSociedad`
- `@idContrato`
- `@idTipoContrato`
- `@idTipoNomina`
- `@idUsuario`
- `@SueldoBase` (= `@SueldoEmpleado`)
- `@NumeroPeriodo`
- `@Ano`, `@Mes`, `@MesCalendario`
- `@PeriodicidadMesLiquidacion`
- `@SMLV`, `@STV`, `@STVIBC`
- `@FechaInicialNomina`, `@FechaFinalNomina`
- `@SubsidioTransporte` (0 / 1) — solo en `spLiquidacionNominaConceptosAumentanFijosArsys`
- `@HorasDiariasLaboradas` — solo en `spLiquidacionNominaNovedadesArsys`

### Pseudocódigo del flujo completo

```text
PARA CADA empleado EN ListaEmpleados:

    -- Paso 1 (solo si es quincena de febrero)
    EXEC Nomina.spActualizarNovedadIncapacidadSegundaQuincenaFebrero
         @idContrato, @FechaInicialNomina

    -- Paso 2
    EXEC Nomina.spRecalcularSaldosCreditosEmpleadosArsys
         @idEmpleadoSociedad, @idUsuario, @idSesionUsuario, @idFormulario

    -- Paso 3: conceptos fijos que aumentan (sin subsidio TT)
    EXEC @Retorno = Nomina.spLiquidacionNominaConceptosAumentanFijosArsys
         (...parámetros..., @SubsidioTransporte = 0)
    IF @Retorno <> 0 RETURN 9999

    -- Paso 4: novedades (pagos y descuentos)
    EXEC @Retorno = Nomina.spLiquidacionNominaNovedadesArsys
         (...parámetros..., @HorasDiariasLaboradas, @SMLV, @STV)
    IF @Retorno <> 0 RETURN 9999

    -- Paso 5: si paga subsidio de transporte → recalcula con @SubsidioTransporte = 1
    DECLARE @PagaSubsidioTransporte BIT =
        (SELECT auxilioTransporte
           FROM Tercero.EmpleadoSociedadNomina
          WHERE idEmpleadoSociedadNomina = @idEmpleadoSociedad)

    IF @PagaSubsidioTransporte = 1
        EXEC @Retorno = Nomina.spLiquidacionNominaConceptosAumentanFijosArsys
             (...parámetros..., @SubsidioTransporte = 1)
        IF @Retorno <> 0 RETURN 9999

    -- Paso 6: préstamos
    EXEC @Retorno = Nomina.spLiquidacionNominaPrestamosArsys (...)
    IF @Retorno <> 0 RETURN 9999

    -- Paso 7: conceptos fijos que disminuyen
    EXEC @Retorno = Nomina.spLiquidacionNominaConceptosDisminuyenFijosArsys (...)
    IF @Retorno <> 0 RETURN 9999

    -- Paso 8: restar días de vacaciones de los días laborados
    EXEC @Retorno = Nomina.spLiquidacionNominaRestaDiasVacacionesArsys (...)
    IF @Retorno <> 0 RETURN 9999

    -- Paso 9: si paga subsidio TT y el pago del mes > 2 SMLV, restar STT de la 1ª quincena
    IF @PagaSubsidioTransporte = 1
        EXEC Nomina.spLiquidacionNominaRestarSubsidioTT (...)

    -- Marcar empleado como procesado en TmpLiquidacionNomina
    UPDATE Nomina.TmpLiquidacionNomina
       SET FechaCreacion = @FechaHoraActual
     WHERE idTipoNomina = @idTipoNomina
       AND idEmpleadoSociedad = @idEmpleadoSociedad
       AND idUsuario = @idUsuario

    UPDATE Nomina.TmpLiquidacionNomina
       SET CantidadTotalEmpleados = @CantidadEmpleadosParaProcesar,
           EmpleadosProcesados    = @CantidadEmpleadosProcesados,
           Estado                 = 'Borrador'
     WHERE idTipoNomina = @idTipoNomina
       AND idUsuario    = @idUsuario

FIN DEL LOOP

-- Paso 10: retención en la fuente (al final, con lista XML)
EXEC Nomina.spLiquidacionNominaRetencionFuente
     @ListEmpleado, @Ano, @Mes, @FechaInicialNomina, @FechaFinalNomina,
     @idUsuario, @idSesionUsuario, @idFormulario
```

### Reglas legales aplicadas en la lógica

- **Auxilio de Transporte**: se paga si `auxilioTransporte = 1` en el empleado. Pero **se descuenta el ya pagado en la primera quincena si el Total del mes > 2 SMLV** (paso 9).
- **Retención en la Fuente**: se aplica al empleado **siempre que el pago supere los 4 SMLV** según ley (paso 10).
- **Febrero**: ajuste obligatorio por días reales (28/29) frente a los **30 días de pago en liquidación** (paso 1).

### Devengados, deducciones y neto a pagar

> El PDF describe los **flujos** que aumentan o disminuyen el valor de pago, pero **no detalla fórmulas explícitas** del devengado total, deducción total o neto a pagar. Lo que sí está documentado:

- **Devengados**: producto de los SPs 3 (conceptos fijos +), 4 (novedades +), 5 (subsidio transporte si aplica), y el componente positivo de las novedades.
- **Deducciones**: producto de los SPs 4 (novedades −), 6 (préstamos), 7 (conceptos fijos −), 8 (resta de días por vacaciones), 9 (resta de subsidio TT > 2 SMLV) y 10 (retención fuente > 4 SMLV).
- **Neto a pagar**: total de devengados − total de deducciones, calculado a partir de `LiquidacionDetalle.valor` por concepto. **Cálculo final exacto: Pendiente de documentar**.

### Convención de conceptos (regla operativa global)

Aunque **no aparece en este PDF**, en el sistema (confirmado por el dueño del dominio):

- Conceptos cuyo código inicia con **`P`** → **PAGOS** (devengados).
- Conceptos cuyo código inicia con **`D`** → **DESCUENTOS** (deducciones).

---

## Casos borde

1. **Doble liquidación**: ejecutar dos veces Definitiva para el mismo `(idContrato, periodo, secuencia)` — [REQUIERE_VALIDACION] sobre la barrera técnica.
2. **Empleado sin contrato activo**: no debería poder liquidarse — [REQUIERE_VALIDACION].
3. **Empleado bloqueado para pago** (`bloqueoPago = 1`): debe excluirse — [REQUIERE_VALIDACION].
4. **Periodo cerrado** (ya en Definitiva y sincronizado a CXP): no debería poder modificarse — [REQUIERE_VALIDACION].
5. **Empleado con auxilio de transporte y pago del mes > 2 SMLV**: el STT pagado en la 1ª quincena se resta. Si la 2ª quincena se liquida sola sin info de la 1ª, podría calcularse mal — [REQUIERE_VALIDACION].
6. **Quincena de Febrero sin que el SP 1 se ejecute**: las incapacidades pueden quedar mal calculadas por la diferencia de 28/29 vs. 30 días.
7. **Lista de cédulas con cédulas no existentes** o **duplicadas** en el campo Cédula — [REQUIERE_VALIDACION].
8. **Lista de cédulas con separador distinto a `;`** (espacios, comas) — [REQUIERE_VALIDACION].
9. **Un SP intermedio falla** (devuelve `<> 0`): el flujo retorna `9999`. ¿Queda el empleado en estado inconsistente en `TmpLiquidacionNomina`? — [REQUIERE_VALIDACION].
10. **Empleado con vacaciones que cubren toda la quincena**: el paso 8 deja `días laborados = 0`. ¿Comportamiento esperado? — [REQUIERE_VALIDACION].
11. **Cambio del sueldo** del empleado a mitad del periodo: ¿qué sueldo toma la liquidación? — [REQUIERE_VALIDACION].
12. **Empleado con retención en la fuente Procedimiento 2**: ¿lo respeta `spLiquidacionNominaRetencionFuente`? — [REQUIERE_VALIDACION].
13. **Recalcular en Borrador después de un cambio** de novedades: los SPs deben dejar el estado consistente, no acumular detalles duplicados — [REQUIERE_VALIDACION].
14. **Migración de Tmp → Liquidacion al pasar a Definitiva**: la fuente no documenta la transacción y compensación si falla — [REQUIERE_VALIDACION].
15. **Empleado pasante / contrato especial**: ¿se le aplican todas las reglas (subsidio TT, retención)? — [REQUIERE_VALIDACION].
16. **Generación de MOVDAT** antes de que el periodo esté en Definitiva: ¿bloqueado o permitido? — [REQUIERE_VALIDACION].

---

## Preguntas abiertas

1. ¿Dónde se persiste el campo **Estado** (Borrador / Pasar Revisión / Definitiva) en el modelo final? ¿En `Nomina.Liquidacion`, en `Nomina.TmpLiquidacionNomina` solamente, o en una tabla de control aparte?
2. ¿Cuál es la **estructura completa** de `Nomina.TmpLiquidacionNomina`? El PDF solo expone fragmentos (`idTipoNomina`, `idEmpleadoSociedad`, `idUsuario`, `FechaCreacion`, `CantidadTotalEmpleados`, `EmpleadosProcesados`, `Estado`).
3. ¿Qué exactamente significa **`sincronizadoCXP (bit)`** en `Nomina.Liquidacion`?
4. ¿Cómo se determina la **`PeriodicidadMesLiquidacion`** y qué valores admite?
5. ¿Qué valores admiten `@SMLV`, `@STV`, `@STVIBC`? ¿Se leen de un catálogo de parámetros legales del año? ¿Dónde?
6. ¿Cómo se computa `@HorasDiariasLaboradas`? ¿Sale de la tabla de equivalencias horas día/mes documentada en la Skill de Novedades?
7. ¿Qué hace exactamente el checkbox **"Importar Datos Usuarios"** de la pantalla?
8. ¿Qué información concreta entrega la pestaña **"Visualizar Acumulados"**? ¿Por empleado, por concepto, por periodo, por año?
9. ¿Qué formato y columnas tiene el archivo **MOVDAT Contabilidad** y **MOVDAT Tesorería**?
10. ¿La transición Borrador → Pasar Revisión → Definitiva está protegida por **permisos** específicos? ¿Cuáles?
11. ¿Existe un permiso para **reabrir** una liquidación Definitiva (rollback)? ¿O es estrictamente unidireccional?
12. ¿Cómo se relaciona este módulo con las **Prestaciones Sociales** (primas, cesantías) y con la **Liquidación de Contrato** del módulo de Novedades? ¿Comparten `Liquidacion` o usan tablas hermanas?
13. ¿Qué firma exacta (parámetros) tienen los 10 SPs? El PDF muestra parámetros aproximados; falta `OUTPUT`, `RETURN`, eventuales tablas-parámetro.
14. ¿Existe un **log** persistente del resultado de cada pasada (más allá de la pestaña Mensajes Liquidación)?
15. ¿Qué condiciones determinan el envío al revisor fiscal (notificación, correo, simple cambio de estado)?
16. ¿Cómo se manejan las **diferencias entre Centro Económico / Centro de Costo / Dependencia** al filtrar empleados? ¿Es una sola condición AND o se pueden combinar?

---

## Cómo extender esta skill

Esta Skill es la **fuente única consolidada** del módulo Liquidación de Nómina. Toda nueva fuente debe integrarse aquí, no en archivos paralelos.

### Cómo anexar nuevos PDFs / documentos

1. Leer la fuente completa.
2. Identificar a qué sección pertenece cada hallazgo (Contexto, Formularios, Permisos, Reglas, Modelo de datos, Flujos, Lógica de cálculo, Casos borde, Preguntas abiertas).
3. **Insertar** el contenido en la sección correspondiente. Crear una sección nueva solo si se trata de un dominio realmente nuevo.
4. Si la nueva fuente repite algo ya documentado, complementarlo. Si lo contradice, abrir entrada en **Preguntas abiertas** y resolverlo con el dueño del dominio (Ferney).
5. Cualquier hallazgo que resuelva una **Pregunta abierta** debe migrarse a la sección estable y eliminarse de Preguntas abiertas.

### Cómo fusionar nuevo contexto

- Toda **adición** debe identificar su **origen** (nombre del documento, fecha, autor) en el Historial de actualizaciones.
- Toda **modificación** de una regla debe explicar por qué cambió.
- Cada **`[REQUIERE_VALIDACION]`** debe resolverse en una de tres formas:
  - Confirmarla → reemplazar la etiqueta con el dato definitivo.
  - Refutarla → ajustar el contenido y eliminar la etiqueta.
  - Postergarla → moverla a **Preguntas abiertas**.

### Cómo versionar cambios

- Cada actualización suma una fila en **Historial de actualizaciones**.
- Formato de fecha: `YYYY-MM-DD`.
- Indicar: fuente del cambio, descripción breve, responsable.
- **No eliminar** conocimiento previo sin validación funcional explícita.

### Cómo evitar contradicciones

- Antes de agregar una regla, **buscar** si ya existe equivalente o contraria.
- Si hay contradicción, **no decidir en silencio**: documentarla en Preguntas abiertas y escalarla al dueño del dominio.
- Mantener el **vocabulario consistente**: Periodo (`AAAAMMNN`), Secuencia (`01`/`02`), SMLV, STV, Borrador / Pasar Revisión / Definitiva, MOVDAT.
- Si el cambio impacta otras Skills del mismo dominio (Novedades, Incapacidad, Prestaciones Sociales, Empleado), agregar nota de **impacto cruzado** y avisar a los suplentes del dominio.

---

## Mantenimiento y evolución de la Skill

Esta Skill fue construida inicialmente a partir de contextualización textual, conocimiento funcional del negocio y documentación funcional y técnica disponible, pero está diseñada para evolucionar continuamente.

### Reglas de mantenimiento

- La documentación inicial corresponde a la fuente original usada para crear esta Skill (`LiquidacionNomina.pdf` — 5 páginas con formulario, modelo de datos y código T-SQL de los 10 SPs).
- Esta Skill puede enriquecerse posteriormente con:
  - nuevos documentos funcionales,
  - cambios del sistema,
  - nuevas reglas de negocio,
  - cambios en formularios,
  - nuevas tablas o cambios en SQL Server,
  - nuevos permisos o roles,
  - decisiones del negocio,
  - casos reales encontrados en soporte.
- Toda nueva información agregada debe:
  - mantener consistencia con la estructura actual,
  - indicar fecha de actualización,
  - indicar fuente del cambio,
  - no eliminar conocimiento previo sin validación funcional.

### Historial de actualizaciones

| Fecha | Fuente | Cambio realizado | Responsable |
| ----- | ------ | ---------------- | ----------- |
| 2026-05-11 | `LiquidacionNomina.pdf` | Creación inicial consolidada de la Skill (formulario, ciclo Borrador → Pasar Revisión → Definitiva, modelo `Nomina.Liquidacion` / `Nomina.LiquidacionDetalle` / `Nomina.TmpLiquidacionNomina`, secuencia de 10 SPs con pseudocódigo, reglas legales 2 SMLV / 4 SMLV, ajuste de Febrero, casos borde y preguntas abiertas). | Ferney Acosta |

### Información pendiente

Marcar como **Pendiente de documentar** cualquier información que aún no exista o no haya sido documentada. No usar la frase "no documentado en la fuente": esta Skill debe poder evolucionar con nuevas fuentes.

Pendientes actuales por validar contra BD / SP / código de dominio:

- Persistencia exacta del **Estado** (Borrador / Pasar Revisión / Definitiva) en el modelo final — **Pendiente de documentar**.
- Estructura **completa** de `Nomina.TmpLiquidacionNomina` — **Pendiente de documentar**.
- Semántica precisa de **`sincronizadoCXP`** en `Nomina.Liquidacion` — **Pendiente de documentar**.
- Catálogo de **PeriodicidadMesLiquidacion** y posibles valores — **Pendiente de documentar**.
- Origen y catálogo de `@SMLV`, `@STV`, `@STVIBC` (tabla de parámetros legales del año) — **Pendiente de documentar**.
- Firma exacta (parámetros, OUTPUT/RETURN, table-valued params) de los 10 SPs — **Pendiente de documentar**.
- Estructura del archivo **MOVDAT Contabilidad** y **MOVDAT Tesorería** — **Pendiente de documentar**.
- Permisos asociados a las transiciones de Estado y a la generación de MOVDAT — **Pendiente de documentar**.
- Procedimiento de **reapertura / anulación** de una liquidación Definitiva — **Pendiente de documentar**.
- Comportamiento del checkbox **"Importar Datos Usuarios"** — **Pendiente de documentar**.
- Detalle exacto de la pestaña **Visualizar Acumulados** — **Pendiente de documentar**.
- Interacción con módulos de **Prestaciones Sociales** y **Liquidación de Contrato / Vacaciones** — **Pendiente de documentar**.

> Cuando se actualice la Skill: si cambian SPs, secuencia, parámetros legales (SMLV, STV), estados o tablas, reflejarlo aquí, agregar la fila correspondiente al **Historial de actualizaciones** y avisar a los suplentes del dominio.
