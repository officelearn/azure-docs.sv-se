---
title: Lägg till karta kontroller i Azure Maps | Microsoft Docs
description: Hur du lägger till zoomkontrollen, försäljningsargument kontroll, rotera kontroll och en väljare för stil på en karta i Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 08/29/2018
ms.topic: how-to-guides
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 435c6545b69b4457c3e1035fb8125399d4c9c23a
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666139"
---
# <a name="add-map-controls-to-azure-maps"></a>Lägg till karta kontroller i Azure Maps

Den här artikeln visar hur du lägger till kartan kontroller på en karta. Du också lära dig hur du skapar en karta med alla kontroller och en [style väljare](https://docs.microsoft.com/azure/azure-maps/choose-map-style#adding-the-style-picker).

## <a name="add-zoom-control"></a>Lägg till zoomkontrollen

<iframe height='500' scrolling='no' title='Att lägga till en zoomkontrollen' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>att lägga till en zoomkontrollen</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket i koden ovan skapar en karta-objekt. Se [skapa en karta](./map-create.md) anvisningar om hur du skapar en karta.

Andra kodblocket skapar ett objekt för kontroll av zoomning som med hjälp av atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest).

Zoomkontrollen lägger till möjligheten att zooma in och ut på kartan. Det tredje blocket lägger till zoomkontrollen på kartan med hjälp av kartans [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod.

## <a name="add-pitch-control"></a>Lägg till försäljningsargument kontroll

<iframe height='500' scrolling='no' title='Lägga till en försäljningsargument-kontroll' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>att lägga till en kontroll för försäljningsargument</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första blocket av koden ovan skapar en Kartobjekt med formatmallen inställd gråskala. Se [skapa en karta](./map-create.md) anvisningar om hur du skapar en karta.

Andra kodblocket skapar ett objekt för kontroll av försäljningsargument med hjälp av atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest).

Försäljningsargument kontroll lägger till möjligheten att ändra lutning på kartan. Det tredje blocket lägger till försäljningsargument kontroll på kartan med hjälp av kartans [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod.

## <a name="add-compass-control"></a>Lägg till kompass kontroll

<iframe height='500' scrolling='no' title='Lägga till en rotera kontroll' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>att lägga till en rotera kontroll</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket i koden ovan skapar en karta-objekt. Se [skapa en karta](./map-create.md) anvisningar om hur du skapar en karta.

Andra kodblocket skapar ett kompass control-objekt med hjälp av atlas [kompass kontroll](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol). Det ger också kompass kontrollen på kartan med hjälp av kartans [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod.

## <a name="a-map-with-all-controls"></a>En karta med alla kontroller

<iframe height='500' scrolling='no' title='En karta med alla kontroller' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>en karta med alla kontroller</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket i koden ovan skapar en karta-objekt. Se [skapa en karta](./map-create.md) anvisningar om hur du skapar en karta.

Andra kodblocket skapar ett kompass control-objekt med hjälp av atlas [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol) och läggs till i kartan med hjälp av kartans [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod.

Tredje kodblocket skapar ett objekt för kontroll av zoomning som med hjälp av atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest) och läggs till i kartan med hjälp av kartans [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod.

Fjärde kodblocket skapar ett objekt för kontroll av försäljningsargument med hjälp av atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest) och läggs till i kartan med hjälp av kartans [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod.

Det sista blocket kod lägger till ett format väljare-objekt på kartan genom att använda atlas [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) och läggs till i kartan med hjälp av kartans [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln: 
* [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol)

* [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)
    * [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol)
    * [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest)
    * [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest)
    * [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol)
    
Flera kodexempel för att lägga till i dina kartor, finns i följande artiklar: 
* [Lägg till en PIN-kod](./map-add-pin.md)
* [Lägg till ett popup-fönster](./map-add-popup.md)