# Anatomy of a Primitive

Fuente: https://vercel.com/academy/shadcn-ui/anatomy-of-a-primitive

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/anatomy-of-a-primitive.md

Ultima lectura: 2026-07-01

## Resumen

Este capitulo explica la anatomia interna de una primitive de Radix UI usando `Dialog` como ejemplo.

La idea central:

```txt
primitive = piezas coordinadas por contexto + estado + accesibilidad
```

Un `Dialog` no es un solo bloque. Esta compuesto por partes:

```txt
Root -> Trigger -> Portal -> Overlay -> Content -> Title / Description / Close
```

Cada parte tiene una responsabilidad concreta. Juntas crean un componente accesible, coordinado y flexible.

## Objetivo del capitulo

Entender como las partes de una primitive trabajan juntas.

Al terminar, deberia poder explicar:

- que hace cada pieza de `Dialog`;
- como se coordina el estado abierto/cerrado;
- por que existe `Portal`;
- como `Title` y `Description` mejoran accesibilidad;
- como se usan `data-state` para estilos;
- que diferencia hay entre uncontrolled y controlled;
- como `asChild` permite usar componentes propios.

## Estructura completa de Dialog

Ejemplo sin estilos:

```tsx
import * as Dialog from "@radix-ui/react-dialog";

function DialogExample() {
  return (
    <Dialog.Root>
      <Dialog.Trigger>Open Dialog</Dialog.Trigger>

      <Dialog.Portal>
        <Dialog.Overlay />
        <Dialog.Content>
          <Dialog.Title>Dialog Title</Dialog.Title>
          <Dialog.Description>
            This is the dialog description.
          </Dialog.Description>

          <div>
            <p>Dialog body content...</p>
          </div>

          <Dialog.Close>Close</Dialog.Close>
        </Dialog.Content>
      </Dialog.Portal>
    </Dialog.Root>
  );
}
```

Lectura rapida:

```txt
Root = controla todo
Trigger = abre
Portal = saca el contenido al body
Overlay = fondo
Content = caja del modal
Title = titulo accesible
Description = descripcion accesible
Close = cierra
```

## 1. Root

```tsx
<Dialog.Root>
```

`Root` es el contenedor de estado y contexto.

No existe para verse en pantalla. Existe para coordinar el sistema completo.

Responsabilidades:

- manejar estado abierto/cerrado;
- proveer contexto a las piezas hijas;
- coordinar `Trigger`, `Content`, `Overlay` y `Close`;
- manejar transiciones de estado;
- permitir modo controlled o uncontrolled.

Props importantes:

```tsx
<Dialog.Root
  open={open}
  defaultOpen={false}
  onOpenChange={setOpen}
  modal={true}
>
```

Resumen:

```txt
Root = cerebro del componente
```

## 2. Trigger

```tsx
<Dialog.Trigger>Open Dialog</Dialog.Trigger>
```

`Trigger` es el elemento que abre el dialog.

Por defecto renderiza un `button`.

Responsabilidades:

- abrir el dialog al activarse;
- recibir atributos ARIA necesarios;
- exponer estado como `data-state`;
- ayudar a devolver el foco cuando el dialog se cierra.

HTML conceptual:

```html
<button
  type="button"
  aria-haspopup="dialog"
  aria-expanded="false"
  data-state="closed"
>
  Open Dialog
</button>
```

Tambien puede usar `asChild`:

```tsx
<Dialog.Trigger asChild>
  <Button>Abrir modal</Button>
</Dialog.Trigger>
```

Resumen:

```txt
Trigger = boton o elemento que abre
```

## 3. Portal

```tsx
<Dialog.Portal>
```

`Portal` renderiza el contenido del dialog fuera de la jerarquia normal del DOM, normalmente al final de `body`.

Sirve para evitar problemas de:

- `z-index`;
- `overflow: hidden`;
- contenedores padre que recortan contenido;
- layouts donde el modal quedaria atrapado.

Sin `Portal`, un modal dentro de una card podria quedar recortado por esa card.

Con `Portal`, el modal se declara donde conviene en React, pero se monta donde conviene en el DOM.

Resumen:

```txt
Portal = manda Overlay y Content a una capa global
```

## 4. Overlay

```tsx
<Dialog.Overlay />
```

`Overlay` es el fondo que aparece detras del modal.

Responsabilidades:

- separar visualmente el modal de la pagina;
- permitir fondo oscuro, blur o transparencia;
- participar en el comportamiento de click outside;
- exponer `data-state` para animaciones.

Ejemplo conceptual:

```html
<div data-state="open"></div>
```

En `shadcn/ui`, aqui suelen ir clases como:

```txt
fixed inset-0 z-50 bg-black/80
```

Resumen:

```txt
Overlay = fondo detras del modal
```

## 5. Content

```tsx
<Dialog.Content>
```

`Content` es la caja principal del dialog.

Responsabilidades:

- contener el contenido visible;
- manejar foco;
- atrapar foco dentro del dialog;
- manejar `Esc`;
- conectar ARIA con `Title` y `Description`;
- coordinar animaciones;
- controlar interacciones fuera del dialog.

HTML conceptual:

```html
<div
  role="dialog"
  aria-labelledby="radix-2"
  aria-describedby="radix-1"
  data-state="open"
  tabindex="-1"
>
</div>
```

Props utiles:

```tsx
<Dialog.Content
  onOpenAutoFocus={handleOpenFocus}
  onCloseAutoFocus={handleCloseFocus}
  onEscapeKeyDown={handleEscape}
  onPointerDownOutside={handleOutsideClick}
>
```

Resumen:

```txt
Content = caja visible + foco + accesibilidad
```

## 6. Title y Description

```tsx
<Dialog.Title>Dialog Title</Dialog.Title>
<Dialog.Description>Dialog description</Dialog.Description>
```

`Title` y `Description` no son solo texto visual. Tambien crean relaciones accesibles.

`Title`:

- define el titulo principal del dialog;
- normalmente renderiza un heading;
- se conecta con `aria-labelledby`.

`Description`:

- da contexto adicional;
- se conecta con `aria-describedby`.

HTML conceptual:

```html
<h2 id="radix-2">Dialog Title</h2>
<p id="radix-1">Dialog description</p>

<div
  role="dialog"
  aria-labelledby="radix-2"
  aria-describedby="radix-1"
>
</div>
```

Esto ayuda a lectores de pantalla a anunciar:

```txt
Esto es un dialog, este es su titulo, esta es su descripcion.
```

Resumen:

```txt
Title = nombre del dialog
Description = contexto del dialog
```

## 7. Close

```tsx
<Dialog.Close>Close</Dialog.Close>
```

`Close` es el elemento que cierra el dialog.

Por defecto renderiza un `button`.

Responsabilidades:

- cerrar el dialog al activarse;
- coordinar el cambio de estado con `Root`;
- permitir que el foco vuelva al trigger.

HTML conceptual:

```html
<button type="button">Close</button>
```

Resumen:

```txt
Close = boton o elemento que cierra
```

## 8. data-state

Radix usa atributos como `data-state` para comunicar estado al CSS.

Ejemplo:

```tsx
<Dialog.Trigger data-state="closed" />
<Dialog.Overlay data-state="open" />
<Dialog.Content data-state="open" />
```

Esto permite estilos y animaciones basadas en estado:

```css
[data-state="open"] {
  animation: fadeIn 200ms ease-out;
}

[data-state="closed"] {
  animation: fadeOut 200ms ease-out;
}
```

En `shadcn/ui`, esto aparece mucho en clases de Tailwind:

```txt
data-[state=open]:animate-in
data-[state=closed]:animate-out
```

Resumen:

```txt
data-state = estado visible para CSS
```

## 9. Flujo de eventos

### Abrir el dialog

1. El usuario activa `Trigger`.
2. `Root` cambia estado a `open`.
3. Las piezas reciben el nuevo estado por contexto.
4. `Portal` monta `Overlay` y `Content`.
5. `Content` recibe foco.
6. El foco queda atrapado dentro del dialog.

### Cerrar el dialog

1. El usuario activa `Close`, presiona `Esc` o hace click fuera.
2. `Root` cambia estado a `closed`.
3. Las piezas reciben el nuevo estado.
4. El foco vuelve al `Trigger`.
5. `Portal` desmonta `Overlay` y `Content`.

Resumen:

```txt
Trigger abre -> Root coordina -> Content enfoca -> Close/Esc/outside cierra
```

## 10. Controlled vs uncontrolled

### Uncontrolled

La primitive maneja el estado internamente.

```tsx
<Dialog.Root defaultOpen={false}>
  {/* Radix maneja open/closed */}
</Dialog.Root>
```

Uso:

- casos simples;
- no necesitas sincronizar el estado con nada externo.

### Controlled

El proyecto maneja el estado.

```tsx
const [open, setOpen] = useState(false);

<Dialog.Root open={open} onOpenChange={setOpen}>
  {/* El proyecto controla el estado */}
</Dialog.Root>
```

Uso:

- necesitas cerrar despues de guardar;
- necesitas analytics;
- necesitas bloquear cierre;
- necesitas sincronizar con URL, forms o estado externo.

### Semi-controlled

Radix maneja el estado, pero el proyecto escucha cambios.

```tsx
<Dialog.Root
  onOpenChange={(open) => {
    console.log("Dialog state changed:", open);
  }}
>
</Dialog.Root>
```

## 11. Customization patterns

### `asChild`

Permite usar un componente propio sin perder comportamiento Radix.

```tsx
<Dialog.Trigger asChild>
  <Button variant="outline">Open</Button>
</Dialog.Trigger>
```

Sin `asChild`, Radix renderiza su propio elemento.

Con `asChild`, Radix le pasa comportamiento al hijo.

### Eventos personalizados

Puedes intervenir eventos importantes.

```tsx
<Dialog.Content
  onEscapeKeyDown={(event) => {
    if (hasUnsavedChanges) {
      event.preventDefault();
      showConfirmDialog();
    }
  }}
  onPointerDownOutside={(event) => {
    if (isFormDirty) {
      event.preventDefault();
    }
  }}
>
```

Esto permite adaptar comportamiento sin reimplementar toda la primitive.

## Patrones comunes en primitives

Cuando entiendes `Dialog`, reconoces patrones que se repiten:

- `Root`: contexto y estado.
- `Trigger`: abre o cambia estado.
- `Content`: area principal interactiva.
- `Portal`: capa global para elementos flotantes.
- `data-state`: estado disponible para estilos.
- `asChild`: composicion con elementos propios.
- controlled/uncontrolled: estado interno o externo.

Estos patrones aparecen en `Popover`, `Dropdown`, `Tabs`, `Accordion` y otros componentes.

## Que significa esto para shadcn/ui

Los componentes de `shadcn/ui` son primitives estilizadas y copiadas al proyecto.

Entender la anatomia ayuda a:

- modificar componentes con criterio;
- saber donde cambiar estilos;
- saber donde cambiar comportamiento;
- debuggear problemas de foco o estado;
- extender componentes propios;
- entender por que el codigo esta dividido en varias piezas.

## Quiz

Pregunta:

```txt
Cual es el proposito principal de Dialog.Portal?
```

Respuesta:

```txt
Renderizar el contenido del dialog fuera de la jerarquia normal del DOM.
```

Explicacion:

`Portal` evita problemas de `z-index`, `overflow` y contenedores padre. Permite que `Overlay` y `Content` se monten en una capa global, normalmente al final de `body`.

## Reflexion practica

Preguntas utiles:

- Que pieza controla el estado?
- Que pieza dispara la apertura?
- Que pieza contiene la UI visible?
- Que pieza conecta accesibilidad?
- Que pieza mueve contenido fuera del DOM normal?
- Que pasa con el foco al abrir y cerrar?
- Necesito modo controlled o uncontrolled?

## Done-When

### Entiendo la responsabilidad de `Root`.

Si. `Root` coordina el estado y provee contexto a las demas piezas. No existe para verse, existe para que `Trigger`, `Portal`, `Overlay`, `Content` y `Close` trabajen juntos.

### Entiendo la responsabilidad de `Trigger`.

Si. `Trigger` es el elemento que abre el dialog. Tambien recibe atributos accesibles y ayuda a que el foco pueda volver a ese punto cuando el dialog se cierra.

### Entiendo por que existe `Portal`.

Si. `Portal` monta el contenido flotante fuera de la jerarquia normal del DOM, normalmente en `body`. Esto evita problemas de `z-index`, `overflow` y contenedores que recortan el modal.

### Entiendo la diferencia entre `Overlay` y `Content`.

Si. `Overlay` es el fondo que separa el modal de la pagina. `Content` es la caja principal del dialog, donde vive el contenido visible y donde Radix maneja foco, teclado y accesibilidad.

### Entiendo por que `Title` y `Description` son importantes.

Si. No son solo texto. Crean relaciones accesibles mediante `aria-labelledby` y `aria-describedby`, para que lectores de pantalla entiendan el titulo y contexto del dialog.

### Entiendo como se cierra un dialog.

Si. Puede cerrarse con `Dialog.Close`, con `Esc` o con interacciones fuera del contenido si estan permitidas. `Root` coordina el cambio de estado y el foco vuelve al trigger.

### Entiendo para que sirve `data-state`.

Si. `data-state` expone el estado `open` o `closed` a CSS. Esto permite animaciones y estilos condicionales sin escribir logica extra.

### Entiendo controlled vs uncontrolled.

Si. En uncontrolled, Radix maneja el estado. En controlled, el proyecto maneja `open` y `onOpenChange`. Controlled conviene cuando el dialog se conecta con forms, side effects, URL o reglas de negocio.

### Entiendo como `asChild` ayuda a personalizar.

Si. `asChild` permite usar un componente propio como `Button` o `Link` mientras Radix le pasa el comportamiento de la primitive. Asi se mantiene semantica, estilo propio y comportamiento accesible.

## Siguiente leccion

`installing-shadcn-ui.md`
