---
title: Arbeta med databaser, behållare och objekt i Azure Cosmos DB
description: I den här artikeln beskrivs hur du skapar och använder databaser, behållare och objekt i Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 43a842c3b6d6d421eca4196c7f3facc7876318cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246791"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Arbeta med databaser, behållare och objekt i Azure Cosmos DB

När du har skapat ett [Azure Cosmos DB-konto](account-overview.md) under din Azure-prenumeration kan du hantera data i ditt konto genom att skapa databaser, behållare och objekt. I den här artikeln beskrivs var och en av dessa entiteter. 

Följande avbildning visar hierarkin för olika entiteter i ett Azure Cosmos DB-konto:

![Azure Cosmos-kontoentiteter](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos-databaser

Du kan skapa en eller flera Azure Cosmos-databaser under ditt konto. En databas är jämförbar med ett namnområde. En databas är hanteringsenhet för en uppsättning Azure Cosmos-behållare. Följande tabell visar hur en Azure Cosmos-databas mappas till olika API-specifika entiteter:

| Azure Cosmos-entitet | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-databas | Databas | Nyckelutrymme | Databas | Databas | Ej tillämpligt |

> [!NOTE]
> När du skapar din första tabell med Tabell API-konton skapas en standarddatabas automatiskt i ditt Azure Cosmos-konto.

### <a name="operations-on-an-azure-cosmos-database"></a>Åtgärder på en Azure Cosmos-databas

Du kan interagera med en Azure Cosmos-databas med Azure Cosmos API:er enligt beskrivningen i följande tabell:

| Åtgärd | Azure CLI | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- |
|Räkna upp alla databaser| Ja | Ja | Ja (databasen mappas till ett nyckelområde) | Ja | Ej tillämpligt | Ej tillämpligt |
|Läsa databas| Ja | Ja | Ja (databasen mappas till ett nyckelområde) | Ja | Ej tillämpligt | Ej tillämpligt |
|Skapa ny databas| Ja | Ja | Ja (databasen mappas till ett nyckelområde) | Ja | Ej tillämpligt | Ej tillämpligt |
|Uppdatera databas| Ja | Ja | Ja (databasen mappas till ett nyckelområde) | Ja | Ej tillämpligt | Ej tillämpligt |


## <a name="azure-cosmos-containers"></a>Azure Cosmos-containrar

En Azure Cosmos-behållare är skalbarhetsenhet både för etablerat dataflöde och lagring. En behållare partitioneras horisontellt och replikeras sedan över flera regioner. De objekt som du lägger till i behållaren och det dataflöde som du etablerar på den distribueras automatiskt över en uppsättning logiska partitioner baserat på partitionsnyckeln. Mer information om partitionering och partitionsnycklar finns i [Partitionsdata](partition-data.md). 

När du skapar en Azure Cosmos-behållare konfigurerar du dataflödet i något av följande lägen:

* **Dedikerat etablerat dataflödesläge:** Dataflödet som etablerats på en behållare är exklusivt reserverat för den behållaren och det backas upp av SLA:erna. Mer information finns i [Så här etablerar du dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).

* **Delat etablerat dataflödesläge**: Dessa behållare delar det etablerade dataflödet med de andra behållarna i samma databas (exklusive behållare som har konfigurerats med dedikerat etablerat dataflöde). Med andra ord delas det etablerade dataflödet i databasen mellan alla behållare för "delat dataflöde". Mer information finns i [Så här etablerar du dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).

> [!NOTE]
> Du kan bara konfigurera delat och dedikerat dataflöde när du skapar databasen och behållaren. Om du vill byta från dedikerat dataflödesläge till delat dataflödesläge (och vice versa) efter det att containern har skapats måste du skapa en ny container och migrera data till den nya containern. Du kan migrera data med hjälp av Azure Cosmos DB ändra feed-funktionen.

En Azure Cosmos-behållare kan skalas elastiskt, oavsett om du skapar behållare med hjälp av dedikerade eller delade etablerade dataflödeslägen.

En Azure Cosmos-behållare är en schemaasoberoende behållare med objekt. Objekt i en behållare kan ha godtyckliga scheman. Ett objekt som representerar en person och ett objekt som representerar en bil kan till exempel placeras i *samma behållare*. Som standard indexeras alla objekt som du lägger till i en behållare automatiskt utan att det krävs explicit index- eller schemahantering. Du kan anpassa indexeringsbeteendet genom att konfigurera [indexeringsprincipen](index-overview.md) på en behållare. 

Du kan ställa in [Tid till Live (TTL)](time-to-live.md) för valda objekt i en Azure Cosmos-behållare eller för hela behållaren att smidigt rensa dessa objekt från systemet. Azure Cosmos DB tar automatiskt bort objekten när de upphör att gälla. Det garanterar också att en fråga som utförs på behållaren inte returnerar de utgångna artiklarna inom en fast bunden. Mer information finns i [Konfigurera TTL på behållaren](how-to-time-to-live.md).

Du kan använda [ändringsflödet](change-feed.md) för att prenumerera på operationsloggen som hanteras för varje logisk partition i behållaren. Ändringsfeeden innehåller loggen för alla uppdateringar som utförs på behållaren, tillsammans med före- och efterbilderna av objekten. Mer information finns i [Skapa reaktiva program med hjälp av ändringsflöde](serverless-computing-database.md). Du kan också konfigurera kvarhållningstiden för ändringsfeeden med hjälp av ändringsfeedprincipen på behållaren. 

Du kan registrera [lagrade procedurer, utlösare, användardefinierade funktioner (UDFs)](stored-procedures-triggers-udfs.md)och [sammanfoga procedurer](how-to-manage-conflicts.md) för din Azure Cosmos-behållare. 

Du kan ange en [unik nyckelbegränsning](unique-keys.md) på din Azure Cosmos-behållare. Genom att skapa en unik nyckelprincip säkerställer du att ett eller flera värden per logisk partitionsnyckel är unika. Om du skapar en behållare med hjälp av en unik nyckelprincip kan inga nya eller uppdaterade objekt med värden som duplicerar de värden som anges av det unika nyckelvillkoret skapas. Mer information finns i [Unika nyckelbegränsningar](unique-keys.md).

En Azure Cosmos-behållare är specialiserad på API-specifika entiteter som visas i följande tabell:

| Azure Cosmos-entitet | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-behållare | Container | Tabell | Samling | Graph | Tabell |

### <a name="properties-of-an-azure-cosmos-container"></a>Egenskaper för en Azure Cosmos-behållare

En Azure Cosmos-behållare har en uppsättning systemdefinierade egenskaper. Beroende på vilket API du använder kanske vissa egenskaper inte visas direkt. I följande tabell beskrivs listan över systemdefinierade egenskaper:

| Systemdefinierad egenskap | Systemgenererad eller användarkonfigurerbar | Syfte | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Systemgenererad | Unik identifierare för behållare | Ja | Inga | Inga | Inga | Inga |
|\_Etag | Systemgenererad | Entitetstagg som används för optimistisk samtidighetskontroll | Ja | Inga | Inga | Inga | Inga |
|\_ts | Systemgenererad | Senast uppdaterad tidsstämpel för behållaren | Ja | Inga | Inga | Inga | Inga |
|\_Själv | Systemgenererad | Adresserbar URI för behållaren | Ja | Inga | Inga | Inga | Inga |
|id | Användarkonfigurerbar | Användardefinierat unikt namn på behållaren | Ja | Ja | Ja | Ja | Ja |
|indexeringPolicy | Användarkonfigurerbar | Ger möjlighet att ändra indexsökväg, indextyp och indexläge | Ja | Inga | Inga | Inga | Ja |
|TimeToLive | Användarkonfigurerbar | Ger möjlighet att ta bort objekt automatiskt från en behållare efter en viss tidsperiod. Mer information finns i [Tid att leva](time-to-live.md). | Ja | Inga | Inga | Inga | Ja |
|ändraFeedPolicy | Användarkonfigurerbar | Används för att läsa ändringar som gjorts i objekt i en behållare. Mer information finns i [Ändra flöde](change-feed.md). | Ja | Inga | Inga | Inga | Ja |
|uniqueKeyPolicy | Användarkonfigurerbar | Används för att säkerställa det unika med ett eller flera värden i en logisk partition. Mer information finns i [Unika nyckelbegränsningar](unique-keys.md). | Ja | Inga | Inga | Inga | Ja |

### <a name="operations-on-an-azure-cosmos-container"></a>Åtgärder på en Azure Cosmos-behållare

En Azure Cosmos-behållare stöder följande åtgärder när du använder någon av Azure Cosmos API:er:

| Åtgärd | Azure CLI | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- |
| Räkna upp behållare i en databas | Ja | Ja | Ja | Ja | Ej tillämpligt | Ej tillämpligt |
| Läsa en behållare | Ja | Ja | Ja | Ja | Ej tillämpligt | Ej tillämpligt |
| Skapa en ny behållare | Ja | Ja | Ja | Ja | Ej tillämpligt | Ej tillämpligt |
| Uppdatera en behållare | Ja | Ja | Ja | Ja | Ej tillämpligt | Ej tillämpligt |
| Ta bort en container | Ja | Ja | Ja | Ja | Ej tillämpligt | Ej tillämpligt |

## <a name="azure-cosmos-items"></a>Azure Cosmos-objekt

Beroende på vilket API du använder kan ett Azure Cosmos-objekt representera antingen ett dokument i en samling, en rad i en tabell eller en nod eller kant i ett diagram. I följande tabell visas mappningen av API-specifika entiteter till ett Azure Cosmos-objekt:

| Entiteten Kosmos | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-objekt | Dokument | Rad | Dokument | Nod eller kant | Objekt |

### <a name="properties-of-an-item"></a>Egenskaper för ett objekt

Varje Azure Cosmos-objekt har följande systemdefinierade egenskaper. Beroende på vilket API du använder kanske vissa av dem inte är direkt exponerade.

| Systemdefinierad egenskap | Systemgenererad eller användarkonfigurerbar| Syfte | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Systemgenererad | Unik identifierare för objektet | Ja | Inga | Inga | Inga | Inga |
|\_Etag | Systemgenererad | Entitetstagg som används för optimistisk samtidighetskontroll | Ja | Inga | Inga | Inga | Inga |
|\_ts | Systemgenererad | Tidsstämpel för den senaste uppdateringen av objektet | Ja | Inga | Inga | Inga | Inga |
|\_Själv | Systemgenererad | Adresserbar URI för objektet | Ja | Inga | Inga | Inga | Inga |
|id | Antingen | Användardefinierat unikt namn i en logisk partition. | Ja | Ja | Ja | Ja | Ja |
|Egenskaper för godtyckliga användardefinierade | Användardefinierade | Användardefinierade egenskaper representerade i API-native representation (inklusive JSON, BSON och CQL) | Ja | Ja | Ja | Ja | Ja |

> [!NOTE]
> Egenskapens `id` unika egenskaper tillämpas bara inom varje logisk partition. Flera dokument kan `id` ha samma egenskap med olika partitionsnyckelvärden.

### <a name="operations-on-items"></a>Operationer på artiklar

Azure Cosmos-objekt stöder följande åtgärder. Du kan använda någon av Azure Cosmos API:er för att utföra åtgärderna.

| Åtgärd | Azure CLI | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- |
| Infoga, ersätta, ta bort, upsert, läsa | Inga | Ja | Ja | Ja | Ja | Ja |

## <a name="next-steps"></a>Nästa steg

Läs mer om följande uppgifter och begrepp:

* [Etablera dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md)
* [Etablera dataflöde i en Azure Cosmos-container](how-to-provision-container-throughput.md)
* [Arbeta med logiska partitioner](partition-data.md)
* [Konfigurera TTL på en Azure Cosmos-behållare](how-to-time-to-live.md)
* [Skapa reaktiva program med hjälp av ändringsflödet](change-feed.md)
* [Konfigurera en unik nyckelbegränsning för din Azure Cosmos-behållare](unique-keys.md)
