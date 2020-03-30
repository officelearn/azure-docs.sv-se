---
title: Interaktionstyper och kortkommandon för ritverktyg på kartan | Microsoft Azure Maps
description: Rita och redigera former med hjälp av en mus, pekskärm eller tangentbord i Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fb8a1e1a8c29086553500bdad2e4604d1e1ef471
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198300"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Interaktionstyper och kortkommandon i ritverktygsmodulen

I den här artikeln beskrivs alla olika sätt att rita och redigera former med hjälp av mus, pekskärm eller kortkommandon.

Rithanteraren stöder tre olika sätt att interagera med kartan för att rita former.

* `click`- Koordinater läggs till när du klickar på musen eller touchen.
* `freehand `- Koordinater läggs till när musen eller beröringen dras på kartan.
* `hybrid`- Koordinater läggs till när musen eller touchen klickas eller dras.

## <a name="how-to-draw-shapes"></a>Så här ritar du former

 Innan någon form kan ritas anger du `drawingMode` alternativet för rithanteraren till en ritningsinställning som stöds. Den här inställningen kan programmeras eller anropas genom att trycka på en av ritknapparna i verktygsfältet. Ritläget förblir aktiverat, även efter att en form har ritats, vilket gör det enkelt att rita ytterligare former av samma typ. Ställ in ritläget på ett inaktivt läge. Du kan också växla till inaktivt läge genom att klicka på knappen Aktuella ritlägen i verktygsfältet.

I nästa avsnitt beskrivs alla olika sätt som former kan ritas på kartan.

### <a name="how-to-draw-a-point"></a>Hur man drar en punkt

När rithanteraren `draw-point` är i ritläge kan följande åtgärder göras för att rita punkter på kartan. Dessa metoder fungerar med alla interaktionslägen.

**Börja rita**
 - Klicka på vänster musknapp eller tryck på kartan för att lägga till en punkt på kartan. 
 - Om musen är över kartan `F` trycker du på tangenten och en punkt läggs till vid muspekarens koordinat. Den här metoden ger högre noggrannhet för att lägga till en punkt på kartan. Det kommer att bli mindre rörelse på musen på grund av att trycka rörelse av vänster musknapp.
 - Fortsätt att klicka, röra `F` vid eller trycka för att lägga till fler punkter på kartan.
 
**Avsluta ritningen**
 - Klicka på valfri knapp i verktygsfältet ritning. 
 - Ställ in ritningsläget programmässigt. 
 - Tryck `C` på.

**Avbryt ritning**
 - Tryck `Escape` på.

### <a name="how-to-draw-a-line"></a>Hur man drar en linje

När rithanteraren `draw-line` är i läge kan följande åtgärder göras för att rita punkter på kartan, beroende på interaktionsläget.

**Börja rita**
 - Klicka läge
   * Klicka på vänster musknapp eller tryck på kartan för att lägga till varje punkt på en linje på kartan. En koordinat läggs till på raden för varje klick eller touch. 
   * Om musen är över kartan `F` trycker du på tangenten och en punkt läggs till vid muspekarens koordinat. Den här metoden ger högre noggrannhet för att lägga till en punkt på kartan. Det kommer att bli mindre rörelse på musen på grund av att trycka rörelse av vänster musknapp.
   * Fortsätt att klicka tills alla önskade punkter har lagts till på raden.
 - Frihandsläge
   * Tryck nedåt på vänster musknapp eller tryck nedåt på kartan och dra musen eller beröringspunkten. Koordinater läggs till på linjen när musen eller beröringspunkten flyttas runt kartan. Så snart musen eller touch-up händelsen utlöses, ritningen är klar. Hur ofta koordinater läggs till definieras `freehandInterval` av alternativet ritningshanterare.
 - Hybridläge
   * Växla mellan klick- och frihandsmetoder, efter önskemål, medan du ritar en enda linje. Klicka till exempel på några punkter och håll sedan ned och dra musen för att lägga till en massa punkter och klicka sedan på några fler. 

**Avsluta ritningen**
 - Hybrid-/klickläge
   * Dubbelklicka på kartan vid den sista punkten. 
   * Klicka på valfri knapp i verktygsfältet ritning. 
   * Ställ in ritningsläget programmässigt. 
 - Frihandsläge
   * Släpp musknappen eller beröringspunkten.
 - Tryck `C` på.

**Avbryt ritning**
 - Tryck `Escape` på.

### <a name="how-to-draw-a-polygon"></a>Hur man drar en polygon

När rithanteraren `draw-polygon` är i läge kan följande åtgärder göras för att rita punkter på kartan, beroende på interaktionsläget.

**Börja rita**
 - Klicka läge
   * Klicka på vänster musknapp eller tryck på kartan för att lägga till varje punkt i en polygon på kartan. En koordinat läggs till i polygonen för varje klick eller beröring. 
   * Om musen är över kartan `F` trycker du på tangenten och en punkt läggs till vid muspekarens koordinat. Den här metoden ger högre noggrannhet för att lägga till en punkt på kartan. Det kommer att bli mindre rörelse på musen på grund av att trycka rörelse av vänster musknapp.
   * Fortsätt att klicka tills alla önskade punkter har lagts till i polygonen.
 - Frihandsläge
   * Tryck nedåt på vänster musknapp eller tryck nedåt på kartan och dra musen eller beröringspunkten. Koordinater läggs till i polygonen när musen eller beröringspunkten flyttas runt kartan. Så snart musen eller touch-up händelsen utlöses, ritningen är klar. Hur ofta koordinater läggs till definieras `freehandInterval` av alternativet ritningshanterare.
 - Hybridläge
   * Växla mellan klick- och frihandsmetoder, efter önskemål, medan du ritar en enda polygon. Klicka till exempel på några punkter och håll sedan ned och dra musen för att lägga till en massa punkter och klicka sedan på några fler. 

**Avsluta ritningen**
 - Hybrid-/klickläge
   * Dubbelklicka på kartan vid den sista punkten. 
   * Klicka på den första punkten i polygonen.
   * Klicka på valfri knapp i verktygsfältet ritning. 
   * Ställ in ritningsläget programmässigt. 
 - Frihandsläge
   * Släpp musknappen eller beröringspunkten.
 - Tryck `C` på.

**Avbryt ritning**
 - Tryck `Escape` på.

### <a name="how-to-draw-a-rectangle"></a>Hur man ritar en rektangel

När rithanteraren `draw-rectangle` är i läge kan följande åtgärder göras för att rita punkter på kartan, beroende på interaktionsläget. Den genererade formen följer den [utökade GeoJSON-specifikationen för rektanglar](extend-geojson.md#rectangle).

**Börja rita**
 - Tryck nedåt på vänster musknapp eller tryck nedåt på kartan för att lägga till rektangelns första hörn och dra för att skapa rektangeln. 

**Avsluta ritningen**
 - Släpp musknappen eller beröringspunkten.
 - Ställ in ritningsläget programmässigt. 
 - Tryck `C` på.

**Avbryt ritning**
 - Tryck `Escape` på.

### <a name="how-to-draw-a-circle"></a>Hur man ritar en cirkel

När rithanteraren `draw-circle` är i läge kan följande åtgärder göras för att rita punkter på kartan, beroende på interaktionsläget. Den genererade formen följer den [utökade GeoJSON-specifikationen för cirklar](extend-geojson.md#circle).

**Börja rita**
 - Tryck nedåt på vänster musknapp eller tryck nedåt på kartan för att lägga till cirkelns mittpunkt och dra ge cirklarna en radie. 

**Avsluta ritningen**
 - Släpp musknappen eller beröringspunkten.
 - Ställ in ritningsläget programmässigt. 
 - Tryck `C` på.

**Avbryt ritning**
 - Tryck `Escape` på.

## <a name="keyboard-shortcuts"></a>Kortkommandon

Ritverktygen stöder kortkommandon. Dessa kortkommandon fungerar när kartan är i fokus.

| Nyckel      | Åtgärd                            |
|----------|-----------------------------------|
| `C` | Slutför alla pågående ritningar och anger att ritläget ska vara inaktivt. Fokus flyttas till kartelement på den översta nivån.  |
| `Escape` | Avbryter alla pågående ritningar och anger att ritläget ska vara inaktivt. Fokus flyttas till kartelement på den översta nivån.  |
| `F` | Lägger till en koordinat till en punkt, linje eller polygon om musen är över kartan. Motsvarande åtgärd för att klicka på kartan när du är i klick- eller hybridläge. Denna genväg möjliggör mer exakta och snabbare ritningar. Du kan använda ena handen för att placera musen och andra för att trycka på knappen utan att flytta musen från tryckgesten. |

## <a name="next-steps"></a>Nästa steg

Läs mer om klasserna i ritverktygsmodulen:

> [!div class="nextstepaction"]
> [Rithanterare](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Verktygsfältet Rita](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
