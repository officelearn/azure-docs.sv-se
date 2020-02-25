---
title: Fråga geospatiala data med Azure Cosmos DB
description: Fråga om spatialdata med Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 08b12bd9d35aaa61c79d35a55068983cdc0f1b83
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566327"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Fråga geospatiala data med Azure Cosmos DB

Den här artikeln beskriver hur du frågar geospatiala data i Azure Cosmos DB att använda SQL och LINQ. För närvarande stöds endast att lagra och komma åt geospatiala data i Azure Cosmos DB SQL API-konton. Azure Cosmos DB stöder följande öppna geospatiala Consortium (OGC) inbyggda funktioner för geospatiala frågor. Mer information om den fullständiga uppsättningen inbyggda funktioner i SQL-språket finns [i fråga system funktioner i Azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Spatial inbyggda SQL-funktioner

Här är en lista över geospatiala systemfunktioner som är användbara för frågor i Azure Cosmos DB:

|**Användning**|**Beskrivning**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Returnerar avståndet mellan de två GeoJSON Point-, Polygon- eller LineString-uttrycken.|
|ST_WITHIN (spatial_expr, spatial_expr) | Returnerar ett booleskt uttryck som anger huruvida det första GeoJSON-objektet (Point, Polygon eller LineString) finns i det andra GeoJSON-objektet (Point, Polygon eller LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Returnerar ett booleskt uttryck som anger huruvida de två angivna GeoJSON-objekten (Point, Polygon eller LineString) överlappar varandra.|
|ST_ISVALID| Returnerar ett booleskt värde som anger huruvida det angivna GeoJSON Point-, Polygon- eller LineString-uttrycket är giltigt.|
| ST_ISVALIDDETAILED| Returnerar ett JSON-värde som innehåller ett booleskt värde om det angivna lin Est ring-uttrycket är giltigt. Om detta är ogiltigt returneras orsaken som ett sträng värde.|

Spatiella funktioner kan användas för att köra närhetsfrågor mot rumsliga data. Här är ett exempel på en fråga som returnerar alla familje dokument som ligger inom 30 km från den angivna platsen med hjälp av den inbyggda funktionen `ST_DISTANCE`.

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

Om du inkluderar rumslig indexering i indexprincip, sedan hanteras ”avståndet-frågor” effektivt via indexet. Mer information om spatial indexering finns i [Geospatial indexering](sql-query-geospatial-index.md). Om du inte har ett rums index för de angivna Sök vägarna gör frågan en genomsökning av behållaren.

`ST_WITHIN` kan användas för att kontrol lera om en punkt ligger inom en polygon. Polygoner används ofta för att representera gränser som postnummer, tillstånd gränser eller naturlig buskmarker. Igen om du inkluderar rumslig indexering i indexprincip, sedan ”i” frågor hanteras effektivt via indexet.

Polygon-argument i `ST_WITHIN` får bara innehålla en enda ring, det vill säga polygonerna får inte innehålla hål i dem.

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
> På samma sätt som för fel matchnings typer fungerar i Azure Cosmos DBs fråga, om det plats värde som anges i båda argumenten är felaktigt eller ogiltigt, utvärderas det som **odefinierat** och det utvärderade dokumentet som ska hoppas över från frågeresultaten. Om frågan inte returnerar några resultat kör du `ST_ISVALIDDETAILED` för att felsöka varför den spatiala typen är ogiltig.
>
>

Azure Cosmos DB stöder också utföra inverterade frågor, det vill säga du indexera polygoner eller rader i Azure Cosmos DB och sedan fråga efter de områden som innehåller en viss tidpunkt. Det här mönstret används ofta i logistik identifiera, till exempel när en lastbil anländer till eller lämnar en avsedda området.

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

`ST_ISVALID` och `ST_ISVALIDDETAILED` kan användas för att kontrol lera om ett spatial objekt är giltigt. Till exempel kontrollerar följande fråga giltigheten för en tidpunkt med en out of latitudvärdet för intervall (-132.8). `ST_ISVALID` returnerar bara ett booleskt värde och `ST_ISVALIDDETAILED` returnerar den booleska och en sträng som innehåller orsaken till att den anses vara ogiltig.

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

Dessa funktioner kan också användas för att verifiera polygoner. Här använder vi till exempel `ST_ISVALIDDETAILED` för att verifiera en polygon som inte är stängd.

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

SQL .NET SDK tillhandahåller också stub-metoder `Distance()` och `Within()` för användning i LINQ-uttryck. SQL LINQ-providern översätter den här metoden anrop till de motsvarande inbyggda SQL-funktionsanrop (ST_DISTANCE och ST_WITHIN respektive).

Här är ett exempel på en LINQ-fråga som hittar alla dokument i Azure Cosmos-behållaren vars `location` värde ligger inom en radie på 30 km av den angivna punkten med LINQ.

**LINQ-fråga för avstånd**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

På samma sätt är det här en fråga för att hitta alla dokument vars `location` är inom den angivna rutan/polygonen.

**LINQ-fråga för inom**

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

Bredvid nu när du har lärt dig hur du kommer igång med geospatialt stöd i Azure Cosmos DB, kan du:

* Läs mer om [Azure Cosmos DB fråga](sql-query-getting-started.md)
* Läs mer om [geospatiala data om geospatiala och geospatiala JSON-platser i Azure Cosmos DB](sql-query-geospatial-intro.md)
* Läs mer om [index spatial data med Azure Cosmos DB](sql-query-geospatial-index.md)
