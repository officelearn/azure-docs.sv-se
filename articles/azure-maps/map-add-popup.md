---
title: Lägg till ett popup-fönster med Azure Maps | Microsoft Docs
description: Lägga till ett popup-fönster i Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 09/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 76a7e230491d5e524a1d73437a56d12594cfebe2
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127445"
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

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup-fönstret](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Finns i följande artiklar som är bra för fullständig kodexempel:

> [!div class="nextstepaction"]
> [Lägga till en form](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Lägg till anpassade HTML](./map-add-custom-html.md)
