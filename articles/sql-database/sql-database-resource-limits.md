---
title: Azure SQL Database-resurs begränsar översikt | Microsoft Docs
description: Den här sidan beskrivs några vanliga DTU-baserade gränserna för enskilda databaser i Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 3cf78369a7177eb3b2e384af0cc81563f6806137
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309990"
---
# <a name="overview-azure-sql-database-resource-limits"></a>Översikt över gränserna för Azure SQL Database 

Den här artikeln innehåller en översikt över Azure SQL Database-resursen begränsar och innehåller information om vad som händer när de gränserna nått eller överskridit.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Vad är det maximala antalet servrar och databaser?

| Maximal | Värde |
| :--- | :--- |
| Databaser per server | 5000 |
| Standardantal servrar per prenumeration i en region | 20 |
| Max antal servrar per prenumeration i en region | 200 |
|||

> [!NOTE]
> För att få mer serverkvoten än standardmängden som, kan du skicka en ny supportförfrågan i Azure portal för prenumerationen med typ av problem ”kvoten”.

> [!IMPORTANT]
> När antalet databaser som närmar sig gränsen per server, kan följande inträffa:
> - Ökad latens i kör frågor mot master-databasen.  Detta inkluderar vyer av användningsstatistik för resurs, till exempel sys.resource_stats.
> - Ökad latens i hanteringsåtgärder och återges portal åsikter som rör uppräkning databaser på servern.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Vad händer när databasen resource gränser?

### <a name="compute-dtus-and-edtus--vcores"></a>Beräkna (dtu: er och edtu: er / vCores)

När databasen beräkning användning (mätt av dtu: er och edtu: er eller vCores) är hög, svarstid ökar och kan även timeout. Under dessa förhållanden frågor i kö av tjänsten och tillhandahålls resurser för körning som resursen bli ledig.
När den påträffar hög beräkning användning inkluderar minskning alternativ:

- Öka prestandanivåerna för databasen eller elastisk pool för att tillhandahålla databasen med fler beräkningsresurser. Se [skala enskild databas](sql-database-single-database-scale.md) och [skala elastisk pool](sql-database-elastic-pool-scale.md).
- Optimera frågor för att minska resursanvändningen för varje fråga. Mer information finns i [frågan finjustera/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

När databasutrymme används når den maximala storleksgränsen, infogas i databasen och uppdateringar som ökar storleken på data misslyckas och klienter tar emot en [felmeddelande](sql-database-develop-error-messages.md). Databasen väljer och BORTTAGNINGAR fortsätter att fungera.

När den påträffar hög användningen inkluderar minskning alternativ:

- Öka max storleken på databasen eller elastisk pool eller Lägg till mer lagringsutrymme. Se [skala enskild databas](sql-database-single-database-scale.md) och [skala elastisk pool](sql-database-elastic-pool-scale.md).
- Om databasen är i en elastisk pool, kan sedan också databasen flyttas utanför poolen så att dess lagringsutrymme inte delas med andra databaser.

### <a name="sessions-and-workers-requests"></a>Sessioner och personer (antal begäranden) 

Det maximala antalet sessioner och personer bestäms av tjänstnivå och prestandanivå servicenivån (dtu: er och edtu: er). Nya begäranden avvisas när sessionen eller worker gränser och klienter får ett felmeddelande. När antalet anslutningar som är tillgängliga kan styras av programmet, är antalet samtidiga arbetare ofta svårare att uppskatta och styra. Detta gäller särskilt under belastningsperioder när gränserna för databasen har uppnåtts och arbetare växer på grund av längre frågor som körs. 

När den påträffar hög session eller worker användning inkluderar minskning alternativ:
- Öka servicenivå för nivå eller prestanda för databasen eller elastisk pool. Se [skala enskild databas](sql-database-single-database-scale.md) och [skala elastisk pool](sql-database-elastic-pool-scale.md).
- Optimera frågor för att minska resursanvändningen för varje fråga om orsaken till ökad worker belastningen beror på konkurrens om beräkningsresurser. Mer information finns i [frågan finjustera/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

När den påträffar hög session eller worker användning inkluderar minskning alternativ:
- Öka servicenivå för nivå eller prestanda på databasen. Se [skala enskild databas](sql-database-single-database-scale.md) och [skala elastisk pool](sql-database-elastic-pool-scale.md).
- Optimera frågor för att minska resursanvändningen för varje fråga om orsaken till ökad worker belastningen beror på konkurrens om beräkningsresurser. Mer information finns i [frågan finjustera/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Nästa steg

- Se [SQL-databasen med vanliga frågor om](sql-database-faq.md) för svar på vanliga frågor och svar.
- Information om allmänna Azure gränser finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md).
- Information om dtu: er och edtu: er finns [dtu: er och edtu: er](sql-database-what-is-a-dtu.md).
- Information om storleksgränser för tempdb finns https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
