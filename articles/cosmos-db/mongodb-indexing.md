---
title: Indexering i Azure Cosmos DBs API för MongoDB
description: Visar en översikt över indexerings funktionerna med Azure Cosmos DB s API för MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: c8879884cf3d882e6a6b441244ed139072bedeeb
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029477"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexera med Azure Cosmos DB s API för MongoDB

Azure Cosmos DBs API för MongoDB utnyttjar funktioner för automatisk index hantering i Cosmos DB. Det innebär att användarna har åtkomst till standard indexerings principerna för Cosmos DB. Så om inga index har definierats av användaren eller om inga index har släppts indexeras alla fält automatiskt som standard när de infogas i en samling. I de flesta fall bör du använda kontots standardindexeringsprincip.

## <a name="indexing-for-version-36"></a>Indexering för version 3,6

Konton som betjänar Wire Protocol version 3,6 har en annan standard indexerings princip än principen i tidigare versioner. Som standard är bara fältet _id indexerat. Om du vill indexera ytterligare fält måste användaren använda MongoDB index Management-kommandon. För att kunna tillämpa en sortering i en fråga, måste ett index skapas för de fält som används i sorterings åtgärden.

### <a name="dropping-the-default-indexes-36"></a>Släpper standard indexen (3,6)

För konton som hanterar Wire Protocol version 3,6 är det enda standard indexet _id, som inte går att släppa.

### <a name="creating-a-compound-index-36"></a>Skapa ett sammansatt index (3,6)

Sanna sammansatta index stöds för konton med hjälp av 3,6-Wire-protokollet. Följande kommando skapar ett sammansatt index för fälten "a" och "b": `db.coll.createIndex({a:1,b:1})`

Sammansatta index kan användas för att sortera effektivt på flera fält samtidigt, till exempel: `db.coll.find().sort({a:1,b:1})`

### <a name="track-the-index-progress"></a>Spåra index förloppet

3,6-versionen av Azure Cosmos DBs API för MongoDB-konton stöder kommandot `currentOp()` för att spåra indexerings förloppet på en databas instans. Det här kommandot returnerar ett dokument som innehåller information om pågående åtgärder på en databas instans. Kommandot `currentOp` används för att spåra alla pågående åtgärder i interna MongoDB, i Azure Cosmos DB s API för MongoDB, men det här kommandot stöder bara spårning av index åtgärden.

Här följer några exempel som visar hur du använder kommandot `currentOp` för att spåra indexerings förloppet:

• Hämta index förloppet för en samling:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

• Hämta index förloppet för alla samlingar i en databas:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

• Hämta index förloppet för alla databaser och samlingar i ett Azure Cosmos-konto:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

Informationen om index förloppet innehåller en procent andel av förloppet för den aktuella index åtgärden. I följande exempel visas utdatafilens format för olika steg i index förloppet:

1. Om index åtgärden för en "foo"-samling och "stapel"-databasen som har 60% indexering har slutförts, kommer följande utdata att vara kvar. `Inprog[0].progress.total` visar 100 som mål slut för ande.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

2. För en index åtgärd som precis har börjat i en "foo"-samling och "stapel"-databasen kan utmatnings dokumentet Visa 0% förlopp tills det når en mätbar nivå.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

3. När den pågående index åtgärden har slutförts, visar utmatnings dokumentet tomma insamlings åtgärder.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="indexing-for-version-32"></a>Indexering för version 3,2

### <a name="dropping-the-default-indexes-32"></a>Släpper standard indexen (3,2)

Följande kommando kan användas för att ta bort standardindexen i en samling ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

### <a name="creating-a-compound-index-32"></a>Skapa ett sammansatt index (3,2)

Sammansatta index innehåller referenser till flera fält i ett dokument. Logiskt sett är detta motsvarigheten till att skapa flera individuella index per fält. Om du vill dra nytta av de optimeringar som tillhandahålls av Cosmos DB:s indexeringstekniker rekommenderar vi att du skapar flera individuella index istället för att använda ett enda (icke-unikt) sammansatt index.

## <a name="common-indexing-operations"></a>Vanliga indexerings åtgärder

Följande åtgärder är gemensamma för båda kontona som betjänar Wire Protocol version 3,6 och konton som hanterar tidigare tråd protokoll versioner. 

## <a name="creating-unique-indexes"></a>Skapa unika index

[Unika index](unique-keys.md) är användbara när du vill säkerställa att samma fältvärde inte förekommer i flera index.

>[!Important]
> För närvarande går det bara att skapa unika index när samlingen är tom (inte innehåller några dokument).

Följande kommando skapar ett unikt index i fältet student_id:

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Om du har fragmenterade (sharded) samlingar kräver MongoDB att du tillhandahåller shardnyckeln (partitionsnyckeln) när du skapar unika index. Med andra ord så utgör alla unika index i en fragmenterad samling alltså sammansatta index där ett av fälten är en partitionsnyckel.

Följande kommandon för skapar en fragmenterad samling ```coll``` (shardnyckeln är ```university```) med ett unikt index för fälten student_id och university:

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

Om satsen ```"university":1``` utelämnas i exemplet ovan returneras ett fel med följande meddelande:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>TTL-index

Om du vill aktivera förfallodatum för dokumenten i en viss samling måste du skapa ett [TTL-index (Time to Live)](../cosmos-db/time-to-live.md). Ett TTL-index är ett index för fältet _ts med värdet ”expireAfterSeconds”.

Exempel:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Föregående kommando raderar alla dokument i samlingen ```db.coll``` som inte har ändrats under de senaste tio sekunderna.

> [!NOTE]
> **_ts** är ett fält som är unikt för Cosmos DB och det går inte att använda med MongoDB-klienter. Det är en reserverad (system)egenskap som innehåller tidsstämpeln för senaste ändringen av dokumentet.

## <a name="migrating-collections-with-indexes"></a>Migrera samlingar med index

För närvarande går det bara att skapa unika index när samlingen inte innehåller några dokument. Många av de vanligaste migreringsverktygen för MongoDB försöker skapa unika index när du har importerat data. För att kringgå det här problemet rekommenderar vi att användare skapar motsvarande samlingar och unika index manuellt, i stället för att tillåta Migreringsverktyg (för ```mongorestore``` det här beteendet uppnås med hjälp av `--noIndexRestore`-flaggan på kommando raden).

## <a name="next-steps"></a>Nästa steg

* [Indexering i Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Ta bort data från Azure Cosmos DB automatiskt med hjälp av förfallodatum](../cosmos-db/time-to-live.md)
