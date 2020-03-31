---
title: Klusterpunktsdata på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du samlar punktdata och återge dem på en karta med hjälp av Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: e65681aefc047ba540d4ad0d91ef6e4d2af5f3ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190267"
---
# <a name="clustering-point-data"></a>Klusterpunktsdata

När du visualiserar många datapunkter på kartan kan datapunkter överlappa varandra. Överlappningen kan orsaka att kartan kan bli oläslig och svår att använda. Klusterpunktsdata är processen att kombinera punktdata som är nära varandra och som representerar dem på kartan som en enda klustrade datapunkt. När användaren zoomar in på kartan delas klustren isär i sina enskilda datapunkter. När du arbetar med ett stort antal datapunkter använder du klustringsprocesserna för att förbättra användarupplevelsen.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Aktivera kluster på en datakälla

Aktivera klustring `DataSource` i klassen `cluster` genom att ange alternativet till true. Ange `ClusterRadius` att välja närliggande punkter och kombinerar dem till ett kluster. Värdet för `ClusterRadius` är i pixlar. Används `clusterMaxZoom` för att ange en zoomnivå där klusterlogiken ska inaktiveras. Här är ett exempel på hur du aktiverar klustring i en datakälla.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Om två datapunkter är nära varandra på marken är det möjligt att klustret aldrig går sönder, oavsett hur nära användaren zoomar in. För att åtgärda detta `clusterMaxZoom` kan du ställa in alternativet att inaktivera klusterlogiken och helt enkelt visa allt.

Här är ytterligare `DataSource` metoder som klassen tillhandahåller för klustring:

| Metod | Returtyp | Beskrivning |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Promise&lt;&lt;&lt;Array-funktionsgeometri, valfri&gt; \| form&gt;&gt; | Hämtar underordnade i det angivna klustret på nästa zoomnivå. Dessa barn kan vara en kombination av former och subclusters. Subclusters kommer att vara funktioner med egenskaper som matchar ClusteredProperties. |
| getClusterExpansionZoom(clusterId: number) | Löftesnummer&lt;&gt; | Beräknar en zoomnivå där klustret börjar expandera eller bryta isär. |
| getClusterLeaves(clusterId: number, limit: number, offset: number) | Promise&lt;&lt;&lt;Array-funktionsgeometri, valfri&gt; \| form&gt;&gt; | Hämtar alla punkter i ett kluster. Ange `limit` att returnera en delmängd av punkterna `offset` och använd till-sidan genom punkterna. |

## <a name="display-clusters-using-a-bubble-layer"></a>Visa kluster med hjälp av ett bubbellager

Ett bubbellager är ett bra sätt att återge klustrade punkter. Använd uttryck för att skala radien och ändra färgen baserat på antalet punkter i klustret. Om du visar kluster med hjälp av ett bubbellager bör du använda ett separat lager för att återge okluserade datapunkter.

Om du vill visa storleken på klustret ovanpå bubblan använder du ett symbollager med text och använder inte en ikon.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Grundläggande kluster med bubbellager" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Pen <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>Basic-bubbelskiktsklustrorna</a> från Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Visa kluster med hjälp av ett symbollager

När du visualiserar datapunkter döljs automatiskt symboler som överlappar varandra för att säkerställa ett renare användargränssnitt. Det här standardbeteendet kan inte vara önskvärt om du vill visa datapunkters densiteten på kartan. Dessa inställningar kan dock ändras. Om du vill visa `allowOverlap` alla symboler `iconOptions` ställer `true`du in alternativet symbollager till . 

Använd klustring för att visa datapunkters densitet samtidigt som du behåller ett rent användargränssnitt. Exemplet nedan visar hur du lägger till anpassade symboler och representerar kluster och enskilda datapunkter med hjälp av symbollagret.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kluster symbollager" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se lagret Pen <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>Clustered Symbol</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Kluster och värmekartorskiktet

Värmekartor är ett bra sätt att visa datatätheten på kartan. Den här visualiseringsmetoden kan hantera ett stort antal datapunkter på egen hand. Om datapunkterna är klustrade och klusterstorleken används som värmekartans vikt kan värmekartan hantera ännu mer data. För att uppnå detta `weight` alternativ, ställa in `['get', 'point_count']`alternativet för värmekartan lagret till . När klusterradien är liten ser värmekartan nästan identisk ut med en värmekarta med hjälp av de obehandlade datapunkterna, men den kommer att fungera mycket bättre. Men ju mindre klusterradien är, desto mer exakt blir värmekartan, men med färre prestandafördelar.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Klustervägd värmekarta" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>penklustret viktad värmekarta</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Mushändelser på klustrade datapunkter

När mushändelser inträffar i ett lager som innehåller klustrade datapunkter återgår den klustrade datapunkten till händelsen som ett GeoJSON-punktfunktionsobjekt. Den här poängfunktionen har följande egenskaper:

| Egenskapsnamn             | Typ    | Beskrivning   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Anger om funktionen representerar ett kluster. |
| `cluster_id`              | sträng  | Ett unikt ID för klustret som `getClusterExpansionZoom`kan `getClusterChildren`användas `getClusterLeaves` med DataSource , och metoder. |
| `point_count`             | nummer  | Antalet punkter som klustret innehåller.  |
| `point_count_abbreviated` | sträng  | En sträng som `point_count` förkortar värdet om det är långt. (till exempel blir 4 000 4K)  |

Det här exemplet tar ett bubbellager som återger klusterpunkter och lägger till en klickhändelse. När klickhändelsen utlöses beräknar och zoomar koden kartan till nästa zoomnivå, där klustret bryts isär. Den här funktionen implementeras `getClusterExpansionZoom` med `DataSource` hjälp av `cluster_id` metoden för klassen och egenskapen för den klickade klustrade datapunkten.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kluster fåClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/moZWeV/'>penklustret getClusterExpansionZoom</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Visa klusterområde 

Punktdata som ett kluster representerar är spridda över ett område. I det här exemplet när musen hovras över ett kluster uppstår två huvudsakliga beteenden. För det första kommer de enskilda datapunkterna i klustret att användas för att beräkna ett konvext skrov. Sedan kommer det konvexa skrovet att visas på kartan för att visa ett område.  Ett konvext skrov är en polygon som sveper en uppsättning `atlas.math.getConvexHull` punkter som ett elastiskt band och kan beräknas med hjälp av metoden. Alla punkter i ett kluster kan hämtas från `getClusterLeaves` datakällan med hjälp av metoden.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Klusterområde konvext skrov" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>penklusterområdet konvext skrov</a> från Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Samla data i kluster

Ofta representeras kluster med hjälp av en symbol med antalet punkter som finns i klustret. Men ibland är det önskvärt att anpassa stilen på kluster med ytterligare mått. Med klusteraggregat kan anpassade egenskaper skapas och fyllas i med hjälp av en beräkning av [ett aggregerat uttryck.](data-driven-style-expressions-web-sdk.md#aggregate-expression)  Klusteraggregat kan definieras `clusterProperties` i alternativet `DataSource`.

I följande exempel används ett samlingsuttryck. Koden beräknar ett antal baserat på egenskapen entitetstyp för varje datapunkt i ett kluster. När en användare klickar på ett kluster visas en popup-bild med ytterligare information om klustret.

<iframe height="500" style="width: 100%;" scrolling="no" title="Klusteraggregat" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>pennklusteraggregaten</a> från<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Klassen DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions-objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.math namnområde](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Se kodexempel för att lägga till funktioner i din app:

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägga till ett heatmapskikt](map-add-heat-map-layer.md)
