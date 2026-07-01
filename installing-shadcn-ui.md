# Installing shadcn/ui

Fuente: https://vercel.com/academy/shadcn-ui/installing-shadcn-ui

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/installing-shadcn-ui.md

Ultima lectura: 2026-07-01

## Resumen

Este capitulo explica como instalar e inicializar `shadcn/ui` dentro de un proyecto Next.js existente.

La idea central:

```txt
proyecto Next.js + Tailwind + TypeScript
-> npx shadcn@latest init
-> components.json + globals.css + lib/utils.ts
-> listo para agregar componentes
```

La instalacion de `shadcn/ui` no consiste en importar una libreria cerrada de componentes. El CLI prepara el proyecto para poder copiar componentes al codebase con una configuracion consistente.

## Objetivo del capitulo

Entender que hace el comando de inicializacion y que archivos deja listos.

Al terminar, deberia poder explicar:

- que prerequisitos necesita el proyecto;
- que comando inicializa `shadcn/ui`;
- que preguntas hace el CLI;
- que archivos crea o modifica;
- para que sirve `components.json`;
- para que sirve `app/globals.css`;
- para que sirve `lib/utils.ts`;
- como saber si el proyecto quedo listo para agregar componentes.

## Prerequisitos

Antes de instalar `shadcn/ui`, el proyecto deberia tener:

- Next.js funcionando;
- TypeScript;
- Tailwind CSS configurado;
- Node.js 18 o superior;
- un package manager como `pnpm`, `npm`, `yarn` o `bun`.

Si no existe todavia un proyecto Next.js con Tailwind, se puede crear uno con:

```bash
npx create-next-app@latest --typescript --tailwind
```

Para este curso, la idea es trabajar sobre un proyecto donde ya exista la base de Next.js + Tailwind.

## Inicializar shadcn/ui

El comando principal es:

```bash
npx shadcn@latest init
```

Este comando se corre en la raiz del proyecto.

El CLI hace preflight checks:

- verifica framework;
- valida Tailwind CSS;
- valida aliases de importacion;
- pregunta color base;
- escribe `components.json`;
- actualiza variables CSS;
- instala dependencias necesarias;
- crea `lib/utils.ts`.

Ejemplo de salida:

```txt
Preflight checks.
Verifying framework. Found Next.js.
Validating Tailwind CSS config. Found v4.
Validating import alias.
Which color would you like to use as the base color? Neutral
Writing components.json.
Updating CSS variables in app/globals.css
Installing dependencies.
Created 1 file:
  - lib/utils.ts

Success! Project initialization completed.
You may now add components.
```

## Elegir base color

Durante la instalacion, el CLI pregunta por el color base.

En la leccion usan:

```txt
Neutral
```

Este valor impacta la paleta neutral del sistema visual:

- bordes;
- fondos secundarios;
- texto muted;
- estados de baja prioridad.

No es una decision irreversible, pero cambiarla despues puede requerir revisar variables CSS y componentes generados.

## Archivos creados o modificados

### 1. `components.json`

El CLI crea un archivo como:

```json
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "new-york",
  "rsc": true,
  "tsx": true,
  "tailwind": {
    "config": "",
    "css": "app/globals.css",
    "baseColor": "neutral",
    "cssVariables": true,
    "prefix": ""
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils",
    "ui": "@/components/ui",
    "lib": "@/lib",
    "hooks": "@/hooks"
  },
  "iconLibrary": "lucide"
}
```

Este archivo configura el CLI.

Define:

- estilo base;
- soporte RSC;
- uso de TypeScript;
- ruta del CSS global;
- color base;
- uso de CSS variables;
- aliases;
- libreria de iconos.

Punto importante:

```txt
components.json le dice al CLI como trabajar dentro de tu proyecto
```

### 2. `app/globals.css`

El CLI crea o modifica:

```txt
app/globals.css
```

Este archivo contiene la base visual global.

Normalmente incluye:

- variables para light mode;
- variables para dark mode;
- tokens semanticos;
- estilos base del `body`;
- conexion entre variables y theme.

Este archivo es importante porque muchos componentes de `shadcn/ui` usan clases semanticas como:

```txt
bg-background
text-foreground
border-border
bg-primary
```

Esas clases dependen de tokens definidos en el CSS global.

### 3. `lib/utils.ts`

El CLI crea:

```txt
lib/utils.ts
```

Con una funcion `cn`:

```tsx
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

`cn` sirve para combinar clases de forma limpia.

Hace dos cosas:

- `clsx`: combina clases condicionales;
- `tailwind-merge`: resuelve conflictos de clases de Tailwind.

Ejemplo conceptual:

```tsx
cn("px-2", isActive && "bg-primary", "px-4");
```

Si hay conflictos, `tailwind-merge` ayuda a que gane la clase correcta.

## Que significa "Project initialization completed"

Cuando el CLI termina con exito, significa que el proyecto esta listo para agregar componentes.

No significa que ya tengas todos los componentes instalados.

Significa que ya existe la infraestructura para agregarlos:

```txt
components.json
app/globals.css
lib/utils.ts
dependencias base
aliases configurados
```

El siguiente paso sera:

```bash
npx shadcn@latest add button
```

o cualquier otro componente.

## Checklist de verificacion despues de init

Despues de correr `npx shadcn@latest init`, revisaria:

- existe `components.json`;
- `components.json` tiene aliases correctos;
- `app/globals.css` fue actualizado;
- existe `lib/utils.ts`;
- `cn` importa `clsx` y `tailwind-merge`;
- el proyecto compila;
- Tailwind sigue funcionando;
- los aliases tipo `@/components` y `@/lib/utils` resuelven bien.

## Problemas comunes

### Tailwind no detectado

Si el CLI no encuentra Tailwind, revisar:

- instalacion de Tailwind;
- ruta del CSS global;
- estructura del proyecto;
- version de Tailwind.

### Alias invalido

Si el alias `@/` no esta configurado, los imports pueden fallar.

Revisar:

```txt
tsconfig.json
```

o

```txt
jsconfig.json
```

### Ruta de CSS incorrecta

Si el proyecto no usa `app/globals.css`, hay que configurar la ruta real.

Ejemplos posibles:

```txt
src/app/globals.css
styles/globals.css
```

### Proyecto sin App Router

Si no se usa Next.js App Router, revisar si `rsc` deberia ser `false`.

## Relacion con lecciones anteriores

Esta leccion conecta con:

- `components-json.md`: porque el init crea ese archivo.
- `core-concepts.md`: porque `cn`, tokens y aliases empiezan a usarse desde aqui.
- `what-are-radix-primitives.md`: porque los componentes que se agregaran luego suelen envolver primitives.
- `anatomy-of-a-primitive.md`: porque al instalar componentes veremos estructuras como `Root`, `Trigger`, `Content`, etc.

## Done-When

### Entiendo los prerequisitos para instalar `shadcn/ui`.

Si. Necesito un proyecto React/Next.js preparado, idealmente con TypeScript y Tailwind CSS. Tambien necesito Node.js 18+ y un package manager.

La instalacion funciona mejor cuando Tailwind y aliases ya estan correctamente configurados.

### Entiendo que hace `npx shadcn@latest init`.

Si. Ese comando inicializa `shadcn/ui` en el proyecto. No instala una libreria cerrada de componentes, sino que configura el proyecto para poder agregar componentes copiables.

El CLI valida el setup, pregunta opciones, crea `components.json`, actualiza CSS global, instala dependencias y crea `lib/utils.ts`.

### Entiendo para que sirve `components.json`.

Si. `components.json` configura el comportamiento del CLI: rutas, aliases, estilo, TypeScript, RSC, Tailwind, CSS variables e icon library.

Es el archivo que le dice a `shadcn/ui` como debe generar componentes dentro del proyecto.

### Entiendo para que sirve `app/globals.css`.

Si. `app/globals.css` contiene tokens, variables y estilos base que los componentes usan para verse correctamente.

Si las variables no estan bien definidas, clases como `bg-background`, `text-foreground` o `border-border` no representaran el sistema visual esperado.

### Entiendo para que sirve `lib/utils.ts`.

Si. `lib/utils.ts` contiene la funcion `cn`, que combina clases y resuelve conflictos de Tailwind.

Es una utilidad central porque muchos componentes de `shadcn/ui` permiten recibir `className` externo y mezclarlo con sus variantes base.

### Entiendo cuando el proyecto queda listo.

Si. El proyecto queda listo cuando el init termina sin errores, existen los archivos esperados, los aliases funcionan y el proyecto compila.

En ese punto ya se pueden agregar componentes con comandos como:

```bash
npx shadcn@latest add button
```

## Siguiente leccion

`adding-your-first-component.md`
