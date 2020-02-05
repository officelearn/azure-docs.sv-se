---
title: Lägg till ett värme kart skikt i en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du lägger till ett termiskt kart skikt till en karta med hjälp av Microsoft Azure Maps-webbsdk.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: d52d808813078c2aca7de59aa626e83f96221720
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986248"
---
# <a name="add-a-heat-map-layer"></a>Lägga till ett heatmapskikt

Värme kartor, även kallade punkt Täthets kartor, är en typ av data visualisering. De används för att representera densiteten för data med hjälp av en rad färger och visa data "frekventa fläckar" på en karta. Värme kartor är ett bra sätt att rendera data uppsättningar med ett stort antal punkter. 

Åter givning av tusentals punkter som symboler kan avse de flesta av kart områdena. Det här fallet resulterar förmodligen i att många symboler överlappar varandra. Gör det svårt att få bättre förståelse för dina data. Att visualisera samma data uppsättning som en värme karta gör det dock enkelt att se densiteten och den relativa densiteten för varje data punkt.

Du kan använda värme kartor i många olika scenarier, inklusive:

- **Temperatur data**: tillhandahåller ungefärliger för vad temperaturen är mellan två data punkter.
- **Data för buller sensorer**: visar inte bara intensiteten för bruset där sensorn är, men det kan också ge insikter om avtagningen över ett avstånd. Brus nivån på en plats kanske inte är hög. Om brus täcknings området från flera sensorer överlappar varandra, är det möjligt att det överlappande området kan uppleva högre buller nivåer. Därför skulle det överlappande område vara synligt i värme kartan.
- **GPS-spårning**: omfattar hastigheten som en viktad höjd karta där intensiteten för varje data punkt baseras på hastigheten. Den här funktionen är till exempel ett sätt att se var ett fordon går snabbare.

> [!TIP]
> Värme kart skikt återges som standard koordinaterna för alla Geometries i en data källa. Om du vill begränsa lagret så att det bara återger punkt geometri funktioner, anger du `filter`-egenskapen för lagret som `['==', ['geometry-type'], 'Point']`. Om du vill inkludera MultiPoint-funktioner anger du även `filter`-egenskapen för lagret som `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Lägga till ett heatmapskikt

För att rendera en data källa med punkter som en värme karta, skicka data källan till en instans av klassen `HeatMapLayer` och Lägg till den på kartan.

I följande kod har varje värme punkt en radie på 10 bild punkter på alla zoomnings nivåer. För att säkerställa en bättre användar upplevelse är värme kartan under etikett skiktet. Etiketterna förblir tydligt synliga. Data i det här exemplet är från [USGS datauppsättningen jord bävning-riskhanterings program](https://earthquake.usgs.gov/). Det är för betydande jord bävningar som har inträffat under de senaste 30 dagarna.

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

Här är det fullständiga kod exemplet i föregående kod.

<br/>

<iframe height='500' scrolling='no' title='Enkelt värme kart skikt' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se det <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>enkla värme kart skiktet</a> för pennor genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-the-heat-map-layer"></a>Anpassa värme kart skiktet

I föregående exempel har du anpassat värme kartan genom att ange alternativ för radie och opacitet. Värme kart skiktet innehåller flera alternativ för anpassning, inklusive:

* `radius`: definierar en pixel-radie där varje data punkt ska renderas. Du kan ställa in radien som ett fast tal eller som ett uttryck. Genom att använda ett uttryck kan du skala radien baserat på zoomnivån och representera ett konsekvent rums område på kartan (till exempel en radie på 5 mil).
* `color`: anger hur värme kartan är färgad. En färg toning är en vanlig funktion i värme kartor. Du kan uppnå resultatet med ett `interpolate`-uttryck. Du kan också använda ett `step` uttryck för att färgsätta värme kartan och dela upp densiteten i intervall som påminner om en profil för konturer eller radar format. Dessa färgpaletter definierar färgerna från minimi värdet till högsta densitet. 

  Du anger färg värden för värme Maps som ett uttryck i `heatmap-density` svärdet. Färgen på det områden där det inte finns några data som definieras vid index 0 i "interpolationsmetod"-uttrycket eller standard färgen för ett "stegat"-uttryck. Du kan använda det här värdet för att definiera en bakgrunds färg. Ofta är det här värdet inställt på transparent eller en halv genomskinlig svart. 
   
  Här är exempel på färg uttryck:

  | Färg uttryck för interpolation | Uttryck för stegvisa färger | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpolerat",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["linjär"\]<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["termisk karta-densitet"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transparent",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "lila",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"steg",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["termisk karta-densitet"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"transparent",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "flottan"<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "grön",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "gul",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "röd"<br/>\] |   

- `opacity`: anger hur ogenomskinligt eller transparent det termiskt kart skiktet är.
- `intensity`: använder en multiplikator för varje data punkts vikt för att öka den övergripande intensiteten för termisk karta. Detta bidrar till att göra en skillnad i vikten av data punkter, vilket gör det lättare att visualisera dem.
- `weight`: som standard har alla data punkter en vikt på 1, och viktas på samma sätt. Vikt alternativet fungerar som en multiplikator och du kan ange det som ett tal eller ett uttryck. Om ett tal har angetts som vikt, är det likvärdigheten att placera varje data punkt på kartan två gånger. Om vikten till exempel är 2, så dubbleras densiteten. Om du ställer in viktnings alternativet på ett tal återges den termiska kartan på samma sätt som med alternativet intensitet. 

  Men om du använder ett uttryck kan vikten för varje data punkt baseras på egenskaperna för varje data punkt. Anta till exempel att varje data punkt representerar en jord bävning. Ett viktigt mått är att varje jord bävning data punkt har varit ett värde för storlek. Jord bävningar sker hela tiden, men de flesta har låg storlek och har inte märkts. Genom att använda värdet i ett uttryck för att tilldela vikten till varje data punkt får du en bättre representation av betydelsen av jord bävningar i värme kartan.
- `source` och `source-layer`: gör det möjligt att uppdatera data källan.

Här är ett verktyg för att testa olika alternativ för termisk kart skikt.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för termisk kart skikt' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>termisk Map-lager</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konsekvent zoomnings bara termisk karta

Som standard har radien för data punkter som återges i värme kart skiktet ett fast pixel-radie för alla zoomnings nivåer. När du zoomar kartan ser data samman varandra och värme kart lagret annorlunda ut. 

Använd ett `zoom`-uttryck för att skala radien för varje zoomnings nivå, så att varje data punkt täcker samma fysiska område i kartan. Det här uttrycket gör att värme kart lagret ser mer statiskt och konsekvent. Varje zoomnivå på kartan har två gånger så många bild punkter lodrätt och vågrätt som föregående zoomnings nivå. 

Att skala radien så att den dubbleras med varje zoomnivå skapar en värme karta som ser konsekvent ut på alla zoomnings nivåer. Använd `zoom` med ett bas-`exponential interpolation`-uttryck, som du ser i följande exempel, om du vill använda den här skalningen. Zooma kartan för att se hur värme kartan skalar med zoomnings nivån.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konsekvent zoomnings bara termisk karta" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se den <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>inkonsekventa den klickbara värme kartan</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> När du aktiverar klustring på data källan grupperas punkter som är nära varandra grupperade tillsammans som en klustrad punkt. Du kan använda antalet punkter för varje kluster som värme kartans vikt uttryck. Detta kan avsevärt minska antalet poäng som ska återges. Antalet poäng i ett kluster lagras i en `point_count`-egenskap för punkt funktionen: 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Om den klustrade radien bara är några få pixlar, skulle det finnas en liten visuell skillnad i åter givningen. En större RADIUS-grupp ger fler punkter i varje kluster och förbättrar prestandan för termisk karta.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Fler kod exempel som kan läggas till i dina kartor finns i följande artiklar:

> [!div class="nextstepaction"]
> [Skapa en data Källa](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)
