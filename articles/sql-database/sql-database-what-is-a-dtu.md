---
title: 'SQL Database: Vad är en DTU? | Microsoft Docs'
description: Förstå vad en Azure SQL Database-transaktionsenhet är.
keywords: databasalternativ, databasprestanda
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 50b12de2400f0db90108e3eeb158357039ac92ec
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649830"
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Databasen Transaction Units (Dtu) och elastiska Datatransaktionsenheter (edtu: er)
Den här artikeln förklarar vad databastransaktionsenheter (DTU:er) och elastiska databastransaktionsenheter (eDTU:er) är och vad som händer när du når det högsta antalet tillåtna DTU:er eller eDTU:er. Specifika prisinformation finns [priser för Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="what-are-database-transaction-units-dtus"></a>Vad är Database Transaction Units (Dtu)?
För en enda Azure SQL-databas på specifika prestandanivåer inom en [tjänstnivån](sql-database-single-database-resources.md), Microsoft garanterar en viss nivå av resurser för den här databasen (oberoende av andra databas i Azure-molnet), vilket ger en förutsägbar nivå av prestanda. Mängden resurser beräknas som ett antal Database Transaction Units eller dtu: er och är ett anpassade mått för bearbetning, lagring och i/o-resurser. Förhållandet mellan resurserna har ursprungligen bestäms av ett [OLTP-arbetsbelastning benchmark](sql-database-benchmark-overview.md)och utformade är typiska för verkliga OLTP-arbetsbelastningar. När din arbetsbelastning överskrider mängden någon av dessa resurser, är din dataflödet begränsad – vilket resulterar i långsammare prestanda och timeout. De resurser som används av din arbetsbelastning påverkar inte resurserna som är tillgängliga för andra SQL-databaser i Azure-molnet och resurserna som används av andra arbetsbelastningar som inte påverkar resurserna som är tillgängliga för din SQL-databas.

![avgränsningsram](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu: er är mest användbara för att förstå den relativa mängden resurser mellan Azure SQL-databaser på olika prestandanivåer och servicenivåer. Till exempel dubblerade dtu: erna genom att öka prestandanivåerna för en databas är lika med dubblerade uppsättning resurser som är tillgängliga i den databasen. En premium P11-databas med 1 750 DTU:er erbjuder exempelvis 350 gånger mer DTU-beräkningskraft än en Basic-databas med 5 DTU:er.  

För att få en djupare inblick i resursanvändningen (DTU) för din arbetsbelastning, Använd [Azure SQL Database Query Performance Insight](sql-database-query-performance.md) till:

- Identifiera de vanligaste frågorna med CPU/varaktighet/körningar som potentiellt kan anpassas för bättre prestanda. Till exempel en i/o-intensiva fråga kan dra nytta av [i minnet optimeringstekniker](sql-database-in-memory.md) för att bättre utnyttja det tillgängliga minnet på en viss tjänst tjänstnivå och prestandanivå nivå.
- Detaljnivån i information om en fråga kan visa text och historik över resursutnyttjandet.
- Rekommendationer som visar åtgärder som utförs av för åtkomst av prestandajustering [SQL Database Advisor](sql-database-advisor.md).

Du kan ändra [DTU tjänstnivåer](sql-database-service-tiers-dtu.md) när som helst med minimal avbrottstid för ditt program (vanligtvis medelvärdet under fyra sekunder). För många företag och appar räcker det att kunna skapa databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. I det här scenariot använder du en elastisk pool med ett visst antal edtu: er som ska delas mellan flera databaser i poolen.

![Introduktion till SQL Database: DTU:er för enkla databaser efter nivå](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>Vad är elastiska Datatransaktionsenheter (edtu: er)?
Snarare än innehåller en särskild uppsättning resurser (dtu: er) som inte kanske alltid behövs för en SQL-databas som alltid är tillgänglig, kan du placera databaser i en [elastisk pool](sql-database-elastic-pool.md) på en SQL Database-server som delar en pool av resurser mellan dessa databaser. Delade resurser i en elastisk pool mäts av elastiska Datatransaktionsenheter eller edtu: er. Elastiska pooler ger en enkel och kostnadseffektiv lösning för att hantera prestandamål för flera databaser med vitt spridda och oförutsägbara användningsmönster. En elastisk pool garanterar resurser inte kan användas av en databas i poolen, medan säkerställt varje databas i poolen alltid har en minimal mängd nödvändiga resurser som är tillgängliga. 

![Introduktion till SQL Database: eDTU:er efter nivå](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

En pool ges ett angivet antal edtu: er för ett fast pris. Inom den elastiska poolen har de enskilda databaserna flexibiliteten att skalas automatiskt inom konfigurerade parametrar. En databas större belastning förbrukar mer edtu: er för att uppfylla begäran. Databaser under ljusare belastningar förbrukar mindre edtu: er. Databaser med någon belastning kommer att använda några edtu: er. Genom att etablera resurser för hela poolen är i stället per databas, hanteringsuppgifter förenklade, vilket ger en förutsägbar budget för poolen.

Ytterligare eDTU:er kan läggas till i en befintlig pool utan avbrott för databasen och utan att databaserna i den elastiska poolen påverkas. Om eDTU:er sedan inte längre behövs, kan de tas bort från en befintlig pool när som helst. Du kan lägga till eller ta bort databaser i poolen eller begränsa mängden edtu: er som en databas kan använda hårt belastad för att reservera edtu: er för andra databaser. Om en databas förutsägbart under-använder resurser, som du kan flytta från poolen och konfigurera den som en enskild databas med en förutsägbar mängd resurser som krävs.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Hur vet jag hur många DTU:er min arbetsbelastning behöver?
Om du vill migrera en befintlig lokal VM-arbetsbelastning eller en VM-arbetsbelastning i SQL Server till Azure SQL Database kan du använda [DTU-kalkylatorn](http://dtucalculator.azurewebsites.net/) för att göra en ungefärlig uppskattning av hur många DTU:er du behöver. Du kan använda för en befintlig Azure SQL Database-arbetsbelastning [SQL Database Query Performance Insight](sql-database-query-performance.md) att förstå din databas resursförbrukning (Dtu) för att få djupare insikter för att optimera din arbetsbelastning. Du kan också använda den [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV att visa resursanvändningen för den senaste timmen. Du kan också katalogvyn [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) visar resursanvändningen under de senaste 14 dagarna, men på en lägre återgivningen av fem minuter långa medelvärden.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Hur vet jag om jag kan dra nytta av en elastisk pool med resurser?
Pooler lämpar sig för ett stort antal databaser med specifika användningsmönster. Det här mönstret kännetecknas av ett lågt utnyttjande medelvärde med relativt sällan användning toppar för en viss databas. SQL Database utvärderar automatiskt den historiska resursanvändningen för databaser på en befintlig SQL Database-server och rekommenderar lämplig poolkonfiguration på Azure Portal. Mer information finns i [När ska jag använda en elastisk pool?](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Vad händer när jag träffar min maximala dtu: er?
Prestandanivåer kalibrera och regleras för att tillhandahålla de resurser som behövs för att köra din databas arbetsbelastning upp till maxvärdet för din valda nivån/prestandanivå. Om din arbetsbelastning är träffa en CPU/Data IO/logg-i/o-gränser, kommer att fortsätta att ta emot den maximala nivån för resurser som är tillåtna, men du får också sannolikt bättre frågan svarstider. Dessa gränser resulterar inte i fel, men i en långsammare arbetsbelastning, såvida inte fördröjningarna blir så allvarliga att tidsgränsen för frågorna överskrids. Om du når högsta tillåtna antal samtidiga användare sessioner/begäranden (trådar) visas explicit fel. Se [gränserna för Azure SQL Database]( sql-database-dtu-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached) information om gränserna inte relaterade till CPU, minne, data-i/o eller transaktionen logg-i/o.

## <a name="next-steps"></a>Nästa steg
* Se [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md) för information om dtu: er och edtu: er som är tillgängliga för enskilda databaser och elastiska pooler, samt gränser resurser förutom CPU, minne, data-i/o och transaktionen logga IO.
* Se [vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers-vcore.md) information om vCore-baserade resurs tilldelning och tjänsten nivåer. 
* Information som hjälper dig att avgöra din förbrukning (DTU:er) finns i [SQL Database Query Performance Insight](sql-database-query-performance.md).
* Information om metodiken bakom benchmark-arbetsbelastningen för OLTP som används för att fastställa DTU-kombinationen finns i [Översikt över SQL Database-benchmark](sql-database-benchmark-overview.md).
