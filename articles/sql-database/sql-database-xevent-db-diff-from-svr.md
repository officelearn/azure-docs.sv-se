---
title: "Utökade händelser i SQL-databas | Microsoft Docs"
description: "Beskriver utökade händelser (XEvents) i Azure SQL-databas och hur händelsesessioner skiljer sig från händelsesessioner i Microsoft SQL Server."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: 3b28cf15-f820-4b3c-8310-908d6d5b9d0c
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genemi
ms.openlocfilehash: f4e41d340b38a5f29387d75b8f65b68c5fb31eb9
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="extended-events-in-sql-database"></a>Utökade händelser i SQL-databas
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Det här avsnittet beskrivs hur implementeringen av utökade händelser i Azure SQL Database är något annorlunda jämfört med utökade händelser i Microsoft SQL Server.

- SQL Database V12 med funktionen för utökade händelser i andra hälften av kalender 2015.
- SQL Server har haft utökade händelser sedan 2008.
- Funktionsuppsättningen för utökade händelser på SQL-databas är en robust delmängd av funktionerna i SQL Server.

*XEvents* är en informell smeknamn som används ibland för ”utökade händelser” i bloggar och andra informell platser.

Mer information om utökade händelser för Azure SQL Database och Microsoft SQL Server finns på:

- [Snabbstart: Utökade händelser i SQL Server](http://msdn.microsoft.com/library/mt733217.aspx)
- [Utökade händelser](http://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Krav

Det här avsnittet förutsätter att du redan har viss erfarenhet av:

- [Azure SQL Database-tjänsten](https://azure.microsoft.com/services/sql-database/).
- [Utökade händelser](http://msdn.microsoft.com/library/bb630282.aspx) i Microsoft SQL Server.

- Den största delen av vår dokumentation om utökade händelser gäller både SQL Server och SQL-databas.

Tidigare exponering för följande objekt är användbart när du väljer filen händelsen som den [mål](#AzureXEventsTargets):

- [Azure Storage-tjänst](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Använda Azure PowerShell med Azure Storage](../storage/common/storage-powershell-guide-full.md) -innehåller omfattande information om PowerShell och Azure Storage-tjänsten.

## <a name="code-samples"></a>Kodexempel

Relaterade avsnitt innehåller två kodexempel:


- [Ring buffert mål koden för utökade händelser i SQL-databas](sql-database-xevent-code-ring-buffer.md)
    - Kort enkelt Transact-SQL-skript.
    - Vi betona i avsnittet kod exempel att du ska släpper dess resurser genom att köra alter släpp när du är klar med ett ringbufferten `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` instruktionen. Du kan senare lägga till en annan instans av ringbufferten av `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Händelsekod filen mål för utökade händelser i SQL-databas](sql-database-xevent-code-event-file.md)
    - Fas 1 är PowerShell för att skapa en Azure Storage-behållare.
    - Fas 2 är Transact-SQL som använder Azure Storage-behållare.

## <a name="transact-sql-differences"></a>Transact-SQL-skillnader


- När du kör den [Skapa händelse SESSION](http://msdn.microsoft.com/library/bb677289.aspx) kommandot på SQL Server du använder den **på servern** satsen. Men på SQL-databas som du använder den **på databasen** satsen i stället.


- Den **på databasen** satsen gäller även för den [ALTER händelse SESSION](http://msdn.microsoft.com/library/bb630368.aspx) och [släppa händelse SESSION](http://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL-kommandon.


- Ett bra tips är att inkludera alternativet händelse session av **STARTUP_STATE = ON** i din **Skapa händelse SESSION** eller **ALTER händelse SESSION** instruktioner.
    - Den **= ON** värdet stöder en automatisk omstart efter en Omkonfigurering av logiska databasen på grund av en växling vid fel.

## <a name="new-catalog-views"></a>Nya katalogvyerna

Funktionen för utökade händelser stöds av flera [katalogen vyer](http://msdn.microsoft.com/library/ms174365.aspx). Katalogvyer berättar om *metadata eller definitioner* användarskapade händelse sessioner i den aktuella databasen. Vyerna som returnerar inte information om instanser av aktiva händelsesessioner.

| Namnet på<br/>katalogvyn | Beskrivning |
|:--- |:--- |
| **sys.database_event_session_actions** |Returnerar en rad för varje åtgärd för varje händelse en händelse-session. |
| **sys.database_event_session_events** |Returnerar en rad för varje händelse i en händelsesession. |
| **sys.database_event_session_fields** |Returnerar en rad för varje anpassa kan kolumn som uttryckligen har ställts in på händelser och mål. |
| **sys.database_event_session_targets** |Returnerar en rad för varje händelsemål för en händelsesessionen. |
| **sys.database_event_sessions** |Returnerar en rad för varje händelse session i SQL-databasen. |

I Microsoft SQL Server har liknande katalogvyer namn som innehåller *.server\_*  i stället för *.database\_*. Namnmönstret är som **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvshttpmsdnmicrosoftcomlibraryms188754aspx"></a>Nya dynamiska hanteringsvyer [(av DMV: er)](http://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database har [dynamiska hanteringsvyer (av DMV: er)](http://msdn.microsoft.com/library/bb677293.aspx) som stöd för utökade händelser. Av DMV: er berättar om *active* händelsesessioner.

| Namnet på DMV | Beskrivning |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Returnerar information om händelsen session åtgärder. |
| **sys.dm_xe_database_session_events** |Returnerar information om Sessionshändelser. |
| **sys.dm_xe_database_session_object_columns** |Visar konfigurationsvärden för objekt som är kopplade till en session. |
| **sys.dm_xe_database_session_targets** |Returnerar information om sessionen mål. |
| **sys.dm_xe_database_sessions** |Returnerar en rad för varje händelsesession som är begränsade till den aktuella databasen. |

I Microsoft SQL Server, kallas liknande katalogvyer utan den  *\_databasen* del av namnet, exempelvis:

- **sys.dm_xe_sessions**, i stället för namn<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>Av DMV: er gemensamma för både
Det finns ytterligare av DMV: er som är gemensamma för både Azure SQL Database och Microsoft SQL Server för utökade händelser:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Hitta de tillgängliga utökade händelser och åtgärder som mål

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Mål för händelsesessioner din SQL-databas

Här följer mål som kan hämta resultat från event-sessioner på SQL-databas:

- [Ring Buffer mål](http://msdn.microsoft.com/library/ff878182.aspx) -kort innehåller händelsedata i minnet.
- [Händelsen räknaren mål](http://msdn.microsoft.com/library/ff878025.aspx) -räknar alla händelser som inträffar under en session för utökade händelser.
- [Målfil för händelsen](http://msdn.microsoft.com/library/ff878115.aspx) -skriver fullständig buffertar till en Azure Storage-behållare.

Den [ETW Event Tracing for Windows ()](http://msdn.microsoft.com/library/ms751538.aspx) API är inte tillgängligt för utökade händelser på SQL-databas.

## <a name="restrictions"></a>Begränsningar

Det finns ett par av säkerhetsrelaterade skillnaderna befitting molnmiljö av SQL-databas:

- Utökade händelser baseras på enskild klientisolering modellen. En händelsesession i en databas kan inte komma åt data eller händelser från en annan databas.
- Du kan inte utfärda en **Skapa händelse SESSION** instruktionen i samband med den **master** databas.

## <a name="permission-model"></a>Behörighetsmodellen

Du måste ha **kontrollen** behörighet på databasen för att utfärda en **Skapa händelse SESSION** instruktionen. Databasägaren (dbo) har **kontrollen** behörighet.

### <a name="storage-container-authorizations"></a>Tillstånd för Storage-behållare

SAS-token som du skapar för Azure Storage-behållare måste ange **rwl** behörigheterna. Den **rwl** värdet ger följande behörigheter:

- Läsa
- Skriva
- Visa lista

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Det finns situationer där intensiva användningen av utökade händelser kan samlas mer aktiva minne än vad som är felfri för systemets övergripande. Därför Azure SQL Database-systemet dynamiskt anger och justerar begränsar mängden active minne som kan visas med en händelsesessionen. Många faktorer är med i beräkningen dynamisk.

Om du får ett felmeddelande om ett maximalt minne har tvingats fram är vissa åtgärder du kan vidta:

- Köra färre samtidiga händelsesessioner.
- Via din **skapa** och **ALTER** uttryck för händelsesessioner, minska mängden minne som du anger på den **MAX\_minne** satsen.

### <a name="network-latency"></a>Svarstid för nätverk

Den **händelsefilen** mål kan uppleva Nätverksfördröjningen eller fel vid spara data till Azure Storage BLOB. Andra händelser i SQL-databas kan fördröjas väntan för nätverkskommunikation ska slutföras. Den här fördröjningen kan sakta din arbetsbelastning.

- För att minska denna risk för prestanda, Undvik att ange den **EVENT_RETENTION_MODE** att **NO_EVENT_LOSS** i din session Händelsedefinitioner.

## <a name="related-links"></a>Relaterade länkar

- [Använda Azure PowerShell med Azure Storage](../storage/common/storage-powershell-guide-full.md).
- [Azure Storage-Cmdlets](http://msdn.microsoft.com/library/dn806401.aspx)
- [Använda Azure PowerShell med Azure Storage](../storage/common/storage-powershell-guide-full.md) -innehåller omfattande information om PowerShell och Azure Storage-tjänsten.
- [Använda Blob storage från .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [Skapa autentiseringsuppgift (Transact-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [Skapa HÄNDELSESESSIONEN (Transact-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)
- [Jonathan Kehayias blogginlägg om utökade händelser i Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Azure *tjänstuppdateringar* webbsidan, begränsats av parametern till Azure SQL Database:
    - [https://Azure.microsoft.com/Updates/?Service=SQL-Database](https://azure.microsoft.com/updates/?service=sql-database)


Andra exempel avsnitt i koden för utökade händelser finns på följande länkar. Du måste regelbundet kontrollera varje prov för att se om exemplet riktar sig till Microsoft SQL Server jämfört med Azure SQL Database. Sedan kan du bestämma om mindre ändringar behövs för att köra exemplet.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
