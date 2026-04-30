# Guía para el equipo — Repertorio de Skills

Esta guía explica cómo cada persona del equipo (Deimar, Ferney, Oscar, Daniel, Reyving) trabaja con su propia rama del repositorio para alimentar el repertorio.

---

## Convenciones

- Cada persona tiene una rama con su nombre: `Deimar`, `Ferney`, `Oscar`, `Daniel`, `Reyving`.
- Cada persona trabaja únicamente sobre `personas/<su-nombre>/` (perfil + temas de negocio).
- El merge a `main` lo hace el responsable del repo (no autoservicio).
- Antes de pushear, **siempre** sincronizar con `main` para evitar conflictos.

---

## 1. Setup inicial (solo la primera vez)

Reemplaza `{TU_NOMBRE}` por tu nombre real (`Deimar`, `Ferney`, `Oscar`, `Daniel` o `Reyving`).

```bash
# 1. Clonar el repositorio
git clone https://github.com/rmramirezORF/Skills.git
cd Skills

# 2. Descargar todas las ramas remotas
git fetch --all

# 3. Crear tu rama local apuntando a la remota
git checkout -b {TU_NOMBRE} origin/{TU_NOMBRE}

# 4. Confirmar que estás en tu rama
git branch
# Deberías ver un asterisco (*) junto a tu nombre
```

---

## 2. Trabajar en tu rama (día a día)

### 2.1 Antes de empezar a trabajar — sincronizar con main

```bash
# Asegurar que estás en tu rama
git checkout {TU_NOMBRE}

# Bajar los últimos cambios de todas las ramas
git fetch --all

# Traer los cambios de main a tu rama
git merge origin/main

# Si hay conflictos, resolverlos antes de continuar.
# Los archivos en personas/{TU_NOMBRE}/ NUNCA deberían tener conflictos
# porque solo tú trabajas ahí.
```

### 2.2 Agregar tu contenido

Tu trabajo va dentro de `personas/{TU_NOMBRE}/`:

```
personas/{TU_NOMBRE}/
├── PERFIL.md           ← Completar con tu rol, dominios, suplentes
└── temas/
    └── <tu-dominio>/   ← Por cada dominio que manejas (nómina, contabilidad, etc.)
        ├── README.md
        ├── glosario.md
        ├── WORKFLOW.md (opcional)
        └── skills/
            └── <skill>/SKILL.md
```

**Plantilla de referencia:** copiar la carpeta `temas/meta/skills/skill-creator/assets/TEMA-NEGOCIO-TEMPLATE/` como base para crear un dominio nuevo.

### 2.3 Antes de pushear — sincronizar otra vez con main

```bash
git fetch --all
git merge origin/main

# Resolver cualquier conflicto si aparece
```

### 2.4 Commitear y pushear a TU rama

```bash
# Ver qué cambió
git status

# Agregar tus archivos (preferible especificar carpeta tuya)
git add personas/{TU_NOMBRE}/

# Commit con mensaje descriptivo
git commit -m "feat(personas): agregar tema de nomina en mi perfil"

# Push a TU rama (no a main)
git push origin {TU_NOMBRE}
```

### 2.5 Avisar al responsable del repo

Cuando termines un avance significativo, avisa al responsable para que haga el merge a `main`.

---

## 3. Reglas importantes

| ✅ Sí | ❌ No |
|---|---|
| Trabajar siempre en TU rama | Hacer push directo a `main` |
| Modificar SOLO `personas/{TU_NOMBRE}/` | Modificar archivos de otras personas |
| Sincronizar con `main` antes de cada push | Pushear sin sincronizar (genera conflictos) |
| Usar la plantilla TEMA-NEGOCIO-TEMPLATE como base | Inventar estructuras nuevas |
| Pedir merge a `main` al responsable | Hacer merge tú mismo a `main` |

---

## 4. Sobre `.claude/` (config local de Claude Code)

La carpeta `.claude/` está en `.gitignore` porque depende del entorno de cada persona. **No la subas al repo.**

Si después de un `git pull` tu `.claude/settings.local.json` desaparece (puede pasar la primera vez), reconfígurala desde Claude Code y listo. No necesitas restaurarla desde git.

---

## 5. Comandos rápidos (copy-paste)

### Inicio de sesión de trabajo
```bash
git checkout {TU_NOMBRE}
git fetch --all
git merge origin/main
```

### Final de sesión de trabajo
```bash
git fetch --all
git merge origin/main
git add personas/{TU_NOMBRE}/
git commit -m "<mensaje claro de qué agregaste>"
git push origin {TU_NOMBRE}
```

### Si te equivocaste de rama y quieres pasar tus cambios sin perderlos
```bash
git stash                        # guarda cambios temporalmente
git checkout {TU_NOMBRE}         # cambia a tu rama
git stash pop                    # recupera los cambios
```

### Para ver el historial de TU rama
```bash
git log --oneline -10
```

### Para ver qué archivos cambiaste pero no commiteaste
```bash
git status
git diff
```

---

## 6. Resolución de conflictos (cuando aparecen)

Si al hacer `git merge origin/main` te aparece un conflicto:

```bash
# 1. Ver qué archivos están en conflicto
git status

# 2. Abrir cada archivo en conflicto y buscar las marcas:
#    <<<<<<< HEAD
#    (tu versión)
#    =======
#    (versión de main)
#    >>>>>>> origin/main

# 3. Decidir qué versión queda y borrar las marcas

# 4. Marcar como resuelto
git add <archivo-resuelto>

# 5. Confirmar el merge
git commit
```

Si tienes dudas con un conflicto, **detente y pregunta** antes de seguir. Es más rápido resolverlo bien la primera vez.

---

## 7. Estructura del repositorio (referencia)

```
.agents/
├── REPERTORIO.md             ← Activador del repertorio (@repertorio)
├── catalog/
│   ├── INDEX.md              ← Temas técnicos del equipo
│   └── PERSONAS.md           ← Personas y temas de negocio
├── temas/                    ← Temas técnicos transversales (NO tocar sin coordinar)
│   ├── hu/, calidad/, frontend/, git/, meta/, documentacion/, bugs/
└── personas/                 ← Aquí trabajas tú
    ├── deimar/
    ├── ferney/
    ├── oscar/
    ├── daniel/
    └── reyving/
        ├── PERFIL.md
        └── temas/<tu-dominio>/
```

---

## 8. ¿Dudas?

- **Sobre cómo crear una skill:** ver `temas/meta/skills/skill-creator/SKILL.md`
- **Sobre la plantilla de tema de negocio:** ver `temas/meta/skills/skill-creator/assets/TEMA-NEGOCIO-TEMPLATE/`
- **Sobre el repertorio en general:** leer `.agents/REPERTORIO.md`
- **Sobre git/conflictos:** preguntar al responsable del repo antes de forzar nada
