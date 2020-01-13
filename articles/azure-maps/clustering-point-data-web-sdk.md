---
title: Kluster plats data på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du använder kluster punkt data och återger dem på en karta med hjälp av Microsoft Azure Maps-webbsdk.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 846abb61511ae1d5aedf77059ed2f1e9f4e5dbfb
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911744"
---
# <a name="clustering-point-data"></a>Kluster plats data

När du visualiserar många data punkter på kartan överlappar de varandra, och kartan ser rörig ut och blir svår att se och använda. Klustring av punkt data kan användas för att förbättra användar upplevelsen. Kluster plats data är en process för att kombinera punkt data som ligger nära varandra och som representerar dem på kartan som en enda klustrad data punkt. När användaren zoomar in i kartan delas klustren isär i sina enskilda data punkter.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="enabling-clustering-on-a-data-source"></a>Aktivera klustring på en data Källa

Kluster kan enkelt aktive ras i `DataSource`-klassen genom att ange alternativet `cluster` till sant. Dessutom kan pixel radien Markera närliggande punkter för att kombinera till ett kluster med hjälp av `clusterRadius` och en zoomnings nivå kan anges för att inaktivera kluster logiken med alternativet `clusterMaxZoom`. Här är ett exempel på hur du aktiverar klustring i en data källa.

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
> Om två data punkter ligger nära varandra på marken är det möjligt att klustret aldrig delas upp, oavsett hur nära användaren zoomar in. För att lösa detta kan du ange `clusterMaxZoom` alternativet för data källan som anger på zoomnivån för att inaktivera kluster logiken och bara Visa allt.

`DataSource`-klassen har också följande metoder för klustring:

| Metod | Returtyp | Beskrivning |
|--------|-------------|-------------|
| getClusterChildren (clusterId: Number) | Funktionen Promise&lt;array&lt;Feature&lt;Geometry, alla&gt; \| former&gt;&gt; | Hämtar underordnade för det aktuella klustret på nästa zoomnings nivå. Dessa underordnade kan vara en kombination av former och under kluster. Under klustren är funktioner med egenskaper som matchar ClusteredProperties. |
| getClusterExpansionZoom (clusterId: Number) | Promise&lt;Number&gt; | Beräknar en zoomnings nivå där klustret börjar expandera eller dela upp. |
| getClusterLeaves (clusterId: Number, Limit: Number, offset: Number) | Funktionen Promise&lt;array&lt;Feature&lt;Geometry, alla&gt; \| former&gt;&gt; | Hämtar alla punkter i ett kluster. Ange `limit` för att returnera en delmängd av punkterna och Använd `offset` för att bläddra igenom punkterna. |

## <a name="display-clusters-using-a-bubble-layer"></a>Visa kluster med ett bubbel-lager

Ett bubbeldiagram är ett bra sätt att rendera grupperade punkter eftersom du enkelt kan skala radien och ändra färgen utifrån antalet punkter i klustret med hjälp av ett uttryck. När du visar kluster med ett bubbel-lager bör du även använda ett separat lager för att återge data punkter som inte är grupperade. Det är ofta bra att även kunna visa storleken på klustret ovanpå bubblorna. Ett symbol lager med text och ingen ikon kan användas för att uppnå det här beteendet. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Basic Bubble Layer-klustring" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se kluster för rit <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>skiktet för rit skiktet</a> ritstift genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Visa kluster med ett symbol lager

När punkt data visualiseras med hjälp av symbol lagret döljs som standard symboler som överlappar varandra för att skapa en renare upplevelse, men det kanske inte är den önskade upplevelsen om du vill se densiteten för data punkter på kartan. Om du ställer in `allowOverlap` alternativet för symbol lagret `iconOptions` egenskap till `true` inaktive ras den här upplevelsen, men alla symboler visas. Med kluster kan du se densiteten för alla data samtidigt som du skapar en snyggt ren användar upplevelse. I det här exemplet används anpassade symboler för att representera kluster och enskilda data punkter.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Grupperat symbol lager" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se det pennan <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>grupperade symbol skiktet</a> genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Kluster och termisk Maps-skikt

Värme kartor är ett bra sätt att Visa densiteten för data på kartan. Den här visualiseringen kan hantera ett stort antal data punkter, men den kan hantera ännu mer data om data punkterna är klustrade och kluster storleken används som värme kartans vikt. Ange `weight` alternativet för värme kart skiktet som `['get', 'point_count']` för att uppnå detta. När kluster-RADIUS-radien är liten kommer värme kartan att se nästan likadant ut för en värme karta med de data punkter som inte är klustrade, men den kommer att fungera mycket bättre. Men ju mindre kluster-RADIUS, desto bättre blir värme kartan, men med mindre av prestanda förmånen.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kluster viktad termisk karta" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>klustrets viktade värme karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Mus händelser på klustrade data punkter

När mus händelser inträffar i ett lager som innehåller klustrade data punkter, returneras den klustrade data punkten till händelsen som ett objekt av en punkt med ett interaktivt JSON-objekt. Den här punkt funktionen kommer att ha följande egenskaper:

| Egenskapsnamn             | Typ    | Beskrivning   |
|---------------------------|---------|---------------|
| `cluster`                 | boolean | Anger om funktionen representerar ett kluster. |
| `cluster_id`              | sträng  | Ett unikt ID för klustret som kan användas med data källan `getClusterExpansionZoom`, `getClusterChildren`och `getClusterLeaves` metoder. |
| `point_count`             | nummer  | Antalet platser som klustret innehåller.  |
| `point_count_abbreviated` | sträng  | En sträng som förkortar `point_count` svärdet om det är långt. (till exempel 4 000 blir 4K)  |

I det här exemplet används ett bubbeldiagram som återger kluster punkter och lägger till en klicknings händelse som när den utlöses, beräknar och zoomar kartan till nästa zoomnings nivå där klustret kommer att delas upp med `getClusterExpansionZoom`-metoden i klassen `DataSource` och egenskapen `cluster_id` för den klickade klustrade data punkten. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kluster getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/moZWeV/'>klustrets getClusterExpansionZoom</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Visa kluster plats 

De punkt data som ett kluster representerar sprids över ett utrymme. I det här exemplet när musen hovrar över ett kluster, kommer de enskilda data punkter som den innehåller (löv) att användas för att beräkna en konvex skrov och visas på kartan för att Visa ytan. Alla punkter som ingår i ett kluster kan hämtas från data källan med hjälp av metoden `getClusterLeaves`. En konvex skrov är en polygon som omsluter en uppsättning punkter som ett elastiskt band och kan beräknas med hjälp av metoden `atlas.math.getConvexHull`.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Kluster Area konvex skrov" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se rit <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>kluster arean konvext</a> från Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Aggregera data i kluster

Kluster representeras ofta med hjälp av en symbol med antalet platser som finns i klustret, men ibland är det önskvärt att ytterligare anpassa formatet på kluster baserat på vissa mått, t. ex. den totala intäkten för alla punkter i ett kluster. Med kluster agg regeringar kan anpassade egenskaper skapas och fyllas i med en [mängd uttrycks](data-driven-style-expressions-web-sdk.md#aggregate-expression) beräkning.  Kluster agg regeringar kan definieras i `clusterProperties` alternativ för `DataSource`.

I följande exempel används ett mängd uttryck för att beräkna ett antal baserat på egenskapen entitetstyp för varje data punkt i ett kluster.

<iframe height="500" style="width: 100%;" scrolling="no" title="Kluster mängd" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se mängd med Penn <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>kluster</a> med Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [DataSource-klass](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions-objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [namn område för Atlas. matematik](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Se kod exempel för att lägga till funktioner i din app:

> [!div class="nextstepaction"]
> [Lägg till ett bubbel-lager](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett symbol lager](map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägg till ett värme kart skikt](map-add-heat-map-layer.md)
