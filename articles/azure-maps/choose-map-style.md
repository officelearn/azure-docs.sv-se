---
title: Kart stils funktioner i Azure Maps | Microsoft Docs
description: Lär dig mer om att Azure Maps stil relaterade funktioner.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 52936b14264bd4fe1846ae365e1de447d594b612
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639056"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Välj en kart stil i Azure Maps

Azure Maps har fyra olika typer av kartor att välja mellan. Mer information om kart format finns i [mappnings format som stöds i Azure Maps](./supported-map-styles.md). Den här artikeln visar hur du använder de formatmallar som är relaterade till att ange ett format för kart belastning, ange en ny stil och använda format väljar kontrollen.

## <a name="set-style-on-map-load"></a>Ställ in stil på kart inläsning

<iframe height='500' scrolling='no' title='Ange formatet på kart inläsning' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan som <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>anger formatet på kart inläsning</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I kod blocket ovan anges prenumerations nyckeln och ett kart objekt skapas, med formatet inställt på grayscale_dark. Se [skapa en karta](./map-create.md) för instruktioner om hur du skapar en karta.

## <a name="update-the-style"></a>Uppdatera formatet

<iframe height='500' scrolling='no' title='Uppdaterar formatet' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan som <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>uppdaterar formatet</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Kod blocket ovan anger prenumerations nyckeln och skapar ett kart objekt utan att ange formatet. Se [skapa en karta](./map-create.md) för instruktioner om hur du skapar en karta.

Det andra kod blocket använder kartans [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) -Metod för att ange kart formatet till satellit.

## <a name="add-the-style-picker"></a>Lägg till format väljare

<iframe height='500' scrolling='no' title='Lägga till format väljare' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan genom <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>att lägga till format väljaren</a> per<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kod blocket i ovanstående kod anger prenumerations nyckeln och skapar ett kart objekt. kart formatet är i förväg inställt på grayscale_dark. Se [skapa en karta](./map-create.md) för instruktioner om hur du skapar en karta.

Det andra kod blocket konstruerar en stil väljare med hjälp av Atlas [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) -konstruktorn.

En stil väljare aktiverar val av stil för kartan. Det tredje kod blocket lägger till format väljaren till kartan med kartans [kontroller. Lägg till](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metod. Format väljaren är i kartans **händelse lyssnare** för att säkerställa att den läses in när kartan har lästs in helt.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Lägg till kontroll i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till kart kontroller](./map-add-controls.md)

Lägg till en kart-PIN-kod:

> [!div class="nextstepaction"]
> [Lägg till en PIN-kod](./map-add-pin.md)
