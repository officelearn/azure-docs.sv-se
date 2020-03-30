---
title: Fråga geospatiala data med Azure Cosmos DB
description: Fråga rumsliga data med Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 08b12bd9d35aaa61c79d35a55068983cdc0f1b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77566327"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Fråga geospatiala data med Azure Cosmos DB

Den här artikeln beskriver hur du frågar geospatiala data i Azure Cosmos DB med SQL och LINQ. För närvarande stöds lagring och åtkomst till geospatiala data endast av Azure Cosmos DB SQL API-konton. Azure Cosmos DB stöder följande inbyggda OGC-funktioner (Open Geospatial Consortium) för geospatial frågor. Mer information om den fullständiga uppsättningen inbyggda funktioner på SQL-språket finns i [Frågesystemfunktioner i Azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Inbyggda funktioner för spatial SQL

Här är en lista över geospatiala systemfunktioner som är användbara för frågor i Azure Cosmos DB:

|**Användning**|**Beskrivning**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Returnerar avståndet mellan de två GeoJSON Point-, Polygon- eller LineString-uttrycken.|
|ST_WITHIN (spatial_expr, spatial_expr) | Returnerar ett booleskt uttryck som anger huruvida det första GeoJSON-objektet (Point, Polygon eller LineString) finns i det andra GeoJSON-objektet (Point, Polygon eller LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Returnerar ett booleskt uttryck som anger huruvida de två angivna GeoJSON-objekten (Point, Polygon eller LineString) överlappar varandra.|
|ST_ISVALID| Returnerar ett booleskt värde som anger huruvida det angivna GeoJSON Point-, Polygon- eller LineString-uttrycket är giltigt.|
| ST_ISVALIDDETAILED| Returnerar ett JSON-värde som innehåller ett booleskt värde om det angivna geojsonpunkt-, polygon- eller radsträngningsuttrycket är giltigt. Om den är ogiltig returneras orsaken som ett strängvärde.|

Spatiella funktioner kan användas för att köra närhetsfrågor mot rumsliga data. Här är till exempel en fråga som returnerar alla familjedokument som ligger inom `ST_DISTANCE` 30 km från den angivna platsen med hjälp av den inbyggda funktionen.

**Fråga**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

**Results**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Om du inkluderar rumslig indexering i indexeringsprincipen visas "avståndsfrågor" effektivt genom indexet. Mer information om spatial indexering finns i [geospatial indexering](sql-query-geospatial-index.md). Om du inte har ett rumsligt index för de angivna sökvägarna gör frågan en genomsökning av behållaren.

`ST_WITHIN`kan användas för att kontrollera om en punkt ligger inom en polygon. Polygoner används ofta för att representera gränser som postnummer, tillståndsgränser eller naturliga formationer. Återigen om du inkluderar rumslig indexering i din indexeringsprincip, kommer "inom" frågor att visas effektivt genom indexet.

Polygon argument `ST_WITHIN` i kan innehålla endast en enda ring, det vill säga polygonerna får inte innehålla hål i dem.

**Fråga**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon',
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Results**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> På samma sätt som inkompatibla typer fungerar i Azure Cosmos DB-frågan, om det platsvärde som anges i något av argumenten är felaktigt eller ogiltigt, utvärderas det till **odefinierat** och det utvärderade dokumentet som ska hoppas över från frågeresultaten. Om frågan inte returnerar `ST_ISVALIDDETAILED` några resultat kör du för att felsöka varför den rumsliga typen är ogiltig.
>
>

Azure Cosmos DB stöder också att utföra omvända frågor, det vill säga du kan indexera polygoner eller rader i Azure Cosmos DB och sedan fråga efter de områden som innehåller en angiven punkt. Detta mönster används ofta i logistik för att identifiera till exempel när en lastbil kommer in i eller lämnar ett angivet område.

**Fråga**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)
```

**Results**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID`och `ST_ISVALIDDETAILED` kan användas för att kontrollera om ett rumsligt objekt är giltigt. Följande fråga kontrollerar till exempel giltigheten för en punkt med ett värde utanför intervallet (-132,8). `ST_ISVALID`returnerar bara ett booleskt värde och `ST_ISVALIDDETAILED` returnerar booleskten och en sträng som innehåller orsaken till att det anses ogiltigt.

**Fråga**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Results**

```json
    [{
      "$1": false
    }]
```

Dessa funktioner kan också användas för att validera polygoner. Här använder `ST_ISVALIDDETAILED` vi till exempel för att validera en Polygon som inte är stängd.

**Fråga**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Results**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>LINQ-frågor i .NET SDK

SQL .NET SDK tillhandahåller `Distance()` också `Within()` stub-metoder och för användning inom LINQ-uttryck. SQL LINQ-providern översätter den här metoden anrop till motsvarande SQL-inbyggda funktionsanrop (ST_DISTANCE respektive ST_WITHIN).

Här är ett exempel på en LINQ-fråga som hittar `location` alla dokument i Azure Cosmos-behållaren vars värde ligger inom en radie av 30 km från den angivna punkten med LINQ.

**LINQ-fråga för Avstånd**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

På samma sätt, här är en fråga `location` för att hitta alla dokument vars är inom den angivna rutan / Polygon.

**LINQ-fråga för Inom**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du kommer igång med geospatial support i Azure Cosmos DB kan du nästa du:

* Läs mer om [Azure Cosmos DB Query](sql-query-getting-started.md)
* Läs mer om [geospatial- och GeoJSON-platsdata i Azure Cosmos DB](sql-query-geospatial-intro.md)
* Läs mer om [Index spatial data med Azure Cosmos DB](sql-query-geospatial-index.md)
