---
title: Lägga till ett verktygsfält för ritning på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du lägger till ett verktygsfält för ritning på en karta med Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: d8509af7829910bdda8bba3d63553e83626fe784
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804681"
---
# <a name="drawing-tool-events"></a>Händelser i ritverktyg

När du använder ritverktyg på en karta är det bra att reagera på vissa händelser när användaren ritar på kartan. I den här tabellen visas `DrawingManager` alla händelser som stöds av klassen.

| Händelse | Beskrivning |
|-------|-------------|
| `drawingchanged` | Utlöses när någon koordinat i en form har lagts till eller ändrats. | 
| `drawingchanging` | Utlöses när en förhandsgranskningskoordinat för en form visas. Den här händelsen avfyras till exempel flera gånger när en koordinat dras. | 
| `drawingcomplete` | Utlöses när en form har ritats eller tagits ur redigeringsläge. |
| `drawingmodechanged` | Utlöses när ritläget har ändrats. Det nya ritläget skickas till händelsehanteraren. |
| `drawingstarted` | Utlöses när användaren börjar rita en form eller sätter en form i redigeringsläge.  |

Följande kod visar hur händelserna i modulen Ritverktyg fungerar. Rita former på kartan och titta på när händelserna avfyras.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Händelser för ritverktyg" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se händelserna med <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>pennritverktyg</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>från Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Exempel

Låt oss se några vanliga scenarier som använder ritverktygshändelserna.

### <a name="select-points-in-polygon-area"></a>Markera punkter i polygonområdet

Den här koden visar hur du övervakar en händelse av en användare ritning former. Koden övervakar i det här exemplet former av polygoner, rektanglar och cirklar. Sedan avgör den vilka datapunkter på kartan som finns inom det ritade området. Händelsen `drawingcomplete` används för att utlösa urvalslogiken. I urvalslogiken loopar koden genom alla datapunkter på kartan. Den kontrollerar om det finns en skärningspunkt mellan punkten och området för den ritade formen. I det här exemplet används [turf.js-biblioteket](https://turfjs.org/) med öppen källkod för att utföra en beräkning av spatial skärningspunkter.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Markera data i ritat polygonområde" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Pen <a href='https://codepen.io/azuremaps/pen/XWJdeja'>Select-data i det ritade polygonområdet</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Rita och söka i polygonområdet

Den här koden söker efter intressanta platser i området i en form när användaren har ritat formen. Du kan ändra och köra koden genom att klicka på "Redigera på kodpenna" längst upp till höger i ramen. Händelsen `drawingcomplete` används för att utlösa söklogiken. Om användaren ritar en rektangel eller polygon utförs en sökning inuti geometri. Om en cirkel ritas används radien och mittpositionen för att utföra en intressepunkt. Händelsen `drawingmodechanged` används för att avgöra när användaren växlar till ritläget och den här händelsen rensar arbetsytan.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rita och söka i polygonområdet" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Pen <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>Draw och sök i polygonområdet</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Skapa ett mätverktyg

Koden nedan visar hur ritningshändelserna kan användas för att skapa ett mätverktyg. Den `drawingchanging` används för att övervaka formen, som den ritas. När användaren flyttar musen beräknas formens dimensioner. Händelsen `drawingcomplete` används för att göra en slutlig beräkning på formen efter att den har ritats. Händelsen `drawingmodechanged` används för att avgöra när användaren växlar till ett ritläge. Dessutom rensar händelsen `drawingmodechanged` arbetsytan och rensar gammal mätinformation.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mätverktyg" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>ritstiftsmätverktyget</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder ytterligare funktioner i ritverktygsmodulen:

> [!div class="nextstepaction"]
> [Hämta formdata](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Interaktionstyper och kortkommandon](drawing-tools-interactions-keyboard-shortcuts.md)

Läs mer om modulen Tjänster:

> [!div class="nextstepaction"]
> [Tjänstmodul](how-to-use-services-module.md)

Kolla in fler kodexempel:

> [!div class="nextstepaction"]
> [Exempelsida för kod](https://aka.ms/AzureMapsSamples)
