---
title: Lägg till ett popup-fönster med Azure Maps | Microsoft Docs
description: Lägga till ett popup-fönster i Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0f86578e33e5c6a2d6528e2deb1c8068a0c94d01
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42747113"
---
# <a name="add-a-popup-to-the-map"></a>Lägga till ett popup-fönster på kartan

Den här artikeln visar hur du lägger till ett popup-fönster på en karta.  

## <a name="understand-the-code"></a>Förstå koden

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Lägga till ett popup-fönster på en karta' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>lägga till ett popup-fönster på en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket skapar en PIN-kod och lägga till den på kartan. Du kan se [lägga till en PIN-kod på kartan](./map-add-pin.md) anvisningar.

Det tredje kodblocket skapar innehåll som ska visas i ett popup-fönster. Popup-innehållet är HTML-element. 

Det fjärde kodblocket skapar en [popup-fönstret objektet](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) via `new atlas.Popup()`. Popup-fönstret Egenskaper, till exempel innehåll och position är en del av [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest). PopupOptions kan definieras i popup-konstruktorn eller via [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) funktion i klassen popup-fönstret.

Det sista blocket kod använder [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) funktion i klassen kartan för att lyssna efter förklaring händelse på PIN-koder och använder [öppna](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) funktion i popup-klassen för att öppna popup-fönstret om händelsen inträffar.


## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 

* [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup-fönstret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Öppna](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [Stäng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)
    
Flera kodexempel för att lägga till i dina kartor, finns i följande artiklar: 
* [Lägga till en form](./map-add-shape.md)
* [Lägg till anpassade HTML](./map-add-custom-html.md)
