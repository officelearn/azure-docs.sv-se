---
title: Hämta data från former på en karta | Microsoft Azure Maps
description: I den här artikeln lär du dig hur du ritar formdata på en karta med hjälp av Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 88db018575f92e777223f383c65cd6db51ba515a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334418"
---
# <a name="get-shape-data"></a>Hämta formdata

Den här artikeln visar hur du hämtar data för former som ritas på kartan. Vi använder **funktionen drawingManager.getSource()** inuti [ritningshanteraren](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--). Det finns olika scenarier när du vill extrahera geojson-data för en ritad form och använda den någon annanstans.  


## <a name="get-data-from-drawn-shape"></a>Hämta data från ritad form

Följande funktion hämtar den ritade formens källdata och matar ut den till skärmen. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Nedan är det fullständiga kör kodexemplet, där du kan rita en form för att testa funktionen:

<br/>

<iframe height="686" title="Hämta formdata" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Hämta formdata</a> från<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder ytterligare funktioner i ritverktygsmodulen:

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
