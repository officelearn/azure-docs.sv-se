---
title: Lägg till ett symbol lager till en karta | Microsoft Azure Maps
description: I den här artikeln får du lära dig mer om hur du använder symbol lagret för att anpassa och lägga till symboler på en karta med hjälp av Microsoft Azure Maps-webbsdk.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 744d5ecd3aab02071f7c3aaff7dd760fc14a2a62
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911166"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Lägga till ett symbol lager till en karta

En symbol kan vara ansluten till en data källa och används för att återge en ikon och/eller text vid en viss tidpunkt. Symbol lager återges med WebGL och kan användas för att återge stora samlingar av punkter på kartan. Det här lagret kan återge mycket mer punkt data på kartan med bra prestanda, än vad som kan nås med HTML-markörer. Symbol lagret stöder dock inte traditionella CSS-och HTML-element för formatering.  

> [!TIP]
> Symbol lager som standard återger koordinaterna för alla Geometries i en data källa. Om du vill begränsa lagret så att det bara återger punkt geometri funktioner ställer du in `filter` egenskapen för lagret på `['==', ['geometry-type'], 'Point']` eller `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` om du även vill inkludera MultiPoint-funktioner.

Kart hanteraren Maps bild Sprite, som används för att läsa in anpassade bilder som används av symbol skiktet, stöder följande bild format:

- -
- PNG
- SVG
- BMP
- GIF (inga animeringar)

## <a name="add-a-symbol-layer"></a>Lägga till ett symbolskikt

Om du vill lägga till ett symbol lager i kartan och återge data måste du först skapa en data källa och lägga till kartan. Ett symbol lager kan sedan skapas och skickas i data källan för att hämta data från. Slutligen måste data läggas till i data källan så att det finns något som ska återges. Följande kod visar den kod som ska läggas till i kartan när den har lästs in för att återge en enskild punkt på kartan med ett symbol lager. 

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

Det finns fyra olika typer av punkt data som kan läggas till i kartan:

- Geometrisk punkt geometri – det här objektet innehåller bara en koordinat för en punkt och inget annat. `atlas.data.Point` Helper-klassen kan användas för att enkelt skapa dessa objekt.
- Real JSON MultiPoint-geometri – det här objektet innehåller koordinaterna för flera punkter men inget annat. `atlas.data.MultiPoint` Helper-klassen kan användas för att enkelt skapa dessa objekt.
- Funktionen för geometriskt JSON – det här objektet består av valfri geometrisk geometri och en uppsättning egenskaper som innehåller metadata som är kopplade till geometrin. `atlas.data.Feature` Helper-klassen kan användas för att enkelt skapa dessa objekt.
- `atlas.Shape`-klassen liknar funktionen för geometriska JSON-funktioner i att den består av en geometrisk geometri och en uppsättning egenskaper som innehåller metadata som är kopplade till geometrin. Om ett interjson-objekt läggs till i en data källa kan det enkelt återges i ett lager, men om egenskapen koordinater för det här interjson-objektet uppdateras, ändras inte data källan och kartan eftersom det inte finns någon mekanism i JSON-objektet för att utlösa en uppdatering. Klassen Shape innehåller funktioner för att uppdatera data som den innehåller, och när en ändring görs meddelas data källan och-mappningen automatiskt och uppdateras. 

Följande kod exempel skapar en geometrisk punkt-geometri och skickar den till `atlas.Shape`-klassen så att den blir lätt att uppdatera. Mitten av kartan används inlednings vis för att återge en symbol. En klick händelse läggs till i kartan, till exempel när den utlöses, används koordinaterna där musen klickas med formen `setCoordinates` funktion som uppdaterar symbolens placering på kartan.

<br/>

<iframe height='500' scrolling='no' title='Byt PIN-sökväg' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>Fäst punkten</a> för Penn växlar genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Som standard optimerar symbol lager åter givningen av symboler genom att dölja symboler som överlappar varandra för prestanda. När du zoomar in dolda symboler blir de synliga. Om du vill inaktivera den här funktionen och återge alla symboler hela tiden, anger du `allowOverlap` egenskapen för de `iconOptions` alternativen till `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Lägga till en anpassad ikon i ett symbol lager

Symbol lager återges med WebGL. Alla resurser, till exempel ikon bilder, måste läsas in i WebGL-kontexten. Det här exemplet visar hur du lägger till en anpassad ikon i kart resurserna och sedan använder den för att återge punkt data med en anpassad symbol på kartan. Ett uttryck måste anges för egenskapen `textField` för symbol lagret. I det här fallet vill vi återge temperatur egenskapen men eftersom den är ett tal måste den konverteras till en sträng. Dessutom vill vi lägga till "°F". Ett uttryck kan användas för detta. `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ikon för anpassad symbol bild' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>bild ikonen för den anpassade symbolen</a> för penna genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Azure Maps Web SDK innehåller flera anpassningsbara bildmallar som du kan använda med symbol lagret. Mer information finns i dokumentet om [att använda bildmallar](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-symbol-layer"></a>Anpassa ett symbol lager 

Symbol skiktet har många tillgängliga format alternativ. Här är ett verktyg för att testa dessa olika format alternativ.

<br/>

<iframe height='700' scrolling='no' title='Symbol lager alternativ' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för Penn <a href='https://codepen.io/azuremaps/pen/PxVXje/'>symbol lager</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Om du bara vill rendera text med ett symbol lager kan du dölja ikonen genom att ange egenskapen `image` för de ikon alternativ som du vill `'none'`.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Skapa en data Källa](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till en popup](map-add-popup.md)

> [!div class="nextstepaction"]
> [Använd data drivna format uttryck](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Använda bildmallar](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett linje lager](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygon-lager](map-add-shape.md)

> [!div class="nextstepaction"]
> [Lägg till ett bubbel-lager](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Lägg till HTML-tillverkare](map-add-bubble-layer.md)
