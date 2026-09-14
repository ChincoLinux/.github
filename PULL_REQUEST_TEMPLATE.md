# Pull Request / Solicitud de cambios

> Trabajamos bajo la doctrina **[A-Dev](ADEV.md)**: PRs atómicos, commits convencionales, evidencia obligatoria.
> **Main está protegido:** nadie puede hacer push directo. Todo cambio entra via PR con 1 approval de otro `core-dev`.

## Checklist previo (obligatorio) / Prior checklist (required)

- [ ] La PR resuelve **un solo objetivo** (atomicidad). Si mezcla refactor/feature/docs, dividir.
- [ ] El título usa **Conventional Commits**: `feat:`, `fix:`, `docs:`, `refactor:`, `chore:`, `test:`, `ci:`.
- [ ] Referencia el issue que resuelve: `Closes #N`.
- [ ] Validación ejecutada y evidenciada (test, build o check mínima que pruebe el cambio).
- [ ] Rebase sobre `main` (sin merge commits).
- [ ] Sin secretos, credenciales ni datos personales en el diff.

## Descripción / Description

### Objetivo / Objective
<!-- Qué resuelve esta PR y por qué -->

### Cambios / Changes
<!-- Lista breve de cambios concretos -->

### Validación realizada / Validation
<!-- Qué se ejecutó y su resultado: tests, build, check -->

## Criterios de aceptación / Acceptance criteria
- [ ] Criterio 1 del issue
- [ ] Criterio 2 del issue
<!-- Copiar los criterios del issue original -->

Closes #N

---

## Para el reviewer / For the reviewer

### Antes de aprobar / Before approving
- [ ] **Seguridad:** sin secretos, sin injection, inputs validados
- [ ] **Atomicidad:** un solo objetivo, sin scope creep
- [ ] **Tests:** tests nuevos o actualizados para el comportamiento cambiado
- [ ] **Convenciones:** Conventional Commits, estilo del repo
- [ ] **CI verde:** todos los checks pasan

### Si pides cambios / If requesting changes
Usa **Request changes** y especifica:
1. **Dónde:** archivo + línea (o referencia al bloque)
2. **Qué:** qué hay que cambiar
3. **Por qué:** razón técnica o de convención
4. **Cómo:** sugerencia concreta de solución (opcional pero recomendado)

Ejemplo:
```
src/yap.py:142
- Qué: falta validación de input en cmd_open_app
- Por qué: path traversal posible si el usuario pasa ../../etc/passwd
- Cómo: usar os.path.basename() o whitelist de apps
```

### Estados de review
- **Comment:** feedback sin bloquear merge
- **Approve:** OK para merge (solo `core-devs` puede aprobar)
- **Request changes:** bloquea merge hasta corregir
