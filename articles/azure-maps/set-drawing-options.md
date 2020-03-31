---
title: Modul för ritverktyg | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du anger data för ritalternativ med hjälp av Microsoft Azure Maps Web SDK
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 711609f9382e2153cbc738d544933796dbbe2e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334314"
---
# <a name="use-the-drawing-tools-module"></a>Använda modulen för ritningsverktyg

Azure Maps Web SDK tillhandahåller en *ritverktygsmodul*. Den här modulen gör det enkelt att rita och redigera former på kartan med hjälp av en inmatningsenhet, till exempel en mus eller pekskärm. Kärnklassen för den här modulen är [ritningshanteraren](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). Rithanteraren innehåller alla funktioner som behövs för att rita och redigera former på kartan. Den kan användas direkt, och den är integrerad med ett anpassat verktygsfältsgränssnitt. Du kan också använda den inbyggda [ritningsverktygsklassen.](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Läsa in ritverktygsmodulen på en webbsida

1. Skapa en ny HTML-fil och [implementera kartan som vanligt](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).
2. Läs in ritverktygsmodulen i Azure Maps. Du kan läsa in den på ett av två sätt:
    - Använd den globalt värdbaserade Azure Content Delivery Network-versionen av Azure Maps-tjänstmodulen. Lägg till referens till JavaScript- och `<head>` CSS-formatmallen i elementet i filen:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - Du kan också läsa in modulen ritverktyg för Azure Maps Web SDK-källkoden lokalt med hjälp av npm-paketet för [azure-maps-drawing-tools](https://www.npmjs.com/package/azure-maps-drawing-tools) och sedan vara värd för den med din app. Det här paketet innehåller även TypeScript-definitioner. Använd det här kommandot:
    
        > **npm installera azure-maps-drawing-tools**
    
        Lägg sedan till en referens till JavaScript- `<head>` och CSS-formatmallen i elementet i filen:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>Använd rithanteraren direkt

När modulen för ritverktyg har lästs in i programmet kan du aktivera rit- och redigeringsfunktioner med hjälp av [rithanteraren](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). Du kan ange alternativ för rithanteraren medan du `drawingManager.setOptions()` instansierar den eller alternativt använder funktionen.

### <a name="set-the-drawing-mode"></a>Ställ in ritläget

Följande kod skapar en instans av rithanteraren och anger alternativet för **ritläge.** 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Koden nedan är ett komplett exempel på hur du ställer in ett ritläge för rithanteraren. Klicka på kartan för att börja rita en polygon.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rita en polygon" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>Rita en polygon</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Ange interaktionstyp

Rithanteraren stöder tre olika sätt att interagera med kartan för att rita former.

* `click`- Koordinater läggs till när du klickar på musen eller touchen.
* `freehand `- Koordinater läggs till när musen eller beröringen dras på kartan. 
* `hybrid`- Koordinater läggs till när musen eller touchen klickas eller dras.

Följande kod aktiverar ritläget för polygon och anger vilken typ av `freehand`ritningsinteraktion som ritningshanteraren ska följa . 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Det här kodexemplet implementerar funktionen för att rita en polygon på kartan. Håll bara ner vänster musknapp och dra runt den, fritt.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Frihandsritning" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Pen <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>Free hand-ritningen</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Anpassa ritalternativ

De tidigare exemplen visade hur du anpassar ritningsalternativen samtidigt som du instansierar rithanteraren. Du kan också ange alternativen för `drawingManager.setOptions()` rithanteraren med hjälp av funktionen. Nedan finns ett verktyg för att testa anpassning av alla alternativ för rithanteraren med hjälp av funktionen setOptions.

<br/>

<iframe height="685" title="Anpassa rithanteraren" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>Hämta formdata</a> från<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder ytterligare funktioner i ritverktygsmodulen:

> [!div class="nextstepaction"]
> [Lägga till ett verktygsfält för ritning](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [Hämta formdata](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagera på rithändelser](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Interaktionstyper och kortkommandon](drawing-tools-interactions-keyboard-shortcuts.md)

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Rithanterare](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Verktygsfältet Rita](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
