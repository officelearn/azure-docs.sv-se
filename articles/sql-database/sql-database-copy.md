---
title: Kopiera en databas
description: Skapa en transaktionskonsekvent kopia av en befintlig Azure SQL-databas på antingen samma server eller en annan server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 02/24/2020
ms.openlocfilehash: 7e4744627cfd08874e07bb126df048ea3e644f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473752"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Kopiera en transaktionskonsekvent kopia av en Azure SQL-databas

Azure SQL Database innehåller flera metoder för att skapa en transaktionskonsekvent kopia av en befintlig Azure SQL-databas ([en enda databas](sql-database-single-database.md)) på antingen samma server eller en annan server. Du kan kopiera en SQL-databas med hjälp av Azure-portalen, PowerShell eller T-SQL.

## <a name="overview"></a>Översikt

En databaskopia är en ögonblicksbild av källdatabasen från och med tidpunkten för kopieringsbegäran. Du kan välja samma server eller en annan server. Du kan också välja att behålla dess tjänstnivå och beräkningsstorlek, eller använda en annan beräkningsstorlek inom samma tjänstnivå (utgåva). När kopian är klar blir den en fullt fungerande, oberoende databas. Nu kan du uppgradera eller nedgradera den till valfri utgåva. Inloggningar, användare och behörigheter kan hanteras oberoende av dem. Kopian skapas med hjälp av geo-replikeringsteknik och när sådd är klar avslutas geo-replikeringslänken automatiskt. Alla krav för att använda geo-replikering gäller för databaskopieringsåtgärden. Mer information finns i [Översikt över aktiv georeplikering.](sql-database-active-geo-replication.md)

> [!NOTE]
> [Automatiska säkerhetskopieringar](sql-database-automated-backups.md) av databaser används när du skapar en databaskopia.

## <a name="logins-in-the-database-copy"></a>Inloggningar i databaskopian

När du kopierar en databas till samma SQL Database-server kan samma inloggningar användas i båda databaserna. Det säkerhetsobjekt som du använder för att kopiera databasen blir databasägaren i den nya databasen. 

När du kopierar en databas till en annan SQL Database-server blir säkerhetsobjektet som initierade kopieringen på målservern ägare till den nya databasen. 

Oavsett målserver kopieras alla databasanvändare, deras behörigheter och deras säkerhetsidentifierare (SID) till databaskopian. Om du använder [innehållna databasanvändare](sql-database-manage-logins.md) för dataåtkomst säkerställer du att den kopierade databasen har samma användarautentiseringsuppgifter, så att du direkt kan komma åt den med samma autentiseringsuppgifter när kopian är klar.

Om du använder inloggningar på servernivå för dataåtkomst och kopierar databasen till en annan server kanske den inloggningsbaserade åtkomsten inte fungerar. Detta kan inträffa eftersom inloggningarna inte finns på målservern, eller på grund av att deras lösenord och säkerhetsidentifierare (SID) är olika. Mer information om hur du hanterar inloggningar när du kopierar en databas till en annan SQL Database-server finns i [Så här hanterar du Azure SQL-databassäkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md). När kopieringen till en annan server har slutförts och innan andra användare mappas om kan endast inloggningen som är associerad med databasägaren eller serveradministratören logga in på den kopierade databasen. Information om hur du löser inloggningar och upprättar dataåtkomst när kopieringen är klar finns i [Lös inloggningar](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Kopiera en databas med hjälp av Azure-portalen

Om du vill kopiera en databas med hjälp av Azure-portalen öppnar du sidan för databasen och klickar sedan på **Kopiera**.

   ![Databaskopia](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell-or-azure-cli"></a>Kopiera en databas med PowerShell eller Azure CLI

Om du vill kopiera en databas använder du följande exempel.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

För PowerShell använder du cmdleten [New-AzSqlDatabaseCopy.](/powershell/module/az.sql/new-azsqldatabasecopy)

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. AzureRM-modulen fortsätter att ta emot buggfixar fram till åtminstone december 2020.  Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Databaskopian är en asynkron åtgärd, men måldatabasen skapas omedelbart efter att begäran har accepterats. Om du behöver avbryta kopieringen medan du fortfarande pågår släpper du måldatabasen med cmdleten [Ta bort AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase)

Ett fullständigt exempel på PowerShell-skript finns i [Kopiera en databas till en ny server](scripts/sql-database-copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Databaskopian är en asynkron åtgärd, men måldatabasen skapas omedelbart efter att begäran har accepterats. Om du behöver avbryta kopieringen medan du fortfarande pågår, släpp måldatabasen med kommandot [az sql db delete.](/cli/azure/sql/db#az-sql-db-delete)

* * *

## <a name="rbac-roles-to-manage-database-copy"></a>RBAC-roller för att hantera databaskopia

Om du vill skapa en databaskopia måste du ha följande roller

- Prenumerationsägare eller
- SQL Server-deltagarroll eller
- Anpassad roll på käll- och måldatabaserna med följande behörighet:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Om du vill avbryta en databaskopia måste du ha följande roller

- Prenumerationsägare eller
- SQL Server-deltagarroll eller
- Anpassad roll på käll- och måldatabaserna med följande behörighet:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Om du vill hantera databaskopiering med Azure-portalen behöver du också följande behörigheter:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

Om du vill se åtgärderna under distributioner i resursgruppen på portalen, åtgärder över flera resursleverantörer, inklusive SQL-åtgärder, behöver du dessa ytterligare RBAC-roller:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="copy-a-database-by-using-transact-sql"></a>Kopiera en databas med Transact-SQL

Logga in i huvuddatabasen med serveradministratörsinloggningen eller inloggningen som skapade databasen som du vill kopiera. För att databaskopian ska lyckas måste inloggningar som `dbmanager` inte är serveradministratör vara medlemmar i rollen. Mer information om inloggningar och anslutning till servern finns i [Hantera inloggningar](sql-database-manage-logins.md).

Börja kopiera källdatabasen med [SKAPA DATABAS ... SOM KOPIA AV](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) utdrag. T-SQL-uttrycket fortsätter att köras tills databaskopieringen är klar.

> [!NOTE]
> Om du avslutar T-SQL-uttrycket avslutas inte databaskopieringen. Om du vill avsluta åtgärden släpper du måldatabasen.
>

### <a name="copy-a-sql-database-to-the-same-server"></a>Kopiera en SQL-databas till samma server

Logga in i huvuddatabasen med serveradministratörsinloggningen eller inloggningen som skapade databasen som du vill kopiera. För att databaskopiering ska lyckas måste inloggningar som `dbmanager` inte är serveradministratör vara medlemmar i rollen.

Det här kommandot kopierar Database1 till en ny databas med namnet Database2 på samma server. Beroende på databasens storlek kan kopieringen ta lite tid att slutföra.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopiera en SQL-databas till en annan server

Logga in i huvuddatabasen för målservern där den nya databasen ska skapas. Använd en inloggning som har samma namn och lösenord som databasägaren för källdatabasen på källservern. Inloggningen på målservern måste också `dbmanager` vara medlem i rollen eller vara serveradministratörens inloggning.

Det här kommandot kopierar Database1 på server1 till en ny databas med namnet Database2 på server2. Beroende på databasens storlek kan kopieringen ta lite tid att slutföra.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Båda servrarnas brandväggar måste konfigureras för att tillåta inkommande anslutning från IP för klienten som utfärdar T-SQL CREATE DATABASE ... SOM KOPIA AV kommandot.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Kopiera en SQL-databas till en annan prenumeration

Du kan använda stegen i [Kopiera en SQL-databas till ett annat serveravsnitt](#copy-a-sql-database-to-a-different-server) för att kopiera databasen till en SQL Database-server i en annan prenumeration med T-SQL. Se till att du använder en inloggning som har samma namn och lösenord som databasägaren för källdatabasen. Dessutom måste inloggningen vara medlem `dbmanager` i rollen eller en serveradministratör, på både käll- och målservrar.

> [!NOTE]
> [Azure-portalen](https://portal.azure.com), PowerShell och Azure CLI stöder inte databaskopia till en annan prenumeration.

### <a name="monitor-the-progress-of-the-copying-operation"></a>Övervaka förloppet för kopieringen

Övervaka kopieringsprocessen genom att fråga [sys.databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)och [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) vyer. Medan kopiering pågår är **kolumnen state_desc** i vyn sys.database för den nya databasen inställd på **KOPIERING**.

* Om kopieringen misslyckas är **kolumnen state_desc** i vyn sys.database för den nya databasen inställd **på MISSTÄNKT**. Kör DROP-satsen på den nya databasen och försök igen senare.
* Om kopieringen lyckas är **kolumnen state_desc** i vyn sys.database för den nya databasen inställd på **ONLINE**. Kopieringen är klar och den nya databasen är en vanlig databas som kan ändras oberoende av källdatabasen.

> [!NOTE]
> Om du bestämmer dig för att avbryta kopieringen medan den pågår kör du [DROP DATABASE-satsen](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) i den nya databasen.

> [!IMPORTANT]
> Om du behöver skapa en kopia med ett betydligt mindre servicemål än källan kanske måldatabasen inte har tillräckliga resurser för att slutföra dirigeringen och det kan leda till att kopieringen misslyckas. I det här fallet använder du en begäran om geoåterställning för att skapa en kopia på en annan server och/eller en annan region. Se [Återställa en Azure SQL-databas med hjälp av databassäkerhetskopior](sql-database-recovery-using-backups.md#geo-restore) för mer information.

## <a name="resolve-logins"></a>Lösa inloggningar

När den nya databasen är online på målservern använder du [ALTER USER-satsen](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) för att mappa om användarna från den nya databasen till inloggningar på målservern. Lös överblivna användare finns [i Felsöka överblivna användare](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Se även [Hur du hanterar Azure SQL-databassäkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md).

Alla användare i den nya databasen behåller de behörigheter som de hade i källdatabasen. Användaren som initierade databaskopian blir databasägare i den nya databasen. När kopieringen har slutförts och innan andra användare mappas om kan endast databasägaren logga in på den nya databasen.

Mer information om hur du hanterar användare och inloggningar när du kopierar en databas till en annan SQL Database-server finns i [Hantera Azure SQL-databassäkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md).

## <a name="database-copy-errors"></a>Fel i databaskopiering

Följande fel kan uppstå när du kopierar en databas i Azure SQL Database. Mer information finns i [Kopiera en Azure SQL Database](sql-database-copy.md).

| Felkod | Severity | Beskrivning |
| ---:| ---:|:--- |
| 40635 |16 |Klient med IP-adressen %.&#x2a;ls är tillfälligt inaktiverad. |
| 40637 |16 |Skapa databaskopia är för närvarande inaktiverad. |
| 40561 |16 |Databaskopian misslyckades. Antingen finns inte käll- eller måldatabasen. |
| 40562 |16 |Databaskopian misslyckades. Källdatabasen har tagits bort. |
| 40563 |16 |Databaskopian misslyckades. Måldatabasen har tagits bort. |
| 40564 |16 |Databaskopian misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen. |
| 40565 |16 |Databaskopian misslyckades. Det är inte mer än 1 samtidig databaskopia från samma källa som tillåts. Släpp måldatabasen och försök igen senare. |
| 40566 |16 |Databaskopian misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen. |
| 40567 |16 |Databaskopian misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen. |
| 40568 |16 |Databaskopian misslyckades. Källdatabasen har blivit otillgänglig. Släpp måldatabasen och försök igen. |
| 40569 |16 |Databaskopian misslyckades. Måldatabasen har blivit otillgänglig. Släpp måldatabasen och försök igen. |
| 40570 |16 |Databaskopian misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen senare. |
| 40571 |16 |Databaskopian misslyckades på grund av ett internt fel. Släpp måldatabasen och försök igen senare. |

## <a name="next-steps"></a>Nästa steg

- Information om inloggningar finns i [Hantera inloggningar](sql-database-manage-logins.md) och [hur du hanterar Azure SQL-databassäkerhet efter haveriberedskap](sql-database-geo-replication-security-config.md).
- Information om hur du exporterar en databas finns i [Exportera databasen till en BACPAC](sql-database-export.md).
