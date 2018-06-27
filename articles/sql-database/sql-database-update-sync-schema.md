---
title: Automatisera replikering av schemaändringar i Azure SQL-datasynkronisering | Microsoft Docs
description: Lär dig mer om att automatisera replikering av schemaändringar i Azure SQL-datasynkronisering.
services: sql-database
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: a39e060708514fdca11a5d89858486b442a18309
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019595"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatisera replikering av schemaändringar i Azure SQL-datasynkronisering

SQL-datasynkronisering kan användarna synkronisera data mellan Azure SQL-databaser och lokala SQL Server i en riktning eller i båda riktningarna. En av de nuvarande begränsningarna för SQL-datasynkronisering är saknas stöd för replikering av schemaändringar. Varje gång du ändrar tabellens schema, måste du tillämpa ändringarna manuellt på alla slutpunkter, inklusive navet och alla medlemmar och sedan uppdatera schemat för synkronisering.

Den här artikeln introducerar en lösning för att replikera schemaändringar automatiskt till alla SQL-datasynkronisering slutpunkter.
1. Den här lösningen använder en DDL-utlösare för att spåra schemaändringar.
2. Utlösaren Infoga schemat ändra kommandon i en spårning.
3. Spårning av den här tabellen som ska synkroniseras alla slutpunkter som använder tjänsten datasynkronisering.
4. DML-utlösare efter infogning används för att tillämpa schemaändringarna på de andra slutpunkterna.

Den här artikeln använder ALTER TABLE är ett exempel på en schemaändring, men den här lösningen fungerar även för andra typer av schemaändringar.

> [!IMPORTANT]
> Vi rekommenderar att du läser den här artikeln noggrant, särskilt i avsnitten om [felsökning](#troubleshooting) och [andra överväganden](#other), innan du börjar att implementera automatiserad ändra schemareplikeringen i Synkronisera miljön. Vi rekommenderar också att du läser [synkronisera data över flera molntjänster och lokala databaser med SQL datasynkronisering](sql-database-sync-data.md). Vissa åtgärder kan bryta lösningen som beskrivs i den här artikeln. Ytterligare en kunskap om SQL Server och Transact-SQL kan krävas för felsökning av dessa problem.

![Automatisera replikering av schemaändringar](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Konfigurera automatisk schemat ändra replikering

### <a name="create-a-table-to-track-schema-changes"></a>Skapa en tabell för att spåra schemaändringar

Skapa en tabell för att spåra schemaändringar i alla databaser i gruppen synkronisering:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Den här tabellen har en identitetskolumn att spåra schemaändringar ordning. Du kan lägga till fler fält om du vill logga mer information om det behövs.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Skapa en tabell för att spåra historiken för schemaändringar

Skapa en tabell för att spåra ID för kommandot Ändra senast använda schemat för alla slutpunkter.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Skapa en ALTER TABLE DDL-utlösare i databasen där schemat ändras

Skapa en DDL-utlösare för ALTER TABLE-åtgärder. Behöver du bara skapa den här utlösaren i databasen där schemat ändras. För att undvika konflikter kan du bara schemaändringar i en databas i en grupp för synkronisering.

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

Utlösaren infogar en post i tabellen för varje ALTER TABLE-kommando schemat ändringsspårning. Det här exemplet lägger till ett filter för att undvika att replikera schemaändringar enligt schemat **DataSync**, eftersom de är förmodligen gjorts av tjänsten datasynkronisering. Lägga till fler filter om du vill replikera vissa typer av schemaändringar.

Du kan också lägga till flera utlösare för att replikera andra typer av schemaändringar. Till exempel skapa CREATE_PROCEDURE, ALTER_PROCEDURE och DROP_PROCEDURE utlösare för att replikera ändringar till lagrade procedurer.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Skapa en utlösare för slutpunkter och tillämpa schemaändringar under infogning

Den här utlösaren kör kommandot Ändra schemat när den är synkroniserad med andra slutpunkter. Du måste skapa den här utlösaren på alla slutpunkter, förutom den där schemaändringar görs (som är i databasen där DDL utlösa `AlterTableDDLTrigger` har skapats i föregående steg).

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

Den här utlösaren körs efter infogningen och kontrollerar om det aktuella kommandot ska köras nästa. Koden logiken säkerställer att ingen ändring schema-instruktionen hoppas över och alla ändringar tillämpas även om insättningspunkten är i fel ordning.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Synkronisera schemat för ändringsspårning tabell till alla slutpunkter

Du kan synkronisera schemat för ändringsspårning tabell till alla slutpunkter som använder den befintliga sync-gruppen eller en ny grupp för synkronisering. Se till att ändringar i tabellen kan synkroniseras till alla slutpunkter, särskilt när du använder en riktning synkronisering.

Synkronisera inte schemat ändra historik tabell eftersom tabellen har olika tillstånd på olika slutpunkter.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Tillämpa schemaändringar i en grupp för synkronisering

Endast schemaändringar som gjorts i databasen där DDL-utlösaren skapas replikeras. Schemaändringar som gjorts i andra databaser replikeras inte.

När schemaändringarna replikeras till alla slutpunkter, även måste du vidta ytterligare åtgärder för att uppdatera sync-schemat om du vill börja eller sluta synkronisera nya kolumner.

#### <a name="add-new-columns"></a>Lägga till nya kolumner

1.  Kontrollera att schemat ändras.

2.  Undvika att data där de nya kolumnerna som är inblandade förrän du har slutfört steg som skapar utlösaren.

3.  Vänta tills schemaändringarna tillämpas på alla slutpunkter.

4.  Uppdatera databasschemat och lägga till den nya kolumnen i sync-schema.

5.  Data i den nya kolumnen är synkroniserad under nästa synkronisering.

#### <a name="remove-columns"></a>Ta bort kolumner

1.  Ta bort kolumner från sync-schemat. Datasynkronisering stoppar synkroniserar data i dessa kolumner.

2.  Kontrollera att schemat ändras.

3.  Uppdatera databasschemat.

#### <a name="update-data-types"></a>Uppdatera datatyper

1.  Kontrollera att schemat ändras.

2.  Vänta tills schemaändringarna tillämpas på alla slutpunkter.

3.  Uppdatera databasschemat.

4.  Om nya och gamla datatyperna inte är helt kompatibel - till exempel om du ändrar från `int` till `bigint` -synkroniseringen misslyckas innan de steg som skapar utlösare har utförts. Synkronisera lyckas efter ett nytt försök.

#### <a name="rename-columns-or-tables"></a>Byt namn på kolumner eller tabeller

Byta namn på kolumner eller tabeller gör datasynkronisering slutar att fungera. Skapa en ny tabell eller kolumn, fyller data, och ta sedan bort den gamla tabellen eller kolumnen i stället för att byta namn på.

#### <a name="other-types-of-schema-changes"></a>Andra typer av schemaändringar

För andra typer av schemaändringar – till exempel krävs skapa lagrade procedurer eller släppa ett index - uppdatering på synkroniseringsschema inte.

## <a name="troubleshoot"></a> Felsöka automatisk schemat ändra replikering

Replikering logiken i den här artikeln upphör att fungera i vissa situationer – till exempel om du har gjort en ändring i en lokal databas som inte stöds i Azure SQL Database schemat. I så fall misslyckas synkroniserar tabellen schema ändringsspårning. Du måste korrigera problemet manuellt:

1.  Inaktivera DDL-utlösare och undvika eventuella ytterligare schemaändringar förrän problemet har åtgärdats.

2.  Inaktivera AFTER INSERT-utlösare på slutpunkten där Schemaändringen inte kan göras i endpoint-databasen där problemet sker. Denna åtgärd kan kommandot schemat ändras för att synkroniseras.

3.  Aktivera synkronisering för att synkronisera schemat för ändringsspårning tabell.

4.  I endpoint databasen där problemet sker, ändra frågan schemat historiktabellen att hämta ID för senaste tillämpade schemat ändra kommandot.

5.  Fråga schemat för ändringsspårning tabellen för att visa en lista över alla kommandon med ett ID som är större än värdet ID som du hämtade i föregående steg.

    a.  Ignorera de kommandon som inte kan köras i endpoint-databasen. Du behöver åtgärda inkonsekvens i schemat. Återställ ursprungliga schemaändringar om inkonsekvensen påverkar ditt program.

    b.  Manuellt koppla de kommandon som ska användas.

6.  Uppdatera schemat ändra historik tabell och ange senaste tillämpade ID till rätt värde.

7.  Kontrollera om schemat är uppdaterad.

8.  Återaktivera inaktiverad i det andra steget AFTER INSERT utlösare.

9.  Återaktivera inaktiverad i det första steget DDL-utlösare.

Om du vill rensa posterna i schemat ändra tabellen Använd bort i stället för TRUNCATE. Aldrig Anger startvärde identitetskolumnen i schema-ändringsspårning tabellen med hjälp av DBCC CHECKIDENT. Du kan skapa ny schemaändring spårningstabeller och uppdatera namnet på tabellen i DDL-utlösaren om omläggning krävs.

## <a name="other"></a> Andra överväganden

-   Databasanvändare konfigurerar databaserna för NAV- och måste ha behörighet att köra kommandona ändra schemat.

-   Du kan lägga till fler filter i DDL-utlösaren att enbart replikera schemaändring i markerade tabellerna eller åtgärder.

-   Du kan bara göra schemaändringar i databasen där DDL-utlösare har skapats.

-   Om du gör en ändring i en lokal SQL Server-databas måste du se till att ändra stöds i Azure SQL Database.

-   Om schemat ändras i databaser än databasen där DDL-utlösaren skapas, replikeras inte ändringarna. För att undvika det här problemet kan skapa du DDL-utlösare för att blockera ändringar på slutpunkter.

-   Om du vill ändra schemat för schemat ändra spårningstabell, inaktivera DDL-utlösaren innan du gör ändringarna och sedan tillämpa ändringen manuellt för alla slutpunkter. Det fungerar inte att uppdatera schemat i en AFTER INSERT-utlösare i samma tabell.

-   Inte anger startvärde identity-kolumn med hjälp av DBCC CHECKIDENT.

-   Använd inte TRUNCATE för att rensa data i tabellen schema ändringsspårning.
