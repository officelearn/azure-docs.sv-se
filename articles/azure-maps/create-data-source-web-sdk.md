---
title: Skapa en datakälla för en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du skapar en datakälla och lägger till den på en karta med Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190237"
---
# <a name="create-a-data-source"></a>Skapa en datakälla

Azure Maps Web SDK lagrar data i datakällor. Med hjälp av datakällor optimeras dataåtgärder för frågor och rendering. För närvarande finns det två typer av datakällor:

**GeoJSON-datakälla**

En GeoJSON-baserad datakäll läser in `DataSource` och lagrar data lokalt med hjälp av klassen. GeoJSON-data kan skapas manuellt eller skapas med hjälp av hjälpklasserna i [namnområdet atlas.data.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) Klassen `DataSource` tillhandahåller funktioner för att importera lokala eller fjärranslutna GeoJSON-filer. Remote GeoJSON-filer måste finnas på en COR-aktiverad slutpunkt. Klassen `DataSource` tillhandahåller funktioner för klusterpunktsdata. Och data kan enkelt läggas till, tas `DataSource` bort och uppdateras med klassen.


> [!TIP]
> Låt oss säga att du vill `DataSource`skriva över alla data i en . Om du ringer `clear` samtal `add` till de dåvarande funktionerna kan kartan återges två gånger, vilket kan orsaka en fördröjning. Använd i `setShapes` stället funktionen, som tar bort och ersätter alla data i datakällan och endast utlöser en enda återrerenad av kartan.

**Källa för vektorpaneler**

En vektorpanelkälla beskriver hur du kommer åt ett vektorpanellager. Använd klassen [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) för att instansiera en vektorpanelkälla. Vektorpanellager liknar panellager, men de är inte desamma. Ett panellager är en rasterbild. Vektorpanellager är en komprimerad fil i PBF-format. Den här komprimerade filen innehåller vektorkartdata och ett eller flera lager. Filen kan återges och formateras på klienten, baserat på stilen på varje lager. Data i en vektorpanel innehåller geografiska funktioner i form av punkter, linjer och polygoner. Det finns flera fördelar med att använda vektorpanellager i stället för rasterpanellager:

 - En filstorlek på en vektorpanel är vanligtvis mycket mindre än en motsvarande rasterpanel. Som sådan används mindre bandbredd. Det innebär lägre latens, en snabbare karta och en bättre användarupplevelse.
 - Eftersom vektorpaneler återges på klienten anpassas de till upplösningen på den enhet som de visas på. Som ett resultat av detta verkar de renderade kartorna mer väldefinierade, med kristallklara etiketter.
 - Om du ändrar formatet på data i vektormappningarna måste du inte hämta data igen, eftersom det nya formatet kan användas på klienten. Om du däremot ändrar stilen på ett rasterpanellager måste det däremot vanligtvis läsas in paneler från servern och sedan använda det nya formatet.
 - Eftersom data levereras i vektorform krävs mindre serverbearbetning för att förbereda data. Därför kan de nyare uppgifterna göras tillgängliga snabbare.

Alla lager som använder en `sourceLayer` vektorkälla måste ange ett värde.

När datakällor har skapats kan de `map.sources` läggas till på kartan via egenskapen, som är en [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). Följande kod visar hur `DataSource` du skapar en och lägger till den på kartan.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Azure Maps följer [Mapbox Vector Tile Specification](https://github.com/mapbox/vector-tile-spec), en öppen standard.

## <a name="connecting-a-data-source-to-a-layer"></a>Ansluta en datakälla till ett lager

Data återges på kartan med hjälp av renderingslager. En enda datakälla kan refereras av ett eller flera renderingslager. Följande renderingslager kräver en datakälla:

- [Bubbellager](map-add-bubble-layer.md) - återger punktdata som skalade cirklar på kartan.
- [Symbollagret](map-add-pin.md) - återger punktdata som ikoner eller text.
- [Värme kartlagret](map-add-heat-map-layer.md) - återger punktdata som en densitet värmekarta.
- [Linjelager](map-add-shape.md) - återge en linje och eller återge dispositionen av polygoner. 
- [Polygon lager](map-add-shape.md) - fyller området i en polygon med en enfärgad eller bild mönster.

Följande kod visar hur du skapar en datakälla, lägger till den på kartan och ansluter den till ett bubbellager. Och importera sedan GeoJSON-punktdata från en fjärrplats till datakällan. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Det finns ytterligare renderingslager som inte ansluter till dessa datakällor, men de läser direkt in data för rendering. 

- [Bildlagret](map-add-image-layer.md) - överlagrar en enskild bild ovanpå kartan och binder hörnen till en uppsättning angivna koordinater.
- [Kakel lager](map-add-tile-layer.md) - lägger ovanpå ett rasterkakellager ovanpå kartan.

## <a name="one-data-source-with-multiple-layers"></a>En datakälla med flera lager

Flera lager kan anslutas till en enda datakälla. Det finns många olika scenarier där det här alternativet är användbart. Tänk till exempel på det scenario där en användare ritar en polygon. Vi bör återge och fylla polygonområdet när användaren lägger till punkter på kartan. Om du lägger till en formaterad linje för att beskriva polygonen blir det lättare att se polygonens kanter, medan användaren ritar. För att enkelt redigera en enskild position i polygonen kan vi lägga till ett handtag, som en stift eller en markör, ovanför varje position.

![Karta som visar flera lager som återger data från en enda datakälla](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

I de flesta mappningsplattformar behöver du ett polygonobjekt, ett linjeobjekt och en nål för varje position i polygonen. När polygonen ändras måste du uppdatera linjen och stiften manuellt, vilket snabbt kan bli komplext.

Med Azure Maps behöver du bara en polygon i en datakälla som visas i koden nedan.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Datakälla](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Se följande artiklar för fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till ett popup-fönster](map-add-popup.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](map-add-shape.md)

> [!div class="nextstepaction"]
> [Lägg till en värmekarta](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)