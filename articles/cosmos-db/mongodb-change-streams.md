---
title: Ändra strömmar i Azure Cosmos DB:s API för MongoDB
description: Lär dig hur du använder ändringsströmmar i Azure Cosmos DB:s API för MongoDB för att få ändringarna gjorda i dina data.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: ecfa98241f74aac43a827b645a6ed877624d643d
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437814"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Ändra strömmar i Azure Cosmos DB:s API för MongoDB

[Ändra feedstöd](change-feed.md) i Azure Cosmos DB:s API för MongoDB är tillgängligt med hjälp av API:et för ändringsströmmar. Genom att använda API:et för ändringsströmmar kan dina program hämta ändringarna i samlingen eller till objekten i en enda fragment. Senare kan du vidta ytterligare åtgärder baserat på resultaten. Ändringar av objekten i samlingen fångas i den ordning de ändras och sorteringsordningen garanteras per fragmentnyckel.

> [!NOTE]
> Om du vill använda ändringsströmmar skapar du kontot med version 3.6 av Azure Cosmos DB:s API för MongoDB eller en senare version. Om du kör exemplen på ändringsström mot `Unrecognized pipeline stage name: $changeStream` en tidigare version kan felet visas.

## <a name="current-limitations"></a>Aktuella begränsningar

Följande begränsningar gäller vid användning av ändringsströmmar:

* Egenskaperna `operationType` `updateDescription` och stöds ännu inte i utdatadokumentet.
* `insert`Typerna `update`, `replace` och åtgärder stöds för närvarande. Borttagning eller andra händelser stöds ännu inte.

På grund av dessa begränsningar krävs $match steg, $project och fullständigadokumentalternativ som visas i föregående exempel.

Till skillnad från ändringsflödet i Azure Cosmos DB:s SQL API finns det inte ett separat [ändringsflödesprocessorbibliotek](change-feed-processor.md) för att använda ändringsströmmar eller ett behov av en lånebehållare. Det finns för närvarande inte stöd för [Azure Functions-utlösare](change-feed-functions.md) för att bearbeta ändringsströmmar.

## <a name="error-handling"></a>Felhantering

Följande felkoder och meddelanden stöds när du använder ändringsströmmar:

* **HTTP-felkod 16500** - När ändringsströmmen begränsas returneras en tom sida.

* **NamespaceNotFound (OperationType Invalidate)** - Om du kör ändringsström på samlingen som inte `NamespaceNotFound` finns eller om samlingen tas bort returneras ett fel. Eftersom `operationType` egenskapen inte kan returneras i utdatadokumentet returneras `operationType Invalidate` `NamespaceNotFound` felet i stället för felet.

## <a name="examples"></a>Exempel

I följande exempel visas hur du får ändringsströmmar för alla objekt i samlingen. I det här exemplet skapas en markör för att titta på objekt när de infogas, uppdateras eller ersätts. Scenen, `$match` `$project` scenen och `fullDocument` alternativet krävs för att få ändringsströmmarna. Det går inte att söka efter borttagningsåtgärder med ändringsströmmar. Som en lösning kan du lägga till en mjuk markör på de objekt som tas bort. Du kan till exempel lägga till ett attribut i objektet "borttaget". När du vill ta bort objektet kan du ange `true` "borttagen" till och ange en TTL på objektet. Eftersom uppdatering av `true` "borttagen" till är en uppdatering visas den här ändringen i ändringsströmmen.

### <a name="javascript"></a>Javascript:

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

### <a name="c"></a>C#:

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

## <a name="changes-within-a-single-shard"></a>Ändringar inom en enskild fragment

I följande exempel visas hur du får ändringar i objekten i en enda fragment. Det här exemplet hämtar ändringar av objekt som har fragmentnyckel som är lika med "a" och shardnyckelvärdet lika med "1". Det är möjligt att ha olika klienter som läser ändringar från olika shards parallellt.

```javascript
var cursor = db.coll.watch(
    [
        {
            $match: {
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="next-steps"></a>Nästa steg

* [Använd tid för att leva för att förfalla data automatiskt i Azure Cosmos DB:s API för MongoDB](mongodb-time-to-live.md)
* [Indexering i Azure Cosmos DB:s API för MongoDB](mongodb-indexing.md)
