---
title: Automatisera replikering av schemaändringar i SQL Data Sync
description: Lär dig hur du automatiserar replikering av schemaändringar i Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 11/14/2018
ms.openlocfilehash: 639901975bbb66b9f410bea297d9e48cd96d6d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822436"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatisera replikering av schemaändringar i Azure SQL Data Sync

Med SQL Data Sync kan användare synkronisera data mellan Azure SQL-databaser och lokala SQL Server i en riktning eller i båda riktningarna. En av de aktuella begränsningarna med SQL Data Sync är brist på stöd för replikering av schemaändringar. Varje gång du ändrar tabellschemat måste du använda ändringarna manuellt på alla slutpunkter, inklusive hubben och alla medlemmar, och sedan uppdatera synkroniseringsschemat.

Den här artikeln introducerar en lösning för att automatiskt replikera schemaändringar till alla SQL Data Sync-slutpunkter.
1. Den här lösningen använder en DDL-utlösare för att spåra schemaändringar.
1. Utlösaren infogar kommandona för schemaändring i en spårningstabell.
1. Den här spårningstabellen synkroniseras med alla slutpunkter med hjälp av tjänsten Datasynkronisering.
1. DML-utlösare efter insättning används för att tillämpa schemaändringarna på de andra slutpunkterna.

Den här artikeln använder ALTER TABLE som ett exempel på en schemaändring, men den här lösningen fungerar även för andra typer av schemaändringar.

> [!IMPORTANT]
> Vi rekommenderar att du läser den här artikeln noggrant, särskilt avsnitten om [Felsökning](#troubleshoot) och [andra överväganden](#other), innan du börjar implementera automatisk schemaändringsreplikering i synkroniseringsmiljön. Vi rekommenderar också att du läser [Synkroniseringsdata i flera molndatabaser och lokala databaser med SQL Data Sync](sql-database-sync-data.md). Vissa databasåtgärder kan bryta lösningen som beskrivs i den här artikeln. Ytterligare domänkunskap om SQL Server och Transact-SQL kan krävas för att felsöka dessa problem.

![Automatisera replikering av schemaändringar](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Konfigurera automatisk schemaändringsreplikering

### <a name="create-a-table-to-track-schema-changes"></a>Skapa en tabell för att spåra schemaändringar

Skapa en tabell för att spåra schemaändringar i alla databaser i synkroniseringsgruppen:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Den här tabellen har en identitetskolumn för att spåra ordningen på schemaändringar. Du kan lägga till fler fält för att logga mer information om det behövs.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Skapa en tabell för att spåra historiken för schemaändringar

På alla slutpunkter skapar du en tabell för att spåra ID:t för det senast tillämpade kommandot schemaändring.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Skapa en ALTER TABLE DDL-utlösare i databasen där schemaändringar görs

Skapa en DDL-utlösare för ALTER TABLE-åtgärder. Du behöver bara skapa den här utlösaren i databasen där schemaändringar görs. Om du vill undvika konflikter tillåter du bara schemaändringar i en databas i en synkroniseringsgrupp.

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

Utlösaren infogar en post i schemaändringsspårningstabellen för varje ALTER TABLE-kommando. I det här exemplet läggs ett filter till för att undvika att schemaändringar som gjorts under **schemaDataSync**replikeras , eftersom dessa troligen görs av tjänsten Data sync. Lägg till fler filter om du bara vill replikera vissa typer av schemaändringar.

Du kan också lägga till fler utlösare för att replikera andra typer av schemaändringar. Skapa till exempel CREATE_PROCEDURE, ALTER_PROCEDURE och DROP_PROCEDURE utlösare för att replikera ändringar av lagrade procedurer.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Skapa en utlösare på andra slutpunkter för att tillämpa schemaändringar under insättning

Den här utlösaren kör kommandot schemaändring när det synkroniseras med andra slutpunkter. Du måste skapa den här utlösaren på alla slutpunkter, utom den där schemaändringar görs (det `AlterTableDDLTrigger` vill säga i databasen där DDL-utlösaren skapas i föregående steg).

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

Den här utlösaren körs efter insättningen och kontrollerar om det aktuella kommandot ska köras härnäst. Kodlogiken säkerställer att ingen schemaändringssats hoppas över och alla ändringar tillämpas även om insättningen är i fel ordning.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Synkronisera schemaändringsspårningstabellen till alla slutpunkter

Du kan synkronisera schemaändringsspårningstabellen till alla slutpunkter med den befintliga synkroniseringsgruppen eller en ny synkroniseringsgrupp. Kontrollera att ändringarna i spårningstabellen kan synkroniseras med alla slutpunkter, särskilt när du använder enriktad synkronisering.

Synkronisera inte tabellen schemaändringshistorik, eftersom tabellen har olika tillstånd på olika slutpunkter.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Använda schemaändringarna i en synkroniseringsgrupp

Endast schemaändringar som görs i databasen där DDL-utlösaren skapas replikeras. Schemaändringar som görs i andra databaser replikeras inte.

När schemaändringarna har replikerats till alla slutpunkter måste du också vidta extra åtgärder för att uppdatera synkroniseringsschemat för att starta eller sluta synkronisera de nya kolumnerna.

#### <a name="add-new-columns"></a>Lägga till nya kolumner

1.  Ändra schemat.

1.  Undvik alla dataändringar där de nya kolumnerna är inblandade tills du har slutfört steget som skapar utlösaren.

1.  Vänta tills schemaändringarna tillämpas på alla slutpunkter.

1.  Uppdatera databasschemat och lägg till den nya kolumnen i synkroniseringsschemat.

1.  Data i den nya kolumnen synkroniseras under nästa synkroniseringsåtgärd.

#### <a name="remove-columns"></a>Ta bort kolumner

1.  Ta bort kolumnerna från synkroniseringsschemat. Datasynkronisering slutar synkronisera data i dessa kolumner.

1.  Ändra schemat.

1.  Uppdatera databasschemat.

#### <a name="update-data-types"></a>Uppdatera datatyper

1.  Ändra schemat.

1.  Vänta tills schemaändringarna tillämpas på alla slutpunkter.

1.  Uppdatera databasschemat.

1.  Om de nya och gamla datatyperna inte är helt `int` kompatibla , till exempel om du ändrar från till `bigint` - kan synkroniseringen misslyckas innan stegen som skapar utlösarna slutförs. Synkroniseringen lyckas efter ett nytt försök.

#### <a name="rename-columns-or-tables"></a>Byta namn på kolumner eller tabeller

Om du byter namn på kolumner eller tabeller slutar datasynkronisering att fungera. Skapa en ny tabell eller kolumn, fyll på data igen och ta sedan bort den gamla tabellen eller kolumnen i stället för att byta namn.

#### <a name="other-types-of-schema-changes"></a>Andra typer av schemaändringar

För andra typer av schemaändringar , till exempel, skapa lagrade procedurer eller släppa ett index- uppdatera synkroniseringsschemat krävs inte.

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a>Felsöka automatisk schemaändringsreplikering

Replikeringslogiken som beskrivs i den här artikeln slutar fungera i vissa situationer, till exempel om du har gjort en schemaändring i en lokal databas som inte stöds i Azure SQL Database. I så fall misslyckas synkroniseringen av schemaändringsspårningstabellen. Du måste åtgärda problemet manuellt:

1.  Inaktivera DDL-utlösaren och undvik ytterligare schemaändringar tills problemet är åtgärdat.

1.  I slutpunktsdatabasen där problemet uppstår inaktiverar du efterinfogningsutlösaren på slutpunkten där schemaändringen inte kan göras. Med den här åtgärden kan kommandot schemaändring synkroniseras.

1.  Utlösarsynkronisering för synkronisering av schemaändringstabellen.

1.  I slutpunktsdatabasen där problemet uppstår frågar du efter tabellen schemaändringshistorik för att hämta ID:et för det senast tillämpade kommandot schemaändring.

1.  Fråga schemaändringsspårningstabellen om du vill visa alla kommandon med ett ID som är större än det ID-värde som du hämtade i föregående steg.

    a.  Ignorera de kommandon som inte kan köras i slutpunktsdatabasen. Du måste hantera schemainkonsekvensen. Återställ de ursprungliga schemaändringarna om inkonsekvensen påverkar ditt program.

    b.  Använd de kommandon som ska användas manuellt.

1.  Uppdatera tabellen schemaändringshistorik och ange det senast tillämpade ID:et till rätt värde.

1.  Dubbelkolla om schemat är uppdaterat.

1.  Aktivera efterinfogningsutlösaren inaktiverad i det andra steget.

1.  Aktivera DDL-utlösaren igen inaktiverad i det första steget.

Om du vill rensa posterna i schemaändringsspårningstabellen använder du DELETE i stället för TRUNKERA. Aldrig reseed identitetskolumnen i schemaändring spårningstabell med hjälp av DBCC CHECKIDENT. Du kan skapa nya schemaändringsspårningstabeller och uppdatera tabellnamnet i DDL-utlösaren om det krävs om du skickar en ny igensättning.

## <a name="other-considerations"></a><a name="other"></a>Andra överväganden

-   Databasanvändare som konfigurerar hubb- och medlemsdatabaserna måste ha tillräckligt med behörighet för att köra schemaändringskommandona.

-   Du kan lägga till fler filter i DDL-utlösaren för att bara replikera schemaändring i valda tabeller eller åtgärder.

-   Du kan bara göra schemaändringar i databasen där DDL-utlösaren skapas.

-   Om du gör en ändring i en lokal SQL Server-databas kontrollerar du att schemaändringen stöds i Azure SQL Database.

-   Om schemaändringar görs i andra databaser än databasen där DDL-utlösaren skapas replikeras inte ändringarna. För att undvika det här problemet kan du skapa DDL-utlösare för att blockera ändringar på andra slutpunkter.

-   Om du behöver ändra schemat för schemaändringsspårningstabellen inaktiverar du DDL-utlösaren innan du gör ändringen och tillämpar sedan ändringen manuellt på alla slutpunkter. Det går inte att uppdatera schemat i en EFTERIN-utlösare i samma tabell.

-   Skicka inte identitetskolumnen igen med hjälp av DBCC CHECKIDENT.

-   Använd inte TRUNKATE för att rensa data i schemaändringsspårningstabellen.

## <a name="next-steps"></a>Nästa steg

Mer information om SQL Data Sync finns i:

-   Översikt – [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md)
-   Konfigurera Data Sync
    - I portalen – [Självstudie: Konfigurera SQL Data Sync att synkronisera data mellan Azure SQL Database och SQL Server lokalt](sql-database-get-started-sql-data-sync.md)
    - Med PowerShell
        -  [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Använd PowerShell för att synkronisera mellan en Azure SQL-databas och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Datasynkroniseringsagent – [Datasynkroniseringsagent för Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Metodtips – [Metodtips för Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Övervaka - [Övervaka SQL Data Sync med Azure Monitor-loggar](sql-database-sync-monitor-oms.md)
-   Felsökning – [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
-   Uppdatera synkroniseringsschemat
    -   Med PowerShell – [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/sql-database-sync-update-schema.md)
