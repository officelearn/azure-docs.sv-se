---
title: Ändra strömmar i Azure Cosmos DB:s API för MongoDB
description: Lär dig hur du använder ändringsströmmar n Azure Cosmos DB:s API för MongoDB för att få ändringarna gjorda i dina data.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: ec1ec1a8a80953f8988355341ee7128bd29b982d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467785"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Ändra strömmar i Azure Cosmos DB:s API för MongoDB

[Ändra feedstöd](change-feed.md) i Azure Cosmos DB:s API för MongoDB är tillgängligt med hjälp av API:et för ändringsströmmar. Genom att använda API:et för ändringsströmmar kan dina program hämta ändringarna i samlingen eller till objekten i en enda fragment. Senare kan du vidta ytterligare åtgärder baserat på resultaten. Ändringar av objekten i samlingen fångas i den ordning de ändras och sorteringsordningen garanteras per fragmentnyckel.

> [!NOTE]
> Om du vill använda ändringsströmmar skapar du kontot med version 3.6 av Azure Cosmos DB:s API för MongoDB eller en senare version. Om du kör exemplen på ändringsström mot `Unrecognized pipeline stage name: $changeStream` en tidigare version kan felet visas. 

I följande exempel visas hur du får ändringsströmmar för alla objekt i samlingen. I det här exemplet skapas en markör för att titta på objekt när de infogas, uppdateras eller ersätts. Det $match steget, $project steget och fullDokumentingsalternativet krävs för att få ändringsströmmarna. Det går inte att söka efter borttagningsåtgärder med ändringsströmmar. Som en lösning kan du lägga till en mjuk markör på de objekt som tas bort. Du kan till exempel lägga till ett attribut i objektet "borttaget" och ange det till "true" och ange ett TTL för objektet, så att du automatiskt kan ta bort det samt spåra det.

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

I följande exempel visas hur du får ändringar i objekten i en enda fragment. Det här exemplet hämtar ändringar av objekt som har fragmentnyckel som är lika med "a" och shardnyckelvärdet lika med "1".

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

## <a name="current-limitations"></a>Aktuella begränsningar

Följande begränsningar gäller vid användning av ändringsströmmar:

* Egenskaperna `operationType` `updateDescription` och stöds ännu inte i utdatadokumentet.
* `insert`Typerna `update`, `replace` och åtgärder stöds för närvarande. Borttagning eller andra händelser stöds ännu inte.

På grund av dessa begränsningar krävs $match steg, $project och fullständigadokumentalternativ som visas i föregående exempel.

## <a name="error-handling"></a>Felhantering

Följande felkoder och meddelanden stöds när du använder ändringsströmmar:

* **HTTP-felkod 429** - När ändringsströmmen begränsas returneras en tom sida.

* **NamespaceNotFound (OperationType Invalidate)** - Om du kör ändringsström på samlingen som inte `NamespaceNotFound` finns eller om samlingen tas bort returneras ett fel. Eftersom `operationType` egenskapen inte kan returneras i utdatadokumentet returneras `operationType Invalidate` `NamespaceNotFound` felet i stället för felet.

## <a name="next-steps"></a>Nästa steg

* [Använd tid för att leva för att förfalla data automatiskt i Azure Cosmos DB:s API för MongoDB](mongodb-time-to-live.md)
* [Indexering i Azure Cosmos DB:s API för MongoDB](mongodb-indexing.md)
