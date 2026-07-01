# Overriding Styles with Tailwind

Fuente: https://vercel.com/academy/shadcn-ui/overriding-styles-with-tailwind

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/overriding-styles-with-tailwind.md

Ultima lectura: 2026-07-01

## Resumen

Este capitulo explica como personalizar componentes de `shadcn/ui` usando Tailwind CSS.

La idea central:

```txt
one-off change -> className
system-wide change -> design tokens
project-wide component behavior/style -> edit component source
```

`shadcn/ui` facilita personalizacion porque los componentes:

- aceptan `className`;
- usan `cn()` para mergear clases;
- usan tokens semanticos;
- viven dentro del proyecto;
- pueden editarse directamente.

## Objetivo del capitulo

Entender los niveles de personalizacion disponibles y cuando usar cada uno.

Al terminar, deberia poder explicar:

- como agregar clases puntuales con `className`;
- como `cn()` y `tailwind-merge` resuelven conflictos;
- como modificar tokens globales en `globals.css`;
- cuando cambiar colores de marca via CSS variables;
- cuando editar el source del componente;
- como evitar overrides caoticos.

## Jerarquia de personalizacion

La leccion plantea tres niveles:

```txt
Level 1: Utility class additions
Level 2: Design token modifications
Level 3: Component source editing
```

Cada nivel tiene un alcance distinto.

| Nivel | Alcance | Uso recomendado |
| --- | --- | --- |
| Utility classes | una instancia | ajuste puntual |
| Design tokens | todo el sistema | tema, marca, colores, radius |
| Source editing | componente en el proyecto | cambio estructural o default global |

## Level 1: Utility class additions

El primer nivel es agregar clases Tailwind directamente a una instancia.

Ejemplo:

```tsx
import { Button } from "@/components/ui/button";
import {
  Card,
  CardContent,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";

export default function CustomizedComponents() {
  return (
    <div className="space-y-6 p-8">
      <Button className="w-full py-3">
        Full Width Button
      </Button>

      <Card className="border-2 border-dashed shadow-lg">
        <CardHeader>
          <CardTitle className="text-center text-purple-600">
            Custom Styled Card
          </CardTitle>
        </CardHeader>
        <CardContent className="bg-gradient-to-r from-blue-50 to-purple-50">
          <p>This card has one-off custom styling.</p>
        </CardContent>
      </Card>
    </div>
  );
}
```

Este enfoque sirve cuando:

- el cambio es puntual;
- no quieres cambiar todos los componentes;
- necesitas ajustar layout local;
- necesitas una variante visual unica para una pantalla.

No conviene abusar de este nivel para cambios de marca global.

## `cn()` y merge de clases

Los componentes de `shadcn/ui` suelen usar `cn()`:

```tsx
import { cn } from "@/lib/utils";

function CustomButton({ className, ...props }) {
  return (
    <Button
      className={cn(
        "bg-gradient-to-r from-purple-500 to-pink-500",
        "shadow-lg transition-all duration-200 hover:scale-105",
        className
      )}
      {...props}
    />
  );
}
```

`cn()` normalmente combina:

- `clsx`: para clases condicionales;
- `tailwind-merge`: para resolver conflictos.

Ejemplos:

```tsx
cn("bg-red-500", "bg-blue-500");
// resultado: "bg-blue-500"

cn("px-4 py-2", "p-6");
// resultado: "p-6"
```

Esto reduce problemas de especificidad y evita concatenar strings manualmente.

## Responsive customizations

Se pueden agregar clases responsive:

```tsx
<Card className="w-full md:w-1/2 lg:w-1/3 xl:w-1/4">
  <CardContent className="p-4 md:p-6 lg:p-8">
    <Button className="w-full sm:w-auto sm:px-8">
      Responsive Button
    </Button>
  </CardContent>
</Card>
```

Esto sirve para adaptar una instancia sin modificar el componente base.

## State-based customizations

Tambien se pueden usar clases de estado:

```tsx
<Button
  className="
    hover:scale-105
    active:scale-95
    disabled:cursor-not-allowed
    disabled:opacity-50
  "
>
  State-Aware Button
</Button>
```

En componentes con primitives, tambien aparecen estados como:

```tsx
<Button
  className="
    data-[state=open]:bg-green-500
    data-[state=closed]:bg-gray-500
  "
>
  State-Aware Button
</Button>
```

Esto conecta con lecciones anteriores sobre `data-state`.

## Level 2: Design token modifications

Cuando el cambio debe afectar todo el sistema, conviene modificar tokens.

Los tokens viven en `app/globals.css`.

Ejemplo conceptual:

```css
:root {
  --radius: 0.625rem;
  --background: oklch(1 0 0);
  --foreground: oklch(0.145 0 0);
  --card: oklch(1 0 0);
  --card-foreground: oklch(0.145 0 0);
  --primary: oklch(0.205 0 0);
  --primary-foreground: oklch(0.985 0 0);
  --border: oklch(0.922 0 0);
}
```

Los componentes usan clases semanticas:

```txt
bg-primary
text-primary-foreground
bg-card
text-card-foreground
border-border
```

Si cambias el token, cambian todos los componentes que lo usan.

## Cambiar colores de marca

Ejemplo:

```css
:root {
  --primary: 260 100% 65%;
  --primary-foreground: 0 0% 100%;
  --accent: 260 50% 95%;
  --accent-foreground: 260 100% 40%;
}

.dark {
  --primary: 260 100% 70%;
  --primary-foreground: 260 20% 10%;
  --accent: 260 20% 15%;
  --accent-foreground: 260 50% 80%;
}
```

Este enfoque es mejor que poner `className="bg-purple-500"` por todos lados.

Por que:

- centraliza la marca;
- mantiene consistencia;
- afecta todos los componentes que usan `primary`;
- respeta light/dark mode;
- evita overrides repetidos.

## Cambiar border radius global

Ejemplo:

```css
:root {
  --radius: 1rem;
}
```

O mas cuadrado:

```css
:root {
  --radius: 0.25rem;
}
```

Esto cambia el lenguaje visual general sin tocar cada componente.

## Crear variantes de tema

Se pueden crear clases de tema:

```css
.theme-ocean {
  --primary: 200 100% 50%;
  --primary-foreground: 0 0% 100%;
  --secondary: 200 20% 90%;
  --secondary-foreground: 200 100% 30%;
  --accent: 180 100% 85%;
  --accent-foreground: 180 100% 25%;
}

.theme-forest {
  --primary: 120 60% 40%;
  --primary-foreground: 0 0% 100%;
  --secondary: 120 20% 90%;
  --secondary-foreground: 120 60% 20%;
  --accent: 80 60% 85%;
  --accent-foreground: 80 60% 25%;
}
```

Uso:

```tsx
<div className="theme-ocean">
  <Card>
    <CardHeader>
      <CardTitle>Ocean Theme</CardTitle>
    </CardHeader>
    <CardContent>
      <Button>Ocean Button</Button>
    </CardContent>
  </Card>
</div>
```

Esto permite temas por seccion sin reescribir componentes.

## Level 3: Component source editing

El tercer nivel es editar directamente el componente en `components/ui`.

Ejemplo: modificar defaults del button.

```tsx
const buttonVariants = cva(
  "inline-flex items-center justify-center whitespace-nowrap rounded-md text-sm font-medium transition-all duration-200 hover:scale-105 active:scale-95 focus-visible:outline-none focus-visible:ring-1 focus-visible:ring-ring disabled:pointer-events-none disabled:opacity-50",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground shadow hover:bg-primary/90",
      },
    },
  }
);
```

Esto sirve cuando el cambio debe aplicar a todos los usos del componente.

Ejemplo: cambiar Card por defecto.

```tsx
function Card({ className, ...props }) {
  return (
    <div
      className={cn(
        "rounded-xl border-2 bg-card text-card-foreground shadow-lg transition-shadow duration-300 hover:shadow-xl",
        className
      )}
      {...props}
    />
  );
}
```

## Agregar props nuevas

Como el codigo es propio, puedes extenderlo:

```tsx
interface CustomCardProps extends React.HTMLAttributes<HTMLDivElement> {
  highlight?: boolean;
  size?: "sm" | "md" | "lg";
}

function Card({
  className,
  highlight,
  size = "md",
  ...props
}: CustomCardProps) {
  return (
    <div
      className={cn(
        "rounded-lg border bg-card text-card-foreground shadow-sm",
        highlight && "border-primary shadow-md",
        size === "sm" && "p-4",
        size === "md" && "p-6",
        size === "lg" && "p-8",
        className
      )}
      {...props}
    />
  );
}
```

Esto es poderoso, pero hay que usarlo con criterio.

Si agregas muchas props especificas, puedes convertir un componente simple en una API interna dificil de mantener.

## Criterio de decision

Usar `className` cuando:

- el cambio es puntual;
- afecta una sola pantalla;
- es layout local;
- no representa una decision de marca.

Modificar tokens cuando:

- cambia color de marca;
- cambia radius global;
- cambia light/dark mode;
- varios componentes deben cambiar juntos;
- el cambio pertenece al design system.

Editar source cuando:

- quieres cambiar defaults del componente;
- necesitas una nueva variant;
- necesitas un comportamiento nuevo;
- quieres una API interna para el proyecto;
- el cambio debe aplicar a cada uso futuro del componente.

## Antipatrones

Evitar:

- repetir `bg-purple-500` en muchas pantallas para simular marca;
- usar `!important`;
- meter demasiadas clases puntuales si el cambio es global;
- editar source para un caso unico;
- crear props nuevas para cada excepcion visual;
- cambiar tokens sin revisar dark mode.

## Quiz

Pregunta:

```txt
Cual es la ventaja de modificar CSS custom properties en vez de agregar clases Tailwind para cambios de color de marca?
```

Respuesta:

```txt
Los cambios se aplican sistematicamente a todos los componentes que usan esos tokens.
```

Explicacion:

Si cambias `--primary`, todos los componentes que usan `bg-primary` o `text-primary-foreground` quedan alineados con la marca. Si agregas clases sueltas, terminas duplicando decisiones visuales en muchas partes.

## Reflexion practica

Para decidir como personalizar, preguntaria:

- Es un cambio local o global?
- Afecta una instancia o todo el sistema?
- Es una decision de marca?
- Deberia vivir como token?
- Deberia ser una variant?
- Estoy agregando clases porque es rapido o porque es correcto?
- Este cambio sera facil de mantener en seis meses?

## Done-When

### Entiendo los tres niveles de personalizacion.

Si. Puedo personalizar una instancia con `className`, cambiar el sistema visual con tokens en `globals.css`, o editar el source del componente cuando necesito cambiar defaults, variants o comportamiento.

### Entiendo cuando usar `className`.

Si. Uso `className` para ajustes locales y puntuales, como width, spacing, layout responsive o una apariencia unica en una pantalla.

No lo usaria como reemplazo de tokens globales cuando el cambio representa marca o sistema visual.

### Entiendo el rol de `cn()` y `tailwind-merge`.

Si. `cn()` combina clases base, clases condicionales y clases externas. `tailwind-merge` ayuda a resolver conflictos de Tailwind para que no se acumulen clases contradictorias.

Esto permite que los componentes acepten personalizacion sin romper sus estilos base.

### Entiendo cuando modificar design tokens.

Si. Modifico tokens cuando el cambio debe afectar muchos componentes de forma coherente: colores de marca, foreground/background, radius, dark mode o temas por seccion.

Esto mantiene el sistema visual centralizado.

### Entiendo cuando editar el source del componente.

Si. Edito el source cuando necesito cambiar el default global del componente, agregar una variant, modificar comportamiento o extender su API interna.

Como el componente vive en el proyecto, este tipo de cambio es posible y controlado.

### Entiendo el riesgo de overrides desordenados.

Si. Si uso clases puntuales para todo, el sistema se vuelve inconsistente. Si edito componentes para casos unicos, creo complejidad innecesaria.

La clave es elegir el nivel correcto de personalizacion segun el alcance del cambio.

## Siguiente leccion

`updating-and-maintaining-components.md`
