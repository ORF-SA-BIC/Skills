# Estructura interna del prompt — 9 secciones obligatorias

Todo activo de IA productivo en ORF respeta el mismo esqueleto de 9 secciones, **en este orden, sin excepción**. Predecibilidad para el operador, mantenibilidad para el equipo.

| # | Sección | Propósito |
|---|---|---|
| 1 | **HEADER** | Identidad y metadata: nombre completo, versión, dueño, fecha y propósito en una línea |
| 2 | **ROLE** | Qué identidad asume el asistente IA: persona, especialidad y autoridad. Define voz, alcance y límites. Ej: _"Eres analista financiero de ORF, experto en cartera"_ |
| 3 | **CONTEXT** | Qué datos y sistemas están en alcance: esquemas, términos de negocio, fuentes que el modelo puede usar |
| 4 | **RULES** | Reglas duras — nunca violar. Política inviolable: PII, formato, fronteras de datos |
| 5 | **GUIDELINES** | Guías blandas — aplicar con criterio. Estilo, tono, criterio del modelo cuando hay ambigüedad |
| 6 | **PROCESS** | Instrucciones paso a paso. Cómo razonar el problema antes de responder |
| 7 | **OUTPUT** | Formato de salida exacto requerido. Esquema (JSON, T-SQL, prosa estructurada) sin ambigüedad |
| 8 | **EXAMPLES** | Un ejemplo bueno y uno malo explicados. Calibración por contraste |
| 9 | **GUARDRAILS** | Qué hacer cuando algo sale mal: input fuera de scope, datos faltantes, conflicto de reglas |

---

## Reglas

1. **Orden fijo:** del 1 al 9. No se reordenan ni se intercalan.
2. **Todas presentes:** no se omiten secciones. Si una no aplica, se documenta como "N/A — razón" para que la auditoría sepa que no fue olvido.
3. **Nombres exactos:** los encabezados van con esos nombres en mayúsculas (HEADER, ROLE, CONTEXT, RULES, GUIDELINES, PROCESS, OUTPUT, EXAMPLES, GUARDRAILS).
4. **Separación lógica:** RULES (duras) vs GUIDELINES (blandas) deben mantenerse separadas. Mezclarlas es el principal antipatrón del estándar.

## Diferencia clave: RULES vs GUIDELINES

| Tipo | Carácter | Ejemplo |
|---|---|---|
| **RULES** | Inviolable. La IA NO puede contradecir esto bajo ningún caso. | "Nunca devolver columnas con PII en consultas de cartera" |
| **GUIDELINES** | Aplicar con criterio. La IA puede modular según contexto. | "Preferir respuestas concisas. Si el usuario pide detalle, expandir." |

Si una regla "se puede saltar a veces" → es GUIDELINE.
Si una regla "no se viola jamás" → es RULE.

## Ver también

- [PROMPT-TEMPLATE.md](../assets/PROMPT-TEMPLATE.md) — plantilla copy-paste con las 9 secciones
- [dimensiones.md](dimensiones.md) — códigos para nombrar el activo
- [versionado.md](versionado.md) — cuándo subir versión
