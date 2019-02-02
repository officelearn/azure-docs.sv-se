---
title: Utökade händelser i SQL-databas | Microsoft Docs
description: Beskriver utökade händelser (XEvents) i Azure SQL-databas och hur händelsesessioner skilja sig från händelsesessioner i Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 5de5a58f936547d04fbce9eb84422652e23b82bd
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564891"
---
# <a name="extended-events-in-sql-database"></a>Utökade händelser i SQL-databas
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Det här avsnittet förklarar hur implementeringen av utökade händelser i Azure SQL Database är något annorlunda jämfört med utökade händelser i Microsoft SQL Server.

- SQL Database V12 fått funktionen utökade händelser i andra hälften av kalender 2015.
- SQL Server har haft utökade händelser sedan 2008.
- Funktionsuppsättningen i utökade händelser på SQL Database är en robust delmängd av funktionerna på SQL Server.

*XEvents* är en informell smeknamn som används ibland för ”utökade händelser” i bloggar och andra informell platser.

Mer information om utökade händelser för Azure SQL Database och Microsoft SQL Server finns på:

- [Snabbstart: Utökade händelser i SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Utökade händelser](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Förutsättningar

Det här avsnittet förutsätter att du redan har viss erfarenhet av:

- [Azure SQL Database-tjänsten](https://azure.microsoft.com/services/sql-database/).
- [Utökade händelser](https://msdn.microsoft.com/library/bb630282.aspx) i Microsoft SQL Server.

- Den största delen av vår dokumentation om utökade händelser gäller för både SQL Server och SQL-databas.

Tidigare exponeringen för följande objekt är användbart när du väljer filen händelse som den [target](#AzureXEventsTargets):

- [Azure Storage-tjänst](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Med Azure PowerShell med Azure Storage](../storage/common/storage-powershell-guide-full.md) -innehåller omfattande information om PowerShell och Azure Storage-tjänsten.

## <a name="code-samples"></a>Kodexempel

Relaterade avsnitt innehåller två kodexempel:


- [Ringbuffertens målkod för utökade händelser i SQL-databas](sql-database-xevent-code-ring-buffer.md)
    - Kort enkelt Transact-SQL-skript.
    - Vi betona i avsnittet kod exempel att när du är klar med ett Ringbuffert mål, bör lansering dess resurser genom att köra en alter släpper `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` instruktionen. Du kan senare lägga till en annan instans av Ringbuffert genom `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Händelsefilens målkod för utökade händelser i SQL-databas](sql-database-xevent-code-event-file.md)
    - Fas 1 är PowerShell för att skapa en Azure Storage-behållare.
    - Fas 2 är Transact-SQL som använder Azure Storage-behållare.

## <a name="transact-sql-differences"></a>Transact-SQL-skillnader


- När du kör den [skapa EVENT SESSION](https://msdn.microsoft.com/library/bb677289.aspx) kommandot på SQL Server du använder den **på SERVER** satsen. Men i SQL-databas som du använder den **på databasen** satsen i stället.


- Den **på databasen** satsen gäller även för de [ALTER EVENT SESSION](https://msdn.microsoft.com/library/bb630368.aspx) och [släppa EVENT SESSION](https://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL-kommandon.


- Ett bra tips är att inkludera alternativet event session av **STARTUP_STATE = ON** i din **skapa EVENT SESSION** eller **ALTER EVENT SESSION** instruktioner.
    - Den **= ON** värdet har stöd för en automatisk omstart efter en omkonfiguration av logiska databasen på grund av en redundansväxling.

## <a name="new-catalog-views"></a>De nya katalogvyerna tas

Utökade händelser-funktionen stöds av flera [catalog vyer](https://msdn.microsoft.com/library/ms174365.aspx). Katalogvyer berättar om *metadata eller definitioner* användarskapade event-sessioner i den aktuella databasen. Vyerna returnerar inte information om instanser av aktiva händelsesessioner.

| Namnet på<br/>Katalogvy | Beskrivning |
|:--- |:--- |
| **sys.database_event_session_actions** |Returnerar en rad för varje åtgärd på varje händelse i en händelsesession. |
| **sys.database_event_session_events** |Returnerar en rad för varje händelse i en händelsesession. |
| **sys.database_event_session_fields** |Returnerar en rad för varje anpassa kan kolumn som uttryckligen har ställts in på mål och händelser. |
| **sys.database_event_session_targets** |Returnerar en rad för varje händelsemål för en händelsesessionen. |
| **sys.database_event_sessions** |Returnerar en rad för varje händelsesessionen i SQL Database-databasen. |

I Microsoft SQL Server, liknande katalogvyer har namn som innehåller *.server\_*  i stället för *.database\_*. Namnmönstret liknar **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvshttpsmsdnmicrosoftcomlibraryms188754aspx"></a>Nya dynamiska hanteringsvyer [(DMV)](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database har [dynamiska hanteringsvyer (DMV)](https://msdn.microsoft.com/library/bb677293.aspx) som har stöd för utökade händelser. DMV: er berättar om *active* händelsesessioner.

| Namnet på DMV | Beskrivning |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Returnerar information om event session åtgärder. |
| **sys.dm_xe_database_session_events** |Returnerar information om Sessionshändelser. |
| **sys.dm_xe_database_session_object_columns** |Visar konfigurationsvärden för objekt som är bundna till en session. |
| **sys.dm_xe_database_session_targets** |Returnerar information om sessionen mål. |
| **sys.dm_xe_database_sessions** |Returnerar en rad för varje händelsesession som är begränsade till den aktuella databasen. |

I Microsoft SQL Server, liknande katalogvyer namnges utan den  *\_databasen* del av namnet, till exempel:

- **sys.dm_xe_sessions**, i stället för namn<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMV: er som är gemensamma för både
Det finns ytterligare DMV: er som är gemensamma för både Azure SQL Database och Microsoft SQL Server för utökade händelser:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Hitta de tillgängliga utökade händelser, åtgärder och mål

Du kan köra en enkel SQL **Välj** att erhålla en lista över tillgängliga händelser, åtgärder och mål.

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Mål för dina SQL Database event-sessioner

Här följer mål som kan samla in resultaten från din event-sessioner på SQL-databas:

- [Ringbuffertmål](https://msdn.microsoft.com/library/ff878182.aspx) -kort innehåller händelsedata i minnet.
- [Räknaren händelsemål](https://msdn.microsoft.com/library/ff878025.aspx) -räknar alla händelser som inträffar under en session för utökade händelser.
- [Händelsefilmål](https://msdn.microsoft.com/library/ff878115.aspx) -skriver fullständig buffertar till en Azure Storage-behållare.

Den [för Windows ETW (Event Tracing)](https://msdn.microsoft.com/library/ms751538.aspx) API: et är inte tillgänglig för utökade händelser i SQL-databas.

## <a name="restrictions"></a>Begränsningar

Det finns ett par olika säkerhetsrelaterade skillnader befitting molnmiljö för SQL-databas:

- Utökade händelser är grundade på en enda klient isoleringsmodell. En händelsesession i en databas kan inte komma åt data eller händelser från en annan databas.
- Du kan inte utfärda en **skapa EVENT SESSION** instruktionen i kontexten för den **master** databas.

## <a name="permission-model"></a>Behörighetsmodellen

Du måste ha **kontroll** behörighet på databasen för att utfärda en **skapa EVENT SESSION** instruktionen. Databasägaren (dbo) har **kontroll** behörighet.

### <a name="storage-container-authorizations"></a>Storage-behållare auktoriseringar

SAS-token som du genererar för Azure Storage-behållare måste ange **rwl** för behörigheterna. Den **rwl** värdet ger följande behörigheter:

- Läsa
- Skriva
- Visa lista

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Det finns scenarier där intensiv användning av utökade händelser kan samlas mer aktiva minne än vad som är felfri för hela systemet. Därför Azure SQL Database-systemet dynamiskt anger och justerar gränser för mängden active minne som kan visas genom en händelsesessionen. Många faktorer är med i beräkningen dynamiskt.

Om du får ett felmeddelande som anger ett maximalt minne har tvingats fram är vissa åtgärder du kan vidta:

- Kör färre samtidiga händelsesessioner.
- Via din **skapa** och **ALTER** -uttryck för händelsesessioner, minska mängden minne som du anger på den **MAX\_minne** satsen.

### <a name="network-latency"></a>Svarstid för nätverk

Den **händelsefil** mål kan uppleva nätverksfördröjning eller fel inträffade när spara data till Azure Storage-blobbar. Andra händelser i SQL-databasen kan vara fördröjda medan de väntar på nätverkskommunikation att slutföra. Den här fördröjningen kan sakta din arbetsbelastning.

- För att minska denna risk för prestanda, Undvik att ange den **EVENT_RETENTION_MODE** alternativet att **NO_EVENT_LOSS** i event session definitionerna.

## <a name="related-links"></a>Relaterade länkar

- [Med hjälp av Azure PowerShell med Azure Storage](../storage/common/storage-powershell-guide-full.md).
- [Azure Storage-cmdletar](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Med Azure PowerShell med Azure Storage](../storage/common/storage-powershell-guide-full.md) -innehåller omfattande information om PowerShell och Azure Storage-tjänsten.
- [Använda Blob storage från .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [Skapa autentiseringsuppgift (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [Skapa HÄNDELSESESSIONEN (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayias blogginlägg om utökade händelser i Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Azure *tjänstuppdateringar* webbsidan, smalare av parametern till Azure SQL Database:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Andra exempel ämnen i koden för utökade händelser finns på följande länkar. Du måste regelbundet kontrollera eventuella exemplet för att se om exemplet riktar sig mot Microsoft SQL Server och Azure SQL Database. Därefter kan du bestämma om mindre ändringar behövs för att köra exemplet.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
