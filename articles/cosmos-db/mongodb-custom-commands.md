---
title: MongoDB-tillägg-kommandon för att hantera data som lagras i Azure Cosmos DB API för MongoDB
description: Den här artikeln beskriver hur du använder MongoDB-tilläggskommandon för att hantera data som lagras i Azure Cosmos DB API för MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: aef77f121f20d867c8ec5e764d8c9639c961713d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58876896"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Använda MongoDB-tillägg-kommandon för att hantera data som lagras i Azure Cosmos DB API för MongoDB 

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan kommunicera med Azure Cosmos DB: s API för MongoDB med någon av öppen källkod [MongoDB-klientdrivrutiner](https://docs.mongodb.org/ecosystem/drivers). Azure Cosmos DB: s API för MongoDB kan du använda befintliga klientdrivrutiner genom att följa den [MongoDB-protokollet](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Du kan dra nytta av fördelarna Cosmos DB som global distribution, automatisk delning, hög tillgänglighet, svarstidsgarantier, automatisk, kryptering i vila, säkerhetskopiering, med hjälp av Azure Cosmos DB: s API för MongoDB och många fler, medan bevara dina investeringar i din MongoDB-app.

## <a name="mongodb-protocol-support"></a>Protokollstöd för MongoDB

Som standard kan Azure Cosmos DB: s API för MongoDB är kompatibelt med MongoDB server-version 3.2, mer information, se [funktioner och syntax som stöds](mongodb-feature-support.md). Funktioner eller frågeoperatorer som lagts till i MongoDB version 3.4 finns för närvarande som förhandsversion i Azure Cosmos DB: s API för MongoDB. Följande tilläggskommandon för stöder Azure Cosmos DB specifika funktioner när du utför CRUD-åtgärder på data som lagras i Azure Cosmos DB API för MongoDB:

* [Skapa databas](#create-database)
* [Uppdatera databas](#update-database)
* [Hämta databas](#get-database)
* [Skapa samling](#create-collection)
* [Uppdatera samlingen](#update-collection)
* [Hämta samling](#get-collection)

## <a id="create-database"></a> Skapa databas

Kommandot create database tillägget skapar en ny MongoDB-databas. Namnet på databasen används i kontexten databaser som kommandot körs. Formatet för kommandot CreateDatabase är följande:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

I följande tabell beskrivs parametrarna i kommandot:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
| CustomAction   |  sträng  |   Namn på anpassade kommandot det måste vara ”CreateDatabase”.      |
| offerThroughput | int  | Dataflöde som du angett för databasen. Den här parametern är valfri. |

### <a name="output"></a>Resultat

Returnerar ett svar för standard-kommando. Se den [standard utdata](#default-output) anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Skapa en databas**

Om du vill skapa en databas med namnet ”test”, använder du följande kommando:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Skapa en databas med dataflöde**

Att skapa en databas med namnet ”test” och etablerade dataflöden med 1000 enheter för programbegäran, använder du följande kommando:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a> Uppdatera databas

Kommandot update databas tillägget uppdaterar egenskaperna som är associerade med den angivna databasen. För närvarande kan du bara uppdatera egenskapen ”offerThroughput”.

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

I följande tabell beskrivs parametrarna i kommandot:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
| CustomAction    |    sträng     |   Namnet på det anpassade kommandot. Måste vara ”UpdateDatabase”.      |
|  offerThroughput   |  int       |     Ny dataflöde som du vill ange för databasen.    |

### <a name="output"></a>Resultat

Returnerar ett svar för standard-kommando. Se den [standard utdata](#default-output) anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Uppdatera det etablerade dataflödet som är associerade med en-databas**

Om du vill uppdatera det etablerade dataflödet för en databas med namnet ”test” till 1200 ru: er, använder du följande kommando:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a> Hämta databas

Kommandot get databas tillägget returnerar databasobjektet. Databasnamnet används från databasens kontext som kommandot körs.

```
{
  customAction: "GetDatabase"
}
```

I följande tabell beskrivs parametrarna i kommandot:


|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  CustomAction   |   sträng      |   Namnet på det anpassade kommandot. Måste vara ”GetDatabase”|
        
### <a name="output"></a>Resultat

Om kommandot lyckas innehåller svaret ett dokument med följande fält:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Status för svaret. 1 == lyckades. 0 == fel.      |
| `database`    |    `string`        |   Namnet på databasen.      |
|   `provisionedThroughput`  |    `int`      |    Etablerat dataflöde som har angetts i databasen. Det här är en valfri svar-parameter.     |

Om kommandot misslyckas, returneras ett svar för standard-kommando. Se den [standard utdata](#default-output) anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Hämta databasen**

Om du vill hämta databasobjektet för en databas med namnet ”test”, använder du följande kommando:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a> Skapa samling

Skapa samlingen Tilläggskommandot skapar en ny MongoDB-samling. Namnet på databasen används i kontexten databaser som kommandot körs. Formatet för kommandot CreateCollection är följande:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

I följande tabell beskrivs parametrarna i kommandot:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
| CustomAction    | sträng | Namnet på det anpassade kommandot. Måste vara ”CreateDatabase”     |
| samling      | sträng | Namnet på samlingen                                   |
| offerThroughput | int    | Etablerat dataflöde för att ställa in i databasen. Det är en valfri parameter |
| shardKey        | sträng | Shard Nyckelsökväg för att skapa en fragmenterad samling. Det är en valfri parameter |

### <a name="output"></a>Resultat

Returnerar ett svar för standard-kommando. Se den [standard utdata](#default-output) anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Skapa en unsharded-samling**

Om du vill skapa en unsharded samling med namnet ”testCollection” och etablerat dataflöde på 1000 ru: er, använder du följande kommando: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Skapa en fragmenterad samling**

Om du vill skapa en fragmenterad samling med namnet ”testCollection” och etablerat dataflöde på 1000 ru: er, använder du följande kommando:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a> Uppdatera samlingen

Kommandot update samling tillägget uppdaterar egenskaperna som är associerade med den angivna samlingen.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

I följande tabell beskrivs parametrarna i kommandot:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  CustomAction   |   sträng      |   Namnet på det anpassade kommandot. Måste vara ”UpdateCollection”.      |
|  samling   |   sträng      |   Namnet på samlingen.       |
| offerThroughput   |int|   Etablerat dataflöde för att ange för samlingen.|

## <a name="output"></a>Resultat

Returnerar ett svar för standard-kommando. Se den [standard utdata](#default-output) anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Uppdatera det etablerade dataflödet som är associerade med en samling**

Om du vill uppdatera det etablerade dataflödet för en samling med namnet ”testCollection” till 1200 ru: er, använder du följande kommando:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a> Hämta samling

Kommandot get samling anpassade returnerar samlingsobjektet.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

I följande tabell beskrivs parametrarna i kommandot:


|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
| CustomAction    |   sträng      |   Namnet på det anpassade kommandot. Måste vara ”GetCollection”.      |
| samling    |    sträng     |    Namnet på samlingen.     |

### <a name="output"></a>Resultat

Om kommandot lyckas innehåller svaret ett dokument med följande fält


|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status för svaret. 1 == lyckades. 0 == fel.      |
| `database`    |    `string`     |   Namnet på databasen.      |
| `collection`    |    `string`     |    Namnet på samlingen.     |
|  `shardKeyDefinition`   |   `document`      |  Indexera specifikationsdokument som används som en shardnyckel. Det här är en valfri svar-parameter.       |
|  `provisionedThroughput`   |   `int`      |    Etablerat dataflöde för att ange för samlingen. Det här är en valfri svar-parameter.     |

Om kommandot misslyckas, returneras ett svar för standard-kommando. Se den [standard utdata](#default-output) anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Hämta samlingen**

Om du vill hämta samlingsobjektet för en samling som heter ”testCollection”, använder du följande kommando:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a> Standardutdata för ett anpassat kommando

Om inte anges innehåller en anpassad svaret ett dokument med följande fält:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status för svaret. 1 == lyckades. 0 == fel.      |
| `code`    |   `int`      |   Returneras bara när kommandot misslyckades (d.v.s. ok == 0). Innehåller MongoDB-felkoden. Det här är en valfri svar-parameter.      |
|  `errMsg`   |  `string`      |    Returneras bara när kommandot misslyckades (d.v.s. ok == 0). Innehåller ett användarvänligt felmeddelande. Det här är en valfri svar-parameter.      |

## <a name="next-steps"></a>Nästa steg

Sedan kan du fortsätta till Läs följande Azure Cosmos DB-begrepp: 

* [Indexering i Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Ta bort data från Azure Cosmos DB automatiskt med hjälp av förfallodatum](../cosmos-db/time-to-live.md)