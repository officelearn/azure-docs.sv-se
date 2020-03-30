---
title: Köpmodeller
description: Lär dig mer om de inköpsmodeller som är tillgängliga för Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 97ce402045cfd2c990b457c5d4d06888cda632d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255995"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Välj mellan vCore och DTU inköpsmodeller

Med Azure SQL Database kan du enkelt köpa en fullständigt hanterad plattform som en tjänst (PaaS) databasmotor som passar dina prestanda- och kostnadsbehov. Beroende på vilken distributionsmodell du har valt för Azure SQL Database kan du välja den inköpsmodell som fungerar för dig:

- [Virtual core (vCore)-baserad inköpsmodell](sql-database-service-tiers-vcore.md) (rekommenderas). Den här inköpsmodellen ger ett val mellan en etablerad beräkningsnivå och en serverlös beräkningsnivå. Med den etablerade beräkningsnivån väljer du den exakta mängden beräkningsresurser som alltid har etablerats för din arbetsbelastning. Med den serverlösa beräkningsnivån anger du automatisk skalning av beräkningsresurserna över ett konfigurerbart beräkningsområde. Med den här beräkningsnivån kan du också automatiskt pausa och återuppta databasen baserat på arbetsbelastningsaktivitet. VCore-enhetspriset per tidsenhet är lägre i den etablerade beräkningsnivån än på den serverlösa beräkningsnivån.
- [Databastransaktionsenhet (DTU)-baserad inköpsmodell](sql-database-service-tiers-dtu.md). Den här inköpsmodellen tillhandahåller paketerade beräknings- och lagringspaket som är balanserade för vanliga arbetsbelastningar.

Olika inköpsmodeller är tillgängliga för olika Azure SQL Database-distributionsmodeller:

- De [enskilda alternativen för distribution](sql-database-single-databases-manage.md) av en databas och elastisk [pool](sql-database-elastic-pool.md) i Azure [SQL Database](sql-database-technical-overview.md) erbjuder både den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).
- Distributionsalternativet [för hanterade instanser](sql-database-managed-instance.md) i Azure SQL Database erbjuder endast den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).
- [Tjänstnivån Hyperskala](sql-database-service-tier-hyperscale.md) är tillgänglig för enskilda databaser som använder den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).

Följande tabell och diagram jämför och kontrasterar de vCore-baserade och DTU-baserade inköpsmodellerna:

|**Inköpsmodell**|**Beskrivning**|**Bäst för**|
|---|---|---|
|DTU-baserad modell|Den här modellen baseras på ett paketerat mått på beräknings-, lagrings- och I/O-resurser. Beräkningsstorlekar uttrycks i DTI:er för enskilda databaser och i elastiska databastransaktionsenheter (eDTI:er) för elastiska pooler. Mer information om DTI:er och eDU:er finns i [Vad är DTI:er och eDTI:er?](sql-database-purchase-models.md#dtu-based-purchasing-model)|Bäst för kunder som vill ha enkla, förkonfigurerade resursalternativ.|
|vCore-baserad modell|Med den här modellen kan du självständigt välja beräknings- och lagringsresurser. Den vCore-baserade inköpsmodellen gör att du också kan använda [Azure Hybrid Benefit för SQL Server för](https://azure.microsoft.com/pricing/hybrid-benefit/) att få kostnadsbesparingar.|Bäst för kunder som värdesätter flexibilitet, kontroll och transparens.|
||||  

![jämförelse av prissättningsmodell](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Beräkna kostnader

### <a name="provisioned-compute-costs"></a>Etablerade beräkningskostnader

I den etablerade beräkningsnivån återspeglar beräkningskostnaden den totala beräkningskapaciteten som har etablerats för programmet.

På tjänstnivån Affärskritiska allokerar vi automatiskt minst 3 repliker. För att återspegla den här ytterligare allokeringen av beräkningsresurser är priset i den vCore-baserade inköpsmodellen ungefär 2,7 x högre på tjänstnivån Affärskritisk än den är på tjänstnivån Allmänt syfte. På samma sätt återspeglar det högre lagringspriset per GB på tjänstnivån Affärskritiskt de högre IO-gränserna och den lägre svarstiden för SSD-lagringen.

Kostnaden för säkerhetskopieringslagring är densamma för tjänstnivån För affärskritisk och tjänstnivån Allmänt ändamål eftersom båda nivåerna använder standardlagring för säkerhetskopior.

### <a name="serverless-compute-costs"></a>Beräkningskostnader utan server

En beskrivning av hur beräkningskapacitet definieras och kostnader beräknas för den serverlösa beräkningsnivån finns i [SQL Database serverless](sql-database-serverless.md).

## <a name="storage-costs"></a>Lagringskostnader

Olika typer av lagring faktureras på olika sätt. För datalagring debiteras du för den etablerade lagringen baserat på den maximala databas- eller poolstorlek du väljer. Kostnaden ändras inte om du inte minskar eller ökar det maximala värdet. Säkerhetskopieringslagring är associerad med automatiska säkerhetskopior av din instans och allokeras dynamiskt. Om du ökar lagringsperioden för säkerhetskopiering ökar säkerhetskopieringslagringen som förbrukas av din instans.

Som standard kopieras 7 dagar med automatiska säkerhetskopior av dina databaser till ett dosig geoupptundant lagringskonto (RA-GRS). Den här lagringen används av veckovisa fullständiga säkerhetskopior, dagliga differentiella säkerhetskopior och säkerhetskopiering av transaktionsloggar, som kopieras var femte minut. Storleken på transaktionsloggarna beror på hur stor ändringstakten för databasen. Ett minsta lagringsbelopp som motsvarar 100 procent av databasstorleken tillhandahålls utan extra kostnad. Ytterligare förbrukning av säkerhetskopieringslagring debiteras i GB per månad.

Mer information om lagringspriser finns på [prissidan.](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

En virtuell kärna (vCore) representerar en logisk CPU och ger dig möjlighet att välja mellan generationer av maskinvara och de fysiska egenskaperna hos maskinvaran (till exempel antalet kärnor, minnet och lagringsstorleken). Den vCore-baserade inköpsmodellen ger dig flexibilitet, kontroll, transparens för individuell resursförbrukning och ett enkelt sätt att översätta lokala arbetsbelastningskrav till molnet. Med den här modellen kan du välja beräknings-, minnes- och lagringsresurser baserat på dina arbetsbelastningsbehov.

I den vCore-baserade inköpsmodellen kan du välja mellan tjänstnivåerna [Allmänt ändamål](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) och [Affärskritiskt](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) för [enskilda databaser,](sql-database-single-database-scale.md) [elastiska pooler](sql-database-elastic-pool.md)och [hanterade instanser](sql-database-managed-instance.md). För enskilda databaser kan du också välja [tjänstnivån Hyperskala](sql-database-service-tier-hyperscale.md).

Med den vCore-baserade inköpsmodellen kan du självständigt välja beräknings- och lagringsresurser, matcha lokala prestanda och optimera priset. I den vCore-baserade inköpsmodellen betalar du för:

- Beräkningsresurser (tjänstnivån + antalet virtuella kärnor och mängden minne + generering av maskinvara).
- Typ och mängd data och logglagring.
- Lagring av säkerhetskopiering (RA-GRS).

> [!IMPORTANT]
> Beräkningsresurser, I/O och data- och logglagring debiteras per databas eller elastisk pool. Lagring av säkerhetskopior debiteras per varje databas. Mer information om hanterade instansavgifter finns i [hanterade instanser](sql-database-managed-instance.md).
> **Begränsningar i regionen:** Den aktuella listan över regioner som stöds finns i [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Om du vill skapa en hanterad instans i en region som för närvarande inte stöds [skickar du en supportbegäran via Azure-portalen](quota-increase-request.md).

Om din enda databas eller elastiska pool förbrukar mer än 300 DTUs kan det minska kostnaderna om du konverterar till den vCore-baserade inköpsmodellen. Du kan konvertera med hjälp av ditt API val eller med hjälp av Azure-portalen, utan driftstopp. Konvertering krävs dock inte och görs inte automatiskt. Om den DTU-baserade inköpsmodellen uppfyller dina prestanda- och affärskrav bör du fortsätta använda den.

Om du vill konvertera från den DTU-baserade inköpsmodellen till den vCore-baserade inköpsmodellen väljer du beräkningsstorleken med hjälp av följande tumregler:

- Varje 100 DU:er på standardnivån kräver minst 1 vCore på tjänstnivån Allmänt syfte.
- Varje 125 DU:er på premiumnivån kräver minst 1 vCore på tjänstnivån Affärskritisk.

> [!NOTE]
> DTU till vCore storlek riktlinjer är ungefärliga, och tillhandahålls för att hjälpa till i den första uppskattningen av målet databas tjänst mål. Den optimala konfigurationen av måldatabasen är arbetsbelastningsberoende. 
> 
> För att uppnå det optimala förhållandet mellan pris och prestanda kan det krävas att man utnyttjar flexibiliteten hos vCore-modellen för att justera antalet virtuella kärnor, [maskinvarugenerering,](sql-database-service-tiers-vcore.md#hardware-generations) [tjänst-](sql-database-service-tiers-vcore.md#service-tiers) och [beräkningsnivåer](sql-database-service-tiers-vcore.md#compute-tiers) samt justering av andra databaskonfigurationsparametrar, till exempel [maximal grad av parallellitet](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing).

## <a name="dtu-based-purchasing-model"></a>DTU-baserad inköpsmodell

En databastransaktionsenhet (DTU) representerar ett blandat mått på CPU, minne, läsningar och skrivningar. Den DTU-baserade inköpsmodellen erbjuder en uppsättning förkonfigurerade paket med beräkningsresurser och inkluderad lagring för att driva olika nivåer av programprestanda. Om du föredrar enkelheten i ett förkonfigurerat paket och fasta betalningar varje månad kan den DTU-baserade modellen vara mer lämplig för dina behov.

I den DTU-baserade inköpsmodellen kan du välja mellan de grundläggande, standard- och premiumtjänstnivåerna för både [enskilda databaser](sql-database-single-database-scale.md) och [elastiska pooler](sql-database-elastic-pool.md). Den DTU-baserade inköpsmodellen är inte tillgänglig för [hanterade instanser](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Databastransaktionsenheter (DU: er)

För en enskild databas med en viss beräkningsstorlek inom en [tjänstnivå](sql-database-single-database-scale.md)garanterar Microsoft en viss resursnivå för databasen (oberoende av någon annan databas i Azure-molnet). Denna garanti ger en förutsägbar prestandanivå. Mängden resurser som allokerats för en databas beräknas som ett antal DU:er och är ett paketerat mått på beräknings-, lagrings- och I/O-resurser.

Förhållandet mellan dessa resurser bestäms ursprungligen av en [online-transaktionsbearbetning (OLTP) benchmark arbetsbelastning](sql-database-benchmark-overview.md) utformad för att vara typisk för verkliga OLTP-arbetsbelastningar. När din arbetsbelastning överskrider mängden av dessa resurser begränsas dataflödet, vilket resulterar i långsammare prestanda och time-outs.

De resurser som används av din arbetsbelastning påverkar inte de resurser som är tillgängliga för andra SQL-databaser i Azure-molnet. På samma sätt påverkar de resurser som används av andra arbetsbelastningar inte de resurser som är tillgängliga för DIN SQL-databas.

![Markeringsramen](./media/sql-database-what-is-a-dtu/bounding-box.png)

DU:er är mest användbara för att förstå de relativa resurser som allokeras för Azure SQL-databaser på olika beräkningsstorlekar och tjänstnivåer. Ett exempel:

- En fördubbling av DU:erna genom att öka beräkningsstorleken för en databas motsvarar en fördubbling av den uppsättning resurser som är tillgängliga för databasen.
- En premiumtjänstnivå P11-databas med 1750 DTU:er ger 350x mer DTU-beräkningskraft än en grundläggande tjänstnivådatabas med 5 DTU:er.  

Om du vill få djupare insikt i resursförbrukningen (DTU) för din arbetsbelastning använder du [information om frågeprestanda](sql-database-query-performance.md) för att:

- Identifiera de vanligaste frågorna efter antal processorer/varaktighet/körning som eventuellt kan justeras för bättre prestanda. En I/O-intensiv fråga kan till exempel dra nytta av [optimeringstekniker i minnet](sql-database-in-memory.md) för att bättre utnyttja det tillgängliga minnet på en viss tjänstnivå och beräkningsstorlek.
- Öka detaljnivån i information om en fråga för att visa dess text och dess historik över resursanvändning.
- Rekommendationer för prestandajustering i Access som visar åtgärder som vidtagits av [SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Elastiska databastransaktionsenheter (eDTI:er)

För SQL-databaser som alltid är tillgängliga, i stället för att tillhandahålla en dedikerad uppsättning resurser (DU: er) som kanske inte alltid behövs, kan du placera dessa databaser i en [elastisk pool](sql-database-elastic-pool.md). Databaserna i en elastisk pool finns på en enda Azure SQL Database-server och delar en pool med resurser.

De delade resurserna i en elastisk pool mäts av elastiska databastransaktionsenheter (eDTI:er). Elastiska pooler ger en enkel och kostnadseffektiv lösning för att hantera prestandamål för flera databaser som har mycket varierande och oförutsägbara användningsmönster. En elastisk pool garanterar att alla resurser inte kan förbrukas av en databas i poolen, samtidigt som varje databas i poolen alltid har en minsta mängd tillgängliga resurser.

En pool får ett visst antal eDTUs för ett fast pris. I den elastiska poolen kan enskilda databaser automatiskt skalas inom de konfigurerade gränserna. En databas under en tyngre belastning kommer att förbruka fler eDTUs för att möta efterfrågan. Databaser under lättare belastningar förbrukar färre eDTI:er. Databaser utan belastning förbrukar inga eD RU:er. Eftersom resurser är etablerade för hela poolen, i stället för per databas, förenklar elastiska pooler dina hanteringsuppgifter och ger en förutsägbar budget för poolen.

Du kan lägga till ytterligare eDU:er i en befintlig pool utan avbrott i databasen och utan påverkan på databaserna i poolen. På samma sätt, om du inte längre behöver extra eDTUs, ta bort dem från en befintlig pool när som helst. Du kan också lägga till databaser i eller subtrahera databaser från en pool när som helst. Om du vill reservera eDTI:er för andra databaser begränsar du antalet eDTI:er som en databas kan använda under en tung belastning. Om en databas konsekvent underbestrar resurser flyttar du den från poolen och konfigurerar den som en enda databas med en förutsägbar mängd resurser som krävs.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Fastställa antalet DTUs som behövs av en arbetsbelastning

Om du vill migrera en befintlig lokal eller virtuell SQL Server-arbetsbelastning till Azure SQL Database använder du [DTU-kalkylatorn](https://dtucalculator.azurewebsites.net/) för att approximera antalet DTU:er som behövs. För en befintlig Azure SQL Database-arbetsbelastning använder du [frågeprestandainsikter](sql-database-query-performance.md) för att förstå din databasresursförbrukning (DTUs) och få djupare insikter för att optimera din arbetsbelastning. Med [den dynamiska hanteringsvyn dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) (DMV) kan du visa resursförbrukning för den senaste timmen. [Katalogvyn sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) visar resursförbrukning för de senaste 14 dagarna, men med en lägre trohet på fem minuters medelvärden.

### <a name="determine-dtu-utilization"></a>Bestäm DTU-användning

Om du vill bestämma den genomsnittliga procentandelen DTU/eDTU-användning i förhållande till DTU/eDTU-gränsen för en databas eller en elastisk pool använder du följande formel:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

Indatavärdena för den här formeln kan erhållas från [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)och [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMVs. Med andra ord, för att bestämma procentandelen DTU/eDTU-användning mot DTU/eDTU-gränsen för en databas eller `avg_cpu_percent` `avg_data_io_percent`en `avg_log_write_percent` elastisk pool, väljer du det största procentvärdet från följande: , och vid en viss tidpunkt.

> [!NOTE]
> DTU-gränsen för en databas bestäms av CPU, läsningar, skrivningar och minne som är tillgängligt för databasen. Eftersom SQL Server-databasmotorn vanligtvis använder allt tillgängligt minne för `avg_memory_usage_percent` datacachen för att förbättra prestanda, är värdet vanligtvis nära 100 % oavsett aktuell databasbelastning. Även om minnet indirekt påverkar DTU-gränsen används det inte i formeln för DTU-utnyttjande.
>

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Arbetsbelastningar som drar nytta av en elastisk resurspool

Pooler är väl lämpade för databaser med ett lågt resursutnyttjande genomsnitt och relativt sällan utnyttjande spikar. Mer information finns i [När bör du överväga en elastisk SQL-databaspool?](sql-database-elastic-pool.md).

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Hårdvarugenerationer i den DTU-baserade inköpsmodellen

I den DTU-baserade inköpsmodellen kan kunderna inte välja den maskinvarugenerering som används för deras databaser. Medan en viss databas vanligtvis stannar på en viss maskinvarugenerering under en lång tid (vanligen i flera månader), finns det vissa händelser som kan orsaka att en databas flyttas till en annan maskinvarugenerering.

En databas kan till exempel flyttas till en annan maskinvarugenerering om den skalas upp eller ned till ett annat tjänstemål, eller om den aktuella infrastrukturen i ett datacenter närmar sig sina kapacitetsgränser eller om den maskinvara som för närvarande används avvecklas på grund av dess uttjänt.

Om en databas flyttas till en annan maskinvara kan arbetsbelastningsprestandan ändras. DTU-modellen garanterar att dataflödet och svarstiden för [DTU-benchmark-arbetsbelastningen](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark) förblir i stort sett identisk när databasen flyttas till en annan maskinvarugenerering, så länge dess tjänstemål (antalet DTU:er) förblir detsamma. 

Men över det breda spektrumet av kundarbetsbelastningar som körs i Azure SQL Database kan effekten av att använda olika maskinvara för samma tjänstmål vara mer uttalad. Olika arbetsbelastningar kommer att dra nytta av olika maskinvarukonfiguration och funktioner. För andra arbetsbelastningar än DTU-riktmärket är det därför möjligt att se prestandaskillnader om databasen flyttas från en maskinvarugenerering till en annan.

Ett program som är känsligt för nätverksfördröjning kan till exempel se bättre prestanda på Gen5-maskinvara jämfört med Gen4 på grund av användningen av Accelerated Networking in Gen5, men ett program med intensiv läsning IO kan se bättre prestanda på Gen4-maskinvara jämfört med Gen5 på grund av minne per kärnkvot på Gen4.

Kunder med arbetsbelastningar som är känsliga för maskinvaruändringar eller kunder som vill styra valet av maskinvarugenerering för sin databas kan använda [vCore-modellen](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore) för att välja önskad maskinvarugenerering när databasen skapas och skalning. I vCore-modellen dokumenteras resursgränser för varje tjänstmål för varje maskinvarugenerering för både [enskilda databaser](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) och [elastiska pooler](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools). Mer information om maskinvarugenerationer i vCore-modellen finns i [Hardware Generations](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations).

## <a name="frequently-asked-questions-faqs"></a>Vanliga frågor och svar (vanliga frågor)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Måste jag koppla från mitt program för att konvertera från en DTU-baserad tjänstnivå till en vCore-baserad tjänstnivå?

Nej. Du behöver inte koppla från programmet. De nya tjänstnivåerna erbjuder en enkel onlinekonverteringsmetod som liknar den befintliga processen att uppgradera databaser från standardnivån till premiumtjänstnivån och tvärtom. Du kan starta den här konverteringen med hjälp av Azure-portalen, PowerShell, Azure CLI, T-SQL eller REST API. Se [Hantera enskilda databaser](sql-database-single-database-scale.md) och Hantera [elastiska pooler](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Kan jag konvertera en databas från en tjänstnivå i den vCore-baserade inköpsmodellen till en tjänstnivå i den DTU-baserade inköpsmodellen?

Ja, du kan enkelt konvertera databasen till alla prestandamål som stöds med hjälp av Azure-portalen, PowerShell, Azure CLI, T-SQL eller REST API. Se [Hantera enskilda databaser](sql-database-single-database-scale.md) och Hantera [elastiska pooler](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om den vCore-baserade inköpsmodellen finns i [vCore-baserad inköpsmodell](sql-database-service-tiers-vcore.md).
- Mer information om den DTU-baserade inköpsmodellen finns i [DTU-baserad inköpsmodell](sql-database-service-tiers-dtu.md).
