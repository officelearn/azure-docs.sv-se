---
title: Lägg till ett termiskt kart skikt i Azure Maps | Microsoft Docs
description: Så här lägger du till ett termiskt kart skikt i Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f7115e7c8b95efd0e3bbc8a788528878c2d1f092
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484310"
---
# <a name="add-a-heat-map-layer"></a>Lägga till ett heatmapskikt

Värme kartor, även kallade punkt Täthets kartor, är en typ av data visualisering som används för att representera densiteten för data med hjälp av en uppsättning färger. De används ofta för att visa data "frekventa fläckar" på en karta och är ett bra sätt att återge data uppsättningar med stora punkter.  Exempel: åter givning av tusentals punkter i kart visningen som symboler omfattar de flesta kart områdena och resulterar i att många symboler överlappar varandra, vilket gör det svårt att få mycket inblick i data. Att visualisera samma data uppsättning som en värme karta gör det dock enkelt att se var punkt data är den mest täta och den relativa densiteten för andra områden. Det finns många scenarier där värme kartor används. Här är några exempel:

- Temperatur data återges ofta som värme karta eftersom den tillhandahåller ungefärliger för den temperatur som är mellan två data punkter.
- Rendering av data för brus sensorer som en värme karta visar inte bara intensiteten för bruset där sensorn är men kan också ge insikter om den här utmatningen över ett avstånd. Brus nivån på en plats kanske inte är hög, men om brus täcknings området från flera sensorer överlappar varandra, är det möjligt att det överlappande området kan uppleva högre buller nivåer och därmed visas i värme kartan.
- Visualisering av en GPS-spårning som innehåller hastigheten som en viktad höjd och en karta där intensiteten för varje data punkt baseras på hastigheten är ett bra sätt att se var fordonet gick snabbare.

> [!TIP]
> Värme kart skikt som standard återger koordinaterna för alla Geometries i en data källa. Om du vill begränsa lagret så att det bara återger punkt geometri funktioner, anger du `filter` egenskapen för lagret som `['==', ['geometry-type'], 'Point']` eller `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` om du även vill inkludera MultiPoint-funktioner.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>Lägga till ett heatmapskikt

Om du vill rendera en data källa med punkter som en värme karta kan du skicka data källan till en instans av klassen `HeatMapLayer` och lägga till den i kartan som visas här.

I följande kod har varje värme punkt en radie på 10 bild punkter på alla zoomnings nivåer. När du lägger till värme kart skiktet till kartan infogar det här exemplet det under etikett lagret för att skapa en bättre användar upplevelse eftersom etiketterna är tydligt synliga ovanför värme kartan. Data i det här exemplet hämtas från [USGS datauppsättningen jord bävning-farlighetsprogram](https://earthquake.usgs.gov/) och representerar betydande jord bävningar som har inträffat under de senaste 30 dagarna.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a data set of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Nedan visas det fullständiga kod exemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Enkelt värme kart skikt' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se det <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>enkla värme kart skiktet</a> för pennor genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-the-heat-map-layer"></a>Anpassa värme kart skiktet

I föregående exempel har du anpassat värme kartan genom att ange alternativ för radie och opacitet. Värme kart skiktet innehåller flera alternativ för anpassning.

* `radius`: definierar en pixel-radie där varje data punkt ska renderas. Radien kan anges som ett fast tal eller som ett uttryck. Med hjälp av ett uttryck är det möjligt att skala radien baserat på zoomnivån, som visas för att representera ett konsekvent avstånd på kartan (till exempel 5-mil-radien).
* `color`: anger hur värme kartan är färgad. En färg toning används ofta för värme kartor och kan uppnås med ett `interpolate`-uttryck. Om du använder ett `step` uttryck för att färgsätta värme kartan bryts densiteten visuellt i intervall som påminner om en profil eller en polär stil karta. Dessa färgpaletter definierar färgerna från minimi värdet till högsta densitet. Färg värden för värme kartor anges som ett uttryck i `heatmap-density` svärdet. Färgen vid index 0 i ett interpolationsmetod-uttryck eller standard färgen för ett steg uttryck definierar färgen på det områden där det inte finns några data och som kan användas för att definiera en bakgrunds färg. Många föredrar att ange det här värdet som transparent eller en halv genomskinlig svart. Här är exempel på färg uttryck.

| Färg uttryck för interpolation | Uttryck för stegvisa färger | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpolerat",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["linjär"\]<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["termisk karta-densitet"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transparent",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "lila",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"steg",<br/>&nbsp;&nbsp;&nbsp;&nbsp;\["termisk karta-densitet"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"transparent",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "flottan"<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "grön",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "gul",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "röd"<br/>\] | 

- `opacity`: anger hur ogenomskinligt eller transparent det termiskt kart skiktet är.
- `intensity`: använder en multiplikator för varje data punkts vikt för att öka den övergripande intensiteten hos termisk karta och hjälper till att göra de små skillnaderna i vikten av data punkter lättare att visualisera.
- `weight`: som standard har alla data punkter en vikt på 1, vilket innebär att alla data punkter viktas lika. Vikt alternativet fungerar som en multiplikator och kan anges som ett tal eller ett uttryck. Om ett tal har angetts som vikt, säger du 2, är det detsamma som att placera varje data punkt på kartan två gånger, vilket dubblerar densiteten. Om du ställer in viktnings alternativet på ett tal återges den termiska kartan på samma sätt som med alternativet intensitet. Men om ett uttryck används kan vikten för varje data punkt baseras på egenskaperna för varje data punkt. Ta jord bävning data som ett exempel, representerar varje data punkt en jord bävning. Ett viktigt mått varje jord bävning data punkt har, är ett värde för storlek. Jord bävningar sker hela tiden, men de flesta har låg storlek och är inte ens filtiga. Genom att använda värdet i ett uttryck för att tilldela vikten till varje data punkt kan mer betydande jord bävningar bättre representeras i värme kartan.
- Förutom bas lager alternativen, min/max zoom, Visible och filter, det finns också ett `source` alternativ om du vill uppdatera data källan och `source-layer` alternativet om data källan är en vektor panels källa.

Här är ett verktyg för att testa olika alternativ för termisk kart skikt.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för termisk kart skikt' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>termisk Map-lager</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Konsekvent zoomnings bara termisk karta

Som standard har radien för data punkter som återges i värme kart skiktet ett fast pixel-radie för alla zoomnings nivåer. När kartan är insamlad är data aggarna tillsammans och värme kart lagret annorlunda. Ett `zoom` uttryck kan användas för att skala radien för varje zoomnings nivå så att varje data punkt täcker samma fysiska område i kartan. Det gör att värme kart lagret ser mer statiskt och konsekvent. Varje zoomnivå på kartan har två gånger så många bild punkter lodrätt och vågrätt som föregående zoomnings nivå. Om du skalar radien så att den dubbleras med varje zoomnivå skapas en värme karta som ser konsekvent ut på alla zoomnings nivåer. Detta kan åstadkommas med hjälp av `zoom` med ett bas-`exponential interpolation` uttryck som visas i exemplet nedan. Zooma kartan för att se hur värme kartan skalar med zoomnings nivån.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Konsekvent zoomnings bara termisk karta" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se den <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>inkonsekventa den klickbara värme kartan</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Genom att aktivera klustring på data källan grupperas punkter som är nära varandra grupperade tillsammans som en klustrad punkt. Antalet punkter för varje kluster kan användas som vikt uttryck för värme kartan och minska antalet poäng som måste återges avsevärt. Antalet poäng i ett kluster lagras i en `point_count` egenskap för punkt funktionen enligt nedan. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Om den klustrade radien bara är några få pixlar, kommer det inte att vara mycket visuellt skillnad på åter givningen. En större radie kommer att gruppera fler punkter i varje kluster och förbättra prestandan för termisk karta, men har en tydligare skillnader.

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