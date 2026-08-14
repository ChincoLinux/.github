# ChincoLinux · Metodología de Trabajo

> **Doctrina canónica:** [ADEV.md](ADEV.md) — fuente de verdad operativa para toda la organización.
> **Proving ground:** Homedir — de donde se extrajo el sistema transferible A-Dev.
> **Roadmap:** [Proyecto #3 — Roadmap Yap + ChincoLinux OS](https://github.com/orgs/ChincoLinux/projects/3)

---

## Índice

1. [Filosofía](#filosofía)
2. [Trunk-Based Development](#trunk-based-development)
3. [Issues](#issues)
4. [Labels](#labels)
5. [Forks y Branches](#forks-y-branches)
6. [Pull Requests](#pull-requests)
7. [Code Review](#code-review)
8. [Merge y Cleanup](#merge-y-cleanup)
9. [Project Board](#project-board)
10. [Automatización](#automatización)
11. [Paso a paso manual](#paso-a-paso-manual)

---

## Filosofía

ChincoLinux opera bajo la doctrina **A-Dev**, extraída del proving ground de Homedir.
Los principios no-negociables son:

1. **Cada cambio aterriza en `main` solo mediante PR atómica.** No hay push directo a `main`.
2. **Cada iteración sale de una rama dedicada** con un objetivo claro y un issue asociado.
3. **Commits convencionales** (`feat:`, `fix:`, `docs:`, `refactor:`, `chore:`, `test:`, `ci:`).
4. **No mezclar** refactor + feature + docs + infra en la misma PR (salvo batch explícito).
5. **CI verde antes de merge.** Si CI falla, se arregla antes de pedir review.
6. **Validación acotada:** ejecutar el check más mínimo que pruebe el cambio antes de abrir PR.
7. **Sin secretos** en código, commits, issues, o PRs.
8. **Contenido bilingüe:** español primario, inglés mirror cuando aplique.

Referencia completa: [ADEV.md § Non-Negotiable Rules](ADEV.md#non-negotiable-rules)

---

## Trunk-Based Development

ChincoLinux usa **Trunk-Based Development**: todos contribuyen a `main` (el trunk) mediante
PRs de vida corta. No hay ramas `develop` ni `release/*` de larga duración.

```
                    main (trunk, siempre verde)
                    ────────────────────────────────
                         ↑        ↑        ↑
                    merge PR   merge PR   merge PR
                         │        │        │
              feat/21-sesiones  fix/bug-3  docs/roadmap
              (rama corta)     (rama corta) (rama corta)
```

### Reglas del trunk

| Regla | Descripción |
|-------|-------------|
| `main` siempre verde | CI pasa en cada commit de `main` |
| Ramas de vida corta | < 7 días ideal, < 14 días máximo |
| Una rama = un objetivo | No mezclar scope en una rama |
| Rebase sobre `main` | Resolver conflictos con rebase, no merge commits |
| Sin force-push a `main` | `main` es protected; solo merge via PR |
| Tags manuales | Versionamiento en cadencia manual, no por PR |

### Branch protection (ruleset)

`main` está protegido por ruleset en todos los repos públicos de la org:
- Require PR before merge
- Require status checks (CI) before merge
- Require at least 1 approval from `core-devs`
- Dismiss stale reviews on new push
- Force-push: blocked
- Delete: blocked

---

## Issues

### Templates disponibles

Los issues usan templates de [`.github/ISSUE_TEMPLATE/`](.github/ISSUE_TEMPLATE):

| Template | Uso | Label auto |
|----------|-----|------------|
| `bug_report.yml` | Reportar un comportamiento incorrecto o falla | `bug` |
| `feature_request.yml` | Proponer una nueva función o mejora | `enhancement` |
| `blank_issues_enabled: true` | Issues libres (discusiones, preguntas) | — |

### Estructura de un issue

Todo issue debe tener:

1. **Título con prefijo convencional:** `feat:`, `fix:`, `docs:`, `test:`, `ci:`, `refactor:`
2. **Descripción del problema** (qué necesidad o fricción existe)
3. **Propuesta de solución** (cómo se resolvería)
4. **Criterios de aceptación** (checklist de qué debe cumplirse para cerrar)
5. **Prioridad** (P0/P1/P2/P3 — se setea en el Project Board)
6. **Referencias** (issues relacionados, dependencias)

### Ciclo de vida de un issue

```
[Open] → [In Progress] → [In Review] → [Done]
  │                                    ↑
  └──── [Blocked] ─────────────────────┘
              ↓
         [wontfix] / [invalid]
```

- **Open:** issue creado, esperando asignación
- **In Progress:** alguien está trabajando en una rama + PR
- **In Review:** PR abierta, esperando code review
- **Done:** PR mergeada, issue cerrado automáticamente (`Closes #N`)
- **Blocked:** depende de otro issue o hay un bloqueo externo

### Auto-asignación al proyecto

Todo issue nuevo se añade automáticamente al [Proyecto #3](https://github.com/orgs/ChincoLinux/projects/3)
mediante el workflow [auto-add-to-project.yml](.github/workflows/auto-add-to-project.yml).

---

## Labels

### Labels de tipo (heredadas de GitHub defaults)

| Label | Color | Uso |
|-------|-------|-----|
| `bug` | rojo | Error o comportamiento incorrecto |
| `enhancement` | azul claro | Mejora sobre funcionalidad existente |
| `feature-request` | azul claro | Nueva funcionalidad |
| `documentation` | azul | Cambios en docs |
| `documentation-improvement` | azul | Mejora de docs existentes |
| `question` | verde | Pregunta o duda |
| `duplicate` | gris | Issue duplicado |
| `invalid` | gris | Issue no válido |
| `wontfix` | blanco | No se va a fixear |

### Labels de impacto (bug severity)

| Label | Uso |
|-------|-----|
| `bug-impact-high` | Bug crítico, bloquea producción |
| `bug-impact-medium` | Bug importante, workaround existe |
| `bug-impact-low` | Bug menor, no bloquea |

### Labels de PR (workflow de review)

| Label | Color | Uso |
|-------|-------|-----|
| `pr:draft` | gris | PR en draft, scope no finalizado |
| `pr:needs-review` | azul | PR lista para review |
| `pr:checks-pending` | amarillo | CI corriendo |
| `pr:checks-failed` | rojo | CI falló |
| `pr:changes-requested` | naranjo | Reviewer pidió cambios |
| `pr:approved` | verde | Aprobado por `core-devs` |
| `pr:acceptance-ok` | azul oscuro | Criterios de aceptación cumplidos |
| `pr:blocked` | rojo oscuro | Bloqueado por dependencia |
| `pr:merged` | verde oscuro | Mergeado |
| `pr:risk-high` | naranjo | Riesgo alto, requiere review extra |
| `pr:risk-critical` | rojo oscuro | Riesgo crítico, requiere 2 approvals |
| `pr:i18n-ok` | azul oscuro | Validación i18n pasada |

### Labels operacionales

| Label | Color | Uso |
|-------|-------|-----|
| `good first issue` | verde | Ideal para nuevos contribuidores |
| `help wanted` | verde | Se busca ayuda |
| `needs-human` | rojo oscuro | Requiere intervención humana |
| `platform-maintenance` | azul oscuro | Mantenimiento de infra/plataforma |
| `accessibility` | morado | Relacionado con accesibilidad |
| `ai-sdlc-assist` | azul | Asistido por IA en el SDLC |
| `ai-sdlc-track` | azul claro | Tracking de trabajo con IA |

---

## Forks y Branches

### Forks

- **Contribuidores externos** (sin acceso a la org): fork → rama → PR desde fork
- **Miembros de la org** (`contributors`, `core-devs`): rama directa en el repo → PR
- **Sincronización de forks:** mantener el fork sincronizado con upstream antes de abrir PR

```bash
# Sincronizar fork con upstream
git remote add upstream https://github.com/ChincoLinux/Yap.git
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

### Nomenclatura de branches

Formato: `<tipo>/<issue-num>-<descripcion-corta>`

| Tipo | Ejemplo | Uso |
|------|----------|-----|
| `feat/` | `feat/21-sesiones` | Nueva funcionalidad |
| `fix/` | `fix/15-path-traversal` | Bug fix |
| `docs/` | `docs/roadmap` | Documentación |
| `refactor/` | `refactor/cmd-query` | Refactor sin cambio de behavior |
| `test/` | `test/apparmor-integration` | Tests |
| `ci/` | `ci/auto-add-project` | CI/CD |
| `chore/` | `chore/deps-update` | Mantenimiento |

### Reglas de branches

1. **Branch desde `main`** (o desde el issue con `gh issue develop`)
2. **Una rama = un issue = una PR** (no mezclar)
3. **Rebase sobre `main`** antes de abrir PR (sin merge commits)
4. **Vida corta:** < 7 días ideal
5. **Eliminar la rama** después del merge (cleanup automático)

```bash
# Crear rama desde issue
gh issue develop 21 --checkout

# O manualmente
git checkout main
git pull
git checkout -b feat/21-sesiones
```

---

## Pull Requests

### Template

Toda PR usa el template [PULL_REQUEST_TEMPLATE.md](PULL_REQUEST_TEMPLATE.md) con:

- Checklist de atomicidad (un solo objetivo)
- Conventional Commits en el título
- Referencia al issue (`Closes #N`)
- Validación ejecutada y evidenciada
- Sin secretos en el diff

### Estructura de una PR

```markdown
## Objetivo
Qué resuelve esta PR.

## Cambios
- Lista breve de cambios

## Validación
- Qué se ejecutó y su resultado (tests, build, check)

## Criterios de aceptación
- [ ] Criterio 1 del issue
- [ ] Criterio 2 del issue

Closes #N
```

### Reglas de PR

1. **Atómica:** un objetivo claro, sin mezclar tipos (ADEV §4)
2. **Conventional Commits** en título y commits
3. **`Closes #N`** para auto-cerrar el issue al merge
4. **CI verde** antes de pedir review
5. **Auto-merge habilitado** cuando CI pase y haya approval (ADEV §29)
6. **Draft solo si scope no está claro** (ADEV §30); si el issue está scoped, PR lista para review
7. **Sin force-push** en ramas compartidas

### Tamaño de PR

- **Ideal:** < 300 líneas cambiadas
- **Aceptable:** < 500 líneas
- **Grande:** > 500 líneas — considerar dividir en PRs más pequeñas
- Si la PR es grande, proponer división en el description

---

## Code Review

### Quién revisa

- **`core-devs`:** mantenedores con permiso de merge. Mínimo 1 approval requerido.
- **`contributors`:** pueden revisar pero su approval no satisface el requirement de merge.
- **PRs `pr:risk-critical`:** requieren 2 approvals de `core-devs`.

### Qué revisar

| Aspecto | Checklist |
|---------|-----------|
| **Seguridad** | Sin secretos, sin injection, validación de inputs, paths sanitizados |
| **Atomicidad** | Un solo objetivo, sin scope creep |
| **Tests** | Tests nuevos o actualizados para el comportamiento cambiado |
| **Convenciones** | Conventional Commits, estilo del repo, sin comentarios eliminados |
| **Performance** | Sin regresiones obvias, sin loops innecesarios |
| **Docs** | Docs actualizadas si el cambio es user-facing |
| **i18n** | Sin texto hardcoded si el proyecto es multilingüe |

### Niveles de review

1. **Comment:** feedback sin bloquear merge
2. **Approve:** OK para merge
3. **Request changes:** bloquear merge hasta corregir

### Tiempos esperados

- PR < 100 líneas: review en < 24h
- PR 100-500 líneas: review en < 48h
- PR > 500 líneas: review en < 72h
- PR `pr:risk-critical`: review prioritaria < 12h

---

## Merge y Cleanup

### Estrategia de merge

- **Squash and merge** (default): todos los commits de la rama se squashean en uno solo en `main`
- **Rebase and merge:** si la rama tiene commits atómicos significativos que se quieren preservar
- **Merge commit:** solo para PRs de batch delivery explícito

### Post-merge cleanup (ADEV §32-33)

1. **Verificar el merge** en `main`
2. **Verificar CI** post-merge
3. **Eliminar la rama** local y remota
4. **Confirmar issue cerrado** (vía `Closes #N`)
5. **Actualizar handoff** si el repo usa workspace model

```bash
# Después del merge
git checkout main
git pull
git branch -d feat/21-sesiones        # local
git push origin --delete feat/21-sesiones  # remoto (o auto-delete en GitHub)
```

GitHub está configurado para **auto-eliminar ramas** después del merge.

---

## Project Board

### Proyecto: [Roadmap Yap + ChincoLinux OS](https://github.com/orgs/ChincoLinux/projects/3)

### Campos del proyecto

| Campo | Tipo | Valores |
|-------|------|---------|
| Status | Single Select | Todo, In Progress, In Review, Done, Blocked |
| Mes | Single Select | Mes 1, Mes 2, Mes 3, Mes 4 |
| Etapa | Single Select | Fase 1 — Fundaciones, Fase 2 — Profesor, Fase 3 — Empaquetado, Fase 4 — Pulido |
| Sprint | Number | 1-14 |
| Fecha Inicio | Date | — |
| Fecha Fin | Date | — |
| Complejidad | Single Select | Baja, Media, Alta |
| Esfuerzo (SP) | Number | Story points (3-13) |

### Vistas

| Vista | Layout | Agrupación | Uso |
|-------|--------|------------|-----|
| Tabla Completa | Table | — | Ver todos los items con todos los campos |
| Roadmap por Fases | Table | Etapa | Ver roadmap agrupado por fase |
| Sprint Board | Board | Status | Kanban por estado |
| Por Sprint | Table | Sprint | Ver items agrupados por sprint |

### Automatización nativa del proyecto

GitHub Projects v2 tiene workflows nativos habilitados:

| Workflow | Acción |
|----------|--------|
| Item added to project | Set Status = Todo automáticamente |
| Item closed | Set Status = Done automáticamente |
| Auto-close issue | Cierra issue cuando PR mergeada |
| Auto-add sub-issues | Sub-issues se añaden al proyecto |
| Pull request linked to issue | Actualiza link cuando PR referencia issue |
| Pull request merged | Marca issue como Done |

### Workflow CI: auto-add

El workflow [`.github/workflows/auto-add-to-project.yml`](.github/workflows/auto-add-to-project.yml)
añade automáticamente todo issue o PR nuevo al proyecto.

**Requisito:** crear un PAT y añadirlo como secret `PROJECT_PAT` (ver [Paso a paso](#paso-a-paso-manual)).

---

## Automatización

### Workflows CI/CD

| Workflow | Repo | Trigger | Acción |
|----------|------|---------|--------|
| `auto-add-to-project.yml` | `.github` | issue/PR opened | Añade al proyecto #3 |
| Native project workflows | GitHub | varios | Status, close, link |

### Issue templates

| Template | Ubicación | Uso |
|----------|-----------|-----|
| `bug_report.yml` | `.github/ISSUE_TEMPLATE/` | Bugs |
| `feature_request.yml` | `.github/ISSUE_TEMPLATE/` | Features |
| `config.yml` | `.github/ISSUE_TEMPLATE/` | Links de contacto |

### PR template

| Template | Ubicación | Uso |
|----------|-----------|-----|
| `PULL_REQUEST_TEMPLATE.md` | `.github/` (org-level) | Template default para todos los repos |

### Branch protection

Configurada via **ruleset** en cada repo público de la org:
- Require PR before merge
- Require CI checks
- Require 1+ approval from `core-devs`
- Auto-delete branch after merge

---

## Paso a paso manual

### 1. Crear PAT (Personal Access Token) para el workflow de auto-add

1. Ve a **GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens**
2. Click **Generate new token**
3. Configurar:
   - **Token name:** `ChincoLinux Project Auto-Add`
   - **Expiration:** 90 días (renovar antes de expirar)
   - **Resource owner:** `ChincoLinux` (organización)
   - **Repository access:** All repositories in ChincoLinux
   - **Permissions:**
     - Organization: `Projects: Read and write`
     - Repository: `Issues: Read and write`, `Pull requests: Read and write`, `Metadata: Read`
4. Click **Generate token**
5. **Copia el token** (solo se ve una vez)

### 2. Añadir PAT como secret de organización

1. Ve a **GitHub → ChincoLinux org → Settings → Secrets and variables → Actions**
2. Tab **Organization secrets**
3. Click **New organization secret**
4. Configurar:
   - **Name:** `PROJECT_PAT`
   - **Secret:** pega el token copiado en el paso anterior
   - **Repository access:** All repositories
5. Click **Add secret**

### 3. Configurar agrupaciones de vistas del proyecto

1. Ve a **https://github.com/orgs/ChincoLinux/projects/3**
2. Para cada vista, configurar la agrupación:

**Vista "Roadmap por Fases":**
- Click en la vista
- Click en el botón **Group** (icono de agrupar)
- Seleccionar **Etapa**
- Las columnas se agruparán por: Fase 1, Fase 2, Fase 3, Fase 4

**Vista "Sprint Board":**
- Click en la vista
- Click en **Group**
- Seleccionar **Status**
- Las columnas serán: Todo, In Progress, In Review, Done, Blocked

**Vista "Por Sprint":**
- Click en la vista
- Click en **Group**
- Seleccionar **Sprint**
- Las columnas se agruparán por número de sprint

### 4. Configurar branch protection (si no está)

1. Ve a **GitHub → ChincoLinux org → Settings → Rulesets**
2. Para cada repo (Yap, ChincoLinux):
   - **New ruleset → New branch ruleset**
   - **Target branches:** `main`
   - **Bypass list:** `core-devs` team
   - **Require a pull request:** ✓, required approvals: 1
   - **Require status checks:** ✓ (seleccionar CI checks cuando existan)
   - **Restrict deletions:** ✓
   - **Block force pushes:** ✓
   - **Auto-delete head branches:** ✓

### 5. Configurar auto-merge en PRs

1. Ve a **GitHub → ChincoLinux org → Settings → General → Pull Requests**
2. Habilitar **Allow auto-merge**
3. Default: **Squash and merge**

### 6. Crear teams (si no existen)

1. Ve a **GitHub → ChincoLinux org → Teams**
2. Crear team **`core-devs`**:
   - Add members (mantenedores)
   - Repository access: All repositories, role: **Admin** o **Maintain**
3. Crear team **`contributors`**:
   - Add members (colaboradores activos)
   - Repository access: All repositories, role: **Write**

### 7. Verificar que todo funciona

1. **Crear un issue de prueba** en cualquier repo de la org
2. Verificar que aparece automáticamente en el [Proyecto #3](https://github.com/orgs/ChincoLinux/projects/3)
3. **Crear una PR de prueba** (o usar una existente)
4. Verificar que la PR aparece en el proyecto
5. Verificar que el workflow `auto-add-to-project` se ejecutó (tab Actions en `.github`)

---

## Referencias

| Recurso | URL |
|---------|-----|
| ADEV.md (doctrina canónica) | [ADEV.md](ADEV.md) |
| Governance | [GOVERNANCE.md](GOVERNANCE.md) |
| Contributing | [CONTRIBUTING.md](CONTRIBUTING.md) |
| Code of Conduct | [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) |
| Security | [SECURITY.md](SECURITY.md) |
| Support | [SUPPORT.md](SUPPORT.md) |
| PR Template | [PULL_REQUEST_TEMPLATE.md](PULL_REQUEST_TEMPLATE.md) |
| Issue Templates | [.github/ISSUE_TEMPLATE/](.github/ISSUE_TEMPLATE/) |
| Auto-add Workflow | [.github/workflows/auto-add-to-project.yml](.github/workflows/auto-add-to-project.yml) |
| Project Board | https://github.com/orgs/ChincoLinux/projects/3 |
| Upstream A-Dev | https://github.com/scanalesespinoza/adev |
| Homedir (proving ground) | https://github.com/scanalesespinoza/homedir |

---

## ChincoLinux Overlay

Esta metodología es un overlay específico de ChincoLinux sobre la doctrina A-Dev canónica.
Los principios no-negociables están en [ADEV.md](ADEV.md); este documento los adapta al
contexto educativo de ChincoLinux con:

- **Trunk-Based Development** como modelo de branching
- **Project Board v2** con campos de roadmap (Mes, Etapa, Sprint, Fechas, Complejidad, Esfuerzo)
- **Labels estandarizados** across todos los repos de la org
- **Templates de issue y PR** org-level en `.github`
- **Automatización** de asignación al proyecto
- **Bilingüe:** español primario, inglés mirror

Mantener este documento sincronizado con ADEV.md upstream.
