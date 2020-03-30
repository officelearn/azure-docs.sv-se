---
title: MongoDB-tilläggskommandon för att hantera data i Azure Cosmos DB:s API för MongoDB
description: I den här artikeln beskrivs hur du använder MongoDB-tilläggskommandon för att hantera data som lagras i Azure Cosmos DB:s API för MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f57b274715eb1c8a4d517f5655c09c366574d412
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445208"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Använd MongoDB-tilläggskommandon för att hantera data som lagras i Azure Cosmos DB:s API för MongoDB 

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan kommunicera med Azure Cosmos DB:s API för MongoDB med hjälp av någon av [mongoDb-klientdrivrutinerna](https://docs.mongodb.org/ecosystem/drivers)med öppen källkod . Azure Cosmos DB:s API för MongoDB gör det möjligt att använda befintliga klientdrivrutiner genom att följa [MongoDB-trådprotokollet](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Genom att använda Azure Cosmos DB:s API för MongoDB kan du dra nytta av fördelarna med Cosmos DB, till exempel global distribution, automatisk sharding, hög tillgänglighet, latensgarantier, automatiska kryptering i vila, säkerhetskopior och många fler, samtidigt som dina investeringar bevaras, samtidigt som dina investeringar bevaras i din MongoDB-app.

## <a name="mongodb-protocol-support"></a>Support för MongoDB-protokoll

Som standard är Azure Cosmos DB:s API för MongoDB kompatibelt med MongoDB-serverversion 3.2, för mer information, se [funktioner och syntax som stöds](mongodb-feature-support.md). De funktioner eller frågeoperatorer som läggs till i MongoDB version 3.4 är för närvarande tillgängliga som en förhandsversion i Azure Cosmos DB:s API för MongoDB. Följande tilläggskommandon stöder Azure Cosmos DB-specifika funktioner när CRUD-åtgärder utförs på data som lagras i Azure Cosmos DB:s API för MongoDB:

* [Skapa databas](#create-database)
* [Uppdatera databas](#update-database)
* [Hämta databas](#get-database)
* [Skapa samling](#create-collection)
* [Uppdatera samling](#update-collection)
* [Hämta samling](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>Skapa databas

Kommandot Skapa databastillägg skapar en ny MongoDB-databas. Databasnamnet används från databaskontexten som kommandot körs emot. Formatet för kommandot CreateDatabase är följande:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

I följande tabell beskrivs parametrarna i kommandot:

|**Field**|**Typ** |**Beskrivning** |
|---------|---------|---------|
| anpassadaction   |  sträng  |   Namnet på det anpassade kommandot, måste det vara "CreateDatabase".      |
| offerGenomströmning | int  | Etablerat dataflöde som du anger i databasen. Den här parametern är valfri. |

### <a name="output"></a>Resultat

Returnerar ett anpassat standardkommandosvar. Se [standardutdata för](#default-output) anpassat kommando för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Skapa en databas**

Om du vill skapa en databas med namnet "test" använder du följande kommando:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Skapa en databas med dataflöde**

Om du vill skapa en databas med namnet "test" och etablerat dataflöde för 1000 ru: er använder du följande kommando:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>Uppdatera databas

Kommandot Uppdatera databastillägg uppdaterar de egenskaper som är associerade med den angivna databasen. För närvarande kan du bara uppdatera egenskapen "offerThroughput".

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

I följande tabell beskrivs parametrarna i kommandot:

|**Field**|**Typ** |**Beskrivning** |
|---------|---------|---------|
| anpassadaction    |    sträng     |   Namnet på det anpassade kommandot. Måste vara "UpdateDatabase".      |
|  offerGenomströmning   |  int       |     Nytt etablerat dataflöde som du vill ange i databasen.    |

### <a name="output"></a>Resultat

Returnerar ett anpassat standardkommandosvar. Se [standardutdata för](#default-output) anpassat kommando för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Uppdatera det etablerade dataflödet som är associerat med en databas**

Om du vill uppdatera det etablerade dataflödet för en databas med namnet "test" till 1200 ru: er använder du följande kommando:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>Hämta databas

Kommandot hämta databastillägg returnerar databasobjektet. Databasnamnet används från databaskontexten som kommandot körs emot.

```
{
  customAction: "GetDatabase"
}
```

I följande tabell beskrivs parametrarna i kommandot:


|**Field**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  anpassadaction   |   sträng      |   Namnet på det anpassade kommandot. Måste vara "GetDatabase"|
        
### <a name="output"></a>Resultat

Om kommandot lyckas innehåller svaret ett dokument med följande fält:

|**Field**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Status för svar. 1 == framgång. 0 == fel.      |
| `database`    |    `string`        |   Databasens namn.      |
|   `provisionedThroughput`  |    `int`      |    Etablerat dataflöde som anges i databasen. Detta är en valfri svarsparameter.     |

Om kommandot misslyckas returneras ett anpassat standardkommandosvar. Se [standardutdata för](#default-output) anpassat kommando för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Hämta databasen**

Om du vill hämta databasobjektet för en databas med namnet "test" använder du följande kommando:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>Skapa samling

Kommandot Skapa samlingstillägg skapar en ny MongoDB-samling. Databasnamnet används från databaskontexten som kommandot körs emot. Formatet för kommandot CreateCollection är följande:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

I följande tabell beskrivs parametrarna i kommandot:

|**Field**|**Typ** |**Beskrivning** |
|---------|---------|---------|
| anpassadaction    | sträng | Namnet på det anpassade kommandot. Måste vara "CreateCollection"     |
| samling      | sträng | Samlingens namn                                   |
| offerGenomströmning | int    | Etablerat dataflöde som ska anges i databasen. Det är en valfri parameter |
| shardKey (shardKey)        | sträng | Shard Key-sökvägen för att skapa en fragmenterad samling. Det är en valfri parameter |

### <a name="output"></a>Resultat

Returnerar ett anpassat standardkommandosvar. Se [standardutdata för](#default-output) anpassat kommando för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Skapa en ohård samling**

Om du vill skapa en oordnad samling med namnet "testCollection" och etablerat dataflöde på 1 000 ru: er använder du följande kommando: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Skapa en fragmenterad samling**

Om du vill skapa en fragmenterad samling med namnet "testCollection" och etablerat dataflöde på 1 000 ru: er använder du följande kommando:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>Uppdatera samling

Kommandot Update collection Extension uppdaterar de egenskaper som är associerade med den angivna samlingen.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

I följande tabell beskrivs parametrarna i kommandot:

|**Field**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  anpassadaction   |   sträng      |   Namnet på det anpassade kommandot. Måste vara "UpdateCollection".      |
|  samling   |   sträng      |   Samlingens namn.       |
| offerGenomströmning   |int|   Etablerat dataflöde som ska anges i samlingen.|

## <a name="output"></a>Resultat

Returnerar ett anpassat standardkommandosvar. Se [standardutdata för](#default-output) anpassat kommando för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Uppdatera det etablerade dataflödet som är associerat med en samling**

Om du vill uppdatera det etablerade dataflödet för en samling med namnet "testCollection" till 1200 RU:er använder du följande kommando:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>Hämta samling

Det anpassade kommandot hämta samling returnerar samlingsobjektet.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

I följande tabell beskrivs parametrarna i kommandot:


|**Field**|**Typ** |**Beskrivning** |
|---------|---------|---------|
| anpassadaction    |   sträng      |   Namnet på det anpassade kommandot. Måste vara "GetCollection".      |
| samling    |    sträng     |    Samlingens namn.     |

### <a name="output"></a>Resultat

Om kommandot lyckas innehåller svaret ett dokument med följande fält


|**Field**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status för svar. 1 == framgång. 0 == fel.      |
| `database`    |    `string`     |   Databasens namn.      |
| `collection`    |    `string`     |    Samlingens namn.     |
|  `shardKeyDefinition`   |   `document`      |  Indexspecifikationsdokument som används som fragmentnyckel. Detta är en valfri svarsparameter.       |
|  `provisionedThroughput`   |   `int`      |    Etablerat dataflöde som ska anges i samlingen. Detta är en valfri svarsparameter.     |

Om kommandot misslyckas returneras ett anpassat standardkommandosvar. Se [standardutdata för](#default-output) anpassat kommando för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Hämta samlingen**

Om du vill hämta samlingsobjektet för en samling med namnet "testCollection" använder du följande kommando:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>Standardutdata för ett anpassat kommando

Om inget anges innehåller ett anpassat svar ett dokument med följande fält:

|**Field**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status för svar. 1 == framgång. 0 == fel.      |
| `code`    |   `int`      |   Returneras endast när kommandot misslyckades (d.v.s. ok == 0). Innehåller MongoDB-felkoden. Detta är en valfri svarsparameter.      |
|  `errMsg`   |  `string`      |    Returneras endast när kommandot misslyckades (d.v.s. ok == 0). Innehåller ett användarvänligt felmeddelande. Detta är en valfri svarsparameter.      |

## <a name="next-steps"></a>Nästa steg

Därefter kan du fortsätta att lära dig följande Azure Cosmos DB begrepp: 

* [Indexering i Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Ta bort data från Azure Cosmos DB automatiskt med hjälp av förfallodatum](../cosmos-db/time-to-live.md)
