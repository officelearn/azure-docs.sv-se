---
title: Hämta data från former på en karta | Microsoft Azure Maps
description: I den här artikeln lär du dig hur du får form data som ritas på en karta med hjälp av Microsoft Azure Maps-webbsdk.
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: ca1b9854b118d359167873313f1fcb11bfecee52
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911013"
---
# <a name="get-shape-data"></a>Hämta formdata

Den här artikeln visar hur du hämtar form data som har ritats på kartan med funktionen [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--) **drawingManager. GetSource ()** . Det kan finnas olika scenarier där du kanske vill extrahera polyjson-data för en ritad form och använda den någon annan stans.  


## <a name="get-data-from-drawn-shape"></a>Hämta data från den ritade formen

Följande funktion hämtar den ritade formens källdata och matar ut den på skärmen. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Nedan visas det fullständiga kod exemplet, där du kan rita en form för att testa funktionerna:

<br/>

<iframe height="686" title="Hämta formdata" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Se hur pennan <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>får form data</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder ytterligare funktioner i modulen rit verktyg:

> [!div class="nextstepaction"]
> [Reagera på rit händelser](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Interaktions typer och kortkommandon](drawing-tools-interactions-keyboard-shortcuts.md)

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Verktygsfältet Rita](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
