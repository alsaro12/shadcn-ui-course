# What is a Component Registry?

Fuente: https://vercel.com/academy/shadcn-ui/what-is-a-component-registry

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/what-is-a-component-registry.md

Ultima lectura: 2026-07-02

## Resumen

Este capitulo explica que es un component registry y por que es clave para el modelo de `shadcn/ui`.

La idea central:

```txt
registry
-> source code + metadata
-> CLI copies files into project
-> team owns the component
```

Un registry no distribuye una caja cerrada como un paquete tradicional. Distribuye codigo fuente y metadata para que el CLI copie archivos al proyecto.

## Objetivo del capitulo

Entender como se descubren, comparten e instalan componentes en el ecosistema `shadcn/ui`.

Al terminar, deberia poder explicar:

- que es un component registry;
- como se diferencia de npm;
- que metadata puede incluir;
- como el CLI usa el registry;
- por que el registry mantiene el modelo copy-paste;
- que beneficios tiene para autores y usuarios de componentes.

## Que es un component registry

Un component registry es un repositorio centralizado de componentes.

Incluye:

- codigo fuente;
- metadata;
- dependencias;
- archivos a copiar;
- documentacion;
- previews o ejemplos.

La forma mental:

```txt
npm instala dependencias
registry copia source code
```

## Diferencia contra npm

Con npm:

```tsx
import { Button } from "some-ui-library";
```

El componente vive fuera del proyecto, dentro de `node_modules`.

Con un registry:

```tsx
import { Button } from "@/components/ui/button";
```

El componente vive en el proyecto, por ejemplo:

```txt
components/ui/button.tsx
```

Eso significa que el equipo puede leerlo, editarlo, versionarlo y adaptarlo.

## Metadata de un registry item

Un registro puede describir un componente con metadata como:

```json
{
  "name": "announcement",
  "description": "A compound badge component designed to display announcements with theming support",
  "dependencies": ["class-variance-authority", "lucide-react"],
  "registryDependencies": ["badge"],
  "files": ["announcement.tsx"],
  "type": "component",
  "category": "ui"
}
```

Campos importantes:

| Campo | Significado |
| --- | --- |
| `name` | nombre del componente |
| `description` | descripcion para documentacion y busqueda |
| `dependencies` | paquetes npm que el componente necesita |
| `registryDependencies` | otros componentes del registry que deben existir |
| `files` | archivos que se copian |
| `type` | tipo de item |
| `category` | agrupacion del componente |

## Como trabaja el CLI

El CLI de `shadcn/ui` puede pedir componentes al registry oficial:

```bash
npx shadcn@latest add button
```

Tambien puede instalar desde un registry custom usando una URL:

```bash
npx shadcn@latest add https://kibo-ui.com/registry/gantt.json
```

El flujo general:

```txt
CLI command
-> fetch registry metadata
-> resolve dependencies
-> copy files
-> update local project
```

## Registry websites

Los sitios de registries suelen ofrecer:

- previews en vivo;
- ejemplos interactivos;
- documentacion;
- snippets;
- busqueda;
- filtros por categoria;
- informacion de dependencias.

Aunque el sitio ayuda a descubrir, lo importante para el proyecto es el archivo de registry que el CLI puede consumir.

## Beneficios para autores

Para quien crea componentes, un registry permite:

- distribuir componentes sin convertirlos en una libreria npm cerrada;
- compartir codigo fuente listo para copiar;
- documentar dependencias y compatibilidad;
- recibir feedback de usuarios;
- mantener changelog y versiones.

## Beneficios para usuarios

Para quien instala componentes, un registry permite:

- descubrir componentes rapidamente;
- ver previews antes de instalarlos;
- copiar el source al proyecto;
- adaptar el componente al producto;
- evitar dependencia permanente de una API externa;
- mantener ownership del codigo.

## Quiz

Pregunta:

```txt
What is the primary difference between a component registry and a traditional npm package repository?
```

Respuesta correcta:

```txt
Registries distribute source code instead of compiled packages.
```

Explicacion:

La diferencia no es el servidor ni la velocidad. La diferencia es el modelo de distribucion. npm instala paquetes; un registry entrega source code para copiar al codebase.

## Done-When

- Entiendo que un component registry es un catalogo de source code y metadata de componentes.
- Entiendo que el CLI de `shadcn/ui` usa el registry para saber que archivos copiar y que dependencias instalar.
- Entiendo que un registry no es lo mismo que npm: npm instala paquetes, el registry distribuye codigo editable.
- Entiendo que los registry items pueden declarar dependencias, archivos, tipo, categoria y otros componentes requeridos.
- Entiendo que los registry websites sirven para descubrir, previsualizar y documentar componentes.
- Entiendo que el registry conserva el modelo copy-paste porque el resultado final vive en mi proyecto.
- Entiendo por que los registries custom permiten extender `shadcn/ui` sin abandonar ownership del codigo.

## Nota personal

El registry es el puente entre compartir componentes y mantener ownership. Permite descubrir y traer codigo rapidamente, pero el resultado no queda como una dependencia opaca: queda como archivo propio dentro del proyecto.

## Siguiente leccion

`extending-shadcn-ui-with-custom-components.md`
