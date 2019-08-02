---
title: Hantera mus händelser med Azure Maps | Microsoft Docs
description: Så här gör du en interaktiv JavaScript-mappning med kart händelser
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: da6b183155de0fbc370751254a6842343d280874
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638966"
---
# <a name="interact-with-the-map---mouse-events"></a>Interagera med kart mus händelser

Den här artikeln visar hur du använder egenskapen [Mappa klass](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) [händelser](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) för att markera händelser på kartan och på olika lager i kartan. Det visar också hur du använder egenskapen mappa klass händelser för att markera händelser när du interagerar med en HTML-markör.

## <a name="interact-with-the-map"></a>Interagera med kartan

<iframe height='600' scrolling='no' title='Interagera med kartan – mus händelser' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan interagerar <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>med kartan – mus händelser</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Spela upp med kartan ovan och se motsvarande mus händelser markerade till höger. Du kan klicka på **fliken JS** för att visa och redigera JavaScript-koden. Du kan också klicka på knappen **Redigera på CodePen** och redigera koden på CodePen.

## <a name="interact-with-map-layers"></a>Interagera med kart skikt

<iframe height='600' scrolling='no' title='Interagera med karta – skikt händelser' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan som <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interagerar med kartan – skikt händelser</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Koden ovan markerar namnet på de händelser som visas när du interagerar med symbol lagret. Symbol-, bubbeldiagram-, linje-och polygon-lagret stöder samma uppsättning händelser. Panel lagret stöder inte någon av dessa händelser.

## <a name="interact-with-html-marker"></a>Interagera med HTML-markör

<iframe height='500' scrolling='no' title='Interagera med händelser för kart-HTML-markör' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan som <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>samverkar med kart-HTML-markörens händelser</a> genom Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Koden ovan lägger till Java Script Map-händelser till en HTML-markör. Den markerar även namnet på de händelser som visas när du interagerar med HTML-markören.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Mappa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

I följande artiklar finns fullständiga kod exempel:

> [!div class="nextstepaction"]
> [Använda modulen Azure Maps tjänster](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Kod exempel sida](https://aka.ms/AzureMapsSamples)
