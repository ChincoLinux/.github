# Contributing / Contribuir

Gracias por querer contribuir a ChincoLinux. Este documento aplica a todos los repositorios de la organización.

Thank you for contributing to ChincoLinux. This document applies to every repository in the organization.

## Primeros pasos / Getting started

1. Lee el `README.md` del repositorio al que quieres contribuir.
2. Revisa los issues abiertos (busca etiquetas `good first issue` / `help wanted`).
3. Comunica tu intención: abre un issue o comenta en uno existente antes de trabajar.

## Flujo de trabajo / Workflow

- Trabajamos bajo la doctrina **[A-Dev](ADEV.md)**: cada cambio aterriza en `main` **solo mediante Pull Request**.
- Los commits usan **Conventional Commits** (`feat:`, `fix:`, `docs:`, `refactor:`, `chore:`…).
- Una PR debe ser **atómica**: un objetivo claro, sin mezclar refactor + feature + docs.
- Cada PR referencia el issue que resuelve (`Closes #N`).
- Validación: ejecuta el check más acotado que pruebe tu cambio antes de abrir la PR.

## Reglas de calidad / Quality rules

- `main` siempre debe estar verde (CI).
- Nada de secretos, credenciales ni datos personales en código o docs.
- Contenido comunitario bilingüe: **es** primario, **en** mirror cuando aplique.
- Resuelve conflictos con rebase sobre `main`; evita `force push` en ramas compartidas.

## Estructura de equipos / Teams

- **`core-devs`**: mantenedores con permiso de revisión/merge.
- **`contributors`**: colaboradores activos con acceso de escritura.

## ¿Dudas? / Questions

Abre un issue en el repositorio correspondiente o contacta al equipo de `core-devs`.
