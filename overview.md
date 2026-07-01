# React UI with shadcn/ui + Radix + Tailwind

Fuente: https://vercel.com/academy/shadcn-ui

Version Markdown oficial: https://vercel.com/academy/shadcn-ui.md

Ultima lectura: 2026-06-30

## Estado

Curso iniciado.

Leccion actual:

- `what-are-radix-primitives.md`

## Resumen

Este curso de Vercel Academy enseña los fundamentos de desarrollo moderno de interfaces con `shadcn/ui`, `Radix UI` y `Tailwind CSS`.

La idea central es entender por que `shadcn/ui` no funciona como una libreria tradicional de componentes. En vez de instalar una dependencia cerrada, se copian componentes al codebase para que el equipo sea dueno del codigo, pueda modificar estilos y entienda la arquitectura interna.

```txt
Radix primitives -> shadcn/ui components -> Tailwind styling -> componentes propios
```

## Datos del curso

| Campo | Valor |
| --- | --- |
| Titulo | React UI with shadcn/ui + Radix + Tailwind |
| Plataforma | Vercel Academy |
| Lecciones | 17 |
| Tipo | UI components + build-along |
| Stack principal | React, TypeScript, Tailwind CSS, Next.js, Radix UI, shadcn/ui |
| Autor de notas | Alonso Santamaria |
| Instructor del curso | Hayden Bleasel |

## Prerequisitos

- React: componentes, hooks y patrones modernos.
- TypeScript: tipos e interfaces basicas.
- Tailwind CSS: utility classes y responsive design.
- Next.js: conceptos basicos para construir ejemplos.
- Node.js: version LTS actual.

## Objetivo del curso

Aprender a construir interfaces accesibles, mantenibles y listas para produccion usando componentes que viven dentro del propio proyecto.

Al terminar, deberiamos poder:

- explicar la diferencia entre librerias tradicionales y `shadcn/ui`;
- configurar `components.json`;
- instalar y mantener componentes de `shadcn/ui`;
- entender la relacion entre `shadcn/ui` y `Radix UI`;
- sobrescribir estilos con Tailwind sin romper la arquitectura;
- escribir componentes propios compatibles con el sistema;
- crear y publicar registros de componentes;
- usar patrones avanzados como controllable state y compound components.

## Mapa de lecciones

| Orden | Leccion | Archivo sugerido |
| --- | --- | --- |
| 1 | Evolution of Component Libraries | `evolution-of-component-libraries.md` |
| 2 | Why shadcn/ui is Different | `why-shadcn-ui-is-different.md` |
| 3 | Core Concepts | `core-concepts.md` |
| 4 | components.json | `components-json.md` |
| 5 | What are Radix Primitives? | `what-are-radix-primitives.md` |
| 6 | Anatomy of a Primitive | `anatomy-of-a-primitive.md` |
| 7 | Installing shadcn/ui | `installing-shadcn-ui.md` |
| 8 | Adding Your First Component | `adding-your-first-component.md` |
| 9 | Overriding Styles with Tailwind | `overriding-styles-with-tailwind.md` |
| 10 | Updating and Maintaining Components | `updating-and-maintaining-components.md` |
| 11 | Exploring globals.css | `exploring-globals-css.md` |
| 12 | What is a Component Registry? | `what-is-a-component-registry.md` |
| 13 | Extending shadcn/ui with Custom Components | `extending-shadcn-ui-with-custom-components.md` |
| 14 | Creating a shadcn Registry File | `creating-a-shadcn-registry-file.md` |
| 15 | Publishing Your Components | `publishing-your-components.md` |
| 16 | use-controllable-state | `use-controllable-state.md` |
| 17 | Compound Components and Advanced Composition | `compound-components-and-advanced-composition.md` |

## Capitulos

### 1. Intro to Modern UI Libraries

Entender la evolucion de las librerias de componentes, el enfoque de `shadcn/ui`, los conceptos base, `components.json` y la anatomia de primitives de `Radix UI`.

### 2. Getting Started with shadcn/ui

Crear un proyecto Next.js, instalar `shadcn/ui`, agregar los primeros componentes, sobrescribir estilos con Tailwind y aprender a mantener componentes actualizados.

### 3. Writing Your Own Components

Extender `shadcn/ui` con componentes propios, pruebas, registros, publicacion de componentes, `use-controllable-state`, CSS variables, grupos arbitrarios de Tailwind y compound components.

## Proyecto sugerido

Si el curso requiere practica separada, conviene crear un proyecto independiente:

```txt
shadcn-ui-course/
```

No conviene mezclarlo con:

- `team-secrets-vault/`
- `visual-workflow-builder/`
- `teensycode-agent-harness/`

Este curso esta orientado a arquitectura de UI y componentes, no a APIs, workflows o agentes.

## Checklist inicial

- [x] Crear carpeta de notas.
- [x] Crear `overview.md`.
- [x] Registrar fuente oficial del curso.
- [x] Registrar version Markdown oficial.
- [x] Revisar primera leccion `evolution-of-component-libraries`.
- [x] Revisar segunda leccion `why-shadcn-ui-is-different`.
- [x] Revisar tercera leccion `core-concepts`.
- [x] Revisar cuarta leccion `components-json`.
- [x] Documentar decisiones sobre `components.json`.
- [ ] Crear proyecto de practica si el curso lo requiere.
- [ ] Instalar `shadcn/ui`.
- [ ] Agregar primer componente.
- [ ] Documentar patron de override con Tailwind.
- [ ] Crear componente propio.
- [ ] Crear registro de componentes.
- [ ] Guardar evidencia final.

## Evidencias

| Evidencia | Valor |
| --- | --- |
| Carpeta de notas | `shadcn ui/` |
| Proyecto local | Pendiente |
| Repo | Pendiente |
| Deployment | Pendiente |
| Primera leccion | `evolution-of-component-libraries.md` |
| Segunda leccion | `why-shadcn-ui-is-different.md` |
| Tercera leccion | `core-concepts.md` |
| Cuarta leccion | `components-json.md` |
| Estado | Curso iniciado |

## Resumen corto para LinkedIn/CV

```txt
React UI with shadcn/ui + Radix + Tailwind: fundamentos de desarrollo de interfaces modernas con componentes copiables y personalizables, primitives accesibles de Radix, Tailwind CSS, configuracion de components.json, mantenimiento de componentes, registros propios y patrones avanzados de composicion.
```
