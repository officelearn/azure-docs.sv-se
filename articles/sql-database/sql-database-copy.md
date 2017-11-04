---
title: Kopiera en Azure SQL database | Microsoft Docs
description: "Skapa ett konsekvent kopia av en befintlig Azure SQL-databas på samma server eller en annan server."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5aaf6bcd-3839-49b5-8c77-cbdf786e359b
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.date: 06/15/2017
ms.author: carlrab
ms.workload: On Demand
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 7999c52a81165a9f8efb2f1302d04c9cb851faca
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="copy-an-azure-sql-database"></a>Kopiera en Azure SQL database

Azure SQL Database erbjuder flera metoder för att skapa en konsekvent transaktionellt kopia av en befintlig Azure SQL-databas på samma server eller en annan server. Du kan kopiera en SQL-databas med hjälp av Azure-portalen, PowerShell eller T-SQL. 

## <a name="overview"></a>Översikt

En databaskopia är en ögonblicksbild av källdatabasen vid tiden för copy-begäran. Du kan välja samma server eller en annan server, dess tjänstnivå och prestandanivå eller en annan prestandanivå inom samma tjänstnivån (edition). När kopieringen är klar, blir den en fullt fungerande oberoende databas. Nu kan du uppgradera eller nedgradera till en utgåva. Inloggningar, användare och behörigheter kan hanteras oberoende av varandra.  

## <a name="logins-in-the-database-copy"></a>Inloggningar i databaskopian

När du kopierar en databas till samma logiska server kan samma inloggningar användas på båda databaserna. Säkerhetsobjektet du använda för att kopiera databasen blir databasägaren på den nya databasen. Alla användare och deras behörigheter sina säkerhetsidentifierare (SID) kopieras till databaskopian.  

När du kopierar en databas till en annan logisk server blir säkerhetsobjekt på den nya servern databasägaren på den nya databasen. Om du använder [innehöll databasanvändare](sql-database-manage-logins.md) för dataåtkomst, se till att både de primära och sekundära databaserna alltid har samma autentiseringsuppgifter så att när kopieringen är klar du omedelbart åt den med samma autentiseringsuppgifter . 

Om du använder [Azure Active Directory](../active-directory/active-directory-whatis.md), du kan helt eliminera behovet av att hantera autentiseringsuppgifter i kopian. Men när du kopierar databasen till en ny server kanske inloggnings-baserad åtkomst inte fungerar, eftersom inloggningar inte finns på den nya servern. Läs om hur du hanterar inloggningar när du kopierar en databas till en annan logisk server i [hur du hanterar Azure SQL database-säkerhet efter katastrofåterställning](sql-database-geo-replication-security-config.md). 

När kopieringen lyckas och innan andra användare mappas om logga inloggningen som initierade kopierar, databasägaren, in på den nya databasen. Lös inloggningar efter kopiering åtgärden har slutförts, se [lösa inloggningar](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Kopiera en databas med hjälp av Azure portal

Öppna sidan för databasen om du vill kopiera en databas med hjälp av Azure portal och klicka sedan på **kopiera**. 

   ![Databaskopian](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Kopiera en databas med hjälp av PowerShell

Om du vill kopiera en databas med hjälp av PowerShell, Använd den [ny AzureRmSqlDatabaseCopy](/powershell/module/azurerm.sql/new-azurermsqldatabasecopy) cmdlet. 

```PowerShell
New-AzureRmSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

En fullständig exempelskript finns [kopiera en databas till en ny server](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Kopiera en databas med hjälp av Transact-SQL

Logga in på master-databasen med de huvudsaklig inloggningen på servernivå eller inloggning som skapade databasen som du vill kopiera. För kopiering av databasen ska lyckas, måste inloggningar som inte är servernivå huvudnamn vara medlemmar i rollen dbmanager. Mer information om inloggningar och ansluter till servern finns [hantera inloggningar](sql-database-manage-logins.md).

Börja kopiera källdatabasen med den [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) instruktionen. Kör den här instruktionen initierar databasen kopiera processen. Eftersom kopiera en databas är en asynkron åtgärd, returnerar instruktionen CREATE DATABASE innan databasen kopieringen är klar.

### <a name="copy-a-sql-database-to-the-same-server"></a>Kopiera en SQL-databas på samma server
Logga in på master-databasen med de huvudsaklig inloggningen på servernivå eller inloggning som skapade databasen som du vill kopiera. För kopiering av databasen ska lyckas, måste inloggningar som inte är servernivå huvudnamn vara medlemmar i rollen dbmanager.

Det här kommandot kopieras Database1 till en ny databas med namnet Database2 på samma server. Kopiera åtgärden kan ta lite tid att slutföra beroende på storleken på databasen.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopiera en SQL-databas till en annan server

Logga in på master-databasen på målservern, SQL database-server där den nya databasen ska skapas. Använd en inloggning som har samma namn och lösenord som databasägaren källdatabasens på källservern för SQL-databasen. Logga in på målservern måste också vara medlem i rollen dbmanager eller att den huvudsaklig inloggningen på servernivå.

Det här kommandot kopieras Database1 på server1 till en ny databas med namnet Database2 på server2. Kopiera åtgärden kan ta lite tid att slutföra beroende på storleken på databasen.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;


### <a name="monitor-the-progress-of-the-copying-operation"></a>Övervaka förloppet för kopiering igen

Övervaka kopieringen genom att fråga vyerna sys.databases och sys.dm_database_copies. När kopieringen pågår, den **state_desc** kolumnen i vyn sys.databases för den nya databasen är inställd på **kopiering**.

* Om kopiering misslyckas den **state_desc** kolumnen i vyn sys.databases för den nya databasen är inställd på **MISSTÄNKER**. Kör instruktionen släpp på den nya databasen och försök igen senare.
* Om kopiering lyckas den **state_desc** kolumnen i vyn sys.databases för den nya databasen är inställd på **ONLINE**. Kopieringen har slutförts och den nya databasen är en vanlig databas som kan ändras oberoende av källdatabasen.

> [!NOTE]
> Om du vill avbryta kopieringen medan det pågår kan köra den [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) satsen på den nya databasen. Du kan också avbryter köra instruktionen DROP DATABASE på källdatabasen också kopieringen.
> 

## <a name="resolve-logins"></a>Lös inloggningar

När den nya databasen är online på målservern, använda den [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) instruktionen för att mappa användare från den nya databasen inloggningar på målservern. Du löser ägarlösa användare se [felsöka ägarlösa användare](https://msdn.microsoft.com/library/ms175475.aspx). Se även [hur du hanterar Azure SQL database-säkerhet efter katastrofåterställning](sql-database-geo-replication-security-config.md).

Alla användare i den nya databasen behålla de behörigheter som de hade i källdatabasen. Användaren som initierade databaskopian blir databasens ägare för den nya databasen och tilldelas en ny säkerhetsidentifierare (SID). När kopieringen lyckas och innan andra användare mappas om logga inloggningen som initierade kopierar, databasägaren, in på den nya databasen.

Läs om hur du hanterar användare och inloggningar när du kopierar en databas till en annan logisk server i [hur du hanterar Azure SQL database-säkerhet efter katastrofåterställning](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Nästa steg

* Information om inloggningar finns [hantera inloggningar](sql-database-manage-logins.md) och [hur du hanterar Azure SQL database-säkerhet efter katastrofåterställning](sql-database-geo-replication-security-config.md).
* Om du vill exportera en databas finns [exportera databasen till en BACPAC](sql-database-export.md).
