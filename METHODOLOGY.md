# ChincoLinux · Metodología de Trabajo

> **Doctrina canónica:** [ADEV.md](ADEV.md) — fuente de verdad operativa para toda la organización.
> **Proving ground:** Homedir — de donde se extrajo el sistema transferible A-Dev.
> **Project Board:** [Proyecto #3 — Roadmap Yap + ChincoLinux OS](https://github.com/orgs/ChincoLinux/projects/3)

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

`main` está protegido por ruleset en todos los repos de la org:
- Require PR before merge
- Require status checks (CI) before merge
- Force-push: blocked
- Delete: blocked
- Required linear history

---

## Issues

### Estructura de un issue

Todo issue debe tener:

1. **Título con prefijo convencional:** `feat:`, `fix:`, `docs:`, `test:`, `ci:`, `refactor:`
2. **Descripción del problema** (qué necesidad o fricción existe)
3. **Propuesta de solución** (cómo se resolvería)
4. **Criterios de aceptación** (checklist de qué debe cumplirse para cerrar)
5. **Referencias** (issues relacionados, dependencias)

### Ciclo de vida de un issue

```
[Open / Todo] → [In Progress] → [Done]
      ↑               │
      └──── [Blocked] ┘
```

- **Todo:** issue creado, esperando asignación
- **In Progress:** alguien está trabajando en una rama + PR (automático al linkear PR)
- **Done:** PR mergeada, issue cerrado automáticamente (`Closes #N`)
- **Blocked:** depende de otro issue o hay un bloqueo externo

### Auto-asignación al proyecto

Todo issue nuevo se añade automáticamente al [Proyecto #3](https://github.com/orgs/ChincoLinux/projects/3)
mediante el workflow `auto-add-to-project.yml`.

### Auto-asignación semanal de responsables

El workflow `weekly-issue-assignment.yml` corre todos los lunes a las 06:00 CLT y:

1. Escanea todos los repos de la org (Yap, ChincoLinux, .github)
2. Busca issues abiertos sin asignar
3. Los asigna round-robin entre los miembros del equipo
4. Genera un reporte en `.github`

El Status **no** se cambia al asignar — solo pasa a **In Progress** cuando se crea una PR
linkeada al issue (workflow nativo de GitHub Projects).

---

## Labels

### Labels de tipo

| Label | Uso |
|-------|-----|
| `bug` | Error o comportamiento incorrecto |
| `enhancement` | Mejora sobre funcionalidad existente |
| `feature-request` | Nueva funcionalidad |
| `documentation` | Cambios en docs |
| `question` | Pregunta o duda |
| `duplicate` | Issue duplicado |
| `invalid` | Issue no válido |
| `wontfix` | No se va a fixear |

### Labels operacionales

| Label | Uso |
|-------|-----|
| `good first issue` | Ideal para nuevos contribuidores |
| `help wanted` | Se busca ayuda |
| `sprint-report` | Reporte semanal de asignación |

---

## Forks y Branches

### Forks

- **Contribuidores externos** (sin acceso a la org): fork → rama → PR desde fork
- **Miembros de la org** (`contributors`, `core-devs`): rama directa en el repo → PR

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
5. **Eliminar la rama** después del merge (auto-delete habilitado)

---

## Pull Requests

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

1. **Atómica:** un objetivo claro, sin mezclar tipos
2. **Conventional Commits** en título y commits
3. **`Closes #N`** para auto-cerrar el issue al merge
4. **CI verde** antes de pedir review
5. **Squash and merge** (estrategia por defecto)
6. **Sin force-push** en ramas compartidas

---

## Code Review

### Quién revisa

- **`core-devs`:** mantenedores con permiso de merge
- **`contributors`:** pueden revisar pero su approval no satisface el requirement de merge

### Qué revisar

| Aspecto | Checklist |
|---------|-----------|
| **Seguridad** | Sin secretos, sin injection, validación de inputs |
| **Atomicidad** | Un solo objetivo, sin scope creep |
| **Tests** | Tests nuevos o actualizados para el comportamiento cambiado |
| **Convenciones** | Conventional Commits, estilo del repo |
| **Docs** | Docs actualizadas si el cambio es user-facing |

---

## Merge y Cleanup

### Estrategia de merge

- **Squash and merge** (default en todos los repos)
- Auto-delete branch after merge: habilitado

### Post-merge cleanup

1. Verificar el merge en `main`
2. Verificar CI post-merge
3. Confirmar issue cerrado (vía `Closes #N`)

---

## Project Board

### Proyecto: [Roadmap Yap + ChincoLinux OS](https://github.com/orgs/ChincoLinux/projects/3)

### Campos del proyecto

El proyecto usa los campos nativos de GitHub Projects v2:

| Campo | Tipo | Descripción |
|-------|------|-------------|
| Title | Text | Título del issue/PR |
| Assignees | People | Responsables |
| Status | Single Select | Todo, In Progress, Done |
| Labels | Labels | Labels del issue/PR |
| Linked pull requests | PRs | PRs linkeadas al issue |
| Repository | Repo | Repo de origen |
| Reviewers | People | Reviewers de la PR |
| Parent issue | Issue | Issue padre (para sub-issues) |
| Sub-issues progress | Text | Progreso de sub-issues |

### Vistas

| Vista | Layout | Uso |
|-------|--------|-----|
| Tabla Completa | Table | Ver todos los items con todos los campos |
| Board | Board | Kanban agrupado por Status (Todo / In Progress / Done) |

### Automatización nativa del proyecto

GitHub Projects v2 tiene workflows nativos habilitados:

| Workflow | Acción |
|----------|--------|
| Item added to project | Status = Todo automáticamente |
| Item closed | Status = Done automáticamente |
| Auto-close issue | Cierra issue cuando PR se mergea |
| Auto-add sub-issues | Sub-issues se añaden al proyecto |
| Pull request linked to issue | Status → In Progress al linkear PR |
| Pull request merged | Marca issue como Done |

---

## Automatización

### Workflows CI/CD

| Workflow | Repos | Trigger | Acción |
|----------|-------|---------|--------|
| `auto-add-to-project.yml` | Yap, ChincoLinux | issue/PR opened | Añade al proyecto #3 |
| `weekly-issue-assignment.yml` | Yap, ChincoLinux, .github | Lunes 06:00 CLT + manual | Asigna issues sin asignar al equipo |
| Native project workflows | GitHub | varios | Status, close, link, sub-issues |

### Configuración de repos

| Config | Valor |
|--------|-------|
| Merge strategy | Squash only |
| Auto-delete branch | Habilitado |
| Branch protection | Ruleset `main-protection` en los 3 repos |
| Required linear history | Sí |
| Force-push to main | Bloqueado |
| Delete main | Bloqueado |

### Teams

| Team | Miembros | Permisos |
|------|----------|----------|
| `core-devs` | VECTORG99, 4mnesia, D4n1sb4ld, martinkuruzg-hue | Maintain en los 3 repos |
| `contributors` | VECTORG99, 4mnesia, D4n1sb4ld, martinkuruzg-hue | Push + Triage en Yap y ChincoLinux, Pull en .github |

### Secret

| Secret | Scope | Repos con acceso |
|--------|-------|------------------|
| `PROJECT_PAT` | Org | .github, ChincoLinux, Yap |

---

## Referencias

| Recurso | URL |
|---------|-----|
| ADEV.md (doctrina canónica) | [ADEV.md](ADEV.md) |
| Project Board | https://github.com/orgs/ChincoLinux/projects/3 |
| Auto-add Workflow | `.github/workflows/auto-add-to-project.yml` |
| Weekly Assignment Workflow | `.github/workflows/weekly-issue-assignment.yml` |
| Upstream A-Dev | https://github.com/scanalesespinoza/adev |
| Homedir (proving ground) | https://github.com/scanalesespinoza/homedir |

---

## ChincoLinux Overlay

Esta metodología es un overlay específico de ChincoLinux sobre la doctrina A-Dev canónica.
Los principios no-negociables están en [ADEV.md](ADEV.md); este documento los adapta al
contexto educativo de ChincoLinux con:

- **Trunk-Based Development** como modelo de branching
- **Project Board v2** con campos nativos de GitHub (Status, Assignees, Labels, etc.)
- **Auto-asignación semanal** de issues al equipo
- **Labels estandarizados** across todos los repos de la org
- **Automatización** de asignación al proyecto y de responsables

Mantener este documento sincronizado con ADEV.md upstream.
