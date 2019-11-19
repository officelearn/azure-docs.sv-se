---
title: Ändra strömmar i Azure Cosmos DBs API för MongoDB
description: Lär dig hur du använder ändrings strömmar n Azure Cosmos DBs API för MongoDB för att hämta de ändringar som gjorts i dina data.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: b32eb7a7236871a06e1fbed5b9f1aac91675c6f3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174738"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Ändra strömmar i Azure Cosmos DBs API för MongoDB

[Ändra feed](change-feed.md) -stöd i Azure Cosmos DBS API för MongoDB är tillgängligt med hjälp av API: et för ändrings strömmar. Med hjälp av API: et för ändrings strömmar kan dina program få ändringarna som gjorts i samlingen eller till objekten i en enda Shard. Senare kan du vidta ytterligare åtgärder baserat på resultaten. Ändringar av objekt i samlingen samlas i ordningen av deras ändrings tid och sorterings ordningen garanteras per Shard nyckel.

I följande exempel visas hur du hämtar ändrings strömmar för alla objekt i samlingen. I det här exemplet skapas en markör som tittar på objekt när de infogas, uppdateras eller ersätts. Alternativet $match Stage, $project Stage och fullDocument krävs för att hämta ändrings strömmar. Det finns för närvarande inte stöd för att titta efter borttagnings åtgärder med ändrings strömmar. Som en lösning kan du lägga till en mjuk markör för de objekt som tas bort. Du kan till exempel lägga till ett attribut i objektet som kallas "borttaget" och ange det som "sant" och ange ett TTL-värde för objektet, så att du kan ta bort det automatiskt och spåra det.

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

I följande exempel visas hur du kan få ändringar i objekten i en enskild Shard, till exempel Shard "a" som har objekt med värdet "1".

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

Följande begränsningar gäller när du använder ändrings strömmar:

* Egenskaperna `operationType` och `updateDescription` stöds inte ännu i utmatnings dokumentet.
* Åtgärds typerna `insert`, `update`och `replace` stöds för närvarande. Borttagnings åtgärden eller andra händelser stöds inte ännu.

På grund av dessa begränsningar krävs $match steg, $project Stage och fullDocument alternativ som du ser i föregående exempel.

## <a name="error-handling"></a>Felhantering

Följande felkoder och meddelanden stöds när du använder ändrings strömmar:

* **Http-felkod 429** -när ändrings data strömmen är begränsad returneras en tom sida.

* **NamespaceNotFound (OperationType ogiltig)** – om du kör ändrings ström för samlingen som inte finns, eller om samlingen har släppts, returneras ett `NamespaceNotFound` fel. Eftersom `operationType` egenskapen inte kan returneras i utdatafilen, i stället för `operationType Invalidate` fel returneras `NamespaceNotFound` fel.

## <a name="next-steps"></a>Nästa steg

* [Använd Time to Live för att förfalla data automatiskt i Azure Cosmos DBs API för MongoDB](mongodb-time-to-live.md)
* [Indexering i Azure Cosmos DBs API för MongoDB](mongodb-indexing.md)