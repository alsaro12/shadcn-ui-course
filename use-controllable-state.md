# useControllableState

Fuente: https://vercel.com/academy/shadcn-ui/use-controllable-state

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/use-controllable-state.md

Ultima lectura: 2026-07-02

## Resumen

Este capitulo explica como crear componentes que funcionen en modo controlado y no controlado usando `@radix-ui/react-use-controllable-state`.

La idea central:

```txt
controlled props
or
internal state
-> same component API
```

Un componente profesional debe poder manejar su propio estado cuando el consumidor no lo controla, pero tambien permitir que el padre controle ese estado cuando lo necesita.

## Objetivo del capitulo

Entender el patron de estado controlable.

Al terminar, deberia poder explicar:

- que significa controlled mode;
- que significa uncontrolled mode;
- para que sirve `useControllableState`;
- como nombrar props como `value`, `defaultValue` y `onValueChange`;
- como manejar multiples estados controlables;
- cuando usar este patron en componentes propios.

## Instalacion

```bash
npm i @radix-ui/react-use-controllable-state
```

Este hook encapsula la logica dificil de decidir si el estado viene desde props o desde estado interno.

## Uso basico

```tsx
import { useControllableState } from "@radix-ui/react-use-controllable-state";

function MyComponent({
  value: controlledValue,
  defaultValue,
  onValueChange,
}) {
  const [value, setValue] = useControllableState({
    prop: controlledValue,
    defaultProp: defaultValue,
    onChange: onValueChange,
  });

  return (
    <input
      value={value}
      onChange={(event) => setValue(event.target.value)}
    />
  );
}
```

Si `prop` existe, el componente esta controlado.

Si `prop` no existe, usa `defaultProp` y maneja su estado internamente.

## Controlled vs uncontrolled

Uncontrolled:

```tsx
<Toggle label="Enable notifications" defaultChecked />
```

El componente maneja su estado.

Controlled:

```tsx
function Settings() {
  const [enabled, setEnabled] = React.useState(false);

  return (
    <Toggle
      label="Enable notifications"
      checked={enabled}
      onCheckedChange={setEnabled}
    />
  );
}
```

El padre maneja el estado.

Hybrid:

```tsx
<Combobox
  value={value}
  onValueChange={setValue}
  defaultOpen={false}
/>
```

Un estado puede estar controlado y otro no.

## Convencion de nombres

Para string o valores:

```tsx
interface ComponentProps {
  value?: string;
  defaultValue?: string;
  onValueChange?: (value: string) => void;
}
```

Para booleanos:

```tsx
interface ToggleProps {
  checked?: boolean;
  defaultChecked?: boolean;
  onCheckedChange?: (checked: boolean) => void;
}
```

Para open/close:

```tsx
interface PopoverLikeProps {
  open?: boolean;
  defaultOpen?: boolean;
  onOpenChange?: (open: boolean) => void;
}
```

La convencion hace que la API sea familiar para cualquier desarrollador React.

## Multiples estados controlables

Un componente complejo puede tener varios estados:

```tsx
const [selectedValue, setSelectedValue] = useControllableState({
  prop: value,
  defaultProp: defaultValue,
  onChange: onValueChange,
});

const [isOpen, setIsOpen] = useControllableState({
  prop: open,
  defaultProp: defaultOpen,
  onChange: onOpenChange,
});

const [search, setSearch] = useControllableState({
  prop: searchValue,
  defaultProp: defaultSearchValue,
  onChange: onSearchValueChange,
});
```

Esto sirve para componentes como combobox, dropdowns, tabs, toggles, filters o selectores.

## Quiz

Pregunta:

```txt
What is the primary benefit of using @radix-ui/react-use-controllable-state over implementing controllable state yourself?
```

Respuesta correcta:

```txt
Battle-tested reliability and edge case handling.
```

Explicacion:

El patron controlado/no controlado tiene detalles faciles de implementar mal. Usar el hook de Radix reduce errores y mantiene comportamiento consistente.

## Reflection Prompt

Pregunta:

```txt
Think about a component in your current project that could benefit from controllable state.
```

Respuesta:

```txt
A filter dropdown or custom select would benefit from controllable state. The selected value should support value, defaultValue, and onValueChange. The open state should support open, defaultOpen, and onOpenChange. That allows simple screens to use the component without managing state, while advanced screens can control value and open state from the parent for validation, URL sync, analytics, or multi-step flows.
```

## Done-When

Resuelto: Entiendo controlled mode.

El padre pasa el valor actual y recibe cambios por callback. El componente no decide solo el valor final.

Resuelto: Entiendo uncontrolled mode.

El componente recibe un valor inicial y luego maneja su propio estado internamente.

Resuelto: Entiendo para que sirve `useControllableState`.

El hook permite soportar ambos modos sin duplicar logica ni crear edge cases manualmente.

Resuelto: Entiendo la convencion de props.

Para valores se usa `value`, `defaultValue`, `onValueChange`. Para booleanos se usa `checked`, `defaultChecked`, `onCheckedChange`. Para apertura se usa `open`, `defaultOpen`, `onOpenChange`.

Resuelto: Entiendo que puede haber multiples estados controlables.

Un combobox puede controlar `value`, `open` y `searchValue` por separado.

Resuelto: Entiendo cuando usar este patron.

Lo usaria en componentes reutilizables donde algunas pantallas necesitan control externo y otras solo necesitan comportamiento interno simple.

## Nota personal

Este patron evita crear dos componentes distintos para el mismo problema. Un solo componente puede servir para uso simple y para casos avanzados.

## Siguiente leccion

`compound-components-and-advanced-composition.md`
