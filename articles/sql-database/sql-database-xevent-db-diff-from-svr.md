---
title: Utökade händelser
description: Beskriver utökade händelser (XEvents) i Azure SQL Database och hur händelsesessioner skiljer sig något från händelsesessioner i Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: cb4eb4474ad074a3e69dc146c97b48d54343595b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213946"
---
# <a name="extended-events-in-sql-database"></a>Utökade händelser i SQL Database
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

I det här avsnittet beskrivs hur implementeringen av utökade händelser i Azure SQL Database skiljer sig något från utökade händelser i Microsoft SQL Server.

- SQL Database V12 fick funktionen utökade händelser under andra halvan av kalendern 2015.
- SQL Server har haft utökade händelser sedan 2008.
- Funktionsuppsättningen med utökade händelser i SQL Database är en robust delmängd av funktionerna på SQL Server.

*XEvents* är ett informellt smeknamn som ibland används för "utökade evenemang" i bloggar och andra informella platser.

Ytterligare information om utökade händelser för Azure SQL Database och Microsoft SQL Server finns på:

- [Snabbstart: Utökade händelser i SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Utökade händelser](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Krav

Det här avsnittet förutsätter att du redan har viss kunskap om:

- [Azure SQL Database-tjänst](https://azure.microsoft.com/services/sql-database/).
- [Utökade händelser](https://msdn.microsoft.com/library/bb630282.aspx) i Microsoft SQL Server.

- Huvuddelen av vår dokumentation om utökade händelser gäller både SQL Server och SQL Database.

Före exponering för följande objekt är användbart när du väljer händelsefilen som [mål:](#AzureXEventsTargets)

- [Azure Storage-tjänst](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Använda Azure PowerShell med Azure Storage](../storage/common/storage-powershell-guide-full.md) – Innehåller omfattande information om PowerShell och Azure Storage-tjänsten.

## <a name="code-samples"></a>Kodexempel

Relaterade ämnen ger två kodexempel:


- [Ring buffertmålskod för utökade händelser i SQL Database](sql-database-xevent-code-ring-buffer.md)
    - Kort enkel Transact-SQL-skript.
    - Vi betonar i kodexemplet att när du är klar med ett ringbuffertmål `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` bör du frigöra dess resurser genom att köra en ändrings-drop-sats. Senare kan du lägga till `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`en annan instans av Ring Buffert av .


- [Målkod för händelsefil för utökade händelser i SQL Database](sql-database-xevent-code-event-file.md)
    - Fas 1 är PowerShell för att skapa en Azure Storage-behållare.
    - Fas 2 är Transact-SQL som använder Azure Storage-behållaren.

## <a name="transact-sql-differences"></a>Transact-SQL-skillnader


- När du kör kommandot [SKAPA HÄNDELSESESSION](https://msdn.microsoft.com/library/bb677289.aspx) PÅ SQL Server använder du **ON SERVER-satsen.** Men på SQL Database använder du **ON DATABASE-satsen** i stället.


- **ON** DATABASE-satsen gäller även för kommandona [ALTER EVENT SESSION](https://msdn.microsoft.com/library/bb630368.aspx) och DROP EVENT [SESSION](https://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL.


- En bästa praxis är att inkludera händelsesessionsalternativet **STARTUP_STATE = PÅ** i dina SKAPA **HÄNDELSESESSIONs-** eller ALTER EVENT SESSION-satser. **ALTER EVENT SESSION**
    - Värdet **= ON** stöder en automatisk omstart efter en omkonfigurering av den logiska databasen på grund av en redundansväxling.

## <a name="new-catalog-views"></a>Nya katalogvyer

Funktionen utökade händelser stöds av flera [katalogvyer](https://msdn.microsoft.com/library/ms174365.aspx). Katalogvyer berättar om *metadata eller definitioner* av användarskapade händelsesessioner i den aktuella databasen. Vyerna returnerar inte information om instanser av aktiva händelsesessioner.

| Namn på<br/>katalogvy | Beskrivning |
|:--- |:--- |
| **sys.database_event_session_actions** |Returnerar en rad för varje åtgärd för varje händelse i en händelsesession. |
| **sys.database_event_session_events** |Returnerar en rad för varje händelse i en händelsesession. |
| **sys.database_event_session_fields** |Returnerar en rad för varje anpassningsduglig kolumn som uttryckligen angavs för händelser och mål. |
| **sys.database_event_session_targets** |Returnerar en rad för varje händelsemål för en händelsesession. |
| **sys.database_event_sessions** |Returnerar en rad för varje händelsesession i SQL Database-databasen. |

I Microsoft SQL Server har liknande katalogvyer namn som innehåller *.server\_ * i stället för *.database\_*. Namnmönstret är som **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvs"></a>Nya dynamiska [hanteringsvyer (DMVs)](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database har [dynamiska hanteringsvyer (DMV: er)](https://msdn.microsoft.com/library/bb677293.aspx) som stöder utökade händelser. DMVs berättar om *aktiva* händelsesessioner.

| Namn på DMV | Beskrivning |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Returnerar information om händelsesessionsåtgärder. |
| **sys.dm_xe_database_session_events** |Returnerar information om sessionshändelser. |
| **sys.dm_xe_database_session_object_columns** |Visar konfigurationsvärdena för objekt som är bundna till en session. |
| **sys.dm_xe_database_session_targets** |Returnerar information om sessionsmål. |
| **sys.dm_xe_database_sessions** |Returnerar en rad för varje händelsesession som är begränsad till den aktuella databasen. |

I Microsoft SQL Server namnges liknande katalogvyer utan * \_databasdelen* av namnet, till exempel:

- **sys.dm_xe_sessions**i stället för namn<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DTV-apparater som är gemensamma för både
För utökade händelser finns det ytterligare DVS som är gemensamma för både Azure SQL Database och Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Hitta tillgängliga utökade händelser, åtgärder och mål

Du kan köra en enkel SQL **SELECT** för att få en lista över tillgängliga händelser, åtgärder och mål.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```


<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>Mål för händelsesessionerna i SQL Database

Här är mål som kan samla in resultat från dina händelsesessioner i SQL Database:

- [Ring buffertmål](https://msdn.microsoft.com/library/ff878182.aspx) - Innehåller kortfattat händelsedata i minnet.
- [Mål för händelseräknare](https://msdn.microsoft.com/library/ff878025.aspx) – räknar alla händelser som inträffar under en utökad händelsesession.
- [Event File target](https://msdn.microsoft.com/library/ff878115.aspx) - Skriver fullständiga buffertar till en Azure Storage-behållare.

[ETW-API:etW-API:etW (Event Tracing for Windows)](https://msdn.microsoft.com/library/ms751538.aspx) är inte tillgängligt för utökade händelser i SQL Database.

## <a name="restrictions"></a>Begränsningar

Det finns ett par säkerhetsrelaterade skillnader som anstår molnmiljön i SQL Database:

- Utökade händelser bygger på isoleringsmodellen för en klient. En händelsesession i en databas kan inte komma åt data eller händelser från en annan databas.
- Du kan inte utfärda en **CREATE EVENT SESSION-sats** i **huvuddatabasens kontext.**

## <a name="permission-model"></a>Behörighetsmodell

Du måste ha **behörigheten Kontroll** i databasen för att kunna utfärda en **CREATE EVENT SESSION-sats.** Databasägaren (dbo) har **behörigheten Kontroll.**

### <a name="storage-container-authorizations"></a>Tillstånd för lagringsbehållare

DEN SAS-token som du genererar för din Azure Storage-behållare måste ange **rwl** för behörigheterna. **Rwl-värdet** ger följande behörigheter:

- Läsa
- Skriva
- Visa lista

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Det finns scenarier där intensiv användning av utökade händelser kan ackumulera mer aktivt minne än vad som är felfritt för det övergripande systemet. Därför azure SQL Database-systemet dynamiskt anger och justerar gränser för mängden aktivt minne som kan ackumuleras av en händelsesession. Många faktorer går in i den dynamiska beräkningen.

Om du får ett felmeddelande om att ett maximalt minne har tillämpats kan du vidta några korrigerande åtgärder:

- Kör färre samtidiga händelsesessioner.
- Genom dina CREATE- och **ALTER-satser** för händelsesessioner minskar du mängden minne som du anger på **ALTER** **MAX\_MEMORY-satsen.**

### <a name="network-latency"></a>Svarstid för nätverk

**Event File-målet** kan uppleva nätverksfördröjning eller fel när data sparas i Azure Storage-blobbar. Andra händelser i SQL Database kan fördröjas medan de väntar på att nätverkskommunikationen ska slutföras. Den här fördröjningen kan minska din arbetsbelastning.

- Undvik att ange **alternativet EVENT_RETENTION_MODE** för att **NO_EVENT_LOSS** i händelsesessionsdefinitionerna för att minska den här prestandarisken.

## <a name="related-links"></a>Relaterade länkar

- [Använda Azure PowerShell med Azure Storage](../storage/common/storage-powershell-guide-full.md).
- [Cmdlets för Azure-lagring](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Använda Azure PowerShell med Azure Storage](../storage/common/storage-powershell-guide-full.md) – Innehåller omfattande information om PowerShell och Azure Storage-tjänsten.
- [Så här använder du Blob-lagring från .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [Skapa autentiseringsuppgift (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [SKAPA HÄNDELSESESSION (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayias blogginlägg om utökade händelser i Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Webbsidan Azure *Service Updates,* som begränsas av parametern till Azure SQL Database:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Andra kodexempelavsnitt för utökade händelser finns på följande länkar. Du måste dock kontrollera ett exempel rutinmässigt för att se om exemplet är inriktat på Microsoft SQL Server jämfört med Azure SQL Database. Sedan kan du bestämma om mindre ändringar behövs för att köra exemplet.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
