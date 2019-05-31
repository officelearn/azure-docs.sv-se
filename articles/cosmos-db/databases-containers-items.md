---
title: Arbeta med databaser, behållare och objekt i Azure Cosmos DB
description: Den här artikeln beskriver hur du skapar och använder databaser, behållare och objekt i Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 574dd9fd6189b6d0f1e5d455146d6d083ad7ff77
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389471"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Arbeta med databaser, behållare och objekt i Azure Cosmos DB

När du har skapat en [Azure Cosmos DB-konto](account-overview.md) under din Azure-prenumeration kan du hantera data i ditt konto genom att skapa databaser, behållare och objekt. Den här artikeln beskrivs var och en av dessa entiteter. 

Följande bild visar hierarkin för olika enheter i ett Azure Cosmos DB-konto:

![Azure Cosmos-konto-entiteter](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos-databaser

Du kan skapa en eller flera Azure-Cosmos-databaser under ditt konto. En databas är detsamma som ett namnområde. En databas är hanteringsenheten för en uppsättning Azure Cosmos-behållare. I följande tabell visas hur en Azure Cosmos-databas är mappad till olika API-specifika entiteter:

| Azure Cosmos entity | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-databas | Databas | Nyckelutrymme | Databas | Databas | Saknas |

> [!NOTE]
> När du skapar din första tabellen med tabell-API-konton kan skapas en standarddatabas automatiskt i ditt Azure Cosmos-konto.

### <a name="operations-on-an-azure-cosmos-database"></a>Åtgärder på en Azure Cosmos-databas

Du kan interagera med en Azure Cosmos-databas med Azure Cosmos-API: er enligt beskrivningen i följande tabell:

| Åtgärd | Azure CLI | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- |
|Räkna upp alla databaser| Ja | Ja | Ja (database mappas till ett keyspace) | Ja | Ej tillämpligt | Ej tillämpligt |
|Läsa databas| Ja | Ja | Ja (database mappas till ett keyspace) | Ja | Ej tillämpligt | Ej tillämpligt |
|Skapa ny databas| Ja | Ja | Ja (database mappas till ett keyspace) | Ja | Ej tillämpligt | Ej tillämpligt |
|Uppdatera databas| Ja | Ja | Ja (database mappas till ett keyspace) | Ja | Ej tillämpligt | Ej tillämpligt |


## <a name="azure-cosmos-containers"></a>Azure Cosmos-behållare

En Azure Cosmos-behållare är Faktureringsenhet skalbarhet både för etablerat dataflöde och lagring. En behållare är horisontellt partitionerade och replikeras sedan över flera regioner. De objekt som du lägger till behållaren och det dataflöde som du etablerar på den distribueras automatiskt i en uppsättning logiska partitioner baserat på partitionsnyckel. Mer information om partitionering och partitionsnycklar finns [partitionera data](partition-data.md). 

När du skapar en Azure Cosmos-behållare kan konfigurera du dataflöde på något av följande lägen:

* **Dedikerat dataflöde läge**: Dataflödet som tillhandahållits i en behållare enbart för behållaren och den backas upp av att serviceavtalen. Mer information finns i [hur du etablera dataflöde för en Azure Cosmos-behållare](how-to-provision-container-throughput.md).

* **Delade etablerat dataflöde läge**: De här behållarna dela det etablerade dataflödet som med andra behållare i samma databas (exklusive behållare som har konfigurerats med dedikerad etablerat dataflöde). Med andra ord delas dataflöde i databasen mellan alla ”delade dataflöde”-behållare. Mer information finns i [hur du etablera dataflöde för en Azure Cosmos-databas](how-to-provision-database-throughput.md).

> [!NOTE]
> Du kan konfigurera delade och dedikerade dataflöde endast när du skapar databasen och behållare. Om du vill växla från dedikerat dataflöde läge till delade genomflödesläge (och vice versa) när behållaren har skapats, måste du skapa en ny behållare och migrera data till den nya behållaren. Du kan migrera data med hjälp av Azure Cosmos DB-funktionen för ändringsfeed.

En Azure Cosmos-behållare kan skala Elastiskt, om du skapar behållare med etablerat dataflöde för dedikerade eller delade lägen.

En Azure Cosmos-behållare är en schemaoberoende behållare för objekt. Objekt i en behållare kan ha valfri scheman. Till exempel ett objekt som representerar en person och ett objekt som representerar en bil kan placeras i den *samma behållare*. Som standard indexeras automatiskt alla objekt som du lägger till en behållare utan explicita index eller schemahantering. Du kan anpassa beteendet indexering genom att konfigurera den [indexeringspolicy](index-overview.md) i en behållare. 

Du kan ange [Time to Live (TTL)](time-to-live.md) för markerade objekt i en Azure Cosmos-behållare eller för hela behållaren smidigt rensa de objekt från systemet. Azure Cosmos DB tar automatiskt bort objekten när de går ut. Det garanterar även att en fråga som utförs på behållaren inte returnerar objekt som har upphört att gälla inom en fast gräns. Mer information finns i [konfigurera TTL-värdet från din behållare](how-to-time-to-live.md).

Du kan använda [ändringsflödet](change-feed.md) prenumererar du till åtgärdsloggen som hanteras för varje logisk partition för din behållare. Ändringsfeed ger en logg för alla uppdateringar som utförs på behållare, tillsammans med de före och efter-bilder av objekten. Mer information finns i [skapa reaktiva program med hjälp av ändringsflödet](serverless-computing-database.md). Du kan också konfigurera kvarhållningsvaraktighetens för ändringsflödet med ändringsflödet i principen för behållaren. 

Du kan registrera [lagrade procedurer, utlösare, användardefinierade funktioner (UDF)](stored-procedures-triggers-udfs.md), och [sammanfoga procedurer](how-to-manage-conflicts.md) för din Azure Cosmos-behållare. 

Du kan ange en [unika nyckelvillkor](unique-keys.md) på din Azure Cosmos-behållare. Genom att skapa en unik nyckel princip kan kontrollera du unikhet för ett eller flera värden per logisk partitionsnyckel. Om du skapar en behållare med hjälp av en unik nyckel princip kan kan inga nya eller uppdaterade objekt med värden som duplicerar värdena som anges av den unika key-begränsningen skapas. Mer information finns i [unika nyckelvillkor](unique-keys.md).

En Azure Cosmos-behållare är specialiserat till API-specifika entiteter som du ser i följande tabell:

| Azure Cosmos entity | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-behållare | Samling | Tabell | Samling | Graph | Tabell |

### <a name="properties-of-an-azure-cosmos-container"></a>Egenskaperna för en Azure Cosmos-behållare

En Azure Cosmos-behållare har en uppsättning systemdefinierade egenskaper. Beroende på vilken API som du använder kanske vissa egenskaper inte exponeras direkt. I följande tabell beskrivs i listan över systemdefinierade egenskaper:

| Systemdefinierade egenskapen | Systemgenererade eller användarangiven | Syfte | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_ID | System-generated | Unik identifierare för behållare | Ja | Nej | Nej | Nej | Nej |
|\_ETag | System-generated | Enhetstagg som används för optimistisk samtidighetskontroll | Ja | Nej | Nej | Nej | Nej |
|\_TS | System-generated | Senast uppdaterade tidsstämpeln i behållaren | Ja | Nej | Nej | Nej | Nej |
|\_Self | System-generated | Adresserbara URI: N för behållaren | Ja | Nej | Nej | Nej | Nej |
|id | Användarangiven | Användardefinierade unika namnet på behållaren | Ja | Ja | Ja | Ja | Ja |
|indexingPolicy | Användarangiven | Ger möjlighet att ändra index sökväg, Indextyp och indexläge | Ja | Nej | Nej | Nej | Ja |
|timeToLive | Användarangiven | Ger möjlighet att ta bort objekt automatiskt från en behållare efter en viss tidsperiod. Mer information finns i [Time to Live](time-to-live.md). | Ja | Nej | Nej | Nej | Ja |
|changeFeedPolicy | Användarangiven | Används för att läsa ändrade objekt i en behållare. Mer information finns i [Ändringsflödet](change-feed.md). | Ja | Nej | Nej | Nej | Ja |
|uniqueKeyPolicy | Användarangiven | Används för att se till att ett eller flera värden i en logisk partition är unikt. Mer information finns i [unika nyckelvillkor](unique-keys.md). | Ja | Nej | Nej | Nej | Ja |

### <a name="operations-on-an-azure-cosmos-container"></a>Åtgärder på en Azure Cosmos-behållare

En Azure Cosmos-behållare stöder följande åtgärder när du använder någon av Azure Cosmos-API: er:

| Åtgärd | Azure CLI | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- |
| Räkna upp behållare i en databas | Ja | Ja | Ja | Ja | Ej tillämpligt | Ej tillämpligt |
| Läsa en behållare | Ja | Ja | Ja | Ja | Ej tillämpligt | Saknas |
| Skapa en ny behållare | Ja | Ja | Ja | Ja | Ej tillämpligt | Saknas |
| Uppdatera en behållare | Ja | Ja | Ja | Ja | Ej tillämpligt | Saknas |
| Ta bort en container | Ja | Ja | Ja | Ja | Ej tillämpligt | Ej tillämpligt |

## <a name="azure-cosmos-items"></a>Azure Cosmos-objekt

Beroende på vilken API som du använder representerar en Azure Cosmos-objektet antingen ett dokument i en samling, en rad i en tabell, eller en nod eller kanttabell i ett diagram. I följande tabell visas mappningen av API-specifika entiteter till ett Azure Cosmos-objekt:

| Cosmos-entitet | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-objekt | Dokument | Rad | Dokument | Nod eller kanttabell | Objekt |

### <a name="properties-of-an-item"></a>Egenskaper för ett objekt

Varje Azure Cosmos-objekt har följande systemdefinierade egenskaper. Beroende på vilken API som du använder kan några av dem inte exponeras direkt.

| Systemdefinierade egenskapen | Systemgenererade eller användarangiven| Syfte | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_ID | System-generated | Unik identifierare för objektet | Ja | Nej | Nej | Nej | Nej |
|\_ETag | System-generated | Enhetstagg som används för optimistisk samtidighetskontroll | Ja | Nej | Nej | Nej | Nej |
|\_TS | System-generated | Tidsstämpel för senaste uppdateringen av objektet | Ja | Nej | Nej | Nej | Nej |
|\_Self | System-generated | Adresserbara URI: N för objektet | Ja | Nej | Nej | Nej | Nej |
|id | Antingen | Användardefinierade unikt namn i en logisk partition. Om användaren inte anger ID, genererar systemet automatiskt en. | Ja | Ja | Ja | Ja | Ja |
|Godtycklig användardefinierade egenskaper | Användardefinierade | Användardefinierade egenskaper som visas i API-intern representation (inklusive JSON, BSON och CQL) | Ja | Ja | Ja | Ja | Ja |

### <a name="operations-on-items"></a>Åtgärder på objekt

Azure Cosmos-objekt stöd för följande åtgärder. Du kan använda någon av de API: erna för Azure Cosmos för att utföra åtgärder.

| Åtgärd | Azure CLI | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- |
| Infoga, Ersätt, ta bort, Upsert, Läs | Nej | Ja | Ja | Ja | Ja | Ja |

## <a name="next-steps"></a>Nästa steg

Läs mer om dessa uppgifter och begrepp:

* [Etablera dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md)
* [Etablera dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md)
* [Arbeta med logiska partitioner](partition-data.md)
* [Konfigurera TTL-värde på en Azure Cosmos-behållare](how-to-time-to-live.md)
* [Skapa reaktiva program med hjälp av ändringsfeed](change-feed.md)
* [Konfigurera en unik nyckel begränsning på din Azure Cosmos-behållare](unique-keys.md)
