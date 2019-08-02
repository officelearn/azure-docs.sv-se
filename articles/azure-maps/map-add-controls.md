---
title: Lägg till kart kontroller i Azure Maps | Microsoft Docs
description: Så här lägger du till zoomnings kontroll, vinkel kontroll, rotera kontroll och en stil väljare till en karta i Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 7a504b8df199a3a461d5eb4e5b7238462b4c438f
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638766"
---
# <a name="add-map-controls-to-azure-maps"></a>Lägg till kart kontroller i Azure Maps

Den här artikeln visar hur du lägger till kart kontroller till en karta. Du får också lära dig hur du skapar en karta med alla kontroller och en [format väljare](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Lägg till zoomkontrollen

<iframe height='500' scrolling='no' title='Lägga till en zoomnings kontroll' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>att lägga till en zoomnings kontroll</a> med Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kod blocket skapar ett kart objekt med hjälp av mekanismen för anonym autentisering. Se [skapa en karta](./map-create.md) för instruktioner om hur du skapar en karta.

Med zoomnings kontrollen lägger du till möjligheten att zooma in och ut från kartan. Det andra kod blocket skapar ett zoomnings kontroll objekt med Atlas- [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) och lägger till det till kartan med hjälp av Map- [kontrollerna. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) -metoden. Zoomnings kontrollen finns i mappnings **händelsens lyssnare** för att säkerställa att den läses in när kartan har lästs in helt.

## <a name="add-pitch-control"></a>Lägg till vinkel kontroll

<iframe height='500' scrolling='no' title='Lägga till en lutnings kontroll' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>att lägga till en kanna</a> -kontroll<a href='https://codepen.io/azuremaps'>@azuremaps</a>med Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kod blocket skapar ett kart objekt med hjälp av mekanismen för anonym autentisering. Se [skapa en karta](./map-create.md) för instruktioner om hur du skapar en karta.

Med bredd kontrollen kan du ändra kartans lutning. Det andra blocket kod skapar ett objekt för att lägga till ett kontroll objekt med Atlas- [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) och lägger till det till kartan med hjälp av Map- [kontrollerna. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) -metoden. Kontrollen för att kontrol lera om den är **i kartan för** att säkerställa att den läses in när kartan har lästs in helt.

## <a name="add-compass-control"></a>Lägg till kompass kontroll

<iframe height='500' scrolling='no' title='Lägga till en rotations kontroll' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>att lägga till en rotations kontroll</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kod blocket skapar ett kart objekt med hjälp av mekanismen för anonym autentisering. Se [skapa en karta](./map-create.md) för instruktioner om hur du skapar en karta.

Det andra blocket kod skapar ett kompass kontroll objekt med hjälp av [kontrollen Atlas kompass](/javascript/api/azure-maps-control/atlas.control.compasscontrol). Den lägger också till kompass kontrollen till kartan med kartans [kontroller. Lägg till](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metod. Kompass kontrollen är i kartans **händelse lyssnare** för att säkerställa att den läses in när kartan har lästs in helt.

## <a name="a-map-with-all-controls"></a>En karta med alla kontroller

<iframe height='500' scrolling='no' title='En karta med alla kontroller' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>en karta med alla kontroller</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kod blocket skapar ett kart objekt med hjälp av mekanismen för anonym autentisering. Se [skapa en karta](./map-create.md) för instruktioner om hur du skapar en karta.

Det andra kod blocket skapar ett kompass kontroll objekt som använder Atlas- [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) och lägger till det i kartan med hjälp av kartans [kontroller. Lägg till](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metod.

Det tredje blocket kod skapar ett zoomnings kontroll objekt med Atlas- [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) och lägger till det till kartan med hjälp av Map- [kontrollerna. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) -metoden.

Det fjärde kod blocket skapar ett objekt för att lägga till ett kontroll objekt med Atlas- [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) och lägger till det till kartan med kartans [kontroller. Lägg till](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metod.

Det sista blocket kod skapar ett format väljar objekt med hjälp av Atlas- [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) och lägger till det till kartan med kartans [kontroller. Lägg till](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metod. Alla kontroll objekt läggs till i mappnings **händelse lyssnaren** för att säkerställa att de läses in när kartan har lästs in helt.

Ordningen på kontroll objekt i skriptet avgör i vilken ordning de visas på kartan. Om du vill ändra ordningen på kontrollerna på kartan kan du ändra deras ordning i skriptet.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Tamazight](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

I följande artiklar finns fullständig kod:

> [!div class="nextstepaction"]
> [Lägg till en PIN-kod](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägg till en popup](./map-add-popup.md)
