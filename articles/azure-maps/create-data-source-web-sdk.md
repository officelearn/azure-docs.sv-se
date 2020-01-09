---
title: Skapa en data källa i Azure Maps | Microsoft Docs
description: Så här skapar du en data källa och använder den med Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: b83a66296d54a179a56e37de199ec900ae23a1db
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433020"
---
# <a name="create-a-data-source"></a>Skapa en datakälla

Azure Maps Web SDK lagrar data i data källor som optimerar data för frågor och åter givning. Det finns för närvarande två typer av data Källor:

**Data källa för interjson**

En interjson-baserad data källa kan läsa in och lagra data lokalt med hjälp av klassen `DataSource`. Du kan skapa eller skapa indata från interjson manuellt med hjälp av klasser i [Atlas. data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) område. `DataSource`-klassen innehåller funktioner för att importera lokala eller fjärranslutna JSON-filer. Fjärranslutna interjson-filer måste finnas på en CORs-aktiverad slut punkt. `DataSource`-klassen innehåller funktioner för kluster plats data. Det går enkelt att lägga till, ta bort och uppdatera data med klassen `DataSource`.


> [!TIP]
> Om du vill skriva över alla data i en `DataSource`, och om du gör anrop till `clear` sedan `add` funktioner, försöker kartan återge två gånger igen, vilket kan orsaka en stund. Använd i stället funktionen `setShapes` som tar bort och ersätter alla data i data källan och utlöser bara en åter givning av kartan.

**Vektor panels källa**

En vektor panels källa beskriver hur du får åtkomst till ett vektor panels lager och kan skapas med [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) -klassen. Azure Maps justeras med [specifikationen Mapbox Vector panel](https://github.com/mapbox/vector-tile-spec), som är en öppen standard. Vektor panels lager liknar panel lager, men i stället för varje bricka är det en komprimerad fil (PBF-format) som innehåller vektor kart data och en eller flera lager som kan återges och skrivas ut på klienten baserat på formatet för varje lager. Data i en vektor panel innehåller geografiska funktioner i form av punkter, linjer och polygoner. Det finns flera fördelar med vektor panels lager över raster panels lager.

 - En fil storlek på en vektor panel är vanligt vis mycket mindre än en motsvarande raster panel. Därför används mindre bandbredd, vilket innebär kortare svars tid och en snabbare karta. Detta ger en bättre användar upplevelse.
 - Eftersom vektor paneler återges på klienten kan de anpassas till upplösningen på den enhet som de visas på. Detta gör att de åter givnings kartor som visas mycket mer väldefinierade och med Crystal Clear-etiketter. 
 - Om du ändrar formatet för data i vektor Maps behöver du inte ladda ned data igen eftersom det nya formatet kan tillämpas på klienten. Att ändra formatet på ett raster panels lager kräver däremot vanligt vis inläsning av paneler från servern där det nya formatet tillämpas.
 - Eftersom data levereras i vektor form krävs mindre bearbetning på Server sidan för att förbereda data, vilket innebär att nya data kan göras tillgängliga snabbare.

Alla lager som använder en Vector-källa måste ange ett `sourceLayer` värde. 

När du har skapat data källor kan de läggas till i kartan via `map.sources`-egenskapen, som är en [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). Följande kod visar hur du skapar en `DataSource` och lägger till den i kartan.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

## <a name="connecting-a-data-source-to-a-layer"></a>Ansluta en data källa till ett lager

Data återges på kartan med åter givnings lager. En enskild data källa kan refereras till av en eller flera åter givnings skikt. Följande åter givnings skikt kräver att en data källa är ström:

- [Bubbel Layer](map-add-bubble-layer.md) – återger punkt data som skalade cirklar på kartan.
- [Symbol lager](map-add-pin.md) – återger punkt data som ikoner och/eller text.
- [Termiskt kart skikt](map-add-heat-map-layer.md) – återger punkt data som en densitets värme karta.
- [Linje skikt](map-add-shape.md) – kan användas för att återge linje och konturer för polygoner. 
- [Polygon-lager](map-add-shape.md) – fyller ytan i en polygon med ett mönster för en solid färg eller bild.

Följande kod visar hur du skapar en data källa, lägger till den i kartan och ansluter den till ett bubbeldiagram och sedan importerar data från en annan plats med en annan plats. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Det finns ytterligare åter givnings skikt som inte ansluter till dessa data källor, utan i stället läsa in de data som de återger direkt. 

- [Bild lager](map-add-image-layer.md) – lägger till en enda bild ovanpå kartan och binder dess hörn till en uppsättning angivna koordinater.
- [Panel lager](map-add-tile-layer.md) – superdriver ett raster panels lager ovanpå kartan.

## <a name="one-data-source-with-multiple-layers"></a>En data källa med flera lager

Flera lager kan anslutas till en enda data källa. Detta kan vara konstigt, men det finns många olika scenarier där detta blir användbart. Ta till exempel scenariot för att skapa en polygon-upplevelse. När du låter en användare rita en polygon ska vi återge fyllningens polygon-område när användaren lägger till punkter i kartan. Om du lägger till en formaterad linje som visar polygonen blir det enklare att se polygonens kanter när den ritas. Slutligen kan du lägga till en viss typ av referens, till exempel en PIN-kod eller markör, ovanför varje position i polygonen för att göra det enklare att redigera varje enskild position. Här är en bild som visar det här scenariot.

![Kartning visar flera nivåer som återger data från en enskild data Källa](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

För att kunna utföra det här scenariot i de flesta mappnings plattformar måste du skapa ett polygon-objekt, ett linje objekt och en PIN-kod för varje position i polygonen. När polygonen ändras måste du uppdatera linjen och PIN-fälten manuellt, vilket kan bli komplext snabbt.

Med Azure Maps allt behöver du en enda polygon i en data källa som visas i koden nedan.

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
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till en popup](map-add-popup.md)

> [!div class="nextstepaction"]
> [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett symbol lager](map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägg till ett bubbel-lager](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett linje lager](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygon-lager](map-add-shape.md)

> [!div class="nextstepaction"]
> [Lägg till en värme karta](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Kodexempel](https://docs.microsoft.com/samples/browse/?products=azure-maps)