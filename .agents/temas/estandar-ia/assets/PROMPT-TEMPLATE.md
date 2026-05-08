# {NOMBRE-COMPLETO-DEL-ACTIVO}

Plantilla canónica de prompt según ORF-STD-IA-2026-01. Copiar este archivo y reemplazar los `{placeholders}`. **Las 9 secciones son obligatorias y van en este orden, sin excepción.**

> Convenciones del nombre del archivo: usar el nombre canónico completo, ej. `FIN-QRY-Cartera_VencimientoCliente_v1_0.md`

---

## 1. HEADER

```yaml
nombre:    {FUENTE-TIPO-Nombre_vMAJOR_minor}
version:   {v1_0 | v1_1 | v2_0 | ...}
dueño:     {Persona o área responsable}
proceso:   {FIN | SAL | SCM | OPS | HR | CST | MKT | EXE}
fuente:    {ERP | CRM | CONV | FUSE | PRED | ALERT | SYS | GOV}
fecha:     {YYYY-MM-DD}
proposito: {Una sola frase clara: qué hace este activo}
```

---

## 2. ROLE

Eres {persona / especialidad / autoridad del modelo}.

Por ejemplo:
- _"Eres un analista financiero de ORF, experto en cartera y vencimientos de clientes corporativos."_
- _"Eres un agente de servicio al cliente especializado en postventa de servicios B2B."_

Define voz, alcance y límites del modelo en este activo.

---

## 3. CONTEXT

Datos, sistemas, esquemas y términos de negocio que el modelo puede usar:

- **Sistemas en alcance:** {ej. ERP Arsys, módulo de Cartera}
- **Esquemas/tablas relevantes:** {ej. `cartera_cliente`, `factura_pendiente`}
- **Términos de negocio:** {definir glosario crítico, ej. "vencimiento" = días desde la fecha de emisión}
- **Fuentes externas permitidas:** {si aplica}

---

## 4. RULES

Reglas duras — **nunca violar**. Política inviolable.

- {Regla inviolable 1, ej. "Nunca devolver el número de identificación completo del cliente, solo los últimos 4 dígitos"}
- {Regla 2, ej. "Solo consultar tablas con prefijo `cartera_*`"}
- {Regla 3, ej. "El output debe ser T-SQL ejecutable contra SQL Server 2019+"}

---

## 5. GUIDELINES

Guías blandas — aplicar con criterio. Estilo, tono, ambigüedad.

- {Guía 1, ej. "Preferir respuestas concisas; expandir solo si el usuario pide detalle"}
- {Guía 2, ej. "Cuando un campo de fecha esté ambiguo, asumir el formato `DD/MM/YYYY`"}
- {Guía 3, ej. "Tono profesional, segunda persona singular ('usted')"}

---

## 6. PROCESS

Instrucciones paso a paso: cómo razonar el problema antes de responder.

1. {Paso 1 — ej. "Identificar las entidades mencionadas en el input (cliente, fecha, monto)"}
2. {Paso 2 — ej. "Determinar el rango temporal de la consulta"}
3. {Paso 3 — ej. "Construir la consulta T-SQL con joins explícitos"}
4. {Paso 4 — ej. "Validar que ningún campo PII quede en la SELECT"}
5. {Paso 5 — ej. "Devolver según el formato de OUTPUT"}

---

## 7. OUTPUT

Formato de salida exacto. Esquema sin ambigüedad.

```{json|sql|yaml|prosa}
{
  "ejemplo_de_estructura": "valor"
}
```

O si es prosa estructurada, especificar las secciones obligatorias del output.

---

## 8. EXAMPLES

### ✅ Ejemplo correcto

**Input:**
```
{ejemplo de input válido}
```

**Output:**
```
{output esperado}
```

**Por qué es correcto:** {explicación breve}

### ❌ Ejemplo incorrecto

**Input:**
```
{mismo input o uno que invita al error}
```

**Output equivocado:**
```
{output que NO se debe producir}
```

**Por qué es incorrecto:** {explicación breve — ej. "Devuelve el número de identificación completo, viola RULE 1"}

---

## 9. GUARDRAILS

Qué hacer cuando algo sale mal.

| Situación | Acción |
|---|---|
| Input fuera de scope | {ej. "Responder: 'Esta consulta está fuera del alcance de cartera. Consulte con FIN-QRY-Tesoreria_*.'"} |
| Datos faltantes | {ej. "Pedir explícitamente el dato faltante en lugar de asumirlo"} |
| Conflicto entre RULES y GUIDELINES | RULES siempre prevalece |
| Conflicto entre dos RULES | {ej. "Detener ejecución y reportar el conflicto al dueño del activo"} |
| Output que viola alguna RULE en revisión final | Descartar y regenerar |

---

## Checklist antes de publicar

- [ ] El nombre del archivo respeta el formato canónico `FUENTE-TIPO-Nombre_vMAJOR_minor.md`
- [ ] Las 9 secciones están presentes y en orden
- [ ] HEADER tiene los 7 campos completos
- [ ] RULES y GUIDELINES están separadas (no mezcladas)
- [ ] EXAMPLES incluye uno correcto y uno incorrecto, ambos explicados
- [ ] GUARDRAILS cubre al menos los casos: fuera de scope, datos faltantes, conflicto de reglas
- [ ] La versión refleja correctamente el tipo de cambio (v1_0 / v1_x / v2_0)
