---
title: Lägg till ett enkelt datalager | Microsoft Azure Maps
description: Lär dig hur du lägger till ett enkelt datalager med hjälp av spatial IO-modulen som tillhandahålls av Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8862c33b7660b8130f692dc4beea89a7b6b5f5ad
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804494"
---
# <a name="add-a-simple-data-layer"></a>Lägga till ett enkelt datalager

Den rumsliga IO-modulen tillhandahåller en `SimpleDataLayer` klass. Den här klassen gör det enkelt att återge formatade funktioner på kartan. Det kan även återge datauppsättningar som har formategenskaper och datauppsättningar som innehåller blandade geometrityper. Det enkla datalagret uppnår den här funktionen genom att radbryta flera renderingslager och använda formatuttryck. Formatuttrycken söker efter vanliga formategenskaper för funktionerna i dessa raderade lager. Funktionen `atlas.io.read` och `atlas.io.write` funktionen använder dessa egenskaper för att läsa och skriva format till ett filformat som stöds. När du har lagt till egenskaperna i ett filformat som stöds kan filen användas för olika ändamål. Filen kan till exempel användas för att visa de formatade funktionerna på kartan.

Förutom stylingfunktioner ger `SimpleDataLayer` den en inbyggd popup-funktion med en popup-mall. Popup-fönstret visas när du klickar på en funktion. Standardfunktionen kan inaktiveras om så önskas. Det här lagret stöder också klustrade data. När du klickar på ett kluster zoomar kartan in i klustret och expanderar det till enskilda punkter och delare.

Klassen `SimpleDataLayer` är avsedd att användas på stora datauppsättningar med många geometrityper och många stilar som används på funktionerna. När den här klassen används lägger den här klassen till en overhead med sex lager som innehåller formatuttryck. Så det finns fall när det är mer effektivt att använda kärnåtergivningslagren. Använd till exempel ett kärnlager för att återge ett par geometrietyper och några format på en funktion

## <a name="use-a-simple-data-layer"></a>Använda ett enkelt datalager

Klassen `SimpleDataLayer` används som de andra renderingslagren används. Koden nedan visar hur du använder ett enkelt datalager på en karta:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Lägg till funktioner i datakällan. Sedan kommer det enkla datalagret att räkna ut hur du bäst kan återge funktionerna. Format för enskilda funktioner kan anges som egenskaper för funktionen. Följande kod visar en GeoJSON-punktfunktion med en `color` egenskap inställd på `red`. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

Följande kod återger funktionen ovan med hjälp av det enkla datalagret. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Använda det enkla datalagret" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true"> Se pennan <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>Använd det enkla datalagret</a> från Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Den verkliga kraften i det enkla datalagret kommer när:

- Det finns flera olika typer av funktioner i en datakälla. Eller
- Funktioner i datauppsättningen har flera formategenskaper individuellt inställda på dem. Eller
- Du är inte säker på vad datauppsättningen exakt innehåller.

När du till exempel tolkar XML-datafeeds kanske du inte känner till de exakta formaten och geometrityperna för funktionerna. Följande exempel visar kraften i det enkla datalagret genom att återge funktionerna i en KML-fil. Det visar också olika alternativ som den enkla datalagerklassen tillhandahåller.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Enkla datalageralternativ" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Se alternativen för penn <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>enkelt datalager</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!NOTE]
> Det här enkla datalagret använder [klassen popup-mall](map-add-popup.md#add-popup-templates-to-the-map) för att visa KML-pratbubblor eller funktionsegenskaper som en tabell. Som standard kommer allt innehåll som återges i popup-fönstret att sandboxas inuti en iframe som en säkerhetsfunktion. Det finns dock begränsningar:
>
> - Alla skript, formulär, pekarlås och de övre navigeringsfunktionerna är inaktiverade. Länkar kan öppnas på en ny flik när du klickar på den. 
> - Äldre webbläsare som inte stöder `srcdoc` parametern på iframes kommer att begränsas till att återge en liten mängd innehåll.
> 
> Om du litar på de data som laddas i popups och eventuellt vill att dessa skript laddas i popups kunna komma åt ditt program, kan du inaktivera detta genom att ställa in popup-mallar `sandboxContent` alternativet till false. 

## <a name="default-supported-style-properties"></a>Formategenskaper som stöds som stöds

Som tidigare nämnts sveper det enkla datalagret flera av kärnåtergivningslagren: bubbel, symbol, linje, polygon och strängpressad polygon. Den använder sedan uttryck för att söka efter giltiga formategenskaper på enskilda funktioner.

Azure Maps och GitHub-formategenskaper är de två huvuduppsättningarna av egenskapsnamn som stöds. De flesta egenskapsnamn för de olika azure maps-lageralternativen stöds som formategenskaper för funktioner i det enkla datalagret. Uttryck har lagts till i vissa lageralternativ för att stödja formategenskapsnamn som ofta används av GitHub. Dessa egenskapsnamn definieras av [GitHubs GeoJSON-kartstöd](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)och de används för att formatera GeoJSON-filer som lagras och återges i plattformen. Alla GitHubs stylingegenskaper stöds i det enkla datalagret, förutom `marker-symbol` stylingegenskaperna.

Om läsaren stöter på en mindre vanlig formategenskap konverteras den till den närmaste egenskapen Azure Maps-stil. Dessutom kan standardformatuttrycken åsidosättas med `getLayers` hjälp av funktionen för det enkla datalagret och uppdatera alternativen på något av lagren.

I följande avsnitt finns information om standardformategenskaper som stöds av det enkla datalagret. Ordningen på egenskapsnamnet som stöds är också egenskapens prioritet. Om två formategenskaper har definierats för samma lageralternativ har den första i listan högre prioritet.

### <a name="bubble-layer-style-properties"></a>Egenskaper för bubbellagerformat

Om en funktion `Point` är `MultiPoint`en eller en , `image` och funktionen inte har en egenskap som skulle användas som en anpassad ikon `BubbleLayer`för att återge punkten som en symbol, kommer funktionen att återges med en .

| Alternativ för lager | Namn på egenskaper som stöds | Standardvärde |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup> `marker-size`, <sup>2</sup>, `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] `size` Värdena och `scale` betraktas som skalärvärden och multipliceras med`8`

\[2\] Om alternativet `marker-size` GitHub anges används följande värden för radien.

| Markörstorlek | Radie |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Kluster återges också med bubbellagret. Som standard är radien för `16`ett kluster inställt på . Färgen på klustret varierar beroende på antalet punkter i klustret, enligt definitionen nedan:

| Antal poäng | Color    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt;10     | `green`  |

### <a name="symbol-style-properties"></a>Egenskaper för symbolformat

Om en funktion `Point` är `MultiPoint`en eller en `image` , och funktionen och har en egenskap som skulle användas som en anpassad `SymbolLayer`ikon för att återge punkten som en symbol, kommer funktionen att återges med en .

| Alternativ för lager | Namn på egenskaper som stöds | Standardvärde |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] Om alternativet `marker-size` GitHub anges används följande värden för alternativet ikonstorlek.

| Markörstorlek | Symbolstorlek |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Om punktfunktionen är ett `point_count_abbreviated` kluster återges egenskapen som en textetikett. Ingen bild kommer att återges.

### <a name="line-style-properties"></a>Egenskaper för linjeformat

Om funktionen är `LineString` `MultiLineString`en `Polygon`, `MultiPolygon`, , eller , kommer `LineLayer`funktionen att återges med en .

| Alternativ för lager | Namn på egenskaper som stöds | Standardvärde |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Egenskaper för polygonformat

Om funktionen är `Polygon` en `MultiPolygon`eller en , och funktionen `height` antingen `height` inte har en egenskap eller egenskapen `PolygonLayer`är noll, kommer funktionen att återges med en .

| Alternativ för lager | Namn på egenskaper som stöds | Standardvärde |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Extruderade polygonstilsegenskaper

Om funktionen är `Polygon` en `MultiPolygon`eller en `height` och har en egenskap med ett värde som `PolygonExtrusionLayer`är större än 0, återges funktionen med en .

| Alternativ för lager | Namn på egenskaper som stöds | Standardvärde |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [SimpleDataLayer (SimpleDataLayer)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Se följande artiklar för fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Läsa och skriva rumsliga data](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Lägga till ett OGC-kartlager](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Ansluta till en WFS-tjänst](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Utnyttja kärnverksamheten](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Information om dataformat som stöds](spatial-io-supported-data-format-details.md)
