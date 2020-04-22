---
title: Hantera indexering i Azure Cosmos DB:s API för MongoDB
description: Den här artikeln innehåller en översikt över Azure Cosmos DB-indexeringsfunktioner med Hjälp av MongoDB API.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732712"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Hantera indexering i Azure Cosmos DB:s API för MongoDB

Azure Cosmos DB:s API för MongoDB drar nytta av de grundläggande indexhanteringsfunktionerna i Azure Cosmos DB. Den här artikeln fokuserar på hur du lägger till index med Azure Cosmos DB:s API för MongoDB. Du kan också läsa en [översikt över indexering i Azure Cosmos DB](index-overview.md) som är relevant för alla API:er.

## <a name="indexing-for-mongodb-server-version-36"></a>Indexering för MongoDB server version 3.6

Azure Cosmos DB:s API för MongoDB-serverversion 3.6 indexerar automatiskt `_id` fältet, som inte kan tas bort. Det framtvingar automatiskt det `_id` unika i fältet per fragmentnyckel.

Om du vill indexera ytterligare fält använder du indexhanteringskommandona MongoDB. Precis som i MongoDB indexerar Azure Cosmos DB:s `_id` API för MongoDB automatiskt fältet. Den här standardindexeringsprincipen skiljer sig från Azure Cosmos DB SQL API, som indexerar alla fält som standard.

Om du vill använda en sortering på en fråga måste du skapa ett index för de fält som används i sorteringsåtgärden.

## <a name="index-types"></a>Indextyper

### <a name="single-field"></a>Ett fält

Du kan skapa index på ett och samma fält. Sorteringsordningen för det enskilda fältindexet spelar ingen roll. Följande kommando skapar ett index `name`i fältet :

`db.coll.createIndex({name:1})`

En fråga använder flera enstaka fältindex där det är tillgängligt. Du kan skapa upp till 500 enstaka fältindex per behållare.

### <a name="compound-indexes-mongodb-server-version-36"></a>Sammansatta index (MongoDB-server version 3.6)

Azure Cosmos DB:s API för MongoDB stöder sammansatta index för konton som använder trådprotokollet version 3.6. Du kan inkludera upp till åtta fält i ett sammansatt index. Till skillnad från i MongoDB bör du bara skapa ett sammansatt index om frågan behöver sorteras effektivt på flera fält samtidigt. För frågor med flera filter som inte behöver sorteras skapar du flera ettfältsindex i stället för ett enda sammansatt index.

Följande kommando skapar ett sammansatt index `name` `age`i fälten och:

`db.coll.createIndex({name:1,age:1})`

Du kan använda sammansatta index för att sortera effektivt på flera fält samtidigt, vilket visas i följande exempel:

`db.coll.find().sort({name:1,age:1})`

Du kan också använda föregående sammansatta index för att effektivt sortera på en fråga med motsatt sorteringsordning i alla fält. Här är ett exempel:

`db.coll.find().sort({name:-1,age:-1})`

Men sekvensen av sökvägarna i det sammansatta indexet måste exakt matcha frågan. Här är ett exempel på en fråga som skulle kräva ytterligare ett sammansatt index:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Index med flera nycklar

Azure Cosmos DB skapar multikey index för att indexera innehåll som lagras i matriser. Om du indexerar ett fält med ett matrisvärde indexerar Azure Cosmos DB automatiskt varje element i matrisen.

### <a name="geospatial-indexes"></a>Geospatiala index

Många geospatiala operatörer kommer att dra nytta av geospatiala index. Azure Cosmos DB:s API för MongoDB stöder `2dsphere` för närvarande index. API:et stöder `2d` ännu inte index.

Här är ett exempel på hur du skapar `location` ett geospatialt index på fältet:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Textindex

Azure Cosmos DB:s API för MongoDB stöder för närvarande inte textindex. För textsökningsfrågor på strängar bör du använda [Azure Cognitive Search-integrering](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) med Azure Cosmos DB.

## <a name="index-properties"></a>Indexegenskaper

Följande åtgärder är vanliga för konton som betjänar trådprotokoll version 3.6 och konton som betjänar tidigare versioner. Du kan läsa mer om [index och indexerade egenskaper som stöds](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Unika index

[Unika index](unique-keys.md) är användbara för att framtvinga att två eller flera dokument inte innehåller samma värde för indexerade fält.

> [!IMPORTANT]
> Unika index kan bara skapas när samlingen är tom (innehåller inga dokument).

Följande kommando skapar ett unikt index `student_id`i fältet :

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

För fragmenterade samlingar måste du ange shard -tangenten (partition) för att skapa ett unikt index. Med andra ord så utgör alla unika index i en fragmenterad samling alltså sammansatta index där ett av fälten är en partitionsnyckel.

Följande kommandon skapar en fragmenterad ```coll``` samling (fragmentnyckeln är) ```university```med ett `student_id` `university`unikt index i fälten och:

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

I föregående exempel returnerar utelämnande av ```"university":1``` satsen ett fel med följande meddelande:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL-index

Om du vill aktivera dokumentets förfallodatum i en viss samling måste du skapa ett [TTL-index (Time-to-live).](../cosmos-db/time-to-live.md) Ett TTL-index är `_ts` ett index `expireAfterSeconds` på fältet med ett värde.

Exempel:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Föregående kommando tar bort alla ```db.coll``` dokument i samlingen som inte har ändrats under de senaste 10 sekunderna.

> [!NOTE]
> Fältet **_ts** är specifikt för Azure Cosmos DB och är inte tillgängligt från MongoDB-klienter. Det är en reserverad (system)-egenskap som innehåller tidsstämpeln för dokumentets senaste ändring.

## <a name="track-index-progress"></a>Spåra indexstatus

Version 3.6 av Azure Cosmos DB:s API `currentOp()` för MongoDB stöder kommandot för att spåra indexförloppet för en databasinstans. Det här kommandot returnerar ett dokument som innehåller information om pågående åtgärder för en databasinstans. Du använder `currentOp` kommandot för att spåra alla pågående åtgärder i inbyggda MongoDB. I Azure Cosmos DB:s API för MongoDB stöder det här kommandot bara spårning av indexåtgärden.

Här är några exempel som `currentOp` visar hur du använder kommandot för att spåra indexstatus:

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

### <a name="examples-of-index-progress-output"></a>Exempel på indexutveckling

Indexstatusinformationen visar hur stor procentandel av förloppet för den aktuella indexoperationen. Här är ett exempel som visar utdatadokumentformatet för olika steg i indexets status:

- En indexåtgärd på en "foo"-samling och "bar"-databas som är 60 procent färdig kommer att ha följande utdatadokument. Fältet `Inprog[0].progress.total` visar 100 som målslutprocent.

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

- Om en indexåtgärd just har startat på en "foo"-samling och "bar"-databas kan utdatadokumentet visa 0 procents förlopp tills det når en mätbar nivå.

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

- När pågående indexoperationen är klar visar `inprog` utdatadokumentet tomma operationer.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Uppdateringar av bakgrundsindex

Oavsett vilket värde som angetts för egenskapen **Bakgrundsindex** görs alltid indexuppdateringar i bakgrunden. Eftersom indexuppdateringar förbrukar frågeenheter (RU: er) med lägre prioritet än andra databasåtgärder, resulterar indexändringar inte i några driftstopp för skrivningar, uppdateringar eller borttagningar.

När du lägger till ett nytt index används indexet omedelbart i frågor. Detta innebär att frågor kanske inte returnerar alla matchande resultat och kommer att göra det utan att returnera några fel. När indexomvandlingen är klar är frågeresultaten konsekventa. Du kan [spåra indexstatus](#track-index-progress).

## <a name="migrate-collections-with-indexes"></a>Migrera samlingar med index

För närvarande kan du bara skapa unika index när samlingen inte innehåller några dokument. Populära MongoDB migreringsverktyg försöker skapa unika index efter att ha importerat data. Om du vill kringgå problemet kan du manuellt skapa motsvarande samlingar och unika index i stället för att tillåta migreringsverktyget att prova. (Du kan uppnå ```mongorestore``` detta genom `--noIndexRestore` att använda flaggan på kommandoraden.)

## <a name="indexing-for-mongodb-version-32"></a>Indexering för MongoDB version 3.2

Tillgängliga indexeringsfunktioner och standardvärden är olika för Azure Cosmos-konton som är kompatibla med version 3.2 i MongoDB-trådprotokollet. Du kan [kontrollera kontots version](mongodb-feature-support-36.md#protocol-support). Du kan uppgradera till 3.6-versionen genom att lämna in en [supportbegäran](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Om du använder version 3.2 beskrivs viktiga skillnader i det här avsnittet med version 3.6.

### <a name="dropping-default-indexes-version-32"></a>Släppa standardindex (version 3.2)

Till skillnad från 3.6-versionen av Azure Cosmos DB:s API för MongoDB indexerar version 3.2 varje egenskap som standard. Du kan använda följande kommando för att släppa```coll```dessa standardindex för en samling ( ):

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

När du har tappat standardindexen kan du lägga till fler index som i version 3.6.

### <a name="compound-indexes-version-32"></a>Sammansatta index (version 3.2)

Sammansatta index innehåller referenser till flera fält i ett dokument. Om du vill skapa ett sammansatt index uppgraderar du till version 3.6 genom att lämna in en [supportbegäran](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Nästa steg

* [Indexering i Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Ta bort data från Azure Cosmos DB automatiskt med hjälp av förfallodatum](../cosmos-db/time-to-live.md)
