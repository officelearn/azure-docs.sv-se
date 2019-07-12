---
title: Azure Cosmos DB-servicekvoter
description: Azure Cosmos DB-tjänsten kvoter och standardgränser på olika resurstyper.
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 74df0038676e8459028084890da569ed3b75a682
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797287"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB-servicekvoter

Den här artikeln innehåller en översikt över standardkvoter som erbjuds för andra resurser i Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Lagring och dataflöde

När du har skapat ett Azure Cosmos-konto i din prenumeration kan du hantera data i ditt konto genom att [skapar databaser, behållare och objekt](databases-containers-items.md). Du kan etablera dataflöde på en behållare på servernivå eller databasnivå i [programbegäran (RU/s eller ru: er)](request-units.md). I följande tabell visas begränsningarna för lagring och dataflöde per behållare/databas.

| Resource | Standardgräns |
| --- | --- |
| Högsta antalet enheter för programbegäran per behållare ([dedikerat dataflöde etablerat läge](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 som standard. Du kan öka det [arkivera en Azure-supportärende](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) eller kontakta oss via [be Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| Maximal ru: er per databas ([delade etablerade genomflödesläge](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 som standard. Du kan öka det [arkivera en Azure-supportärende](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) eller kontakta oss via [be Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| Maximal Begäransenheter per partitionsnyckel (logisk) | 10 000 |
| Högsta lagring över alla objekt per partitionsnyckel (logisk)| 10 GB |
| Maximalt antal distinkta (logisk) partitionsnycklar | Obegränsat |
| Maximalt lagringsutrymme per behållare | Obegränsat |
| Maximalt lagringsutrymme per databas | Obegränsat |

> [!NOTE]
> Metodtips att hantera arbetsbelastningar som ibland partitionsnycklar som behöver högre gränser för lagring eller dataflöde, se [designa för Partition snabbtangenter](synthetic-partition-keys.md)
>

En Cosmos-behållare (eller delade dataflöde databasen) måste ha ett minsta dataflöde på 400 ru: er. När behållaren växer, beror det minsta dataflödet som stöds även på följande faktorer:

* Det maximala dataflöde som någonsin har etablerats på behållaren. Tjänsten stöder sänka dataflödet för en behållare till 10% av det etablerade högsta. Till exempel om ditt dataflöde har ökat till 10000 ru: er, är sedan det lägsta möjliga dataflöden 1000 ru: er
* Det totala antalet behållare som du tidigare har skapat i en delad dataflöde-databas, mätt på 100 ru: er per behållare. Till exempel om du har skapat fem behållare i en delad dataflöde databas kan måste sedan dataflödet vara minst 500 ru: er

Den aktuella och lägsta genomströmningen i en behållare eller en databas kan hämtas från Azure portal eller SDK: erna. Mer information finns i [etablera dataflöde på behållare och databaser](set-throughput.md). Sammanfattningsvis är här de minsta etablerade RU-gränserna. 

| Resource | Standardgräns |
| --- | --- |
| Minsta ru: er per behållare ([dedikerat dataflöde etablerat läge](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minsta ru: er per databas ([delade etablerade genomflödesläge](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minsta ru: er per behållare i en delad dataflöde-databas | 100 |

Cosmos DB stöder elastisk skalning av dataflöde (ru) per behållare eller databasen via SDK: er eller portalen. Varje behållare kan skala direkt och synkront inom ett intervall för skala med 10 till 100 gånger mellan lägsta och högsta värden. Om det begärda genomströmning värdet ligger utanför intervallet, utförs skalning asynkront. Asynkrona skalning kan det ta några minuter till timmar att slutföra beroende på det begärda dataflöde och lagringsstorlek för data i behållaren.  

## <a name="control-plane-operations"></a>Kontrollplanåtgärder

Du kan [etablera och hantera ditt Azure Cosmos-konto](how-to-manage-database-account.md) med hjälp av Azure-portalen, Azure PowerShell, Azure CLI och Azure Resource Manager-mallar. I följande tabell visas gränserna per prenumeration, konto och antalet åtgärder.

| Resource | Standardgräns |
| --- | --- |
| Maximal databaskonton per prenumeration | 50 som standard. Du kan öka det [arkivera en Azure-supportärende](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) eller kontakta oss via [be Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Maximalt antal regionala redundanstestningar | 1 per timme som standard. Du kan öka det [arkivera en Azure-supportärende](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) eller kontakta oss via [be Cosmos DB](mailto:askcosmosdb@microsoft.com)|

> [!NOTE]
> Regionala redundanstestningar gäller endast för en enda region skrivningar konton. Flera regioner write-konton kräver inte eller har några begränsningar för hur du ändrar skrivregionen.

Cosmos DB tar automatiskt säkerhetskopior av dina data med jämna mellanrum. Mer information om kvarhållning av säkerhetskopior intervall och windows finns [onlinesäkerhetskopiering och på begäran data Återställ i Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-container-limits"></a>Gränserna per behållare

Beroende på vilken API som du använder en Azure Cosmos-behållare representerar en samling-, en tabell, eller graph. Behållare har stöd för konfigurationer för [unika nyckelvillkor](unique-keys.md), [lagrade procedurer, utlösare och UDF: er](stored-procedures-triggers-udfs.md), och [indexeringspolicy](how-to-manage-indexing-policy.md). I följande tabell visas begränsningarna som är specifika för konfigurationer i en behållare. 

| Resource | Standardgräns |
| --- | --- |
| Maximal längd på databas-eller | 255 |
| Maximal lagrade procedurer per behållare | 100 <sup>*</sup>|
| Maximal UDF: er per behållare | 25 <sup>*</sup>|
| Maximalt antal sökvägar i indexeringsprincip| 100 <sup>*</sup>|
| Maximalt antal unika nycklar per behållare|10 <sup>*</sup>|
| Maximalt antal sökvägar per unika nyckelvillkor|16 <sup>*</sup>|

<sup>*</sup> Du kan öka någon av gränserna per behållare genom att kontakta Azure-supporten eller kontakta oss via [be Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="per-item-limits"></a>Gränserna per objekt

Beroende på vilken API som du använder representerar en Azure Cosmos-objektet antingen ett dokument i en samling, en rad i en tabell, eller en nod eller kanttabell i ett diagram. I följande tabell visas gränserna per objekt i Cosmos DB. 

| Resource | Standardgräns |
| --- | --- |
| Maximal storlek på ett objekt | 2 MB (UTF-8 längden på JSON-representation) |
| Maximal längd på partitionsnyckelvärde | 2 048 byte |
| Maximal längd på id-värde | 1 024 byte |
| Maximalt antal egenskaper per artikel | Ingen gräns |
| Maximal kapslingsdjup | Ingen gräns |
| Maximal längd på egenskapsnamn | Ingen gräns |
| Maximal längd på värdet för egenskapen | Ingen gräns |
| Maximal längd på strängvärde för egenskapen | Ingen gräns |
| Maximal längd på numeriska egenskapsvärdet | IEEE754 dubbel precision 64-bitars |

Det finns inga begränsningar för objekt-nyttolaster som antalet egenskaper och kapsling djup, förutom längd på partitionsnyckel och ID-värden och totala storlek begränsning av 2 MB. Du kan behöva konfigurera indexeringsprincip för behållare med stora och komplexa objekt strukturer du minskar användningen av RU. Se [modellering av objekt i Cosmos DB](how-to-model-partition-example.md) för ett exempel på verkliga och mönster för att hantera stora objekt.

## <a name="per-request-limits"></a>Gränserna per begäran

Cosmos DB stöder [CRUD-och fråga](https://docs.microsoft.com/rest/api/cosmos-db/) mot resurser som behållare, objekt och databaser.  

| Resource | Standardgräns |
| --- | --- |
| Högsta körningstiden för en enda åtgärd (till exempel körning av en lagrad procedur eller en enskild fråga sidan Hämta)| 5 SEK. |
| Tillåtna storlek (lagrad procedur, CRUD)| 2 MB |
| Maximal svarsstorlek (till exempel sidnumrerade fråga) | 4 MB |

När en åtgärd som frågan når gränsen för körning timeout eller svaret storlek, returneras en sida av resultaten och ett fortsättningstoken att klienten kan fortsätta körningen. Det finns ingen gräns på en enskild fråga kan köras sidor/continuations varaktigheten.

Cosmos DB använder HMAC för auktorisering. Du kan använda antingen en huvudnyckel eller en [resurstokens](secure-access-to-data.md) för detaljerad åtkomstkontroll till resurser som behållare, partitionera nycklar eller objekt. I följande tabell visas begränsningar för auktoriseringstoken i Cosmos DB.

| Resource | Standardgräns |
| --- | --- |
| Den längsta master token förfallotiden | 15 min  |
| Minsta resurstid för förfallodatum för token | 10 min  |
| Maximala förfallotid för token | 24 timmar som standard. Du kan öka det [arkivera en Azure-supportärende](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) eller kontakta oss via [be Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Maximal klockan skeva för token auktorisering| 15 min |

Cosmos DB stöder körning av utlösare under skrivåtgärder. Tjänsten stöder högst en före utlösare och en efter utlösare per skrivning. 

## <a name="sql-query-limits"></a>Gränser för SQL-fråga

Cosmos DB har stöd för frågor till objekt som använder [SQL](how-to-sql-query.md). I följande tabell beskrivs begränsningar i fråga instruktioner, till exempel när det gäller antal satser eller frågelängd.

| Resource | Standardgräns |
| --- | --- |
| Maximal längd på SQL-fråga| 256 KB <sup>*</sup>|
| Maximal kopplingar per fråga| 5 <sup>*</sup>|
| Maximal ANDs per fråga| 2000 <sup>*</sup>|
| Maximal ORs per fråga| 2000 <sup>*</sup>|
| Maximal UDF: er per fråga| 10 <sup>*</sup>|
| Högst argument per i uttrycket| 6000 <sup>*</sup>|
| Maximalt antal poäng per polygon| 4096 <sup>*</sup>|

<sup>*</sup> Du kan öka någon av dessa gränser för SQL-fråga genom att kontakta Azure-supporten eller kontakta oss via [be Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="mongodb-api-specific-limits"></a>MongoDB API-specifika begränsningar

Cosmos DB stöder MongoDB-protokollet för appar som skrivits mot MongoDB. Du kan hitta kommandona som stöds och protokoll versioner på [stöd för MongoDB-funktioner och syntax](mongodb-feature-support.md).

I följande tabell visas begränsningarna som är specifika för funktioner som stöds med MongoDB. Det gäller även andra begränsningar för tjänster som nämns för SQL (kärna) API MongoDB-API: et.

| Resource | Standardgräns |
| --- | --- |
| Maximal minnesstorlek för MongoDB-fråga | 40 MB |
| Maximal körningstid för MongoDB-åtgärder| 30 sekunder |

## <a name="try-cosmos-db-free-limits"></a>Testa Cosmos DB kostnadsfritt gränser

I följande tabell visas begränsningarna för den [testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utvärderingsversion.

| Resource | Standardgräns |
| --- | --- |
| Utvärderingsperioden | 30 dagar (kan vara förnyas valfritt antal gånger) |
| Maximal behållare per prenumeration (SQL, Gremlin, tabell-API) | 1 |
| Maximal behållare per prenumeration (MongoDB-API) | 3 |
| Maximalt dataflöde per behållare | 5000 |
| Maximalt dataflöde per delade dataflöden databas | 20000 |
| Det största totala lagringsutrymmet per konto | 10 GB |

Testa Cosmos DB stöder global distribution i endast regionerna centrala USA, Norra Europa och Asien, sydöstra. Azure supportärenden kan skapas för testa Azure Cosmos DB-konton. Dock tillhandahåller support till prenumeranter med befintliga supportavtal.

## <a name="next-steps"></a>Nästa steg

Läs mer om Cosmos DB viktiga begrepp [globalt](distribute-data-globally.md) och [partitionering](partitioning-overview.md) och [etablerat dataflöde](request-units.md).

Kom igång med Azure Cosmos DB med någon av våra snabbstarter:

* [Komma igång med SQL-API för Azure Cosmos DB](create-sql-api-dotnet.md)
* [Kom igång med Azure Cosmos DB-API:n för Azure MongoDB](create-mongodb-nodejs.md)
* [Kom igång med Cassandra-API för Azure Cosmos DB](create-cassandra-dotnet.md)
* [Komma igång med Gremlin API för Azure Cosmos DB](create-graph-dotnet.md)
* [Komma igång med Tabell-API för Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/)
