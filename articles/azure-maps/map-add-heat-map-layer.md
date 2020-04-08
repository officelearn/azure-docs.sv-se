---
title: Lägg till ett värmekartlager på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om hur du lägger till ett värmekartlager på en karta med Hjälp av Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 19765bd28f365cc6f6d5b06646896613dd3e3e87
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804562"
---
# <a name="add-a-heat-map-layer"></a>Lägga till ett heatmapskikt

Värmekartor, även kallade punktdensitetskartor, är en typ av datavisualisering. De används för att representera datatätheten med hjälp av en rad färger och visa data "hotspots" på en karta. Värmekartor är ett bra sätt att återge datauppsättningar med ett stort antal punkter. 

Om du återges tiotusentals punkter som symboler kan de flesta av kartområdet täckas. Det här fallet resulterar sannolikt i många symboler överlappande. Gör det svårt att få en bättre förståelse för data. Visualisering av samma datauppsättning som en värmekarta gör det dock enkelt att se densiteten och den relativa densiteten för varje datapunkt.

Du kan använda värmekartor i många olika scenarier, bland annat:

- **Temperaturdata**: Ger approximationer för vad temperaturen är mellan två datapunkter.
- **Data för brussensorer**: Visar inte bara intensiteten i bullret där sensorn är, men det kan också ge insikt i avledning över ett avstånd. Ljudnivån på en och samma plats kanske inte är hög. Om bullertäckningsområdet från flera sensorer överlappar varandra är det möjligt att det här överlappande området kan få högre ljudnivåer. Som sådan skulle det överlappande området vara synligt i värmekartan.
- **GPS-spår:** Inkluderar hastigheten som en viktad höjdkarta, där intensiteten för varje datapunkt baseras på hastigheten. Den här funktionen ger till exempel ett sätt att se var ett fordon körde för fort.

> [!TIP]
> Värme kartlager som standard återge koordinaterna för alla geometrier i en datakälla. Om du vill begränsa lagret så att det bara `filter` återger punktgeometrifunktioner ställer du in egenskapen för lagret på `['==', ['geometry-type'], 'Point']`. Om du också vill inkludera MultiPoint-funktioner ställer du in egenskapen `filter` för lagret på `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Lägga till ett heatmapskikt

Om du vill återge en datakälla för punkter som en `HeatMapLayer` värmekarta skickar du datakällan till en förekomst av klassen och lägger till den på kartan.

I följande kod har varje värmepunkt en radie på 10 pixlar på alla zoomnivåer. För att säkerställa en bättre användarupplevelse ligger värmekartan under etikettlagret. Etiketterna förblir tydligt synliga. Uppgifterna i det här exemplet kommer från [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/). Det är för betydande jordbävningar som har inträffat under de senaste 30 dagarna.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Här är det fullständiga kodexemplet för koden som körs.

<br/>

<iframe height='500' scrolling='no' title='Enkelt värmekarta lager' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>Simple Heat Map</a> Layer<a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Anpassa värmekartlagret

I föregående exempel anpassades värmekartan genom att ange alternativ för radie och opacitet. Värmekartlagret innehåller flera alternativ för anpassning, bland annat:

* `radius`: Definierar en pixelradie där varje datapunkt ska återges. Du kan ange radien som ett fast tal eller som ett uttryck. Med hjälp av ett uttryck kan du skala radien baserat på zoomnivån och representera ett konsekvent rumsligt område på kartan (till exempel en radie på 5 km).
* `color`: Anger hur värmekartan färgas. En färggradient är ett vanligt inslag i värmekartor. Du kan uppnå effekten `interpolate` med ett uttryck. Du kan också `step` använda ett uttryck för att färglägga värmekartan och dela upp densiteten visuellt i områden som liknar en kontur- eller radarstilskarta. Dessa färgpaletter definierar färgerna från minimum till det maximala densitetsvärdet. 

  Du anger färgvärden för värmekartor `heatmap-density` som ett uttryck för värdet. Färgen på området där det inte finns några data definieras vid index 0 i uttrycket "Interpolation" eller standardfärgen för ett "Klev"-uttryck. Du kan använda det här värdet för att definiera en bakgrundsfärg. Ofta är det här värdet inställt på genomskinligt, eller en halvtransparent svart. 
   
  Här är exempel på färguttryck:

  | Interpolationsfärguttryck | Uttryck för stegvis färg | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpolera".<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["linjär",\]<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["värmekarttäthet",\]<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transparent",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, "lila",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, "#fb00fb".<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"steg",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["värmekarttäthet",\]<br/>&nbsp;&nbsp;&nbsp;&nbsp;"transparent".<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01, "marin",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, "grön",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, "gul".<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "röd"<br/>\] |   

- `opacity`: Anger hur ogenomskinligt eller transparent värmekartlagret är.
- `intensity`: Använder en multiplikator på vikten för varje datapunkt för att öka värmekartans totala intensitet. Det orsakar en skillnad i vikten av datapunkter, vilket gör det lättare att visualisera.
- `weight`: Som standard har alla datapunkter en vikt på 1 och viktas lika. Alternativet Vikt fungerar som en multiplikator och du kan ange det som ett tal eller ett uttryck. Om ett tal anges som vikt är det likvärdigheten att placera varje datapunkt på kartan två gånger. Till exempel, om vikten är 2, fördubblas densiteten. Om du ställer in viktalternativet på ett tal återges värmekartan på ett liknande sätt som med hjälp av intensitetsalternativet. 

  Om du använder ett uttryck kan dock vikten för varje datapunkt baseras på egenskaperna för varje datapunkt. Anta till exempel att varje datapunkt representerar en jordbävning. Magnitudvärdet har varit ett viktigt mått för varje jordbävningsdatapunkt. Jordbävningar inträffar hela tiden, men de flesta har en låg magnitud, och är inte märkt. Använd magnitudvärdet i ett uttryck för att tilldela vikten till varje datapunkt. Genom att använda magnitudvärdet för att tilldela vikten får du en bättre representation av betydelsen av jordbävningar inom värmekartan.
- `source`och `source-layer`: Gör att du kan uppdatera datakällan.

Här är ett verktyg för att testa de olika alternativen värmekarta lager.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för värmekarta' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>pennvärmekartlagret</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konsekvent zoombar värmekarta

Som standard har raderna för datapunkter som återges i värmekartlagret en fast pixelradie för alla zoomnivåer. När du zoomar kartan sammanställer data och värmekartlagret ser annorlunda ut. 

Använd `zoom` ett uttryck för att skala radien för varje zoomnivå, så att varje datapunkt täcker samma fysiska område på kartan. Detta uttryck gör att värmekartlagret ser mer statiskt och konsekvent ut. Varje zoomnivå på kartan har dubbelt så många pixlar vertikalt och horisontellt som föregående zoomnivå. 

Skalning av radien så att den fördubblas med varje zoomnivå skapar en värmekarta som ser konsekvent ut på alla zoomnivåer. Om du vill använda `zoom` den här `exponential interpolation` skalningen använder du med ett bas2-uttryck, med pixelradien inställd `2 * Math.pow(2, minZoom - maxZoom)` för den lägsta zoomnivån och en skalad radie för den maximala zoomnivån som beräknas enligt följande exempel. Zooma kartan för att se hur värmekartan skalas med zoomnivån.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konsekvent zoombar värmekarta" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se den <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>zoombara värmekartan</a> för<a href='https://codepen.io/azuremaps'>@azuremaps</a>pennan med Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> När du aktiverar klustring på datakällan grupperas punkter som ligger nära varandra som en klustrade punkt. Du kan använda poängantalet för varje kluster som viktuttryck för värmekartan. Detta kan avsevärt minska antalet punkter som ska återges. Poängantalet för ett kluster `point_count` lagras i en egenskap för poängfunktionen: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Om klusterradien bara är några få pixlar skulle det finnas en liten visuell skillnad i återgivningen. En större radie grupperar fler punkter i varje kluster och förbättrar värmekartans prestanda.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [HeatMapLayer (värmekartläggning)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Fler kodexempel att lägga till i dina kartor finns i följande artiklar:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)
