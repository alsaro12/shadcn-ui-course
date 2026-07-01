# Adding Your First Component

Fuente: https://vercel.com/academy/shadcn-ui/adding-your-first-component

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/adding-your-first-component.md

Ultima lectura: 2026-07-01

## Resumen

Este capitulo explica el flujo completo para agregar el primer componente de `shadcn/ui` a un proyecto.

La idea central:

```txt
npx shadcn@latest add card
-> CLI busca el componente en el registry
-> copia el codigo fuente al proyecto
-> queda disponible en components/ui/card.tsx
-> el equipo lo importa y lo usa
```

El punto importante es que el CLI no instala un componente como dependencia cerrada. Copia el codigo fuente del componente dentro del proyecto.

## Objetivo del capitulo

Entender que ocurre cuando se agrega un componente con el CLI.

Al terminar, deberia poder explicar:

- que comando agrega un componente;
- donde se crea el archivo;
- como se importa el componente;
- que significa ownership del codigo;
- como se usan subcomponentes como `CardHeader` y `CardContent`;
- como agregar varios componentes;
- como funcionan variantes como `Button variant` y `Badge variant`;
- cuando usar `add --all`.

## Flujo del CLI

El CLI de `shadcn/ui` conecta el registry con el proyecto local.

Cuando ejecutas:

```bash
npx shadcn@latest add card
```

el CLI:

1. revisa el registry;
2. detecta dependencias necesarias;
3. usa `components.json` para saber donde escribir;
4. crea el archivo del componente;
5. deja el codigo listo para importar.

Salida esperada:

```txt
Checking registry.
Created 1 file:
  - components/ui/card.tsx
```

## Agregar `Card`

Comando:

```bash
npx shadcn@latest add card
```

Archivo creado:

```txt
components/ui/card.tsx
```

Este archivo contiene el componente completo.

Ejemplo simplificado:

```tsx
import * as React from "react";

import { cn } from "@/lib/utils";

function Card({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="card"
      className={cn(
        "bg-card text-card-foreground flex flex-col gap-6 rounded-xl border py-6 shadow-sm",
        className
      )}
      {...props}
    />
  );
}

function CardHeader({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="card-header"
      className={cn("grid auto-rows-min gap-1.5 px-6", className)}
      {...props}
    />
  );
}

function CardTitle({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="card-title"
      className={cn("leading-none font-semibold", className)}
      {...props}
    />
  );
}

function CardContent({ className, ...props }: React.ComponentProps<"div">) {
  return (
    <div
      data-slot="card-content"
      className={cn("px-6", className)}
      {...props}
    />
  );
}

export { Card, CardHeader, CardTitle, CardContent };
```

En la implementacion real tambien aparecen piezas como:

- `CardDescription`;
- `CardAction`;
- `CardFooter`.

## Que significa que el codigo es tuyo

Despues de agregar `Card`, el archivo vive dentro del proyecto.

Eso significa:

- puedes leerlo;
- puedes modificar clases;
- puedes cambiar estructura;
- puedes agregar variants;
- puedes eliminar partes que no usas;
- puedes adaptar el componente al producto.

No dependes de una API cerrada.

No esperas a que una libreria externa permita el cambio.

El componente ya es parte del codebase.

## Usar el componente

Ejemplo en `src/app/page.tsx`:

```tsx
import {
  Card,
  CardContent,
  CardDescription,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";

export default function Home() {
  return (
    <main className="container mx-auto p-8">
      <h1 className="mb-8 text-3xl font-bold">My shadcn/ui Project</h1>

      <div className="grid gap-6 md:grid-cols-2 lg:grid-cols-3">
        <Card>
          <CardHeader>
            <CardTitle>Getting Started</CardTitle>
            <CardDescription>
              Learn the basics of shadcn/ui component development.
            </CardDescription>
          </CardHeader>
          <CardContent>
            <p>
              This card demonstrates the basic structure and styling of
              shadcn/ui components.
            </p>
          </CardContent>
        </Card>
      </div>
    </main>
  );
}
```

El patron es composicional:

```txt
Card
  CardHeader
    CardTitle
    CardDescription
  CardContent
  CardFooter
```

No es un componente gigante con muchas props. Es un conjunto de piezas que se combinan.

## Agregar varios componentes

Se pueden agregar varios componentes en un solo comando:

```bash
npx shadcn@latest add button badge avatar
```

Salida esperada:

```txt
Checking registry.
Installing dependencies.
Created 3 files:
  - components/ui/button.tsx
  - components/ui/badge.tsx
  - components/ui/avatar.tsx
```

Esto permite construir una interfaz mas completa.

Ejemplo:

```tsx
import { Avatar, AvatarFallback, AvatarImage } from "@/components/ui/avatar";
import { Badge } from "@/components/ui/badge";
import { Button } from "@/components/ui/button";
import {
  Card,
  CardContent,
  CardDescription,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";

export default function Home() {
  return (
    <Card>
      <CardHeader>
        <div className="flex items-center justify-between">
          <CardTitle>Getting Started</CardTitle>
          <Badge>New</Badge>
        </div>
        <CardDescription>
          Learn the basics of shadcn/ui component development.
        </CardDescription>
      </CardHeader>

      <CardContent>
        <div className="mb-4 flex items-center space-x-4">
          <Avatar>
            <AvatarImage src="https://github.com/shadcn.png" />
            <AvatarFallback>CN</AvatarFallback>
          </Avatar>
          <div>
            <p className="text-sm font-medium">shadcn</p>
            <p className="text-xs text-muted-foreground">Component Author</p>
          </div>
        </div>

        <Button variant="outline" size="sm">
          Learn More
        </Button>
      </CardContent>
    </Card>
  );
}
```

## Variants y props

Muchos componentes de `shadcn/ui` ya traen variants.

### Button variants

```tsx
<Button variant="default">Default</Button>
<Button variant="destructive">Destructive</Button>
<Button variant="outline">Outline</Button>
<Button variant="secondary">Secondary</Button>
<Button variant="ghost">Ghost</Button>
<Button variant="link">Link</Button>
```

### Button sizes

```tsx
<Button size="default">Default</Button>
<Button size="sm">Small</Button>
<Button size="lg">Large</Button>
<Button size="icon">
  <Star className="h-4 w-4" />
</Button>
```

### Badge variants

```tsx
<Badge>Default</Badge>
<Badge variant="secondary">Secondary</Badge>
<Badge variant="destructive">Destructive</Badge>
<Badge variant="outline">Outline</Badge>
```

Estas variants vienen del patron visto en `core-concepts.md`: estilos sistematicos con `cva`.

## Agregar todos los componentes

Comando:

```bash
npx shadcn@latest add --all
```

Esto agrega todos los componentes disponibles y sus dependencias.

No siempre conviene hacerlo.

Mejor criterio:

```txt
agregar solo los componentes que el proyecto necesita
```

Porque:

- mantiene el codebase mas limpio;
- evita archivos sin uso;
- facilita ownership real;
- reduce ruido en revisiones.

`add --all` puede servir para exploracion, demos o prototipos, pero en produccion conviene ser mas selectivo.

## Que revisar despues de agregar un componente

Checklist practico:

- el archivo existe en `components/ui`;
- los imports resuelven;
- el componente se puede importar desde la app;
- el proyecto compila;
- Tailwind aplica estilos;
- variantes funcionan;
- `className` externo se mezcla correctamente con `cn`;
- no se agregaron componentes innecesarios.

## Quiz

Pregunta:

```txt
Que pasa cuando ejecutas `npx shadcn@latest add button`?
```

Respuesta:

```txt
El CLI copia el codigo fuente del componente Button dentro de tu proyecto.
```

Explicacion:

No instala el button como paquete npm separado ni lo carga desde CDN. Sigue el modelo copy-paste: descarga el codigo fuente, lo coloca en tu proyecto y te da ownership completo.

## Reflexion practica

Preguntas utiles:

- Estoy agregando solo los componentes que necesito?
- Entiendo el archivo que el CLI acaba de crear?
- Se donde modificar estilos si el componente no encaja con el producto?
- Las variants existentes cubren mi caso?
- Necesito crear una variante nueva?
- El componente se compone bien con otros?

## Done-When

### Entiendo el flujo para agregar un componente.

Si. El flujo es correr el comando `npx shadcn@latest add <component>`, dejar que el CLI consulte el registry y revisar el archivo creado en `components/ui`.

El componente queda dentro del proyecto y se importa desde el alias configurado.

### Entiendo que el componente se copia al proyecto.

Si. `shadcn/ui` no instala el componente como caja negra. Copia el codigo fuente del componente al codebase.

Eso significa que el equipo puede leerlo, modificarlo, versionarlo y adaptarlo.

### Entiendo como usar `Card`.

Si. `Card` se usa de forma composicional con piezas como `CardHeader`, `CardTitle`, `CardDescription`, `CardContent` y `CardFooter`.

Esto permite crear layouts flexibles sin depender de un componente monolitico lleno de props.

### Entiendo como agregar varios componentes.

Si. Puedo pasar varios nombres al comando:

```bash
npx shadcn@latest add button badge avatar
```

El CLI crea los archivos necesarios y agrega dependencias si hacen falta.

### Entiendo como usar variants.

Si. Componentes como `Button` y `Badge` tienen variants predefinidas para representar estilos semanticos.

Ejemplo:

```tsx
<Button variant="outline" size="sm">
  Learn More
</Button>
```

### Entiendo cuando evitar `add --all`.

Si. `add --all` agrega todos los componentes, pero puede ensuciar el codebase con archivos sin uso.

Para proyectos reales conviene agregar componentes bajo demanda, segun lo que la interfaz necesite.

### Entiendo que revisar despues de agregar un componente.

Si. Debo revisar que el archivo exista, los imports funcionen, el proyecto compile, Tailwind aplique estilos y el componente se pueda usar con sus variants y `className`.

## Siguiente leccion

`overriding-styles-with-tailwind.md`
