---
title: Kopiera en Azure SQL database | Microsoft Docs
description: Skapa en transaktionsmässigt konsekvent kopia av en befintlig Azure SQL-databas på samma server eller en annan server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 87db25bbfd68aea1b8835211d7b51dfddcd43038
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57899391"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Kopiera en transaktionsmässigt konsekvent kopia av en Azure SQL database

Azure SQL Database erbjuder flera metoder för att skapa en transaktionsmässigt konsekvent kopia av en befintlig Azure SQL-databas på samma server eller en annan server. Du kan kopiera en SQL-databas med hjälp av Azure-portalen, PowerShell eller T-SQL. 

## <a name="overview"></a>Översikt

En databaskopia är en ögonblicksbild av källdatabasen vid tidpunkten för kopiera begäran. Du kan välja samma server eller en annan server, dess tjänstnivå och beräkningsstorleken eller en annan beräkningsstorleken inom samma tjänstnivå (edition). När kopieringen är klar, blir det en fullt fungerande databas för oberoende. Nu kan du uppgradera eller nedgradera den till en utgåva. Inloggningar, användare och behörigheter kan hanteras oberoende av varandra.  

> [!NOTE]
> [Automatisk säkerhetskopiering av databaser](sql-database-automated-backups.md) används när du skapar en databaskopia.

## <a name="logins-in-the-database-copy"></a>Inloggningar i databaskopian

När du kopierar en databas till samma SQL-databasserver, kan du använda samma inloggningar på båda databaserna. Säkerhetsobjektet du använda för att kopiera databasen blir databasägare på den nya databasen. Alla användare, deras behörigheter och deras säkerhetsidentifierare (SID) kopieras till databaskopian.  

När du kopierar en databas till en annan SQL Database-server blir säkerhetsobjekt på den nya servern databasägaren på den nya databasen. Om du använder [innehöll databasanvändare](sql-database-manage-logins.md) för dataåtkomst, se till att båda primära och sekundära databaserna alltid har samma autentiseringsuppgifter, så att när kopieringen är klar kan du omedelbart använda tjänsten med samma autentiseringsuppgifter . 

Om du använder [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), du kan helt eliminera behovet för hantering av autentiseringsuppgifter i kopian. Men när du kopierar databasen till en ny server kanske åtkomst inloggningsbaserade inte fungerar, eftersom inloggningarna inte finns på den nya servern. Läs om hur du hanterar inloggningar när du kopierar en databas till en annan SQL Database-server i [hur du hanterar Azure SQL database-säkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md). 

När kopieringen lyckas och innan andra användare mappas om logga inloggningen som initierade kopierar, databasägaren, in på den nya databasen. För att lösa inloggningar efter kopiering åtgärden har slutförts, se [lösa inloggningar](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Kopiera en databas med hjälp av Azure portal

Öppna sidan för din databas om du vill kopiera en databas med hjälp av Azure-portalen, och klicka sedan på **kopiera**. 

   ![Databaskopian](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Kopiera en databas med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska.

Om du vill kopiera en databas med hjälp av PowerShell, Använd den [New AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet. 

```PowerShell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

En fullständig exempelskript finns i [kopiera en databas till en ny server](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Kopiera en databas med hjälp av Transact-SQL

Logga in på master-databasen med den primära inloggningen på servernivå eller inloggning som skapade databasen som du vill kopiera. Databasen kopiera ska lyckas, måste inloggningar som inte är servernivå vara medlemmar i rollen dbmanager. Mer information om inloggningar och ansluter till servern finns i [hantera inloggningar](sql-database-manage-logins.md).

Börja kopiera källdatabasen med den [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) instruktionen. Kör den här instruktionen initierar databasen kopiering av processen. Eftersom kopiera en databas är en asynkron process, returnerar CREATE DATABASE-instruktionen innan databasen kopieringen har slutförts.

### <a name="copy-a-sql-database-to-the-same-server"></a>Kopiera en SQL-databas till samma server

Logga in på master-databasen med den primära inloggningen på servernivå eller inloggning som skapade databasen som du vill kopiera. Databasen kopiera ska lyckas, måste inloggningar som inte är servernivå vara medlemmar i rollen dbmanager.

Det här kommandot kopierar Databas1 till en ny databas med namnet Databas2 på samma server. Kopiera åtgärden kan ta lite tid att slutföra beroende på databasens storlek.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopiera en SQL-databas till en annan server

Logga in på master-databasen på målservern, SQL Database-servern där den nya databasen ska skapas. Använd en inloggning som har samma namn och lösenord som databasens ägare av källdatabasen på källservern för SQL-databas. Logga in på målservern måste också vara medlem i rollen dbmanager eller vara den primära inloggningen på servernivå.

Det här kommandot kopierar Databas1 på server1 till en ny databas med namnet Databas2 på server2. Kopiera åtgärden kan ta lite tid att slutföra beroende på databasens storlek.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;

## <a name="to-move-a-database-between-subscriptions"></a>Du flyttar en databas mellan prenumerationer

I den [Azure-portalen](https://portal.azure.com), klickar du på **SQL-servrar** och välj sedan den server som är värd för databasen i listan. Klicka på **flytta**, och sedan välja resurser att flytta och prenumeration för att flytta till.

### <a name="monitor-the-progress-of-the-copying-operation"></a>Övervaka förloppet för åtgärden kopiering

Övervaka kopieringen genom att fråga vyerna sys.databases och sys.dm_database_copies. Medan kopiering pågår, den **state_desc** kolumnen i vyn sys.databases för den nya databasen är inställd på **kopiering**.

* Om kopieringen misslyckas den **state_desc** kolumnen i vyn sys.databases för den nya databasen är inställd på **MISSTÄNKER**. Kör DROP-uttryck på den nya databasen och försök igen senare.
* Om kopieringen lyckas den **state_desc** kolumnen i vyn sys.databases för den nya databasen är inställd på **ONLINE**. Kopieringen är klar och den nya databasen är en vanlig databas som kan ändras oberoende av källdatabasen.

> [!NOTE]
> Om du vill avbryta kopieringen medan det pågår kan köra den [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) uttryck på den nya databasen. Du kan också avbryter DROP DATABASE-instruktionen körs på källdatabasen också kopieringen.

## <a name="resolve-logins"></a>Lösa inloggningar

När den nya databasen är online på målservern, använda den [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) -uttrycket för att mappa om användare från den nya databasen med inloggningar på målservern. För att lösa överblivna användare kan se [felsöka överblivna användare](https://msdn.microsoft.com/library/ms175475.aspx). Se även [hur du hanterar Azure SQL database-säkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md).

Alla användare i den nya databasen och behålla de behörigheter som de hade i källdatabasen. Användaren som initierat databaskopian blir databasägare för den nya databasen och tilldelas en ny säkerhetsidentifierare (SID). När kopieringen lyckas och innan andra användare mappas om logga inloggningen som initierade kopierar, databasägaren, in på den nya databasen.

Läs om hur du hanterar användare och inloggningar när du kopierar en databas till en annan SQL Database-server i [hur du hanterar Azure SQL database-säkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Nästa steg

* Information om inloggningar finns i [hantera inloggningar](sql-database-manage-logins.md) och [hur du hanterar Azure SQL database-säkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md).
* Om du vill exportera en databas, se [exportera databasen till en BACPAC](sql-database-export.md).
