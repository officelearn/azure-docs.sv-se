---
title: Azure Cosmos DB-tjänstkvoter
description: Azure Cosmos DB-tjänstkvoter och standardgränser för olika resurstyper.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: b24d7db679bb9cb9dacd5e1db8e6410b883548cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415696"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB-tjänstkvoter

Den här artikeln innehåller en översikt över standardkvoterna som erbjuds till olika resurser i Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Lagring och dataflöde

När du har skapat ett Azure Cosmos-konto under din prenumeration kan du hantera data i ditt konto genom [att skapa databaser, behållare och objekt](databases-containers-items.md). Du kan etablera dataflöde på behållarnivå eller databasnivå när det gäller [begärandenheter (RU/s eller RU: er)](request-units.md). I följande tabell visas gränserna för lagring och dataflöde per behållare/databas.

| Resurs | Standardgräns |
| --- | --- |
| Maximal ru:er per behållare[(etablerat läge för dedikerat dataflöde)](databases-containers-items.md#azure-cosmos-containers) | 1 000 000 som standard. Du kan öka den genom [att skicka in en Azure-supportbiljett](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Maximal ru:er per databas[(etablerat läge för delat dataflöde)](databases-containers-items.md#azure-cosmos-containers) | 1 000 000 som standard. Du kan öka den genom [att skicka in en Azure-supportbiljett](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Maximal RUs per (logisk) partitionsnyckel | 10 000 |
| Maximal lagring för alla objekt per (logisk) partitionsnyckel| 20 GB |
| Maximalt antal distinkta (logiska) partitionsnycklar | Obegränsat |
| Maximal lagring per behållare | Obegränsat |
| Maximal lagring per databas | Obegränsat |
| Maximal anknytningsstorlek per konto (funktionen bifogad fil skrivs av) | 2 GB |
| Minsta ru: er som krävs per 1 GB | 10 RU/s |

> [!NOTE]
> Mer information om metodtips för hantering av arbetsbelastningar som har partitionsnycklar som kräver högre gränser för lagring eller dataflöde finns i [Skapa en syntetisk partitionsnyckel](synthetic-partition-keys.md).
>

En Cosmos-behållare (eller delad dataflödesdatabas) måste ha ett minsta dataflöde på 400 ru: er. När behållaren växer beror även det minsta dataflödet som stöds på följande faktorer:

* Det minsta dataflöde som du kan ange på en behållare beror på det maximala dataflödet som någonsin etablerats på behållaren. Om ditt dataflöde till exempel har ökats till 1 0000 ru: er, skulle det lägsta möjliga etablerade dataflödet vara 1000 ru:er
* Det minsta dataflödet i en databas med delat dataflöde beror också på det totala antalet behållare som du någonsin har skapat i en delad dataflödesdatabas, mätt med 100 ru:er per behållare. Om du till exempel har skapat fem behållare i en databas med delat dataflöde måste dataflödet vara minst 500 ru:er

Det aktuella och minsta dataflödet för en behållare eller en databas kan hämtas från Azure-portalen eller SDK:erna. Mer information finns i [Etablera dataflöde på behållare och databaser](set-throughput.md). 

> [!NOTE]
> I vissa fall kan du sänka dataflödet till mindre än 10 %. Använd API:et för att få exakt minsta RUs per behållare.
>

Sammanfattningsvis är här de minsta etablerade RU-gränsvärdena. 

| Resurs | Standardgräns |
| --- | --- |
| Minsta ru:er per behållare[(etablerat läge för dedikerat dataflöde)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| Minsta ru:er per databas[(etablerat läge för delat dataflöde)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| Minsta RUs per behållare i en delad dataflödesdatabas | 100 |

Cosmos DB stöder elastisk skalning av dataflöde (RU: er) per behållare eller databas via SDK eller portalen. Varje behållare kan skala synkront och omedelbart inom ett skalintervall på 10 till 100 gånger, mellan lägsta och högsta värden. Om det begärda dataflödesvärdet ligger utanför intervallet utförs skalning asynkront. Asynkron skalning kan ta minuter till timmar att slutföra beroende på det begärda dataflödet och datalagringsstorleken i behållaren.  

## <a name="control-plane-operations"></a>Styra planets verksamhet

Du kan [etablera och hantera ditt Azure Cosmos-konto](how-to-manage-database-account.md) med azure-portalen, Azure PowerShell, Azure CLI och Azure Resource Manager-mallarna. I följande tabell visas gränserna per prenumeration, konto och antal åtgärder.

| Resurs | Standardgräns |
| --- | --- |
| Maximalt databaskonton per prenumeration | 50 som standard. Du kan öka den genom [att skicka in en Azure-supportbiljett](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Maximalt antal regionala redundans | 1/timme som standard. Du kan öka den genom [att skicka in en Azure-supportbiljett](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|

> [!NOTE]
> Regionala redundanser gäller endast för konton för en regionskrivningar. Skrivkonton i flera regioner kräver inte eller har några begränsningar för att ändra skrivregionen.

Cosmos DB tar automatiskt säkerhetskopior av dina data med jämna mellanrum. Mer information om lagringsintervall för säkerhetskopiering och fönster finns [i Online-säkerhetskopiering och dataåterställning på begäran i Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Gränser per konto

| Resurs | Standardgräns |
| --- | --- |
| Maximalt antal databaser | Obegränsat |
| Maximalt antal behållare per databas med delat dataflöde |25 |
| Maximalt antal behållare per databas eller konto med dedikerat dataflöde  |obegränsat |
| Maximalt antal regioner | Ingen gräns (alla Azure-regioner) |

## <a name="per-container-limits"></a>Gränser för per behållare

Beroende på vilket API du använder kan en Azure Cosmos-behållare representera antingen en samling, en tabell eller ett diagram. Behållare stöder konfigurationer för [unika nyckelbegränsningar,](unique-keys.md) [lagrade procedurer, utlösare och UDF-filer](stored-procedures-triggers-udfs.md)och [indexeringsprincipen](how-to-manage-indexing-policy.md). I följande tabell visas de gränser som är specifika för konfigurationer i en behållare. 

| Resurs | Standardgräns |
| --- | --- |
| Maximal längd på databas- eller behållarnamn | 255 |
| Maximala lagrade procedurer per behållare | 100<sup>*</sup>|
| Högsta UDF per behållare | 25<sup>*</sup>|
| Maximalt antal sökvägar i indexeringsprincipen| 100<sup>*</sup>|
| Maximalt antal unika nycklar per behållare|10<sup>*</sup>|
| Maximalt antal sökvägar per unikt nyckelvillkor|16<sup>*</sup>|

<sup>*</sup>Du kan öka någon av dessa per-behållare gränser genom att kontakta Azure Support.

## <a name="per-item-limits"></a>Gränser per artikel

Beroende på vilket API du använder kan ett Azure Cosmos-objekt representera antingen ett dokument i en samling, en rad i en tabell eller en nod eller kant i ett diagram. I följande tabell visas gränserna per objekt i Cosmos DB. 

| Resurs | Standardgräns |
| --- | --- |
| Maximal storlek på en artikel | 2 MB (UTF-8 längd JSON representation) |
| Maximalt värde för partitionsnyckel | 2048 byte |
| Högsta id-värde | 1023 byte |
| Maximalt antal egenskaper per artikel | Ingen praktisk gräns |
| Maximalt kapslingsdjup | Ingen praktisk gräns |
| Maximal längd på egenskapsnamnet | Ingen praktisk gräns |
| Maximal längd på egenskapsvärdet | Ingen praktisk gräns |
| Maximal längd på egenskapsvärdet för sträng | Ingen praktisk gräns |
| Maximal längd för numeriskt egenskapsvärde | IEEE754 dubbel precision 64-bitars |

Det finns inga begränsningar för artikeln nyttolaster som antal egenskaper och kapsling djup, förutom längdbegränsningar för partitionsnyckel och ID-värden, och den totala storleken begränsning av 2 MB. Du kan behöva konfigurera indexeringsprincipen för behållare med stora eller komplexa artikelstrukturer för att minska RU-förbrukningen. Se [Modellering av objekt i Cosmos DB](how-to-model-partition-example.md) för ett verkligt exempel och mönster för att hantera stora objekt.

## <a name="per-request-limits"></a>Gränser per begäran

Azure Cosmos DB stöder [CRUD- och frågeåtgärder](https://docs.microsoft.com/rest/api/cosmos-db/) mot resurser som behållare, objekt och databaser. Den stöder också [transaktionsbatchbegäranden](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) mot flera objekt med samma partitionsnyckel i en behållare.

| Resurs | Standardgräns |
| --- | --- |
| Maximal körningstid för en enskild åtgärd (som en körning av lagrad procedur eller en enda frågesidas hämtning)| 5 sek |
| Maximal begäran storlek (till exempel lagrad procedur, CRUD)| 2 MB |
| Maximal svarsstorlek (till exempel sidnumrerad fråga) | 4 MB |
| Maximalt antal operationer i en transaktionsbatch | 100 |

När en åtgärd som fråga når körningen timeout eller svar storleksgräns, returnerar den en sida med resultat och en fortsättningstoken till klienten för att återuppta körningen. Det finns ingen praktisk gräns för hur länge en enskild fråga kan köras över sidor/fortsättningar.

Cosmos DB använder HMAC för auktorisering. Du kan använda antingen en huvudnyckel eller en [resurstoken](secure-access-to-data.md) för detaljerad åtkomstkontroll till resurser som behållare, partitionsnycklar eller objekt. I följande tabell visas begränsningar för auktoriseringstoken i Cosmos DB.

| Resurs | Standardgräns |
| --- | --- |
| Maximal utgångstid för huvudtoken | 15 min  |
| Minsta resurstokens utgångstid | 10 min  |
| Maximal resurstokens utgångstid | 24 h som standard. Du kan öka den genom [att skicka in en Azure-supportbiljett](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Maximal klocksnedställning för tokenauktorisering| 15 min |

Cosmos DB stöder körning av utlösare under skrivningar. Tjänsten stöder högst en förutlösare och en post-trigger per skrivåtgärd. 

## <a name="autopilot-mode-limits"></a>Gränser för autopilotläge

Se [autopilotartikeln](provision-throughput-autopilot.md#autopilot-limits) för dataflödes- och lagringsgränserna i autopilotläge.

## <a name="sql-query-limits"></a>SQL-frågegränser

Cosmos DB stöder frågor om objekt med [SQL](how-to-sql-query.md). I följande tabell beskrivs begränsningar i frågesatser, till exempel när det gäller antal satser eller frågelängd.

| Resurs | Standardgräns |
| --- | --- |
| Maximal längd på SQL-frågan| 256 kB |
| Högsta JOIN per fråga| 5<sup>*</sup>|
| Högsta udden per fråga| 10<sup>*</sup>|
| Högsta poäng per polygon| 4096 |
| Maximalt inkluderade sökvägar per behållare| 500 |
| Maximalt undantagna banor per behållare| 500 |
| Maximala egenskaper i ett sammansatt index| 8 |

<sup>*</sup>Du kan öka dessa SQL-frågegränser genom att kontakta Azure Support.

## <a name="mongodb-api-specific-limits"></a>MongoDB API-specifika gränser

Cosmos DB stöder MongoDB-kabelprotokollet för program skrivna mot MongoDB. Du hittar kommandona och protokollversionerna som stöds på [MongoDB-funktioner och syntax som stöds](mongodb-feature-support.md).

I följande tabell visas de gränser som är specifika för MongoDB-funktionsstöd. Andra tjänstgränser som nämns för SQL-API:et (core) gäller även för MongoDB-API:et.

| Resurs | Standardgräns |
| --- | --- |
| Maximal MongoDB-frågeminnesstorlek (Den här begränsningen är endast avsedd för 3.2-serverversion) | 40 MB |
| Maximal körningstid för MongoDB-åtgärder| 30s |
| Tidsgränsen för inaktiv anslutning för anslutningsstängning på serversidan* | 30 minuter |

\*Vi rekommenderar att klientprogram anger tidsgränsen för inaktiv anslutning i drivrutinsinställningarna till 2-3 minuter eftersom [standardtidsgränsen för Azure LoadBalancer är 4 minuter](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout).  Den här timeouten säkerställer att inaktiva anslutningar inte stängs av en mellanliggande belastningsutjämnare mellan klientdatorn och Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Prova Cosmos DB Free gränser

I följande tabell visas gränserna för [prova Azure Cosmos DB för kostnadsfri utvärderingsversion.](https://azure.microsoft.com/try/cosmosdb/)

| Resurs | Standardgräns |
| --- | --- |
| Rättegångens varaktighet | 30 dagar (kan förnyas valfritt antal gånger) |
| Maximala behållare per prenumeration (SQL, Gremlin, Tabell-API) | 1 |
| Maximala behållare per prenumeration (MongoDB API) | 3 |
| Maximalt dataflöde per behållare | 5000 |
| Maximalt dataflöde per databas med delat dataflöde | 20000 |
| Maximal total lagring per konto | 10 GB |

Prova Cosmos DB stöder global distribution i endast regionerna Centrala USA, Norra Europa och Sydostasien. Azure-supportbiljetter kan inte skapas för Prova Azure Cosmos DB-konton. Stöd ges dock till prenumeranter med befintliga supportplaner.

## <a name="free-tier-account-limits"></a>Begränsningar för konto på den kostnadsfria nivån
I följande tabell visas gränserna för [Azure Cosmos DB-konton på den kostnadsfria nivån.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Resurs | Standardgräns |
| --- | --- |
| Antal kostnadsfria nivåkonton per Azure-prenumeration | 1 |
| Varaktighet för rabatt på free-tier | Kontots livstid. Måste anmäla sig när kontot skapas. |
| Maximal RU/s gratis | 400 RU/s |
| Maximal förvaring gratis | 5 GB |
| Maximalt antal databaser med delat dataflöde | 5 |
| Maximalt antal behållare i en databas med delat dataflöde | 25 <br>I konton på den kostnadsfria nivån är den lägsta RU/s för en databas med delat dataflöde med upp till 25 behållare 400 RU/s. |

  Utöver ovanstående gäller [per-konto-gränserna](#per-account-limits) även för konton på den kostnadsfria nivån.

## <a name="next-steps"></a>Nästa steg

Läs mer om Cosmos DB:s kärnkoncept [global distribution](distribute-data-globally.md) och [partitionering](partitioning-overview.md) och [etablerat dataflöde](request-units.md).

Kom igång med Azure Cosmos DB med någon av våra snabbstarter:

* [Komma igång med SQL-API för Azure Cosmos DB](create-sql-api-dotnet.md)
* [Kom igång med Azure Cosmos DB-API:n för Azure MongoDB](create-mongodb-nodejs.md)
* [Kom igång med Cassandra-API för Azure Cosmos DB](create-cassandra-dotnet.md)
* [Komma igång med Gremlin API för Azure Cosmos DB](create-graph-dotnet.md)
* [Komma igång med Tabell-API för Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/)
