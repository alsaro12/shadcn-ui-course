# Updating and Maintaining Components

Fuente: https://vercel.com/academy/shadcn-ui/updating-and-maintaining-components

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/updating-and-maintaining-components.md

Ultima lectura: 2026-07-02

## Resumen

Este capitulo explica como mantener componentes de `shadcn/ui` despues de copiarlos al proyecto.

La idea central:

```txt
copy-paste ownership
-> control total
-> cambios sin sorpresas automaticas
-> responsabilidad de mantenimiento
```

En `shadcn/ui`, los componentes no se actualizan solos como una dependencia tradicional de npm. Cuando el codigo vive en tu repo, tu decides cuando revisar cambios, que mejoras traer y que personalizaciones conservar.

## Objetivo del capitulo

Entender que el modelo de ownership tambien implica mantenimiento.

Al terminar, deberia poder explicar:

- que se gana al copiar componentes al proyecto;
- que responsabilidades aparecen despues de copiarlos;
- como comparar un componente local contra la version mas reciente;
- cuando conviene usar proxy components;
- cuando conviene editar el source directamente;
- como aplicar updates sin perder personalizaciones.

## Ownership: beneficios y responsabilidades

Cuando copias un componente de `shadcn/ui`, ganas:

- control completo sobre comportamiento y estilos;
- estabilidad frente a cambios externos;
- personalizaciones persistentes;
- optimizaciones especificas del proyecto.

Pero tambien te encargas de:

- revisar fixes de seguridad de dependencias;
- traer bug fixes relevantes;
- incorporar mejoras cuando aporten valor;
- manejar breaking changes de forma deliberada.

El tradeoff es claro:

```txt
mas control
-> menos updates automaticos
-> mas responsabilidad sobre el codigo
```

## Proxying components

Una estrategia para personalizar sin tocar el archivo original es crear un componente proxy.

Ejemplo:

```tsx
import {
  Tooltip,
  TooltipContent,
  TooltipTrigger,
} from "@/components/ui/tooltip";

type MyTooltipProps = {
  text: string;
  children: React.ReactNode;
};

export function MyTooltip({ text, children }: MyTooltipProps) {
  return (
    <Tooltip>
      <TooltipTrigger>{children}</TooltipTrigger>
      <TooltipContent className="bg-black text-white">
        <p>{text}</p>
      </TooltipContent>
    </Tooltip>
  );
}
```

Ventaja:

- el componente original queda mas facil de reemplazar con `--overwrite`.

Costo:

- crece el numero de componentes;
- puede duplicar parte del design system;
- reduce el sentido del ownership directo, porque el componente base queda como una capa que intentas no tocar.

## Actualizar con overwrite

Si el componente base no fue modificado directamente, se puede reinstalar con overwrite:

```bash
npx shadcn@latest add tooltip --overwrite
```

Tambien se pueden actualizar todos:

```bash
npx shadcn@latest add --all --overwrite
```

Este flujo es util cuando los componentes locales siguen muy cerca del registry.

No conviene ejecutarlo a ciegas si el proyecto ya hizo personalizaciones importantes.

## Overwriting components

El uso mas comun en `shadcn/ui` es instalar los componentes y editarlos directamente.

Ese es el modelo intencional:

```txt
registry source -> copied file -> project-owned component
```

La consecuencia es que los updates upstream ya no entran solos.

Para revisar diferencias se usa:

```bash
npx shadcn@latest diff button
```

Para revisar todos los componentes:

```bash
npx shadcn@latest diff
```

Despues de ver el diff, el trabajo correcto es traer manualmente lo que aplica.

## Estrategia practica de mantenimiento

Flujo recomendado:

1. Revisar el diff.
2. Entender si el cambio es bug fix, mejora interna, accesibilidad, dependencia o cambio visual.
3. Comparar contra las personalizaciones locales.
4. Aplicar solo los cambios que aportan valor.
5. Probar el componente en los estados importantes.
6. Dejar el commit pequeno y facil de revisar.

Esto evita dos extremos:

- ignorar todo update por miedo a perder cambios;
- sobrescribir todo y romper personalizaciones locales.

## Quiz

Pregunta:

```txt
What is the recommended approach when shadcn/ui releases an update to a component you've heavily customized?
```

Respuesta correcta:

```txt
Review the changes and selectively apply updates while preserving customizations.
```

Explicacion:

Si un componente fue muy personalizado, el update no debe aplicarse de forma automatica. Primero se revisa que cambio trae la version nueva y despues se incorpora manualmente lo que sirve.

## Done-When

Resuelto: Entiendo que `shadcn/ui` no actualiza componentes automaticamente.

Cuando agrego un componente, el archivo queda dentro de mi repo. Desde ese momento, no se comporta como una dependencia que recibe updates sola. Yo decido cuando revisar cambios y cuando aplicarlos.

Resuelto: Entiendo el beneficio del ownership.

El beneficio es que puedo modificar comportamiento, estructura y estilos sin esperar permisos de una libreria externa. El componente pasa a ser codigo del proyecto.

Resuelto: Entiendo la responsabilidad del ownership.

Si el codigo es mio, tambien debo mantenerlo. Eso incluye revisar bugs, cambios de seguridad, mejoras de accesibilidad y cambios del registry que puedan convenir al proyecto.

Resuelto: Entiendo cuando usar `--overwrite`.

`--overwrite` sirve para reemplazar un componente local con la version del registry. Es util si no lo he personalizado mucho. Si ya lo edite, puede borrar cambios importantes.

Resuelto: Entiendo para que sirve `npx shadcn@latest diff`.

El comando `diff` me permite comparar mi componente local con la version mas reciente del registry. Asi puedo decidir que partes traer sin reemplazar todo.

Resuelto: Entiendo el tradeoff de proxy components.

Un proxy component permite personalizar sin tocar el componente base, lo que facilita updates. El costo es que agrega otra capa y puede duplicar el tamano del sistema de componentes.

Resuelto: Entiendo el enfoque recomendado para componentes personalizados.

Si un componente fue modificado, lo correcto es revisar el diff y aplicar cambios selectivamente. No conviene actualizar a ciegas ni ignorar todos los updates.

## Nota personal

Para proyectos reales, conviene tratar componentes de `shadcn/ui` como codigo propio. No son archivos intocables del vendor. Si el componente cambia para resolver un problema del producto, esta bien editarlo, pero los updates futuros deben pasar por revision de diff.

## Siguiente leccion

`exploring-globals-css.md`
