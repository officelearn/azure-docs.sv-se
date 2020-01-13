---
title: Funktions typer och kortkommandon för rit verktyg på kartan | Microsoft Azure Maps
description: Rita och redigera former med hjälp av en mus, touch-skärm eller tangent bord i Microsoft Azure Maps-webbsdk
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 267579f7b4bbfe026f3aa01b00f01f3b872cf4a6
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911660"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Interaktions typer och kortkommandon i modulen rit verktyg

Den här artikeln beskriver alla olika sätt att rita och redigera former på en karta med hjälp av en mus, en pekskärm eller kortkommandon.

Drawing Manager har stöd för tre olika sätt att interagera med kartan för att rita former.

* `click`-koordinater läggs till när musen eller touchen klickas.
* `freehand `-koordinater läggs till när musen eller touchen dras på kartan. 
* `hybrid`-koordinater läggs till när musen eller touchen klickas eller dras.

## <a name="how-to-draw-shapes"></a>Rita former

I följande avsnitt beskrivs alla olika sätt som former kan ritas på kartan. Innan en form kan ritas måste `drawingMode` alternativet i Drawing Manager ställas in på en ritnings inställning som stöds. Detta kan göras via programmering eller genom att trycka på en av rit knapparna i verktygsfältet. Rit läget förblir aktiverat, även när en form har ritats, vilket gör det enkelt att rita ytterligare former av samma typ. Rit läget kan försättas i ett inaktivt tillstånd eller genom att klicka på knappen nuvarande ritnings lägen i verktygsfältet. 

### <a name="how-to-draw-a-point"></a>Så här ritar du en punkt

När ritnings hanteraren är i `draw-point` rit läge kan du utföra följande åtgärder för att rita punkter på kartan. Dessa metoder fungerar med alla interaktions lägen.

**Starta ritning**
 - Klicka på den vänstra mus knappen eller tryck på kartan för att lägga till en punkt på kartan. 
 - Om musen är över kartan trycker du på `F`-tangenten och en punkt läggs till med den koordinat där mus pekaren är. Detta ger en högre precisions metod för att lägga till en punkt på kartan eftersom det kommer att röra sig om mindre rörelse på musen på grund av att den vänstra mus knappen visas.
 - Fortsätt att klicka, vidrör eller trycka på `F` för att lägga till fler punkter på kartan.
 
**Slutför ritning**
 - Klicka på en knapp i verktygsfältet Rita. 
 - Ange rit läget program mässigt. 
 - Tryck på `C` nyckeln.

**Avbryt ritning**
 - Tryck på `Escape` nyckeln.

### <a name="how-to-draw-a-line"></a>Så här ritar du en linje

När ritnings hanteraren är i `draw-line` läge kan du utföra följande åtgärder för att rita punkter på kartan beroende på vad interaktions läget är inställt på.

**Starta ritning**
 - Klicka på läge
   * Klicka på den vänstra mus knappen eller tryck på kartan för att lägga till varje punkt på en linje på kartan. En koordinat läggs till i raden för varje klick/touch. 
   * Om musen är över kartan trycker du på `F`-tangenten och en punkt läggs till med den koordinat där mus pekaren är. Detta ger en högre precisions metod för att lägga till en punkt på kartan eftersom det kommer att röra sig om mindre rörelse på musen på grund av att den vänstra mus knappen visas.
   * Fortsätt att klicka tills alla önskade punkter har lagts till på raden.
 - FreeHand-läge
   * Tryck på vänster MUSKNAPP eller tryck på kartan och dra musen eller tryck punkten runt. Koordinater läggs till på raden när musen eller tryck punkten flyttas runt kartan. När musen eller touch-händelsen utlöses, slutförs ritningen. Den frekvens med vilken koordinater läggs till definieras av `freehandInterval` alternativ för rit hanterare.
 - Hybrid läge
   * Växla mellan klicknings-och FreeHand-metoder som du vill när du ritar en enda rad. Klicka till exempel på några punkter, håll och dra musen för att lägga till en bunt med punkter och klicka sedan på några fler. 

**Slutför ritning**
 - Hybrid/klick läge
   * Dubbelklicka på kartan vid den sista punkten. 
   * Klicka på en knapp i verktygsfältet Rita. 
   * Ange rit läget program mässigt. 
 - FreeHand-läge
   * Släpp mus knappen eller touch-punkten.
 - Tryck på `C` nyckeln.

**Avbryt ritning**
 - Tryck på `Escape` nyckeln.

### <a name="how-to-draw-a-polygon"></a>Så här ritar du en polygon

När ritnings hanteraren är i `draw-polygon` läge kan du utföra följande åtgärder för att rita punkter på kartan beroende på vad interaktions läget är inställt på.

**Starta ritning**
 - Klicka på läge
   * Klicka på den vänstra mus knappen eller tryck på kartan för att lägga till varje punkt i en polygon på kartan. En koordinat läggs till i polygonen för varje klick/touch. 
   * Om musen är över kartan trycker du på `F`-tangenten och en punkt läggs till med den koordinat där mus pekaren är. Detta ger en högre precisions metod för att lägga till en punkt på kartan eftersom det kommer att röra sig om mindre rörelse på musen på grund av att den vänstra mus knappen visas.
   * Fortsätt att klicka tills alla önskade punkter har lagts till i polygonen.
 - FreeHand-läge
   * Tryck på vänster MUSKNAPP eller tryck på kartan och dra musen eller tryck punkten runt. Koordinater läggs till i polygonen när musen eller touch-punkten flyttas runt kartan. När musen eller touch-händelsen utlöses, slutförs ritningen. Observera att den frekvens med vilken koordinater läggs till definieras av `freehandInterval` alternativ för rit hanterare.
 - Hybrid läge
   * Växla mellan klicknings-och FreeHand-metoder som du vill när du ritar en enda polygon. Klicka till exempel på några punkter, håll och dra musen för att lägga till en bunt med punkter och klicka sedan på några fler. 

**Slutför ritning**
 - Hybrid/klick läge
   * Dubbelklicka på kartan vid den sista punkten. 
   * Klicka på den första punkten i polygonen.
   * Klicka på en knapp i verktygsfältet Rita. 
   * Ange rit läget program mässigt. 
 - FreeHand-läge
   * Släpp mus knappen eller touch-punkten.
 - Tryck på `C` nyckeln.

**Avbryt ritning**
 - Tryck på `Escape` nyckeln.

### <a name="how-to-draw-a-rectangle"></a>Så här ritar du en rektangel

När ritnings hanteraren är i `draw-rectangle` läge kan du utföra följande åtgärder för att rita punkter på kartan beroende på vad interaktions läget är inställt på. Den genererade formen följer den [utökade INTERjson-specifikationen för rektanglar](extend-geojson.md#rectangle).

**Starta ritning**
 - Tryck på knappen med vänster MUSKNAPP eller tryck på kartan för att lägga till det första hörnet i rektangeln och dra för att skapa rektangeln. 

**Slutför ritning**
 - Släpp mus knappen eller touch-punkten.
 - Ange rit läget program mässigt. 
 - Tryck på `C` nyckeln.

**Avbryt ritning**
 - Tryck på `Escape` nyckeln.

### <a name="how-to-draw-a-circle"></a>Så här ritar du en cirkel

När ritnings hanteraren är i `draw-circle` läge kan du utföra följande åtgärder för att rita punkter på kartan beroende på vad interaktions läget är inställt på. Den genererade formen följer den [utökade INTERjson-specifikationen för cirklar](extend-geojson.md#circle).

**Starta ritning**
 - Tryck på vänster MUSKNAPP eller tryck på kartan för att lägga till cirkelns centrum och dra ge cirklarna en radie. 

**Slutför ritning**
 - Släpp mus knappen eller touch-punkten.
 - Ange rit läget program mässigt. 
 - Tryck på `C` nyckeln.

**Avbryt ritning**
 - Tryck på `Escape` nyckeln.

## <a name="keyboard-shortcuts"></a>Kortkommandon

Rit verktygen stöder kortkommandon som gör det enklare att rita och redigera former på kartan. Dessa kortkommandon fungerar när kartan är i fokus.

| Nyckel      | Åtgärd                            |
|----------|-----------------------------------|
| `C` | Slutför alla ritningar som pågår och ställer in ritnings läget på inaktiv. Fokus flyttas till ett kart element på översta nivån.  |
| `Escape` | Avbryter alla ritningar som pågår och ställer in ritnings läget på inaktivt. Fokus flyttas till ett kart element på översta nivån.  |
| `F` | Lägger till en koordinat till en punkt, linje eller polygon om musen är över kartan. Motsvarande åtgärd för att klicka på kartan när du är i klick eller hybrid läge. Med den här genvägen kan du få mer exakta och snabbare ritningar eftersom du kan använda en hand för att placera musen och andra för att trycka på knappen utan att musen flyttas från tryck gesten. |

## <a name="next-steps"></a>Nästa steg

Läs mer om klasserna i modulen rit verktyg:

> [!div class="nextstepaction"]
> [Drawing Manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Verktygsfältet Rita](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)