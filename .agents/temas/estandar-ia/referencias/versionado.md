# Manejo de versiones — vMAJOR_minor

Notación: `v<MAJOR>_<minor>`. La versión es **contrato**: un consumidor sabe — sólo leyendo el sufijo — si su integración sigue funcionando.

---

## Las tres situaciones

### `v1_0` — Versión inicial

El primer release estable de un activo. Major 1, minor 0.

A partir de aquí, cualquier cambio se categoriza.

```
FIN-QRY-Cartera_VencimientoCliente_v1_0
```

---

### `v1_1` — Refinamiento (compatible hacia atrás)

Misma lógica, mismo contrato. Refinamiento de redacción, ajuste de tono, mejor ejemplo.

**No rompe integraciones.**

```
FIN-QRY-Cartera_VencimientoCliente_v1_1
```

Casos típicos:
- Mejora de un ejemplo en EXAMPLES
- Reformulación de una regla en GUIDELINES sin cambiar su intención
- Ajuste menor en el HEADER
- Aclaración en la sección PROCESS

---

### `v2_0` — Ruptura (incompatible)

Lógica reestructurada. Cambia el contrato: input, output o reglas duras.

**Romperá integraciones.** Coexiste con `v1_x` durante el período de deprecación.

```
FIN-QRY-Cartera_VencimientoCliente_v2_0
```

Casos típicos:
- Cambio del esquema de OUTPUT (ej. JSON pasa de array a objeto anidado)
- Eliminación o cambio semántico de una regla en RULES
- Cambio en el contrato esperado de input
- Reescritura de la sección PROCESS que cambia los pasos del razonamiento

---

## Decisión rápida

```
¿El cambio puede romper a un consumidor existente?
├── SÍ  → v2_0 (o v3_0, v4_0...)
└── NO  → ¿Cambia la intención de alguna sección?
          ├── SÍ  → v1_1 (o v1_2...)
          └── NO  → no requiere nueva versión (corrección menor sin cambio observable)
```

---

## Reglas

1. **El minor reinicia con cada major.** De `v1_5` saltar a una ruptura es `v2_0`, no `v2_6`.
2. **Las versiones no se reutilizan.** Una vez publicada `v1_0`, nunca se sobrescribe; los cambios crean `v1_1`.
3. **Coexistencia durante deprecación:** cuando se publica `v2_0`, la `v1_x` se mantiene activa hasta que todos los consumidores migren.
4. **Versionar implica documentar:** cada nueva versión registra qué cambió y por qué (en HEADER o en un changelog del activo).

---

## Ver también

- [estructura-prompt.md](estructura-prompt.md) — qué cambios afectan qué sección
- [dimensiones.md](dimensiones.md) — el resto del nombre canónico
