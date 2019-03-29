---
title: Klustring punkt data i Azure Maps | Microsoft Docs
description: Så här klusterdata punkt i webb-SDK
author: rbrundritt
ms.author: richbrun
ms.date: 03/27/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d4dc6f0c8fd2dff74a1997c9dca5a31abc70c03a
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580830"
---
# <a name="clustering-point-data"></a>Klustring punkt data

När visualisera många datapunkter på kartan datapunkter överlappar varandra, kartan ser stökig och blir det svårt att se och använda. Klustring av data för punkt kan användas för att förbättra den här användarupplevelsen. Klustring punkt data är processen att kombinera punkt data som är nära varandra och som representerar dem på kartan som en enskild klustrad datapunkt. När du zoomar in på kartan, dela klustren upp till deras enskilda datapunkter.

## <a name="enabling-clustering-on-a-data-source"></a>Aktiverar kluster på en datakälla

Klustring kan enkelt aktiveras på den `DataSource` klassen genom att ange den `cluster` på Sant. Dessutom pixel radien att välja Orienteringspunkter ska kombineras till ett kluster kan anges med hjälp av den `clusterRadius` och en zoomnivå kan anges som du vill inaktivera klustring logic med den `clusterMaxZoom` alternativet. Här är ett exempel på hur du aktiverar kluster i en datakälla.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximium zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15 
});
```

> [!TIP]
> Om två datapunkter ligger nära varandra på grund av, är det möjligt klustret kommer aldrig att dela upp, oavsett hur nära användaren Zooma i. För att lösa det, kan du ange den `clusterMaxZoom` möjlighet att datakällan anger vid zoomningsnivån inaktiverar klustring logiken och bara visa allt.

Den `DataSource` klassen har också följande relaterade till kluster:

| Metod | Returtyp | Beskrivning |
|--------|-------------|-------------|
| getClusterChildren(clusterId: number) | Löftet&lt;funktionen&lt;geometri, alla&gt; \| form&gt; | Hämtar underordnade grupper till viss klustret på nästa zoomnivån. De här underordnade kan vara en kombination av former och subclusters. Subclusters blir funktioner med egenskaper som matchar ClusteredProperties. |
| getClusterExpansionZoom(clusterId: number) | Löftet&lt;tal&gt; | Beräknar en zoomnivå som klustret ska starta expandera eller dela upp. |
| getClusterLeaves (clusterId: number, gränsen: number, förskjutning: tal) | Löftet&lt;funktionen&lt;geometri, alla&gt; \| form&gt; | Hämtar alla punkter i ett kluster. Ange den `limit` att returnera en delmängd av punkterna och använda den `offset` vill bläddra igenom punkterna. |

## <a name="display-clusters-using-a-bubble-layer"></a>Visa kluster med ett bubbeldiagram lager

Ett bubbeldiagram-lager är ett bra sätt att återge klustrade punkter som du kan enkelt skala radien och ändra färgen dem baserat på antalet punkter i klustret med hjälp av ett uttryck. Vid visning av kluster med ett bubbeldiagram lager måste använda du också ett separat lager för att återge oklustrade datapunkter. Det är ofta bra att även att kunna visa storleken på klustret ovanpå bubblorna. En symbol lager med text och ingen ikon kan användas för att uppnå det här beteendet. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Grundläggande bubbla layer klustring" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>grundläggande bubbla layer klustring</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Visa kluster med en symbol-lager

När visualisera dina data punkt med Symbol-lager som standard döljs automatiskt symboler som överlappar varandra för att skapa en tydligare upplevelse, men det kanske inte önskat upplevelse om du vill se tätheten av data pekar på kartan. Ange den `allowOverlap` alternativet lager som symbolen `iconOptions` egenskap `true` inaktiverar den här upplevelsen men leder till alla symboler som visas. Med hjälp av kluster kan du se tätheten av alla data när du skapar en bra ren användarupplevelse. Anpassade symboler används för att representera kluster och enskilda datapunkter i det här exemplet.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Klustrade Symbol-lager" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>klustrade symbolen layer</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Layer-kluster och termiska kartor

Den termiska kartor är ett bra sätt att visa tätheten av data på kartan. Den här visualiseringen kan hantera ett stort antal datapunkter på egen hand, men den kan hantera ännu mer data om datapunkter är klustrade och klusterstorleken används som vikten för den termiska kartan. Ange den `weight` alternativet på den termiska kartskiktet till `['get', 'point_count']` att uppnå detta. När klustret radius är liten, den termiska kartan ser ut nästan identisk med en termisk karta med det oklustrade datapunkten men ger mycket bättre prestanda. Men ju mindre kluster radius, desto mer exakt den termiska kartan tas dock med mindre en prestanda-förmånen.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Klustret viktad termisk karta" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>kluster viktad termisk karta</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Mushändelser på klustrade datapunkter

När mushändelser inträffar på ett lager med klustrade datapunkter, returneras den klustrade datapunkten på händelsen som ett GeoJSON point funktionen-objekt. Den här punkt-funktionen har följande egenskaper:

| Egenskapsnamn | Type | Beskrivning |
|---------------|------|-------------|
| kluster | boolesk | Anger om funktionen representerar ett kluster. |
| cluster_id | sträng | Ett unikt ID för det kluster som kan användas med datakällan `getClusterExpansionZoom`, `getClusterChildren`, och `getClusterLeaves` metoder. |
| point_count | nummer | Antal punkter som innehåller klustret. |
| point_count_abbreviated | sträng | En sträng som abbreviates point_count värdet om det är långt. (exempel: 4 000 blir 4K) |

Det här exemplet tar ett bubbeldiagram lager som återger kluster punkter och lägger till en click-händelse som när det utlöses, beräkna och Zooma på kartan till nästa zoomnivån då klustret bryts skillnad med hjälp av den `getClusterExpansionZoom` -metoden för den `DataSource` klassen och `cluster_id` egenskapen för den klickade på klustrade datapunkt. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Klustret getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/moZWeV/'>kluster getClusterExpansionZoom</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Visningsområdet för kluster 

Punkt-data som representerar ett kluster är utspridd över ett område. I det här exemplet när musen hovrar över ett kluster, enskilda data punkter som den innehåller (löv) kommer används för att beräkna en konvex skrov och visas på kartan för att visa området. Alla punkter i ett kluster kan hämtas från datakällan med den `getClusterLeaves` metoden. Fartygsskrov konvex är en polygon som omsluter en uppsättning punkter som ett band av elastisk och kan beräknas med hjälp av den `atlas.math.getConvexHull` metoden.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Klustret området konvex skrov" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>kluster området konvex skrov</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [DataSource-klass](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions objekt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas.Math namnområde](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Se kodexempel för att lägga till funktioner i din app:

> [!div class="nextstepaction"]
> [Lägg till ett bubbeldiagram lager](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Lägg till en symbol-lager](map-add-pin.md)

> [!div class="nextstepaction"]
> [Lägg till ett lager för den termiska kartan](map-add-heat-map-layer.md)
