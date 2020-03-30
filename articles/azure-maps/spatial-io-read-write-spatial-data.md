---
title: Läsa och skriva rumsliga data | Microsoft Azure Maps
description: Lär dig hur du läser och skriver data med hjälp av spatial IO-modulen som tillhandahålls av Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4c47335689401ebce98224992c74c3396821a1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334161"
---
# <a name="read-and-write-spatial-data"></a>Läsa och skriva rumsliga data

I tabellen nedan visas de rumsliga filformat som stöds för att läsa och skriva operationer med modulen Spatial IO.

| Dataformat       | Läsa | Skriva |
|-------------------|------|-------|
| GeoJSON (geografiskt)           | ✓  |  ✓  |
| Georss            | ✓  |  ✓  |
| Gml               | ✓  |  ✓  |
| Gpx               | ✓  |  ✓  |
| Kml               | ✓  |  ✓  |
| Kmz               | ✓  |  ✓  |
| Rumslig CSV       | ✓  |  ✓  |
| Välkänd text   | ✓  |  ✓  |

I nästa avsnitt beskrivs alla olika verktyg för att läsa och skriva rumsliga data med hjälp av modulen Spatial IO.

## <a name="read-spatial-data"></a>Läsa rumsliga data

Funktionen `atlas.io.read` är den huvudsakliga funktion som används för att läsa vanliga rumsliga dataformat som KML, GPX, GeoRSS, GeoJSON och CSV-filer med rumsliga data. Den här funktionen kan också läsa komprimerade versioner av dessa format, som en zip-fil eller en KMZ-fil. KMZ-filformatet är en komprimerad version av KML som även kan innehålla tillgångar som bilder. Du kan också läsa-funktionen ta in en URL som pekar på en fil i något av dessa format. Webbadresser bör finnas på en CORS-aktiverad slutpunkt, eller så ska en proxytjänst tillhandahållas i läsalternativen. Proxytjänsten används för att läsa in resurser på domäner som inte är CORS-aktiverade. Läsfunktionen returnerar ett löfte om att lägga till bildikonerna på kartan och bearbetar data asynkront för att minimera påverkan på gränssnittstråden.

När du läser en komprimerad fil, antingen som en zip eller en KMZ, kommer den att packas upp och skannas efter den första giltiga filen. Doc.kml eller en fil med annat giltigt tillägg, till exempel: .kml, .xml, geojson, .json, .csv, .tsv eller .txt. Sedan är bilder som refereras i KML- och GeoRSS-filer förinläst för att säkerställa att de är tillgängliga. Oåtkomliga bilddata kan läsa in en alternativ reservbild eller tas bort från formatmallarna. Bilder som extraherats från KMZ-filer konverteras till data-URI:er.

Resultatet från läsfunktionen är `SpatialDataSet` ett objekt. Det här objektet utökar klassen GeoJSON FeatureCollection. Det kan enkelt skickas `DataSource` in i en as-is för att återge sina funktioner på en karta. Den `SpatialDataSet` innehåller inte bara funktionsinformation, men det kan också innehålla KML marköverlägg, bearbetningsmått och andra detaljer som beskrivs i följande tabell.

| Egenskapsnamn | Typ | Beskrivning | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Begränsningsramen för alla data i datauppsättningen. |
| `features` | `Feature[]` | GeoJSON-funktioner i datauppsättningen. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | En rad KML GroundOverlays. |
| `icons` | Poststräng,&lt;sträng&gt; | En uppsättning ikon-URL:er. Nyckel = ikonnamn, Värde = URL. |
| properties | valfri | Egenskapsinformation som tillhandahålls på dokumentnivå för en rumslig datauppsättning. |
| `stats` | `SpatialDataSetStats` | Statistik om innehållet och bearbetningstiden för en rumslig datauppsättning. |
| `type` | `'FeatureCollection'` | Skrivskyddat geojsontypsvärde. |

## <a name="examples-of-reading-spatial-data"></a>Exempel på läsning av rumsliga data

Följande kod visar hur du läser en spatial datauppsättning och `SimpleDataLayer` återge den på kartan med hjälp av klassen. Koden använder en GPX-fil som pekas ut av en URL.

<br/>

<iframe height='500' scrolling='no' title='Läs in rumsliga data enkel' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>läses in spatialdata enkel</a> av Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nästa kod demo visar hur man läser och laddar KML, eller KMZ, till kartan. KML kan innehålla marköverlägg, som kommer att `ImageLyaer` `OgcMapLayer`vara i form av en eller . Dessa överlägg måste läggas till på kartan separat från funktionerna. Om datauppsättningen har anpassade ikoner måste dessa ikoner dessutom läsas in i kartresurserna innan funktionerna läses in.

<br/>

<iframe height='500' scrolling='no' title='Ladda KML på kartan' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pen <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>load KML Onto</a> Map<a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Du kan också tillhandahålla en proxytjänst för åtkomst till domänövergripande tillgångar som kanske inte har CORS aktiverat. Läsfunktionen försöker komma åt filer på en annan domän med CORS först. Efter första gången den misslyckas med att komma åt någon resurs på en annan domän med CORS kommer det bara att begära ytterligare filer om en proxytjänst har tillhandahållits. Läsfunktionen lägger till filens URL till slutet av proxy-URL:en. Det här kodavsnittet visar hur du skickar en proxytjänst till läsfunktionen:

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

Demon nedan visar hur man läser en avgränsad fil och återge den på kartan. I det här fallet använder koden en CSV-fil som har rumsliga datakolumner.

<br/>

<iframe height='500' scrolling='no' title='Lägga till en avgränsad fil' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>Lägg till en avgränsad</a> fil<a href='https://codepen.io/azuremaps'>@azuremaps</a>av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Skriv rumsliga data

Det finns två huvudsakliga skrivfunktioner i den rumsliga IO-modulen. Funktionen `atlas.io.write` genererar en sträng, `atlas.io.writeCompressed` medan funktionen genererar en komprimerad zip-fil. Den komprimerade zip-filen skulle innehålla en textbaserad fil med rumsliga data i den. Båda dessa funktioner returnerar ett löfte om att lägga till data i filen. Och de båda kan skriva någon `SpatialDataSet`av `DataSource` `ImageLayer`följande `OgcMapLayer`data: , , , funktionsinsamling, funktion, geometri eller en matris med en kombination av dessa datatyper. När du skriver med båda funktionerna kan du ange önskat filformat. Om filformatet inte anges skrivs data som KML.

Verktyget nedan visar de flesta av de skrivalternativ `atlas.io.write` som kan användas med funktionen.

<br/>

<iframe height='700' scrolling='no' title='Skrivalternativ för rumsliga data' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för skriva av pen<a href='https://codepen.io/azuremaps'>@azuremaps</a>spatial <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>data</a> av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Exempel på att skriva rumsliga data

Med följande exempel kan du dra och släppa och sedan läsa in rumsliga filer på kartan. Du kan exportera GeoJSON-data från kartan och skriva dem i ett av de rumsliga dataformat som stöds som en sträng eller som en komprimerad fil.

<br/>

<iframe height='700' scrolling='no' title='Dra och släppa rumsliga filer på kartan' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>Dra och släpp rumsliga filer på kartan</a> med Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Du kan också tillhandahålla en proxytjänst för åtkomst till domänövergripande tillgångar som kanske inte har CORS aktiverat. Det här kodavsnittet visar att du kan infoga en proxytjänst:

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Läsa och skriva välkänd text (WKT)

[Välkänt text](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) är en OGC-standard (Open Geospatial Consortium) för att representera rumsliga geometrier som text. Många geospatiala system stöder WKT, till exempel Azure SQL och Azure PostgreSQL med hjälp av PostGIS-insticksprogrammet. Liksom de flesta OGC-standarder formateras koordinaterna som "longitudfrihet" för att anpassa sig till "x y"-konventionen. Som ett exempel kan en punkt vid longitud -110 `POINT(-110 45)` och latitud 45 skrivas som med WKT-formatet.

Välkänd text kan läsas `atlas.io.ogc.WKT.read` med hjälp av `atlas.io.ogc.WKT.write` funktionen och skrivas med hjälp av funktionen.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Exempel på läsning och skrivning av välkänd text (WKT)

Följande kod visar hur du läser den `POINT(-122.34009 47.60995)` välkända textsträngen och återge den på kartan med hjälp av ett bubbellager.

<br/>

<iframe height='500' scrolling='no' title='Läs välkänd text' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>Läs välkänd text</a> av<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Följande kod visar att läsa och skriva välkänd text fram och tillbaka.

<br/>

<iframe height='700' scrolling='no' title='Läsa och skriva välkänd text' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>Läs och skriv välkänd text</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>av Azure Maps ( ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Läsa och skriva GML

GML är en spatial XML-filspecifikation som ofta används som ett tillägg till andra XML-specifikationer. GeoJSON-data kan skrivas som XML `atlas.io.core.GmlWriter.write` med GML-taggar med hjälp av funktionen. XML-koden som innehåller GML kan `atlas.io.core.GmlReader.read` läsas med hjälp av funktionen. Läsfunktionen har två alternativ:

- Alternativet `isAxisOrderLonLat` - Axelordningen för koordinaterna "latitud, longitud" eller "longitud, latitud" kan variera mellan datauppsättningar och den är inte alltid väl definierad. Som standard gml-läsaren läser koordinatdata som "latitud, longitud", men om du ställer in det här alternativet till true kommer det att läsas som "longitud, latitud".
- Alternativet `propertyTypes` - Det här alternativet är en tabell för nyckelvärdesökning där nyckeln är namnet på en egenskap i datauppsättningen. Värdet är den objekttyp som ska kastas värdet till vid parsning. Typvärdena som `string`stöds `number` `boolean`är: `date`, , och . Om en egenskap inte finns i uppslagstabellen eller om typen inte har definierats tolkas egenskapen som en sträng.

Funktionen `atlas.io.read` fungerar som `atlas.io.core.GmlReader.read` standard när den upptäcker att indata är XML, men data är inte ett av de andra stöden för spatial XML-format.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [atlas.io statiska funktioner](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader (0)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter (Brännare)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [atlas.io.ogc.WKT-funktioner](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Se följande artiklar för fler kodexempel att lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägga till ett OGC-kartlager](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Ansluta till en WFS-tjänst](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Utnyttja kärnverksamheten](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Information om dataformat som stöds](spatial-io-supported-data-format-details.md)
