---
title: Hantera mus händelser med Azure Maps | Microsoft Docs
description: Så här skapar du en interaktiv Web SDK-mappning med kart händelser
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 37a3fc3178fe5caeacedfd355a6065ee189a5890
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976532"
---
# <a name="interact-with-the-map---mouse-events"></a>Interagera med kart mus händelser

Den här artikeln visar hur du använder egenskapen [Mappa klass händelser](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) för att markera händelser på kartan och på olika lager i kartan. Det visar också hur du använder egenskapen mappa klass händelser för att markera händelser när du interagerar med en HTML-markör.

## <a name="interact-with-the-map"></a>Interagera med kartan

Spela upp med kartan nedan och se motsvarande mus händelser markerade till höger. Du kan klicka på **fliken JS** för att visa och redigera JavaScript-koden. Du kan också klicka på knappen **Redigera på CodePen** och redigera koden på CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interagera med kartan – mus händelser' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan interagerar <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>med kartan – mus händelser</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interagera med kart skikt

I följande kod visas namnet på de händelser som utlöses när du interagerar med symbol lagret. Symbol-, bubbeldiagram-, linje-och polygon-lagret stöder samma uppsättning händelser. Värme kartan och panel lagren stöder inte någon av dessa händelser.

<br/>

<iframe height='600' scrolling='no' title='Interagera med karta – skikt händelser' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan som <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interagerar med kartan – skikt händelser</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interagera med HTML-markör

Följande kod lägger till Java Script Map-händelser till en HTML-markör. Den markerar även namnet på de händelser som visas när du interagerar med HTML-markören.

<br/>

<iframe height='500' scrolling='no' title='Interagera med händelser för kart-HTML-markör' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan som <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>samverkar med kart-HTML-markörens händelser</a> genom Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

I följande artiklar finns fullständiga kod exempel:

> [!div class="nextstepaction"]
> [Använda modulen Azure Maps tjänster](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)
