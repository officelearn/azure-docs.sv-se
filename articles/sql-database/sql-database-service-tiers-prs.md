---
title: Azure SQL Database Premium RS tjänsten dras tillbaka nivå | Microsoft Docs
description: Premium RS tjänstnivån dras och stöd för den avslutar – Se migreringsalternativ.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/13/2018
ms.openlocfilehash: e6ce99e70d57fd8d29b0f5cb2bd1084accfc4757
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262973"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Tjänstnivå för Azure SQL Database Premium RS (förhandsversion) tas ur - alternativ för migrering

I februari 2018 meddelade Microsoft att Premium RS tjänstnivån i Azure SQL Database inte skulle släppts för allmän tillgänglighet och skulle inte längre att stödjas efter 31 januari 2019. Den här slutet av support tidsgräns har utökats till den 30 juni 2019. Den här artikeln beskriver dina alternativ för att migrera från Premium RS tjänstnivån till en annan tjänstnivå. Efter den 30 juni 2019 Microsoft kommer automatiskt att migrera Premium RS-databaser till en allmänt tillgänglig tjänstnivå som bäst motsvarar prestandakraven för Premium RS-databasen.

Följande är mål för migrering och prisalternativ som är lämpligt för Premium RS-kunder:

- vCore-tjänstnivåer

  Den **generella** och **affärskritisk** tjänstnivåer i den [vCore-baserad inköpsmodell](sql-database-service-tiers-vcore.md). Dessa två tjänstnivåer är allmänt tillgängliga. Den vCore-baserade inköpsmodellen erbjuder även den **hyperskala** tjänstnivå (i allmänt tillgänglig förhandsversion) som kan anpassas efter på begäran till din arbetsbelastning behov med automatisk skalning upp till 100 TB per databas. Tjänstnivån hyperskala ger IO-prestanda som är jämförbar med Premium-tjänstnivån i den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) till ett pris som närmare på Premium RS-tjänstnivån.
- Priser för Dev/Test

  [Priser för utveckling/testning](https://azure.microsoft.com/pricing/dev-test/) ger besparingar upp till 55% rabatt jämfört med priserna för licensinkluderade med Visual Studio-prenumerationen.
- Azure Hybrid-förmånen och priser för reserverad kapacitet

  [Azure Hybrid-förmånen och reserverad kapacitet priser](https://azure.microsoft.com/pricing/details/sql-database/) ger besparingar på upp till 80% rabatt jämfört med priserna för licensinkluderade. Mer information om dessa alternativ finns i [Azure Hybrid-förmånen för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) och [Azure SQL Database reserverad kapacitet](sql-database-reserved-capacity.md).

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Agera nu för att migrera dina databaser för Premium RS till alternativa SQL Database-tjänstnivåer

Granska riktlinjerna i den här artikeln tillsammans med våra priser och dokumentationen för att fastställa rätt migrering destinationer för Premium RS-arbetsbelastningar.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrera beräkningsintensiva arbetsbelastningar och spara

För Premium RS beräkningsintensiva arbetsbelastningar rekommenderar vi att migrera till vår allmänt tillgänglig nivån för vCore-baserade generell användning och spara mer jämfört med inkluderade licenser priser med Azure Hybrid-förmånen för SQL Server och erbjudanden för reserverad kapacitet. Om du skulle i stället är kvar på en DTU-baserade köpalternativ, kan du migrera dina beräkningsintensiva Premium RS-databaser till en Standard-tjänstnivå och fortfarande spara jämfört med den Premium RS prissättning för allmän tillgänglighet (om den hade gått för allmän tillgänglighet).

> [!WARNING]
> Migrera dina arbetsbelastningar för Premium RS till Premium DTU-baserade tjänstnivåer öka månatliga kostnader jämfört med aktuell Premium RS-prissättning. Vi rekommenderar att du överväger hyperskala eller affärskritisk nivå med Azure Hybrid-förmånen och reserverad kapacitet priser för att upprätthålla liknande eller lägre kostnader än Premium RS.

### <a name="premium-rs-databases"></a>Premium RS-databaser

|**Om du är på...**|**Migrera till jämförbara vCore-baserade...**|**Migrera till jämförbara DTU-baserade...**|
|---|---|---|
|Premium RS 1|Allmänt syfte 1 vCore (Gen 4)|Standard 3|
|Premium RS 2|Allmänt syfte 2 virtuella kärnor (Gen 4)|Standard 4|
|Premium RS 4|Allmänt syfte 4 virtuella kärnor (Gen4)|Standard 6|
|Premium RS 6|Allmänt syfte 6 virtuella kärnor (Gen4)|Standard 7|

### <a name="premium-rs-pools"></a>Premium RS-pooler

|**Om du är på...**|**Migrera till jämförbara vCore-baserade...**|**Migrera till jämförbara DTU-baserade...**|
|---|---|---|
|Premium RS lagringspoolen 125 DTU|Allmänt syfte 1 vCore (Gen 4)|Standardpool 100 edtu: er|
|Premium RS lagringspoolen 250 DTU|Allmänt syfte 2 virtuella kärnor (Gen 4)|Standardpool 250 edtu: er|
|Premium RS lagringspoolen 500 DTU|Allmänt syfte 4 virtuella kärnor (Gen4)|Standardpool 500 edtu: er|
|Premium RS lagringspoolen 1000 DTU|Allmänt syfte 8 virtuella kärnor (Gen4)|Standardpool 1000 edtu: er|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Optimera besparingar och prestanda för dina i/o-intensiva arbetsbelastningar

Vi rekommenderar att du migrerar dina i/o-intensiva enskilda databaser till vår vCore-baserade hyperskala nivå, för närvarande i förhandsversion och i/o-intensiva database-pooler till vår allmänt tillgänglig affärskritisk nivå för en optimal kombination av prestanda och kostnader.  Följande vCore-baserade alternativ att underhålla eller förbättra din aktuella prestanda och spara pengar när kombineras med Azure Hybrid-förmånen och reserverade kapacitet priser.

|**Om du är på...**|**Migrera till jämförbara vCore-baserade...**|**Migrera till jämförbara DTU-baserade...**|
|---|---|---|
|Premium RS 1|(Förhandsversion) Hyperskala 1 vCore (Gen 4) eller företag kritiska 1 vCore (Gen 4)|Premium 1|
|Premium RS 2|(Förhandsversion) Hyperskala 2 virtuella kärnor (Gen 4) eller Business kritiska 2 virtuella kärnor (Gen 4|Premium 2|
|Premium RS 4|(Förhandsversion) Hyperskala 4 virtuella kärnor (Gen 4) eller företag kritiska 4 virtuella kärnor (Gen4)|Premium 4
|Premium RS 6|(Förhandsversion) Hyperskala 6 virtuella kärnor (Gen 4) eller företag kritiska 6 virtuella kärnor (Gen4)|Premium 6|

|**Om du är på...**|**Migrera till jämförbara vCore-baserade...**|**Migrera till jämförbara DTU-baserade...**|
|---|---|---|
|Premium RS lagringspoolen 125 DTU|Företag kritiska 2 virtuella kärnor (Gen4)|Premium-pool 125 edtu: er|
|Premium RS lagringspoolen 250 DTU|Företag kritiska 2 virtuella kärnor (Gen4)|Premium-pool 250 edtu: er|
|Premium RS lagringspoolen 500 DTU|Företag kritiska 4 virtuella kärnor (Gen4)|Premium-pool 500 edtu: er|
|Premium RS lagringspoolen 1000 DTU|Företag kritiska 8 virtuella kärnor (Gen4)|Premium-pool 1000 edtu: er|

## <a name="take-advantage-of-our-new-offers"></a>Dra nytta av våra nya erbjudanden

Vår tjänstnivåer i den vCore-baserade inköpsmodellen är berättigade till specialerbjudanden som kan du spara upp till 80% rabatt jämfört med inkluderade licenser priser. Använd din SQL Server Standard eller Enterprise edition-licenser med aktiv Software Assurance och spara upp till 55% rabatt jämfört med inkluderade licenser omfattar den [Azure Hybrid-förmånen för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Du kan kombinera hybrid-förmånen med [Azure SQL Database reserverad kapacitet](sql-database-reserved-capacity.md) priser och spara upp till 80% när du genomför betalats i förskott till ett eller tre år termen.  Aktivera båda förmåner idag från Azure-portalen.

Om du har några frågor eller problem angående den här ändras eller om du behöver hjälp med migrationen, kontakta [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migrering från en tjänstnivå för Premium RS till en tjänstnivå i DTU- eller vCore-modellen

### <a name="migration-of-a-database"></a>Migrering av en databas

Migrera en databas från Premium RS-tjänsten nivå till en tjänstenivå antingen DTU eller vCore-modellen påminner om att uppgradera eller nedgradera mellan tjänstnivåer på tjänstnivån Premium RS.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Använda databaskopian och konvertera en Premium RS-databas till en DTU-baserade eller vCore-baserad databas

Du kan kopiera en databas med lämplig storlek för beräkning av Premium RS till en databas med en DTU-baserade eller vCore-baserade beräkningsstorleken utan begränsningar eller särskilda ordningsföljd så länge Målstorlek för beräkning har stöd för den maximala databasstorleken av källdatabasen. Databaskopian skapar en ögonblicksbild av data från och med starttiden för kopieringen och utför inte synkronisera data mellan källan och målet.

## <a name="next-steps"></a>Nästa steg

- För att få information om specifika storlekar och lagring som kan användas för enkel databas, finns i [SQL Database vCore-baserade resursbegränsningar för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- För att få information om specifika storlekar och lagring som kan användas för elastiska pooler, se [SQL Database vCore-baserade resursbegränsningar för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
