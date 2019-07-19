---
title: Kopiera en Azure SQL-databas | Microsoft Docs
description: Skapa en transaktions konsekvent kopia av en befintlig Azure SQL-databas på antingen samma server eller en annan server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 06/03/2019
ms.openlocfilehash: dc82ace4c5bf1204075bfa5e34d0b1489a3acb98
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876280"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Kopiera en transaktions konsekvent kopia av en Azure SQL-databas

Azure SQL Database innehåller flera metoder för att skapa en transaktions konsekvent kopia av en befintlig Azure SQL-databas ([enkel databas](sql-database-single-database.md)) på samma server eller en annan server. Du kan kopiera en SQL-databas med hjälp av Azure Portal, PowerShell eller T-SQL. 

## <a name="overview"></a>Översikt

En databas kopia är en ögonblicks bild av käll databasen vid tidpunkten för kopierings förfrågan. Du kan välja samma server eller en annan server. Du kan också välja att behålla tjänst nivån och beräknings storleken, eller använda en annan beräknings storlek inom samma service nivå (utgåva). När kopieringen är klar blir den en fullständigt fungerande, oberoende databas. I det här läget kan du uppgradera eller nedgradera det till vilken version som helst. Inloggningar, användare och behörigheter kan hanteras oberoende av varandra.  

> [!NOTE]
> [Automatiserade databas säkerhets kopieringar](sql-database-automated-backups.md) används när du skapar en databas kopia.

## <a name="logins-in-the-database-copy"></a>Inloggningar i databas kopian

När du kopierar en databas till samma SQL Database-Server kan samma inloggningar användas på båda databaserna. Säkerhets objekt som du använder för att kopiera databasen blir databas ägaren på den nya databasen. Alla databas användare, deras behörigheter och deras säkerhets identifierare (sid) kopieras till databas kopian.  

När du kopierar en databas till en annan SQL Database Server blir säkerhetsobjektet på den nya servern databas ägaren på den nya databasen. Om du använder [inneslutna databas användare](sql-database-manage-logins.md) för data åtkomst ser du till att både den primära och sekundära databasen alltid har samma användarautentiseringsuppgifter, så att när kopieringen är klar kan du omedelbart komma åt den med samma autentiseringsuppgifter. 

Om du använder [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)kan du helt ta bort behovet av att hantera autentiseringsuppgifter i kopian. Men när du kopierar databasen till en ny server kanske den inloggnings åtkomsten inte fungerar, eftersom inloggningarna inte finns på den nya servern. Information om hur du hanterar inloggningar när du kopierar en databas till en annan SQL Database-Server finns i [så här hanterar du Azure SQL Database-säkerhet efter haveri beredskap](sql-database-geo-replication-security-config.md). 

När kopieringen är klar och innan andra användare mappas om, så kan databas ägaren logga in på den nya databasen. Information om hur du löser inloggningar när kopieringen är klar finns i [lösa inloggningar](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Kopiera en databas med hjälp av Azure Portal

Om du vill kopiera en databas med hjälp av Azure Portal öppnar du sidan för databasen och klickar sedan på **Kopiera**. 

   ![Databas kopia](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Kopiera en databas med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill kopiera en databas med hjälp av PowerShell använder du cmdleten [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) . 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Ett fullständigt exempel skript finns i [Kopiera en databas till en ny server](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Kopiera en databas med hjälp av Transact-SQL

Logga in på huvud databasen med huvud inloggningen på server nivå eller den inloggning som skapade databasen som du vill kopiera. För att kopieringen av databasen ska lyckas måste inloggningar som inte är huvudobjektet på server nivå vara medlemmar i DBManager-rollen. Mer information om inloggningar och anslutning till-servern finns i [Hantera inloggningar](sql-database-manage-logins.md).

Starta kopieringen av käll databasen med instruktionen [create Database](https://msdn.microsoft.com/library/ms176061.aspx) . Om du kör den här instruktionen initieras databas kopierings processen. Eftersom kopiering av en databas är en asynkron process returneras CREATE DATABASE-uttrycket innan databas kopieringen har slutförts.

### <a name="copy-a-sql-database-to-the-same-server"></a>Kopiera en SQL-databas till samma server

Logga in på huvud databasen med huvud inloggningen på server nivå eller den inloggning som skapade databasen som du vill kopiera. För att kopieringen av databasen ska lyckas måste inloggningar som inte är huvudobjektet på server nivå vara medlemmar i DBManager-rollen.

Detta kommando kopierar Databas1 till en ny databas med namnet Databas2 på samma server. Beroende på databasens storlek kan kopierings åtgärden ta lite tid att slutföra.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopiera en SQL-databas till en annan server

Logga in på mål serverns huvud databas, den SQL Database Server där den nya databasen ska skapas. Använd en inloggning som har samma namn och lösen ord som databas ägaren till käll databasen på käll SQL Databases servern. Inloggningen på mål servern måste också vara medlem i DBManager-rollen eller vara huvudsaklig inloggning på server nivå.

Detta kommando kopierar Databas1 på Server1 till en ny databas med namnet Databas2 på Server2. Beroende på databasens storlek kan kopierings åtgärden ta lite tid att slutföra.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> Båda servrarnas brand väggar måste konfigureras för att tillåta inkommande anslutning från IP-adressen för klienten som utfärdar T-SQL COPY-kommandot.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Kopiera en SQL-databas till en annan prenumeration

Du kan använda stegen som beskrivs i föregående avsnitt för att kopiera databasen till en SQL Database-Server i en annan prenumeration. Se till att du använder en inloggning som har samma namn och lösen ord som databas ägaren till käll databasen och att den är medlem i DBManager-rollen eller är primär inloggning på server nivå. 

> [!NOTE]
> [Azure Portal](https://portal.azure.com) stöder inte kopiering till en annan prenumeration eftersom portalen anropar arm-API: et och använder prenumerations certifikat för att få åtkomst till båda servrarna som ingår i geo-replikering.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>Övervaka förloppet för kopierings åtgärden

Övervaka kopierings processen genom att skicka frågor till vyerna sys. Databass och sys. DM _database_copies. När kopiering pågår är kolumnen **state_desc** i vyn sys. Databass för den nya databasen inställd på **Kopiera**.

* Om kopieringen Miss lyckas anges kolumnen **state_desc** i vyn sys. databases för den nya databasen som **misstänkt**. Kör DROP-instruktionen på den nya databasen och försök igen senare.
* Om kopieringen lyckas är kolumnen **state_desc** i vyn sys. Databass inställd på **online**. Kopieringen är klar och den nya databasen är en vanlig databas som kan ändras oberoende av käll databasen.

> [!NOTE]
> Om du väljer att avbryta kopieringen medan den pågår kör du [Drop Database](https://msdn.microsoft.com/library/ms178613.aspx) -instruktionen på den nya databasen. Om du kör DROP DATABASE-instruktionen på käll databasen avbryts även kopierings processen.

## <a name="resolve-logins"></a>Lösa inloggningar

När den nya databasen är online på mål servern använder du instruktionen [Alter User](https://msdn.microsoft.com/library/ms176060.aspx) för att mappa om användarna från den nya databasen till inloggningar på mål servern. Information om hur du löser överblivna användare finns i [Felsöka överblivna användare](https://msdn.microsoft.com/library/ms175475.aspx). Se även [hur du hanterar Azure SQL Database-säkerhet efter haveri beredskap](sql-database-geo-replication-security-config.md).

Alla användare i den nya databasen behåller de behörigheter som de hade i käll databasen. Användaren som initierade databas kopian blir databas ägaren till den nya databasen och tilldelas en ny säkerhets identifierare (SID). När kopieringen är klar och innan andra användare mappas om, så kan databas ägaren logga in på den nya databasen.

Information om hur du hanterar användare och inloggningar när du kopierar en databas till en annan SQL Database-Server finns i [så här hanterar du Azure SQL Database-säkerhet efter haveri beredskap](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Nästa steg

* Information om inloggningar finns i [Hantera inloggningar](sql-database-manage-logins.md) och [Hantera Azure SQL Database-säkerhet efter haveri beredskap](sql-database-geo-replication-security-config.md).
* Information om hur du exporterar en databas finns i [Exportera databasen till en BACPAC](sql-database-export.md).
