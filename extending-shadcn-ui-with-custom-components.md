# Extending shadcn/ui with Custom Components

Fuente: https://vercel.com/academy/shadcn-ui/extending-shadcn-ui-with-custom-components

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/extending-shadcn-ui-with-custom-components.md

Ultima lectura: 2026-07-02

## Resumen

Este capitulo explica como crear componentes propios que se sientan nativos dentro del ecosistema `shadcn/ui`.

La idea central:

```txt
existing shadcn/ui patterns
-> custom component
-> same tokens, same DX, same accessibility expectations
```

El ejemplo del curso es un `MetricCard`, un componente para mostrar metricas de dashboard con valor, descripcion, cambio porcentual, icono de tendencia, variantes y estados visuales.

## Objetivo del capitulo

Entender como extender `shadcn/ui` sin romper la consistencia del design system.

Al terminar, deberia poder explicar:

- como crear un componente propio usando componentes existentes;
- por que conviene componer sobre `Card`, `Badge`, `Button` y otros bloques ya disponibles;
- como usar TypeScript para definir una API clara;
- como usar `cva` para variantes;
- como mantener soporte de tema claro y oscuro;
- como disenar componentes accesibles y reutilizables.

## Principios de integracion

Un componente custom debe respetar los mismos pilares que los componentes oficiales:

- consistencia visual con tokens y clases semanticas;
- consistencia de comportamiento con patrones conocidos;
- accesibilidad con HTML semantico, teclado y atributos ARIA cuando apliquen;
- soporte de theming con CSS variables;
- buena experiencia de desarrollo con TypeScript y props claras.

El objetivo no es solo que el componente funcione. Debe parecer parte del mismo sistema.

## Ejemplo: `MetricCard`

El componente representa una tarjeta de metrica:

```tsx
interface MetricCardProps extends React.HTMLAttributes<HTMLDivElement> {
  title: string;
  description?: string;
  value: string | number;
  change?: {
    value: number;
    period: string;
    trend: "up" | "down" | "neutral";
  };
  variant?: "default" | "compact" | "detailed";
  status?: "success" | "warning" | "error" | "info" | "neutral";
}
```

Puntos importantes:

- extiende atributos de `div`, asi acepta props HTML normales;
- usa props especificas para el dominio de metricas;
- permite variantes visuales;
- permite estados semanticos;
- separa `value`, `change`, `trend` y `period` para que la API sea clara.

## Usar componentes existentes

En vez de crear todo desde cero, el componente puede componerse con piezas existentes:

```tsx
import {
  Card,
  CardContent,
  CardDescription,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";
```

Esto mantiene:

- spacing consistente;
- colores consistentes;
- estructura conocida;
- comportamiento esperado;
- menos codigo nuevo que mantener.

## Variantes con `cva`

`class-variance-authority` permite definir variantes controladas:

```tsx
const metricCardVariants = cva("transition-all duration-200", {
  variants: {
    variant: {
      default: "p-6",
      compact: "p-4",
      detailed: "p-6 space-y-4",
    },
    status: {
      success: "border-green-200 bg-green-50/50 dark:border-green-800 dark:bg-green-950/50",
      warning: "border-yellow-200 bg-yellow-50/50 dark:border-yellow-800 dark:bg-yellow-950/50",
      error: "border-red-200 bg-red-50/50 dark:border-red-800 dark:bg-red-950/50",
      info: "border-blue-200 bg-blue-50/50 dark:border-blue-800 dark:bg-blue-950/50",
      neutral: "",
    },
  },
  defaultVariants: {
    variant: "default",
    status: "neutral",
  },
});
```

Esto evita llenar el componente de condicionales sueltas y mantiene una API predecible.

## Helpers internos

El curso muestra helpers para:

- formatear numeros con `Intl.NumberFormat`;
- elegir iconos segun tendencia;
- elegir clases segun `trend`.

Ejemplo conceptual:

```tsx
const formatValue = (val: string | number) => {
  if (typeof val === "number") {
    return new Intl.NumberFormat().format(val);
  }

  return val;
};
```

Los helpers mantienen el JSX principal mas legible.

## Arquitectura recomendada

Para componentes custom complejos:

- definir tipos primero;
- usar composicion sobre componentes existentes;
- usar tokens semanticos;
- soportar `className`;
- usar `cn()` para combinar clases;
- separar helpers;
- exportar el componente y sus tipos;
- probar light mode, dark mode y estados.

## Quiz

Pregunta:

```txt
When creating custom components that extend shadcn/ui, what is the most critical factor for maintaining design system consistency?
```

Respuesta correcta:

```txt
Following the same design token system and CSS custom properties.
```

Explicacion:

La consistencia no depende solo de copiar colores. Depende de usar los mismos tokens, patrones de clases y reglas de theming para que el componente se adapte igual que el resto del sistema.

## Reflection Prompt

Pregunta:

```txt
Consider a complex component your application needs that doesn't exist in shadcn/ui. How would you approach designing its API to be both powerful and easy to use?
```

Respuesta:

```txt
I would start by identifying the essential use cases and the minimum props needed for the common case. Then I would separate required domain props from optional customization props. For styling, I would use the same design tokens, cn(), className support, and cva variants used by shadcn/ui. For complex behavior, I would split helper functions and subcomponents instead of putting everything into one large component. The API should feel familiar: clear prop names, TypeScript support, semantic variants, accessibility by default, and composition where the user needs flexibility.
```

## Done-When

Resuelto: Entiendo que un componente custom debe seguir los patrones de `shadcn/ui`.

No basta con que funcione. Debe usar tokens, `cn()`, `className`, TypeScript y una estructura parecida a los componentes existentes.

Resuelto: Entiendo por que conviene componer sobre componentes existentes.

Si uso `Card`, `CardHeader`, `CardContent` o `Badge`, heredo consistencia visual y reduzco codigo propio.

Resuelto: Entiendo el rol de TypeScript.

Los tipos definen la API del componente, documentan que props acepta y ayudan a evitar usos incorrectos.

Resuelto: Entiendo para que sirve `cva`.

`cva` permite centralizar variantes como `default`, `compact`, `success` o `warning` sin llenar el JSX de condicionales repetidas.

Resuelto: Entiendo como mantener theming.

El componente debe usar tokens semanticos y clases compatibles con light/dark mode para no quedar aislado del sistema.

Resuelto: Entiendo que accessibility sigue siendo parte del contrato.

Un componente custom debe mantener HTML semantico, teclado, labels y atributos ARIA cuando correspondan.

## Nota personal

Crear componentes propios en `shadcn/ui` no significa inventar un sistema aparte. Significa extender el sistema actual con la misma filosofia de ownership, composicion y tokens.

## Siguiente leccion

`creating-a-shadcn-registry-file.md`
