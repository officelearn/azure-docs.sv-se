---
title: "XEvent-ringbufferten koden för SQL-databas | Microsoft Docs"
description: "Ger en Transact-SQL-kodexempel som görs enkelt och snabbt med hjälp av ringbufferten mål, i Azure SQL Database."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: 2510fb3f-c8f2-437a-8f49-9d5f6c96e75b
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genemi
ms.openlocfilehash: 61251eb9b125209ffd15adafdb0bace495e7cadd
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Ring buffert mål koden för utökade händelser i SQL-databas

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Du vill använda en fullständig kodexempel för enklaste snabbt sätt att avbilda och rapportera information om en utökad händelse under ett test. Det enklaste målet för utökade händelsedata är den [ringbufferten mål](http://msdn.microsoft.com/library/ff878182.aspx).

Det här avsnittet presenteras en Transact-SQL-kodexempel som:

1. Skapar en tabell med data för att visa med.
2. Skapar en session för en befintlig utökad händelse, nämligen **sqlserver.sql_statement_starting**.
   
   * Händelsen är begränsad till SQL-uttryck som innehåller en viss uppdatering sträng: **instruktionen som '% uppdatering tabEmployee %'**.
   * Väljer att skicka utdata för händelsen till ett mål av typen ringbufferten, nämligen **package0.ring_buffer**.
3. Startar händelsesessionen.
4. Utfärdar ett par enkla UPDATE SQL-instruktioner.
5. Utfärdar en SQL SELECT-instruktion för att hämta utdata för händelse från ringbufferten.
   
   * **sys.dm_xe_database_session_targets** och andra dynamiska hanteringsvyer (av DMV: er) som är anslutna.
6. Stoppar händelsesessionen.
7. Utelämnar ringbufferten mål, om du vill frigöra resurser.
8. Utelämnar händelsesessionen och demo-tabellen.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto och prenumeration. Registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* Alla databaser som du kan skapa en tabell i.
  
  * Alternativt kan du [skapa en **AdventureWorksLT** demonstrationsdatabas](sql-database-get-started.md) i minuter.
* SQL Server Management Studio (ssms.exe), helst den senaste månatliga update-versionen. 
  Du kan hämta den senaste ssms.exe från:
  
  * Avsnittet [Hämta SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
  * [En direktlänk till nedladdningen.](http://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Kodexempel

Med mycket mindre ändringar, kan du köra följande kodexempel i ringbufferten på Azure SQL Database eller Microsoft SQL Server. Skillnaden är förekomsten av noden '_databas' i vissa dynamiska hanteringsvyer (av DMV: er), används i FROM-satsen i steg 5. Exempel:

* sys.dm_xe**_databas**_session_targets
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

Om du vill visa resultatet, vi har klickat på cellen under kolumnrubriken **target_data_XML**.

Sedan i resultatfönstret klickar vi på cellen under kolumnrubriken **target_data_XML**. Detta klickar du på Skapa en annan fil flik i ssms.exe där innehållet i resultatcellen visades som XML.

Utdata visas i följande blocket. Det ser ut långt, men det är bara två  **<event>**  element.

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


#### <a name="release-resources-held-by-your-ring-buffer"></a>Frigöra resurser som innehas av Ring Buffer

När du är klar med Ring Buffer kan du ta bort den och släpper dess resurser som utfärdar en **ALTER** ut så här:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


Definitionen för händelsesessionen uppdaterats, men inte ta bort. Du kan senare lägga till en annan instans av ringbufferten händelsesessionen:

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

* [Utökad händelse överväganden i SQL-databas](sql-database-xevent-db-diff-from-svr.md), vilket står i kontrast vissa aspekter av utökade händelser som skiljer sig åt mellan Azure SQL Database jämfört med Microsoft SQL Server.

Andra exempel avsnitt i koden för utökade händelser finns på följande länkar. Du måste regelbundet kontrollera varje prov för att se om exemplet riktar sig till Microsoft SQL Server jämfört med Azure SQL Database. Sedan kan du bestämma om mindre ändringar behövs för att köra exemplet.

* Kodexempel för Azure SQL Database: [händelsefilen mål koden för utökade händelser i SQL-databas](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
