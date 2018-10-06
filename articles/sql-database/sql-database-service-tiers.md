---
title: Azure SQL Database köpa modeller | Microsoft Docs
description: Läs mer om den inköpsmodellen modeller som är tillgängliga databaser i Azure SQL Database-tjänsten.
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
ms.date: 10/02/2018
ms.openlocfilehash: 1bcaceb0f4d96b66d7fd25b1733b3f505316fe5a
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830563"
---
# <a name="azure-sql-database-purchasing-models"></a>Azure SQL Database köpa modeller

Azure SQL Database kan du enkelt köpa helt hanterad PaaS-databasmotor som passar ditt behov av prestanda och kostnader. Beroende på distributionsmodell för Azure SQL Database, kan du välja den inköpsmodellen som passar dina behov:

- [Logiska servrar](sql-database-logical-servers.md) i [Azure SQL Database](sql-database-technical-overview.md) finns i två inköpschef modeller för beräkning, lagring och IO-resurser: en [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och en [vCore-baserade inköpsmodell](sql-database-service-tiers-vcore.md). I den här inköpsmodellen kan du välja [enkla databaser](sql-database-single-databases-manage.md) eller [elastiska pooler](sql-database-elastic-pool.md).
- [Hanterade instanser](sql-database-managed-instance.md) i Azure SQL Database endast erbjudandet den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).

> [!IMPORTANT]
> [Hyperskala databaser (förhandsversion)](sql-database-service-tier-hyperscale.md) finns i offentlig förhandsversion endast för enskilda databaser med hjälp av vCore köpa modellen.

Följande tabell och diagrammet Jämför och kontrastera dessa två inköpschef modeller.

|**Inköpsmodell**|**Beskrivning**|**Bäst för**|
|---|---|---|
|DTU-baserad modell|Den här modellen är baserad på ett paketerat mått av beräkning, lagring och IO-resurser. Compute-storlekar uttrycks i Databastransaktionsenheter (dtu: er) för enskilda databaser och elastiska Databastransaktionsenheter (edtu: er) för elastiska pooler. Mer information om dtu: er och edtu: er finns i [vad är dtu: er och edtu: er](sql-database-service-tiers.md#dtu-based-purchasing-model)?|Bäst för kunder som vill ha enkel, förkonfigurerade alternativ.| 
|vCore-baserad modell|Den här modellen kan du välja oberoende beräknings- och lagringsresurser. Du kan också använda Azure Hybrid-förmånen för SQL Server för att få kostnadsbesparingar.|Bäst för kunder som värde flexibilitet, kontroll och transparens.|
||||  

![Prismodell](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model"></a>vCore-baserade inköpsmodellen 

En virtuell kärna representerar en logisk CPU med möjligheten att välja mellan av maskinvara och fysiska egenskaper av maskinvara (till exempel antal kärnor, minne, lagringsstorlek). Den vCore-baserade inköpsmodellen ger den flexibilitet, kontroll, transparens av enskild resursförbrukning och ett enkelt sätt att överföra lokala arbetsbelastningskrav till molnet. Den här modellen kan du välja beräkning, minne och lagring utifrån deras arbetsbelastningsbehov. I den vCore-baserade inköpsmodellen, kan du välja mellan [generella](sql-database-high-availability.md#standardgeneral-purpose-availability) och [affärskritisk](sql-database-high-availability.md#premiumbusiness-critical-availability) tjänstnivåer för både [enkla databaser](sql-database-single-database-scale.md), [ hanterade instanser](sql-database-managed-instance.md), och [elastiska pooler](sql-database-elastic-pool.md). För enskilda databaser du kan också välja den [hyperskala (förhandsversion)](sql-database-service-tier-hyperscale.md) tjänstnivå.

Den vCore-baserade inköpsmodellen kan du välja beräknings- och lagringsresurser, matcha lokala prestanda och optimera priset oberoende av varandra. I den vCore-baserade inköpsmodellen, kunder som betalar för:

- Compute (tjänstnivå + antalet virtuella kärnor och mängden minne + maskinvarusystem)
- Typ och mängden lagringsutrymme för data och loggfiler 
- Backup storage (RA-GRS) 

> [!IMPORTANT]
> Beräknings-, IOs, data och logglagring debiteras per databas eller elastisk pool. Lagring för säkerhetskopior debiteras per varje databas. Information av avgifter för hanterad instans i [Azure SQL Database Managed Instance](sql-database-managed-instance.md).
> **Region begränsningar:** vCore-baserade inköpsmodellen ännu inte finns tillgänglig i följande regioner: västra Europa, Frankrike, centrala, Storbritannien, södra, Storbritannien, västra och Australien, sydöstra Australien.

Om din databas eller elastisk pool förbrukar mer än 300 DTU-konvertering till vCore minska dina kostnader. Du kan konvertera med hjälp av ditt API föredrar eller med hjälp av Azure-portalen utan avbrott. Dock krävs inte konverteringen. Om den DTU-inköpsmodellen uppfyller dina krav på prestanda och företag, bör du fortsätta använda den. Om du vill konvertera från DTU-modellen till vCore-modellen kan du bör välja beräkningsstorleken med hjälp av följande tumregel: varje 100 dtu: er i Standard-nivån kräver minst 1 vCore i nivån generell användning; varje 125 DTU på premiumnivån kräver minst 1 vCore i nivån affärskritisk.

## <a name="dtu-based-purchasing-model"></a>DTU-baserade inköpsmodellen

Database Transaction Unit (DTU) representerar ett blandat mått på processor, minne, läser och skriver. DTU-baserade inköpsmodellen erbjuder en uppsättning förkonfigurerade paket av beräkningsresurser och inkluderat lagringsutrymme för att driva olika nivåer av programprestanda. Kunder som föredrar enkelheten med ett förkonfigurerade paket och fasta betalningar varje månad, kanske den DTU-baserade modellen passar deras behov. I DTU-baserade inköpsmodellen kunderna kan välja mellan **grundläggande**, **Standard**, och **Premium** tjänstnivåer för både [enkla databaser](sql-database-single-database-scale.md) och [elastiska pooler](sql-database-elastic-pool.md). Den här inköpsmodell är inte tillgänglig i [hanterade instanser](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Databastransaktionsenheter (dtu: er)

För en enskild Azure SQL-databas på en specifik compute storlek i en [tjänstnivå](sql-database-single-database-scale.md), Microsoft garanterar en viss nivå av resurser för den här databasen (oberoende av någon annan databas i Azure-molnet), vilket ger en förutsägbar nivån på prestanda. Mängden resurser beräknas som ett antal Databastransaktionsenheter eller dtu: er och är ett paketerat mått av beräkning, lagring och IO-resurser. Förhållandet mellan här resursernas fördelning har ursprungligen bestämts av en [OLTP-benchmarkarbetsbelastning](sql-database-benchmark-overview.md), utformade för att representera verkliga OLTP-arbetsbelastningar. När din arbetsbelastning överstiger mängden av någon av dessa resurser, är ditt dataflöde begränsade - resulterande långsammare prestanda och tidsgränser. De resurser som används av din arbetsbelastning inverkar inte resurserna som är tillgängliga för andra SQL-databaser i Azure-molnet och de resurser som används av andra arbetsbelastningar inverkar inte resurserna som är tillgängliga för din SQL-databas.

![avgränsningsruta](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu: er som är mest användbara för att förstå den relativa mängden resurser mellan tjänstnivåerna och Azure SQL-databaser på olika instansstorlekarna. Till exempel motsvarar fördubbling av dtu: erna genom att öka beräkningsstorleken för en databas fördubbling av uppsättningen resurser som är tillgängliga för databasen. En premium P11-databas med 1 750 DTU:er erbjuder exempelvis 350 gånger mer DTU-beräkningskraft än en Basic-databas med 5 DTU:er.  

Om du vill få bättre insikt i resursförbrukning (DTU) för din arbetsbelastning kan använda [Azure SQL Database Query Performance Insight](sql-database-query-performance.md) till:

- Identifiera de viktigaste frågorna med CPU/varaktighet/körning antalet som potentiellt kan anpassas för bättre prestanda. Till exempel en i/o-intensiva fråga kan dra nytta av användningen av [InMemory-optimeringstekniker](sql-database-in-memory.md) för att bättre utnyttja det tillgängliga minnet på en viss tjänstnivån och beräkna storleken.
- Granska nedåt i detaljerna för en fråga kan visa text och historik över resursanvändningen.
- Rekommendationer som visar åtgärder som utförs av för åtkomst av prestandajustering [SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Elastiska Databastransaktionsenheter (edtu: er)

Snarare än ger en dedikerad uppsättning resurser (dtu: er) som inte behövs kanske alltid för en SQL-databas som alltid är tillgänglig, kan du placera databaser i en [elastisk pool](sql-database-elastic-pool.md) på en SQL Database-server som delar en pool av resurser mellan dessa databaser. De delade resurserna i en elastisk pool mäts som elastiska Databastransaktionsenheter eller edtu: er. Elastiska pooler erbjuder en enkel kostnadseffektiv lösning för att hantera prestandamål för flera databaser med mycket varierande och oförutsägbara användningsmönster. En elastisk pool garanterar resurser inte kan användas av en databas i poolen, även att se till att varje databas i poolen alltid har en minimal mängd nödvändiga resurser som är tillgängliga. 

![Introduktion till SQL Database: eDTU:er efter nivå](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

En pool tilldelas ett bestämt antal edtu: er till ett fast pris. Inom den elastiska poolen har de enskilda databaserna flexibiliteten att skalas automatiskt inom konfigurerade parametrar. En databas under tyngre belastning förbrukar fler edtu: er för att möta efterfrågan. Databaser under ljusare laster förbrukar färre edtu: er. Databaser med utan belastning förbrukar inga edtu: er. Genom att etablera resurser för hela poolen, snarare än per databas, blir hanteringsuppgifter förenklade, vilket ger en förutsägbar budget för poolen.

Ytterligare eDTU:er kan läggas till i en befintlig pool utan avbrott för databasen och utan att databaserna i den elastiska poolen påverkas. Om eDTU:er sedan inte längre behövs, kan de tas bort från en befintlig pool när som helst. Du kan lägga till eller ta bort databaser i poolen eller begränsa antalet edtu: er en databas kan använda vid hög belastning för att reservera edtu: er för andra databaser. Om en databas förutsägbart underutnyttjar resurser, som du kan flytta från poolen och konfigurera den som en enkel databas med en förutsägbara mängd resurser som krävs.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Bestämma antalet dtu: er som krävs för en arbetsbelastning

Om du vill migrera en befintlig lokal VM-arbetsbelastning eller en VM-arbetsbelastning i SQL Server till Azure SQL Database kan du använda [DTU-kalkylatorn](http://dtucalculator.azurewebsites.net/) för att göra en ungefärlig uppskattning av hur många DTU:er du behöver. Du kan använda för en befintlig Azure SQL Database-arbetsbelastning [SQL Database Query Performance Insight](sql-database-query-performance.md) att förstå databasens resursförbrukning (dtu: er) för att få bättre insikt för att optimera din arbetsbelastning. Du kan också använda den [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV visa resursförbrukning för den senaste timmen. Du kan också katalogvyn [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) visar resursförbrukning för de senaste 14 dagarna, men med lägre tillförlitlighet med medelvärden på fem minuter.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Arbetsbelastningar som har nytta av en elastisk pool med resurser

Pooler lämpar sig för ett stort antal databaser med specifika användningsmönster. För en viss databas kännetecknas det här mönstret av ett medelvärde för låg användning med relativt ovanliga användningstoppar. SQL Database utvärderar automatiskt den historiska resursanvändningen för databaser på en befintlig SQL Database-server och rekommenderar lämplig poolkonfiguration på Azure Portal. Mer information finns i [När ska jag använda en elastisk pool?](sql-database-elastic-pool.md)

## <a name="next-steps"></a>Nästa steg

- VCore-baserade inköpsmodellen Se [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md)
- DTU-baserade inköpsmodellen Se [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md).
