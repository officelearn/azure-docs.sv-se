---
title: Indexering i Azure Cosmos DB:s API för MongoDB
description: Visar en översikt över indexeringsfunktionerna med Azure Cosmos DB:s API för MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: f3f369928270c77557337bfdb1037cc5174c39f2
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637965"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexering med Azure Cosmos DB:s API för MongoDB

Azure Cosmos DB:s API för MongoDB utnyttjar kärnindexhanteringsfunktionerna i Azure Cosmos DB. Det här dokumentet fokuserar på hur du lägger till index med Azure Cosmos DB:s API för MongoDB. Du kan också läsa en [översikt över indexering i Azure Cosmos DB](index-overview.md) som är relevant för alla API:er.

## <a name="indexing-for-version-36"></a>Indexering för version 3.6

Fältet `_id` indexeras alltid automatiskt och kan inte tas bort. Azure Cosmos DB:s API för MongoDB tillämpar `_id` automatiskt fältets unika egenskaper per shardnyckel.

Om du vill indexera ytterligare fält bör du använda kommandona för MongoDB-indexhantering. Precis som i MongoDB indexeras endast fältet `_id` automatiskt. Den här standardindexeringsprincipen skiljer sig från Azure Cosmos DB SQL API, som indexerar alla fält som standard.

Om du vill använda en sortering på en fråga måste ett index skapas i fälten som används i sorteringsåtgärden.

## <a name="index-types"></a>Indextyper

### <a name="single-field"></a>Ett fält

Du kan skapa index på ett och samma fält. Sorteringsordningen för det enskilda fältindexet spelar ingen roll. Följande kommando skapar ett index `name`i fältet :

`db.coll.createIndex({name:1})`

En fråga kommer att använda flera enskilda fält index, där det är tillgängligt. Du kan skapa upp till 500 enstaka fältindex per behållare.

### <a name="compound-indexes-36"></a>Sammansatta index (3,6)

Sammansatta index stöds för konton som använder 3.6-trådprotokollet. Du kan inkludera upp till 8 fält i ett sammansatt index. Till skillnad från i MongoDB bör du bara skapa ett sammansatt index om frågan behöver sorteras effektivt på flera fält samtidigt. För frågor med flera filter som inte behöver sorteras bör du skapa flera ettfältsindex i stället för ett enda sammansatt index.

Följande kommando skapar ett sammansatt index `name` `age`för fälten och:

`db.coll.createIndex({name:1,age:1})`

Sammansatta index kan användas för att sortera effektivt på flera fält samtidigt, till exempel:

`db.coll.find().sort({name:1,age:1})`

Ovanstående sammansatta index kan också användas för effektiv sortering på en fråga med motsatt sorteringsordning på alla fält. Här är ett exempel:

`db.coll.find().sort({name:-1,age:-1})`

Men sekvensen av sökvägarna i det sammansatta indexet måste exakt matcha frågan. Här är ett exempel på en fråga som skulle kräva ytterligare ett sammansatt index:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Index med flera nycklar

Azure Cosmos DB skapar multikey index för att indexera innehåll som lagras i matriser. Om du indexerar ett fält med ett matrisvärde indexerar Azure Cosmos DB automatiskt varje element i matrisen.

### <a name="geospatial-indexes"></a>Geospatiala index

Många geospatiala operatörer kommer att dra nytta av geospatiala index. Azure Cosmos DB:s API för MongoDB stöder `2dsphere` för närvarande index. `2d`index stöds ännu inte.

Här är ett exempel på hur du skapar `location` ett geospatialt index på fältet:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Textindex

Textindex stöds för närvarande inte. För textsökningsfrågor på strängar bör du använda [Azure Cognitive Searchs](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) integrering med Azure Cosmos DB.

## <a name="index-properties"></a>Indexegenskaper

Följande åtgärder är vanliga för både konton som betjänar trådprotokoll version 3.6 och konton som betjänar tidigare trådprotokollversioner. Du kan också läsa mer om [index och indexerade egenskaper som stöds](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Unika index

[Unika index](unique-keys.md) är användbara när du vill säkerställa att samma fältvärde inte förekommer i flera index.

>[!Important]
> Unika index kan bara skapas när samlingen är tom (innehåller inga dokument).

Följande kommando skapar ett unikt index på fältet "student_id":

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

För fragmenterade samlingar måste du ange fragmentnyckeln (partition) för att skapa ett unikt index. Med andra ord så utgör alla unika index i en fragmenterad samling alltså sammansatta index där ett av fälten är en partitionsnyckel.

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

### <a name="ttl-indexes"></a>TTL-index

Om du vill aktivera förfallodatum för dokumenten i en viss samling måste du skapa ett [TTL-index (Time to Live)](../cosmos-db/time-to-live.md). Ett TTL-index är ett index för fältet _ts med värdet ”expireAfterSeconds”.

Exempel:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Föregående kommando raderar alla dokument i samlingen ```db.coll``` som inte har ändrats under de senaste tio sekunderna.

> [!NOTE]
> **_ts** är ett Azure Cosmos DB-specifikt fält och är inte tillgängligt från MongoDB-klienter. Det är en reserverad (system)egenskap som innehåller tidsstämpeln för senaste ändringen av dokumentet.

## <a name="track-the-index-progress"></a>Spåra indexstatus

3.6-versionen av Azure Cosmos DB:s API för `currentOp()` MongoDB-konton stöder kommandot för att spåra indexförloppet för en databasinstans. Det här kommandot returnerar ett dokument som innehåller information om pågående åtgärder för en databasinstans. Kommandot `currentOp` används för att spåra alla pågående åtgärder i inbyggda MongoDB, medan det här kommandot i Azure Cosmos DB:s API för MongoDB endast stöder spårning av indexåtgärden.

Här är några exempel som `currentOp` visar hur du använder kommandot för att spåra indexets förlopp:

* Hämta indexstatus för en samling:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Hämta indexstatus för alla samlingar i en databas:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Hämta indexstatus för alla databaser och samlingar i ett Azure Cosmos-konto:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

Indexstatusinformationen innehåller en procentandel av förloppet för den aktuella indexoperationen. I följande exempel visas utdatadokumentformatet för olika steg i indexets status:

1. Om indexoperationen på en foo-samlings- och "bardatabas" med 60 % indexering är klar kommer följande utdatadokument att ha följande utdatadokument. `Inprog[0].progress.total`visar 100 som måluppfyllande.

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

2. För en indexåtgärd som just har startat på en "foo"-samling och "bar"-databas kan utdatadokumentet visa 0 % förlopp tills det når en mätbar nivå.

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

3. När pågående indexoperationen är klar visar utdatadokumentet tomma inprog-operationer.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Uppdateringar av bakgrundsindex

Oavsett vilket värde som angetts för egenskapen **Bakgrundsindex** görs alltid indexuppdateringar i bakgrunden. Indexuppdateringar förbrukar RU:s lägre prioritet än andra databasåtgärder. Indexändringar leder därför inte till några driftstopp för skrivningar, uppdateringar eller borttagningar.

När du lägger till ett nytt index används det omedelbart i frågor. Detta innebär att frågor kanske inte returnerar alla matchande resultat, och kommer att göra det utan att returnera några fel. När indexomvandlingen är klar är frågeresultaten konsekventa. Du kan [spåra indexets förlopp](#track-the-index-progress).

## <a name="migrating-collections-with-indexes"></a>Migrera samlingar med index

För närvarande går det bara att skapa unika index när samlingen inte innehåller några dokument. Många av de vanligaste migreringsverktygen för MongoDB försöker skapa unika index när du har importerat data. För att kringgå det här problemet föreslås att användare manuellt skapar motsvarande samlingar och ```mongorestore``` unika index, i `--noIndexRestore` stället för att tillåta migreringsverktyget (för det här beteendet uppnås med hjälp av flaggan på kommandoraden).

## <a name="indexing-for-version-32"></a>Indexering för version 3.2

För Azure Cosmos DB-konton som är kompatibla med 3.2-versionen av MongoDB-trådprotokollet är de tillgängliga indexeringsfunktionerna och standardvärdena olika. Du kan [kontrollera kontots version](mongodb-feature-support-36.md#protocol-support). Du kan uppgradera till 3.6-versionen genom att lämna in en [supportbegäran](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Om du använder version 3.2 beskrivs viktiga skillnader i det här avsnittet med version 3.6.

### <a name="dropping-the-default-indexes-32"></a>Släppa standardindex (3,2)

Till skillnad från 3.6-versionen av Azure Cosmos DB:s API för MongoDB indexerar 3.2-versionen varje egenskap som standard. Följande kommando kan användas för att släppa dessa ```coll```standardindex för en samling:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

När du har tappat standardindexen kan du lägga till ytterligare index enligt version 3.6.

### <a name="compound-indexes-32"></a>Sammansatta index (3,2)

Sammansatta index innehåller referenser till flera fält i ett dokument. Om du vill skapa ett sammansatt index, uppgradera till 3,6 version genom att lämna in en [supportbegäran](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Nästa steg

* [Indexering i Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Ta bort data från Azure Cosmos DB automatiskt med hjälp av förfallodatum](../cosmos-db/time-to-live.md)
