---
title: Lägga till ett lager för den termiska kartan i Azure Maps | Microsoft Docs
description: Hur du lägger till ett kartskikt för den termiska kartan Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 957ce60b8519ccb1e3287232f7a5459a56b25bb7
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960623"
---
# <a name="add-a-heat-map-layer"></a>Lägga till ett heatmapskikt

Värma maps, även kallat peka densitet maps, är en typ av datavisualisering som används för att representera densitet med hjälp av en uppsättning färger. De är ofta används för att visa data ”hotspots” på en karta och är ett bra sätt att återge stora peka datauppsättningar.  Till exempel återgivningen tiotusentals punkter inom kartvyn som symboler, täcker större delen av mappningsområdet och skulle resultera i många symboler som omfattas av andra, vilket gör det svårt att få mycket insyn i data. Dock gör visualisera samma datauppsättningen som en termisk karta det enkelt att se var point-data är den densest och den relativa densiteten till andra delar. Det finns många scenarier i vilka den termiska kartor, används. Här följer några exempel;

* Temperaturdata renderas ofta som termisk karta eftersom det ger uppskattningar för vilka temperaturen mellan två datapunkter.
* Data för bruset sensorer som en termisk karta renderas inte bara visar intensiteten på bruset där sensorn är men kan också ge insikter om nedbrytning över ett avstånd. Bruset nivån på en plats som en får inte vara hög, men om täckning för brus från flera sensorer överlappar, det är möjligt att den här överlappande området kan få högre bruset och därför skulle vara synliga i den termiska kartan.
* Visualisera en GPS är trace som innehåller hastigheten som en viktad höjd karta där intensiteten för varje datapunkt baseras på hastigheten ett bra sätt att snabbt se där fordonets minskar.

> [!TIP]
> Termisk karta lager som standard renderas koordinaterna för alla geometrier i en datakälla. Att begränsa lagret så att den endast renderingar peka geometri funktioner, inställd på `filter` tillhör skiktet till `['==', '$type', 'Point']`

## <a name="add-a-heat-map-layer"></a>Lägga till ett heatmapskikt

För att rendera en datakälla med punkter som en termisk karta ska skicka din datakälla till en instans av den `HeatMapLayer` klassen och lägga till den i kartan som visas här.

<br/>

<iframe height='500' scrolling='no' title='Enkel termisk Kartskikt' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>enkel termisk Kartskiktet</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

I det här exemplet har varje punkt i den termiska radius på 10 bildpunkter alls zoomningsnivåer. När du lägger till kartskiktet den termiska kartan infogas den under etiketten lagret i det här exemplet. Detta skapar en bättre användarupplevelse som etiketter är väl synliga ovanför den termiska kartan. Data i det här exemplet kommer från den [USGS jordbävning risker programmet](https://earthquake.usgs.gov/) och består av punkter som representerar betydande jordbävningar som har inträffat under de senaste 30 dagarna.

## <a name="customizing-the-heat-map-layer"></a>Anpassa den termiska kartan lagret

I föregående exempel anpassad den termiska kartan genom att ange alternativen radius och opacitet. Termisk karta lagret innehåller flera alternativ för anpassning;

* `radius`: Definierar en pixel radius där du kan rendera varje datapunkt. Radien kan anges som ett fast antal eller som ett uttryck. Med hjälp av ett uttryck, är det möjligt att skala radien baserat på zoomningsnivån som visas representerar ett konsekvent spatial område på kartan (till exempel 5 mil radius).
* `color`: Anger hur den termiska kartan färger. En toning färgpalett används ofta för den termiska kartor, men stegvis färg färgpalett är också användbart om du vill göra den termiska kartan mer ut sluten data. Dessa färger från färgpaletten Definiera färger från lägsta värdet för maximal densitet. Färgvärden för den termiska kartor har angetts som ett uttryck på den `heatmap-density` värde. Färgen vid index 0 i ett toning uttryck eller en steg-färg standardfärgen definierar färg i området där det finns inga data och kan användas för att definiera en bakgrundsfärg. Många föredrar att ställa in det här värdet för transparent eller en delvis transparent svart. Här följer exempel på färg-uttryck

| Toningsslut-uttryck | Uttryck för stegvis | 
|---------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'interpolera'<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[”linjär'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[”den termiska kartan densitet'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, 'transparent',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, 'lila',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, '#fb00fb'<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, '#00c3ff'<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;steg,<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[”den termiska kartan densitet'\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;transparent,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, 'mörkblå',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, 'mörkblå',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, ”grön”,<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, ”gult”<br/>&nbsp;&nbsp;&nbsp;&nbsp;1,00, ”red”<br/>\] |   

* `opacity`: Anger hur täckande eller genomskinlig termisk karta skiktet är.
* `intensity`: Gäller en multiplikator vikten för varje datapunkt för att öka övergripande intensiteten av den termiska kartan. Det här hjälper dig att göra små skillnader i vikten för datapunkter blir det lättare att visualisera.
* `weight`: Som standard alla datapunkter har en vikt på 1, alltså alla datapunkter viktas likvärdigt. Alternativet vikt fungerar som en multiplikator och kan anges som ett tal eller ett uttryck. Om ett tal har angetts som vikten, exempelvis 2, skulle det vara detsamma som att placera varje datapunkt på kartan två gånger, vilket fördubblar densiteten. Ange alternativet vikt till ett antal renderas för den termiska kartan på ett liknande sätt att använda alternativet intensiteten. Men om ett uttryck används kan vikten för varje datapunkt baseras på vissa mått i egenskaperna för varje datapunkt. Ta jordbävning data varje datapunkt exempelvis representerar en jordbävning. Ett viktigt mått när varje datapunkt jordbävning har, är ett värde för omfattning. Jordbävningar inträffa när som helst, men de flesta har en låg omfattning och ännu inte tyckte. Med hjälp av magnitude värdet i ett uttryck som ska tilldelas varje datapunkt vikten gör att mer betydande jordbävningar bättre kan representeras i den termiska kartan.
* Förutom grundläggande layer-alternativ, Min/max Zooma, synliga och filtrera, det finns också en `source` om du vill uppdatera datakällan och `source-layer` om datakällan är en vektor panelen källa.

Här är ett verktyg för att testa olika termisk karta layer alternativ.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för den termiska kartan Bildrutsskikt' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>värma kartan alternativ för Bildrutsskikt</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Genom att aktivera klustring på datakällan, grupperas som ligger nära varandra som en klustrad. Antalet punkt i varje kluster kan användas som Viktuttrycket för den termiska kartan och minska antalet punkter som måste vara återges. Antalet punkt i ett kluster som är lagrad i en `point_count property` av funktionen punkt som visas nedan. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Om klustring radien är endast ett fåtal bildpunkter det ska vara lite visual skillnad renderingen. En större radie kommer gruppera flera punkter i varje kluster och förbättra prestandan för den termiska kartan, men har den en märkbar blir skillnaderna.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Flera kodexempel för att lägga till i dina kartor, finns i följande artiklar:

> [!div class="nextstepaction"]
> [Lägg till en symbol-lager](./map-add-pin.md)

