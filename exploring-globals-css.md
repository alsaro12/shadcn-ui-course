# Exploring globals.css

Fuente: https://vercel.com/academy/shadcn-ui/exploring-globals-css

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/exploring-globals-css.md

Ultima lectura: 2026-07-02

## Resumen

Este capitulo explica como `globals.css` sostiene el sistema de theming de `shadcn/ui`.

La idea central:

```txt
semantic CSS variables
-> Tailwind utilities
-> components adapt to theme
```

En vez de hardcodear colores como `blue-500` o `gray-200`, `shadcn/ui` usa variables con nombres semanticos: `background`, `foreground`, `primary`, `muted`, `border`, `ring`, etc.

Eso permite cambiar el tema sin reescribir cada componente.

## Objetivo del capitulo

Entender la arquitectura de variables CSS que usa `shadcn/ui`.

Al terminar, deberia poder explicar:

- que papel cumple `globals.css`;
- por que los tokens semanticos son mejores que nombres basados en color;
- como se conectan `:root`, `.dark` y Tailwind;
- que hace `@theme inline`;
- por que `OKLCH` es util para colores modernos;
- como modificar el tema sin romper componentes.

## Estructura base

Un `globals.css` moderno de `shadcn/ui` incluye piezas como:

```css
@import "tailwindcss";
@import "tw-animate-css";

@custom-variant dark (&:is(.dark *));

@theme inline {
  --radius-sm: calc(var(--radius) - 4px);
  --radius-md: calc(var(--radius) - 2px);
  --radius-lg: var(--radius);
  --radius-xl: calc(var(--radius) + 4px);
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-primary: var(--primary);
  --color-primary-foreground: var(--primary-foreground);
  --color-border: var(--border);
  --color-ring: var(--ring);
}
```

Puntos importantes:

- `@import "tailwindcss"` carga Tailwind.
- `@import "tw-animate-css"` agrega utilidades de animacion.
- `@custom-variant dark` define como se activa dark mode.
- `@theme inline` mapea variables CSS a tokens que Tailwind puede usar.

## Tokens semanticos

La diferencia clave esta en nombrar por proposito, no por color.

Enfoque fragil:

```css
.button-blue {
  background: #3b82f6;
}
```

Enfoque semantico:

```txt
bg-primary
text-primary-foreground
border-border
bg-card
text-card-foreground
```

`primary` no significa "azul". Significa "accion principal".

`muted` no significa "gris". Significa "informacion de menor enfasis".

Esto permite que el mismo componente funcione con temas distintos.

## `:root` y `.dark`

Los valores para light mode viven en `:root`:

```css
:root {
  --radius: 0.625rem;
  --background: oklch(1 0 0);
  --foreground: oklch(0.145 0 0);
  --primary: oklch(0.205 0 0);
  --primary-foreground: oklch(0.985 0 0);
  --border: oklch(0.922 0 0);
  --ring: oklch(0.708 0 0);
}
```

Los valores para dark mode viven en `.dark`:

```css
.dark {
  --background: oklch(0.145 0 0);
  --foreground: oklch(0.985 0 0);
  --primary: oklch(0.922 0 0);
  --primary-foreground: oklch(0.205 0 0);
  --border: oklch(1 0 0 / 10%);
  --ring: oklch(0.556 0 0);
}
```

Cuando se agrega la clase `.dark` al root del documento, cambian las variables y los componentes se adaptan.

No hace falta reescribir cada `Button`, `Card`, `Dialog` o `Input`.

## Pares background / foreground

Muchos tokens vienen en pares:

| Background | Foreground |
| --- | --- |
| `background` | `foreground` |
| `card` | `card-foreground` |
| `popover` | `popover-foreground` |
| `primary` | `primary-foreground` |
| `secondary` | `secondary-foreground` |
| `accent` | `accent-foreground` |
| `sidebar` | `sidebar-foreground` |

La regla practica:

```txt
si uso bg-primary, el texto deberia usar text-primary-foreground
```

Esto mantiene contraste y consistencia visual.

## Capas de variables

La leccion separa las variables por responsabilidad.

| Capa | Tokens | Para que sirve |
| --- | --- | --- |
| Foundation | `background`, `foreground` | canvas y texto principal |
| Surface | `card`, `popover` | contenedores y superficies |
| Interactive | `primary`, `secondary` | acciones y botones |
| Utility | `muted`, `accent`, `destructive` | enfasis, apoyo y feedback |
| Structural | `border`, `input`, `ring` | bordes, inputs y focus |

Esta separacion ayuda a mantener un design system escalable.

## OKLCH

`shadcn/ui` usa valores `OKLCH`.

Ejemplo:

```css
--primary: oklch(0.205 0 0);
--destructive: oklch(0.577 0.245 27.325);
```

`OKLCH` expresa color con:

- lightness;
- chroma;
- hue.

Beneficios:

- cambios de brillo mas consistentes visualmente;
- mejor base para contraste;
- soporte para pantallas modernas;
- relaciones de color mas faciles de razonar.

## Radius system

El radius se controla desde una variable base:

```css
:root {
  --radius: 0.625rem;
}
```

Y luego se derivan tamanos:

```css
@theme inline {
  --radius-sm: calc(var(--radius) - 4px);
  --radius-md: calc(var(--radius) - 2px);
  --radius-lg: var(--radius);
  --radius-xl: calc(var(--radius) + 4px);
}
```

Si cambias `--radius`, todo el sistema se mueve de forma consistente.

## Base layer

La capa base aplica estilos globales:

```css
@layer base {
  * {
    @apply border-border outline-ring/50;
  }

  body {
    @apply bg-background text-foreground;
  }
}
```

Esto asegura que:

- los bordes usen el token correcto;
- los outlines conecten con `ring`;
- el body use background y foreground del tema.

## Como personalizar sin caos

Buenas practicas:

- mantener nombres semanticos;
- cambiar valores, no el significado del token;
- preservar pares de background y foreground;
- revisar contraste en light y dark mode;
- probar componentes reales despues de cambiar variables;
- evitar tokens como `--blue-500` si el objetivo es theming.

Ejemplo correcto:

```css
--primary: oklch(0.55 0.18 250);
--primary-foreground: oklch(0.98 0 0);
```

Ejemplo menos mantenible:

```css
--blue-500: oklch(0.55 0.18 250);
--white: oklch(0.98 0 0);
```

## Quiz

Pregunta:

```txt
What is the key advantage of shadcn/ui's semantic CSS variable naming approach?
```

Respuesta correcta:

```txt
Separates theme values from usage context, enabling flexible theming.
```

Explicacion:

Los componentes no dependen de un color especifico. Dependen de un proposito visual. Por eso el tema puede cambiar sin modificar el componente.

## Done-When

- Entiendo que `globals.css` define la base del tema de `shadcn/ui`.
- Entiendo que los tokens semanticos describen proposito, no color fijo.
- Entiendo que `:root` define el tema base y `.dark` sobrescribe variables para dark mode.
- Entiendo que `@theme inline` conecta variables CSS con utilidades de Tailwind.
- Entiendo que `bg-primary` deberia combinarse con `text-primary-foreground`.
- Entiendo que cambiar `--primary`, `--background` o `--radius` puede afectar muchos componentes.
- Entiendo que `OKLCH` ayuda a crear relaciones de color mas consistentes.
- Entiendo que debo probar light mode, dark mode, focus states y contraste despues de modificar tokens.

## Nota personal

`globals.css` no es solo un archivo global de estilos. En `shadcn/ui`, funciona como el contrato visual del proyecto. Si los tokens estan bien definidos, los componentes pueden ser flexibles sin llenar el codigo de overrides.

## Siguiente leccion

`what-is-a-component-registry.md`
