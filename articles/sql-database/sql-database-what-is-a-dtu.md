---
title: 'SQL Database: Vad är en DTU? | Microsoft Docs'
description: Förstå vad en Azure SQL Database-transaktionsenhet är.
keywords: databasalternativ, databasprestanda
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 22337e412661172475a05f6fec31ae03683be988
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Databasen Transaction Units (Dtu) och elastiska Datatransaktionsenheter (edtu: er)
Den här artikeln förklarar vad databastransaktionsenheter (DTU:er) och elastiska databastransaktionsenheter (eDTU:er) är och vad som händer när du når det högsta antalet tillåtna DTU:er eller eDTU:er. Specifika prisinformation finns [priser för Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="what-are-database-transaction-units-dtus"></a>Vad är Database Transaction Units (Dtu)?
För en enda Azure SQL-databas på specifika prestandanivåer inom en [tjänstnivån](sql-database-single-database-resources.md), Microsoft garanterar en viss nivå av resurser för den här databasen (oberoende av andra databas i Azure-molnet), och tillhandahålla en förutsägbar nivå av prestanda. Den här mängden resurser beräknas som ett antal Database Transaction Units eller dtu: er och är ett anpassade mått för bearbetning, lagring och i/o-resurser. Förhållandet mellan resurserna har ursprungligen bestäms av ett [OLTP-arbetsbelastning benchmark](sql-database-benchmark-overview.md) utformats är typiska för verkliga OLTP-arbetsbelastningar. När din arbetsbelastning överskrider mängden någon av dessa resurser, är din dataflödet begränsad – vilket resulterar i långsammare prestanda och timeout. De resurser som används av din arbetsbelastning påverkar inte resurserna som är tillgängliga för andra SQL-databaser i Azure-molnet och resurs som används av andra arbetsbelastningar som inte påverkar resurserna som är tillgängliga för din SQL-databas.

![avgränsningsram](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu: er är mest användbara för att förstå den relativa mängden resurser mellan Azure SQL-databaser på olika prestandanivåer och servicenivåer. Till exempel dubblerade dtu: erna genom att öka prestandanivåerna för en databas är lika med dubblerade uppsättning resursen är tillgänglig i den databasen. En premium P11-databas med 1 750 DTU:er erbjuder exempelvis 350 gånger mer DTU-beräkningskraft än en Basic-databas med 5 DTU:er.  

För att få en djupare inblick i resursanvändningen (DTU) för din arbetsbelastning, Använd [Azure SQL Database Query Performance Insight](sql-database-query-performance.md) till:

- Identifiera de vanligaste frågorna med CPU/varaktighet/körningar som potentiellt kan anpassas för bättre prestanda. Till exempel en i/o-intensiva fråga kan dra nytta av [i minnet optimeringstekniker](sql-database-in-memory.md) för att bättre utnyttja det tillgängliga minnet på en viss tjänst tjänstnivå och prestandanivå nivå.
- Detaljnivån i information om en fråga kan visa text och historik över resursutnyttjandet.
- Rekommendationer som visar åtgärder som utförs av för åtkomst av prestandajustering [SQL Database Advisor](sql-database-advisor.md).

Du kan [byta tjänstnivå](sql-database-service-tiers.md) när som helst med minimalt avbrott för dina program (normalt mindre än fyra sekunder). För många företag och appar räcker det att kunna skapa databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. I det här scenariot använder du en elastisk pool med ett visst antal edtu: er som ska delas mellan flera databaser i poolen.

![Introduktion till SQL Database: DTU:er för enkla databaser efter nivå](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>Vad är elastiska Datatransaktionsenheter (edtu: er)?
Snarare än innehåller en särskild uppsättning resurser (Dtu) till en SQL-databas som alltid är tillgänglig oavsett om de inte behövs kan du placera databaser i en [elastisk pool](sql-database-elastic-pool.md) på en SQL Database-server som delar en pool av resurser bland dessa databas. Delade resurser i en elastisk pool som mäts med elastiska Datatransaktionsenheter eller edtu: er. Elastiska pooler erbjuder en enkel kostnadseffektiv lösning för att hantera prestandamål för flera databaser med mycket varierande och oförutsägbara användningsmönster. I en elastisk pool, kan du garantera att ingen en databas använder alla resurser i poolen och även som en minimal mängd resurser alltid är tillgängligt för en databas i en elastisk pool. 

![Introduktion till SQL Database: eDTU:er efter nivå](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

En pool tilldelas ett bestämt antal eDTU:er till ett fast pris. Inom den elastiska poolen har de enskilda databaserna flexibiliteten att skalas automatiskt inom konfigurerade parametrar. En databas under hög belastning kan använda sig av fler eDTU:er för att möta efterfrågan, medan databaser under lätta laster förbrukar färre, upp till databaser utan belastning som inte använder några eDTU:er. Genom att etablera resurser för hela poolen, istället för per databas, blir hanteringsuppgifter förenklade och du får en förutsägbar budget för poolen.

Ytterligare eDTU:er kan läggas till i en befintlig pool utan avbrott för databasen och utan att databaserna i den elastiska poolen påverkas. Om eDTU:er sedan inte längre behövs, kan de tas bort från en befintlig pool när som helst. Du kan lägga till eller ta bort databaser i poolen, eller begränsa antalet eDTU:er som en databas kan använda vid hög belastning för att reservera eDTU:er för andra databaser. Om en databas förutsägbart underutnyttjar resurser, kan du flytta ut den från poolen och konfigurera den som en enda databas med den förutsägbara mängd resurser som det kräver.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Hur vet jag hur många DTU:er min arbetsbelastning behöver?
Om du vill migrera en befintlig lokal VM-arbetsbelastning eller en VM-arbetsbelastning i SQL Server till Azure SQL Database kan du använda [DTU-kalkylatorn](http://dtucalculator.azurewebsites.net/) för att göra en ungefärlig uppskattning av hur många DTU:er du behöver. För en befintlig Azure SQL Database-arbetsbelastning kan du använda [SQL Database Query Performance Insight](sql-database-query-performance.md) för att förstå databasens resursförbrukning (DTU:er) och få djupare insikt i hur du kan optimera arbetsbelastningen. Du kan också använda [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)-DMV:n om du vill hämta information om resursförbrukningen för den senaste timmen. Du kan också fråga katalogvyn [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) om du vill få samma data för de senaste 14 dagarna, dock med lägre tillförlitlighet med medelvärden på fem minuter.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Hur vet jag om jag kan dra nytta av en elastisk pool med resurser?
Pooler lämpar sig för ett stort antal databaser med specifika användningsmönster. För en viss databas kännetecknas det här mönstret av låg genomsnittlig användning med relativt ovanliga användningstoppar. SQL Database utvärderar automatiskt den historiska resursanvändningen för databaser på en befintlig SQL Database-server och rekommenderar lämplig poolkonfiguration på Azure Portal. Mer information finns i [När ska jag använda en elastisk pool?](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Vad händer när jag träffar min maximala dtu: er?
Prestandanivåerna kalibreras och regleras för att tillhandahålla nödvändiga resurser så att din databasarbetsbelastning kan köra upp till den högsta gränsen som tillåts för din valda tjänstnivå/prestandanivå. Om din arbetsbelastning når någon av gränserna för processor, data-IO eller logg-IO tillhandahålls fortfarande resurser på den högsta tillåtna nivån, men du kan märka att svarstiderna för dina frågor blir längre. Dessa gränser resulterar inte i fel, men i en långsammare arbetsbelastning, såvida inte fördröjningarna blir så allvarliga att tidsgränsen för frågorna överskrids. Om du når gränsen för det högsta antalet tillåtna samtidiga användarsessioner/begäranden (arbetstrådar) returneras explicita fel. Se [gränserna för Azure SQL Database]( sql-database-dtu-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached) för information om gränsen för resurser än CPU, minne, data-i/o och transaktionen logga IO.

## <a name="next-steps"></a>Nästa steg
* Se [tjänstnivån](sql-database-service-tiers.md) för information om dtu: er och edtu: er som är tillgängliga för enskilda databaser och elastiska pooler, samt gränser resurser förutom CPU, minne, data-i/o och transaktionen logga IO.
* Information som hjälper dig att avgöra din förbrukning (DTU:er) finns i [SQL Database Query Performance Insight](sql-database-query-performance.md).
* Information om metodiken bakom benchmark-arbetsbelastningen för OLTP som används för att fastställa DTU-kombinationen finns i [Översikt över SQL Database-benchmark](sql-database-benchmark-overview.md).
