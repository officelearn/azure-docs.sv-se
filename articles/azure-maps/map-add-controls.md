---
title: Lägg till karta kontroller i Azure Maps | Microsoft Docs
description: Hur du lägger till zoomkontrollen, försäljningsargument kontroll, rotera kontroll och en väljare för stil på en karta i Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 781e5d71637a1e86a56dee0aad3c1a5e00f1807a
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885795"
---
# <a name="add-map-controls-to-azure-maps"></a>Lägg till karta kontroller i Azure Maps

Den här artikeln visar hur du lägger till kartan kontroller på en karta. Du också lära dig hur du skapar en karta med alla kontroller och en [style väljare](https://docs.microsoft.com/azure/azure-maps/choose-map-style#adding-the-style-picker).

## <a name="add-zoom-control"></a>Lägg till zoomkontrollen

<iframe height='500' scrolling='no' title='Att lägga till en zoomkontrollen' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>att lägga till en zoomkontrollen</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket anger prenumerationsnyckeln och skapar en Kartobjekt utan att ange förväg format. Se [skapa en karta](./map-create.md) anvisningar om hur du skapar en karta.

Zoomkontrollen lägger till möjligheten att zooma in och ut på kartan. Andra kodblocket skapar ett Zoomkontroll-objekt med hjälp av atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest) och läggs till i kartan med hjälp av kartans [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod. Zoomkontrollen ligger inom kartan **händelselyssnaren** så laddas när kartan har lästs in helt.

## <a name="add-pitch-control"></a>Lägg till försäljningsargument kontroll

<iframe height='500' scrolling='no' title='Lägga till en försäljningsargument-kontroll' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>att lägga till en kontroll för försäljningsargument</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket anger prenumerationsnyckeln och skapar en Kartobjekt utan att ange förväg format. Se [skapa en karta](./map-create.md) anvisningar om hur du skapar en karta.

Försäljningsargument kontroll lägger till möjligheten att ändra lutning på kartan. Andra kodblocket skapar ett försäljningsargument Control-objekt med hjälp av atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest) och läggs till i kartan med hjälp av kartans [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod. Försäljningsargument kontrollen är i kartan **händelselyssnaren** så laddas när kartan har lästs in helt.

## <a name="add-compass-control"></a>Lägg till kompass kontroll

<iframe height='500' scrolling='no' title='Lägga till en rotera kontroll' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>att lägga till en rotera kontroll</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket anger prenumerationsnyckeln och skapar en Kartobjekt utan att ange förväg format. Se [skapa en karta](./map-create.md) anvisningar om hur du skapar en karta.

Andra kodblocket skapar ett kompass Control-objekt med hjälp av atlas [kompass kontroll](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol). Det ger också kompass kontrollen på kartan med hjälp av kartans [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod. Kompass kontrollen är i kartan **händelselyssnaren** så laddas när kartan har lästs in helt.

## <a name="a-map-with-all-controls"></a>En karta med alla kontroller

<iframe height='500' scrolling='no' title='En karta med alla kontroller' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>en karta med alla kontroller</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket anger prenumerationsnyckeln och skapar en Kartobjekt utan att ange förväg format. Se [skapa en karta](./map-create.md) anvisningar om hur du skapar en karta.

Andra kodblocket skapar ett kompass Control-objekt med hjälp av atlas [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol) och läggs till i kartan med hjälp av kartans [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod.

Det tredje kodblocket skapar ett Zoomkontroll-objekt med hjälp av atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest) och läggs till i kartan med hjälp av kartans [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod.

Fjärde kodblocket skapar ett försäljningsargument Control-objekt med hjälp av atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest) och läggs till i kartan med hjälp av kartans [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod.

Det sista blocket kod skapar ett Style väljare-objekt med hjälp av atlas [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) och läggs till i kartan med hjälp av kartans [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod. Alla objekt kontroll har lagts till i kartan **händelselyssnaren** så att de laddar när kartan har lästs in helt.

Ordningen på objekten kontroll i skriptet avgör den ordning som de visas på kartan. Du kan ändra deras inbördes ordning i skriptet om du vill ändra ordningen på kontrollerna på kartan.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

Se följande artiklar för fullständiga koden:

> [!div class="nextstepaction"]
> [Lägg till en PIN-kod](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägg till ett popup-fönster](./map-add-popup.md)