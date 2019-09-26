---
title: Arbeta med datum i Azure Cosmos DB
description: Läs mer om hur du arbetar med datum i Azure Cosmos DB.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: ea7880d051303afad01ad8ba4a2d68d7331c6a89
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71291150"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Arbeta med datum i Azure Cosmos DB
Azure Cosmos DB ger schemaflexibilitet och omfattande indexering via ett ursprungligt [JSON](https://www.json.org) datamodellen. Alla Azure Cosmos DB-resurser, inklusive databaser, behållare, dokument och lagrade procedurer modelleras och lagras som JSON-dokument. Som ett krav för portabelt stöder JSON (och Azure Cosmos DB) bara en liten uppsättning grundläggande typer: Sträng, tal, boolesk, matris, objekt och null. JSON är flexibel och tillåta utvecklare och ramverk för att representera mer komplexa typer med hjälp av dessa primitiver och skriva dem som objekt eller matriser. 

Förutom de grundläggande typerna behöver många program DateTime-typen för att representera datum och tidsstämpel. Den här artikeln beskriver hur utvecklare kan lagra, hämta och fråga efter datum i Azure Cosmos DB med .NET SDK.

## <a name="storing-datetimes"></a>Lagra datum och tid

Azure Cosmos DB är begränsad till JSON-typerna som inte innehåller en DateTime-typ. I Azure Cosmos DB måste datumen lagras som strängar. För närvarande stöder Azure Cosmos DB inte lokalisering av datum. Det rekommenderade formatet för datetime-strängar i Azure Cosmos DB `YYYY-MM-DDThh:mm:ss.sssZ` är som följer ISO 8601 UTC-standarden. Om du formaterar strängarna i det här formatet kan sorterings datumen lexicographically. Logiken för att hantera icke-UTC-datum måste definieras av klienten. De flesta program kan använda standard-strängrepresentation för DateTime av följande skäl:

* Strängar kan jämföras och relativa ordning av datum/tid-värden bevaras när de omvandlas till strängar. 
* Den här metoden kräver inte någon anpassad kod eller ett attribut för JSON-konvertering.
* Datum som lagras i JSON är mänskliga läsbar.
* Den här metoden kan dra nytta av Azure Cosmos DB-index för snabb frågeprestanda.

Till exempel följande kodavsnitt lagrar en `Order` objekt som innehåller två datum/tid-egenskaper – `ShipDate` och `OrderDate` som ett dokument med hjälp av .NET-SDK:

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

Det här dokumentet lagras i Azure Cosmos DB på följande sätt:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Du kan också lagra datum och tid som Unix tidsstämplar, det vill säga som ett tal som representerar antalet förflutna sekunder sedan den 1 januari 1970. Azure Cosmos DB interna tidsstämpel (`_ts`) egenskapen följer den här metoden. Du kan använda den [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) klassen för att serialisera datum och tid som tal. 

## <a name="indexing-datetimes-for-range-queries"></a>Indexering datum och tid för intervallfrågor
Omfångsfrågor är vanliga med DateTime-värden. Om du behöver hitta alla beställningar som skapats sedan igår eller hitta alla beställningar som levereras under de senaste fem minuterna, måste du utföra intervallfrågor. Om du vill köra dessa frågor effektivt, måste du konfigurera din samling för intervallet indexering på strängar.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Du kan lära dig mer om hur du konfigurerar indexering principer på [indexera Azure Cosmos DB-principer](index-policy.md).

## <a name="querying-datetimes-in-linq"></a>Förfrågningar till datum och tid i LINQ
SQL .NET SDK har automatiskt stöd för frågor till data som lagras i Azure Cosmos DB via LINQ. Följande kodavsnitt visar exempelvis en LINQ-frågan som filter order som levererades under de senaste tre dagarna.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Du kan lära dig mer om Azure Cosmos DB SQL-frågespråket och LINQ-providern på [frågor till Cosmos DB](how-to-sql-query.md).

I den här artikeln har tittat vi på hur du lagrar, index- och fråga efter datum och tid i Azure Cosmos DB.

## <a name="next-steps"></a>Nästa steg
* Ladda ned och kör den [kodexempel på GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Läs mer om [SQL-frågor](how-to-sql-query.md)
* Läs mer om [indexera Azure Cosmos DB-principer](index-policy.md)
