# Core Concepts

Fuente: https://vercel.com/academy/shadcn-ui/core-concepts

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/core-concepts.md

Ultima lectura: 2026-06-30

## Resumen

Este capitulo explica los conceptos que hacen funcionar a `shadcn/ui` como sistema de componentes.

La idea central:

```txt
primitives -> variants -> composition -> design system -> utility functions -> flexible components
```

`shadcn/ui` no es solo una coleccion de componentes bonitos. Es una forma de organizar comportamiento, estilos, tokens, variantes y composicion para que el equipo pueda construir UI consistente sin perder control del codigo.

## Objetivo del capitulo

Entender las piezas principales de la arquitectura de `shadcn/ui`:

- primitives de Radix UI para comportamiento accesible;
- variants con `class-variance-authority`;
- composicion de componentes pequenos;
- design system con CSS variables y Tailwind CSS;
- utilidad `cn` para combinar clases;
- patron `asChild` para cambiar el elemento renderizado sin perder estilos.

## 1. Primitives: la capa de comportamiento

Muchos componentes de `shadcn/ui` se apoyan en primitives de Radix UI.

Las primitives resuelven comportamiento complejo sin imponer estilos visuales.

Ejemplo conceptual con `Dialog`:

```tsx
import * as DialogPrimitive from "@radix-ui/react-dialog";

<DialogPrimitive.Root>
  <DialogPrimitive.Trigger>Open</DialogPrimitive.Trigger>
  <DialogPrimitive.Portal>
    <DialogPrimitive.Overlay />
    <DialogPrimitive.Content>
      <DialogPrimitive.Title>Dialog Title</DialogPrimitive.Title>
      <DialogPrimitive.Description>
        Dialog content
      </DialogPrimitive.Description>
      <DialogPrimitive.Close>Close</DialogPrimitive.Close>
    </DialogPrimitive.Content>
  </DialogPrimitive.Portal>
</DialogPrimitive.Root>;
```

La primitive aporta:

- atributos ARIA correctos;
- navegacion con teclado;
- cierre con `Esc`;
- manejo y retencion de foco;
- renderizado en portal;
- eventos como click fuera para cerrar.

La primitive no aporta:

- colores;
- spacing;
- bordes;
- sombras;
- animaciones visuales propias del producto.

Por eso la primitive funciona como el motor del componente. `shadcn/ui` agrega la carroceria visual encima.

## 2. Variants: estilos sistematicos

`shadcn/ui` usa `class-variance-authority` (`cva`) para definir variantes de estilo de forma ordenada y type-safe.

Ejemplo:

```tsx
import { cva, type VariantProps } from "class-variance-authority";

const buttonVariants = cva(
  "inline-flex items-center justify-center rounded-md text-sm font-medium transition-colors",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground",
        destructive: "bg-destructive text-destructive-foreground",
        outline: "border border-input bg-background",
        ghost: "hover:bg-accent hover:text-accent-foreground",
      },
      size: {
        default: "h-9 px-4 py-2",
        sm: "h-8 px-3 text-xs",
        lg: "h-10 px-8",
        icon: "h-9 w-9",
      },
    },
    defaultVariants: {
      variant: "default",
      size: "default",
    },
  }
);
```

Esto permite usar:

```tsx
<Button variant="destructive" size="lg">
  Delete Account
</Button>
```

Beneficios:

- las variantes validas quedan definidas en un solo lugar;
- TypeScript puede detectar valores incorrectos;
- los estilos base no se duplican;
- se pueden combinar variantes como `variant` y `size`;
- agregar una variante nueva es una edicion explicita del componente.

## 3. Composition: construir UI compleja desde piezas pequenas

`shadcn/ui` favorece composicion antes que componentes monoliticos.

En lugar de depender de un componente gigante con muchas props, se combinan piezas:

```tsx
<Card>
  <CardHeader>
    <CardTitle>Recent Orders</CardTitle>
    <CardDescription>You have 3 orders this month</CardDescription>
  </CardHeader>
  <CardContent>
    <Table>
      <TableHeader>
        <TableRow>
          <TableHead>Order</TableHead>
          <TableHead>Status</TableHead>
          <TableHead>Amount</TableHead>
        </TableRow>
      </TableHeader>
      <TableBody>
        <TableRow>
          <TableCell>Order #3210</TableCell>
          <TableCell>
            <Badge variant="outline">Processing</Badge>
          </TableCell>
          <TableCell>$42.25</TableCell>
        </TableRow>
      </TableBody>
    </Table>
  </CardContent>
</Card>
```

Ventajas:

- cada pieza es mas facil de entender;
- se puede reutilizar en otros contextos;
- los cambios locales no fuerzan reescribir toda la interfaz;
- el equipo puede crear patrones propios combinando componentes base.

La composicion es clave porque encaja con el modelo de ownership. Como el codigo vive en el proyecto, el equipo puede ajustar las piezas y sus combinaciones.

## 4. Design system integration

`shadcn/ui` integra el sistema visual con CSS custom properties y Tailwind CSS.

El sistema no se expresa como colores sueltos, sino como tokens semanticos:

```css
:root {
  --background: 0 0% 100%;
  --foreground: 222.2 84% 4.9%;
  --card: 0 0% 100%;
  --card-foreground: 222.2 84% 4.9%;
  --primary: 221.2 83.2% 53.3%;
  --primary-foreground: 210 40% 98%;
  --border: 214.3 31.8% 91.4%;
  --radius: 0.5rem;
}
```

Luego los componentes usan clases semanticas:

```tsx
<div className="bg-background text-foreground border-border">
  <Button className="bg-primary text-primary-foreground">
    Click me
  </Button>
</div>
```

La diferencia entre token semantico y color directo:

```txt
bg-blue-600    -> describe un color
bg-primary     -> describe una intencion del sistema
```

Esto facilita:

- mantener consistencia;
- soportar dark mode;
- cambiar el tema sin reescribir todos los componentes;
- entender que rol cumple cada color;
- conectar diseno y codigo de forma mas clara.

## 5. La utilidad `cn`

La funcion `cn` permite combinar clases de forma inteligente.

En `shadcn/ui`, normalmente combina utilidades como `clsx` y `tailwind-merge`.

Ejemplo:

```tsx
import { cn } from "@/lib/utils";

function Button({ className, variant, size, ...props }) {
  return (
    <button
      className={cn(buttonVariants({ variant, size }), className)}
      {...props}
    />
  );
}
```

Uso:

```tsx
<Button variant="outline" className="w-full border-dashed">
  Custom Button
</Button>
```

La utilidad `cn` importa porque:

- permite pasar clases extra desde fuera;
- evita concatenaciones manuales;
- resuelve conflictos de clases de Tailwind;
- mantiene el componente flexible sin romper sus variantes base.

## 6. El patron `asChild`

El patron `asChild` permite usar los estilos y comportamiento de un componente sobre otro elemento.

Ejemplo:

```tsx
<Button>Click me</Button>

<Button asChild>
  <Link href="/dashboard">Go to Dashboard</Link>
</Button>
```

Sin `asChild`, `Button` renderiza un `button`.

Con `asChild`, el hijo recibe los estilos y props del componente.

Esto es util cuando necesitas que algo se vea como boton, pero semanticamente sea otro elemento, por ejemplo:

- un link de navegacion;
- un trigger de otro componente;
- una composicion con primitives.

El punto importante es no sacrificar semantica HTML por apariencia visual.

## Como encajan todos los conceptos

La arquitectura mental queda asi:

```txt
Radix primitive
  -> comportamiento accesible

CVA variants
  -> estilos sistematicos

CSS variables + Tailwind
  -> design system

cn
  -> combinacion flexible de clases

asChild
  -> composicion sin perder semantica

componentes copiados
  -> ownership del equipo
```

El resultado es un sistema donde los componentes son:

- accesibles por base;
- personalizables;
- consistentes;
- componibles;
- faciles de leer;
- propiedad del proyecto.

## Modelo mental

Cuando lea o modifique un componente de `shadcn/ui`, conviene separarlo en cuatro capas:

| Capa | Pregunta |
| --- | --- |
| Behavior | Que comportamiento viene de Radix o React? |
| Appearance | Que estilos vienen de Tailwind, tokens y variants? |
| Composition | Como se combina con otros componentes? |
| Customization | Que cambios propios del producto se agregaron? |

Esta separacion evita mezclar todo en una sola pregunta. Permite modificar con mas criterio.

## Quiz

Pregunta:

```txt
Cual es el proposito principal de las primitives de Radix UI en los componentes de shadcn/ui?
```

Respuesta:

```txt
Aportar comportamiento accesible y logica compleja sin imponer estilos visuales.
```

Explicacion:

Radix UI no esta ahi para definir como se ve el componente. Esta ahi para resolver interacciones dificiles: foco, teclado, ARIA, estado y comportamiento accesible. `shadcn/ui` agrega estilos y convenciones encima.

## Reflexion practica

Preguntas utiles:

- Estoy separando comportamiento de apariencia?
- Mis variantes estan centralizadas o dispersas en muchos condicionales?
- Mis componentes se componen bien o dependen de props excesivas?
- Uso tokens semanticos o colores directos sin intencion?
- Permito personalizacion externa sin romper la base del componente?
- Estoy respetando la semantica HTML cuando uso componentes visuales?

## Done-When

### Entiendo que las primitives son la capa de comportamiento.

Si. Las primitives de Radix UI resuelven la parte mas compleja del componente: accesibilidad, navegacion por teclado, manejo de foco, estado y eventos.

No definen el estilo visual. Por eso son una buena base para `shadcn/ui`: entregan comportamiento robusto y dejan libertad para construir la apariencia.

### Entiendo para que sirven las variants.

Si. Las variants organizan las opciones visuales de un componente en un solo lugar.

Con `cva`, un `Button` puede tener variantes como `default`, `destructive`, `outline` o `ghost`, y tamanos como `sm`, `lg` o `icon`. Esto mantiene consistencia y ayuda a TypeScript a validar usos incorrectos.

### Entiendo por que la composicion es preferible a componentes monoliticos.

Si. La composicion permite construir interfaces complejas combinando piezas pequenas y reutilizables.

Esto da mas flexibilidad que un componente gigante lleno de props. Tambien hace que cada parte sea mas facil de entender, probar, modificar y reutilizar.

### Entiendo como se integra el design system.

Si. `shadcn/ui` usa CSS variables para definir tokens semanticos y Tailwind CSS para aplicarlos en los componentes.

Eso permite que clases como `bg-primary`, `text-foreground` o `border-border` representen intenciones del sistema visual, no colores aislados. Asi se mantiene consistencia y se facilita cambiar temas.

### Entiendo el rol de la utilidad `cn`.

Si. `cn` combina clases base, variantes y clases externas de forma limpia.

Sirve para que el componente tenga estilos propios, pero permita personalizacion desde fuera sin concatenaciones manuales ni conflictos innecesarios entre clases de Tailwind.

### Entiendo el patron `asChild`.

Si. `asChild` permite aplicar los estilos y comportamiento de un componente a otro elemento hijo.

Esto es util cuando necesito que algo se vea como un `Button`, pero semanticamente sea un `Link` u otro componente. Asi se mantiene flexibilidad sin sacrificar semantica.

### Puedo explicar como todos los conceptos trabajan juntos.

Si. Radix aporta comportamiento accesible, `cva` organiza variantes, Tailwind y CSS variables conectan el design system, `cn` permite personalizacion flexible, `asChild` mejora la composicion y el modelo copy-paste da ownership.

Juntas, estas piezas explican por que `shadcn/ui` puede ser consistente y flexible al mismo tiempo.

## Siguiente leccion

`components-json.md`
