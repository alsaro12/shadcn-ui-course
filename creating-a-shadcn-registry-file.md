# Creating a shadcn Registry File

Fuente: https://vercel.com/academy/shadcn-ui/creating-a-shadcn-registry-file

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/creating-a-shadcn-registry-file.md

Ultima lectura: 2026-07-02

## Resumen

Este capitulo explica como empaquetar un componente custom de `shadcn/ui` como registry item.

La idea central:

```txt
custom component
-> registry item JSON
-> CLI can install it in another project
```

Un registry file describe metadata, dependencias, archivos y configuracion necesaria para que el CLI pueda copiar el componente correctamente.

## Objetivo del capitulo

Entender como crear un archivo JSON de registry para compartir componentes.

Al terminar, deberia poder explicar:

- que es un registry item;
- que campos necesita;
- como declarar dependencias npm;
- como declarar dependencias de otros componentes `shadcn/ui`;
- como incluir archivos;
- por que el registry facilita distribucion entre proyectos.

## Estructura de un registry item

Ejemplo base:

```json
{
  "$schema": "https://ui.shadcn.com/schema/registry-item.json",
  "name": "metric-card",
  "type": "registry:ui",
  "description": "A customizable metric display card with trend indicators and status variants.",
  "dependencies": ["class-variance-authority", "lucide-react"],
  "devDependencies": [],
  "registryDependencies": ["card", "badge"],
  "files": [
    {
      "path": "ui/metric-card.tsx",
      "type": "registry:component",
      "content": "..."
    }
  ],
  "tailwind": {
    "config": {
      "theme": {
        "extend": {}
      }
    }
  },
  "cssVars": {
    "light": {},
    "dark": {}
  }
}
```

## Campos importantes

| Campo | Para que sirve |
| --- | --- |
| `$schema` | valida la forma del archivo |
| `name` | identificador del componente |
| `type` | tipo de registry item |
| `description` | descripcion para documentacion y busqueda |
| `categories` | ayuda a clasificar el componente |
| `dependencies` | paquetes npm requeridos |
| `devDependencies` | paquetes de desarrollo requeridos |
| `registryDependencies` | componentes `shadcn/ui` requeridos |
| `files` | archivos que el CLI debe copiar |
| `tailwind` | configuracion Tailwind adicional |
| `cssVars` | variables CSS adicionales |

## Dependencias

Hay dos tipos principales:

```json
{
  "dependencies": ["class-variance-authority", "lucide-react"],
  "registryDependencies": ["card", "badge"]
}
```

`dependencies` son paquetes npm.

`registryDependencies` son componentes o piezas que tambien deben instalarse desde el registry.

## Archivos

El array `files` indica que se copiara:

```json
{
  "files": [
    {
      "path": "ui/metric-card.tsx",
      "type": "registry:component",
      "content": "..."
    }
  ]
}
```

El `content` contiene el codigo del componente como string.

Esto permite que el registry item sea instalable sin depender de una libreria compilada.

## Configuracion adicional

Si el componente necesita extender Tailwind o agregar variables CSS, se declara en:

```json
{
  "tailwind": {
    "config": {
      "theme": {
        "extend": {}
      }
    }
  },
  "cssVars": {
    "light": {},
    "dark": {}
  }
}
```

Si no necesita nada extra, puede quedar vacio.

## Instalacion desde registry

Cuando el registry item esta publicado, se puede instalar asi:

```bash
npx shadcn@latest add <registry-url>
```

El CLI debe poder:

- leer metadata;
- instalar dependencias;
- instalar registry dependencies;
- copiar archivos;
- ubicar el componente segun `components.json`.

## Quiz

Pregunta:

```txt
What is the primary benefit of creating registry items for custom shadcn/ui components?
```

Respuesta correcta:

```txt
Easy distribution and installation across projects with dependency management.
```

Explicacion:

El registry item convierte un componente custom en algo compartible. Otros proyectos pueden instalarlo con el CLI y traer archivos, dependencias y estructura de forma consistente.

## Reflection Prompt

Pregunta:

```txt
What types of custom components would benefit most from being packaged as registry items?
```

Respuesta:

```txt
I would package components that are reused across several projects, such as dashboard cards, data tables, billing widgets, navigation blocks, empty states, upload components, or internal design system patterns. I would avoid packaging one-off screen-specific components. A good registry item should be reusable, documented, typed, tested, theme-compatible, and stable enough that another project can install it without guessing its dependencies.
```

## Done-When

Resuelto: Entiendo que un registry item es un JSON instalable.

El archivo describe que componente se instala, que dependencias necesita y que archivos se copian.

Resuelto: Entiendo la diferencia entre `dependencies` y `registryDependencies`.

`dependencies` instala paquetes npm. `registryDependencies` instala otros componentes del registry, como `card` o `badge`.

Resuelto: Entiendo el rol de `files`.

`files` contiene la ruta, tipo y contenido de los archivos que el CLI debe crear en el proyecto destino.

Resuelto: Entiendo para que sirven `tailwind` y `cssVars`.

Sirven para declarar configuracion de estilos adicional si el componente necesita extender el tema o agregar variables.

Resuelto: Entiendo por que esto facilita compartir componentes.

El registry item empaqueta codigo, metadata y dependencias en una forma que el CLI puede instalar de manera repetible.

Resuelto: Entiendo que sigue siendo modelo copy-paste.

Aunque el componente venga de un registry, termina viviendo como codigo fuente dentro del proyecto que lo instala.

## Nota personal

Un registry file es una forma ordenada de compartir componentes sin convertirlos en una dependencia cerrada. Es ideal para componentes internos de equipo o bloques reutilizables.

## Siguiente leccion

`publishing-your-components.md`
