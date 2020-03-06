---
title: Läs-och skriv spatialdata | Microsoft Azure Maps
description: Lär dig mer om att läsa och skriva data med hjälp av den spatiala IO-modulen, som tillhandahålls av Azure Maps Web SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 458b307cf1158c467100e032e3f789462e8fdcca
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370913"
---
# <a name="read-and-write-spatial-data"></a>Läsa och skriva spatialdata

I tabellen nedan visas de spatiala fil format som stöds för läsning och skrivning av åtgärder med den spatiala IO-modulen.

| Data format       | Läsa | Skriva |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| Spatial CSV       | ✓  |  ✓  |
| Välkänd text   | ✓  |  ✓  |

I följande avsnitt beskrivs alla olika verktyg för att läsa och skriva spatialdata med hjälp av den spatiala IO-modulen.

## <a name="read-spatial-data"></a>Läs spatialdata

Funktionen `atlas.io.read` är huvud funktionen som används för att läsa vanliga avstånds data format som KML, GPX, GeoRSS, interjson och CSV-filer med spatialdata. Den här funktionen kan också läsa komprimerade versioner av dessa format, som en zip-fil eller en KMZ-fil. KMZ-filformatet är en komprimerad version av KML som också kan omfatta till gångar som bilder. Alternativt kan Read-funktionen ta i en URL som pekar på en fil i något av dessa format. URL: er ska finnas på en CORs-aktiverad slut punkt, eller så ska en proxyserver anges i Läs alternativen. Proxy-tjänsten används för att läsa in resurser i domäner som inte är CORs-aktiverade. Funktionen Read returnerar ett löfte för att lägga till avbildnings ikonerna i kartan och bearbetar data asynkront för att minimera påverkan till UI-tråden.

När du läser en komprimerad fil, antingen som en zip-eller KMZ, kommer den att zippas och genomsökas efter den första giltiga filen. Till exempel doc. KML eller en fil med ett annat giltigt tillägg, till exempel:. KML,. XML, interjson,. JSON,. csv,. tsv eller. txt. Sedan är bilder som refereras i KML-och GeoRSS-filer förinstallerade för att säkerställa att de är tillgängliga. Otillgängliga bilddata kan läsa in en alternativ återställnings avbildning eller tas bort från formaten. Avbildningar som extraherats från KMZ-filer konverteras till data-URI: er.

Resultatet från funktionen Read är ett `SpatialDataSet`-objekt. Det här objektet utökar FeatureCollection-klassen för polyjson. Det kan enkelt skickas till en `DataSource` som är att återge dess funktioner på en karta. `SpatialDataSet` innehåller inte bara funktions information, men den kan även innehålla KML-Språköverlägg, bearbeta mått och annan information som beskrivs i följande tabell.

| Egenskapsnamn | Typ | Beskrivning | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Avgränsnings ruta för alla data i data uppsättningen. |
| `features` | `Feature[]` | Interjson-funktioner i data uppsättningen. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | En matris med KML-GroundOverlays. |
| `icons` | Registrera&lt;sträng, sträng&gt; | En uppsättning ikon-URL: er. Nyckel = ikon namn, värde = URL. |
| properties | valfri | Egenskaps information som anges på dokument nivå för en spatial data uppsättning. |
| `stats` | `SpatialDataSetStats` | Statistik om innehåll och bearbetnings tid för en spatial data uppsättning. |
| `type` | `'FeatureCollection'` | Skrivskyddat värde för polyjson-typ. |

## <a name="examples-of-reading-spatial-data"></a>Exempel på att läsa spatialdata

Följande kod visar hur du läser en enkel spatial data uppsättning och återger den på kartan med klassen `SimpleDataLayer`. Koden använder en GPX-fil som en URL pekar på.

<br/>

<iframe height='500' scrolling='no' title='Enkel inläsning av spatialdata' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se de spatialdata som är <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>enkla</a> med Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Nästa kod demonstration visar hur du läser och läser in KML, eller KMZ, på kartan. KML kan innehålla mark överlägg som är i form av en `ImageLyaer` eller `OgcMapLayer`. Dessa överlägg måste läggas till i kartan separat från funktionerna. Om data uppsättningen däremot har anpassade ikoner, måste dessa ikoner läsas in på Maps-resurserna innan funktionerna läses in.

<br/>

<iframe height='500' scrolling='no' title='Läs in KML på kartan' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se KML för Penn <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>inläsning på kartan</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Du kan också ange en proxy-tjänst för åtkomst till kors domän till gångar som kanske inte har CORs aktiverat. Det här kodfragmentet visar att du kan tillhandahålla en proxy-tjänst:

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

//Read a KML file from a URL or pass in a raw KML string.
atlas.io.read('https://s3-us-west-2.amazonaws.com/s.cdpn.io/1717245/2007SanDiegoCountyFires.kml', {
    //Provide a proxy service
    proxyService: proxyServiceUrl
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

Den senaste demon nedan visar hur du läser en avgränsad fil och återger den på kartan. I det här fallet använder koden en CSV-fil som har rums data kolumner.

<br/>

<iframe height='500' scrolling='no' title='Lägg till en avgränsad fil' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>Lägg till en avgränsad fil</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Skriv spatialdata

Det finns två huvudsakliga Skriv funktioner i den spatiala IO-modulen. Funktionen `atlas.io.write` genererar en sträng, medan funktionen `atlas.io.writeCompressed` genererar en komprimerad ZIP-fil. Den komprimerade ZIP-filen innehåller en text baserad fil med spatialdata i den. Båda dessa funktioner returnerar ett löfte för att lägga till data i filen. Och båda kan skriva någon av följande data: `SpatialDataSet`, `DataSource`, `ImageLayer`, `OgcMapLayer`, funktions samling, funktion, geometri eller en matris med valfri kombination av dessa data typer. När du skriver med hjälp av någon av funktionerna kan du ange önskat fil format. Om fil formatet inte anges skrivs data som KML.

Verktyget nedan visar de flesta av de Skriv alternativ som kan användas med funktionen `atlas.io.write`.

<br/>

<iframe height='700' scrolling='no' title='Skriv alternativ för spatialdata' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>Skriv alternativen för data skrivning</a> med penna genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Exempel på att skriva spatialdata

I följande exempel kan du dra och släppa och sedan läsa in spatialdata på kartan. Du kan exportera indata från en data Källa från kartan och skriva dem i ett av de spatialdata som stöds som en sträng eller som en komprimerad fil.

<br/>

<iframe height='700' scrolling='no' title='Dra och släpp spatialdata på kartan' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>Dra och släpp spatialdata från pennan till kartan</a> genom Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Du kan också ange en proxy-tjänst för åtkomst till kors domän till gångar som kanske inte har CORs aktiverat. Det här kodfragmentet visar att du kan inkludera en proxy-tjänst:

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

function readData(data, fileName) {
    loadingIcon.style.display = '';
    fileCount++;
    //Attempt to parse the file and add the shapes to the map.
    atlas.io.read(data, {
        //Provide a proxy service
        proxyService: proxyServiceUrl
    }).then(
        //Success
        function(r) {
            //some code goes here ...
        }
    );
}
```

## <a name="read-and-write-well-known-text-wkt"></a>Läsa och skriva välkänd text (well)

[Välkänd text](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (well) är en Open GEOSPATIAL CONSORTIUM (OGC) standard för att representera avstånds Geometries som text. Många geospatiala system stöder well, till exempel Azure SQL och Azure PostgreSQL med hjälp av PostGIS-plugin-programmet. Precis som de flesta OGC-standarder formateras koordinaterna som "longitud latitud" för att anpassas till "x"-konventionen. Som exempel kan en punkt vid longitud-110 och latitud 45 skrivas som `POINT(-110 45)` med well-formatet.

Välkänd text kan läsas med hjälp av funktionen `atlas.io.ogc.WKT.read` och skrivas med hjälp av funktionen `atlas.io.ogc.WKT.write`.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Exempel på läsning och skrivning av välkänd text (well)

Följande kod visar hur du läser den välkända text strängen `POINT(-122.34009 47.60995)` och återger den på kartan med ett bubbel-lager.

<br/>

<iframe height='500' scrolling='no' title='Läs välkänd text' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se den <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>läsbara text</a> som är välkänd för pennan genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Följande kod visar Läs-och skriv-välkänd text och tillbaka.

<br/>

<iframe height='700' scrolling='no' title='Läsa och skriva välkänd text' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Skriv- <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>och skrivbar text</a> för pennan genom att Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Läs-och skriv GML

GML är en spatial XML-filspecifikation som ofta används som ett tillägg till andra XML-specifikationer. Det går att skriva data i interjson med GML-Taggar med hjälp av funktionen `atlas.io.core.GmlWriter.write`. XML-filen som innehåller GML kan läsas med hjälp av funktionen `atlas.io.core.GmlReader.read`. Funktionen Read har två alternativ:

- Alternativet `isAxisOrderLonLat` – axel ordningen för koordinaterna "latitud, longitud" eller "longitud, latitud" kan variera mellan data mängder och det är inte alltid väl definierat. Som standard läser GMLs läsaren koordinatens data som "latitud, longitud", men om du ställer in det här alternativet på sant så läses det som "longitud, latitud".
- Alternativet `propertyTypes` – det här alternativet är en uppslags tabell med nyckel värden där nyckeln är namnet på en egenskap i data uppsättningen. Värdet är objekt typen för att konvertera värdet till vid parsning. De typ värden som stöds är: `string`, `number`, `boolean`och `date`. Om en egenskap inte finns i uppslags tabellen eller om typen inte har definierats, kommer egenskapen att parsas som en sträng.

Funktionen `atlas.io.read` kommer att använda funktionen `atlas.io.core.GmlReader.read` när den identifierar att indata är XML, men att data inte är en av de andra stödda XML-formaten för avstånd.

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [atlas.io-statiska funktioner](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas. io. OGC. well-funktioner](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägg till ett OGC-kart skikt](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Ansluta till en WFS-tjänst](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Utnyttja kärn åtgärder](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Information om data format som stöds](spatial-io-supported-data-format-details.md)
