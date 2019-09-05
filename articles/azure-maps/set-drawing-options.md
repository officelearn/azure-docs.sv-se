---
title: Ange alternativ för ritning i Azure Maps | Microsoft Docs
description: Ange ritnings alternativ data med Azure Maps Web SDK
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d85525274db7818737b62ad4e9ea2026b8aef3b2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309778"
---
# <a name="set-drawing-options"></a>Ange alternativ för ritning

Den här artikeln visar hur olika alternativ i [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) ändrar användar upplevelsen. Du kan ange alternativ för ritnings hanteraren när du instansierar den eller också kan du använda funktionen **drawingManager. setOptions ()** för att ange alternativ.


## <a name="set-drawing-mode"></a>Ange rit läge

Följande kod skapar en instans av Drawing Manager och anger alternativet för rit **läge** . 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Koden nedan är ett komplett exempel på hur du ställer in ett ritläge i Drawing Manager. Klicka på kartan för att börja rita en polygon.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rita en polygon" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan genom att <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>Rita en polygon</a> efter Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="set-interaction-type"></a>Ange typ av interaktion

I följande kod anges vilken typ av ritnings interaktion som ritnings hanteraren ska följa. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

Nedan visas kod exemplet med en funktion som gör att du kan rita på kartan fritt, samtidigt som du håller ned den vänstra mus knappen och drar den runt. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kostnads fri ritning" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se rit <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>fri hands ritningen</a> med Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="customizing-drawing-options"></a>Anpassa ritnings alternativ

I föregående exempel visas hur du anpassar ritnings alternativ när du instansierar ritnings hanteraren. Du kan också ange alternativ för Drawing Manager med hjälp av funktionen **drawingManager. setOptions ()** . Nedan finns ett verktyg för att testa anpassningen av alla alternativ för Drawing Manager med hjälp av funktionen setOptions.

<br/>

<iframe height="685" title="Anpassa Drawing Manager" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Se hur pennan <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>får form data</a> genom att Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Verktygsfältet Rita](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
