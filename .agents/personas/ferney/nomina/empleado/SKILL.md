---
name: nomina-empleado
display_name: Empleado Sociedad (Nómina Arsys)
owner: ferney
domain: Nómina
modulo: Empleado
ambito: Funcional + Técnico + Seguridad + Modelo de datos
fuente: Documento "EmpleadoSociedad.pdf" (módulo Nómina, esquema Tercero / Nomina)
descripcion: Skill empresarial consolidada para soporte funcional, técnico, de seguridad y de modelo de datos del módulo Empleado Sociedad (Empleado Sociedad V2) del sistema de Nómina Arsys. Pantalla principal + pestañas Datos Básicos, Dirección, Grupo Empresarial, Nómina (con 11 sub-pestañas), Firma y Retención en la Fuente.
---

# Skill: Nomina - Empleado

## Propósito

Servir como **fuente única de verdad** del módulo **Empleado Sociedad** dentro del sistema de Nómina de Arsys: documentar de forma consolidada el formulario `Empleado Sociedad V2`, todas sus pestañas y sub-pestañas, las acciones y permisos que controlan los controles individuales, las reglas de negocio del registro y mantenimiento del empleado, y el modelo de datos asociado (esquema `Tercero` / `Nomina`).

Esta Skill debe permitir a un agente IA (y a un humano) responder preguntas funcionales, técnicas, de seguridad y de datos sobre el módulo Empleado, así como guiar el alta, edición, consulta y mantenimiento de un empleado en el ERP.

---

## Contexto funcional

### Alcance

El módulo **Empleado Sociedad** centraliza toda la información de un empleado vinculado a la organización:

- Identificación y datos personales (Datos Básicos).
- Residencia y datos de contacto (Dirección).
- Vinculación organizacional (Grupo Empresarial).
- Datos para la liquidación de nómina (Nómina y sus sub-pestañas: Contrato, Seguridad Social, Sueldos, Bancaria, Parafiscales, Parámetro Contable, Estudios, Idiomas, Aficiones, Grupo Familiar, Archivos, Visualización Archivos).
- Firma del empleado (Firma).
- Configuración del cálculo de retención en la fuente (Retención Fuente).

### Objetivo de negocio

- Mantener el **maestro de empleados** completo, consistente y auditable.
- Soportar la **liquidación de nómina** (contrato, sueldo, EPS, ARP, fondos, cuenta bancaria, parafiscales).
- Soportar **reportes legales** (Planilla Única a la DIAN, retención en la fuente).
- Asegurar el **control de acceso granular** a campos sensibles mediante permisos por control, no solo por pestaña.

### Actores involucrados

| Actor | Rol |
|---|---|
| Administrador | Acceso total sin restricción de campos. |
| Usuario de Nómina / RRHH | Ejecuta el alta y mantenimiento; sus capacidades dependen de los permisos otorgados (a nivel de pestaña y de control). |
| Contabilidad | Mantiene la sub-pestaña Parámetro Contable (cuentas y conceptos contables). |
| Auditor | Consulta histórico (Sueldos, Parafiscales, Archivos, Visualización Archivos). |
| DIAN | Receptor final del reporte derivado de campos clave (clase documento, fecha ingreso, número persona, retención fuente). |

### Procesos principales

1. **Alta** del empleado (Datos Básicos → Dirección → Grupo Empresarial → Nómina → Firma → Retención Fuente).
2. **Mantenimiento** de campos específicos sujetos a permisos individuales.
3. **Vinculación contractual** (Contrato + Seguridad Social + Bancaria).
4. **Carga y visualización** de documentación física del empleado (Archivos / Visualización Archivos).
5. **Configuración** del procedimiento de retención en la fuente (1 o 2).
6. **Consulta histórica** de sueldos, primas, cesantías y vacaciones (Sueldos + Parafiscales).

### Barra superior del formulario

Botones presentes en toda la pantalla: **Nuevo, Guardar, Buscar, Reportes, Sincronizar, Manuales, Ayuda, Salir**. Encabezado **Arsys Nómina** con estado **"Creando Registro"** durante el alta.

---

## Formularios y pantallas

> El formulario principal se identifica como **"Empleado Sociedad V2"**. Está compuesto por una **cabecera fija** (Datos Básicos Empleado) y **cinco pestañas principales**: `Dirección`, `Grupo Empresarial`, `Nómina`, `Firma`, `Retención Fuente`. La pestaña `Nómina` contiene **doce sub-pestañas**.

### 1. Pestaña — Datos Básicos Empleado (cabecera fija)

Propósito: identificación legal y personal del empleado.

| Campo | Tipo | Obligatorio | Editable | Regla | Observación |
|---|---|---|---|---|---|
| Clase Documento | Combo (catálogo) | Sí | Sí (según permiso global) | Valor del catálogo DIAN (ver lista debajo) | Marcado con `* ●` en la pantalla → obligatorio. |
| Cédula | Texto | Sí | Sí | Identificador del empleado | Debe ser único — [REQUIERE_VALIDACION] |
| Ciudad Expedición | Lookup | Sí | Sí (permiso `CIUDADEXPDOCUIDENTIDAD`) | Municipio donde se expidió el documento | |
| Primer Nombre | Texto | Sí | Sí (permiso `CAMBIARNOMBRE`) | | |
| Segundo Nombre | Texto | No | Sí (permiso `CAMBIARNOMBRE`) | | |
| Primer Apellido | Texto | Sí | Sí (permiso `CAMBIARNOMBRE`) | | |
| Segundo Apellido | Texto | No | Sí (permiso `CAMBIARNOMBRE`) | | |
| Nombre Completo | Texto | Sí | Sí (permiso `CAMBIARNOMBRE`) | Suele componerse de los 4 campos anteriores — [REQUIERE_VALIDACION] | |
| Género | Radio | Sí | Sí (permiso `GENERO`) | Valores: **Masculino**, **Femenino**. | |
| Tipo RH | Combo | Sí | Sí (permiso `RH`) | Grupo sanguíneo | |
| Fecha Nacimiento | Fecha | Sí | Sí (permiso `FECHANACIMIENTO`) | | |
| Ciudad | Lookup | Sí | Sí (permiso `CIUDADNACIMIENTO`) | Ciudad de nacimiento | |
| Estado | Combo | [REQUIERE_VALIDACION] | Sí | Estado del empleado en el maestro | |
| Estado Civil | Combo | Sí | Sí (permiso `ESTADOCIVIL`) | | |
| Peso (Kg) | Numérico | No | Sí (permiso `PESO`) | | |
| Estatura (Mts) | Numérico | No | Sí (permiso `ESTATURA`) | | |
| Responsabilidades | Multi-selección | Sí (parametrización tributaria) | Sí | Lista de responsabilidades tributarias | |
| Auxilio Transporte | Sí/No | Sí | Sí | Indica si el empleado recibe auxilio | |
| Foto | Imagen | No | Sí (permiso `MODIFICARFOTO`) | Formato PNG/JPG, **resolución 178 x 153 px** | Botones: **Modificar Imagen**, **Seleccionar Imagen** |

#### Catálogo de Clase Documento (valores DIAN)

| Código | Descripción |
|---|---|
| 11 | Registro Civil de Nacimiento |
| 12 | Tarjeta de Identificación |
| 13 | Cédula de Ciudadanía |
| 21 | Tarjeta de Extranjería |
| 22 | Cédula de Extranjería |
| 31 | NIT |
| 41 | Pasaporte |
| 42 | Documento de Identificación Extranjero |
| 43 | Sin Identificación del Exterior o para uso definido por la DIAN |
| 44 | Documento de Identificación Extranjero persona Jurídica |

---

### 2. Pestaña — Dirección

Propósito: lugar actual de residencia y datos de contacto del empleado, incluida la actividad económica de la ARL.

| Campo | Tipo | Obligatorio | Editable | Regla | Observación |
|---|---|---|---|---|---|
| País | Lookup | Sí | Sí | | |
| Departamento | Lookup | Sí | Sí | Dependiente de País | |
| Municipio | Lookup | Sí | Sí | Dependiente de Departamento | |
| División Territorial | Lookup | [REQUIERE_VALIDACION] | Sí | | |
| Sitio Ubicación | Lookup | Sí | Sí (permiso `SITIOUBICACION`) | | |
| Dirección | Texto | Sí | Sí (permiso `DIRECCION`) | Dirección residencial | |
| Email Empresa | Texto | Sí | Sí (permiso `EMAIL`) | Importante para emisión de correos institucionales | |
| Email Personal | Texto | No | Sí (permiso `EMAIL`) | | |
| Teléfono | Texto | No | Sí (permiso `TELEFONO`) | | |
| Celular | Texto | Sí | Sí (permiso `CELULAR`) | | |
| Act. Económica ARL | Lookup | Sí | Sí | Actividad económica asociada a la ARL | |
| Act. Económica (lista de selección) | Grid + acciones | — | Sí | Botones de **Agregar** y **Eliminar Actividad** | |

---

### 3. Pestaña — Grupo Empresarial

Propósito: vinculación organizacional del empleado para liquidación y reportes.

| Campo | Tipo | Obligatorio | Editable | Regla | Observación |
|---|---|---|---|---|---|
| Unidad Negocio | Combo | Sí | Sí (permiso `UNIDADNEGOCIO`) | | |
| Centro Económico | Combo | Sí | Sí (permiso `CENTROECONOMICOEMPLEADO`) | | |
| Centro Costo | Combo | Sí | Sí (permiso `CENTROCOSTO`) | | |
| Tipo Nómina | Combo | Sí | Sí (permiso `TIPONOMINA`) | Ej. `0001 - ADMINISTRACION` | |
| Dependencia | Combo | Sí | Sí (permiso `DEPENDENCIA`) | | |
| Cargo | Combo | Sí | Sí (permiso `CARGO`) | | |
| Nivel Jerárquico | Texto/Combo | [REQUIERE_VALIDACION] | Sí | Acompaña al cargo | |
| Riesgo ARP | Combo | Sí | Sí (permiso `RIESGOARP`) | Clasificación de riesgo | |
| Área Funcional | Combo | [REQUIERE_VALIDACION] | Sí | | |
| Régimen Laboral | Radio | Sí | Sí (permiso `REGIMENLABORAL`) | Valores: **Anterior** / **Nuevo** | Afecta cálculo de cesantías |
| Fecha Ingreso (Planilla Única) | Fecha | Sí | Sí | Usada para reporte a la DIAN | |
| Número Persona | Texto/numérico | Sí | Sí (permiso `NUMEROPERSONA`) | **Coincide con el número de identificación** | |
| Bloqueo Pago | Radio | Sí | Sí (permiso `BLOQUEOPAGO`) | Sí / No. Si está en `Sí` el empleado no entra al pago. | |

---

### 4. Pestaña — Nómina

Contenedor de **doce sub-pestañas** específicas para la liquidación de nómina.

#### 4.1 Sub-pestaña Contrato

Propósito: condiciones contractuales del empleado.

| Campo | Tipo | Obligatorio | Editable | Regla | Observación |
|---|---|---|---|---|---|
| Tipo Contrato | Combo | Sí | Sí (permiso `TIPOCONTRATO`) | Ej. Fijo, Término Indefinido, Pasante, etc. | |
| Modelo Contrato | Combo | Sí | Sí (permiso `MODELOCONTRATO`) | | |
| Subtipo Cotizante | Combo | Sí | Sí | Categoría de cotizante (SOI / PILA) | |
| Fijo / Variable | Radio | Sí | Sí (permiso `FIJOVARIABLE`) | Fijo / Variable | |
| Sábado Laborable | Radio | Sí | Sí (permiso `SABADOLABORABLE`) | Si / No | |
| Afecta Planilla Única | Check | Sí | Sí | Define si el contrato se reporta a la DIAN | |
| Alto Riesgo | Check | Sí | Sí | Marcador para contratos de alto riesgo | |
| Número Contrato | Numérico | Sí | — | **Consecutivo** según creación | [REQUIERE_VALIDACION] |
| Fecha Contrato | Fecha | Sí | Sí (permiso `FECHACONTRATO`) | | |
| Última Fecha Renovación | Fecha | No | Sí | Última renovación registrada | |
| Fecha Terminación | Fecha | No | Sí (permiso `FECHATERMINACION`) | | |
| Sueldo | Numérico | Sí | Sí (permiso `SUELDOCONTRATO`) | Debe ser > 0 — [REQUIERE_VALIDACION] | |
| Meses Contrato | Numérico | Sí | Sí (permiso `MESESCONTRATO`) | | |

#### 4.2 Sub-pestaña Seguridad Social

Propósito: afiliaciones a seguridad social y prestaciones.

| Campo | Tipo | Obligatorio | Editable | Regla | Observación |
|---|---|---|---|---|---|
| EPS | Combo | Sí | Sí (permiso `EPS`) | | |
| ARP | Combo | Sí | Sí (permiso `ARP`) | | |
| Caja Compensación | Combo | Sí | Sí (permiso `CAJACOMPENSACION`) | | |
| Fondo Pensión | Combo | Sí | Sí (permiso `FONDOPENSION`) | | |
| Fondo Cesantías | Combo | Sí | Sí (permiso `FONDOCESANTIAS`) | | |
| Fondo Solidaridad | Combo | No | Sí (permiso `FONDOSOLIDARIDAD`) | | |
| Botón Guardar | Botón | — | Sí (permiso `BOTONGUARDARSEGURIDADSOCIAL`) | | |

#### 4.3 Sub-pestaña Sueldos

Propósito: **histórico de sueldos** del empleado. **Solo visualización**.

| Columna | Tipo | Observación |
|---|---|---|
| Fecha Inicio | Fecha | |
| Fecha Final | Fecha | Vacío si está vigente |
| Sueldo | Numérico | |
| Estado | Texto | Activo = sueldo vigente. Solo uno puede estar activo. |

#### 4.4 Sub-pestaña Bancaria

Propósito: cuenta bancaria para el pago de nómina.

| Campo | Tipo | Obligatorio | Editable | Regla | Observación |
|---|---|---|---|---|---|
| Cuenta Bancaria (Banco) | Combo | Sí | Sí (permiso `BANCO`) | Catálogo de bancos | |
| Tipo Cuenta | Combo | Sí | Sí | Ahorros / Corriente | [REQUIERE_VALIDACION] (no listado explícitamente) |
| Número Cuenta | Texto | Sí | Sí (permiso `NUMEROCUENTA`) | | |
| Titular Cuenta | Texto | Sí | Sí (permiso `TITULARCUENTA`) | Nombre del titular | |

#### 4.5 Sub-pestaña Parafiscales

Propósito: histórico de pagos parafiscales (cesantías, primas, vacaciones causadas y faltantes).

Tabla principal (visible en la imagen — tab interno **Cesantías**):

| Columna | Tipo |
|---|---|
| Año | Numérico |
| Fondo | Texto |
| Básico | Numérico |
| Auxilio Transporte | Numérico |
| Sueldo Promedio | Numérico |
| Días | Numérico |
| Valor Cesantías | Numérico |
| Valor Intereses | Numérico |

Sub-pestañas internas:

- **Cesantías** — `PESTANACESANTIA`
- **Primas** — `PESTANAPRIMA`
- **Vacaciones** — `PESTANAVACACIONES`
- **Causación de Vacaciones** — `PESTANACAUSACIONVACACIONES`
- **Disfrute de Vacaciones** — `PESTANADISFRUTEVACACIONES`

#### 4.6 Sub-pestaña Parámetro Contable

Propósito: parametrizar conceptos contables con los que se paga la nómina (movimientos contables).

| Campo | Tipo | Obligatorio | Editable | Regla | Observación |
|---|---|---|---|---|---|
| Parámetro Contable | Combo | Sí | Sí | Concepto contable | |
| Descripción Parámetro Contable | Texto | No | — | Derivado del combo | |
| Acción | Botón/Grid | — | Sí | Adicionar / quitar | |
| Botón Guardar | Botón | — | Sí | | |

#### 4.7 Sub-pestaña Estudios

| Campo | Tipo | Obligatorio | Editable | Observación |
|---|---|---|---|---|
| Título | Texto | Sí | Sí | |
| Tipo Estudio | Combo | Sí | Sí | |
| Centro Docente | Lookup | Sí | Sí | |
| Profesión | Lookup | Sí | Sí | |
| Botón Guardar | Botón | — | Sí (permiso `BOTONGUARDARESTUDIO`) | |

Grid: **Título, Tipo Estudio, Centro Docente, Profesión, Acción**.

#### 4.8 Sub-pestaña Idiomas

| Campo | Tipo | Obligatorio | Editable | Observación |
|---|---|---|---|---|
| Idioma Nativo | Combo | Sí | Sí | Ej. `01 - ESPAÑOL` |
| Idioma | Combo | Sí | Sí | Idiomas adicionales |
| Dominio | Combo | Sí | Sí | Nivel de dominio (porcentaje en grid) |
| Botón Guardar | Botón | — | Sí (permiso `BOTONGUARDARIDIOMA`) | |

Grid: **Código, Descripción, Nativo, %, Acción**.

#### 4.9 Sub-pestaña Aficiones

| Campo | Tipo | Obligatorio | Editable | Observación |
|---|---|---|---|---|
| Afición | Combo | Sí | Sí | Catálogo de aficiones |
| Botón Guardar | Botón | — | Sí (permiso `BOTONGUARDARAFICION`) | |

Grid: **Código, Descripción, Acción**.

#### 4.10 Sub-pestaña Grupo Familiar

| Campo | Tipo | Obligatorio | Editable | Observación |
|---|---|---|---|---|
| Documento Identidad | Combo | Sí | Sí | Tipo de documento |
| Número Documento | Texto | Sí | Sí | |
| Nombres y Apellidos | Texto | Sí | Sí | |
| Fecha Nacimiento | Fecha | Sí | Sí | |
| Parentesco | Combo | Sí | Sí | Madre, padre, esposa, hijos |
| Dirección | Texto | No | Sí | |
| Teléfono | Texto | No | Sí | |
| Escolaridad | Combo | No | Sí | Grado de escolaridad |
| Botón Adicionar | Botón | — | Sí (permiso `BOTONADICIONARGRUPOFAMILIAR`) | |

Grid: **Documento Identidad, Número Documento, Nombres y Apellidos, Fecha Nacimiento, Parentesco, Dirección, Teléfono, Escolaridad, Acción**.

#### 4.11 Sub-pestaña Archivos

Propósito: cargar documentación del empleado.

| Sección | Documentos | Observación |
|---|---|---|
| Hoja de Vida | Archivos PDF/imagen | |
| Contrato | Archivos PDF/imagen | |
| Estudios | Diplomas, certificados | |
| Afiliaciones | Documentos de EPS, ARP, fondos | |
| Varios | Otros documentos | |

Grid: **Nombre de Archivo, Acción**. Botón **Adjuntar Archivo**.

> El PDF asocia el permiso `PESTANAVISUALIZARARCHIVOS` a esta pestaña. [REQUIERE_VALIDACION] — el nombre sugiere que también controla la pestaña siguiente.

#### 4.12 Sub-pestaña Visualización Archivos

Propósito: **visualizar** la documentación cargada, sin lógica de modificación. Vista previa de documentos.

---

### 5. Pestaña — Firma

Propósito: cargar la imagen de la firma del empleado.

| Campo | Tipo | Obligatorio | Editable | Regla | Observación |
|---|---|---|---|---|---|
| Imagen Firma | Imagen | No | Sí (permiso `MODIFICARFIRMA`) | **PNG, resolución 227 x 76 px** | Botones **Modificar Imagen** y **Seleccionar Imagen** |

---

### 6. Pestaña — Retención en la Fuente

Propósito: definir el procedimiento de cálculo de retención en la fuente del empleado.

| Campo | Tipo | Obligatorio | Editable | Regla | Observación |
|---|---|---|---|---|---|
| Cálculo Retención Fuente | Radio | Sí | Sí (permiso `CALCULORETENCIONFUENTE`) | Valores: **Procedimiento 1** / **Procedimiento 2** | **Generalmente aplica Procedimiento 1**. |

---

## Permisos y acciones

> La fuente describe **acciones / permisos atómicos por control y por pestaña**. Cuando un usuario es **Administrador**, **no se aplica restricción** sobre los campos. Para los demás usuarios, se concede el permiso correspondiente para habilitar la modificación.

### Tabla consolidada de permisos

| Control | Acción | Rol (sugerido) | Permiso |
|---|---|---|---|
| Pestaña Datos Básicos completa | Ver / editar | RRHH | `PESTANADATOSBASICOSTERCERO` |
| Nombre Completo (4 campos de nombre) | Editar | RRHH | `CAMBIARNOMBRE` |
| Género | Editar | RRHH | `GENERO` |
| Tipo RH | Editar | RRHH | `RH` |
| Peso | Editar | RRHH | `PESO` |
| Estado Civil | Editar | RRHH | `ESTADOCIVIL` |
| Estatura | Editar | RRHH | `ESTATURA` |
| Ciudad Expedición Documento | Editar | RRHH | `CIUDADEXPDOCUIDENTIDAD` |
| Fecha Nacimiento | Editar | RRHH | `FECHANACIMIENTO` |
| Ciudad de Nacimiento | Editar | RRHH | `CIUDADNACIMIENTO` |
| Foto | Editar | RRHH | `MODIFICARFOTO` |
| Pestaña Dirección completa | Ver / editar | RRHH | `PESTANADIRECCION` |
| Celular | Editar | RRHH | `CELULAR` |
| Teléfono | Editar | RRHH | `TELEFONO` |
| Email | Editar | RRHH | `EMAIL` |
| Dirección | Editar | RRHH | `DIRECCION` |
| Sitio de Ubicación | Editar | RRHH | `SITIOUBICACION` |
| Pestaña Grupo Empresarial completa | Ver / editar | RRHH | `PESTANAGRUPOEMPRESARIAL` |
| Número de Persona | Editar | RRHH | `NUMEROPERSONA` |
| Bloqueo de Pago | Editar | RRHH / Aprobador | `BLOQUEOPAGO` |
| Cargo | Editar | RRHH | `CARGO` |
| Tipo Nómina | Editar | RRHH | `TIPONOMINA` |
| Centro Económico | Editar | RRHH | `CENTROECONOMICOEMPLEADO` |
| Centro de Costo | Editar | RRHH | `CENTROCOSTO` |
| Dependencia | Editar | RRHH | `DEPENDENCIA` |
| Riesgo ARP | Editar | RRHH | `RIESGOARP` |
| Régimen Laboral | Editar | RRHH | `REGIMENLABORAL` |
| Unidad de Negocio | Editar | RRHH | `UNIDADNEGOCIO` |
| Sub-pestaña Contrato | Ver / editar | RRHH | `PESTANACONTRATO` |
| Tipo Contrato | Editar | RRHH | `TIPOCONTRATO` |
| Modelo Contrato | Editar | RRHH | `MODELOCONTRATO` |
| Fijo / Variable | Editar | RRHH | `FIJOVARIABLE` |
| Sábado Laborable | Editar | RRHH | `SABADOLABORABLE` |
| Fecha Contrato | Editar | RRHH | `FECHACONTRATO` |
| Fecha Terminación | Editar | RRHH | `FECHATERMINACION` |
| Valor Sueldo | Editar | RRHH | `SUELDOCONTRATO` |
| Meses Contrato | Editar | RRHH | `MESESCONTRATO` |
| Sub-pestaña Seguridad Social | Ver / editar | RRHH | `PESTANASEGURIDADSOCIAL` |
| Botón Guardar Seguridad Social | Ejecutar | RRHH | `BOTONGUARDARSEGURIDADSOCIAL` |
| EPS | Editar | RRHH | `EPS` |
| ARP | Editar | RRHH | `ARP` |
| Caja de Compensación | Editar | RRHH | `CAJACOMPENSACION` |
| Fondo de Pensión | Editar | RRHH | `FONDOPENSION` |
| Fondo de Cesantías | Editar | RRHH | `FONDOCESANTIAS` |
| Fondo de Solidaridad | Editar | RRHH | `FONDOSOLIDARIDAD` |
| Sub-pestaña Bancaria | Ver / editar | RRHH / Tesorería | `PESTANABANCARIA` |
| Banco | Editar | Tesorería | `BANCO` |
| Número Cuenta | Editar | Tesorería | `NUMEROCUENTA` |
| Titular Cuenta | Editar | Tesorería | `TITULARCUENTA` |
| Sub-pestaña Parafiscales | Ver | Auditoría / RRHH | `PESTANAPARAFISCALES` |
| Sub-tab Prima | Ver | Auditoría / RRHH | `PESTANAPRIMA` |
| Sub-tab Cesantías | Ver | Auditoría / RRHH | `PESTANACESANTIA` |
| Sub-tab Vacaciones | Ver | Auditoría / RRHH | `PESTANAVACACIONES` |
| Sub-tab Causación Vacaciones | Ver | Auditoría / RRHH | `PESTANACAUSACIONVACACIONES` |
| Sub-tab Disfrute Vacaciones | Ver | Auditoría / RRHH | `PESTANADISFRUTEVACACIONES` |
| Sub-pestaña Parámetro Contable | Ver / editar | Contabilidad | `PESTANAPARAMETROCONTABLE` |
| Sub-pestaña Estudios | Ver / editar | RRHH | `PESTANAESTUDIOS` |
| Botón Guardar Estudio | Ejecutar | RRHH | `BOTONGUARDARESTUDIO` |
| Sub-pestaña Idiomas | Ver / editar | RRHH | `PESTANAIDIOMAS` |
| Botón Guardar Idioma | Ejecutar | RRHH | `BOTONGUARDARIDIOMA` |
| Sub-pestaña Aficiones | Ver / editar | RRHH | `PESTANAAFICIONES` |
| Botón Guardar Afición | Ejecutar | RRHH | `BOTONGUARDARAFICION` |
| Sub-pestaña Grupo Familiar | Ver / editar | RRHH | `PESTANAGRUPOFAMILIAR` |
| Botón Adicionar Grupo Familiar | Ejecutar | RRHH | `BOTONADICIONARGRUPOFAMILIAR` |
| Sub-pestaña Archivos | Ver / cargar | RRHH | `PESTANAVISUALIZARARCHIVOS` |
| Pestaña Firma | Editar | RRHH | `MODIFICARFIRMA` |
| Pestaña Retención Fuente | Ver / editar | RRHH / Contabilidad | `CALCULORETENCIONFUENTE` |

### Regla general de aplicación

- **Administrador**: sin restricciones; ve y edita todo.
- **Otros usuarios**: cada **pestaña / sub-pestaña** se muestra/oculta según su permiso `PESTANA*`; dentro de cada pestaña, los **controles** individuales se habilitan según los permisos atómicos. Si un usuario tiene la pestaña pero no el permiso del control, el campo queda en **solo lectura**.
- Los permisos `BOTON*` controlan **explícitamente** la ejecución del botón Guardar / Adicionar dentro de la sub-pestaña.

---

## Reglas de negocio

### Reglas explícitas (presentes en el documento)

1. **Clase Documento** debe tomar uno de los valores del catálogo DIAN (11, 12, 13, 21, 22, 31, 41, 42, 43, 44).
2. **Cuando el usuario es Administrador**, no se aplican restricciones por campo.
3. **Para usuarios no administradores**, las modificaciones sobre los campos están condicionadas a permisos atómicos por control.
4. **Régimen Laboral** distingue entre **Anterior** y **Nuevo** y afecta el cálculo de cesantías.
5. **Número Persona** corresponde al **mismo número de identificación** del empleado.
6. **Fecha Ingreso** se usa específicamente para el reporte en la **Planilla Única a la DIAN**.
7. **Bloqueo Pago** en `Sí` impide que al empleado se le procese el pago (interpretación funcional).
8. **Número Contrato** es un **consecutivo** asignado al crear el contrato.
9. **Afecta Planilla Única** (en Contrato) determina si el contrato se reporta a la DIAN.
10. **Sub-pestaña Sueldos** es **solo visualización histórica**; el sueldo activo (Estado = Activo) es el sueldo actual.
11. **Procedimiento de Retención** acepta solo los valores **Procedimiento 1** o **Procedimiento 2**. Generalmente aplica **Procedimiento 1**.
12. **Foto del empleado**: formato **PNG o JPG**, resolución **178 x 153 px**.
13. **Firma del empleado**: formato **PNG**, resolución **227 x 76 px**.
14. **Visualización de Archivos** es estrictamente de lectura (sin lógica de negocio).

### Reglas implícitas / inferidas (marcar para validación)

- La combinación **Clase Documento + Cédula** debería ser **única** en el maestro — [REQUIERE_VALIDACION].
- **Sueldo del contrato** debe ser > 0 — [REQUIERE_VALIDACION].
- **Fecha Terminación**, si existe, debe ser ≥ **Fecha Contrato** — [REQUIERE_VALIDACION].
- Solo **una fila de Sueldos** puede tener `Estado = Activo` por empleado — [REQUIERE_VALIDACION].
- Los **Fondos** (Pensión, Cesantías, Solidaridad) deben corresponder a entidades vigentes (terceros activos) — [REQUIERE_VALIDACION].
- La **Cuenta Bancaria** debería pasar validación de dígito verificador según el banco — [REQUIERE_VALIDACION].

---

## Modelo de datos

> El esquema base de creación del empleado es **`Tercero`**. La tabla central es **`Tercero.EmpleadoSociedadNomina`**. A partir de ella se relacionan tablas que respaldan municipio, sitio de ubicación, clase de documento, responsabilidades, actividad económica, contrato, retención en la fuente y cuenta bancaria. También existe una relación con el "tercero usuario" (vínculo con el usuario del sistema).

### Tabla central

#### `Tercero.EmpleadoSociedadNomina`

| Columna (aprox.) | Tipo (aprox.) | Rol |
|---|---|---|
| `idEmpleadoSociedadNomina` | `bigint` | PK |
| `idClaseDocumento` | `int` | FK → `ClaseDocumentoNomina` |
| `idMunicipio` | `bigint` | FK → `MunicipioNomina` (residencia / nacimiento) |
| `idMunicipioExpedicion` | `int` | FK → `MunicipioNomina` (expedición documento) |
| `idResponsabilidadTercero` | `int` | FK → `ResponsabilidadTercero` |
| `idCuentaBancariaNomina` | `int` | FK → `CuentaBancariaNomina` |
| `descripcion` | `varchar(150)` | Nombre completo |
| `primerNombre` | `varchar(50)` | |
| `segundoNombre` | `varchar(50)` | |
| `primerApellido` | `varchar(50)` | |
| `segundoApellido` | `varchar(50)` | |
| `nit` | `varchar(15)` | Identificación |
| `genero` | `char(1)` | M / F |
| `estadoCivil` | `char(1)` | |
| `peso` | `numeric(18,2)` | Kg |
| `estatura` | `numeric(18,2)` | Metros |
| `direccion` | `varchar(150)` | |
| `telefono` | `varchar(50)` | |
| `celular` | `varchar(50)` | |
| `email` | `varchar(150)` | Email empresarial |
| `emailPersonal` | `varchar(150)` | |
| `fechaNacimiento` | `datetime` | |
| `numeroPersona` | `varchar(50)` | = número identificación |
| `bloqueoPago` | `bit` | |
| `numeroCuentaBanco` | `varchar(50)` | |
| `titularCuentaBanco` | `varchar(150)` | |
| `regimenLaboral` | `bit` | Anterior / Nuevo |
| `auxilioTransporte` | `bit` | |
| `fechaContratacion` | `datetime` | |
| `subTipoCotizante` | `bigint` | |
| `altoRiesgo` | `bit` | |
| `estado` | `bit` | Activo / inactivo |
| `fechaCreacion` | `datetime` | Auditoría |
| `fechaModificacion` | `datetime` | Auditoría |
| `idUsuarioCreacion` | `int` | Auditoría |
| `idUsuarioModificacion` | `int` | Auditoría |

> Los nombres y tipos exactos de cada columna deben validarse contra el modelo EDMX o `sys.columns` — [REQUIERE_VALIDACION]. El diagrama del PDF presenta varias columnas con texto difuminado.

### Tablas relacionadas (esquema `Tercero` / `Nomina`)

- **`MunicipioNomina` (Gnl)** — catálogo de municipios. PK `idMunicipio`.
- **`SitioUbicacionNomina`** — sitio de ubicación del empleado.
- **`ResponsabilidadTercero`** — responsabilidades tributarias del tercero.
- **`ClaseDocumentoNomina`** — catálogo de clases de documento DIAN (códigos 11..44).
- **`ActividadEconomicaTercero`** — actividad económica (ARL).
- **`TerceroUsuarioNomina`** — relación tercero ↔ usuario del sistema.
- **`Contrato (Nomina)`** — contratos del empleado.
- **`EmpleadoRetencionFuente`** — configuración de retención en la fuente.
- **`CuentaBancariaNomina`** — cuentas bancarias para pago de nómina.

### Relaciones inferidas del diagrama

```
            MunicipioNomina ◄────── EmpleadoSociedadNomina ──────► SitioUbicacionNomina
                                            │   (Tercero)
       ResponsabilidadTercero ◄─────────────┤
                                            ├──────► ClaseDocumentoNomina
       ActividadEconomicaTercero ◄──────────┤
                                            ├──────► TerceroUsuarioNomina
                                            │
                                            ├──────► Contrato (Nomina)
                                            │
                                            ├──────► EmpleadoRetencionFuente
                                            │
                                            └──────► CuentaBancariaNomina
```

Cardinalidades documentadas en el diagrama (aproximadas):

- `EmpleadoSociedadNomina` **1 ── N** `Contrato` (Nomina).
- `EmpleadoSociedadNomina` **1 ── N** `EmpleadoRetencionFuente`.
- `EmpleadoSociedadNomina` **N ── 1** `MunicipioNomina` (ubicación / nacimiento / expedición).
- `EmpleadoSociedadNomina` **N ── 1** `ClaseDocumentoNomina`.
- `EmpleadoSociedadNomina` **N ── 1** `SitioUbicacionNomina`.
- `EmpleadoSociedadNomina` **1 ── N** `ActividadEconomicaTercero` (lista de actividades).
- `EmpleadoSociedadNomina` **1 ── N** `ResponsabilidadTercero`.
- `EmpleadoSociedadNomina` **1 ── 1** `TerceroUsuarioNomina` (vínculo a usuario del sistema).
- `EmpleadoSociedadNomina` **1 ── N** `CuentaBancariaNomina`.

> Las tablas que respaldan **Sueldos histórico**, **Parámetro Contable**, **Estudios**, **Idiomas**, **Aficiones**, **Grupo Familiar**, **Archivos**, **Firma** y los **Parafiscales históricos** **no aparecen explícitamente en el diagrama** del PDF — [REQUIERE_VALIDACION] contra el EDMX.

---

## Flujos funcionales

### Flujo 1 — Crear empleado (alta)

**Entrada:**
- Información personal completa.
- Documento físico de identidad.
- Documentos de afiliación (EPS, ARP, fondos).
- Contrato firmado.
- Cuenta bancaria.

**Proceso:**
1. Botón **Nuevo** en la barra superior (estado: "Creando Registro").
2. **Pestaña Datos Básicos**:
   - Seleccionar `Clase Documento` y diligenciar `Cédula`, `Ciudad Expedición`.
   - Diligenciar nombres y apellidos.
   - Seleccionar `Género`, `Tipo RH`, `Fecha Nacimiento`, `Ciudad` (nacimiento), `Estado Civil`.
   - Marcar `Auxilio de Transporte`.
   - Cargar foto.
3. **Pestaña Dirección**:
   - Seleccionar `País → Departamento → Municipio → Sitio Ubicación`.
   - Diligenciar `Dirección`, `Email Empresa`, `Email Personal`, `Teléfono`, `Celular`.
   - Asociar `Actividad Económica ARL`.
4. **Pestaña Grupo Empresarial**:
   - Asignar `Unidad Negocio`, `Centro Económico`, `Centro Costo`, `Tipo Nómina`, `Dependencia`, `Cargo`, `Nivel Jerárquico`, `Área Funcional`, `Riesgo ARP`.
   - Seleccionar `Régimen Laboral` (Anterior / Nuevo).
   - Registrar `Fecha Ingreso (Planilla Única)`.
   - Capturar `Número Persona` (= identificación).
   - Configurar `Bloqueo Pago = No`.
5. **Pestaña Nómina** (recorrer sub-pestañas):
   - **Contrato**: tipo, modelo, sueldo, meses, fechas.
   - **Seguridad Social**: EPS, ARP, Caja, Fondo Pensión, Fondo Cesantías, Fondo Solidaridad. Guardar con `BOTONGUARDARSEGURIDADSOCIAL`.
   - **Bancaria**: banco, tipo, número, titular.
   - **Parámetro Contable**: parametrizar.
   - **Estudios / Idiomas / Aficiones / Grupo Familiar**: registrar.
   - **Archivos**: cargar hoja de vida, contrato, estudios, afiliaciones, varios.
6. **Pestaña Firma**: cargar imagen PNG 227x76.
7. **Pestaña Retención Fuente**: elegir Procedimiento 1 (default) o 2.
8. Botón **Guardar** de la barra superior.

**Validaciones:**
- Clase Documento + Cédula únicos — [REQUIERE_VALIDACION].
- Permisos por control — el sistema bloquea / oculta los campos para los que el usuario no tiene permiso.
- Foto 178x153 px y firma 227x76 px.

**Salida:**
- Empleado creado en `Tercero.EmpleadoSociedadNomina` y tablas relacionadas.
- Contrato registrado en `Nomina.Contrato`.
- Cuenta bancaria en `CuentaBancariaNomina`.
- Configuración de retención en `EmpleadoRetencionFuente`.

**Errores posibles:**
- Documento duplicado.
- Permisos insuficientes — el botón Guardar de una sub-pestaña no se habilita.
- Imagen con formato/resolución incorrecta.

---

### Flujo 2 — Editar empleado

**Entrada:** identificación del empleado a editar.

**Proceso:**
1. Botón **Buscar**.
2. Localizar y abrir el empleado.
3. Modificar **únicamente los campos con permiso**. Los campos sin permiso quedan en solo lectura.
4. Botón **Guardar**.

**Validaciones:**
- Cada campo se valida con su permiso atómico.
- Cambios en campos críticos (Sueldo, Tipo Nómina, Régimen Laboral, EPS / ARP / Fondos) se persisten en la entidad correspondiente; el cambio de sueldo deja **histórico** en la sub-pestaña Sueldos.

**Salida:** registros actualizados con auditoría (`fechaModificacion`, `idUsuarioModificacion`).

---

### Flujo 3 — Consultar empleado

**Entrada:** filtro (NIT, nombre, tipo nómina, dependencia).

**Proceso:**
1. Botón **Buscar**.
2. Filtrar y abrir empleado.
3. Navegar por las pestañas/sub-pestañas según permisos.

**Salida:** vista del registro, históricos de sueldos, parafiscales y documentos.

---

### Flujo 4 — Bloquear pago de un empleado

**Entrada:** identificación del empleado.

**Proceso:**
1. Abrir empleado → pestaña **Grupo Empresarial**.
2. Cambiar `Bloqueo Pago = Sí`. Requiere permiso `BLOQUEOPAGO`.
3. **Guardar**.

**Validaciones:** permiso `BLOQUEOPAGO`.

**Salida:** el empleado no entra a la liquidación de pago hasta que se desbloquee.

---

### Flujo 5 — Cambiar sueldo del empleado

**Entrada:** identificación del empleado + nuevo sueldo.

**Proceso:**
1. Abrir empleado → pestaña **Nómina** → sub-pestaña **Contrato**.
2. Modificar campo **Sueldo** (requiere `SUELDOCONTRATO`).
3. **Guardar**.

**Validaciones:**
- Permiso `SUELDOCONTRATO`.
- Sueldo > 0 — [REQUIERE_VALIDACION].
- Solo una fila de Sueldos con `Estado = Activo` — [REQUIERE_VALIDACION].

**Salida:** sueldo anterior queda inactivo en la sub-pestaña Sueldos; el nuevo queda activo.

---

### Flujo 6 — Cargar documentación del empleado (Archivos)

**Entrada:** archivos físicos digitalizados.

**Proceso:**
1. Sub-pestaña **Archivos**.
2. Seleccionar la categoría (Hoja de Vida / Contrato / Estudios / Afiliaciones / Varios).
3. Adjuntar archivo.

**Validaciones:** [REQUIERE_VALIDACION] sobre tipos y tamaños permitidos.

**Salida:** documentos visibles en la sub-pestaña **Visualización Archivos** (solo lectura).

---

### Flujo 7 — Cambiar procedimiento de retención en la fuente

**Entrada:** empleado + procedimiento (1 o 2).

**Proceso:**
1. Pestaña **Retención Fuente**.
2. Seleccionar Procedimiento 1 o 2.
3. **Guardar**.

**Validaciones:** permiso `CALCULORETENCIONFUENTE`.

**Salida:** registro en `EmpleadoRetencionFuente`. Afecta los próximos cálculos de retención.

---

### Flujo 8 — Retiro / desactivación

> El PDF **no documenta un flujo explícito** de retiro. La inferencia es que se gestiona por:
> 1. `Fecha Terminación` en sub-pestaña Contrato.
> 2. `estado` en la tabla `EmpleadoSociedadNomina` (activo / inactivo).
> 3. `Bloqueo Pago = Sí` como detención temporal.
> [REQUIERE_VALIDACION].

---

## Casos borde

1. **Documento duplicado**: dos empleados con la misma `Clase Documento` + `Cédula` — [REQUIERE_VALIDACION] (debe rechazarse al guardar).
2. **Cambio de Régimen Laboral** en un empleado existente: impacta cálculo de cesantías históricas y futuras — [REQUIERE_VALIDACION].
3. **Cambio de Tipo Nómina**: puede afectar liquidaciones en curso — [REQUIERE_VALIDACION].
4. **Cambio de EPS / Fondos a mitad de mes**: convivencia con cálculos de seguridad social — [REQUIERE_VALIDACION].
5. **Cuenta bancaria inválida** (banco vs. tipo cuenta vs. número): el sistema no documenta validación de dígito verificador — [REQUIERE_VALIDACION].
6. **Permisos insuficientes**: el botón **Guardar** de una sub-pestaña no se habilita y se pierden los cambios capturados — buena práctica avisar antes de capturar.
7. **Foto / Firma fuera de resolución**: 178x153 (foto) o 227x76 (firma). El comportamiento exacto (rechazo, redimensión) no está documentado — [REQUIERE_VALIDACION].
8. **Empleado bloqueado** que tiene novedades pendientes: ¿se calculan y quedan acumuladas hasta desbloqueo? — [REQUIERE_VALIDACION].
9. **Fecha Terminación anterior a Fecha Contrato**: no se aclara — [REQUIERE_VALIDACION].
10. **Sueldo = 0** o negativo: comportamiento — [REQUIERE_VALIDACION].
11. **Empleado de Alto Riesgo**: implicaciones en cotización a Fondo de Pensión y ARP — [REQUIERE_VALIDACION].
12. **Auxilio de Transporte = Sí** pero sueldo por encima del tope legal: validación — [REQUIERE_VALIDACION].
13. **Empleado sin Sitio Ubicación** o `Email Empresa`: si son obligatorios reales — [REQUIERE_VALIDACION].
14. **Cambio de Centro de Costo a mitad de periodo**: distribución contable — [REQUIERE_VALIDACION].

---

## Preguntas abiertas

1. ¿Cuál es la **clave funcional única** del empleado? ¿`Clase Documento + Cédula` o el `idEmpleadoSociedadNomina`?
2. ¿Cómo se almacena el histórico de **Sueldos**? ¿Tabla independiente con FK al empleado? Nombre exacto — [REQUIERE_VALIDACION].
3. ¿Cuáles son los **valores válidos** del campo `Estado Civil` y `Tipo RH`?
4. ¿Existe **regla DIAN** sobre cuándo `Auxilio Transporte = Sí` aplica (tope salarial)?
5. ¿Qué hace exactamente el botón **Sincronizar** de la barra superior?
6. ¿Cómo se determina el **número de contrato** (consecutivo): global, por tipo de nómina, por unidad de negocio?
7. ¿La **Visualización Archivos** tiene su propio permiso, o queda implícita en `PESTANAVISUALIZARARCHIVOS`?
8. ¿Cómo se versionan los **Sueldos** internamente? (¿al cambiar sueldo, se cierra la fila activa con `Fecha Final` y se crea otra?)
9. ¿Qué tabla(s) respaldan el **histórico de parafiscales** mostrado en la sub-pestaña Parafiscales? — [REQUIERE_VALIDACION].
10. ¿Qué tabla respalda **Parámetro Contable**, **Estudios**, **Idiomas**, **Aficiones**, **Grupo Familiar**, **Archivos**? — [REQUIERE_VALIDACION].
11. ¿Existen **stored procedures** específicos en el alta/edición del empleado? — [REQUIERE_VALIDACION].
12. ¿Hay **flujo de retiro** propio o se gestiona con `Fecha Terminación` + `estado = 0` + `Bloqueo Pago = Sí`?
13. ¿Cuál es la **regla de SubTipo Cotizante** (catálogo PILA) y cómo se elige?
14. ¿Cómo interactúa el campo **Afecta Planilla Única** (Contrato) con la pestaña Planilla Única del módulo Prestaciones Sociales?
15. ¿El permiso `CALCULORETENCIONFUENTE` controla tanto la pestaña en Grupo Empresarial como la pestaña Retención en la Fuente?

---

## Cómo extender esta skill

Esta Skill está diseñada como **única fuente de verdad consolidada** del módulo Empleado. Cualquier nueva fuente (PDF, documento Word, captura, hilo de soporte, decisión de negocio) debe **integrarse en este mismo archivo**, no en archivos paralelos.

### Cómo anexar nuevos PDFs / documentos

1. Leer la nueva fuente de principio a fin.
2. Identificar a qué sección del SKILL.md pertenece cada hallazgo (Formularios, Permisos, Reglas, Modelo de datos, Flujos, Casos borde, Preguntas abiertas).
3. **Insertar** el contenido en la sección correspondiente. No crear secciones nuevas a menos que sea un dominio realmente nuevo (en cuyo caso, agregar un encabezado `##` al mismo nivel que los existentes).
4. **No duplicar**: si la nueva fuente repite algo ya documentado, complementarlo. Si lo contradice, abrir una entrada en **Preguntas abiertas** y resolverlo con el dueño del dominio (Ferney).
5. Cualquier campo o regla resuelta desde **Preguntas abiertas** debe migrarse a la sección estable (Reglas / Modelo de datos / Flujos) y eliminarse de Preguntas abiertas.

### Cómo fusionar nuevo contexto

- Toda **adición** debe identificar su **origen** (nombre del documento, fecha, autor) en el Historial de actualizaciones.
- Toda **modificación** de una regla existente debe explicar por qué cambió.
- Toda **etiqueta `[REQUIERE_VALIDACION]`** debe resolverse en una de tres formas:
  - Confirmarla → reemplazar la etiqueta por el dato definitivo.
  - Refutarla → ajustar el contenido y eliminar la etiqueta.
  - Postergarla → moverla a **Preguntas abiertas** con contexto.

### Cómo versionar cambios

- Cada actualización suma una fila en **Historial de actualizaciones** (sección de Mantenimiento y evolución).
- El cambio debe incluir: fecha (formato `YYYY-MM-DD`), fuente (documento o conversación), descripción breve del cambio y responsable.
- **No eliminar** conocimiento previo salvo que sea refutado por evidencia explícita.

### Cómo evitar contradicciones

- Antes de agregar una regla, **buscar** si ya existe una equivalente o contraria en el documento.
- Si hay contradicción, **no escoger en silencio**: documentarlo en Preguntas abiertas y escalarlo al dueño del dominio.
- Mantener el **glosario implícito**: usar siempre los mismos términos (Tipo Nómina, Régimen Laboral, Régimen Anterior/Nuevo, Bloqueo Pago, Número Persona, etc.).
- Si un cambio impacta a otras Skills del mismo dominio (Novedades, Incapacidad, Prestaciones Sociales), agregar nota de **impacto cruzado** y avisar a los suplentes del dominio.

---

## Mantenimiento y evolución de la Skill

Esta Skill fue construida inicialmente a partir de contextualización textual, conocimiento funcional del negocio y documentación funcional y técnica disponible, pero está diseñada para evolucionar continuamente.

### Reglas de mantenimiento

- La documentación inicial corresponde a la fuente original usada para crear esta Skill (`EmpleadoSociedad.pdf` — 10 páginas con formulario, pestañas, permisos y diagrama de BD).
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
| 2026-05-11 | `EmpleadoSociedad.pdf` | Creación inicial consolidada de la Skill (formulario `Empleado Sociedad V2`, 5 pestañas principales, 12 sub-pestañas de Nómina, ~60 permisos atómicos, catálogo de Clase Documento DIAN, modelo de datos `Tercero.EmpleadoSociedadNomina` + 9 tablas relacionadas, 8 flujos funcionales, casos borde y preguntas abiertas). | Ferney Acosta |

### Información pendiente

Marcar como **Pendiente de documentar** cualquier información que aún no exista o no haya sido documentada. No usar la frase "no documentado en la fuente": esta Skill debe poder evolucionar con nuevas fuentes.

Pendientes actuales por validar contra BD / EDMX / código de dominio:

- Nombres y tipos exactos de columnas de `Tercero.EmpleadoSociedadNomina` (varias columnas del diagrama son ilegibles en el PDF) — **Pendiente de documentar**.
- Tablas que respaldan **Sueldos histórico, Parámetro Contable, Estudios, Idiomas, Aficiones, Grupo Familiar, Archivos, Firma, Parafiscales históricos** — **Pendiente de documentar**.
- Reglas de unicidad real sobre `Clase Documento + Cédula` — **Pendiente de documentar**.
- Catálogo de valores de `Estado Civil`, `Tipo RH`, `SubTipo Cotizante`, `Tipo Cuenta`, `Estado` — **Pendiente de documentar**.
- Validaciones de cuenta bancaria (banco/tipo/dígito verificador) — **Pendiente de documentar**.
- Comportamiento ante foto/firma con resolución incorrecta — **Pendiente de documentar**.
- Flujo formal de **retiro / desactivación** del empleado — **Pendiente de documentar**.
- Reglas exactas del campo **Afecta Planilla Única** en el contrato — **Pendiente de documentar**.
- Alcance exacto del permiso `CALCULORETENCIONFUENTE` (¿controla Grupo Empresarial + Retención Fuente?) — **Pendiente de documentar**.
- Procedimientos almacenados involucrados en el alta/edición — **Pendiente de documentar**.
- Comportamiento del botón **Sincronizar** de la barra superior — **Pendiente de documentar**.

> Cuando se actualice la Skill: si cambian campos, permisos, tablas o reglas, reflejarlo aquí, agregar la fila correspondiente al **Historial de actualizaciones** y avisar a los suplentes del dominio.
