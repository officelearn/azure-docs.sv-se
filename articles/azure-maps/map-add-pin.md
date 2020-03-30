---
title: Lägga till ett symbollager på en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om hur du använder symbollagret för att anpassa en symbol och lägga till symboler på en karta med Hjälp av Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b8d131dcc798fb2fe1d4bb650cd5b0a68903381b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209706"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Lägga till ett symbollager på en karta

Anslut en symbol till en datakälla och använd den för att återge en ikon eller en text vid en given punkt. 

Symbollager återges med WebGL. Använd ett symbollager för att återge stora samlingar av punkter på kartan. Jämfört med HTML-markör återger symbollagret ett stort antal punktdata på kartan, med bättre prestanda. Symbollagret stöder dock inte traditionella CSS- och HTML-element för styling.  

> [!TIP]
> Symbollager återges som standard koordinaterna för alla geometrier i en datakälla. Om du vill begränsa lagret så att det `filter` bara återger `['==', ['geometry-type'], 'Point']` punktgeometrifunktioner som anger egenskapen för lagret till eller `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` om du vill kan du även inkludera MultiPoint-funktioner.

Kartbilderna sprite manager läser in anpassade bilder som används av symbollagret. Den stöder följande bildformat:

- Jpeg
- PNG
- Svg
- BMP
- GIF (inga animeringar)

## <a name="add-a-symbol-layer"></a>Lägga till ett symbolskikt

Innan du kan lägga till ett symbollager på kartan måste du göra ett par steg. Skapa först en datakälla och lägg till den på kartan. Skapa ett symbollager. Skicka sedan datakällan till symbollagret för att hämta data från datakällan. Slutligen lägger du till data i datakällan så att det finns något att återge. 

Koden nedan visar vad som ska läggas till på kartan efter att den har lästs in. Det här exemplet återger en enda punkt på kartan med hjälp av ett symbollager. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Det finns fyra olika typer av punktdata som kan läggas till på kartan:

- GeoJSON Point geometri - Det här objektet innehåller bara en koordinat av en punkt och inget annat. Hjälpklassen `atlas.data.Point` kan användas för att enkelt skapa dessa objekt.
- GeoJSON MultiPoint-geometri - Det här objektet innehåller koordinaterna för flera punkter och inget annat. Hjälpklassen `atlas.data.MultiPoint` kan användas för att enkelt skapa dessa objekt.
- GeoJSON-funktion - Det här objektet består av alla GeoJSON-geometri och en uppsättning egenskaper som innehåller metadata som är associerade med geometrin. Hjälpklassen `atlas.data.Feature` kan användas för att enkelt skapa dessa objekt.
- `atlas.Shape`liknar GeoJSON-funktionen. Båda består av en GeoJSON-geometri och en uppsättning egenskaper som innehåller metadata som är associerade med geometrin. Om ett GeoJSON-objekt läggs till i en datakälla kan det enkelt återges i ett lager. Men om egenskapen koordinater för det GeoJSON-objektet uppdateras ändras inte datakällan och kartan. Det beror på att det inte finns någon mekanism i JSON-objektet för att utlösa en uppdatering. Formklassen innehåller funktioner för uppdatering av data som den innehåller. När en ändring görs meddelas och uppdateras datakällan och kartan automatiskt. 

Följande kodexempel skapar en GeoJSON Point-geometri och skickar den `atlas.Shape` till klassen för att göra det enkelt att uppdatera. Kartans mittpunkt används ursprungligen för att återge en symbol. En klickhändelse läggs till på kartan så att koordinaterna för musen `setCoordinates` används med formerfunktionen när den utlöses. Muskoordinaterna registreras vid tidpunkten för klickhändelsen. Sedan `setCoordinates` uppdaterar platsen för symbolen på kartan.

<br/>

<iframe height='500' scrolling='no' title='Växla stiftplats' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>pennväxlingsstiftsplatsen</a> av<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Som standard optimerar symbollager återgivningen av symboler genom att dölja symboler som överlappar varandra. När du zoomar in blir de dolda symbolerna synliga. Om du vill inaktivera den här funktionen `allowOverlap` och återge alla symboler hela tiden anger du egenskapen för `iconOptions` alternativen till `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Lägga till en anpassad ikon i ett symbollager

Symbollager återges med WebGL. Därför måste alla resurser, till exempel ikonbilder, läsas in i WebGL-kontexten. Det här exemplet visar hur du lägger till en anpassad ikon i kartresurserna. Den här ikonen används sedan för att återge punktdata med en anpassad symbol på kartan. Symbollagrets `textField` egenskap kräver att ett uttryck anges. I det här fallet vill vi göra temperaturegenskapen. Eftersom temperaturen är ett tal måste den konverteras till en sträng. Dessutom vill vi lägga till "°F" till den. Ett uttryck kan användas för att göra denna sammanfogning. `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Bildikon för anpassad symbol' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se ikonen för <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>bild av</a> den<a href='https://codepen.io/azuremaps'>@azuremaps</a>anpassade pennan från Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Azure Maps web SDK innehåller flera anpassningsbara avbildningsmallar som du kan använda med symbollagret. Mer information finns i dokumentet [Så här använder du bildmallar.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-symbol-layer"></a>Anpassa ett symbollager 

Symbollagret har många formateringsalternativ tillgängliga. Här är ett verktyg för att testa dessa olika styling alternativ.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för symbollager' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för <a href='https://codepen.io/azuremaps/pen/PxVXje/'>pennsymbollager</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> När du bara vill återge text med ett symbollager `image` kan du dölja `'none'`ikonen genom att ange ikonens egenskaper till .

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [SymbolLager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TextAlternativ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Se följande artiklar för fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett popup-fönster](map-add-popup.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Så här använder du avbildningsmallar](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](map-add-shape.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Lägg till HTML-tillverkare](map-add-bubble-layer.md)
