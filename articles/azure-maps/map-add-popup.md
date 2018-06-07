---
title: Lägg till ett popup-fönster med Azure Maps | Microsoft Docs
description: Hur du lägger till ett popup-fönster till Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: d9eeac28b204af0bb7d8e204762aae854055b33a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599311"
---
# <a name="add-a-popup-to-the-map"></a>Lägg till ett popup-fönster på kartan

Den här artikeln visar hur du lägger till ett popup-fönster till en karta.  

## <a name="understand-the-code"></a>Förstå koden

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Lägg till ett popup-fönster till en karta' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>lägga till ett popup-fönster till en karta</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Första kodblocket skapar ett Kartobjekt i koden ovan. Du kan se [skapa en karta](./map-create.md) anvisningar.

Andra kodblocket skapar en PIN-kod och lägga till den i kartan. Du kan se [Lägg till en PIN-kod på kartan](./map-add-pin.md) anvisningar.

Det tredje kodblocket skapar innehållet som ska visas i ett popup-fönster. Popup-innehållet är HTML-element. 

Det fjärde kodblocket skapar en [popup-objektet](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) via `new atlas.Popup()`. Popup-egenskaper, till exempel innehåll och position är en del av [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popupoptions?view=azure-iot-typescript-latest). PopupOptions kan definieras i popup-konstruktor eller via [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) funktion i popup-klassen.

Senaste kodblocket använder [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener) funktion i kartan klassen för att lyssna efter förklaring händelse på PIN-koder och använder [öppna](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open) funktion i popup-klassen för att öppna popup-fönstret om händelsen inträffar.


## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 

* [karta](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup-fönster](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Öppna](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Stäng](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)
    
Att lägga till i din maps mer kodexempel finns i följande artiklar: 
* [Lägga till en form](./map-add-shape.md)
* [Lägg till anpassade HTML](./map-add-custom-html.md)
