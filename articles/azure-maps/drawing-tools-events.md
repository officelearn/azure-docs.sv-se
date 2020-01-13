---
title: Lägg till ett verktygsfält för ritning i en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du lägger till ett verktygsfält för ritning till en karta med Microsoft Azure Maps-webbsdk
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fd235f3f39d67f86c8387add79ca0dbf17dc5906
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911673"
---
# <a name="drawing-tool-events"></a>Rit verktygs händelser

När du använder rit verktyg på en karta är det ofta användbart att reagera på vissa händelser när användaren ritar på kartan. I följande tabell visas alla händelser som stöds av klassen `DrawingManager`.

| Händelse | Beskrivning |
|-------|-------------|
| `drawingchanged` | Utlöses när en koordinat i en form har lagts till eller ändrats. | 
| `drawingchanging` | Utlöses när en förhands gransknings koordinat för en form visas. Kan till exempel utlösa flera gånger när en koordinat dras. | 
| `drawingcomplete` | Utlöses när en form har tagits bort eller tagits bort från redigerings läget. |
| `drawingmodechanged` | Utlöses när rit läget har ändrats. Det nya ritnings läget överförs till händelse hanteraren. |
| `drawingstarted` | Utlöses när användaren börjar att rita en form eller placerar en form i redigerings läge.  |

Följande kod visar hur händelserna i modulen rit verktyg fungerar. Rita former på kartan och se vilka händelser som utlöses.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rit verktygs händelser" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se händelser för rit verktyg för rit <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>verktyg</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Exempel

Följande är exempel på några vanliga scenarier som använder rit verktygs händelser.

### <a name="select-points-in-polygon-area"></a>Välj punkter i polygon-ytan

Följande kod visar hur du övervakar ritningen över former som representerar polygon-områden (polygoner, rektanglar och cirklar) och avgör vilka data punkter på kartan som ligger inom det ritade området. `drawingcomplete`-händelsen används för att utlösa Select-logiken. I Välj logik upprepas alla data punkter på kartan genom att de testas och testas i snitt med området polygon i den ritade figuren. I det här exemplet används [Turf. js](https://turfjs.org/) -biblioteket med öppen källkod för att utföra en spatial skärnings beräkning.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Välj data i ritat polygon-ytdiagram" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/XWJdeja'>data i den ritade polygonens yta</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Rita och Sök i polygon-område

Följande kod visar hur du utför en sökning efter intressanta punkter i ett form område när användaren har ritat formen. `drawingcomplete`-händelsen används för att utlösa Sök logiken. Om användaren ritar en rektangel eller polygon utförs en sökning i geometrin. Om en cirkel ritas, används Radi-och Center positionen för att utföra en orienterings punkts ökning. Händelsen `drawingmodechanged` används för att avgöra när användaren växlar till ett ritläge och rensar arbets ytan.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rita och Sök i polygon-område" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>ritning och Sök i polygon-område</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Skapa ett Mät verktyg

Följande kod visar hur du kan använda rit händelser för att skapa ett Mät verktyg. `drawingchanging` används för att övervaka formen när den ritas. När användaren flyttar musen beräknas formens dimensioner. `drawingcomplete`-händelsen används för att göra en slutgiltig beräkning på formen när den har ritats. `drawingmodechanged`-händelsen används för att avgöra när användaren växlar till ett ritläge och rensar arbets ytan och gammal mått information.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mät verktyg" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>Mät verktyget</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder ytterligare funktioner i modulen rit verktyg:

> [!div class="nextstepaction"]
> [Hämta formdata](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Interaktions typer och kortkommandon](drawing-tools-interactions-keyboard-shortcuts.md)

Läs mer om modulen tjänster:

> [!div class="nextstepaction"]
> [Tjänstmodul](how-to-use-services-module.md)

Se fler kod exempel:

> [!div class="nextstepaction"]
> [Kod exempel sida](https://aka.ms/AzureMapsSamples)
