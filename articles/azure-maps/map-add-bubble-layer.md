---
title: Lägg till ett bubbeldiagram till Azure Maps | Microsoft Docs
description: Så här lägger du till ett bubbel-lager till JavaScript-kartan
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 516e4f35c88ae9c0e2d63e8a4ee40eb57c05ac29
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639036"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Lägg till ett bubbel-lager till en karta

Den här artikeln visar hur du kan återge punkt data från en data källa som ett bubbel lager på en karta. Bubbeldiagram återger punkter som cirklar på kartan med en fast pixel-radie. 

> [!TIP]
> Bubbel lager som standard återger koordinaterna för alla Geometries i en data källa. För att begränsa lagret så att det bara återger punkt geometri funktioner anger `filter` du egenskapen för lagret till `['==', ['geometry-type'], 'Point']` eller `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` om du även vill inkludera Multipoint-funktioner.

## <a name="add-a-bubble-layer"></a>Lägga till ett bubbelskikt

<iframe height='500' scrolling='no' title='BubbleLayer data Källa' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>data källan</a> för pen BubbleLayer genom att<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar det första blocket kod ett kart objekt. Du kan se [skapa en karta](./map-create.md) för instruktioner.

I det andra blocket kod definieras en matris med [punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objekt och läggs till i [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) -objektet.

Ett [bubbeldiagram](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) återger punktbaserade data figursatt i [data källan](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som cirklar på kartan. Det sista blocket med kod skapar ett bubbeldiagram och lägger till det i kartan. Se egenskaper för ett bubbel-lager på [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Matrisen med punkt objekt, data källan och bubbeldiagram skapas och läggs till i kartan i [Event Listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) -funktionen för att se till att cirkeln visas när kartan har lästs in helt.

## <a name="show-labels-with-a-bubble-layer"></a>Visa etiketter med ett bubbel-lager

<iframe height='500' scrolling='no' title='MultiLayer data Källa' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>data källan</a> för pen MultiLayer genom att<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Koden ovan visar hur du visualiserar och etiketterar data på kartan. Det första kod blocket ovan skapar ett kart objekt. Du kan se [skapa en karta](./map-create.md) för instruktioner.

Det andra blocket kod skapar ett [punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) objekt. Det skapar sedan ett data käll objekt med klassen [data källa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) och lägger till punkten i data källan.

Ett [bubbeldiagram](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) återger punktbaserade data figursatt i [data källan](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som cirklar på kartan. Det tredje blocket med kod skapar ett bubbeldiagram och lägger till det i kartan. Se egenskaper för ett bubbel-lager på [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Ett [symbol lager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) använder text eller ikoner för att återge punktbaserade data i [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) som symboler på kartan. Det sista blocket med kod skapar och lägger till ett symbol lager till kartan som återger text etiketten för bubblan. Se egenskaperna för ett symbol lager på [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Data källan och lagren skapas och läggs till i kartan i [Event Listener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) -funktionen för att säkerställa att data visas efter att kartan har lästs in helt.

## <a name="customize-a-bubble-layer"></a>Anpassa ett bubbel-lager

Bubble-lagret har bara några format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för bubbel Layer' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>alternativen</a> för rit skiktet Ritstift efter Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till ett symbol lager](map-add-pin.md)

> [!div class="nextstepaction"]
> [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)