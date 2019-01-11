---
title: Lägga till en form med Azure Maps | Microsoft Docs
description: Lägga till en figur i en Javascript-karta
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 583223ccfd30468c047fc77c3b6598ce7b0a1973
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214614"
---
# <a name="add-a-shape-to-a-map"></a>Lägga till formen på en karta

Den här artikeln visar hur du lägger till en [form](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) till kartan och uppdatera egenskaperna för en befintlig form på kartan.

<a id="addALine"></a>

## <a name="add-a-line"></a>Lägg till en rad

<iframe height='500' scrolling='no' title='Lägg till en rad i en karta' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qomaKv/'>till en rad i en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket i koden ovan skapar en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, ett datakällobjekt skapas med hjälp av den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klass. En [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) objektet skapas och läggs till datakällan.

En [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderingar rad objekt och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Senaste kodblocket skapar och lägger till en linjeskikt på kartan. Se egenskaperna för en linjeskikt på [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Datakällan och linjeskikt skapas och läggs till kartan inom den [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktion för att tillse att raden visas när kartan har lästs in helt.

## <a name="customize-a-line-layer"></a>Anpassa en linjeskikt

Raden skiktet flera alternativ för formatering. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Layer Linjealternativ' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Layer kommandoradsalternativ</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Lägga till en cirkel

<iframe height='500' scrolling='no' title='Lägga till en cirkel till en karta' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>lägga till en cirkel till en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket i koden ovan skapar en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, ett datakällobjekt skapas med hjälp av den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klass. En cirkel är en [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) av [punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) och har en `subType` -egenskapen inställd på ”cirkel” och en `radius` egenskapen värdet taxor. När en återställningspunkt-funktion med en undertyp till cirkel läggs till en datakälla, konverterar den till en cirkulär polygon i kartan.

En [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) återger data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) på kartan. Senaste kodblocket skapar och lägger till en polygonskikt på kartan. Se egenskaperna för en polygonskikt på [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Datakällan och polygonskikt skapas och läggs till kartan inom den [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktion för att tillse att cirkeln visas när kartan har lästs in helt.

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Lägga till en polygon

Det finns två olika sätt som du kan lägga till en polygon på kartan. Båda beskrivs i följande exempel.

### <a name="use-polygon-layer"></a>Använda polygonskikt 

<iframe height='500' scrolling='no' title='Lägga till en polygon på en karta ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>lägga till en polygon på en karta </a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, ett datakällobjekt skapas med hjälp av den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klass. En [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) skapas från en matris med koordinater och läggs till datakällan. 

En [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) återger data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) på kartan. Senaste kodblocket skapar och lägger till en polygonskikt på kartan. Se egenskaperna för en polygonskikt på [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Datakällan och polygonskikt skapas och läggs till kartan inom den [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktion för att tillse att polygonen visas när kartan har lästs in helt.

### <a name="use-polygon-and-line-layer"></a>Använd polygon- och lager

<iframe height='500' scrolling='no' title='Polygon- och -lagret för att lägga till polygon' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon- och -lagret för att lägga till polygon</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, ett datakällobjekt skapas med hjälp av den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klass. En [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) skapas från en matris med koordinater och läggs till datakällan. 

En [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) återger data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) på kartan. Se egenskaperna för en polygonskikt på [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). En [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) är en matris med rader. Se egenskaperna för en linjeskikt på [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Det tredje kodblocket skapar polygon- och linjeskikt.

Det sista blocket kod lägger till polygon- och linjeskikt på kartan. Datakällan och lagren skapas och läggs till kartan inom den [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktion för att tillse att polygonen visas när kartan har lästs in helt.

## <a name="customize-a-polygon-layer"></a>Anpassa en polygonskikt

Polygonskikt har bara ett par alternativ för formatering. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-a-shape"></a>Uppdatera en form

Klassen Shape omsluter en [geometri](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) eller [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) och gör det enkelt att uppdatera och underhålla dem.
`new Shape(data: Feature<data.Geometry, any>)` skapar ett formobjekt och initierar den med den angivna funktionen.

<br/>

<iframe height='500' scrolling='no' title='Uppdatera formegenskaper' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Uppdatera formegenskaper</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första blocket av koden ovan skapar en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

En återställningspunkt är en [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) av [peka](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) klassen. Andra kodblocket initierar radievärde för skjutreglaget HTML-element och sedan skapar och omsluter ett point-objekt i en [form](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) sms_sci_roamingboundary.

Det tredje kodblocket skapar en funktion som tar värdet från HTML-element intervallet skjutreglaget och ändrar värdet för radius med hjälp av klassen shape [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#addproperty) metod.

I det fjärde kodblocket, ett datakällobjekt skapas med hjälp av den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klass. Punkten läggs sedan till datakällan.

En [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) återger data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) på kartan. Det tredje kodblocket skapar en polygonskikt. Se egenskaperna för en polygonskikt på [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Datakällan och händelselyssnaren klickar du på polygonskikt skapas och läggs till kartan inom den [händelselyssnaren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funktion för att tillse att punkten visas när kartan har lästs in helt.

## <a name="next-steps"></a>Nästa steg

Flera kodexempel för att lägga till i dina kartor, finns i följande artiklar:

> [!div class="nextstepaction"]
> [Lägg till anpassade HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Visa sökresultat](./map-search-location.md)
