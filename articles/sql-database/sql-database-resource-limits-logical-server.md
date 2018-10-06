---
title: Resursgränser för Azure SQL Database - logisk server | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure SQL Database-resursgränser för enskilda databaser och pooler databaser med elastiska pooler. Den innehåller också information om vad som händer när de resursbegränsningar nått eller överskridit.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan,moslake
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: b48c090cc67d4557140b5734f1a5e1f763b271ab
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829571"
---
# <a name="sql-database-resource-limits-for-single-and-pooled-databases-on-a-logical-server"></a>SQL Database-resursgränser för enkel och delade databaser på en logisk server 

Den här artikeln innehåller en översikt över SQL Database-resursgränser för enkel och delade databaser på en logisk server. Den innehåller också information om vad som händer när de resursbegränsningar nått eller överskridit.

> [!NOTE]
> Managed Instance gränser för finns i [SQL Database-resursgränser för hanterade instanser](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Maximal resursbegränsningar

| Resurs | Gräns |
| :--- | :--- |
| Databaser per server | 5000 |
| Standardvärdet för antal servrar per prenumeration i valfri region | 20 |
| Maxantal servrar per prenumeration i valfri region | 200 |  
| DTU / eDTU kvot per server | 54,000 |  
| vCore-kvot per server/instans | 540 |
| Högsta antal pooler per server | begränsas av antalet dtu: er eller v-kärnor |
||||

> [!NOTE]
> Om du vill ha mer DTU-kvot /eDTU, vCore kvot eller fler servrar än standardvärdet, kan du skicka en ny supportbegäran i Azure-portalen för prenumerationen med ärendetypen ”kvot”. DTU / eDTU-kvot och databas gränsen per server begränsar antalet elastiska pooler per server. 

> [!IMPORTANT]
> När antalet databaser som närmar sig gränsen per logisk server, kan följande inträffa:
> - Öka fördröjning vid körning av frågor mot huvuddatabasen.  Detta inkluderar vyer av Resursstatistik, till exempel sys.resource_stats.
> - Ökad svarstid i hanteringsåtgärder och rendering portal översiktsvyer som rör uppräkning av databaser på servern.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Vad händer när databasen resource har nått?

### <a name="compute-dtus-and-edtus--vcores"></a>Compute (dtu: er och edtu: er / virtuella kärnor)

När beräkning databasanvändning (mätt av dtu: er och edtu: er eller v-kärnor) blir hög svarstid ökar och kan även timeout. Under dessa förhållanden frågor köas av tjänsten och tillhandahålls resurser för körning som resurs bli ledig.
När den påträffar hög beräknings-användning, är minskning alternativen:

- Öka beräkningsstorleken för databas eller elastisk pool att tillhandahålla databasen med fler beräkningsresurser. Se [skala resurser för enkel databas](sql-database-single-database-scale.md) och [skala elastisk poolresurser](sql-database-elastic-pool-scale.md).
- Optimera frågor för att minska resursanvändningen för varje fråga. Mer information finns i [fråga justering/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

När databasutrymme används når den maximala storleksgränsen, databasen infogningar och uppdateringar som ökar storleken på data kan misslyckas och klienterna får en [felmeddelande](sql-database-develop-error-messages.md). Databasen väljer och BORTTAGNINGAR fortsätter att lyckas.

När den påträffar hög användningen är minskning alternativen:

- Ökar den maximala storleken på databasen eller den elastiska poolens eller Lägg till mer lagringsutrymme. Se [skala resurser för enkel databas](sql-database-single-database-scale.md) och [skala elastisk poolresurser](sql-database-elastic-pool-scale.md).
- Om databasen är i en elastisk pool, kan sedan också databasen flyttas utanför poolen så att dess lagringsutrymme inte delas med andra databaser.
- Komprimera en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessioner och arbetare (begäranden) 

Det maximala antalet sessioner och arbetare bestäms av tjänstnivån och beräkna storleken (dtu: er och edtu: er). Nya begäranden avvisas när sessionen eller arbetare har nått och klienterna får ett felmeddelande. Antalet anslutningar som är tillgängliga kan styras av programmet, är antalet samtidiga arbetare ofta svårare att beräkna och styr. Detta gäller särskilt under belastningsperioder när databasen resource har nått och arbetare växer på grund av längre körning av frågor. 

När den påträffar hög användning för sessionen eller arbete, är minskning alternativen:
- Öka tjänsten nivå eller beräkna storleken på databasen eller den elastiska poolen. Se [skala resurser för enkel databas](sql-database-single-database-scale.md) och [skala elastisk poolresurser](sql-database-elastic-pool-scale.md).
- Optimera frågor för att minska resursanvändningen för varje fråga om orsaken till ökad worker användningen är på grund av konkurrens om beräkningsresurser. Mer information finns i [fråga justering/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Nästa steg

- Se [SQL Database vanliga frågor och svar](sql-database-faq.md) svar på vanliga frågor och svar.
- Information om allmänna Azure-begränsningar finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
- Information om dtu: er och edtu: er finns i [dtu: er och edtu: er](sql-database-service-tiers.md#dtu-based-purchasing-model).
- Information om storleksgränser för tempdb finns i [TempDB i Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
