---
title: Lägga till en form med Azure Maps | Microsoft Docs
description: Hur du lägger till en form en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: dec9b7289927365faa9c58522df2571db99f0494
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599379"
---
# <a name="add-a-shape-to-a-map"></a>Lägga till en form till en karta

Den här artikeln visar hur du lägger till en rad, en cirkel och en polygon på kartan. 

<a id="addALine"></a>

## <a name="add-a-line"></a>Lägga till en linje

<iframe height='500' scrolling='no' title='Lägga till en rad i en karta' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qomaKv/'>till en rad i en karta</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Första kodblocket skapar ett Kartobjekt i koden ovan. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket skapas en rad. En rad är en [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) av LineString med LineStringProperties som egenskapen funktionen. Använd `new atlas.data.Feature(new atlas.data.LineString())` att skapa en rad och dess egenskaper. 

En rad-lagret är en matris med rader. Senaste kodblocket använder [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings) funktion i klassen kartan att lägga till raden lagret kartan och definiera egenskaperna för rad-lagret. Se egenskaperna för en rad lager vid [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/linestringlayeroptions?view=azure-iot-typescript-latest).

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Lägga till en cirkel

<iframe height='500' scrolling='no' title='Lägga till en cirkel till en karta' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>lägga till en cirkel till en karta</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Första kodblocket skapar ett Kartobjekt i koden ovan. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket skapas en cirkel. En cirkel är en [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) av [punkt](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest) med [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circleproperties?view=azure-iot-typescript-latest) som egenskapen funktionen. Använd `new atlas.data.Feature(new atlas.data.Point())` att skapa en cirkel och dess egenskaper.

Ett cirkel-lager är en matris med cirklar. Senaste kodblocket använder [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addcircles) funktion i kartan klassen för att lägga till cirkel-lagret på kartan och definiera egenskaperna för cirkel-lagret. Se egenskaperna för ett cirkel-lager vid [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circlelayeroptions?view=azure-iot-typescript-latest).

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Lägga till en polygon
<iframe height='500' scrolling='no' title='Lägga till en polygon till en karta ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>lägga till en polygon till en karta </a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Första kodblocket skapar ett Kartobjekt i koden ovan. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket skapas en polygon. En polygon har en [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) av [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygon?view=azure-iot-typescript-latest) med [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonproperties?view=azure-iot-typescript-latest) som egenskapen funktionen. Använd `new atlas.data.Feature(new atlas.data.Polygon())` att skapa en polygon och dess egenskaper. Ange beställda koordinaterna för polygon sökvägen i konstruktorn polygon.

En polygon lagret är en matris med polygoner. Senaste kodblocket använder [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpolygons) funktion i kartan klassen för att lägga till lagret polygon på kartan och definiera dess egenskaper. Se egenskaperna för en polygon lager vid [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>Nästa steg
Att lägga till i din maps mer kodexempel finns i följande artiklar:
* [Lägg till anpassade HTML](./map-add-custom-html.md)
* [Visa sökresultat](./map-search-location.md)


