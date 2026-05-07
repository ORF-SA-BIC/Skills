# Dimensiones — Códigos del estándar ORF-STD-IA-2026-01

Cada activo de IA se describe en **6 dimensiones**. Tres se codifican en el nombre, tres viven como metadata.

## Anatomía del nombre

```
FIN-QRY-Cartera_VencimientoCliente_v1_0
 ①   ②          ③                   ④
```

| # | Segmento | Ejemplo |
|---|---|---|
| ① | Fuente/Dominio | `FIN` |
| ② | Tipo | `QRY` |
| ③ | Nombre (PascalCase + snake_case) | `Cartera_VencimientoCliente` |
| ④ | Versión | `v1_0` |
| ⑤ | Owner (metadata aparte) | `Cartera · ORF` |

> **Nota sobre el segmento ①:** según el documento puede ser **Proceso** (preferido) o **Fuente/Dominio** cuando el activo es agnóstico de proceso. Es decir, el segmento 1 puede contener un código de cualquiera de las dos tablas siguientes (Proceso o Fuente).

---

## Dimensión 02 — Fuente / Dominio (8 códigos)

De qué sistema o capa proviene el dato o la lógica que el activo invoca. Cerrado · gobernanza.

| Código | Significado | Descripción |
|---|---|---|
| `ERP` | Núcleo del ERP | Procesos núcleo del ERP — datos transaccionales y maestros |
| `CRM` | Datos del cliente | Datos de relacionamiento y ciclo de vida del cliente |
| `CONV` | Capa conversacional | Interacción natural con usuarios |
| `FUSE` | Fusión y consultas | Consultas a través de múltiples fuentes |
| `PRED` | Predicción y scoring | Modelos de pronóstico |
| `ALERT` | Alertas y monitoreo | Eventos sobre umbrales |
| `SYS` | Sistema / infraestructura | Prompts de plataforma |
| `GOV` | Gobernanza y enrutamiento | Políticas y direccionamiento |

---

## Dimensión 03 — Proceso de negocio (8 códigos)

A qué proceso de la cadena de valor sirve el activo. Es la dimensión que conecta el inventario de IA con las áreas funcionales. Cerrado · gobernanza.

| Código | Significado | Descripción |
|---|---|---|
| `FIN` | Finance | Tesorería, Contabilidad, Cartera, Costos, Presupuesto |
| `SAL` | Sales | Ventas, gestión comercial, pipeline y forecast |
| `SCM` | Supply chain | Cadena de suministro, abastecimiento, logística entrante |
| `OPS` | Operations | Operaciones internas, manufactura, planta, mantenimiento |
| `HR` | Gestión Humana | Talento, nómina, desarrollo, clima |
| `CST` | Servicio al cliente | Postventa, retención y satisfacción |
| `MKT` | Mercadeo | Marca, contenido, demanda y posicionamiento |
| `EXE` | Ejecutivo / transversal | Comité, dirección, reportes ejecutivos transversales |

---

## Dimensión 04 — Tipo (8 códigos)

Qué hace el prompt — su función operativa. Cerrado · gobernanza.

| Código | Significado | Descripción |
|---|---|---|
| `CTX` | Archivo de contexto | Reglas y definiciones de negocio que otros prompts consumen |
| `QRY` | Prompt de consulta | Genera T-SQL u otra consulta estructurada sobre datos |
| `ENR` | Prompt de enriquecimiento | Procesa datos no estructurados para extraer estructura |
| `RTR` | Prompt enrutador | Detecta intención y enruta al prompt o agente correcto |
| `SUM` | Prompt de resumen | Formatea y narra resultados — output legible al humano |
| `VAL` | Prompt de validación | Verifica calidad del output — gatekeeper de salida |
| `ALS` | Prompt de alerta | Monitorea umbrales y dispara notificaciones contextuales |
| `RPT` | Prompt de reporte | Generación estructurada de reportes recurrentes |

---

## Reglas de uso

1. **Códigos cerrados:** no se inventan códigos nuevos. Cualquier extensión requiere aprobación del comité de gobernanza (fase F4).
2. **Posición fija:** los códigos van en mayúsculas, separados por guion (`-`).
3. **Nombre del activo:** usa `PascalCase` para palabras conceptuales y `snake_case` (`_`) para separar conceptos. Ejemplo: `Cartera_VencimientoCliente` (cartera = concepto 1, vencimiento del cliente = concepto 2).
4. **Mutabilidad:** los códigos NO cambian. Si una dimensión necesita ajuste, se revisa el estándar (no el activo individual).
