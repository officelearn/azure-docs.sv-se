---
title: Mappa style funktioner i Azure Maps | Microsoft Docs
description: Lär dig mer om Azure Maps style relaterade funktioner.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ffed12b9184c7b6a690c30db9826f031fe6c9f9b
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259796"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Välja en karta i Azure Maps

Azure Maps har fyra olika kartor format att välja bland. Mer information om Koppla format finns i [Koppla format som stöds i Azure Maps](./supported-map-styles.md). Den här artikeln visar hur du använder formatrelaterade-funktioner för att ställa in en stil på kartan belastningen, en ny formatmall och använda väljarkontrollen style.

## <a name="set-style-on-map-load"></a>Ange stil på kartan belastningen

<iframe height='500' scrolling='no' title='Ange formatet på kartan belastningen' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Ange formatet på kartan belastningen</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Kodblocket ovan anger prenumerationsnyckeln och skapar en Kartobjekt med formatet inställd grayscale_dark. Se [skapa en karta](./map-create.md) anvisningar om hur du skapar en karta.

## <a name="update-the-style"></a>Uppdatera format

<iframe height='500' scrolling='no' title='Uppdatera formatmallen' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>uppdatera formatmallen</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Kodblocket ovan anger prenumerationsnyckeln och skapar en Kartobjekt utan att ange förväg format. Se [skapa en karta](./map-create.md) anvisningar om hur du skapar en karta.

Andra kodblocket använder kartans [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metoden för att ange formatmallen karta till satellit.

## <a name="add-the-style-picker"></a>Lägg till style-väljare

<iframe height='500' scrolling='no' title='Att lägga till style-väljare' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>att lägga till style-väljaren</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket i koden ovan anger prenumerationsnyckeln och skapar en Kartobjekt, formatmallen kartan är redan inställd på grayscale_dark. Se [skapa en karta](./map-create.md) anvisningar om hur du skapar en karta.

Andra kodblocket konstruerar en style väljare med hjälp av atlas [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) konstruktor.

En style Väljaren kan style val för kartan. Det tredje kodblocket lägger till style-väljare på kartan med hjälp av kartans [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metod. Style-Väljaren ligger inom kartan **händelselyssnaren** så att den läses in när kartan har lästs in helt.

## <a name="next-steps"></a>Nästa steg

Mer information om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Lägg till kontrollen i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till karta kontroller](./map-add-controls.md)

Lägg till en karta PIN-kod:

> [!div class="nextstepaction"]
> [Lägg till en PIN-kod](./map-add-pin.md)
