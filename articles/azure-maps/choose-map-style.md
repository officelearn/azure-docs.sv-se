---
title: Mappa style funktioner i Azure Maps | Microsoft Docs
description: Lär dig mer om Azure Maps style relaterade funktioner.
author: walsehgal
ms.author: v-musehg
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 73ea3c7c3cbb10beedb7ff64c47ebdbd908bd522
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123512"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Välja en karta i Azure Maps

Azure Maps har fyra olika kartor format att välja bland. Mer information om Koppla format finns i [Koppla format som stöds i Azure Maps](./supported-map-styles.md). Den här artikeln visar hur du använder formatrelaterade-funktioner för att ställa in en stil på kartan belastningen, en ny formatmall och använda väljarkontrollen style.

## <a name="set-style-on-map-load"></a>Ange stil på kartan belastningen

<iframe height='500' scrolling='no' title='Ange formatet på kartan belastningen' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Ange formatet på kartan belastningen</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Koden ovan skapar en Kartobjekt med formatet inställd gråskala. Se [skapa en karta](./map-create.md) anvisningar om hur du skapar en karta.

## <a name="update-the-style"></a>Uppdatera format

<iframe height='500' scrolling='no' title='Uppdatera formatmallen' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>uppdatera formatmallen</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Första blockeringen av kod i koden ovan skapar en Kartobjekt utan att ange formatet före. Se [skapa en karta](./map-create.md) anvisningar om hur du skapar en karta.

Andra kod klockan ska använda på kartan [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle) metoden för att ange formatmallen karta till satellit.

## <a name="add-the-style-picker"></a>Lägg till style-väljare

<iframe height='500' scrolling='no' title='Att lägga till style-väljare' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>att lägga till style-väljaren</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Första kodblock i koden ovan skapar en Kartobjekt utan att ange formatet före. Se [skapa en karta](./map-create.md) anvisningar om hur du skapar en karta.

Andra kodblocket konstruerar en style väljare med hjälp av atlas [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) konstruktor.

En style Väljaren kan style val för kartan. Det tredje kodblocket lägger till style-väljare på kartan med hjälp av kartans [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metod.

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
