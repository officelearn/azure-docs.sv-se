---
title: MongoDB-tilläggs kommandon för att hantera data i Azure Cosmos DB s API för MongoDB
description: Den här artikeln beskriver hur du använder MongoDB-tilläggs kommandon för att hantera data som lagras i Azure Cosmos DB s API för MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f57b274715eb1c8a4d517f5655c09c366574d412
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445208"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Använd MongoDB-tilläggs kommandon för att hantera data som lagras i Azure Cosmos DB s API för MongoDB 

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan kommunicera med Azure Cosmos DBens API för MongoDB genom att använda någon av [MongoDB-klient driv rutinerna](https://docs.mongodb.org/ecosystem/drivers)med öppen källkod. Azure Cosmos DBens API för MongoDB gör det möjligt att använda befintliga klient driv rutiner genom att följa [MongoDB-Wire-protokollet](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Genom att använda Azure Cosmos DBs API för MongoDB kan du dra nytta av fördelarna Cosmos DB till exempel global distribution, automatisk horisontell partitionering, hög tillgänglighet, fördröjnings garantier, automatisk, kryptering i vila, säkerhets kopiering och många fler, samtidigt som du behåller dina investeringar i MongoDB-appen.

## <a name="mongodb-protocol-support"></a>Stöd för MongoDB-protokoll

Som standard är Azure Cosmos DBs API för MongoDB kompatibel med MongoDB Server version 3,2. mer information finns i [funktioner som stöds och syntax](mongodb-feature-support.md). De funktioner eller fråge operatörer som läggs till i MongoDB version 3,4 är för närvarande tillgängliga som en för hands version i Azure Cosmos DBs API för MongoDB. Följande tilläggs kommandon stöder Azure Cosmos DB vissa funktioner när du utför CRUD-åtgärder på data som lagras i Azure Cosmos DB s API för MongoDB:

* [Skapa databas](#create-database)
* [Uppdatera databas](#update-database)
* [Hämta databas](#get-database)
* [Skapa samling](#create-collection)
* [Uppdatera samling](#update-collection)
* [Hämta samling](#get-collection)

## <a id="create-database"></a>Skapa databas

Kommandot Skapa databas tillägg skapar en ny MongoDB-databas. Databas namnet används från databasens kontext mot vilken kommandot körs. Formatet för CreateDatabase-kommandot är följande:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

I följande tabell beskrivs parametrarna i kommandot:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
| Anpassad   |  sträng  |   Namnet på det anpassade kommandot, det måste vara "CreateDatabase".      |
| offerThroughput | int  | Tillhandahållet data flöde som du har angett för databasen. Den här parametern är valfri. |

### <a name="output"></a>Resultat

Returnerar ett anpassat standard kommando svar. Se [standardutdata](#default-output) för det anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Skapa en databas**

Om du vill skapa en databas med namnet "test" använder du följande kommando:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Skapa en databas med data flöde**

Om du vill skapa en databas med namnet "test" och ett tillhandahållet data flöde på 1000 ru: er, använder du följande kommando:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a>Uppdatera databas

Kommandot uppdatera databas tillägg uppdaterar de egenskaper som är associerade med den angivna databasen. För närvarande kan du bara uppdatera egenskapen "offerThroughput".

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

I följande tabell beskrivs parametrarna i kommandot:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
| Anpassad    |    sträng     |   Namnet på det anpassade kommandot. Måste vara "UpdateDatabase".      |
|  offerThroughput   |  int       |     Nytt allokerat data flöde som du vill ange för databasen.    |

### <a name="output"></a>Resultat

Returnerar ett anpassat standard kommando svar. Se [standardutdata](#default-output) för det anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Uppdatera det etablerade data flödet som är associerat med en databas**

Om du vill uppdatera det etablerade data flödet för en databas med namnet "test" till 1200 ru: er, använder du följande kommando:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a>Hämta databas

Kommandot Hämta databas tillägg returnerar databasobjektet. Databas namnet används från databas kontexten som kommandot körs mot.

```
{
  customAction: "GetDatabase"
}
```

I följande tabell beskrivs parametrarna i kommandot:


|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  Anpassad   |   sträng      |   Namnet på det anpassade kommandot. Måste vara "GetDatabase"|
        
### <a name="output"></a>Resultat

Om kommandot lyckas innehåller svaret ett dokument med följande fält:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Status för svar. 1 = = lyckades. 0 = = Miss lyckas.      |
| `database`    |    `string`        |   Namnet på databasen.      |
|   `provisionedThroughput`  |    `int`      |    Allokerat data flöde som har angetts för databasen. Detta är en valfri svars parameter.     |

Om kommandot Miss lyckas returneras ett anpassat standard kommando svar. Se [standardutdata](#default-output) för det anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Hämta databasen**

Använd följande kommando för att hämta databasobjektet för en databas med namnet "test":

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a>Skapa samling

Kommandot Skapa samlings tillägg skapar en ny MongoDB-samling. Databas namnet används från databasens kontext mot vilken kommandot körs. Formatet för CreateCollection-kommandot är följande:

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
| Anpassad    | sträng | Namnet på det anpassade kommandot. Måste vara "CreateCollection"     |
| samling      | sträng | Samlingens namn                                   |
| offerThroughput | int    | Tillhandahållet data flöde som ska anges för-databasen. Det är en valfri parameter |
| shardKey        | sträng | Shard nyckel Sök väg för att skapa en shardade-samling. Det är en valfri parameter |

### <a name="output"></a>Resultat

Returnerar ett anpassat standard kommando svar. Se [standardutdata](#default-output) för det anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Skapa en unsharded-samling**

Om du vill skapa en unsharded-samling med namnet "testCollection" och ett allokerat data flöde på 1000 ru: er använder du följande kommando: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Skapa en shardade-samling**

Om du vill skapa en shardade-samling med namnet "testCollection" och ett allokerat data flöde på 1000 ru: er använder du följande kommando:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a>Uppdatera samling

Kommandot för att uppdatera samlings tillägg uppdaterar egenskaperna som är associerade med den angivna samlingen.

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
|  Anpassad   |   sträng      |   Namnet på det anpassade kommandot. Måste vara "UpdateCollection".      |
|  samling   |   sträng      |   Samlingens namn.       |
| offerThroughput   |int|   Tillhandahållet data flöde som ska anges för samlingen.|

## <a name="output"></a>Resultat

Returnerar ett anpassat standard kommando svar. Se [standardutdata](#default-output) för det anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Uppdatera det allokerade data flödet som är associerat med en samling**

Om du vill uppdatera det etablerade data flödet för en samling med namnet "testCollection" till 1200 ru: er använder du följande kommando:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a>Hämta samling

Det anpassade kommandot Hämta samling returnerar objektet Collection.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

I följande tabell beskrivs parametrarna i kommandot:


|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
| Anpassad    |   sträng      |   Namnet på det anpassade kommandot. Måste vara "GetCollection".      |
| samling    |    sträng     |    Samlingens namn.     |

### <a name="output"></a>Resultat

Om kommandot lyckas innehåller svaret ett dokument med följande fält


|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status för svar. 1 = = lyckades. 0 = = Miss lyckas.      |
| `database`    |    `string`     |   Namnet på databasen.      |
| `collection`    |    `string`     |    Samlingens namn.     |
|  `shardKeyDefinition`   |   `document`      |  Index Specifikations dokument som används som en Shard nyckel. Detta är en valfri svars parameter.       |
|  `provisionedThroughput`   |   `int`      |    Tillhandahållet data flöde som ska anges för samlingen. Detta är en valfri svars parameter.     |

Om kommandot Miss lyckas returneras ett anpassat standard kommando svar. Se [standardutdata](#default-output) för det anpassade kommandot för parametrarna i utdata.

### <a name="examples"></a>Exempel

**Hämta samlingen**

Använd följande kommando för att hämta samlings objekt för en samling med namnet "testCollection":

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a>Standardutdata för ett anpassat kommando

Om inget anges innehåller ett anpassat svar ett dokument med följande fält:

|**Fält**|**Typ** |**Beskrivning** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status för svar. 1 = = lyckades. 0 = = Miss lyckas.      |
| `code`    |   `int`      |   Returneras endast om kommandot misslyckades (t. ex. OK = = 0). Innehåller fel koden för MongoDB. Detta är en valfri svars parameter.      |
|  `errMsg`   |  `string`      |    Returneras endast om kommandot misslyckades (t. ex. OK = = 0). Innehåller ett användarvänligt fel meddelande. Detta är en valfri svars parameter.      |

## <a name="next-steps"></a>Nästa steg

Härnäst kan du fortsätta med att lära dig följande Azure Cosmos DB koncept: 

* [Indexering i Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Ta bort data från Azure Cosmos DB automatiskt med hjälp av förfallodatum](../cosmos-db/time-to-live.md)
