---
title: Lägga till en form med Azure Maps | Microsoft Docs
description: Lägga till en figur i en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b1fe17adc80fc7f93f1511d577b1dc363e36e2e3
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746009"
---
# <a name="add-a-shape-to-a-map"></a>Lägga till formen på en karta

Den här artikeln visar hur du lägger till en rad, en cirkel och en polygon på kartan. 

<a id="addALine"></a>

## <a name="add-a-line"></a>Lägg till en rad

<iframe height='500' scrolling='no' title='Lägg till en rad i en karta' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qomaKv/'>till en rad i en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket skapas en rad. En rad är en [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.atlas.data.feature?view=azure-iot-typescript-latest) av LineString med LineStringProperties som egenskapen funktionen. Använd `new atlas.data.Feature(new atlas.data.LineString())` att skapa en rad och definiera dess egenskaper. 

En linjeskikt är en matris med rader. Det sista blocket kod använder [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) funktion i kartan klassen för att lägga till raden skiktet på kartan och definiera egenskaperna för linjeskikt. Se egenskaperna för en linjeskikt på [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.linestringlayeroptions?view=azure-iot-typescript-latest).

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Lägga till en cirkel

<iframe height='500' scrolling='no' title='Lägga till en cirkel till en karta' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>lägga till en cirkel till en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket skapas en tom cirkel. En cirkel är en [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.atlas.data.feature?view=azure-iot-typescript-latest) av [punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.atlas.data.point?view=azure-iot-typescript-latest) med [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/modelscircleproperties?view=azure-iot-typescript-latest) som egenskapen funktionen. Använd `new atlas.data.Feature(new atlas.data.Point())` att skapa en cirkel och definiera dess egenskaper.

Ett cirkel-lager är en matris med cirklar. Det sista blocket kod använder [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcircles) funktion i kartan klassen för att lägga till cirkel skiktet på kartan och definiera egenskaperna för cirkel-lagret. Se egenskaperna för ett cirkel-lager i [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.circlelayeroptions?view=azure-iot-typescript-latest).

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Lägga till en polygon
<iframe height='500' scrolling='no' title='Lägga till en polygon på en karta ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>lägga till en polygon på en karta </a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket skapas en polygon. En polygon har en [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.atlas.data.feature?view=azure-iot-typescript-latest) av [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.atlas.data.polygon?view=azure-iot-typescript-latest) med [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.atlas.data.polygonproperties?view=azure-iot-typescript-latest) som egenskapen funktionen. Använd `new atlas.data.Feature(new atlas.data.Polygon())` att skapa en polygon och definiera dess egenskaper. Ange sorterad koordinaterna för polygon-sökvägen i konstruktorn polygon.

En polygonskikt är en matris med polygoner. Det sista blocket kod använder [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpolygons) funktion i kartan klassen för att lägga till polygonskikt på kartan och definiera dess egenskaper. Se egenskaperna för en polygonskikt på [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>Nästa steg
Flera kodexempel för att lägga till i dina kartor, finns i följande artiklar:
* [Lägg till anpassade HTML](./map-add-custom-html.md)
* [Visa sökresultat](./map-search-location.md)


