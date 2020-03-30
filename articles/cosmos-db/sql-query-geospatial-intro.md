---
title: Geospatial- och GeoJSON-platsdata i Azure Cosmos DB
description: Förstå hur du skapar rumsliga objekt med Azure Cosmos DB och SQL API.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 59c8b31dcc8594d2cafb2db7832e290b01026f60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367592"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Geospatial- och GeoJSON-platsdata i Azure Cosmos DB

Den här artikeln är en introduktion till geospatiala funktioner i Azure Cosmos DB. För närvarande stöds lagring och åtkomst till geospatiala data endast av Azure Cosmos DB SQL API-konton. Efter att ha läst vår dokumentation om geospatial indexering kommer du att kunna svara på följande frågor:

* Hur lagrar jag spatialdata i Azure Cosmos DB?
* Hur kan jag fråga geospatiala data i Azure Cosmos DB i SQL och LINQ?
* Hur aktiverar eller inaktiverar jag spatial indexering i Azure Cosmos DB?

## <a name="introduction-to-spatial-data"></a>Introduktion till rumsliga data

Rumsliga data beskriver objektens position och form i rymden. I de flesta program motsvarar dessa objekt på jorden och geospatiala data. Rumsliga data kan användas för att representera platsen för en person, en plats av intresse, eller gränsen för en stad, eller en sjö. Vanliga användningsfall innebär ofta närhetsfrågor, till exempel "hitta alla kaféer nära min nuvarande plats".

Azure Cosmos DB:s SQL API stöder två rumsliga datatyper: **geometridatatypen** och geografidatatypen. **geography**

- **Geometritypen** representerar data i ett euklidiskt (platt) koordinatsystem
- **Geografitypen** representerar data i ett koordinerat koordinssystem.

## <a name="supported-data-types"></a>Datatyper som stöds

Azure Cosmos DB stöder indexering och frågor av geospatiala punktdata som representeras med hjälp av [GeoJSON-specifikationen](https://tools.ietf.org/html/rfc7946). GeoJSON-datastrukturer är alltid giltiga JSON-objekt, så att de kan lagras och efterfrågas med Hjälp av Azure Cosmos DB utan några specialiserade verktyg eller bibliotek.

Azure Cosmos DB stöder följande rumsliga datatyper:

- Punkt
- LineString (Radsträng)
- Polygon
- MultiPolygon (multipolygon)

### <a name="points"></a>Punkter

En **punkt** betecknar en enda position i rymden. I geospatiala data representerar en punkt den exakta platsen, som kan vara en gatuadress för en livsmedelsbutik, en kiosk, en bil eller en stad.  En punkt representeras i GeoJSON (och Azure Cosmos DB) med hjälp av dess koordinatpar eller longitud och latitud.

Här är ett exempel JSON för en punkt:

**Poäng i Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Rumsliga datatyper kan bäddas in i ett Azure Cosmos DB-dokument som visas i det här exemplet med en användarprofil som innehåller platsdata:

**Använda profil med plats lagrad i Azure Cosmos DB**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Punkter i ett geometrikoordinatsystem

För **geometridatatypen** anger GeoJSON-specifikationen den vågräta axeln först och den lodräta axeln i andra.

### <a name="points-in-a-geography-coordinate-system"></a>Punkter i ett geografikoordinatsystem

För **geografidatatypen** anger GeoJSON-specifikationen longitud först och latitudsekret. Liksom i andra mappningsprogram är longitud och latitud vinklar och representerade i form av grader. Longitudvärden mäts från Prime Meridian och är mellan -180 grader och 180,0 grader, och latitudvärden mäts från ekvatorn och är mellan -90,0 grader och 90,0 grader.

Azure Cosmos DB tolkar koordinater som representeras enligt WGS-84 referenssystem. Se nedan för mer information om koordinatreferenssystem.

### <a name="linestrings"></a>LineStrings (Radsträngar)

**LineStrings** representerar en serie med två eller flera punkter i rymden och de linjesegment som ansluter dem. I geospatiala data används LineStrings ofta för att representera motorvägar eller floder.

**LineStrings i GeoJSON**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Polygoner

En **Polygon** är en gräns för anslutna punkter som utgör en stängd LineString. Polygoner används ofta för att representera naturliga formationer som sjöar eller politiska jurisdiktioner som städer och stater. Här är ett exempel på en Polygon i Azure Cosmos DB:

**Polygoner i GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> GeoJSON-specifikationen kräver att för giltiga polygoner ska det sista koordinatparet vara detsamma som den första för att skapa en sluten form.
>
> Punkter i en Polygon måste anges i moturs ordning. En Polygon som anges i medurs ordning representerar inversen av regionen inom den.
>
>

### <a name="multipolygons"></a>MultiPolygons

En **MultiPolygon** är en matris med noll eller fler polygoner. **MultiPolygoner** kan inte överlappa sidor eller ha något gemensamt område. De kan röra vid en eller flera punkter.

**MultiPolygons i GeoJSON**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Koordinera referenssystem

Sedan forma av jorden är ojämnt, föreställs koordinater av geografiska geospatial data i många koordinat hänvisar till system (CRS), varje med deras eget, tar fram av hänvisar till och enheter av mätningen. Till exempel är "National Grid of Britain" ett referenssystem är korrekt för Storbritannien, men inte utanför det.

Den mest populära CRS som används idag är World Geodetic System [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). GPS-enheter och många karttjänster, inklusive Google Maps och Bing Maps API:er, använder WGS-84. Azure Cosmos DB stöder indexering och frågor av geografiska geospatiala data med endast WGS-84 CRS.

## <a name="creating-documents-with-spatial-data"></a>Skapa dokument med rumsliga data
När du skapar dokument som innehåller GeoJSON-värden indexeras de automatiskt med ett rumsligt index i enlighet med behållarens indexeringsprincip. Om du arbetar med en Azure Cosmos DB SDK på ett dynamiskt skrivet språk som Python eller Node.js, måste du skapa giltig GeoJSON.

**Skapa dokument med geospatiala data i Node.js**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Om du arbetar med SQL-API:erna `Point`kan `LineString` `Polygon`du `MultiPolygon` använda , `Microsoft.Azure.Cosmos.Spatial` , och klasserna i namnområdet för att bädda in platsinformation i programobjekten. Dessa klasser hjälper till att förenkla serialisering och avserialisering av rumsliga data i GeoJSON.

**Skapa dokument med geospatiala data i .NET**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Om du inte har latitud- och longitudinformation, men har fysiska adresser eller platsnamn som ort eller land/region, kan du slå upp de faktiska koordinaterna med hjälp av en geokodningstjänst som Bing Maps REST Services. Läs mer om Geokodning av Bing Maps [här](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du kommer igång med geospatial support i Azure Cosmos DB kan du nästa du:

* Läs mer om [Azure Cosmos DB Query](sql-query-getting-started.md)
* Läs mer om [att fråga spatialdata med Azure Cosmos DB](sql-query-geospatial-query.md)
* Läs mer om [Index spatial data med Azure Cosmos DB](sql-query-geospatial-index.md)