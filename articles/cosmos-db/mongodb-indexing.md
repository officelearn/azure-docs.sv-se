---
title: Indexering i MongoDB-API:t för Azure Cosmos DB | Microsoft Docs
description: Visar en översikt över indexeringsfunktionerna i MongoDB-API:t för Azure Cosmos DB.
services: cosmos-db
documentationcenter: ''
author: orestis-ms
manager: jhubbard
editor: ''
ms.assetid: daacbabf-1bb5-497f-92db-079910703047
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 03/01/2018
ms.author: orkostak
ms.openlocfilehash: 090f8a664409d9cde1e4440ca9e2390a7c9def7a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="indexing-in-the-azure-cosmos-db-mongodb-api"></a>Indexering i MongoDB-API:t för Azure Cosmos DB

MongoDB-API:t för Azure Cosmos DB använder sig av hanteringsfunktionerna i Azure Cosmos DB. Därmed har användarna även åtkomst till standardindexeringsprinciperna i Azure Cosmos DB. Om inga index har definierats av användaren eller om inga index har tagits bort, indexeras alla fält automatiskt när de infogas i samlingen. I de flesta fall bör du använda kontots standardindexeringsprincip.

## <a name="dropping-the-default-indexes"></a>Ta bort standardindex

Följande kommando kan användas för att ta bort standardindexen i en samling ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

## <a name="creating-compound-indexes"></a>Skapa sammansatta index

Sammansatta index innehåller referenser till flera fält i ett dokument. Logiskt sett är detta motsvarigheten till att skapa flera individuella index per fält. Om du vill dra nytta av de optimeringar som tillhandahålls av Cosmos DB:s indexeringstekniker rekommenderar vi att du skapar flera individuella index istället för att använda ett enda (icke-unikt) sammansatt index.

## <a name="creating-unique-indexes"></a>Skapa unika index

[Unika index](unique-keys.md) är användbara när du vill säkerställa att samma fältvärde inte förekommer i flera index. 
>[!important] 
> För närvarande går det bara att skapa unika index när samlingen är tom (inte innehåller några dokument). 

Följande kommando skapar ett unikt index för fältet ”student_id”:

```JavaScript
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

```JavaScript
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
>

## <a name="migrating-collections-with-indexes"></a>Migrera samlingar med index

För närvarande går det bara att skapa unika index när samlingen inte innehåller några dokument. Många av de vanligaste migreringsverktygen för MongoDB försöker skapa unika index när du har importerat data. Om du vill kringgå det här problemet rekommenderar vi att användarna manuellt skapar motsvarande samlingar och unika index istället för att låta migreringsverktyget göra detta (för ```mongorestore``` uppnås detta beteende med hjälp av flaggan --noIndexRestore på kommandoraden).

## <a name="next-steps"></a>Nästa steg
* [Hur fungerar dataindexeringen i Azure Cosmos DB?](../cosmos-db/indexing-policies.md)
* [Ta bort data från Azure Cosmos DB-samlingarna automatiskt med hjälp av förfallodatum](../cosmos-db/time-to-live.md)
