---
title: Geospatiala och geospatiala JSON-plats data i Azure Cosmos DB
description: Lär dig hur du skapar spatiala objekt med Azure Cosmos DB och SQL API.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 0fe83b8e28b96f1d89a7c98cfe86a6e924f1bc49
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566353"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Geospatiala och geospatiala JSON-plats data i Azure Cosmos DB

Den här artikeln ger en introduktion till geospatiala funktionerna i Azure Cosmos DB. För närvarande stöds endast att lagra och komma åt geospatiala data i Azure Cosmos DB SQL API-konton. När du har läst vår dokumentation om Geospatial indexering kan du svara på följande frågor:

* Hur jag för att lagra rumsliga data i Azure Cosmos DB?
* Hur kan jag läsa geospatiala data i Azure Cosmos DB i SQL och LINQ?
* Hur jag för att aktivera eller inaktivera rumslig indexering i Azure Cosmos DB?

## <a name="introduction-to-spatial-data"></a>Introduktion till spatialdata

Spatialdata beskriver position och formen på objekt i utrymmet. I de flesta program motsvarar dessa objekt på jorden och geospatiala data. Rumsliga data kan användas för att representera platsen för en person, en plats i närheten eller ens en stad eller en sjö gränser. Vanliga användningsområden ofta omfattar närhetsförfrågningar, till exempel, ”hitta alla kaféer nära min aktuella plats”.

Azure Cosmos DB SQL API har stöd för **geografi** data typen. **Geografi** typen representerar data i ett system för Round-jordens koordinater.

## <a name="supported-data-types"></a>Data typer som stöds

Azure Cosmos DB stöder indexering och frågor om geospatiala punkt data som representeras med hjälp av den geospatiala [JSON-specifikationen](https://tools.ietf.org/html/rfc7946). GeoJSON-datastrukturer är alltid giltig JSON-objekt, så att de kan lagras och frågor med Azure Cosmos DB utan några särskilda verktyg och bibliotek.

Azure Cosmos DB stöder följande spatiala data typer:

- Pekaren
- Lin Est ring
- Polygonlasso
- MultiPolygon

### <a name="points"></a>Saker

En **punkt** anger en enda position i rymden. I geospatiala data motsvarar en punkt den exakta platsen, vilket kan vara en gatuadress en LIVSMEDELSBUTIK, en kiosk, en bil eller en stad.  En punkt representeras i GeoJSON (och Azure Cosmos DB) med hjälp av dess koordinat par eller longitud och latitud.

Här är ett exempel-JSON för en punkt:

**Punkter i Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Spatiala data typer kan bäddas in i ett Azure Cosmos DB-dokument som visas i det här exemplet på en användar profil som innehåller plats data:

**Använd profil med platsen lagrad i Azure Cosmos DB**

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

### <a name="points-in-geography-coordinate-system"></a>Punkter i geografi system

För geografi data typen anger **geografi** specifikation longitud först och Latitude Second. Som i andra program för mappning, longitud och latitud är vinklar och representeras i grader. Longitudvärden mäts från nollmeridianen och är mellan-180 grader och 180.0 grader, och värdena för latitud mäts från ekvatorn och är mellan-90.0 grader och 90.0 grader.

Azure Cosmos DB tolkar koordinater framställd per referenssystem WGS 84. Nedan finns mer information om koordinaten referenssystem.

### <a name="linestrings"></a>Lin Est rings

**Lin Est rings** representerar en serie med två eller flera punkter i utrymmet och de linje segment som kopplar dem. I geospatiala data användas LineStrings brukar för att representera motorvägar eller vattendrag.

**Lin Est rings i interjson**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Polygoner

En **polygon** är en kant linje med anslutna punkter som utgör en stängd lin Est ring. Polygoner användas brukar för att representera naturlig buskmarker som sjöar eller politisk jurisdiktioner som orter och delstater. Här är ett exempel på en polygon i Azure Cosmos DB:

**Polygoner i polyjson**

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
> GeoJSON-specifikationen kräver att för giltiga polygoner senaste koordinaten paret som anges ska vara samma som först som skapar en sluten form.
>
> Punkter inom en Polygon måste anges i motsols ordning. En Polygon som angetts i medurs ordning representerar inversen till regionen i den.
>
>

### <a name="multipolygons"></a>Multipolygoner

En **multipolygon** är en matris med noll eller flera polygoner. **Multipolygoner** får inte överlappa sidor eller ha några gemensamma områden. De kan röra sig vid en eller flera punkter.

**Multipolygoner i polyjson**

```json
{
    "type":"MultiPolygon",
    "coordinates":[ [
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
    ],
    [
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
    ] ]
}
```

## <a name="coordinate-reference-systems"></a>Samordna referenssystem

Eftersom jordens form är oregelbunden, representeras koordinaterna för geospatiala geografiska data i många koordinatsystem, var och en med sina egna referens ramar och mått enheter. Den ”nationella rutnät för Storbritannien” är till exempel en referenssystem stämmer för Storbritannien, men inte utanför den.

Det mest populära BOKNINGs systemet som används idag är världens Geodetic System [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). Använd WGS 84 GPS-enheter och många mappning tjänster, inklusive Google Maps och Bing Maps API: er. Azure Cosmos DB stöder indexering och frågor om geospatiala geografi data med endast WGS-84-boknings system.

## <a name="creating-documents-with-spatial-data"></a>Skapa dokument med rumsliga data
När du skapar dokument som innehåller GeoJSON-värden, indexeras de automatiskt med en spatialindexet i enlighet med indexprincip i behållaren. Om du arbetar med ett Azure Cosmos DB SDK på ett dynamiskt skrivna språk som Python eller Node.js, måste du skapa giltiga GeoJSON.

**Skapa dokument med geospatiala data i Node. js**

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

Om du arbetar med SQL-API: erna kan du använda klasserna `Point`, `LineString`, `Polygon`och `MultiPolygon` i `Microsoft.Azure.Cosmos.Spatial`-namnrymden för att bädda in plats information i dina program objekt. De här klasserna underlätta serialisering och deserialisering av spatialdata i GeoJSON.

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

Om du inte har information om latitud och longitud, men har fysiska adresser eller plats namn som stad eller land/region, kan du leta upp de faktiska koordinaterna genom att använda en tjänst som Bing Maps REST Services. Lär dig mer om Bing Maps-geografi [här](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="next-steps"></a>Nästa steg

Bredvid nu när du har lärt dig hur du kommer igång med geospatialt stöd i Azure Cosmos DB, kan du:

* Läs mer om [Azure Cosmos DB fråga](sql-query-getting-started.md)
* Lär dig mer om att [fråga spatialdata med Azure Cosmos DB](sql-query-geospatial-query.md)
* Läs mer om [index spatial data med Azure Cosmos DB](sql-query-geospatial-index.md)