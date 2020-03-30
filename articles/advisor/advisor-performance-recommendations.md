---
title: Förbättra prestanda för Azure-program med Azure Advisor
description: Använd Advisor för att optimera prestanda för dina Azure-distributioner.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 405ec395feeb33b8511b9b915151b2ed9503c371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443063"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Förbättra prestanda för Azure-program med Azure Advisor

Azure Advisors prestandarekommendationer hjälper till att förbättra hastigheten och svarstiden för dina affärskritiska program. Du kan få prestandarekommendationer från Advisor på fliken **Prestanda** på advisor-instrumentpanelen.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Minska DNS-tiden för att leva på din Traffic Manager-profil för att växla över till felfria slutpunkter snabbare

[Med inställningarna för Tid till Live (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) i Traffic Manager-profilen kan du ange hur snabbt du ska växla slutpunkter om en viss slutpunkt slutar svara på frågor. Att minska TTL-värdena innebär att klienter dirigeras till fungerande slutpunkter snabbare.

Azure Advisor identifierar Traffic Manager-profiler med en längre TTL konfigurerad och rekommenderar att du konfigurerar TTL till antingen 20 sekunder eller 60 sekunder beroende på om profilen är konfigurerad för [Snabb redundans](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Förbättra databasens prestanda med SQL DB Advisor

Advisor ger dig en konsekvent, konsoliderad vy över rekommendationer för alla dina Azure-resurser. Den integreras med SQL Database Advisor för att ge dig rekommendationer för att förbättra prestanda för din SQL Azure-databas.SQL Database Advisor utvärderar prestanda för dina SQL Azure-databaser genom att analysera din användningshistorik. Den erbjuder sedan rekommendationer som är bäst lämpade för att köra databasens typiska arbetsbelastning.

> [!NOTE]
> För att få rekommendationer måste en databas ha ungefär en veckas användning, och inom den veckan måste det finnas en konsekvent aktivitet. SQL Database Advisor kan lättare optimera för konsekventa frågemönster än för slumpmässiga aktivitetssprängningar.

Mer information om SQL Database Advisor finns i [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>Förbättra apptjänstens prestanda och tillförlitlighet

Azure Advisor integrerar rekommendationer om bästa praxis för att förbättra din App Services-upplevelse och identifiera relevanta plattformsfunktioner. Exempel på App Services-rekommendationer är:
* Identifiering av instanser där minne eller CPU-resurser är uttömda av appkörningar med begränsningsalternativ.
* Identifiering av instanser där samlokalisering av resurser som webbappar och databaser kan förbättra prestanda och lägre kostnad.

Mer information om App Services-rekommendationer finns i [Metodtips för Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Använda hanterade diskar för att förhindra disk-I/O-begränsning

Advisor identifierar virtuella datorer som tillhör ett lagringskonto som når sitt skalbarhetsmål. Detta villkor gör dessa virtuella datorer mottagliga för I/O-begränsning. Advisor rekommenderar att de använder hanterade diskar för att förhindra prestandaförsämring.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Förbättra prestanda och tillförlitlighet för virtuella datordiskar med hjälp av Premium Storage

Advisor identifierar virtuella datorer med standarddiskar som har en hög mängd transaktioner på ditt lagringskonto och rekommenderar uppgradering till premiumdiskar. 

Azure Premium Storage ger diskstöd med hög prestanda med låg latens för virtuella datorer som kör I/O-intensiva arbetsbelastningar. Virtuella datordiskar som använder premiumlagringskonton lagrar data på SSD-enheter (Solid State Drives). För bästa prestanda för ditt program rekommenderar vi att du migrerade alla virtuella datordiskar som kräver hög IOPS till premiumlagring.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Ta bort datasnedställning i sql-datalagertabellen för att öka frågeprestanda

Datasnedställning kan orsaka onödiga dataförflyttningar eller resursflaskhalsar när du kör din arbetsbelastning. Advisor identifierar distributionsdata som är större än 15 % och rekommenderar att du distribuerar om dina data och går igenom valen av tabelldistributionsnyckel. Mer information om hur du identifierar och tar bort skeva finns i [felsöka skeva](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Skapa eller uppdatera inaktuell tabellstatistik i sql-informationslagrets tabell för att öka frågeprestanda

Advisor identifierar tabeller som inte har aktuell [tabellstatistik](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) och rekommenderar att du skapar eller uppdaterar tabellstatistik. SQL-informationslagerfråganoptimeraren använder uppdaterade statiska data som kan uppskatta kardinaliteten eller antalet rader i frågeresultatet som gör att frågeoptimeraren kan skapa en frågeplan av hög kvalitet för snabbaste prestanda.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Skala upp för att optimera cacheanvändningen på dina SQL Data Warehouse-tabeller för att öka frågeprestanda

Azure Advisor identifierar om ditt SQL Data Warehouse har hög cache använd procentsats och en låg träffprocent. Det här villkoret indikerar hög cachevräkning, vilket kan påverka prestanda för ditt SQL Data Warehouse. Advisor föreslår att du skalar upp ditt SQL Data Warehouse för att säkerställa att du allokerar tillräckligt med cachekapacitet för din arbetsbelastning.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Konvertera SQL Data Warehouse-tabeller till replikerade tabeller för att öka frågeprestanda

Advisor identifierar tabeller som inte är replikerade tabeller men som skulle ha nytta av att konvertera och föreslår att du konverterar dessa tabeller. Rekommendationer baseras på den replikerade tabellstorleken, antalet kolumner, tabelldistributionstyp och antal partitioner i SQL Data Warehouse-tabellen. Ytterligare heuristik kan ges i rekommendationen för sammanhang. Mer information om hur den här rekommendationen bestäms finns i [SQL Data Warehouse Recommendations](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migrera ditt lagringskonto till Azure Resource Manager för att få alla de senaste Azure-funktionerna

Migrera distributionsmodellen för lagringskonto till Azure Resource Manager (Resource Manager) för att dra nytta av malldistributioner, ytterligare säkerhetsalternativ och möjligheten att uppgradera till ett GPv2-konto för användning av Azure Storages senaste funktioner. Advisor identifierar alla fristående lagringskonton som använder den klassiska distributionsmodellen och rekommenderar att du migrerar till Resurshanterarens distributionsmodell.

> [!NOTE]
> Klassiska aviseringar i Azure Monitor har dragits tillbaka i augusti 2019. Vi rekommenderar att du uppgraderar ditt klassiska lagringskonto för att använda Resource Manager för att behålla aviseringar med den nya plattformen. Mer information finns i [Classic Alerts Retirement](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Utforma dina lagringskonton för att förhindra att du når den maximala prenumerationsgränsen

En Azure-region kan stödja högst 250 lagringskonton per prenumeration. När gränsen har nåtts kan du inte skapa fler lagringskonton i den regionen/prenumerationskombinationen. Advisor kontrollerar dina prenumerationer och ytrekommendationer för att du ska kunna utforma färre lagringskonton för alla som är nära att nå maxgränsen.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Optimera prestanda för dina Azure MySQL-, Azure PostgreSQL- och Azure MariaDB-servrar 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Åtgärda CPU-trycket för dina Azure MySQL-, Azure PostgreSQL- och Azure MariaDB-servrar med CPU-flaskhalsar
Mycket hög användning av processorn under en längre period kan orsaka långsam frågeprestanda för din arbetsbelastning. Om du ökar processorstorleken kan du optimera körningen av databasfrågorna och förbättra den övergripande prestandan. Azure Advisor identifierar servrar med en hög CPU-användning som sannolikt kör CPU-begränsade arbetsbelastningar och rekommenderar skalning av din beräkning.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Minska minnesbegränsningar på dina Azure MySQL-, Azure PostgreSQL- och Azure MariaDB-servrar eller flytta till en minnesoptimerad SKU
Ett lågt cachehitförhållande kan resultera i långsammare frågeprestanda och ökad IOPS. Detta kan bero på en felaktig frågeplan eller köra en minnesintensiv arbetsbelastning. Om du åtgärdar frågeplanen eller [ökar minnet](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) för Azure-databasen för PostgreSQL-databasserver, Azure MySQL-databasserver eller Azure MariaDB-server kan du optimera körningen av databasarbetsbelastningen. Azure Advisor identifierar servrar som påverkas på grund av den här hög buffertpoolens omsättning och rekommenderar att du antingen åtgärdar frågeplanen, flyttar till en högre SKU med mer minne eller ökar lagringsstorleken för att få mer IOPS.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Använda en Azure MySQL- eller Azure PostgreSQL-läsreplik för att skala ut läsningar för läsintensiva arbetsbelastningar
Azure Advisor utnyttjar arbetsbelastningsbaserad heuristik, till exempel förhållandet mellan läsningar och skrivningar på servern under de senaste sju dagarna för att identifiera läsintensiva arbetsbelastningar. Din Azure-databas för PostgreSQL-resurs eller Azure-databas för MySQL-resurs med ett mycket högt läs-/skrivförhållande kan resultera i CPU- och/eller minneskonkurrensar som leder till långsamma frågeprestanda. Om du lägger till en [replik](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) kan du skala ut läsningar till replikservern, vilket förhindrar cpu- och/eller minnesbegränsningar på den primära servern. Advisor identifierar servrar med så höga läsintensiva arbetsbelastningar och rekommenderar att du lägger till en [läsreplik](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) för att avlasta några av läsarbetsbelastningarna.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Skala din Azure MySQL-, Azure PostgreSQL- eller Azure MariaDB-server till en högre SKU för att förhindra anslutningsbegränsningar
Varje ny anslutning till databasservern har ett visst minne. Databasserverns prestanda försämras om anslutningarna till servern misslyckas på grund av en [övre gräns](https://docs.microsoft.com/azure/postgresql/concepts-limits) i minnet. Azure Advisor identifierar servrar som körs med många anslutningsfel och rekommenderar att du uppgraderar serverns anslutningsgränser för att ge mer minne till servern genom att skala upp beräkning eller använda minnesoptimerade SKU:er, som har mer beräkning per kärna.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Skala cachen till en annan storlek eller SKU för att förbättra cache- och programprestanda

Cacheinstanser fungerar bäst när de inte körs under högt minnestryck, hög serverbelastning eller hög nätverksbandbredd, vilket kan leda till att de inte svarar, upplever dataförlust eller blir otillgängliga. Advisor identifierar cacheinstanser under dessa förhållanden och rekommenderar att du antingen tillämpar metodtips för att minska minnestrycket, serverbelastningen eller nätverksbandbredden eller skalas till en annan storlek eller SKU med större kapacitet.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Lägga till regioner med trafik till ditt Azure Cosmos DB-konto

Advisor identifierar Azure Cosmos DB-konton som har trafik från en region som för närvarande inte är konfigurerad och rekommenderar att du lägger till den regionen. Detta kommer att förbättra svarstiden för begäranden som kommer från den regionen och säkerställer tillgänglighet vid avbrott i regionen. [Läs mer om global datadistribution med Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Konfigurera din Azure Cosmos DB-indexeringsprincip med kundkom med eller uteslutna sökvägar

Azure Advisor identifierar Cosmos DB-behållare som använder standardindexeringsprincipen men kan dra nytta av en anpassad indexeringsprincip baserat på arbetsbelastningsmönstret. Standardindexeringsprincipen indexerar alla egenskaper, men om du använder en anpassad indexeringsprincip med explicita inkluderade eller uteslutna sökvägar som används i frågefilter kan du minska ru:erna och lagring som förbrukas för indexering. [Läs mer om hur du ändrar indexprinciper](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Ställ in Azure Cosmos DB-frågesidstorleken (MaxItemCount) på -1 

Azure Advisor identifierar Azure Cosmos DB-behållare som använder frågesidans storlek på 100 och rekommenderar att du använder en sidstorlek på -1 för snabbare genomsökningar. [Läs mer om maxantal](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Så här får du tillgång till prestandarekommendationer i Advisor

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

2.  Klicka på fliken Prestanda på instrumentpanelen **För Rådgivare.**

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:

* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Kostnadsrekommendationer för rådgivare](advisor-cost-recommendations.md)
* [Advisor rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Säkerhetsrekommendationer för rådgivare](advisor-security-recommendations.md)
* [Advisor Operational Excellence rekommendationer](advisor-operational-excellence-recommendations.md)
