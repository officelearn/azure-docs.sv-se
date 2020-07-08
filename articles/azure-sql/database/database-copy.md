---
title: Kopiera en databas
description: Skapa en transaktions konsekvent kopia av en befintlig databas i Azure SQL Database antingen på samma server eller på en annan server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 02/24/2020
ms.openlocfilehash: d92882014f66234be8a8b1d7063dae866ec6f230
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84045300"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Kopiera en transaktions konsekvent kopia av en databas i Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database tillhandahåller flera metoder för att skapa en transaktions konsekvent kopia av en befintlig [databas](single-database-overview.md) på samma server eller en annan server. Du kan kopiera en databas med hjälp av Azure Portal, PowerShell eller T-SQL.

## <a name="overview"></a>Översikt

En databas kopia är en ögonblicks bild av käll databasen vid tidpunkten för kopierings förfrågan. Du kan välja samma server eller en annan server. Du kan också välja att behålla tjänst nivån och beräknings storleken, eller använda en annan beräknings storlek inom samma service nivå (utgåva). När kopieringen är klar blir den en fullständigt fungerande, oberoende databas. I det här läget kan du uppgradera eller nedgradera det till vilken version som helst. Inloggningar, användare och behörigheter kan hanteras oberoende av varandra. Kopian skapas med hjälp av Geo-Replication-tekniken och när dirigeringen är klar avslutas geo-replikeringslänken automatiskt. Alla krav för att använda geo-replikering gäller för databas kopierings åtgärden. Mer information finns i [Översikt över aktiv geo-replikering](active-geo-replication-overview.md) .

> [!NOTE]
> [Automatiserade databas säkerhets kopieringar](automated-backups-overview.md) används när du skapar en databas kopia.

## <a name="logins-in-the-database-copy"></a>Inloggningar i databas kopian

När du kopierar en databas till samma server kan samma inloggningar användas på båda databaserna. Säkerhets objekt som du använder för att kopiera databasen blir databas ägaren på den nya databasen.

När du kopierar en databas till en annan server blir det säkerhets objekt som initierade kopierings åtgärden på mål servern ägare till den nya databasen.

Oavsett mål servern kopieras alla databas användare, deras behörigheter och deras säkerhets identifierare (sid) till databas kopian. Om du använder [inneslutna databas användare](logins-create-manage.md) för data åtkomst säkerställer du att den kopierade databasen har samma användarautentiseringsuppgifter, så att när kopieringen är klar kan du omedelbart komma åt den med samma autentiseringsuppgifter.

Om du använder inloggningar på server nivå för data åtkomst och kopierar databasen till en annan server, kanske inloggnings-baserad åtkomst inte fungerar. Detta kan inträffa eftersom inloggningarna inte finns på mål servern eller på grund av att deras lösen ord och säkerhets identifierare (sid) skiljer sig åt. Information om hur du hanterar inloggningar när du kopierar en databas till en annan server finns i [hantera Azure SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md). När kopierings åtgärden till en annan server lyckas och innan andra användare mappas om, är det bara inloggningen som är kopplad till databas ägaren eller så kan Server administratören logga in på den kopierade databasen. Information om hur du löser inloggningar och upprättar data åtkomst när kopieringen är klar finns i [lösa inloggningar](#resolve-logins).

## <a name="copy-using-the-azure-portal"></a>Kopiera med Azure Portal

Om du vill kopiera en databas med hjälp av Azure Portal öppnar du sidan för databasen och klickar sedan på **Kopiera**.

   ![Databas kopia](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Kopiera med PowerShell eller Azure CLI

Använd följande exempel för att kopiera en databas.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För PowerShell använder du cmdleten [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) .

> [!IMPORTANT]
> PowerShell-modulen Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för AZ. SQL-modulen. AzureRM-modulen kommer att fortsätta att ta emot fel korrigeringar fram till minst december 2020.  Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell AZ-modulen](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Databas kopieringen är en asynkron åtgärd, men mål databasen skapas direkt efter att begäran har godkänts. Om du vill avbryta kopieringen medan du fortfarande pågår släpper du mål databasen med hjälp av cmdleten [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .

Ett fullständigt exempel på PowerShell-skript finns i [Kopiera en databas till en ny server](scripts/copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Databas kopieringen är en asynkron åtgärd, men mål databasen skapas direkt efter att begäran har godkänts. Om du vill avbryta kopieringen medan du fortfarande pågår släpper du mål databasen med kommandot [AZ SQL DB Delete](/cli/azure/sql/db#az-sql-db-delete) .

* * *

## <a name="copy-using-transact-sql"></a>Kopiera med Transact-SQL

Logga in på huvud databasen med Server Administratörs inloggning eller inloggning som skapade databasen som du vill kopiera. För att databas kopieringen ska lyckas måste inloggningar som inte är Server administratör vara medlemmar i `dbmanager` rollen. Mer information om inloggningar och anslutning till-servern finns i [Hantera inloggningar](logins-create-manage.md).

Starta kopieringen av käll databasen med [create-databasen... SOM en kopia av](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) instruktionen. T-SQL-instruktionen fortsätter att köras tills databas kopierings åtgärden har slutförts.

> [!NOTE]
> Om du avslutar T-SQL-instruktionen avbryts inte databas kopierings åtgärden. Avsluta åtgärden genom att släppa mål databasen.
>

### <a name="copy-to-the-same-server"></a>Kopiera till samma server

Logga in på huvud databasen med Server Administratörs inloggning eller inloggning som skapade databasen som du vill kopiera. För att kopieringen av databasen ska lyckas måste inloggningar som inte är Server administratör vara medlemmar i `dbmanager` rollen.

Detta kommando kopierar Databas1 till en ny databas med namnet Databas2 på samma server. Beroende på databasens storlek kan kopierings åtgärden ta lite tid att slutföra.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-a-different-server"></a>Kopiera till en annan server

Logga in på huvud databasen på mål servern där den nya databasen ska skapas. Använd en inloggning som har samma namn och lösen ord som databas ägaren till käll databasen på käll servern. Inloggningen på mål servern måste också vara medlem i `dbmanager` rollen eller vara Server Administratörs inloggning.

Detta kommando kopierar Databas1 på Server1 till en ny databas med namnet Databas2 på Server2. Beroende på databasens storlek kan kopierings åtgärden ta lite tid att slutföra.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Båda servrarnas brand väggar måste konfigureras för att tillåta inkommande anslutning från IP-adressen för klienten som utfärdar T-SQL CREATE-databasen... SOM en kopia av kommandot.

### <a name="copy-to-a-different-subscription"></a>Kopiera till en annan prenumeration

Du kan använda stegen i avsnittet [Kopiera en SQL Database till en annan server](#copy-to-a-different-server) för att kopiera databasen till en server i en annan prenumeration med hjälp av T-SQL. Se till att du använder en inloggning som har samma namn och lösen ord som databas ägaren till käll databasen. Dessutom måste inloggningen vara medlem i `dbmanager` rollen eller en Server administratör på både käll-och mål servrar.

> [!NOTE]
> [Azure Portal](https://portal.azure.com), PowerShell och Azure CLI stöder inte databas kopiering till en annan prenumeration.

## <a name="monitor-the-progress-of-the-copying-operation"></a>Övervaka förloppet för kopierings åtgärden

Övervaka kopierings processen genom att skicka frågor till vyerna [sys. Databass](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys. dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)och [sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) . När kopieringen pågår är kolumnen **state_desc** i vyn sys. Databass för den nya databasen som **kopia.**

* Om kopieringen Miss lyckas är kolumnen **state_desc** i vyn sys. Databass för den nya databasen **misstänkt**. Kör DROP-instruktionen på den nya databasen och försök igen senare.
* Om kopieringen lyckas anges kolumnen **state_desc** i vyn sys. Databass för den nya databasen som **online**. Kopieringen är klar och den nya databasen är en vanlig databas som kan ändras oberoende av käll databasen.

> [!NOTE]
> Om du väljer att avbryta kopieringen medan den pågår kör du [Drop Database](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) -instruktionen på den nya databasen.

> [!IMPORTANT]
> Om du behöver skapa en kopia med ett betydligt mindre tjänst mål än källan, kanske mål databasen inte har tillräckligt med resurser för att slutföra initierings processen och det kan leda till att kopieringen Miss lyckas. I det här scenariot använder du en geo-Restore-begäran för att skapa en kopia på en annan server och/eller en annan region. Mer information finns i [återställa en Azure SQL Database med hjälp av databas säkerhets kopior](recovery-using-backups.md#geo-restore) .

## <a name="rbac-roles-to-manage-database-copy"></a>RBAC-roller för att hantera databas kopia

Om du vill skapa en databas kopia måste du ha följande roller

* Prenumerations ägare eller
* SQL Server deltagar roll eller
* Anpassad roll för käll-och mål databaserna med följande behörighet:

   Microsoft. SQL/Servers/databaser/läsa Microsoft. SQL/Servers/databaser/skriva

Om du vill avbryta en databas kopia måste du ha följande roller

* Prenumerations ägare eller
* SQL Server deltagar roll eller
* Anpassad roll för käll-och mål databaserna med följande behörighet:

   Microsoft. SQL/Servers/databaser/läsa Microsoft. SQL/Servers/databaser/skriva

Om du vill hantera databas kopieringen med hjälp av Azure Portal måste du också ha följande behörigheter:

   Microsoft. Resources/Subscriptions/resurser/Läs Microsoft. Resources/Subscriptions/Resources/Write Microsoft. Resources/Deployments/Read Microsoft. Resources/Deployments/Write Microsoft. Resources/operationstatuses/Read

Om du vill se åtgärder under distributioner i resurs gruppen på portalen, så behöver du flera olika resurs leverantörer, inklusive SQL-åtgärder, du behöver dessa ytterligare RBAC-roller:

   Microsoft. Resources/Subscriptions/ResourceGroups/Deployments/Operations/Read Microsoft. Resources/Subscriptions/ResourceGroups/distributions/operationstatuses/Read

## <a name="resolve-logins"></a>Lösa inloggningar

När den nya databasen är online på mål servern använder du instruktionen [Alter User](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) för att mappa om användarna från den nya databasen till inloggningar på mål servern. Information om hur du löser överblivna användare finns i [Felsöka överblivna användare](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Se även [hur du hanterar Azure SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md).

Alla användare i den nya databasen behåller de behörigheter som de hade i käll databasen. Den användare som initierade databas kopian blir databas ägaren till den nya databasen. När kopieringen är klar och innan andra användare mappas om kan bara databas ägaren logga in på den nya databasen.

Information om hur du hanterar användare och inloggningar när du kopierar en databas till en annan server finns i [hantera Azure SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md).

## <a name="database-copy-errors"></a>Databas kopierings fel

Följande fel kan uppstå när du kopierar en databas i Azure SQL Database. Mer information finns i [Kopiera en Azure SQL Database](database-copy.md).

| Felkod | Severity | Beskrivning |
| ---:| ---:|:--- |
| 40635 |16 |Klienten med IP-adressen%. &#x2a;LS är tillfälligt inaktive rad. |
| 40637 |16 |Skapa databas kopiering har inaktiverats för tillfället. |
| 40561 |16 |Databas kopieringen misslyckades. Käll-eller mål databasen finns inte. |
| 40562 |16 |Databas kopieringen misslyckades. Käll databasen har släppts. |
| 40563 |16 |Databas kopieringen misslyckades. Mål databasen har släppts. |
| 40564 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen. |
| 40565 |16 |Databas kopieringen misslyckades. Högst 1 samtidiga databas kopiering från samma källa tillåts. Släpp mål databasen och försök igen senare. |
| 40566 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen. |
| 40567 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen. |
| 40568 |16 |Databas kopieringen misslyckades. Käll databasen är inte tillgänglig. Släpp mål databasen och försök igen. |
| 40569 |16 |Databas kopieringen misslyckades. Mål databasen är inte tillgänglig. Släpp mål databasen och försök igen. |
| 40570 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen senare. |
| 40571 |16 |Databas kopieringen misslyckades på grund av ett internt fel. Släpp mål databasen och försök igen senare. |

## <a name="next-steps"></a>Nästa steg

* Information om inloggningar finns i [Hantera inloggningar](logins-create-manage.md) och [Hantera Azure SQL Database säkerhet efter haveri beredskap](active-geo-replication-security-configure.md).
* Information om hur du exporterar en databas finns i [Exportera databasen till en BACPAC](database-export.md).
