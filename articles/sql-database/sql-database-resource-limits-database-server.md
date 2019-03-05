---
title: Resursgränser för Azure SQL Database-server | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure SQL Database-server resursgränser för enskilda databaser och elastiska pooler. Den innehåller också information om vad som händer när de resursbegränsningar nått eller överskridit.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan,moslake,josack
manager: craigg
ms.date: 03/01/2019
ms.openlocfilehash: 00b20b3f144a2e98fb028e3db7c50af61330d721
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316463"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>SQL Database-resursgränser för Azure SQL Database-server

Den här artikeln innehåller en översikt över SQL Database-resursgränser för en SQL Database-server som hanterar enskilda databaser och elastiska pooler. Den innehåller också information om vad som händer när de resursbegränsningar nått eller överskridit.

> [!NOTE]
> Hanterade instanser gränser för finns i [SQL Database-resursgränser för hanterade instanser](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Maximal resursbegränsningar

| Resurs | Gräns |
| :--- | :--- |
| Databaser per server | 5000 |
| Standardvärdet för antal servrar per prenumeration i valfri region | 20 |
| Maxantal servrar per prenumeration i valfri region | 200 |  
| DTU / eDTU kvot per server | 54,000 |  
| vCore-kvot per server/instans | 540 |
| Högsta antal pooler per server | Begränsas av antalet dtu: er eller v-kärnor. Till exempel om varje pool har 1000 dtu: er, sedan en server kan stöda 54 pooler.|
||||

> [!NOTE]
> Om du vill ha mer DTU-kvot /eDTU, vCore kvot eller fler servrar än standardvärdet, kan du skicka en ny supportbegäran i Azure-portalen för prenumerationen med ärendetypen ”kvot”. DTU / eDTU-kvot och databas gränsen per server begränsar antalet elastiska pooler per server.
> [!IMPORTANT]
> När antalet databaser som närmar sig gränsen per SQL Database-server, kan följande inträffa:
> - Öka fördröjning vid körning av frågor mot huvuddatabasen.  Detta inkluderar vyer av Resursstatistik, till exempel sys.resource_stats.
> - Ökad svarstid i hanteringsåtgärder och rendering portal översiktsvyer som rör uppräkning av databaser på servern.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Vad händer när databasen resource har nått

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

## <a name="transaction-log-rate-governance"></a>Transaktionen Log Rate styrning 
Transaktionen log rate styrning är en process i Azure SQL-databas som används för att begränsa enligt hög datainmatningsfrekvensen för arbetsbelastningar som bulk insert SELECT INTO och indexversioner. Dessa gränser spåras och tillämpas på nivån under en sekund att frekvensen för generering av loggar poster, begränsande dataflöde, oavsett hur många IOs kan utfärdas mot datafiler.  Transaktionspriser log generation för närvarande skalas linjärt upp till en tidpunkt som beror på maskinvaran, log-maximala hastighet tillåts som 48 MB/s med vCore köpa modellen. 

> [!NOTE]
> De faktiska fysiska IOs till transaktionsloggfiler är inte regleras eller begränsas. 

Loggen är inställda så att de kan uppnås och varar i en mängd olika scenarier, medan den övergripande systemprestandan kan bibehålla sin funktionalitet minimerade påverkan på användarbelastningen. Log rate styrning garanterar att transaktionsloggen säkerhetskopior stannar inom publicerade återställningsmöjligheter serviceavtal.  Den här styrning förhindrar även att en orimlig eftersläpning på sekundära repliker.

När poster skapas bör utvärderas varje åtgärd och om den ska fördröjas för att upprätthålla en önskad Loggfilens största hastighet (MB/s per sekund) för att utvärdera. Förskjutningar läggs inte när loggposter rensade till lagring, i stället log rate styrning används under generering av loggar rate själva.

Den faktiska log-generationen priserna gäller vid körning kan också påverkas av återkopplingsmekanismerna, minskar de tillåtna log-priserna tillfälligt så att systemet kan stabilisera. Logghantering filen utrymme, undvika slut i loggen utrymme villkor och Tillgänglighetsgruppen replikeringsmekanismer kan tillfälligt försämra gränser för hela systemet. 

Log rate resursstyrning-Trafikstyrning är visas via följande typer av vänta (visas i den [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Vänta typ | Anteckningar |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Begränsning av databasen |
| POOL_LOG_RATE_GOVERNOR | Begränsa poolen |
| INSTANCE_LOG_RATE_GOVERNOR | Instans på begränsa |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Feedback-kontroll, tillgänglighet grupp fysiska replikering i Premium/affärskritisk inte hänger |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Feedback-kontroll, begränsa priserna för att undvika en out of utrymme loggvillkor |
||||

## <a name="next-steps"></a>Nästa steg

- Information om allmänna Azure-begränsningar finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
- Information om dtu: er och edtu: er finns i [dtu: er och edtu: er](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Information om storleksgränser för tempdb finns i [TempDB i Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
