---
title: Premium RS service nivå Pension
description: Premium RS tjänst nivå för Azure SQL Database dras tillbaka och support för den slutar att se migreringen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/07/2019
ms.openlocfilehash: e277c2660107e7bb741157b7d8c573ff69b9186e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84051208"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Azure SQL Database Premium RS tjänst nivå (för hands version) dras tillbaka – alternativ för migrering
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I februari 2018 meddelade Microsoft att den Premium RS tjänst nivån i Azure SQL Database inte skulle släppas för allmän tillgänglighet och inte längre stöds efter 31 januari 2019. Tids gränsen för slut på support har utökats till den 30 juni 2019. I den här artikeln beskrivs alternativen för att migrera från Premium RS tjänst nivå till en annan tjänst nivå. Efter den 30 juni 2019 kommer Microsoft automatiskt att migrera dina Premium RS-databaser till en allmänt tillgänglig tjänst nivå som närmast motsvarar prestanda kraven i Premium RSs databasen.

Följande är de destinationer för migrering och pris alternativ som kan vara lämpliga för Premium RS kunder:

- vCore tjänst nivåer

  **Generell användning** -och **affärskritisk** tjänst nivåerna i den [vCore-baserade inköps modellen](service-tiers-vcore.md). Dessa två tjänst nivåer är allmänt tillgängliga. Den vCore-baserade inköps modellen erbjuder också den **storskaliga** tjänst nivån som anpassar efter behov till din arbets belastnings behov med automatisk skalning upp till 100 TB per databas. Den storskaliga Service nivån ger i/o-prestanda som är jämförbar med Premium service-nivån i den [DTU-baserade inköps modellen](service-tiers-dtu.md) till ett pris närmare Premium RS tjänst nivå.
- Priser för utveckling/testning

  [Prissättningen för utveckling/testning](https://azure.microsoft.com/pricing/dev-test/) ger besparingar på upp till 55% jämfört med licens avgifter med din Visual Studio-prenumeration.
- Priser för Azure Hybrid-förmån och reserverad kapacitet

  [Priserna för Azure Hybrid-förmån och reserverad kapacitet](https://azure.microsoft.com/pricing/details/sql-database/) ger en besparing på upp till 80% jämfört med licens avgifter. Mer information om de här alternativen finns i [Azure Hybrid-förmån för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) och [Azure SQL Database reserverad kapacitet](reserved-capacity-overview.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Agera nu för att migrera Premium RS-databaser till alternativa SQL Database tjänst nivåer

Läs vägledningen i den här artikeln tillsammans med vår priser och dokumentation för att fastställa rätt migreringstjänster för dina Premium RS arbets belastningar.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrera beräknings intensiva arbets belastningar och spara

För dina beräknings intensiva Premium RS arbets belastningar rekommenderar vi att du migrerar till vår allmänt tillgängliga vCore-baserade Generell användning tjänst nivå och sparar fler jämfört med licens avgifter med hjälp av Azure Hybrid-förmån för SQL Server och reserverade kapacitets erbjudanden. Om du hellre vill behålla ett DTU-baserat köp alternativ kan du migrera dina beräknings intensiva Premium RS-databaser till en standard tjänst nivå och fortfarande Spara jämfört med den Premium RS allmänna tillgänglighets priset (om det hade blivit allmänt tillgängligt).

> [!WARNING]
> Om du migrerar Premium RS-arbetsbelastningar till DTU-baserade Premium service-nivåer kan du öka månads kostnaden jämfört med nuvarande Premium RS prissättning. Vi rekommenderar att du överväger de storskaliga eller Affärskritisk nivåerna med Azure Hybrid-förmån och reserverat kapacitets pris för att upprätthålla liknande eller lägre kostnader än Premium RS.

### <a name="premium-rs-databases"></a>Premium RS databaser

|**Om du för närvarande är på...**|**Migrera till jämförbar vCore-baserad...**|**Migrera till jämförbar DTU-baserad...**|
|---|---|---|
|Premium RS 1|Generell användning 1 vCore (Gen4)|Standard 3|
|Premium RS 2|Generell användning 2 virtuella kärnor (Gen4)|Standard 4|
|Premium RS 4|Generell användning 4 virtuella kärnor (Gen4)|Standard 6|
|Premium RS 6|Generell användning 6 virtuella kärnor (Gen4)|Standard 7|

### <a name="premium-rs-pools"></a>Premium RS pooler

|**Om du för närvarande är på...**|**Migrera till jämförbar vCore-baserad...**|**Migrera till jämförbar DTU-baserad...**|
|---|---|---|
|Premium RS pool 125 DTU|Generell användning 1 vCore (Gen4)|Standardpool 100-eDTU: er|
|Premium RS pool 250 DTU|Generell användning 2 virtuella kärnor (Gen4)|Standardpool 250-eDTU: er|
|Premium RS pool 500 DTU|Generell användning 4 virtuella kärnor (Gen4)|Standardpool 500-eDTU: er|
|Premium RS pool 1000 DTU|Generell användning 8 virtuella kärnor (Gen4)|Standardpool 1000-eDTU: er|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Optimera besparingar och prestanda för dina IO-intensiva arbets belastningar

Vi rekommenderar att du migrerar dina IO-intensiva enkla databaser till vår vCore-baserade hög skalnings nivå, som för närvarande finns i för hands version och dina IO-intensiva databas-pooler till vår allmänt tillgängliga Affärskritisk nivå, för den optimala kombinationen av prestanda och kostnad.  Följande vCore-baserade alternativ kommer att underhålla eller förbättra dina aktuella prestanda och kan spara pengar när de kombineras med Azure Hybrid-förmån och reserverade kapacitets priser.

|**Om du för närvarande är på...**|**Migrera till jämförbar vCore-baserad...**|**Migrera till jämförbar DTU-baserad...**|
|---|---|---|
|Premium RS 1| Skalning 1 vCore (Gen4) eller Affärskritisk 1 vCore (Gen4)|Premium 1|
|Premium RS 2| Storskalig 2 virtuella kärnor (Gen4) eller Affärskritisk 2 virtuella kärnor (Gen4|Premium 2|
|Premium RS 4| Storskalig 4-virtuella kärnor (Gen4) eller Affärskritisk 4 virtuella kärnor (Gen4)|Premium 4
|Premium RS 6| Storskalig 6 virtuella kärnor (Gen4) eller Affärskritisk 6 virtuella kärnor (Gen4)|Premium 6|

|**Om du för närvarande är på...**|**Migrera till jämförbar vCore-baserad...**|**Migrera till jämförbar DTU-baserad...**|
|---|---|---|
|Premium RS pool 125 DTU|Affärskritisk 2 virtuella kärnor (Gen4)|Premium-pool 125 eDTU: er|
|Premium RS pool 250 DTU|Affärskritisk 2 virtuella kärnor (Gen4)|Premium-pool 250 eDTU: er|
|Premium RS pool 500 DTU|Affärskritisk 4 virtuella kärnor (Gen4)|Premium-pool 500 eDTU: er|
|Premium RS pool 1000 DTU|Affärskritisk 8 virtuella kärnor (Gen4)|Premium-pool 1000 eDTU: er|

## <a name="take-advantage-of-our-new-offers"></a>Dra nytta av våra nya erbjudanden

Våra tjänst nivåer i den vCore-baserade inköps modellen är berättigade till Special erbjudanden som kan spara upp till 80% jämfört med licens priset. Använd SQL Server Standard-eller Enterprise Edition-licenser med aktiv Software Assurance för att spara upp till 55% jämfört med licens priset med [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) för SQL Server. Du kan kombinera hybrid-förmånen med [Azure SQL Database reserverat kapacitets](reserved-capacity-overview.md) priser och Spara upp till 80% när du betjänar på ett eller tre år.  Aktivera båda fördelarna idag från Azure Portal.

Kontakta [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)om du har några frågor eller problem angående den här ändringen eller om du behöver hjälp med migreringen.

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migrering från en Premium RS tjänst nivå till en tjänst nivå i antingen DTU-eller vCore-modellen

### <a name="migration-of-a-database"></a>Migrering av en databas

Migrering av en databas från en Premium RS tjänst nivå till en tjänst nivå i antingen DTU-eller vCore-modellen liknar uppgradering eller nedgradering mellan tjänst nivåer på Premium RS tjänst nivå.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Använda databas kopiering för att konvertera en Premium RS-databas till en DTU-baserad eller vCore-baserad databas

Du kan kopiera en databas med en Premium RS beräknings storlek till en databas med en DTU-baserad eller vCore beräknings storlek utan begränsningar eller särskilda sekvenser så länge mål beräknings storleken har stöd för den maximala databas storleken för käll databasen. Databas kopian skapar en ögonblicks bild av data från kopierings åtgärdens start tid och utför inte datasynkronisering mellan källan och målet.

## <a name="next-steps"></a>Nästa steg

- Mer information om de olika storlekarna för beräkning och lagrings storlek som är tillgängliga för en enskild databas finns i [SQL Database vCore resurs gränser för enskilda databaser](resource-limits-vcore-single-databases.md)
- Mer information om de olika beräknings storlekarna och de lagrings storlekar som är tillgängliga för elastiska pooler finns i [SQL Database vCore resurs gränser för elastiska pooler](resource-limits-vcore-elastic-pools.md).
