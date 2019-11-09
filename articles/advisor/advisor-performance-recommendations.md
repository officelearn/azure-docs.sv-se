---
title: Förbättra prestanda för Azure-program med Azure Advisor | Microsoft Docs
description: Använd Advisor för att optimera prestanda för dina Azure-distributioner.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 1fec8a10a6699dc1360494cc7ac66e13f5d37ff0
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885740"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Förbättra prestanda för Azure-program med Azure Advisor

Azure Advisor prestanda rekommendationer hjälper till att förbättra hastighet och svars tider för affärs kritiska program. Du kan få prestanda rekommendationer från Advisor på fliken **prestanda** på instrument panelen för Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Minska DNS-tiden till att leva i Traffic Managers profilen för att växla över till felfria slut punkter snabbare

Med [Inställningar för Time to Live (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) i din Traffic Manager-profil kan du ange hur snabbt ska byta slut punkter om en specifik slut punkt slutar svara på frågor. Att minska TTL-värdena innebär att klienter dirigeras till fungerande slut punkter snabbare.

Azure Advisor identifierar Traffic Manager profiler med ett längre TTL-värde konfigurerat och rekommenderar att du konfigurerar TTL till antingen 20 sekunder eller 60 sekunder beroende på om profilen har kon figurer ATS för [snabb redundans](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Förbättra databasens prestanda med SQL DB Advisor

Advisor ger dig en enhetlig, sammanslagen översikt över rekommendationer för alla dina Azure-resurser. Den integreras med SQL Database Advisor för att få rekommendationer för att förbättra prestandan för din SQL Azure-databas. SQL Database Advisor bedömer prestanda för dina SQL Azure databaser genom att analysera användnings historiken. Den erbjuder sedan rekommendationer som passar bäst för att köra databasens typiska arbets belastning.

> [!NOTE]
> För att få rekommendationer måste en databas ha ungefär en vecka med användning och inom den veckan måste det finnas en viss konsekvent aktivitet. SQL Database Advisor kan optimeras enklare för konsekventa fråge mönster än för slumpmässiga aktiviteter.

Mer information om SQL Database Advisor finns i [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>Förbättra App Service prestanda och tillförlitlighet

Azure Advisor integrerar rekommendationer för bästa praxis för att förbättra din App Services upplevelse och identifiera relevanta plattforms funktioner. Exempel på App Services rekommendationer är:
* Identifiering av instanser där minnes-eller processor resurser förbrukas av app-körtid med alternativ för att minska.
* Identifiering av instanser där collocating-resurser som webbappar och databaser kan förbättra prestanda och sänka kostnaderna.

Mer information om App Services rekommendationer finns i [metod tips för Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Använd Managed Disks för att förhindra disk-I/O-begränsning

Advisor identifierar virtuella datorer som tillhör ett lagrings konto som når sitt skalbarhets mål. Det här tillståndet gör att de virtuella datorerna är utsatta för I/O-begränsning. Advisor rekommenderar att de använder Managed Disks för att förhindra försämrade prestanda.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Förbättra prestanda och tillförlitlighet för virtuella dator diskar med hjälp av Premium Storage

Advisor identifierar virtuella datorer med standard diskar som har en stor mängd transaktioner på ditt lagrings konto och rekommenderar att du uppgraderar till Premium-diskar. 

Azure Premium Storage levererar stöd för hög prestanda, låg latens disk för virtuella datorer som kör I/O-intensiva arbets belastningar. Virtuella dator diskar som använder Premium Storage-konton lagrar data i solid state-hårddiskar (SSD). För bästa prestanda för ditt program rekommenderar vi att du migrerade alla virtuella dator diskar som kräver höga IOPS till Premium Storage.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Ta bort data skevningen i SQL Data Warehouse-tabellen för att öka prestandan för frågor

Data snedheten kan orsaka onödig data flytt eller resurs Flask halsar när du kör arbets belastningen. Advisor identifierar fördelnings data som är större än 15% och rekommenderar att du distribuerar om dina data och går tillbaka till dina val av tabell distributions nyckel. Mer information om hur du identifierar och tar bort skevning finns i [Felsöka skevning](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Skapa eller uppdatera gammal tabell statistik i SQL Data Warehouse-tabellen för att öka prestandan för frågor

Advisor identifierar tabeller som inte har aktuell [tabell statistik](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) och rekommenderar att du skapar eller uppdaterar tabell statistik. SQL Data Warehouse Query-Optimering använder uppdaterade statics för att beräkna kardinalitet eller antal rader i frågeresultatet som möjliggör optimering av frågor för att skapa en plan med hög kvalitet för snabbast prestanda.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Skala upp för att optimera användningen av cachen på dina SQL Data Warehouse tabeller för att öka prestanda för frågor

Azure Advisor identifierar om din SQL Data Warehouse har hög cache som använder procent och en låg procent andel. Det här villkoret indikerar hög cache-avlägsnande, vilket kan påverka prestanda för SQL Data Warehouse. Advisor föreslår att du skalar upp SQL Data Warehouse för att se till att du allokerar tillräckligt med cache-kapacitet för din arbets belastning.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Konvertera SQL Data Warehouse tabeller till replikerade tabeller för att öka prestanda för frågor

Advisor identifierar tabeller som inte är replikerade tabeller, men som drar nytta av att konvertera och föreslå att du konverterar dessa tabeller. Rekommendationerna baseras på den replikerade tabell storleken, antalet kolumner, tabell distributions typen och antalet partitioner i SQL Data Warehouses tabellen. Ytterligare heuristik kan ges i rekommendationen för Context. Mer information om hur den här rekommendationen fastställs finns [SQL Data Warehouse rekommendationer](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migrera ditt lagrings konto till Azure Resource Manager för att få alla de senaste Azure-funktionerna

Migrera lagrings kontots distributions modell till Azure Resource Manager (Resource Manager) för att dra nytta av mallar distributioner, ytterligare säkerhets alternativ och möjligheten att uppgradera till ett GPv2-konto för användning av Azure Storages senaste funktioner. Advisor identifierar alla fristående lagrings konton som använder den klassiska distributions modellen och rekommenderar att du migrerar till distributions modellen för Resource Manager.

> [!NOTE]
> Klassiska aviseringar i Azure Monitor har dragits tillbaka i augusti 2019. Vi rekommenderar att du uppgraderar det klassiska lagrings kontot så att du kan använda Resource Manager för att behålla aviserings funktionen med den nya plattformen. Mer information finns i [indragningen av klassiska aviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Utforma dina lagrings konton för att förhindra att den maximala prenumerations gränsen överskrids

En Azure-region kan stödja högst 250 lagrings konton per prenumeration. När gränsen har nåtts kan du inte skapa fler lagrings konton i samma region/prenumerations kombination. Advisor kontrollerar dina prenumerations-och ytbehandlings rekommendationer för att du ska kunna utforma färre lagrings konton för alla som är nära att nå Max gränsen.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Optimera prestanda för dina Azure MySQL-, Azure PostgreSQL-och Azure MariaDB-servrar 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Åtgärda CPU-belastningen på dina Azure MySQL-, Azure PostgreSQL-och Azure MariaDB-servrar med processor Flask halsar
Mycket hög användning av processorn under en längre period kan orsaka långsamma prestanda för frågor för din arbets belastning. Att öka processor storleken bidrar till att optimera körningen av databas frågorna och förbättra den övergripande prestandan. Azure Advisor identifierar servrar med hög processor användning som troligen kör CPU-begränsade arbets belastningar och rekommenderar att du skalar beräkningen.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Minska minnes begränsningar på dina Azure MySQL-, Azure PostgreSQL-och Azure MariaDB-servrar eller flytta till en minnesoptimerade SKU
Ett lågt cacheträffar kan resultera i sämre prestanda för frågor och ökad IOPS. Detta kan bero på en felaktig frågeplan eller körning av en minnes intensiv arbets belastning. Genom att åtgärda frågeuttrycket eller [öka minnet](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) för Azure Database for PostgreSQL databas server, Azure MySQL Database Server eller Azure MariaDB Server kan du optimera körningen av databasens arbets belastning. Azure Advisor identifierar servrar som påverkas på grund av den här omsättningen för den höga bufferten och rekommenderar att du reparerar frågeplan, flyttar till en högre SKU med mer minne eller ökar lagrings storleken för att få mer IOPS.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Använd en Azure MySQL-eller Azure PostgreSQL-Läs replik för att skala upp läsningar för Läs intensiva arbets belastningar
Azure Advisor använder sig av arbets belastnings Heuristiker som förhållandet mellan läsningar och skrivningar på servern under de senaste sju dagarna för att identifiera Läs intensiva arbets belastningar. Azure-databasen för PostgreSQL-resurs eller Azure-databas för MySQL-resurs med mycket höga Läs/skriv-förhållande kan leda till processor-och/eller minnes innehåll som leder till långsam frågans prestanda. Om du lägger till en [replik](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) kan du skala ut läsningar till replik servern, vilket förhindrar CPU-och/eller minnes begränsningar på den primära servern. Advisor identifierar servrar med sådana höga Läs intensiva arbets belastningar och rekommenderar att du lägger till en [Läs replik](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) för att avlasta några av de Läs arbets belastningarna.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Skala din Azure MySQL-, Azure PostgreSQL-eller Azure MariaDB-server till en högre SKU för att förhindra anslutnings begränsningar
Varje ny anslutning till din databas server upptar lite minne. Databas serverns prestanda försämras om anslutningar till servern inte kan utföras på grund av en [övre gräns](https://docs.microsoft.com/azure/postgresql/concepts-limits) i minnet. Azure Advisor identifierar servrar som kör med många anslutnings problem och rekommenderar att du uppgraderar serverns anslutnings gränser för att ge mer minne till servern genom att skala upp beräkning eller använda Minnesoptimerade SKU: er, som har mer data bearbetning per kärna.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Skala cacheminnet till en annan storlek eller SKU för att förbättra cache-och program prestanda

Cache-instanser fungerar bäst när de inte körs under hög minnes belastning, hög server belastning eller hög nätverks bandbredd, vilket kan göra att de inte svarar, få data förlust eller bli otillgängliga. Advisor identifierar cache-instanser under dessa villkor och rekommenderar att du antingen använder bästa praxis för att minska minnes belastningen, Server belastningen eller nätverks bandbredden eller skala till en annan storlek eller SKU med mer kapacitet.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Lägg till regioner med trafik till ditt Azure Cosmos DB-konto

Advisor identifierar Azure Cosmos DB konton som har trafik från en region som inte är konfigurerad för tillfället och som rekommenderar att du lägger till den regionen. Detta förbättrar svars tiden för begär Anden som kommer från den regionen och garanterar tillgängligheten i händelse av region avbrott. [Läs mer om global data distribution med Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Konfigurera Azure Cosmos DB indexerings princip med kund inkluderade eller undantagna sökvägar

Azure Advisor identifierar Cosmos DB behållare som använder standard indexerings principen, men kan dra nytta av en anpassad indexerings princip som baseras på arbets belastnings mönstret. Standard indexerings principen indexerar alla egenskaper, men om du använder en anpassad indexerings princip med explicit inkluderade eller undantagna sökvägar som används i Frågefilter kan du minska ru: er och lagrings utrymme som förbrukas för indexering. [Läs mer om hur du ändrar index principer](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Ställ in Azure Cosmos DB-frågesidstorleken (MaxItemCount) på -1 

Azure Advisor identifierar Azure Cosmos DB behållare som använder sid storleken för frågan på 100 och rekommenderar att du använder en sid storlek på-1 för snabbare genomsökningar. [Läs mer om maximalt antal objekt](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Få åtkomst till prestanda rekommendationer i Advisor

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna [Advisor](https://aka.ms/azureadvisordashboard).

2.  På Advisor-instrumentpanelen klickar du på fliken **prestanda** .

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:

* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Rekommendationer om Advisor-kostnader](advisor-performance-recommendations.md)
* [Rekommendationer om rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
