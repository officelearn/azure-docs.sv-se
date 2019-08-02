---
title: Azure SQL Database inköps modeller | Microsoft Docs
description: Lär dig mer om de inköps modeller som är tillgängliga för Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: 98d257c28ab5ff2cf902c0b8205ac8918ccf4d45
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567012"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Välj mellan vCore och DTU inköps modeller

Med Azure SQL Database kan du enkelt köpa en helt hanterad plattform som en tjänst (PaaS) databas motor som passar dina prestanda-och kostnads behov. Beroende på vilken distributions modell som du har valt för Azure SQL Database kan du välja den inköps modell som passar dig bäst:

- [Virtual Core (vCore)-baserad inköps modell](sql-database-service-tiers-vcore.md) (rekommenderas). Den här inköps modellen ger ett val mellan en allokerad beräknings nivå och en server lös (för hands version) beräknings nivå. Med den allokerade beräknings nivån väljer du den exakta mängden beräknings resurser som alltid är etablerade för din arbets belastning. Med Server lös beräknings nivån anger du automatisk skalning av beräknings resurserna över ett konfigurerbart beräknings intervall. Med den här beräknings nivån kan du också automatiskt pausa och återuppta databasen baserat på arbets belastnings aktivitet. VCore enhets pris per tidsenhet är lägre än den allokerade beräknings nivån än den är i Server lös beräknings nivån.
- [-Baserad inköps modell för databas transaktions enhet (DTU)](sql-database-service-tiers-dtu.md). Den här inköps modellen tillhandahåller paket för beräkning och lagring som är balanserade för vanliga arbets belastningar.

Olika inköps modeller är tillgängliga för olika Azure SQL Database distributions modeller:

- Distributions alternativen för [enkel databas](sql-database-single-databases-manage.md) och [elastisk pool](sql-database-elastic-pool.md) i [Azure SQL Database](sql-database-technical-overview.md) erbjuder både den [DTU-baserade inköps modellen](sql-database-service-tiers-dtu.md) och den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md).
- Distributions alternativet för [hanterade instanser](sql-database-managed-instance.md) i Azure SQL Database erbjuder bara den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md).
- Den [storskaliga Service nivån](sql-database-service-tier-hyperscale.md) är tillgänglig för enskilda databaser som använder den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md).

I följande tabell och diagram jämförs och kontrasterar vCore-baserade och DTU-baserade inköps modeller:

|**Inköps modell**|**Beskrivning**|**Bäst för**|
|---|---|---|
|DTU-baserad modell|Den här modellen är baserad på ett samlat mått för beräknings-, lagrings-och I/O-resurser. Beräknings storlekarna uttrycks i DTU: er för enskilda databaser och i elastiska databas transaktions enheter (eDTU: er) för elastiska pooler. Mer information om DTU: er och eDTU: er finns i [Vad är DTU: er och eDTU: er?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Bäst för kunder som vill ha enkla, förkonfigurerade resurs alternativ.|
|vCore-baserad modell|Med den här modellen kan du oberoende välja beräknings-och lagrings resurser. Med den vCore-baserade inköps modellen kan du också använda [Azure Hybrid-förmån för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) för att få kostnads besparingar.|Bäst för kunder med värde flexibilitet, kontroll och transparens.|
||||  

![jämförelse av pris modell](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Beräknings kostnader

### <a name="provisioned-compute-costs"></a>Etablerade beräknings kostnader

I den allokerade beräknings nivån återspeglar beräknings kostnaden den totala beräknings kapaciteten som är etablerad för programmet.

I nivån verksamhets kritisk tjänst allokerar vi automatiskt minst tre repliker. För att avspegla den här ytterligare allokeringen av beräknings resurser, är priset i den vCore-baserade inköps modellen cirka 2.7 x högre i nivån affärs kritisk tjänst än den är i tjänst nivån allmän användning. På samma sätt återspeglar det högre lagrings priset per GB i nivån för verksamhets kritiska tjänster den höga I/O och låg latens för SSD-lagringen.

Kostnaden för lagring av säkerhets kopior är samma för nivån affärs kritisk tjänst och tjänst nivån generell användning eftersom båda nivåerna använder standard lagring.

### <a name="serverless-compute-costs"></a>Beräknings kostnader utan Server

En beskrivning av hur beräknings kapaciteten definieras och kostnader beräknas för Server lös beräknings nivån finns [SQL Database utan server (för hands version)](sql-database-serverless.md).

## <a name="storage-costs"></a>Lagringskostnader

Olika typer av lagring faktureras på olika sätt. För data lagring debiteras du för det tillhandahållna lagrings utrymmet baserat på den maximala databas-eller pool storlek som du väljer. Kostnaden ändras inte om du inte minskar eller ökar Max värdet. Lagring av säkerhets kopior är kopplad till automatiserade säkerhets kopieringar av din instans och tilldelas dynamiskt. Om du ökar säkerhets kopierings perioden ökar säkerhets kopierings lagringen som används av din instans.

Som standard kopieras sju dagars automatiserade säkerhets kopieringar av dina databaser till ett GRS-standard-Blob Storage-konto (Read-Access Geo-redundant Storage). Den här lagringen används av varje veckovis fullständig säkerhets kopiering, dagliga differentiella säkerhets kopieringar och säkerhets kopior av transaktions loggar, som kopieras var femte minut. Storleken på transaktions loggarna beror på ändrings takten för databasen. En minsta lagrings mängd som motsvarar 100 procent av databasens storlek tillhandahålls utan extra kostnad. Ytterligare förbrukning av lagring av säkerhets kopior debiteras i GB per månad.

Mer information om lagrings priser finns på sidan med [priser](https://azure.microsoft.com/pricing/details/sql-database/single/) .

## <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

En virtuell kärna (vCore) representerar en logisk CPU och ger dig möjlighet att välja mellan generationens maskin vara och maskin varans fysiska egenskaper (till exempel antalet kärnor, minne och lagrings storlek). Den vCore-baserade inköps modellen ger dig flexibilitet, kontroll, transparens för enskild resursanvändning och ett enkelt sätt att översätta lokala arbets belastnings krav till molnet. Med den här modellen kan du välja beräknings-, minnes-och lagrings resurser baserat på dina arbets belastnings behov.

I den vCore-baserade inköps modellen kan du välja mellan [allmänna syften](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) och [affärs kritiska](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) tjänst nivåer för [enskilda databaser](sql-database-single-database-scale.md), elastiska [pooler](sql-database-elastic-pool.md)och hanterade [instanser](sql-database-managed-instance.md). För enskilda databaser kan du också välja den [storskaliga tjänst nivån](sql-database-service-tier-hyperscale.md).

Med den vCore-baserade inköps modellen kan du välja beräknings-och lagrings resurser oberoende av varandra, matcha lokala prestanda och optimera priset. I den vCore-baserade inköps modellen betalar du för:

- Beräknings resurser (tjänst nivå + antal virtuella kärnor och mängden minne och maskin varans generation).
- Typ av och mängd data och logg lagring.
- Lagring av säkerhets kopior (RA-GRS).

> [!IMPORTANT]
> Beräknings resurser, I/O och data-och logg lagring debiteras per databas eller elastisk pool. Lagring av säkerhets kopior debiteras per varje databas. Mer information om avgifter för hanterade instanser finns i [hanterade instanser](sql-database-managed-instance.md).
> **Regions begränsningar:** Den aktuella listan över regioner som stöds finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Om du vill skapa en hanterad instans i en region som för närvarande inte stöds [skickar du en supportbegäran via Azure Portal](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Om den enskilda databasen eller den elastiska poolen förbrukar mer än 300 DTU: er kan du minska kostnaderna genom att konvertera till den vCore-baserade inköps modellen. Du kan konvertera genom att använda ditt API eller genom att använda Azure Portal, utan drift stopp. Konverteringen är dock inte obligatorisk och sker inte automatiskt. Om den DTU-baserade inköps modellen uppfyller dina prestanda-och affärs behov bör du fortsätta att använda den.

Om du vill konvertera från den DTU-baserade inköps modellen till den vCore-baserade inköps modellen väljer du beräknings storlek med hjälp av följande regler:

- Varje 100 DTU: er på standard nivån kräver minst 1 vCore i tjänst nivån allmän användning.
- Varje 125 DTU: er på Premium nivån kräver minst 1 vCore i nivån affärs kritisk tjänst.

## <a name="dtu-based-purchasing-model"></a>DTU-baserad inköps modell

En databas transaktions enhet (DTU) representerar ett blandat mått på processor, minne, läsningar och skrivningar. Den DTU-baserade inköps modellen erbjuder en uppsättning förkonfigurerade paket med beräknings resurser och inkluderat lagrings utrymme för att driva olika nivåer av program prestanda. Om du föredrar att förenkla en förkonfigurerad samling och fasta betalningar varje månad kan den DTU-baserade modellen vara lämpligare för dina behov.

I den DTU-baserade inköps modellen kan du välja mellan tjänst nivåerna Basic, standard och Premium för både [enkla databaser](sql-database-single-database-scale.md) och [elastiska pooler](sql-database-elastic-pool.md). Den DTU-baserade inköps modellen är inte tillgänglig för [hanterade instanser](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Databas transaktions enheter (DTU: er)

För en enskild databas med en viss beräknings storlek inom en [tjänst nivå](sql-database-single-database-scale.md)garanterar Microsoft en viss nivå av resurser för databasen (oberoende av andra databaser i Azure-molnet). Den här garantin ger en förutsägbar prestanda nivå. Mängden resurser som har allokerats för en databas beräknas som ett antal DTU: er och är ett samlat mått för beräknings-, lagrings-och I/O-resurser.

Förhållandet mellan dessa resurser bestäms ursprungligen av en [benchmark-belastning (Online Transaction Processing)](sql-database-benchmark-overview.md) som utformats för att vara typiska för verkliga OLTP-arbetsbelastningar. När din arbets belastning överskrider mängden av någon av dessa resurser begränsas ditt data flöde, vilket ger sämre prestanda och timeout.

De resurser som används av arbets belastningen påverkar inte resurserna som är tillgängliga för andra SQL-databaser i Azure-molnet. På samma sätt påverkar resurserna som används av andra arbets belastningar inte resurserna som är tillgängliga för din SQL-databas.

![avgränsnings ruta](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTU: er är mest användbara för att förstå de relativa resurser som är allokerade för Azure SQL-databaser med olika beräknings storlekar och tjänst nivåer. Exempel:

- Att dubblera DTU: er genom att öka beräknings storleken för en databas, motsvarar att dubblera den uppsättning av resurser som är tillgängliga för den databasen.
- En Premium Service Tier p11-databas med 1750 DTU: er ger beräkningskraft mer DTU-beräknings kraft än en grundläggande tjänst nivå databas med 5 DTU: er.  

Om du vill få djupare insikt i resurs användningen för din arbets belastning använder du [frågor om prestanda](sql-database-query-performance.md) insikter för att:

- Identifiera de vanligaste frågorna efter processor/varaktighet/antal körningar som kan justeras för bättre prestanda. Till exempel kan en I/O-intensiv fråga ha nytta av [minnes optimerings tekniker](sql-database-in-memory.md) för att bättre kunna använda det tillgängliga minnet på en viss tjänst nivå och beräknings storlek.
- Öka detalj nivån i informationen om en fråga för att visa dess text och dess historik för resursanvändning.
- Få åtkomst till prestanda justerings rekommendationer som visar åtgärder som vidtagits av [SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Elastiska databas transaktions enheter (eDTU: er)

För SQL-databaser som alltid är tillgängliga, i stället för att tillhandahålla en dedikerad uppsättning resurser (DTU: er) som kanske inte alltid behövs, kan du placera dessa databaser i en [elastisk pool](sql-database-elastic-pool.md). Databaserna i en elastisk pool finns på en enda Azure SQL Database Server och delar en pool med resurser.

De delade resurserna i en elastisk pool mäts av elastiska databas transaktions enheter (eDTU: er). Elastiska pooler ger en enkel och kostnads effektiv lösning för att hantera prestanda mål för flera databaser som har mycket varierande och oförutsägbara användnings mönster. En elastisk pool garanterar att alla resurser inte kan konsumeras av en databas i poolen, samtidigt som varje databas i poolen alltid har en minsta mängd resurser som är tillgängliga.

En pool får ett angivet antal eDTU: er för ett angivet pris. I den elastiska poolen kan enskilda databaser skalas i den konfigurerade gränsen. En databas under en kraftigare belastning förbrukar mer eDTU: er för att möta efter frågan. Databaser under ljusare belastning förbrukar färre eDTU: er. Databaser utan belastning förbrukar ingen eDTU: er. Eftersom resurser etablerades för hela poolen, i stället för per databas, fören klar elastiska pooler dina hanterings uppgifter och ger en förutsägbar budget för poolen.

Du kan lägga till ytterligare eDTU: er i en befintlig pool utan avbrott i databasen och utan påverkan på databaserna i poolen. Om du inte längre behöver extra eDTU: er kan du när som helst ta bort dem från en befintlig pool. Du kan också lägga till databaser i eller ta bort databaser från en pool när som helst. Om du vill reservera eDTU: er för andra databaser begränsar du antalet eDTU: er som en databas kan använda under en kraftig belastning. Om en databas konsekvent underanvänder resurser, så flytta den ut från poolen och konfigurera den som en enkel databas med en förutsägbar mängd resurser som krävs.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Fastställa antalet DTU: er som krävs av en arbets belastning

Om du vill migrera en befintlig lokal eller SQL Server virtuell dators arbets belastning till Azure SQL Database använder du DTU- [kalkylatorn](https://dtucalculator.azurewebsites.net/) för att uppskatta antalet DTU: er som behövs. För en befintlig Azure SQL Database arbets belastning kan du använda [frågor om prestanda](sql-database-query-performance.md) för att förstå din databas resurs förbrukning (DTU: er) och få djupare insikter för att optimera din arbets belastning. Med [sys. DM _db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) Dynamic Management View (DMV) kan du Visa resursförbrukning för den senaste timmen. I vyn [sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) visas resursförbrukning för de senaste 14 dagarna, men med en lägre åter givning på fem minuters genomsnitt.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Arbets belastningar som drar nytta av en elastisk pool av resurser

Pooler lämpar sig väl för databaser med ett genomsnittligt medelvärde för resursanvändning och relativt ovanliga användnings toppar. SQL Database utvärderar automatiskt den historiska resurs användningen av databaser på en befintlig SQL Database-Server och rekommenderar lämplig konfiguration av poolen i Azure Portal. Mer information finns i [när bör du överväga en SQL Database elastisk pool?](sql-database-elastic-pool.md).

## <a name="frequently-asked-questions-faqs"></a>Vanliga frågor och svar

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Måste jag ta mitt program offline för att konvertera från en DTU-baserad tjänst nivå till en vCore tjänst nivå?

Nej. Du behöver inte göra programmet offline. De nya tjänst nivåerna erbjuder en enkel online-konverterings metod som liknar den befintliga processen för att uppgradera databaser från standard till Premium service-nivån och det andra sättet. Du kan starta konverteringen med hjälp av Azure Portal, PowerShell, Azure CLI, T-SQL eller REST API. Se [hantera enskilda databaser](sql-database-single-database-scale.md) och [Hantera elastiska pooler](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Kan jag konvertera en databas från en tjänst nivå i den vCore-baserade inköps modellen till en tjänst nivå i den DTU-baserade inköps modellen?

Ja, du kan enkelt konvertera databasen till alla prestanda mål som stöds med hjälp av Azure Portal, PowerShell, Azure CLI, T-SQL eller REST API. Se [hantera enskilda databaser](sql-database-single-database-scale.md) och [Hantera elastiska pooler](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om den vCore-baserade inköps modellen finns i [vCore-baserad inköps modell](sql-database-service-tiers-vcore.md).
- Mer information om den DTU-baserade inköps modellen finns i [DTU-baserad inköps modell](sql-database-service-tiers-dtu.md).
