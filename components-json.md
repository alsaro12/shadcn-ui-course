# Understanding components.json

Fuente: https://vercel.com/academy/shadcn-ui/components-json

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/components-json.md

Ultima lectura: 2026-07-01

## Resumen

Este capitulo explica el rol de `components.json` dentro de un proyecto con `shadcn/ui`.

La idea central:

```txt
components.json = configuracion central del CLI de shadcn/ui
```

Este archivo le dice al CLI:

- donde crear componentes;
- que aliases usar para imports;
- si el proyecto usa TypeScript;
- si el proyecto usa React Server Components;
- donde esta el CSS global;
- que estrategia de Tailwind usar;
- si los design tokens se expresan como CSS variables.

Sin este archivo, el CLI no tiene suficiente contexto para generar componentes de forma coherente con la estructura del proyecto.

## Objetivo del capitulo

Entender como `components.json` coordina la instalacion y generacion de componentes en `shadcn/ui`.

Al terminar, deberia poder leer un `components.json` y explicar:

- que hace cada campo;
- que rutas va a usar el CLI;
- como se conectan Tailwind, CSS variables y aliases;
- que cambia si uso `rsc`, `tsx` o `cssVariables`;
- que riesgos hay si modifico la configuracion despues de haber generado componentes.

## Ejemplo base

Un `components.json` tipico puede verse asi:

```json
{
  "$schema": "https://ui.shadcn.com/schema.json",
  "style": "default",
  "rsc": true,
  "tsx": true,
  "tailwind": {
    "config": "tailwind.config.js",
    "css": "app/globals.css",
    "baseColor": "slate",
    "cssVariables": true,
    "prefix": ""
  },
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils"
  }
}
```

Este archivo no define componentes. Define como el CLI debe agregarlos al proyecto.

## 1. Schema validation

Campo:

```json
{
  "$schema": "https://ui.shadcn.com/schema.json"
}
```

El schema ayuda al editor a entender la estructura valida del archivo.

Sirve para:

- autocompletar campos;
- validar configuracion;
- mostrar errores temprano;
- sugerir valores permitidos;
- documentar el significado de cada opcion.

No cambia visualmente los componentes, pero mejora la experiencia de configuracion y reduce errores.

## 2. Style configuration

Campo:

```json
{
  "style": "default"
}
```

`style` define la variante visual base que usa `shadcn/ui`.

En la practica, normalmente se usa:

```txt
default
```

Este campo existe para indicar el estilo de componentes que el CLI debe generar. Puede afectar decisiones base de apariencia, espaciado, tipografia o convenciones visuales.

## 3. React Server Components

Campo:

```json
{
  "rsc": true
}
```

`rsc` indica si el proyecto usa React Server Components.

En proyectos con Next.js App Router, lo normal es:

```txt
rsc: true
```

Esto importa porque algunos componentes necesitan correr en cliente. Si `rsc` esta activado, el CLI puede generar directivas como:

```tsx
"use client";
```

cuando el componente lo requiere.

Regla practica:

- Next.js App Router: `rsc: true`;
- proyectos sin React Server Components: `rsc: false`.

## 4. TypeScript

Campo:

```json
{
  "tsx": true
}
```

`tsx` define si los componentes se generan como TypeScript o JavaScript.

Si esta en `true`, el CLI genera archivos:

```txt
.tsx
```

con tipos, interfaces y mejor soporte de editor.

Para proyectos modernos de React y Next.js, conviene mantener:

```txt
tsx: true
```

porque mejora autocompletado, validacion y mantenimiento.

## 5. Tailwind configuration

La seccion `tailwind` controla como se conecta `shadcn/ui` con Tailwind CSS.

Ejemplo:

```json
{
  "tailwind": {
    "config": "tailwind.config.js",
    "css": "app/globals.css",
    "baseColor": "slate",
    "cssVariables": true,
    "prefix": ""
  }
}
```

### `config`

Campo:

```json
{
  "config": "tailwind.config.js"
}
```

Indica donde esta el archivo de configuracion de Tailwind.

En versiones mas recientes de Tailwind y `shadcn/ui`, puede no ser tan necesario porque parte de la configuracion vive en `globals.css`.

Lo importante es que el CLI necesita saber donde modificar o leer configuracion relacionada con Tailwind.

### `css`

Campo:

```json
{
  "css": "app/globals.css"
}
```

Indica donde esta el CSS global del proyecto.

El CLI puede agregar ahi:

- CSS variables;
- tokens del sistema visual;
- estilos base necesarios;
- configuracion relacionada con temas.

Si esta ruta esta mal, los componentes pueden generarse, pero no verse correctamente.

### `baseColor`

Campo:

```json
{
  "baseColor": "slate"
}
```

Define la paleta neutral base para componentes.

Opciones comunes:

- `slate`;
- `gray`;
- `zinc`;
- `neutral`;
- `stone`.

Esto afecta colores neutrales como:

- borders;
- muted text;
- backgrounds secundarios;
- estados visuales de baja prioridad.

### `cssVariables`

Campo:

```json
{
  "cssVariables": true
}
```

Define si el design system se expresa con CSS custom properties.

Con `true`, los tokens viven como variables:

```css
:root {
  --background: 0 0% 100%;
  --foreground: 222.2 84% 4.9%;
  --primary: 221.2 83.2% 53.3%;
}
```

Esto permite usar clases semanticas como:

```txt
bg-background
text-foreground
bg-primary
```

Con `false`, los tokens se implementan como valores directos de Tailwind, no como CSS custom properties.

Recomendacion:

```txt
cssVariables: true
```

porque da mas flexibilidad para temas, dark mode y personalizacion.

### `prefix`

Campo:

```json
{
  "prefix": ""
}
```

Sirve si el proyecto usa un prefijo en las clases de Tailwind.

Por ejemplo, si el prefix es:

```txt
tw-
```

el CLI generaria clases como:

```txt
tw-bg-primary
tw-text-foreground
```

Si el proyecto no usa prefijo, se deja vacio.

## 6. Path aliases

La seccion `aliases` define rutas logicas que usa el CLI cuando crea archivos e imports.

Ejemplo:

```json
{
  "aliases": {
    "components": "@/components",
    "utils": "@/lib/utils"
  }
}
```

### `components`

Campo:

```json
{
  "components": "@/components"
}
```

Indica donde se agregan los componentes.

Con esta configuracion:

```bash
npx shadcn@latest add button
```

el CLI crea algo como:

```txt
components/ui/button.tsx
```

### `utils`

Campo:

```json
{
  "utils": "@/lib/utils"
}
```

Indica donde vive la utilidad `cn`.

Los componentes generados pueden importar:

```tsx
import { cn } from "@/lib/utils";
```

Si este alias no coincide con la estructura real del proyecto, los componentes pueden generarse con imports rotos.

## 7. Rol del CLI

El CLI de `shadcn/ui` consume `components.json` para tomar decisiones.

Lo usa para:

1. determinar donde crear archivos;
2. configurar imports;
3. decidir si generar TSX o JSX;
4. aplicar clases y estrategia de estilos;
5. actualizar CSS global o config de Tailwind;
6. respetar aliases del proyecto;
7. agregar `"use client"` cuando corresponda.

En resumen:

```txt
components.json no renderiza UI
components.json guia al CLI para generar codigo compatible con tu proyecto
```

## 8. Inicializar components.json

Se puede crear con:

```bash
npx shadcn@latest init
```

Ese comando normalmente:

- pregunta por la estructura del proyecto;
- genera `components.json`;
- configura rutas;
- actualiza CSS global;
- crea `lib/utils.ts`;
- prepara la utilidad `cn`.

Es mejor correrlo al inicio del proyecto, antes de agregar muchos componentes.

## 9. Problemas comunes

### Rutas incorrectas

Si `tailwind.css`, `tailwind.config` o aliases apuntan a rutas que no existen, el CLI puede generar componentes incompletos o con imports rotos.

### Alias inconsistentes

Si el proyecto usa `@/src/components` pero `components.json` dice `@/components`, los componentes pueden caer en una carpeta equivocada.

### RSC mal configurado

Si usas Next.js App Router y `rsc` esta mal configurado, algunos componentes client-side pueden no recibir `"use client"` cuando lo necesitan.

### Tailwind prefix omitido

Si el proyecto usa prefijo de Tailwind y `components.json` no lo declara, las clases generadas no coincidiran con la configuracion real.

### Cambios despues de generar componentes

Cambiar `components.json` afecta componentes nuevos, pero no necesariamente modifica componentes que ya existen.

Si cambias `baseColor`, `cssVariables` o aliases, puede tocar:

- regenerar componentes;
- actualizar CSS global;
- mover archivos;
- corregir imports.

## Best practices

- Usar `tsx: true` para proyectos TypeScript.
- Usar `cssVariables: true` para flexibilidad de temas.
- Configurar `rsc` segun el tipo de proyecto.
- Mantener aliases simples y consistentes.
- Verificar que `css` apunte al CSS global real.
- Documentar decisiones especiales si el proyecto se sale de la estructura estandar.

## Quiz

Pregunta:

```txt
Que pasa cuando `cssVariables` esta en `false` dentro de components.json?
```

Respuesta:

```txt
Los design tokens se implementan como valores directos de Tailwind en vez de CSS custom properties.
```

Explicacion:

`cssVariables` controla si los tokens del sistema visual se expresan como variables CSS o como valores directos. Con `true`, es mas facil cambiar tema y soportar dark mode. Con `false`, se pierde parte de esa flexibilidad.

## Reflexion practica

Antes de agregar componentes, conviene revisar:

- Mi CSS global esta en la ruta configurada?
- Uso App Router y necesito `rsc: true`?
- Mi proyecto usa TypeScript?
- Mis aliases coinciden con `tsconfig.json`?
- Quiero tokens con CSS variables o valores directos?
- Uso prefix de Tailwind?

## Done-When

### Entiendo que `components.json` configura al CLI, no a los componentes directamente.

Si. `components.json` no es el componente ni renderiza UI. Es el archivo que le da contexto al CLI para saber como generar archivos, imports, estilos y utilidades dentro de la estructura real del proyecto.

### Entiendo para que sirve `$schema`.

Si. `$schema` permite que el editor valide el archivo y ofrezca autocompletado. Ayuda a evitar errores de configuracion antes de ejecutar el CLI.

### Entiendo que hacen `rsc` y `tsx`.

Si. `rsc` indica si el proyecto usa React Server Components, importante especialmente en Next.js App Router. `tsx` indica si los componentes se generan en TypeScript.

En un proyecto moderno con Next.js App Router y TypeScript, normalmente usaria:

```json
{
  "rsc": true,
  "tsx": true
}
```

### Entiendo la seccion `tailwind`.

Si. La seccion `tailwind` le dice al CLI donde esta la configuracion de Tailwind, donde esta el CSS global, que color neutral base usar, si usar CSS variables y si las clases de Tailwind tienen prefix.

Esta seccion es critica porque conecta los componentes con el sistema visual del proyecto.

### Entiendo la diferencia entre `cssVariables: true` y `cssVariables: false`.

Si. Con `cssVariables: true`, los tokens del design system viven como CSS custom properties y se pueden cambiar con mas flexibilidad.

Con `cssVariables: false`, los tokens se generan como valores directos de Tailwind. Eso puede ser mas simple, pero limita temas y personalizacion global.

### Entiendo para que sirven los aliases.

Si. Los aliases le indican al CLI donde colocar componentes y desde donde importar utilidades como `cn`.

Si los aliases no coinciden con la estructura real del proyecto, los componentes pueden generarse en carpetas incorrectas o con imports rotos.

### Entiendo que pasa si cambio `components.json` despues.

Si. Los cambios en `components.json` afectan principalmente componentes nuevos. Los componentes ya generados no se actualizan magicamente.

Si cambio configuraciones grandes como aliases, `baseColor` o `cssVariables`, probablemente tendre que revisar imports, CSS global o regenerar componentes.

## Siguiente leccion

`what-are-radix-primitives.md`
