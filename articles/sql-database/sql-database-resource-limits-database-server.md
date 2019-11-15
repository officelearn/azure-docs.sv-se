---
title: Server resurs gränser
description: Den här artikeln innehåller en översikt över gränsen för Azure SQL Database Server resurser för enskilda databaser och elastiska pooler. Den innehåller också information om vad som händer när dessa resurs gränser nåtts eller överskrids.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/14/2019
ms.openlocfilehash: 52e7a3408c231ba8a38fdc22c2fcac65ee26bb82
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082512"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>SQL Database resurs gränser för Azure SQL Database Server

Den här artikeln innehåller en översikt över SQL Database resurs gränser för en SQL Database Server som hanterar enskilda databaser och elastiska pooler. Den innehåller också information om vad som händer när dessa resurs gränser nåtts eller överskrids.

> [!NOTE]
> Begränsningar för hanterade instanser finns i [SQL Database resurs gränser för hanterade instanser](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Högsta antal resurs gränser

| Resource | Gräns |
| :--- | :--- |
| Databaser per server | 5000 |
| Standard antal servrar per prenumeration i valfri region | 20 |
| Maximalt antal servrar per prenumeration i valfri region | 200 |  
| Kvot för DTU/eDTU per server | 54,000 |  
| vCore-kvot per Server/instans | 540 |
| Högsta antal pooler per server | Begränsas av antalet DTU: er eller virtuella kärnor. Om varje pool till exempel är 1000 DTU: er, kan en server stödja 54-pooler.|
|||

> [!NOTE]
> För att få mer DTU-/eDTU kvot, vCore kvot eller fler servrar än standard beloppet kan en ny supportbegäran skickas i Azure Portal för prenumerationen med ärende typen "kvot". Kvoten DTU/eDTU och databas begränsning per server begränsar antalet elastiska pooler per server.
> [!IMPORTANT]
> När antalet databaser närmar sig gränsen per SQL Database Server kan följande inträffa:
>
> - Ökande svars tid för att köra frågor mot huvud databasen.  Detta inkluderar vyer av statistik över resursutnyttjande, till exempel sys. resource_stats.
> - Ökande svars tid i hanterings åtgärder och åter givning av Portal synvinklar som innefattar att räkna upp databaser på servern.

### <a name="storage-size"></a>Lagrings storlek

- För resurs lagrings storlekar för enskilda databaser kan du referera till antingen [DTU-baserade resurs gränser](sql-database-dtu-resource-limits-single-databases.md) eller [vCore resurs gränser](sql-database-vcore-resource-limits-single-databases.md) för lagrings storleks gränser per pris nivå.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Vad händer när databas resurs gränser nås

### <a name="compute-dtus-and-edtus--vcores"></a>Compute (DTU: er och eDTU: er/virtuella kärnor)

När databas beräknings användningen (uppmätt av DTU: er och eDTU: er, eller virtuella kärnor) blir hög, ökar svars tiden för frågan och kan till och med ta lång tid. Under dessa villkor kan frågor köas av tjänsten och de tillhandahålls resurser för körning när resursen blir kostnads fri.
När du räknar med hög beräknings användning är följande alternativ för minskning:

- Öka beräknings storleken för databasen eller den elastiska poolen för att tillhandahålla databasen med fler beräknings resurser. Se [skala resurser för enkel databas](sql-database-single-database-scale.md) och [skala elastiska pooler](sql-database-elastic-pool-scale.md).
- Optimera frågor för att minska resursutnyttjande för varje fråga. Mer information finns i [fråga om justering/tips](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

När databas utrymmet som används når den maximala storleks gränsen, infogas och uppdateras databasen som ökar data storleken och klienterna får ett [fel meddelande](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). Databasen MARKERAs och tas bort fortsätter att fungera.

När du ska räkna med hög användnings utrymme är alternativen för minskning:

- Öka den maximala storleken på databasen eller den elastiska poolen, eller Lägg till mer lagrings utrymme. Se [skala resurser för enkel databas](sql-database-single-database-scale.md) och [skala elastiska pooler](sql-database-elastic-pool-scale.md).
- Om databasen finns i en elastisk pool kan du eventuellt flytta databasen utanför poolen så att lagrings utrymmet inte delas med andra databaser.
- Krymp en databas för att frigöra outnyttjat utrymme. Mer information finns i [Hantera fil utrymme i Azure SQL Database](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessioner och arbetare (begär Anden)

Det maximala antalet sessioner och arbets tagare bestäms av tjänst nivån och beräknings storlek (DTU: er och eDTU: er). Nya begär Anden avvisas när sessioner eller arbets gränser nås och klienter får ett fel meddelande. Även om antalet tillgängliga anslutningar kan styras av programmet är antalet samtidiga arbetare ofta svårare att uppskatta och kontrol lera. Detta gäller särskilt under belastnings perioder när databas resurs gränserna har nåtts och arbets tagarna är igång på grund av längre körnings frågor.

När du räknar med hög arbets belastning eller arbets belastning, är alternativ för minskning följande:

- Öka tjänst nivån eller beräknings storleken för databasen eller den elastiska poolen. Se [skala resurser för enkel databas](sql-database-single-database-scale.md) och [skala elastiska pooler](sql-database-elastic-pool-scale.md).
- Optimering av frågor för att minska resursutnyttjande för varje fråga om orsaken till ökad arbets belastning beror på konkurrens för beräknings resurser. Mer information finns i [fråga om justering/tips](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="transaction-log-rate-governance"></a>Hastighets styrning för transaktions logg

Styrning av transaktions logg hastighet är en process i Azure SQL Database som används för att begränsa hög förbruknings frekvens för arbets belastningar som Mass infogning, SELECT INTO och indexe build. Dessa gränser spåras och framtvingas på den andra nivån till frekvensen för genereringen av logg poster, vilket begränsar data flödet, oavsett hur många IOs som kan utfärdas mot datafiler.  Taxan för transaktions logg skapande skalas linjärt upp till en punkt som är maskin vara beroende av, med den högsta logg frekvensen som tillåts som 96 MB/s med vCore inköps modell.

> [!NOTE]
> Faktiska fysiska IOs till transaktionsloggfiler är inte reglerade eller begränsade.

Logg taxan ställs in så att de kan uppnås och hanteras i flera olika scenarier, medan det övergripande systemet kan underhålla sin funktionalitet med minimerad påverkan på användar belastningen. Styrning av logg hastighet säkerställer att säkerhets kopior av transaktions loggar stannar inom publicerings service avtal.  Denna styrning förhindrar också en alltför lång efter släpning på sekundära repliker.

När logg poster skapas utvärderas och utvärderas varje åtgärd för om den ska fördröjas för att upprätthålla den högsta önskade logg frekvensen (MB/s per sekund). Fördröjningarna läggs inte till när logg posterna töms på lagringen, i takt med att logg takts styrningen används vid själva genereringen av logg hastighet.

De faktiska taxan för logg skapande som påförs vid körning kan också påverkas av feedback-mekanismer, vilket tillfälligt minskar de tillåtna logg priserna så att systemet kan stabiliseras. Hantering av logg fil utrymme, Undvik att köra i slut på logg utrymmes villkor och replikering av tillgänglighets grupper kan tillfälligt minska de totala system gränserna.

Trafikstyrningen för logg hastighets styrning sker via följande vänte typer (visas i [sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Wait-typ | Anteckningar |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Databas begränsning |
| POOL_LOG_RATE_GOVERNOR | Begränsning av pool |
| INSTANCE_LOG_RATE_GOVERNOR | Begränsning på instans nivå |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Feedback-kontroll, fysisk replikering för tillgänglighets grupp i Premium/Affärskritisk inte hålla sig uppdaterad |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Feedback-kontroll, begränsa priser för att undvika ett slut på logg utrymmes villkor |
|||

När du påträffar en logg hastighets gräns som hindrar önskad skalbarhet, bör du överväga följande alternativ:

- Skala upp till en större nivå för att få maximal logg hastighet på 96 MB/s.
- Om data som läses in är tillfälliga, d.v.s. mellanlagring av data i en ETL-process, kan den läsas in i tempdb (som är minimalt loggad).
- För analys scenarier läser du in i en klustrad columnstore-tabell. Detta minskar den nödvändiga logg frekvensen på grund av komprimering. Den här tekniken ökar processor användningen och gäller endast för data uppsättningar som drar nytta av klustrade columnstore-index.

## <a name="next-steps"></a>Nästa steg

- Information om allmänna Azure-gränser finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
- Information om DTU: er och eDTU: er finns i [DTU: er och eDTU: er](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Information om tempdb-storleks gränser finns [i tempdb i Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
