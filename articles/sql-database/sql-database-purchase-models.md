---
title: Azure SQL Database köpa modeller | Microsoft Docs
description: Läs mer om inköpschef modeller som är tillgängliga för Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 98c19732c372fbcda3ca8e746d002f94c2687b22
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431363"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Välj mellan vCore- och DTU-köp av modeller

Azure SQL Database kan du enkelt köpa en helt hanterad plattform som en tjänst (PaaS) database engine som passar ditt behov av prestanda och kostnader. Beroende på distributionsmodell som du har valt för Azure SQL Database, kan du välja den inköpsmodellen som passar dig:

- [Virtuell kärna (vCore)-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) (rekommenderas). Den här inköpsmodellen erbjuder mellan en etablerade beräkningen och en nivå för beräkning utan server (förhandsversion). Med etablerade beräkningen-nivån kan välja du den exakta mängden beräkningsresurser som levereras alltid för din arbetsbelastning. Med beräkning utan Server-nivå anger du automatisk skalning för beräkningsresurser över en rad med konfigurerbara beräkning. Med den här Beräkningsnivån kan du också automatiskt pausa och återuppta databasen baserat på arbetsbelastningen aktivitet. VCore-a-pris per tidsenhet är lägre i den etablerade Beräkningsnivån än på nivån för beräkning utan server.
- [Database transaction unit (DTU)-baserade inköpsmodellen](sql-database-service-tiers-dtu.md). Den här inköpsmodell innehåller tillsammans beräknings- och paket som belastningsutjämnade för vanliga arbetsbelastningar.

Olika inköpschef modeller är tillgängliga för olika distributionsmodeller för Azure SQL Database:

- Den [enkel databas](sql-database-single-databases-manage.md) och [elastisk pool](sql-database-elastic-pool.md) distributionsalternativ i [Azure SQL Database](sql-database-technical-overview.md) erbjuder både den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).
- Den [hanterad instans](sql-database-managed-instance.md) distributionsalternativ i Azure SQL Database erbjuder endast de [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).
- Den [hyperskala tjänstnivå](sql-database-service-tier-hyperscale.md) är tillgängligt för enskilda databaser som använder den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).

Följande tabell och diagrammet Jämför och kontrastera vCore-baserade och DTU-baserade inköpschef modeller:

|**Inköpsmodell**|**Beskrivning**|**Bäst för**|
|---|---|---|
|DTU-baserad modell|Den här modellen är baserad på ett paketerat mått på beräkning, lagring och i/o-resurser. Instansstorlekarna uttrycks i dtu: er för enkla databaser och elastiska databastransaktionsenheter (edtu: er) för elastiska pooler. Mer information om dtu: er och edtu: er finns i [vad är dtu: er och edtu: er?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Bäst för kunder som vill ha enkel, förkonfigurerade alternativ.|
|vCore-baserad modell|Den här modellen kan du välja oberoende beräknings- och lagringsresurser. Den vCore-baserade inköpsmodellen kan du använda [Azure Hybrid-förmånen för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) att få kostnadsbesparingar.|Bäst för kunder som värde flexibilitet, kontroll och transparens.|
||||  

![priser för modellen jämförelse](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Beräkna kostnader

### <a name="provisioned-compute-costs"></a>Etablerade beräkningskostnaderna

I den etablerade beräkning-nivån omfattar beräkningskostnaden totala beräkningskapaciteten som tillhandahålls för programmet.

På affärsnivå kritiska-allokera vi automatiskt minst 3 repliker. För att återspegla den här ytterligare tilldelning av beräkningsresurser är priset i den vCore-baserade inköpsmodellen cirka 2.7 x högre på kritiska-affärsnivå än på tjänstnivån generell användning. På samma sätt visar högre lagringspris per GB på kritiska-affärsnivå hög i/o och låg fördröjning av SSD-lagring.

Kostnaden för lagring av säkerhetskopior är likadan ut för kritiska-affärsnivå och generell användning tjänstnivå eftersom båda nivåerna standardlagring.

### <a name="serverless-compute-costs"></a>Kostnader för beräkning utan Server

En beskrivning av hur beräkningskapacitet definieras och kostnader beräknas för beräkning utan Server-nivå, finns i [SQL-databas utan server (förhandsversion)](sql-database-serverless.md).

## <a name="storage-costs"></a>Lagringskostnader

Olika typer av lagring faktureras på olika sätt. För lagring av data debiteras du för etablerad lagring baserat på den maximala storleken för databas eller pool du väljer. Kostnaden ändras inte såvida du inte minska eller öka den högsta. Lagringsenhet för säkerhetskopior är associerad till automatiska säkerhetskopior av din instans och allokeras dynamiskt. Öka din backup-kvarhållningsperiod ökar den lagringsenheten för säkerhetskopiering som förbrukas av instansen.

Som standard kopieras 7 dagars automatiska säkerhetskopior av dina databaser till en skrivskyddad geo-redundant lagring (RA-GRS) standard Blob storage-konto. Den här lagringen används av veckovisa, fullständiga säkerhetskopior, dagliga differentiella säkerhetskopieringar och säkerhetskopieringar av transaktionsloggen, som kopieras var femte minut. Storleken på transaktionsloggarna beror på ändringsfrekvensen i databasen. En minimimängd lagringsutrymme som är lika med 100 procent av databasens storlek tillhandahålls utan extra kostnad. Ytterligare förbrukning av lagringsenhet för säkerhetskopior debiteras i GB per månad.

Mer information om priserna för lagring finns i den [priser](https://azure.microsoft.com/pricing/details/sql-database/single/) sidan.

## <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

En virtuell kärna (vCore) representerar en logisk CPU och ger dig möjlighet att välja mellan av maskinvara och de fysiska egenskaperna av maskinvara (till exempel antalet kärnor, minne och storlek). Den vCore-baserade inköpsmodellen ger dig flexibilitet, kontroll och transparens av enskild resursförbrukning och ett enkelt sätt att överföra lokala arbetsbelastningskrav till molnet. Den här modellen kan du välja resurser för beräkning, minne och lagring som baserat dina arbetsbelastningsbehov.

I den vCore-baserade inköpsmodellen, kan du välja mellan den [generella](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) och [affärskritisk](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) tjänstnivåer för [enkla databaser](sql-database-single-database-scale.md), [ elastiska pooler](sql-database-elastic-pool.md), och [hanterade instanser](sql-database-managed-instance.md). För enskilda databaser du kan också välja den [hyperskala tjänstnivå](sql-database-service-tier-hyperscale.md).

Den vCore-baserade inköpsmodellen kan du välja beräknings- och lagringsresurser, matcha lokala prestanda och optimera priset oberoende av varandra. I den vCore-baserade inköpsmodellen betalar du för:

- Beräkningsresurs (tjänstnivån + antalet virtuella kärnor och mängden minne + generering av maskinvara).
- Typ och mängden lagringsutrymme för data och loggfiler.
- Backup storage (RA-GRS).

> [!IMPORTANT]
> Beräkningsresurser, i/o och data och loggfiler lagring debiteras per databas eller elastisk pool. Lagringsenhet för säkerhetskopior debiteras per varje databas. Läs mer om avgifter för hanterad instans, [hanterade instanser](sql-database-managed-instance.md).
> **Region begränsningar:** Den aktuella listan över regioner som stöds finns i [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Skapa en hanterad instans i en region som för närvarande inte stöds, [skicka en supportförfrågan via Azure portal](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Om din databas eller elastisk pool förbrukar mer än 300 dtu: er, kan konvertera till den vCore-baserade inköpsmodellen minska kostnaderna. Du kan konvertera med hjälp av ditt API föredrar eller med hjälp av Azure-portalen utan avbrott. Konvertering krävs inte och är inte klar automatiskt. Om den DTU-baserade inköpsmodellen uppfyller dina krav på prestanda och företag, bör du fortsätta använda den.

Välj beräkningsstorleken för att konvertera från den DTU-baserade inköpsmodellen till den vCore-baserade inköpsmodellen, med hjälp av följande tumregel:

- Varje 100 dtu: er på standard-nivån kräver minst 1 vCore i tjänstnivå för generell användning.
- Varje 125 dtu: er på premium-nivån kräver minst 1 vCore i kritiska-företagsnivån.

## <a name="dtu-based-purchasing-model"></a>DTU-baserade inköpsmodellen

En transaktion databasenhet (DTU) representerar ett blandat mått på processor, minne, läsningar och skrivningar. DTU-baserade inköpsmodellen erbjuder en uppsättning förkonfigurerade paket av beräkningsresurser och inkluderat lagringsutrymme för att driva olika nivåer av programprestanda. Om du föredrar enkelheten med ett förkonfigurerat paket och fasta betalningar varje månad, kanske den DTU-baserade modellen passar dina behov.

I DTU-baserade inköpsmodellen som du kan välja mellan basic, standard och premium-tjänstnivåer för både [enkla databaser](sql-database-single-database-scale.md) och [elastiska pooler](sql-database-elastic-pool.md). DTU-baserade inköpsmodellen är inte tillgängligt för [hanterade instanser](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Databastransaktionsenheter (dtu: er)

För en enkel databas på en specifik compute storlek i en [tjänstnivå](sql-database-single-database-scale.md), Microsoft garanterar en viss nivå av resurser för den här databasen (oberoende av någon annan databas i Azure-molnet). Garantin ger en förutsägbar prestanda. Mängden resurser som allokerats för en-databas beräknas som ett antal dtu: er och är ett paketerat mått på beräkning, lagring och i/o-resurser.

Förhållandet mellan dessa resurser är ursprungligen bestämts av en [benchmark-arbetsbelastningen för online transaktionsbearbetning (OLTP)](sql-database-benchmark-overview.md) utformad att representera verkliga OLTP-arbetsbelastningar. När din arbetsbelastning överstiger mängden av någon av dessa resurser, är ditt dataflöde begränsad, vilket resulterar i långsammare prestanda och tidsgränser.

De resurser som används av din arbetsbelastning påverkar inte resurserna som är tillgängliga för andra SQL-databaser i Azure-molnet. På samma sätt kan de resurser som används av andra arbetsbelastningar som inte påverkar resurserna som är tillgängliga för din SQL-databas.

![avgränsningsruta](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu: er som är mest användbara för att förstå de relativa resurser som ska allokeras till Azure SQL-databaser på olika instansstorlekarna och tjänstnivåer. Exempel:

- Fördubbling av dtu: erna genom att öka beräkningsstorleken för en databas motsvarar fördubbling av uppsättningen resurser som är tillgängliga för databasen.
- En premium-nivån P11-tjänstdatabasen med 1 750 dtu: er ger 350 gånger mer DTU-beräkningskraft än en enkel databas med 5 dtu: er.  

Om du vill få bättre insikt i resursförbrukning (DTU) för din arbetsbelastning kan använda [fråga prestandainsikter](sql-database-query-performance.md) till:

- Identifiera de viktigaste frågorna med CPU/varaktighet/körning antalet som potentiellt kan anpassas för bättre prestanda. Till exempel en I/O-intensiva fråga kan dra nytta av [InMemory-optimeringstekniker](sql-database-in-memory.md) för att bättre utnyttja det tillgängliga minnet på en viss tjänstnivån och beräkna storleken.
- Granska nedåt i detaljerna för en fråga för att visa texten och dess historik för Resursanvändning.
- Komma åt prestandajustering rekommendationer som visar åtgärder som vidtas av [SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Elastiska databastransaktionsenheter (edtu: er)

För SQL-databaser som alltid är tillgängliga, snarare än tillhandahåller en dedikerad uppsättning resurser (dtu: er) som inte kanske alltid behövs kan du placera dessa databaser i en [elastisk pool](sql-database-elastic-pool.md). Databaser i en elastisk pool finns på en enskild Azure SQL Database-server och delar en pool av resurser.

De delade resurserna i en elastisk pool mäts som elastiska databastransaktionsenheter (edtu: er). Elastiska pooler erbjuder en enkel och kostnadseffektiv lösning för att hantera prestandamål för flera databaser som har mycket olika och oförutsägbara användningsmönster. En elastisk pool garanterar att alla resurser inte kan användas av en databas i poolen, samtidigt som man säkerställer att varje databas i poolen alltid har en minimal mängd nödvändiga resurser som är tillgängliga.

En pool tilldelas ett bestämt antal edtu: er till ett fast pris. I den elastiska poolen kan enskilda databaserna skalas automatiskt inom konfigurerade parametrar. En databas under tyngre belastning förbrukar fler edtu: er för att möta efterfrågan. Databaser under ljusare laster förbrukar färre edtu: er. Databaser med utan belastning förbrukar inga edtu: er. Eftersom resurserna etableras för hela poolen i stället för per databas, förenkla dina hanteringsuppgifter elastiska pooler och ger en förutsägbar budget för poolen.

Du kan lägga till ytterligare edtu: er i en befintlig pool utan avbrott för databasen och utan någon inverkan på databaser i poolen. På samma sätt, om du behöver inte längre Edtu kan ta bort dem från en befintlig pool när som helst. Du kan också lägga till databaser till eller ta bort databaser från en pool när som helst. Begränsa antalet edtu: er som en databas kan använda en vid hög belastning för att reservera edtu: er för andra databaser. Om en databas underuses konsekvent resurser, flytta den från poolen och konfigurera den som en enkel databas med en förutsägbara mängd resurser som krävs.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Bestämma antalet dtu: er som krävs för en arbetsbelastning

Om du vill migrera en befintlig lokal eller SQL Server VM-arbetsbelastning till Azure SQL Database, Använd den [DTU-Kalkylatorn](https://dtucalculator.azurewebsites.net/) för att beräkna ungefär hur många Dtu: er. Använd för en befintlig Azure SQL Database-arbetsbelastning [fråga prestandainsikter](sql-database-query-performance.md) att förstå din databas-resursförbrukning (dtu: er) och få djupare kunskaper för att optimera din arbetsbelastning. Den [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) dynamisk hanteringsvy (DMV) kan du visa resursförbrukning för den senaste timmen. Den [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) katalogvy visar resursförbrukning för de senaste 14 dagarna, men med lägre tillförlitlighet med medelvärden på fem minuter.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Arbetsbelastningar som har nytta av en elastisk pool med resurser

Pooler lämpar sig väl för databaser med ett låga resursutnyttjande medelvärde och relativt ovanliga användningstoppar. SQL Database automatiskt utvärderar den historiska resursanvändningen för databaser på en befintlig SQL Database-server och rekommenderar lämplig poolkonfiguration på Azure-portalen. Mer information finns i [när bör du överväga en SQL Database-elastisk pool?](sql-database-elastic-pool.md).

## <a name="frequently-asked-questions-faqs"></a>Vanliga frågor och svar (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Måste jag ta mitt program offline för att konvertera från en DTU-baserade tjänstnivå till en vCore-baserad tjänstenivå?

Nej. Du behöver inte koppla från programmet. De nya tjänstenivåerna erbjuder ett enkelt sätt att konvertering online som liknar den befintliga processen att uppgradera databaser från standard till premium-tjänstnivån och tvärtom. Du kan starta den här konverteringen med hjälp av Azure portal, PowerShell, Azure CLI, T-SQL eller REST API. Se [Hantera enkla databaser](sql-database-single-database-scale.md) och [hantera elastiska pooler](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Kan jag byta en databas från en tjänstnivå i den vCore-baserade inköpsmodellen till en tjänstenivå i DTU-baserade inköpsmodellen?

Du kan Ja, enkelt konvertera din databas till alla mål för stöds prestanda med hjälp av Azure portal, PowerShell, Azure CLI, T-SQL eller REST API. Se [Hantera enkla databaser](sql-database-single-database-scale.md) och [hantera elastiska pooler](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om den vCore-baserade inköpsmodellen [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).
- Läs mer om den DTU-baserade inköpsmodellen [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md).
