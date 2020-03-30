---
title: Premium RS-tjänstnivå pensionering
description: Premium RS-tjänstnivån dras tillbaka och stödet för den upphör – se migreringsalternativ.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/07/2019
ms.openlocfilehash: f00ecd19877ba6236bde5de73d450967abc1fe15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821035"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Azure SQL Database Premium RS-tjänstnivå (förhandsversion) dras tillbaka – alternativ för migrering

I februari 2018 meddelade Microsoft att Premium RS-tjänstnivån i Azure SQL Database inte skulle släppas för allmän tillgänglighet och inte längre skulle stödjas efter den 31 januari 2019. Denna sista ansökningsdag har förlängts till den 30 juni 2019. I den här artikeln beskrivs alternativen för att migrera från Premium RS-tjänstnivån till en annan tjänstnivå. Efter den 30 juni 2019 migrerar Microsoft automatiskt dina Premium RS-databaser till en allmänt tillgänglig tjänstnivå som bäst matchar prestandakraven i din Premium RS-databas.

Följande är de migreringsmål och prisalternativ som kan vara lämpliga för Premium RS-kunder:

- vCore-tjänstnivåer

  Tjänstnivåerna **Allmänt syfte** och **Affärskritiska** i den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md). Dessa två tjänstnivåer är i allmän tillgänglighet. Den vCore-baserade inköpsmodellen erbjuder också den **hyperskala** tjänstnivå som anpassar på begäran till din arbetsbelastnings behov med automatisk skalning upp till 100 TB per databas. Tjänstnivån Hyperskala ger IO-prestanda som är jämförbar med Premium-tjänstnivån i den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) till ett pris som ligger närmare Premium RS-tjänstnivån.
- Priser för utveckling/test

  [Priser för utveckling/test](https://azure.microsoft.com/pricing/dev-test/) ger besparingar på upp till 55 % jämfört med licenspriser med din Visual Studio-prenumeration.
- Azure Hybrid-förmån och prissättning för reserverad kapacitet

  [Azure Hybrid-förmån och prissättning för reserverad kapacitet](https://azure.microsoft.com/pricing/details/sql-database/) ger besparingar på upp till 80 % jämfört med licens inkluderade priser. Mer information om dessa alternativ finns i [Azure Hybrid Benefit för reserverad](https://azure.microsoft.com/pricing/hybrid-benefit/) kapacitet för SQL Server och Azure SQL [Database](sql-database-reserved-capacity.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Agera nu för att migrera dina Premium RS-databaser till alternativa SQL Database-tjänstnivåer

Läs vägledningen i den här artikeln tillsammans med vår prissättning och dokumentation för att fastställa rätt migreringsmål för dina Premium RS-arbetsbelastningar.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrera beräkningsintensiva arbetsbelastningar och spara

För dina beräkningsintensiva Premium RS-arbetsbelastningar rekommenderar vi att du migrerar till vår allmänt tillgängliga vCore-baserade tjänstnivå för allmänt syfte och sparar fler kontra licens inkluderade priser med hjälp av Azure Hybrid Benefit för SQL Server och erbjudanden med reserverad kapacitet. Om du hellre vill behålla ett DTU-baserat inköpsalternativ kan du migrera dina beräkningsintensiva Premium RS-databaser till en standardtjänstnivå och ändå spara jämfört med Premium RS-priser för allmän tillgänglighet (om det hade gått till allmän tillgänglighet).

> [!WARNING]
> Migrera dina Premium RS-arbetsbelastningar till DTU-baserade Premium-tjänstnivåer kan öka månadskostnader jämfört med gällande Premium RS-priser. Vi rekommenderar att du överväger nivåerna Hyperskala eller Affärskritiska med Azure Hybrid Benefit och prissättning för reserverad kapacitet för att upprätthålla liknande eller lägre kostnader än Premium RS.

### <a name="premium-rs-databases"></a>Premium RS-databaser

|**Om du för närvarande är på ...**|**Migrera till jämförbar vCore-baserad...**|**Migrera till jämförbara DTU-baserade...**|
|---|---|---|
|Premium RS 1|Allmänt ändamål 1 vCore (Gen4)|Standard 3|
|Premium RS 2|Allmänt ändamål 2 virtuella kärnor (Gen4)|Standard 4|
|Premium RS 4|Allmänt ändamål 4 virtuella kärnor (Gen4)|Standard 6|
|Premium RS 6|Allmänt ändamål 6 vCores (Gen4)|Standard 7|

### <a name="premium-rs-pools"></a>Premium RS-pooler

|**Om du för närvarande är på ...**|**Migrera till jämförbar vCore-baserad...**|**Migrera till jämförbara DTU-baserade...**|
|---|---|---|
|Premium RS pool 125 DTU|Allmänt ändamål 1 vCore (Gen4)|Standardpool 100 eDTUs|
|Premium RS pool 250 DTU|Allmänt ändamål 2 virtuella kärnor (Gen4)|Standardpool 250 eDTUs|
|Premium RS pool 500 DTU|Allmänt ändamål 4 virtuella kärnor (Gen4)|Standardpool 500 eDTUs|
|Premium RS pool 1000 DTU|Allmänt ändamål 8 virtuella kärnor (Gen4)|Standardpool 1000 eDTUs|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Optimera besparingar och prestanda för dina IO-intensiva arbetsbelastningar

Vi rekommenderar att du migrerar dina IO-intensiva enskilda databaser till vår vCore-baserade hyperskalanivå, som för närvarande är i förhandsversion, och dina IO-intensiva databaspooler till vår allmänt tillgängliga affärskritiska nivå, för optimal kombination av prestanda och kostnad.  Följande vCore-baserade alternativ kommer att upprätthålla eller förbättra din nuvarande prestanda och kan spara pengar i kombination med Azure Hybrid-förmånen och prissättningen för reserverad kapacitet.

|**Om du för närvarande är på ...**|**Migrera till jämförbar vCore-baserad...**|**Migrera till jämförbara DTU-baserade...**|
|---|---|---|
|Premium RS 1| Hyperskala 1 vCore (Gen4) eller affärskritisk 1 vCore (Gen4)|Premium 1|
|Premium RS 2| Hyperskala 2 virtuella kärnor (Gen4) eller affärskritiska 2 virtuella kärnor (Gen4|Premium 2|
|Premium RS 4| Hyperskala 4 virtuella kärnor (Gen4) eller affärskritiska 4 virtuella kärnor (Gen4)|Premium 4
|Premium RS 6| Hyperskala 6 virtuella kärnor (Gen4) eller affärskritiska 6 virtuella kärnor (Gen4)|Premium 6|

|**Om du för närvarande är på ...**|**Migrera till jämförbar vCore-baserad...**|**Migrera till jämförbara DTU-baserade...**|
|---|---|---|
|Premium RS pool 125 DTU|Affärskritiska 2 virtuella kärnor (Gen4)|Premium pool 125 eDTUs|
|Premium RS pool 250 DTU|Affärskritiska 2 virtuella kärnor (Gen4)|Premium pool 250 eDTUs|
|Premium RS pool 500 DTU|Affärskritiska 4 virtuella kärnor (Gen4)|Premium pool 500 eDTUs|
|Premium RS pool 1000 DTU|Affärskritiska 8 virtuella kärnor (Gen4)|Premium pool 1000 eDTUs|

## <a name="take-advantage-of-our-new-offers"></a>Dra nytta av våra nya erbjudanden

Våra servicenivåer i den vCore-baserade inköpsmodellen är berättigade till specialerbjudanden som kan spara upp till 80 % jämfört med priser som ingår i licensen. Använd dina SQL Server Standard- eller Enterprise-editionlicenser med aktiv Software Assurance för att spara upp till 55 % jämfört med licenspriser med [Azure Hybrid Benefit för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Du kan kombinera hybridförmånen med [Azure SQL Database-prissättning](sql-database-reserved-capacity.md) för reserverad kapacitet och spara upp till 80 % när du genomför förskott till en en eller treårsperiod.  Aktivera båda fördelarna idag från Azure-portalen.

Om du har några frågor eller funderingar angående den här ändringen eller om du behöver migreringshjälp kontaktar du [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migrering från en Premium RS-tjänstnivå till en tjänstnivå i antingen DTU- eller vCore-modellen

### <a name="migration-of-a-database"></a>Migrering av en databas

Migrera en databas från en Premium RS-tjänstnivå till en tjänstnivå i antingen DTU eller vCore-modellen liknar uppgradering eller nedgradering mellan tjänstnivåer på Premium RS-tjänstnivån.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Använda databaskopia för att konvertera en Premium RS-databas till en DTU-baserad eller vCore-baserad databas

Du kan kopiera en databas med en Premium RS-beräkningsstorlek till en databas med en DTU-baserad eller vCore-baserad beräkningsstorlek utan begränsningar eller särskild sekvensering så länge målberäkningsstorleken stöder källdatabasens maximala databasstorlek. Databaskopian skapar en ögonblicksbild av data från och med kopieringens starttid och utför inte datasynkronisering mellan källan och målet.

## <a name="next-steps"></a>Nästa steg

- Mer information om specifika beräkningsstorlekar och lagringsstorleksalternativ som är tillgängliga för en enskild databas finns i [SQL Database vCore-baserade resursgränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)
- Mer information om specifika beräkningsstorlekar och lagringsstorleksalternativ som är tillgängliga för elastiska pooler finns i [SQL Database vCore-baserade resursgränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).
