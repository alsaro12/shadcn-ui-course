# Compound Components and Advanced Composition

Fuente: https://vercel.com/academy/shadcn-ui/compound-components-and-advanced-composition

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/compound-components-and-advanced-composition.md

Ultima lectura: 2026-07-02

## Resumen

Este capitulo explica el patron de compound components para construir APIs flexibles y mantenibles.

La idea central:

```txt
one large component with many props
-> split into cooperating components
-> clearer composition
```

En vez de crear un componente gigante con demasiadas props, se divide la responsabilidad en piezas que trabajan juntas: `Root`, `Header`, `Title`, `Content`, `Footer`, `Status`, etc.

## Objetivo del capitulo

Entender como usar composicion avanzada para componentes complejos.

Al terminar, deberia poder explicar:

- que es un compound component;
- por que evita componentes monoliticos;
- como se comparte estado con context;
- que responsabilidad tiene `Root`;
- como disenar subcomponentes;
- cuando conviene usar este patron;
- como optimizar y testear componentes compuestos.

## Problema: componente monolitico

Un componente con demasiadas props puede terminar asi:

```tsx
<DataTable
  data={data}
  columns={columns}
  pagination
  sorting
  filtering
  actions={["edit", "delete"]}
  rowSelection
/>
```

Cuando crece, se vuelve dificil de leer, mantener y extender.

## Solucion: compound components

La misma UI puede expresarse por composicion:

```tsx
<DataTable.Root data={data}>
  <DataTable.Toolbar>
    <DataTable.Search />
    <DataTable.Filter />
    <DataTable.Actions />
  </DataTable.Toolbar>

  <DataTable.Content>
    <DataTable.Header>
      <DataTable.Column sortable>Name</DataTable.Column>
      <DataTable.Column sortable>Date</DataTable.Column>
      <DataTable.Column>Actions</DataTable.Column>
    </DataTable.Header>

    <DataTable.Body>
      {data.map((row) => (
        <DataTable.Row key={row.id} data={row}>
          <DataTable.Cell>{row.name}</DataTable.Cell>
          <DataTable.Cell>{row.date}</DataTable.Cell>
          <DataTable.Cell>
            <DataTable.RowActions row={row} />
          </DataTable.Cell>
        </DataTable.Row>
      ))}
    </DataTable.Body>
  </DataTable.Content>

  <DataTable.Pagination />
</DataTable.Root>
```

El resultado es mas expresivo: la estructura visual se entiende leyendo el JSX.

## Cuando usar este patron

Conviene usar compound components cuando:

- el componente tiene muchas areas internas;
- algunas partes son opcionales;
- necesitas varias configuraciones de uso;
- hay estado compartido entre subcomponentes;
- quieres evitar una API con demasiadas props;
- necesitas que el consumidor componga la UI.

No hace falta usarlo para componentes simples como un badge o un boton basico.

## Context como comunicacion interna

Los subcomponentes comparten estado usando context:

```tsx
interface CardContextValue {
  isCollapsible: boolean;
  isCollapsed: boolean;
  setIsCollapsed: (collapsed: boolean) => void;
  isExpandable: boolean;
  isExpanded: boolean;
  setIsExpanded: (expanded: boolean) => void;
  variant: "default" | "outline" | "ghost";
  size: "default" | "sm" | "lg";
}
```

El hook interno protege el uso correcto:

```tsx
const useCardContext = () => {
  const context = React.useContext(CardContext);

  if (!context) {
    throw new Error("Card compound components must be used within Card.Root");
  }

  return context;
};
```

Esto evita usar `Card.Title` fuera de `Card.Root`.

## Responsabilidad de `Root`

`Root` administra:

- estado compartido;
- provider de context;
- variantes globales;
- tamanos;
- configuracion de colapso, expansion o dismiss;
- contenedor base.

Ejemplo conceptual:

```tsx
function CardRoot({
  children,
  collapsible = false,
  defaultCollapsed = false,
  variant = "default",
  size = "default",
}: CardRootProps) {
  const [isCollapsed, setIsCollapsed] = React.useState(defaultCollapsed);

  const contextValue = {
    isCollapsible: collapsible,
    isCollapsed,
    setIsCollapsed,
    variant,
    size,
  };

  return (
    <CardContext.Provider value={contextValue}>
      <div>{children}</div>
    </CardContext.Provider>
  );
}
```

## Responsabilidad de subcomponentes

Cada subcomponente debe hacer una cosa:

- `Header`: estructura superior y acciones;
- `Title`: titulo con estilo consistente;
- `Description`: texto secundario;
- `Content`: contenido principal;
- `Footer`: acciones inferiores;
- `Status`: indicador de estado.

Si `Content` detecta que la card esta colapsada, puede ocultarse:

```tsx
function CardContent({ children, forceVisible = false }) {
  const { isCollapsed } = useCardContext();

  if (isCollapsed && !forceVisible) {
    return null;
  }

  return <div>{children}</div>;
}
```

## API final

El export puede agrupar piezas:

```tsx
export const Card = {
  Root: CardRoot,
  Header: CardHeader,
  Title: CardTitle,
  Description: CardDescription,
  Content: CardContent,
  Footer: CardFooter,
  Status: CardStatus,
};
```

Uso:

```tsx
<Card.Root collapsible>
  <Card.Header>
    <Card.Title>Project Status</Card.Title>
    <Card.Description>Current project health overview</Card.Description>
  </Card.Header>
  <Card.Content>
    <Card.Status status="success" label="All systems operational" />
  </Card.Content>
</Card.Root>
```

## Optimizacion

Para componentes grandes, se puede separar context:

```txt
CardStateContext -> estado que cambia
CardConfigContext -> configuracion estable
```

Esto reduce renders innecesarios cuando solo cambia una parte del estado.

Tambien conviene usar `useMemo` para valores de context si hay subcomponentes pesados.

## Testing

Los tests deben cubrir:

- que los subcomponentes se rendericen dentro de `Root`;
- que se comparta context correctamente;
- que colapsar o expandir cambie la UI esperada;
- que los componentes lancen error si se usan fuera de contexto;
- que variantes y tamanos apliquen clases correctas.

## Quiz

Pregunta:

```txt
What is the primary advantage of compound components over monolithic components with many props?
```

Respuesta correcta:

```txt
More flexible composition and clearer separation of concerns.
```

Explicacion:

El beneficio principal es distribuir responsabilidades. Cada pieza hace una parte del trabajo y el consumidor puede componer solo lo que necesita.

## Reflection Prompt

Pregunta:

```txt
Think about a complex component that could benefit from the compound component pattern.
```

Respuesta:

```txt
A data table is a good candidate. I would split it into Table.Root, Table.Toolbar, Table.Search, Table.Filter, Table.Content, Table.Header, Table.Column, Table.Body, Table.Row, Table.Cell, Table.RowActions, and Table.Pagination. Root would own shared data, selection, sorting, filtering, and pagination state. The smaller components would consume context and render their specific part. This avoids one huge DataTable component with many props and makes the JSX describe the actual layout.
```

## Done-When

Resuelto: Entiendo que es un compound component.

Es un conjunto de componentes que trabajan juntos bajo una API comun, normalmente compartiendo estado desde `Root`.

Resuelto: Entiendo por que evita componentes monoliticos.

En lugar de meter muchas props en un solo componente, reparte la responsabilidad entre piezas pequenas y claras.

Resuelto: Entiendo el rol de context.

Context permite que subcomponentes accedan a estado compartido sin pasar props manualmente por cada nivel.

Resuelto: Entiendo la responsabilidad de `Root`.

`Root` provee context, administra estado compartido y define configuracion global como `variant`, `size`, `collapsible` o `expandable`.

Resuelto: Entiendo como disenar subcomponentes.

Cada subcomponente debe tener una responsabilidad concreta y consumir solo el contexto que necesita.

Resuelto: Entiendo cuando usar este patron.

Lo usaria en componentes complejos con varias partes opcionales, como tablas, dashboards, cards avanzadas, steppers o builders.

Resuelto: Entiendo que tambien se debe testear la composicion.

Hay que probar estado compartido, renderizado condicional, uso fuera de contexto y variantes.

## Nota personal

Compound components funcionan bien cuando la estructura importa. El JSX deja de ser una lista enorme de props y se convierte en una representacion clara de la interfaz.

## Cierre del curso

Con este capitulo termina el curso. La ruta completa cubre desde fundamentos de `shadcn/ui` hasta componentes propios, registries, publicacion, estado controlable y composicion avanzada.
