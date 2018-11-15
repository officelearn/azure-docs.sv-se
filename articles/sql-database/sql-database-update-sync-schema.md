---
title: Automatisera replikeringen av schemaändringar i Azure SQL Data Sync | Microsoft Docs
description: Lär dig att automatisera replikeringen av schemaändringar i Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: cfd59c3e64e019feb4efe4546872012a02402ad7
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684339"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatisera replikeringen av schemaändringar i Azure SQL Data Sync

SQL Data Sync kan användarna synkronisera data mellan Azure SQL-databaser och en lokal SQL Server i en riktning eller i båda riktningarna. En av de nuvarande begränsningarna för SQL Data Sync är en avsaknaden av stöd för replikering av ändringar av entitetsschemat. Varje gång du ändrar tabellens schema, måste du tillämpa ändringarna manuellt för alla slutpunkter, inklusive hubben och alla medlemmar och sedan uppdatera synkroniseringsschemat.

Den här artikeln introducerar en lösning för att replikera ändringar av entitetsschemat automatiskt till alla SQL Data Sync-slutpunkter.
1. Den här lösningen använder en DDL-utlösare för att spåra ändringar av entitetsschemat.
1. Utlösaren Infoga kommandona schemat ändras i en spårning.
1. Den här spårningstabell synkroniseras till alla slutpunkter med hjälp av tjänsten Data Sync.
1. DML-utlösare efter infogning används för att tillämpa schemaändringarna på de andra slutpunkterna.

Den här artikeln använder ALTER TABLE som ett exempel på en schemaändring, men den här lösningen fungerar även för andra typer av ändringar av entitetsschemat.

> [!IMPORTANT]
> Vi rekommenderar att du läser den här artikeln noggrant, särskilt i avsnitten om [felsökning](#troubleshoot) och [annat att tänka på](#other), innan du börjar implementera automatiserade ändringen schemareplikeringen i miljön synkronisering. Vi rekommenderar också att du läser [synkronisera data i flera moln och lokala databaser med SQL Data Sync](sql-database-sync-data.md). Vissa databasåtgärder kan bryta lösningen som beskrivs i den här artikeln. Ytterligare en kunskaper om SQL Server och Transact-SQL kan krävas för att felsöka problemen.

![Automatisera replikeringen av schemaändringar](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Konfigurera automatiserade ändringen schemareplikeringen

### <a name="create-a-table-to-track-schema-changes"></a>Skapa en tabell för att spåra ändringar av entitetsschemat

Skapa en tabell för att spåra ändringar av entitetsschemat i alla databaser i synkroniseringsgruppen:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Den här tabellen innehåller en identity-kolumn för att spåra ordningen på schemaändringar. Du kan lägga till fler fält om du vill logga mer information om det behövs.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Skapa en tabell för att spåra historiken för schemaändringar

Skapa en tabell för att spåra ID för kommandot change nyligen tillämpade schemat för alla slutpunkter.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Skapa en ALTER TABLE DDL-utlösare i databasen där schemaändringar görs

Skapa en DDL-utlösare för ALTER TABLE-åtgärderna. Du behöver bara skapa den här utlösaren i databasen där schemaändringar görs. För att undvika konflikter kan endast tillåta schemaändringar i en databas i en synkroniseringsgrupp.

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

Utlösaren infogar en post i tabellen för varje ALTER TABLE-kommando schemat ändringsspårning. Det här exemplet lägger till ett filter för att undvika att replikera schemaändringar som gjorts under schemat **DataSync**, eftersom de är mest sannolika gjorda av tjänsten Data Sync. Lägga till fler filter om du bara vill replikera vissa typer av ändringar av entitetsschemat.

Du kan också lägga till fler utlösare för att replikera andra typer av ändringar av entitetsschemat. Till exempel skapa CREATE_PROCEDURE, ALTER_PROCEDURE och DROP_PROCEDURE utlösare för att replikera ändringar till lagrade procedurer.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Skapa en utlösare för andra slutpunkter och tillämpa schemaändringar under infogning

Den här utlösaren körs kommandot schemat ändras när den har synkroniserats till andra slutpunkter. Du måste skapa den här utlösaren för alla slutpunkter, förutom den där schemaändringar görs (det vill säga i databasen där DDL utlösa `AlterTableDDLTrigger` har skapats i föregående steg).

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

Den här utlösaren körs efter inmatningen och kontrollerar om det aktuella kommandot ska köras nästa. Logiken som kod säkerställer att ingen ändring schema-instruktionen har hoppats över och alla ändringar tillämpas även om infogningen är ogiltigt.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Synkronisera schemat Ändringsspårningsregistret till alla slutpunkter

Du kan synkronisera schemat Ändringsspårningsregistret till alla slutpunkter med hjälp av befintlig synkroniseringsgrupp eller en ny synkroniseringsgrupp. Kontrollera att ändringarna i tabellen Uppföljning kan synkroniseras till alla slutpunkter, särskilt när du använder en riktning synkronisering.

Synkronisera inte historiktabellen för schemat ändras eftersom tabellen upprätthåller olika tillstånd på olika slutpunkter.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Tillämpa schemaändringar i en synkroniseringsgrupp

Replikeras endast schemaändringar gjorts i databasen var DDL-utlösare ska skapas. Schemaändringar som gjorts i andra databaser replikeras inte.

När schemaändringarna replikeras till alla slutpunkter, måste du också vidta ytterligare åtgärder för att uppdatera synkroniseringsschemat för att starta eller stoppa synkroniserar de nya kolumnerna.

#### <a name="add-new-columns"></a>Lägga till nya kolumner

1.  Göra schemat ändras.

1.  Undvika att data om de nya kolumnerna genomförs förrän du har slutfört steg som skapar utlösaren.

1.  Vänta tills schemaändringarna tillämpas på alla slutpunkter.

1.  Uppdatera databasschemat och Lägg till den nya kolumnen till i synkroniseringsschemat.

1.  Data i den nya kolumnen har synkroniserats under nästa synkronisering.

#### <a name="remove-columns"></a>Ta bort kolumner

1.  Ta bort kolumner från synkroniseringsschemat. Datasynkronisering stoppar synkroniserar data i dessa kolumner.

1.  Göra schemat ändras.

1.  Uppdatera databasschemat.

#### <a name="update-data-types"></a>Uppdatera datatyper

1.  Göra schemat ändras.

1.  Vänta tills schemaändringarna tillämpas på alla slutpunkter.

1.  Uppdatera databasschemat.

1.  Om nya och gamla datatyperna inte är helt kompatibla – till exempel om du ändrar från `int` till `bigint` -synkroniseringen misslyckas innan de steg som skapar utlösare har utförts. Synkronisera lyckas efter ett nytt försök.

#### <a name="rename-columns-or-tables"></a>Byt namn på kolumner eller tabeller

Byta namn på kolumner eller tabeller gör datasynkronisering slutar att fungera. Skapa en ny tabell eller kolumn, återfyller data, och sedan ta bort den gamla tabellen eller kolumnen i stället för att byta namn på.

#### <a name="other-types-of-schema-changes"></a>Andra typer av schemaändringar

För andra typer av schemaändringar – exempelvis krävs skapa lagrade procedurer eller släppa ett index - uppdaterar synkroniseringsschemat inte.

## <a name="troubleshoot"></a> Felsöka automatiserade ändringen schemareplikeringen

Replikering logiken i den här artikeln slutar fungera i vissa situationer – till exempel om du har gjort ett schema som ändras i en lokal databas som inte stöds i Azure SQL Database. I så fall misslyckas synkroniserar schemat Ändringsspårningsregistret. Du måste korrigera problemet manuellt:

1.  Inaktivera DDL-utlösare och undvika eventuella ytterligare schemaändringar förrän problemet har lösts.

1.  Inaktivera AFTER INSERT-utlösare på den slutpunkt som där en schemaändring inte kan göras i endpoint-databasen där problemet sker. Denna åtgärd kan kommandot schemat ändras för att synkroniseras.

1.  Utlös sync för att synkronisera Ändringsspårningsregistret schemat.

1.  I endpoint-databasen där problemet sker, ändra frågan schemat historiktabellen att hämta ID för senaste tillämpade schemat ändra kommandot.

1.  Fråga schemat för ändringsspårning tabell om du vill visa alla kommandon med ett ID som är större än det ID-värdet som du hämtade i föregående steg.

    a.  Ignorera de kommandon som inte kan köras i databasen för slutpunkten. Du behöver åtgärda inkonsekvens i schemat. Återställa de ursprungliga schemaändringarna om inkonsekvensen påverkar ditt program.

    b.  Tillämpa manuellt de kommandon som ska användas.

1.  Uppdatera schemat ändra historik tabell och ange ID för senaste tillämpade till det korrekta värdet.

1.  Kontrollera om schemat är uppdaterad.

1.  Återaktivera AFTER INSERT utlösaren inaktiverad i det andra steget.

1.  Återaktivera DDL-utlösare som inaktiverade i det första steget.

Om du vill rensa posterna i tabellen schema ändra spårning, använder du borttagning i stället för TRUNCATE. Aldrig reseed identitetskolumnen i schemat Ändringsspårningsregistret med hjälp av DBCC CHECKIDENT. Du kan skapa ny schemaändring spårningstabeller och uppdatera tabellnamnet i DDL-utlösare om omläggning krävs.

## <a name="other"></a> Annat att tänka på

-   Databasanvändare som konfigurerar databaserna för hubben och medlem måste ha tillräckligt med behörighet att köra kommandona schemat ändras.

-   Du kan lägga till fler filter i DDL-utlösare för att bara replikera schemaändring i valda tabeller eller åtgärder.

-   Du kan endast ändra schemat i databasen var DDL-utlösare ska skapas.

-   Om du gör en ändring i en lokal SQL Server-databas, kontrollera att en schemaändring stöds i Azure SQL Database.

-   Om schemat ändras i databaser än databasen var DDL-utlösare ska skapas, replikeras inte ändringarna. För att undvika det här problemet kan skapa du DDL-utlösare för att blockera ändringar på andra slutpunkter.

-   Om du vill ändra schemat för schemat ändra spårningstabell, inaktivera DDL-utlösare innan du gör ändringen och sedan manuellt tillämpa ändringen för alla slutpunkter. Det fungerar inte att uppdatera schemat i en AFTER INSERT-utlösare i samma tabell.

-   Inte reseed identity-kolumn med hjälp av DBCC CHECKIDENT.

-   Använd inte TRUNCATE för att rensa data i tabellen schemat ändringsspårning.

## <a name="next-steps"></a>Nästa steg

Mer information om SQL Data Sync finns i:

-   Översikt – [synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md)
-   Konfigurera datasynkronisering
    - I portalen – [självstudie: Ställ in SQL Data Sync att synkronisera data mellan Azure SQL Database och SQL Server lokalt](sql-database-get-started-sql-data-sync.md)
    - Med PowerShell
        -  [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Data synkroniseras Agent - [Data synkroniseras Agent för Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Metodtips – [Metodtips för Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Övervaka – [övervaka SQL Data Sync med Log Analytics](sql-database-sync-monitor-oms.md)
-   Felsök - [felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
-   Uppdatera synkroniseringsschemat
    -   Med PowerShell - [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/sql-database-sync-update-schema.md)
