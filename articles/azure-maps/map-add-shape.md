---
title: Lägg till en form med Azure Maps | Microsoft Docs
description: Så här lägger du till en form i en JavaScript-mappning
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0696eba4f3cca7beedc2efcda0182ab82b3d69d9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638707"
---
# <a name="add-a-shape-to-a-map"></a>Lägga till en form till en karta

Den här artikeln visar hur du återger Geometries på kartan med hjälp av linje-och polygon lager. Azure Maps Web SDK stöder även skapande av cirkel-Geometries som definierats i det [utökade INTERjson-schemat](extend-geojson.md#circle). Alla funktioner Geometries kan också enkelt uppdateras om de omsluts med klassen [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Lägg till rader till kartan

`LineString`och `MultiLineString` -funktioner används för att representera sökvägar och disstreck på kartan.

### <a name="add-a-line"></a>Lägg till en rad

<iframe height='500' scrolling='no' title='Lägga till en rad i en karta' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Lägg till en linje till en karta</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kod blocket i koden ovan skapar ett kart objekt. Du kan se [skapa en karta](./map-create.md) för instruktioner.

I det andra kodblock skapas ett data käll objekt med hjälp av klassen [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Ett [lin Est ring](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) -objekt skapas och läggs till i data källan.

En [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) återger rad objekt som har omslutits i [data källan](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Det sista blocket med kod skapar och lägger till ett linje lager till kartan. Se egenskaperna för ett linje lager på [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Data källan och rad skiktet skapas och läggs till i kartan i [händelse hanteraren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) för att säkerställa att linjen visas när kartan har lästs in helt.

### <a name="add-symbols-along-a-line"></a>Lägg till symboler längs en linje

Det här exemplet visar hur du lägger till pil-ikoner längs en linje på kartan. När du använder ett symbol lager ställer du in alternativet "placering" på "linje". då återges symbolerna längs linjen och ikonerna roteras (0 grader = höger).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Visa pil längs linje" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pilen för Penn <a href='https://codepen.io/azuremaps/pen/drBJwX/'>Visa utmed rad</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="line-stroke-gradient"></a>Lägg till en linje toning till en linje

Förutom att kunna använda en enda linje färg på en linje kan du också fylla en linje med en toning av färger som visar över gången från ett linje segment till nästa. Till exempel kan rad övertoningar användas för att representera ändringar över tid och avstånd, eller olika temperaturer över en ansluten linje med objekt. För att kunna använda den här funktionen på en rad måste data källan ha `lineMetrics` alternativet inställt på sant och ett färg tonings uttryck kan skickas `strokeColor` till alternativet på raden. Uttrycket linje toning måste referera till det `['line-progress']` data uttryck som visar de beräknade linje måtten för uttrycket.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linje med Penseldrags toning" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>linjen med penseldrags toning</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-line-layer"></a>Anpassa ett linje lager

Linje skiktet har flera format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för linje skikt' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för Penn <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>linje skiktet</a> efter Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>Lägg till en polygon till kartan

`Polygon`och `MultiPolygon` -funktioner används ofta för att representera ett område på en karta. 

### <a name="use-a-polygon-layer"></a>Använda ett polygon-lager 

Ett polygon-lager återger ytan på en polygon. 

<iframe height='500' scrolling='no' title='Lägg till en polygon till en karta ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Lägg till en polygon till en karta</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I koden ovan skapar det första blocket kod ett kart objekt. Du kan se [skapa en karta](./map-create.md) för instruktioner.

I det andra kodblock skapas ett data käll objekt med hjälp av klassen [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . En [polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) skapas från en matris med koordinater och läggs till i data källan. 

En [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) återger data som radbryts i data [källan](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) på kartan. Det sista blocket med kod skapar och lägger till ett polygon-lager till kartan. Se egenskaper för ett polygon-lager på [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Data källan och polygon-lagret skapas och läggs till i kartan i [händelse hanteraren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) för att säkerställa att polygonen visas när kartan har lästs in helt.

### <a name="use-a-polygon-and-line-layer-together"></a>Använda ett polygon-och linje lager tillsammans

Ett linje lager kan användas för att återge konturen för en polygon. 

<iframe height='500' scrolling='no' title='Polygon och linje skikt för att lägga till polygon' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Lägg till polygon</a> med Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>i Pen-polygonen och linje lagret.
</iframe>

I koden ovan skapar det första blocket kod ett kart objekt. Du kan se [skapa en karta](./map-create.md) för instruktioner.

I det andra kodblock skapas ett data käll objekt med hjälp av klassen [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . En [polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) skapas från en matris med koordinater och läggs till i data källan. 

En [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) återger data som radbryts i data [källan](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) på kartan. Se egenskaper för ett polygon-lager på [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). En [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) är en matris med rader. Se egenskaperna för ett linje lager på [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Det tredje blocket med kod skapar polygoner och linje lager.

Det sista blocket kod lägger till polygon-och linje skikten i kartan. Data källan och lagren skapas och läggs till i kartan i [händelse hanteraren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) för att säkerställa att polygonen visas när kartan har lästs in helt.

> [!TIP]
> Linje lager som standard återger koordinaterna för polygoner och linjer i en data källa. För att begränsa lagret så att det bara återger lin Est ring-funktioner, `filter` anger du egenskapen för lagret `['==', ['geometry-type'], 'LineString']` till `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` eller om du även vill inkludera MultiLineString-funktioner.

### <a name="fill-a-polygon-with-a-pattern"></a>Fylla en polygon med ett mönster

Förutom att fylla en polygon med en färg kan du också använda ett bild mönster. Läs in ett bild mönster i Maps-avbildningen Sprite-resurser och referera sedan `fillPattern` till den här avbildningen med egenskapen för polygon-lagret.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fyllnings mönster för polygon" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se fyllnings <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>mönstret</a> för pen-polygonen<a href='https://codepen.io/azuremaps'>@azuremaps</a>genom att Azure Maps () på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-polygon-layer"></a>Anpassa ett polygon-lager

Polygon-lagret har bara några få format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Lägg till en cirkel till kartan

Azure Maps använder en utökad version av det injson-schema som tillhandahåller en definition för cirklar som anges [här](extend-geojson.md#circle). En cirkel kan återges på kartan genom `Point` att skapa en funktion som har en `subType` `"Circle"` egenskap med värdet och en `radius` egenskap som har ett tal som representerar radien i meter. Exempel:

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

Azure Maps Web SDK konverterar dessa `Point` funktioner till `Polygon` funktioner under försättsblad och kan återges på kartan med hjälp av polygon-och linje lager som visas här.

<iframe height='500' scrolling='no' title='Lägga till en cirkel till en karta' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Lägg till en cirkel till en karta</a> efter Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kod blocket i koden ovan skapar ett kart objekt. Du kan se [skapa en karta](./map-create.md) för instruktioner.

I det andra kodblock skapas ett data käll objekt med hjälp av klassen [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . En cirkel är en [funktion](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) i [punkt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) och har en `subType` egenskap inställd på `"Circle"` och ett `radius` egenskaps värde i meter. När en punkt funktion med en `subType` i `"Circle"` läggs till i en data källa konverteras den till en cirkelformad polygon i kartan.

En [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) återger data som radbryts i data [källan](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) på kartan. Det sista blocket med kod skapar och lägger till ett polygon-lager till kartan. Se egenskaper för ett polygon-lager på [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Data källan och polygon-lagret skapas och läggs till i kartan i [händelse hanteraren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) för att säkerställa att cirkeln visas när kartan har lästs in helt.

## <a name="make-a-geometry-easy-to-update"></a>Gör en geometri lätt att uppdatera

En `Shape` klass radbryter en [geometri](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) eller [funktion](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) och gör det enkelt att uppdatera och underhålla dem.
`new Shape(data: Feature<data.Geometry, any>)`skapar ett Shape-objekt och initierar det med den angivna funktionen.

<br/>

<iframe height='500' scrolling='no' title='Uppdatera form egenskaper' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se egenskaperna för Penn <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>uppdaterings formen</a> genom Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Det första kod blocket ovan skapar ett kart objekt. Du kan se [skapa en karta](./map-create.md) för instruktioner.

En punkt är en [funktion](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) i [peka](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) på klassen. Det andra kodblock initierar RADIUS-värdet för HTML-Slider-elementet och skapar sedan och omsluter ett punkt objekt i ett [form](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) klass objekt.

Det tredje kod blocket skapar en funktion som tar värdet från Slider-elementet för HTML-intervall och ändrar RADIUS-värdet med form klassen [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) -metoden.

I det fjärde blocket kod skapas ett data käll objekt med hjälp av klassen [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Punkten läggs sedan till i data källan.

En [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) återger data som radbryts i data [källan](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) på kartan. Det tredje blocket med kod skapar ett polygon-lager. Se egenskaper för ett polygon-lager på [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Data källan, klicka på händelse hanteraren och polygon-lagret skapas och läggs till i kartan i [händelse hanteraren](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) för att säkerställa att punkten visas när kartan har lästs in helt.

## <a name="next-steps"></a>Nästa steg

Fler kod exempel som kan läggas till i dina kartor finns i följande artiklar:

> [!div class="nextstepaction"]
> [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)
