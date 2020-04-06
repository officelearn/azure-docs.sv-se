---
title: Arbeta med datum i Azure Cosmos DB
description: Lär dig hur du lagrar, indexerar och frågar DataTime-objekt i Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 174279e4bd241ee9b336fc1ce7e0af389d2297a3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667008"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Arbeta med datum i Azure Cosmos DB

Azure Cosmos DB ger schemaflexibilitet [JSON](https://www.json.org) och omfattande indexering via en inbyggd JSON-datamodell. Alla Azure Cosmos DB-resurser, inklusive databaser, behållare, dokument och lagrade procedurer modelleras och lagras som JSON-dokument. Som ett krav för att vara bärbar stöder JSON (och Azure Cosmos DB) endast en liten uppsättning grundläggande typer: Sträng, Tal, Boolean, Array, Object och Null. JSON är dock flexibelt och gör det möjligt för utvecklare och ramverk att representera mer komplexa typer med hjälp av dessa primitiver och komponera dem som objekt eller matriser.

Förutom de grundläggande typerna behöver många program typen DateTime för att representera datum och tidsstämplar. I den här artikeln beskrivs hur utvecklare kan lagra, hämta och frågedatum i Azure Cosmos DB med hjälp av .NET SDK.

## <a name="storing-datetimes"></a>Lagra DateTimes

Azure Cosmos DB stöder JSON-typer som - sträng, tal, boolesk, null, array, objekt. Det stöder inte direkt en DateTime-typ. Azure Cosmos DB stöder för närvarande inte lokalisering av datum. Så du måste lagra DateTimes som strängar. Det rekommenderade formatet för DateTime-strängar i `YYYY-MM-DDThh:mm:ss.fffffffZ` Azure Cosmos DB är som följer ISO 8601 UTC-standarden. Vi rekommenderar att du lagrar alla datum i Azure Cosmos DB som UTC. Om du konverterar datumsträngarna till det här formatet tillåts sorteringsdatum lexikografiskt. Om icke-UTC-datum lagras måste logiken hanteras på klientsidan. Om du vill konvertera en lokal DateTime till UTC måste förskjutningen vara känd/lagrad som en egenskap i JSON och klienten kan använda förskjutningen för att beräkna UTC DateTime-värdet.

Intervallfrågor med DateTime-strängar som filter stöds bara om DateTime-strängarna alla finns i UTC och samma längd. I Azure Cosmos DB returnerar funktionen [GetCurrentDateTime](sql-query-getcurrentdatetime.md) det aktuella UTC-strängvärdet för UTC-datum och tid i formatet: `YYYY-MM-DDThh:mm:ss.fffffffZ`.

De flesta program kan använda standardsträngrepresentationen för DateTime av följande skäl:

* Strängar kan jämföras och den relativa ordningen för DateTime-värdena bevaras när de omvandlas till strängar.
* Den här metoden kräver ingen anpassad kod eller attribut för JSON-konvertering.
* Datumen som lagras i JSON är läsbara för människor.
* Den här metoden kan dra nytta av Azure Cosmos DB:s index för snabb frågeprestanda.

I följande kodavsnitt lagras till `Order` exempel ett objekt `ShipDate` som `OrderDate` innehåller två DateTime-egenskaper – och som ett dokument med .NET SDK:

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

Det här dokumentet lagras i Azure Cosmos DB enligt följande:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Du kan också lagra DateTimes som Unix-tidsstämplar, det vill säga som ett tal som representerar antalet sekunder som förflutit sedan den 1 januari 1970. Azure Cosmos DB:s interna`_ts`tidsstämpel ( ) följer den här metoden. Du kan använda klassen [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) för att serialisera DateTimes som nummer.

## <a name="querying-datetimes-in-linq"></a>Fråga DateTimes i LINQ

SQL .NET SDK stöder automatiskt frågor om data som lagras i Azure Cosmos DB via LINQ. Följande kodavsnitt visar till exempel en LINQ-fråga som filtrerar order som levererats under de senaste tre dagarna:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Översatt till följande SQL-uttryck och körs på Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2014-09-30T23:14:25.7251173Z")
```

Du kan läsa mer om Azure Cosmos DB:s SQL-frågespråk och LINQ-providern på [Querying Cosmos DB i LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Indexera DateTimes för intervallfrågor

Frågor är vanliga med DateTime-värden. Om du vill köra dessa frågor effektivt måste du ha ett index definierat för alla egenskaper i frågans filter.

Du kan läsa mer om hur du konfigurerar indexeringsprinciper på [Azure Cosmos DB Indexeringsprinciper](index-policy.md). 

## <a name="next-steps"></a>Efterföljande moment

* Hämta och kör [kodexemplen på GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Läs mer om [SQL-frågor](sql-query-getting-started.md)
* Läs mer om [Azure Cosmos DB-indexeringsprinciper](index-policy.md)
