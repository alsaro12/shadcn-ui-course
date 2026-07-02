# Publishing Your Components

Fuente: https://vercel.com/academy/shadcn-ui/publishing-your-components

Version Markdown oficial: https://vercel.com/academy/shadcn-ui/publishing-your-components.md

Ultima lectura: 2026-07-02

## Resumen

Este capitulo explica una forma simple de publicar registry items para que otros proyectos puedan instalarlos por URL.

La idea central:

```txt
registry item JSON
-> static hosting
-> public URL
-> npx shadcn@latest add <url>
```

No hace falta crear un paquete npm ni una app completa. Para empezar, basta con servir el JSON como archivo estatico con headers correctos.

## Objetivo del capitulo

Entender como publicar un componente para que sea instalable desde internet.

Al terminar, deberia poder explicar:

- que estructura minima necesita el proyecto;
- donde colocar el registry item;
- para que sirve `vercel.json`;
- que headers debe tener el JSON;
- como desplegar;
- como instalar el componente desde la URL final.

## Estructura minima

El curso propone una estructura simple:

```txt
my-component/
├── public/
│   └── metric-card.json
└── vercel.json
```

El archivo `metric-card.json` vive dentro de `public/`.

Eso permite servirlo como archivo estatico.

## Configurar headers

Archivo:

```txt
vercel.json
```

Contenido:

```json
{
  "headers": [
    {
      "source": "/(.*).json",
      "headers": [
        {
          "key": "Access-Control-Allow-Origin",
          "value": "*"
        },
        {
          "key": "Content-Type",
          "value": "application/json"
        }
      ]
    }
  ]
}
```

Esto asegura dos cosas:

- el JSON se puede leer desde otros origenes;
- el archivo se sirve como `application/json`.

## Deploy

Desde la raiz del proyecto:

```bash
vercel --prod
```

Despues del deploy, el registry item queda disponible en una URL parecida a:

```txt
https://your-project-name.vercel.app/metric-card.json
```

## Instalar el componente

Cualquier proyecto puede instalarlo asi:

```bash
npx shadcn@latest add https://your-project-name.vercel.app/metric-card.json
```

El CLI descarga el JSON, lee dependencias y copia archivos en el proyecto destino.

## Cuando usar este enfoque

Conviene cuando:

- quieres compartir un componente rapidamente;
- el registry item ya esta listo;
- necesitas una URL publica;
- no quieres publicar un paquete npm;
- quieres probar distribucion con poco setup.

Para una organizacion grande, despues podria convenir crear un registry mas formal con versionado, documentacion, previews y reglas de calidad.

## Done-When

Resuelto: Entiendo que publicar un componente puede ser tan simple como servir un JSON.

Si el registry item esta bien formado y tiene una URL publica, el CLI puede instalarlo.

Resuelto: Entiendo donde va el archivo.

El JSON del registry item se coloca en `public/` para que sea accesible como archivo estatico.

Resuelto: Entiendo para que sirve `vercel.json`.

Sirve para configurar headers, especialmente CORS y `Content-Type`, de modo que el archivo pueda ser consumido correctamente.

Resuelto: Entiendo como se despliega.

Se ejecuta `vercel --prod` desde la raiz del proyecto que contiene `public/` y `vercel.json`.

Resuelto: Entiendo como se instala desde la URL.

Con `npx shadcn@latest add <url-del-json>`, el CLI puede traer el componente al proyecto destino.

Resuelto: Entiendo que no hace falta npm para este flujo.

El componente se distribuye como source code a traves de un registry item, no como paquete compilado.

## Nota personal

Este enfoque es util para empezar rapido. Primero se valida que el componente se pueda instalar por URL; despues se puede agregar documentacion, ejemplos, versionado y un registry mas completo.

## Siguiente leccion

`use-controllable-state.md`
