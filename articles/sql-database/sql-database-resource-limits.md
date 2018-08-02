---
title: Azure SQL Database-resurs begränsar översikt | Microsoft Docs
description: Den här sidan beskriver några vanliga DTU-baserade resursbegränsningar för enskilda databaser i Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 6f6fa1ebc086530f138d32ee5a9c799b5bfbbdeb
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412118"
---
# <a name="overview-azure-sql-database-resource-limits"></a>Översikt över Azure SQL Database-resursgränser 

Den här artikeln innehåller en översikt över Azure SQL Database-resursen begränsar och ger information om vad som händer när de resursbegränsningar nått eller överskridit.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Vad är det maximala antalet servrar och databaser?

| Maximal | Värde |
| :--- | :--- |
| Databaser per server | 5000 |
| Standardvärdet för antal servrar per prenumeration i valfri region | 20 |
| Maxantal servrar per prenumeration i valfri region | 200 |
| DTU / eDTU kvot per server | 54,000 |
| vCore-kvot per server | 540 |
| Högsta antal pooler per server | begränsas av antalet dtu: er eller v-kärnor |
|||

> [!NOTE]
> Om du vill ha mer DTU-kvot /eDTU, vCore kvot eller fler servrar än standardvärdet, kan du skicka en ny supportbegäran i Azure-portalen för prenumerationen med ärendetypen ”kvot”. DTU / eDTU-kvot och databas gränsen per server begränsar antalet elastiska pooler per server. 

> [!IMPORTANT]
> När antalet databaser som närmar sig gränsen per server, kan följande inträffa:
> - Öka fördröjning vid körning av frågor mot huvuddatabasen.  Detta inkluderar vyer av Resursstatistik, till exempel sys.resource_stats.
> - Ökad svarstid i hanteringsåtgärder och rendering portal översiktsvyer som rör uppräkning av databaser på servern.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Vad händer när databasen resource har nått?

### <a name="compute-dtus-and-edtus--vcores"></a>Compute (dtu: er och edtu: er / virtuella kärnor)

När beräkning databasanvändning (mätt av dtu: er och edtu: er eller v-kärnor) blir hög svarstid ökar och kan även timeout. Under dessa förhållanden frågor köas av tjänsten och tillhandahålls resurser för körning som resurs bli ledig.
När den påträffar hög beräknings-användning, är minskning alternativen:

- Ökning av prestandanivån för databasen eller elastisk pool för att tillhandahålla databasen med mer beräkningsresurser. Se [skala resurser för enkel databas](sql-database-single-database-scale.md) och [skala elastisk poolresurser](sql-database-elastic-pool-scale.md).
- Optimera frågor för att minska resursanvändningen för varje fråga. Mer information finns i [fråga justering/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

När databasutrymme används når den maximala storleksgränsen, databasen infogningar och uppdateringar som ökar storleken på data kan misslyckas och klienterna får en [felmeddelande](sql-database-develop-error-messages.md). Databasen väljer och BORTTAGNINGAR fortsätter att lyckas.

När den påträffar hög användningen är minskning alternativen:

- Ökar den maximala storleken på databasen eller den elastiska poolens eller Lägg till mer lagringsutrymme. Se [skala resurser för enkel databas](sql-database-single-database-scale.md) och [skala elastisk poolresurser](sql-database-elastic-pool-scale.md).
- Om databasen är i en elastisk pool, kan sedan också databasen flyttas utanför poolen så att dess lagringsutrymme inte delas med andra databaser.
- Komprimera en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessioner och arbetare (begäranden) 

Det maximala antalet sessioner och arbetare bestäms av tjänstnivå och prestandanivå servicenivå (dtu: er och edtu: er). Nya begäranden avvisas när sessionen eller arbetare har nått och klienterna får ett felmeddelande. Antalet anslutningar som är tillgängliga kan styras av programmet, är antalet samtidiga arbetare ofta svårare att beräkna och styr. Detta gäller särskilt under belastningsperioder när databasen resource har nått och arbetare växer på grund av längre körning av frågor. 

När den påträffar hög användning för sessionen eller arbete, är minskning alternativen:
- Öka servicenivå för tjänstenivå eller prestandanivå för databasen eller den elastiska poolen. Se [skala resurser för enkel databas](sql-database-single-database-scale.md) och [skala elastisk poolresurser](sql-database-elastic-pool-scale.md).
- Optimera frågor för att minska resursanvändningen för varje fråga om orsaken till ökad worker användningen är på grund av konkurrens om beräkningsresurser. Mer information finns i [fråga justering/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Nästa steg

- Se [SQL Database vanliga frågor och svar](sql-database-faq.md) svar på vanliga frågor och svar.
- Information om allmänna Azure-begränsningar finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
- Information om dtu: er och edtu: er finns i [dtu: er och edtu: er](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).
- Information om storleksgränser för tempdb finns i https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
