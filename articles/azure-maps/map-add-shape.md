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
ms.openlocfilehash: f61c7a939902ee5d02b2e9ba896c7555968f9d0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769523"
---
# <a name="add-a-shape-to-a-map"></a>Lägga till formen på en karta

Den här artikeln visar hur du kan visa geometrier på kartan med rad och polygon lager. Azure Maps Web SDK stöder också skapandet av cirkel geometrier enligt definitionen i den [utökat GeoJSON-schema](extend-geojson.md#circle). Alla funktionen geometrier kan också enkelt uppdatera omslutna av den [form](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) klass.

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Lägg till rader på kartan

`LineString` och `MultiLineString` funktioner används för att representera sökvägar och beskrivs på kartan.

### <a name="add-a-line"></a>Lägg till en rad

<iframe height='500' scrolling='no' title='Lägg till en rad i en karta' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qomaKv/'>till en rad i en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket i koden ovan skapar en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, ett datakällobjekt skapas med hjälp av den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klass. En [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) objektet skapas och läggs till datakällan.

En [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderingar rad objekt och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Senaste kodblocket skapar och lägger till en linjeskikt på kartan. Se egenskaperna för en linjeskikt på [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Datakällan och linjeskikt skapas och läggs till kartan inom den [händelsehanteraren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) så att raden visas när kartan har lästs in helt.

### <a name="add-symbols-along-a-line"></a>Lägg till symboler längs en linje

Detta exempel visar hur du lägger till ikoner längs en linje på kartan. När du använder en symbol-lagret, anger du alternativet ”placering” till ”rad”, detta rendera symboler på samma rad och rotera ikonerna (0 grader = höger).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Visa pilen längs linje" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Show-pil längst rad</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="line-stroke-gradient"></a> Lägg till en linje övertoning till en linje

Förutom att använda en enda linjefärg på en rad kan du också fylla en rad med en Färgtoning att visa övergången från en linjesegment till nästa. Exempelvis kan rad toningar användas för att representera ändringar över tid och avståndet eller annan temperaturer mellan en anslutna objekt. För att tillämpa den här funktionen på en rad, datakällan måste ha den `lineMetrics` alternativuppsättning till true och ett uttryck för toning kan sedan skickas till den `strokeColor` alternativet på raden. Tonad linje-uttrycket måste referens i `['line-progress']` data-uttryck som visar beräknade rad-mått i uttrycket.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linje med linje toning" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>raden med linje toning</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-line-layer"></a>Anpassa en linjeskikt

Raden skiktet flera alternativ för formatering. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Layer Linjealternativ' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Layer kommandoradsalternativ</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>Lägga till en polygon på kartan

`Polygon` och `MultiPolygon` funktioner används ofta för att representera ett område på en karta. 

### <a name="use-a-polygon-layer"></a>Använd en polygonskikt 

En polygonskikt återger området i en polygon. 

<iframe height='500' scrolling='no' title='Lägga till en polygon på en karta ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>lägga till en polygon på en karta </a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, ett datakällobjekt skapas med hjälp av den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klass. En [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) skapas från en matris med koordinater och läggs till datakällan. 

En [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) återger data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) på kartan. Senaste kodblocket skapar och lägger till en polygonskikt på kartan. Se egenskaperna för en polygonskikt på [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Datakällan och polygonskikt skapas och läggs till kartan inom den [händelsehanteraren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) så att polygonen visas när kartan har lästs in helt.

### <a name="use-a-polygon-and-line-layer-together"></a>Använda en polygon- och layer tillsammans

Ett rad-lager kan användas för att återge konturerna för en polygon. 

<iframe height='500' scrolling='no' title='Polygon- och -lagret för att lägga till polygon' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polygon- och -lagret för att lägga till polygon</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar första kodblocket en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, ett datakällobjekt skapas med hjälp av den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klass. En [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) skapas från en matris med koordinater och läggs till datakällan. 

En [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) återger data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) på kartan. Se egenskaperna för en polygonskikt på [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). En [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) är en matris med rader. Se egenskaperna för en linjeskikt på [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Det tredje kodblocket skapar polygon- och linjeskikt.

Det sista blocket kod lägger till polygon- och linjeskikt på kartan. Datakällan och lagren skapas och läggs till kartan inom den [händelsehanteraren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) så att polygonen visas när kartan har lästs in helt.

> [!TIP]
> Rad lager som standard renderas koordinaterna för polygoner som rader i en datakälla. Funktioner för att begränsa lagret så att den återger endast LineString set den `filter` tillhör skiktet till `['==', ['geometry-type'], 'LineString']` eller `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` om du vill inkludera MultiLineString funktioner också.

### <a name="fill-a-polygon-with-a-pattern"></a>Fylla en polygon med ett mönster

Förutom att fylla en polygon med en färg användas även ett mönster för avbildningen. Läsa in en bild-mönstret i maps sprite bildresurser och sedan referera till den här avbildningen med den `fillPattern` egenskapen skiktets polygon.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fyllningsmönstret för polygon" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Polygon fyllningsmönstret</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-polygon-layer"></a>Anpassa en polygonskikt

Polygonskikt har bara ett par alternativ för formatering. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Lägga till en cirkel på kartan

Azure Maps använder en utökad version av det GeoJSON-schema som innehåller en definition för cirklar enligt [här](extend-geojson.md#circle). En cirkel kan återges på kartan genom att skapa en `Point` funktion som har en `subType` egenskapen med värdet `"Circle"` och en `radius` egenskap som har ett tal som representerar radien i meter. Exempel:

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Azure Maps Web SDK konverterar dessa `Pooint` funktioner i `Polygon` funktioner under försättsbladen och kan återges på kartan med polygon- och lager som visas här.

<iframe height='500' scrolling='no' title='Lägga till en cirkel till en karta' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>lägga till en cirkel till en karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kodblocket i koden ovan skapar en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

I det andra kodblocket, ett datakällobjekt skapas med hjälp av den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klass. En cirkel är en [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) av [punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) och har en `subType` egenskapen `"Circle"` och en `radius` egenskapsvärdet i mätare. När en återställningspunkt-funktion med en `subType` av `"Circle"` har lagts till i en datakälla, den konverteras till en cirkulär polygon i kartan.

En [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) återger data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) på kartan. Senaste kodblocket skapar och lägger till en polygonskikt på kartan. Se egenskaperna för en polygonskikt på [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Datakällan och polygonskikt skapas och läggs till kartan inom den [händelsehanteraren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) så att cirkeln visas när kartan har lästs in helt.

## <a name="make-a-geometry-easy-to-update"></a>Gör det enkelt att uppdatera en geometri

En `Shape` klassen radbryter en [geometri](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) eller [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) och gör det enkelt att uppdatera och underhålla dem.
`new Shape(data: Feature<data.Geometry, any>)` skapar ett formobjekt och initierar den med den angivna funktionen.

<br/>

<iframe height='500' scrolling='no' title='Uppdatera formegenskaper' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Uppdatera formegenskaper</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första blocket av koden ovan skapar en Kartobjekt. Du kan se [skapa en karta](./map-create.md) anvisningar.

En återställningspunkt är en [funktionen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) av [peka](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) klassen. Andra kodblocket initierar radievärde för skjutreglaget HTML-element och sedan skapar och omsluter ett point-objekt i en [form](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) sms_sci_roamingboundary.

Det tredje kodblocket skapar en funktion som tar värdet från HTML-element intervallet skjutreglaget och ändrar värdet för radius med hjälp av klassen shape [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) metod.

I det fjärde kodblocket, ett datakällobjekt skapas med hjälp av den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klass. Punkten läggs sedan till datakällan.

En [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) återger data och är inneslutna i den [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) på kartan. Det tredje kodblocket skapar en polygonskikt. Se egenskaperna för en polygonskikt på [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Datakällan och händelsehanteraren klickar du på polygonskikt skapas och läggs till kartan inom den [händelsehanteraren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) så att punkten visas när kartan har lästs in helt.

## <a name="next-steps"></a>Nästa steg

Flera kodexempel för att lägga till i dina kartor, finns i följande artiklar:

> [!div class="nextstepaction"]
> [Använda datadrivna style uttryck](data-driven-style-expressions-web-sdk.md)
