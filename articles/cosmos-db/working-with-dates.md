---
title: Arbeta med datum i Azure Cosmos DB
description: Lär dig att lagra, indexera och fråga datum/tid-objekt i Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 92fa35fbe8e5eef4dbdc8b6c47a9055affd449a5
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273192"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Arbeta med datum i Azure Cosmos DB

Azure Cosmos DB ger flexibilitet i schema och omfattande indexering via en intern [JSON](https://www.json.org) -datamodell. Alla Azure Cosmos DB-resurser, inklusive databaser, behållare, dokument och lagrade procedurer modelleras och lagras som JSON-dokument. Som ett krav för att bärbara JSON (och Azure Cosmos DB) stöder bara en liten uppsättning grundläggande typer: sträng, nummer, booleskt värde, matris, objekt och Null. JSON är flexibel och tillåta utvecklare och ramverk för att representera mer komplexa typer med hjälp av dessa primitiver och skriva dem som objekt eller matriser.

Förutom de grundläggande typerna behöver många program DateTime-typen för att representera datum och tidsstämpel. Den här artikeln beskriver hur utvecklare kan lagra, hämta och fråga efter datum i Azure Cosmos DB med .NET SDK.

## <a name="storing-datetimes"></a>Lagra datum och tid

Azure Cosmos DB stöder JSON-typer som-String, Number, Boolean, null, matris, Object. Det stöder inte direkt en DateTime-typ. För närvarande stöder Azure Cosmos DB inte lokalisering av datum. Så du måste lagra datum och tid som strängar. Det rekommenderade formatet för DateTime-strängar i Azure Cosmos DB är `YYYY-MM-DDThh:mm:ss.sssZ` som följer ISO 8601 UTC-standarden. Vi rekommenderar att du lagrar alla datum i Azure Cosmos DB som UTC. Om du konverterar datum strängarna till det här formatet kan sorterings datumen lexicographically. Om icke-UTC-datum lagras måste logiken hanteras på klient sidan. Om du vill konvertera en lokal datum-DateTime till UTC måste förskjutningen vara känd/lagrad som en egenskap i JSON och klienten kan använda offset för att beräkna UTC-slutdatum svärdet.

De flesta program kan använda standard-strängrepresentation för DateTime av följande skäl:

* Strängar kan jämföras och relativa ordning av datum/tid-värden bevaras när de omvandlas till strängar.
* Den här metoden kräver inte någon anpassad kod eller ett attribut för JSON-konvertering.
* Datum som lagras i JSON är mänskliga läsbar.
* Den här metoden kan dra nytta av Azure Cosmos DB-index för snabb frågeprestanda.

Följande fragment lagrar till exempel ett `Order`-objekt som innehåller två DateTime-egenskaper – `ShipDate` och `OrderDate` som ett dokument med hjälp av .NET SDK:

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });
```

Det här dokumentet lagras i Azure Cosmos DB på följande sätt:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Du kan också lagra datum och tid som Unix tidsstämplar, det vill säga som ett tal som representerar antalet förflutna sekunder sedan den 1 januari 1970. Azure Cosmos DB egenskapen intern tidsstämpel (`_ts`) följer den här metoden. Du kan använda klassen [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) för att serialisera datetime-värden som tal.

## <a name="querying-datetimes-in-linq"></a>Förfrågningar till datum och tid i LINQ

SQL .NET SDK har automatiskt stöd för frågor till data som lagras i Azure Cosmos DB via LINQ. I följande kodfragment visas till exempel en LINQ-fråga som filtrerar order som har levererats under de senaste tre dagarna:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Översatt till följande SQL-uttryck och körs på Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")
```

Du kan lära dig mer om SQL-frågespråket i Azure Cosmos DB och LINQ-providern vid [frågor Cosmos db i LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Indexering datum och tid för intervallfrågor

Frågor är vanliga med DateTime-värden. Om du vill köra dessa frågor effektivt måste du ha ett index definierat för alla egenskaper i frågans filter.

Du kan lära dig mer om hur du konfigurerar indexerings principer på [Azure Cosmos DB indexerings principer](index-policy.md). 

## <a name="next-steps"></a>Nästa steg

* Hämta och kör [kod exemplen på GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Läs mer om [SQL-frågor](sql-query-getting-started.md)
* Läs mer om [Azure Cosmos DB indexerings principer](index-policy.md)
