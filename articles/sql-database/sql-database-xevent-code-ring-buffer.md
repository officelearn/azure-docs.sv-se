---
title: XEvent-ringbufferten kod för SQL-databas | Microsoft Docs
description: Innehåller ett Transact-SQL-kodexempel som görs enkelt och snabbt med hjälp av Ring Buffer-mål i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 522561d8afcf6fe1bdd2887f7fd5180b79565a61
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874625"
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Ringbuffertens målkod för utökade händelser i SQL-databas

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Du vill ha ett komplett kodexempel för det enklaste snabbt sättet att avbilda och rapportera information om en utökad händelse under ett test. Det enklaste målet för utökade händelser är den [Ringbuffert target](https://msdn.microsoft.com/library/ff878182.aspx).

Det här avsnittet anger ett Transact-SQL-kodexempel som:

1. Skapar en tabell med data för att visa med.
2. Skapar en session för en befintlig utökade händelser, nämligen **sqlserver.sql_statement_starting**.
   
   * Händelsen är begränsad till SQL-uttryck som innehåller en viss uppdatering-sträng: **instruktionen som '% UPDATE tabEmployee %'**.
   * Väljer att skicka utdata för händelsen till ett mål av typen Ringbuffert, nämligen **package0.ring_buffer**.
3. Startar händelsesessionen.
4. Problem med några enkla SQL-UPDATE-instruktioner.
5. Utfärdar en SQL SELECT-instruktion för att hämta utdata för händelse från ringbufferten.
   
   * **sys.dm_xe_database_session_targets** och andra dynamiska hanteringsvyer (DMV) är anslutna.
6. Stoppar händelsesessionen.
7. Utelämnar ringbufferten mål, om du vill frigöra dess resurser.
8. Utelämnar händelsesessionen och demo-tabellen.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* Alla databaser som du kan skapa en tabell i.
  
  * Du kan också [skapa en **AdventureWorksLT** demonstrationsdatabas](sql-database-get-started.md) på några minuter.
* SQL Server Management Studio (ssms.exe), helst den senaste månatliga update-versionen. 
  Du kan hämta den senaste ssms.exe från:
  
  * Avsnittet [Hämta SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [En direktlänk till nedladdningen.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Kodexempel

Med mycket små ändringar, kan du köra följande kodexempel i ringbufferten på Azure SQL Database eller Microsoft SQL Server. Skillnaden är förekomsten av noden '_databas' namnet på vissa dynamiska hanteringsvyer (DMV) används i FROM-satsen i steg 5. Exempel:

* sys.dm_xe **_databas**_session_targets
* sys.dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;

## <a name="ring-buffer-contents"></a>Ring Buffer innehållet

Vi använde ssms.exe för att köra kodexemplet.

Om du vill visa resultatet klickar vi på cellen under kolumnrubriken **target_data_XML**.

Sedan i resultatfönstret klickar vi på cellen under kolumnrubriken **target_data_XML**. Detta klickar du på Skapa en annan fil-flik i ssms.exe där innehållet i resultatcellen visades som XML.

Utdata visas i följande block. Det verkar lång tid, men det är bara två **<event>** element.

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### <a name="release-resources-held-by-your-ring-buffer"></a>Frigöra resurser som lagras av din Ringbuffert

När du är klar med din Ringbuffert, kan du ta bort den och släpper dess resurser som utfärdar en **ALTER** ut så här:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


Definitionen av din händelsesessionen uppdateras, men inte ta bort. Du kan senare lägga till en annan instans av ringbufferten i event-sessionen:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```


## <a name="more-information"></a>Mer information

Primär avsnittet för utökade händelser på Azure SQL Database är:

* [Extended event-överväganden i SQL Database](sql-database-xevent-db-diff-from-svr.md), som står i kontrast vissa aspekter av utökade händelser som skiljer sig åt mellan Azure SQL Database jämfört med Microsoft SQL Server.

Andra exempel ämnen i koden för utökade händelser finns på följande länkar. Du måste regelbundet kontrollera eventuella exemplet för att se om exemplet riktar sig mot Microsoft SQL Server och Azure SQL Database. Därefter kan du bestämma om mindre ändringar behövs för att köra exemplet.

* Kodexempel för Azure SQL Database: [händelse målkod för utökade händelser i SQL-databas](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
