# What are Radix Primitives?

Fuente: https://vercel.com/academy/shadcn-ui/what-are-radix-primitives

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/what-are-radix-primitives.md

Ultima lectura: 2026-07-01

## Resumen

Este capitulo explica que son las primitives de Radix UI y por que son una base tan importante para `shadcn/ui`.

La idea central:

```txt
Radix primitives = comportamiento accesible sin estilos visuales
```

Radix UI resuelve la parte dificil de componentes interactivos:

- accesibilidad;
- atributos ARIA;
- navegacion con teclado;
- manejo de foco;
- estado abierto/cerrado;
- portales;
- eventos de mouse, touch y teclado;
- compatibilidad entre navegadores.

`shadcn/ui` toma esa base y agrega estilos, tokens y convenciones encima.

## Objetivo del capitulo

Entender por que `shadcn/ui` no implementa todo desde cero.

En vez de escribir manualmente toda la logica de un dialog, dropdown, popover o tabs, `shadcn/ui` usa Radix UI primitives como foundation layer.

Al terminar, deberia poder explicar:

- que aporta una primitive;
- que no aporta una primitive;
- por que accesibilidad es dificil de implementar a mano;
- como se compara Radix con librerias tradicionales;
- como `shadcn/ui` combina Radix, Tailwind y ownership.

## 1. Que es una primitive

Una primitive es una pieza base de comportamiento.

No es un componente visual terminado.

Ejemplo conceptual:

```tsx
import * as Dialog from "@radix-ui/react-dialog";

function UnstyledDialog() {
  return (
    <Dialog.Root>
      <Dialog.Trigger>Open Dialog</Dialog.Trigger>
      <Dialog.Portal>
        <Dialog.Overlay />
        <Dialog.Content>
          <Dialog.Title>Dialog Title</Dialog.Title>
          <Dialog.Description>
            This dialog has behavior but no visual styling.
          </Dialog.Description>
          <Dialog.Close>Close</Dialog.Close>
        </Dialog.Content>
      </Dialog.Portal>
    </Dialog.Root>
  );
}
```

Ese dialog ya sabe comportarse como dialog, pero se vera crudo porque no tiene estilos propios.

La primitive aporta:

- estructura funcional;
- estado;
- eventos;
- accesibilidad;
- interacciones complejas.

La primitive no aporta:

- colores;
- tipografia;
- padding;
- sombras;
- bordes;
- estilos de marca.

## 2. Separacion entre comportamiento y apariencia

Radix separa dos problemas que muchas librerias mezclan:

```txt
behavior != appearance
```

Behavior:

- abrir y cerrar;
- mover foco;
- responder a teclado;
- conectar labels y descripciones;
- cerrar al hacer click fuera;
- manejar portales.

Appearance:

- colores;
- espacios;
- layout;
- bordes;
- sombras;
- animaciones;
- dark mode.

Esta separacion es poderosa porque permite usar una base accesible sin quedar atrapado por un estilo visual impuesto.

## 3. Por que existe Radix: el problema de accesibilidad

Construir componentes accesibles desde cero es mas dificil de lo que parece.

Un dialog correcto debe manejar:

### Focus management

- el foco debe quedar dentro del dialog mientras esta abierto;
- al cerrar, el foco debe volver al trigger;
- `Tab` debe recorrer elementos dentro del dialog;
- el primer elemento enfocable debe recibir foco al abrir.

### Keyboard navigation

- `Esc` debe cerrar;
- `Enter` puede activar el trigger;
- `Tab` y `Shift+Tab` deben comportarse correctamente;
- algunos componentes necesitan flechas de teclado.

### Screen reader support

- roles correctos;
- `aria-labelledby`;
- `aria-describedby`;
- relacion clara entre titulo, descripcion y contenido;
- estados comprensibles para tecnologias asistivas.

### Mouse y touch

- click fuera para cerrar cuando corresponde;
- eventos tactiles en mobile;
- evitar cierres accidentales;
- limpiar listeners correctamente.

### Estado y portales

- estado abierto/cerrado;
- renderizado fuera de la jerarquia normal del DOM;
- capas visuales;
- compatibilidad con animaciones.

Radix existe para resolver todo eso de forma robusta y reutilizable.

## 4. El espectro de componentes

Hay varias formas de construir componentes.

### 1. Custom desde cero

Maximo control, maximo trabajo.

```tsx
function CustomDialog({ isOpen, onClose, children }) {
  // foco
  // teclado
  // ARIA
  // eventos
  // estado
  // compatibilidad
}
```

Ventaja:

- control total.

Problema:

- facil equivocarse;
- alto costo de mantenimiento;
- accesibilidad dificil;
- muchos edge cases.

### 2. Libreria visual tradicional

Facil de usar, menos flexible.

```tsx
import { Dialog } from "some-styled-library";

<Dialog variant="primary" size="large">
  Content
</Dialog>;
```

Ventaja:

- rapido;
- viene estilado;
- menos trabajo inicial.

Problema:

- personalizacion limitada;
- dependencia de theme y API;
- estilo visual impuesto.

### 3. Primitives + custom styling

Comportamiento robusto con estilo propio.

```tsx
import * as DialogPrimitive from "@radix-ui/react-dialog";

function StyledDialogContent({ className, ...props }) {
  return (
    <DialogPrimitive.Content
      className={className}
      {...props}
    />
  );
}
```

Ventaja:

- base accesible;
- estilo libre;
- menos trabajo que hacerlo todo desde cero.

Problema:

- hay que construir el sistema visual.

### 4. shadcn/ui

Radix primitives + estilos con Tailwind + ownership.

```tsx
import { Dialog } from "@/components/ui/dialog";

<Dialog>
  {/* accesible, estilado y modificable */}
</Dialog>;
```

Ventaja:

- comportamiento accesible;
- buenos defaults visuales;
- codigo propio dentro del proyecto;
- personalizacion completa.

## 5. Primitives comunes en shadcn/ui

| Primitive | Que aporta | Se usa en |
| --- | --- | --- |
| Dialog | modales, focus trap, overlay, teclado | Dialog, Alert Dialog, Drawer |
| Dropdown Menu | posicionamiento, navegacion, menus anidados | Dropdown Menu, Context Menu |
| Popover | contenido flotante, click outside, foco | Popover, Tooltip, Combobox |
| Accordion | secciones colapsables, teclado, modo single/multiple | Accordion, Collapsible |
| Tabs | tabs, relaciones ARIA, flechas de teclado | Tabs |
| Form primitives | relaciones de campos, validacion, errores | Form, Input, Label |

La leccion clave: `shadcn/ui` no inventa de nuevo estos patrones. Los compone sobre Radix.

## 6. Como trabajan con React

### Composition pattern

Radix usa composicion, no componentes monoliticos.

Ejemplo:

```tsx
<Dialog.Root>
  <Dialog.Trigger />
  <Dialog.Portal>
    <Dialog.Overlay />
    <Dialog.Content>
      <Dialog.Title />
      <Dialog.Description />
      <Dialog.Close />
    </Dialog.Content>
  </Dialog.Portal>
</Dialog.Root>
```

Cada parte tiene una responsabilidad clara.

Esto permite:

- controlar estructura;
- insertar elementos propios;
- cambiar layout;
- mantener comportamiento accesible.

### Controlled y uncontrolled

Las primitives pueden manejar su propio estado o recibir estado externo.

Uncontrolled:

```tsx
<Dialog.Root>
  {/* Radix maneja open/closed internamente */}
</Dialog.Root>
```

Controlled:

```tsx
<Dialog.Root open={isOpen} onOpenChange={setIsOpen}>
  {/* El proyecto controla el estado */}
</Dialog.Root>
```

Esto da flexibilidad para casos simples y casos avanzados.

### `asChild`

Radix soporta el patron `asChild`.

```tsx
<Dialog.Trigger asChild>
  <Button>Open Dialog</Button>
</Dialog.Trigger>
```

Esto permite usar un componente propio como trigger sin perder el comportamiento de la primitive.

## 7. Curva de aprendizaje

Radix puede sentirse mas complejo al inicio porque hay que entender partes como:

- `Root`;
- `Trigger`;
- `Portal`;
- `Overlay`;
- `Content`;
- `Title`;
- `Description`;
- `Close`.

Pero la inversion vale la pena porque esos patrones se repiten en varias primitives.

Cuando entiendes un dialog, se vuelve mas facil entender popovers, dropdowns, tabs o accordions.

## Comparacion rapida

| Aspecto | Libreria tradicional | Radix primitives |
| --- | --- | --- |
| Estilos | predefinidos | sin estilos |
| Personalizacion | limitada por API/theme | amplia |
| Accesibilidad | depende de la libreria | built-in y robusta |
| Bundle | puede incluir estilos innecesarios | comportamiento sin estilos |
| Curva de aprendizaje | API especifica | patrones de primitives |
| Control visual | limitado | completo |

## Conexion con shadcn/ui

`shadcn/ui` combina Radix con:

- Tailwind CSS para estilos;
- CSS variables para tokens;
- TypeScript para tipos;
- copy-paste para ownership;
- registry para distribucion.

El resultado:

```txt
Radix -> comportamiento
shadcn/ui -> estilos y convenciones
tu proyecto -> ownership y personalizacion
```

## Malentendidos comunes

### "Primitives son demasiado complejas"

Tienen curva de aprendizaje, pero siguen patrones consistentes. Aprender una ayuda a entender las demas.

### "Puedo construirlo mas rapido desde cero"

Tal vez para un caso simple. Pero los edge cases de accesibilidad, foco, teclado y browsers suelen aparecer despues.

### "Primitives son excesivas"

Para componentes interactivos de produccion, no son excesivas. Son una forma de evitar errores invisibles para usuarios con teclado o lector de pantalla.

### "Radix me encierra"

Radix encierra menos que una libreria visual cerrada. Como no impone estilos, se puede reemplazar o adaptar con menos friccion.

## Quiz

Pregunta:

```txt
Cual es el beneficio principal de usar Radix UI primitives como base de componentes?
```

Respuesta:

```txt
Proveen comportamiento accesible y robusto sin imponer restricciones visuales.
```

Explicacion:

Radix no existe para dar estilos bonitos por defecto. Su valor principal es resolver interacciones complejas, accesibilidad, teclado, foco y estructura funcional. `shadcn/ui` agrega la capa visual encima.

## Reflexion practica

Cuando piense en un componente interactivo, conviene preguntar:

- Que comportamiento complejo tiene?
- Como se maneja el foco?
- Como se usa solo con teclado?
- Que lee un screen reader?
- Que pasa en mobile?
- El componente necesita estado controlado?
- Lo estoy resolviendo a mano cuando existe una primitive robusta?

## Done-When

### Entiendo que una primitive aporta comportamiento, no apariencia.

Si. Una primitive de Radix UI resuelve interacciones, estado, foco, teclado y accesibilidad, pero no define colores, spacing, tipografia ni identidad visual.

Por eso `shadcn/ui` puede usar Radix como base y agregar estilos propios con Tailwind CSS.

### Entiendo por que construir accesibilidad desde cero es dificil.

Si. Un componente accesible necesita manejar foco, teclado, relaciones ARIA, screen readers, portales, estado, eventos y compatibilidad entre navegadores.

Son detalles faciles de romper si se implementan manualmente. Radix reduce ese riesgo porque ya trae patrones probados.

### Entiendo la diferencia entre una libreria visual y una primitive.

Si. Una libreria visual suele entregar componentes ya estilados y personalizables solo dentro de su API.

Una primitive entrega comportamiento sin estilo. Eso exige construir o adoptar una capa visual, pero da mucha mas libertad para adaptar el componente al producto.

### Entiendo como Radix encaja con shadcn/ui.

Si. Radix aporta la capa funcional y accesible. `shadcn/ui` agrega estilos, tokens, variantes y convenciones. El proyecto recibe el codigo y puede modificarlo.

La combinacion funciona porque separa responsabilidades sin perder ownership.

### Entiendo los patrones React de Radix.

Si. Radix usa composicion con piezas como `Root`, `Trigger`, `Portal`, `Overlay` y `Content`. Tambien soporta estado controlled/uncontrolled y el patron `asChild`.

Eso permite construir componentes flexibles sin perder comportamiento accesible.

### Puedo explicar cuando usar primitives.

Si. Conviene usar primitives cuando el componente tiene interaccion compleja: dialogs, dropdowns, popovers, accordions, tabs, tooltips o componentes con foco y teclado.

En esos casos, una primitive evita repetir logica dificil y permite concentrarse en apariencia, composicion y experiencia del producto.

## Siguiente leccion

`anatomy-of-a-primitive.md`
