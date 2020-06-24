---
title: Arbeta med databaser, behållare och objekt i Azure Cosmos DB
description: Den här artikeln beskriver hur du skapar och använder databaser, behållare och objekt i Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.reviewer: sngun
ms.openlocfilehash: 257d7a2e374867f6ff14aeaa633d95521b7ca39e
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2020
ms.locfileid: "85114766"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Arbeta med databaser, behållare och objekt i Azure Cosmos DB

När du har skapat ett [Azure Cosmos DB konto](account-overview.md) i din Azure-prenumeration kan du hantera data i ditt konto genom att skapa databaser, behållare och objekt. I den här artikeln beskrivs var och en av dessa entiteter. 

Följande bild visar hierarkin för olika entiteter i ett Azure Cosmos DB konto:

:::image type="content" source="./media/databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos Account-entiteter" border="false":::

## <a name="azure-cosmos-databases"></a>Azure Cosmos-databaser

Du kan skapa en eller flera Azure Cosmos-databaser under ditt konto. En databas motsvarar ett namn område. En databas är enhets hantering för en uppsättning av Azure Cosmos-behållare. Följande tabell visar hur en Azure Cosmos-databas mappas till olika API-/regionsspecifika entiteter:

| Azure Cosmos-entitet | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-databas | Databas | Keyspace | Databas | Databas | NA |

> [!NOTE]
> När du skapar din första tabell med Tabell-API-konton skapas en standard databas automatiskt i ditt Azure Cosmos-konto.

### <a name="operations-on-an-azure-cosmos-database"></a>Åtgärder på en Azure Cosmos-databas

Du kan interagera med en Azure Cosmos-databas med Azure Cosmos-API: er enligt beskrivningen i följande tabell:

| Åtgärd | Azure CLI | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- |
|Räkna upp alla databaser| Ja | Ja | Ja (databasen är mappad till ett tecken utrymme) | Yes | NA | NA |
|Läs databas| Ja | Ja | Ja (databasen är mappad till ett tecken utrymme) | Yes | NA | NA |
|Skapa ny databas| Ja | Ja | Ja (databasen är mappad till ett tecken utrymme) | Yes | NA | NA |
|Uppdatera databas| Ja | Ja | Ja (databasen är mappad till ett tecken utrymme) | Yes | NA | NA |


## <a name="azure-cosmos-containers"></a>Azure Cosmos-containrar

En Azure Cosmos-behållare är enhets skalbarhet både för allokerat data flöde och lagring. En behållare är vågrätt partitionerad och replikeras sedan över flera regioner. De objekt som du lägger till i behållaren och det data flöde som du etablerar på den distribueras automatiskt över en uppsättning logiska partitioner baserat på partitionsnyckel. Mer information om partitionering och partitionerings nycklar finns i [partitionera data](partition-data.md). 

När du skapar en Azure Cosmos-behållare konfigurerar du genomflödet i något av följande lägen:

* **Dedikerat allokerat data flödes läge**: det data flöde som har allokerats på en behållare är exklusivt reserverat för den behållaren och den backas upp av service avtal. Mer information finns i [så här etablerar du data flöde i en Azure Cosmos-behållare](how-to-provision-container-throughput.md).

* **Delat etablerat data flödes läge**: de här behållarna delar det etablerade data flödet med andra behållare i samma databas (exklusive behållare som har kon figurer ATS med dedikerat etablerat data flöde). Med andra ord delas det etablerade data flödet på databasen över alla "delade data flöde"-behållare. Mer information finns i [så här etablerar du data flöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).

> [!NOTE]
> Du kan bara konfigurera delade och dedikerade data flöde när du skapar databasen och behållaren. Om du vill byta från dedikerat dataflödesläge till delat dataflödesläge (och vice versa) efter det att containern har skapats måste du skapa en ny container och migrera data till den nya containern. Du kan migrera data med hjälp av funktionen Azure Cosmos DB ändra feed.

En Azure Cosmos-behållare kan skala elastiskt, oavsett om du skapar behållare med hjälp av dedikerade eller delade allokerade data flödes lägen.

En Azure Cosmos-behållare är en schema oberoende behållare med objekt. Objekt i en behållare kan ha godtyckliga scheman. Till exempel kan ett objekt som representerar en person och ett objekt som representerar en bil placeras i *samma behållare*. Som standard indexeras alla objekt som du lägger till i en behållare automatiskt utan att det krävs explicita index eller schema hantering. Du kan anpassa indexerings beteendet genom att konfigurera [indexerings principen](index-overview.md) på en behållare. 

Du kan ställa in [Time to Live (TTL)](time-to-live.md) på valda objekt i en Azure Cosmos-behållare eller för hela behållaren för att städa bort objekten från systemet. Azure Cosmos DB tar automatiskt bort objekten när de upphör att gälla. Det garanterar också att en fråga som utförs på behållaren inte returnerar de inaktuella objekten inom en fast bindning. Läs mer i [Konfigurera TTL på din behållare](how-to-time-to-live.md).

Du kan använda [ändra feed](change-feed.md) för att prenumerera på den Operations logg som hanteras för varje logisk partition i din behållare. Ändra feed innehåller loggen över alla uppdateringar som utförs på behållaren, tillsammans med objekten före och efter för objekten. Mer information finns i [bygga reaktiva program med hjälp av ändra feed](serverless-computing-database.md). Du kan också konfigurera Retentions tiden för ändrings flödet med hjälp av principen för att ändra feed i behållaren.

Du kan registrera [lagrade procedurer, utlösare, användardefinierade funktioner (UDF: er)](stored-procedures-triggers-udfs.md)och [sammanfognings procedurer](how-to-manage-conflicts.md) för din Azure Cosmos-behållare.

Du kan ange en [unik nyckel begränsning](unique-keys.md) i din Azure Cosmos-behållare. Genom att skapa en unik nyckel princip säkerställer du att ett eller flera värden är unika per logisk partitionsnyckel. Om du skapar en behållare med hjälp av en unik nyckel princip kan inga nya eller uppdaterade objekt med värden som duplicerar värdena som anges av den unika nyckel begränsningen skapas. Mer information finns i [unika nyckel begränsningar](unique-keys.md).

En Azure Cosmos-behållare är specialiserad i API-specifika entiteter som visas i följande tabell:

| Azure Cosmos-entitet | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-behållare | Container | Tabell | Samling | Graph | Tabell |

> [!NOTE]
> När du skapar behållare ser du till att du inte skapar två behållare med samma namn men med olika Skift läge. Det beror på att vissa delar av Azure-plattformen inte är Skift läges känsliga, och det kan leda till förvirring/kollisioner för telemetri och åtgärder på behållare med sådana namn.

### <a name="properties-of-an-azure-cosmos-container"></a>Egenskaper för en Azure Cosmos-behållare

En Azure Cosmos-behållare har en uppsättning systemdefinierade egenskaper. Beroende på vilket API du använder kanske vissa egenskaper inte är direkt exponerade. I följande tabell beskrivs en lista över systemdefinierade egenskaper:

| Systemdefinierad egenskap | Systemgenererad eller användare kan konfigureras | Syfte | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Systemgenererad | Unikt ID för behållare | Yes | Inga | Inga | Inga | Inga |
|\_etag | Systemgenererad | Entity-taggen används för optimistisk concurrency-kontroll | Yes | Inga | Inga | Inga | Inga |
|\_ts | Systemgenererad | Senast uppdaterad tidsstämpel för behållaren | Yes | Inga | Inga | Inga | Inga |
|\_ständiga | Systemgenererad | Adresser bara URI för behållaren | Yes | Inga | Inga | Inga | Inga |
|id | Kan konfigureras av användare | Användardefinierat unikt namn på behållaren | Ja | Ja | Ja | Ja | Ja |
|indexingPolicy | Kan konfigureras av användare | Ger möjlighet att ändra index Sök väg, index typ och index läge | Yes | Inga | Inga | Inga | Yes |
|TimeToLive | Kan konfigureras av användare | Ger möjlighet att ta bort objekt automatiskt från en behållare efter en angiven tids period. Mer information finns i [Time to Live](time-to-live.md). | Yes | Inga | Inga | Inga | Yes |
|changeFeedPolicy | Kan konfigureras av användare | Används för att läsa ändringar som gjorts i objekt i en behållare. Mer information finns i [ändra feed](change-feed.md). | Yes | Inga | Inga | Inga | Yes |
|uniqueKeyPolicy | Kan konfigureras av användare | Används för att säkerställa att ett eller flera värden är unika i en logisk partition. Mer information finns i [unika nyckel begränsningar](unique-keys.md). | Yes | Inga | Inga | Inga | Yes |

### <a name="operations-on-an-azure-cosmos-container"></a>Åtgärder på en Azure Cosmos-behållare

En Azure Cosmos-behållare stöder följande åtgärder när du använder någon av Azure Cosmos-API: erna:

| Åtgärd | Azure CLI | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- |
| Räkna upp behållare i en databas | Ja | Ja | Ja | Ja | NA | NA |
| Läsa en behållare | Ja | Ja | Ja | Ja | NA | NA |
| Skapa en ny behållare | Ja | Ja | Ja | Ja | NA | NA |
| Uppdatera en behållare | Ja | Ja | Ja | Ja | NA | NA |
| Ta bort en container | Ja | Ja | Ja | Ja | NA | NA |

## <a name="azure-cosmos-items"></a>Azure Cosmos-objekt

Beroende på vilket API du använder kan ett Azure Cosmos-objekt representera antingen ett dokument i en samling, en rad i en tabell eller en nod eller kant i ett diagram. I följande tabell visas mappningen av API-/regionsspecifika entiteter till ett Azure Cosmos-objekt:

| Cosmos-entitet | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-objekt | Dokument | Rad | Dokument | Nod eller kant | Objekt |

### <a name="properties-of-an-item"></a>Egenskaper för ett objekt

Varje Azure Cosmos-objekt har följande systemdefinierade egenskaper. Beroende på vilket API du använder kanske vissa av dem inte är direkt exponerade.

| Systemdefinierad egenskap | Systemgenererad eller användare kan konfigureras| Syfte | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Systemgenererad | Unikt ID för objektet | Yes | Inga | Inga | Inga | Inga |
|\_etag | Systemgenererad | Entity-taggen används för optimistisk concurrency-kontroll | Yes | Inga | Inga | Inga | Inga |
|\_ts | Systemgenererad | Tidsstämpel för den senaste uppdateringen av objektet | Yes | Inga | Inga | Inga | Inga |
|\_ständiga | Systemgenererad | Adresser bara URI för objektet | Yes | Inga | Inga | Inga | Inga |
|id | Antingen | Användardefinierat unikt namn i en logisk partition. | Yes | Ja | Ja | Ja | Ja |
|Godtyckliga användardefinierade egenskaper | Användardefinierade | Användardefinierade egenskaper som representeras i API – intern representation (inklusive JSON, BSON och CQL) | Yes | Ja | Ja | Ja | Ja |

> [!NOTE]
> `id`Egenskapens unikhet är endast tvingande inom varje logisk partition. Flera dokument kan ha samma `id` egenskap med olika nyckel värden.

### <a name="operations-on-items"></a>Åtgärder för objekt

Azure Cosmos-objekt har stöd för följande åtgärder. Du kan använda någon av Azure Cosmos-API: erna för att utföra åtgärderna.

| Åtgärd | Azure CLI | API för SQL | Cassandra-API | API för Azure Cosmos DB för MongoDB | Gremlin-API | Tabell-API |
| --- | --- | --- | --- | --- | --- | --- |
| Infoga, ersätta, ta bort, upsert, läsa | Inga | Yes | Ja | Ja | Ja | Ja |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om dessa uppgifter och koncept:

* [Etablera dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md)
* [Etablera dataflöde i en Azure Cosmos-container](how-to-provision-container-throughput.md)
* [Arbeta med logiska partitioner](partition-data.md)
* [Konfigurera TTL på en Azure Cosmos-behållare](how-to-time-to-live.md)
* [Bygga reaktiva program med hjälp av ändra feed](change-feed.md)
* [Konfigurera en unik nyckel begränsning på din Azure Cosmos-behållare](unique-keys.md)
