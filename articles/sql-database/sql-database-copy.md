---
title: Kopiera en databas
description: Skapa en transaktions konsekvent kopia av en befintlig Azure SQL-databas på antingen samma server eller en annan server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 11/14/2019
ms.openlocfilehash: e1df345fb9a89972ad1857a937c22d6e10ad1fba
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289415"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Kopiera en transaktions konsekvent kopia av en Azure SQL-databas

Azure SQL Database innehåller flera metoder för att skapa en transaktions konsekvent kopia av en befintlig Azure SQL-databas ([enkel databas](sql-database-single-database.md)) på samma server eller en annan server. Du kan kopiera en SQL-databas med hjälp av Azure Portal, PowerShell eller T-SQL.

## <a name="overview"></a>Översikt

En databas kopia är en ögonblicks bild av käll databasen vid tidpunkten för kopierings förfrågan. Du kan välja samma server eller en annan server. Du kan också välja att behålla tjänst nivån och beräknings storleken, eller använda en annan beräknings storlek inom samma service nivå (utgåva). När kopieringen är klar blir den en fullständigt fungerande, oberoende databas. I det här läget kan du uppgradera eller nedgradera det till vilken version som helst. Inloggningar, användare och behörigheter kan hanteras oberoende av varandra. Kopian skapas med hjälp av Geo-Replication-tekniken och när dirigeringen är klar avslutas geo-replikeringslänken automatiskt. Alla krav för att använda geo-replikering gäller för databas kopierings åtgärden. Mer information finns i [Översikt över aktiv geo-replikering](sql-database-active-geo-replication.md) .

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

Använd följande exempel för att kopiera en databas.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

För PowerShell använder du cmdleten [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) .

> [!IMPORTANT]
> PowerShell-modulen Azure Resource Manager (RM) stöds fortfarande av Azure SQL Database, men all framtida utveckling är för AZ. SQL-modulen. AzureRM-modulen kommer att fortsätta att ta emot fel korrigeringar fram till minst december 2020.  Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell AZ-modulen](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Databas kopieringen är en asynkron åtgärd, men mål databasen skapas direkt efter att begäran har godkänts. Om du vill avbryta kopieringen medan du fortfarande pågår släpper du mål databasen med hjälp av cmdleten [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azure-cli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Databas kopieringen är en asynkron åtgärd, men mål databasen skapas direkt efter att begäran har godkänts. Om du vill avbryta kopieringen medan du fortfarande pågår släpper du mål databasen med kommandot [AZ SQL DB Delete](/cli/azure/sql/db#az-sql-db-delete) .

* * *

Ett fullständigt exempel skript finns i [Kopiera en databas till en ny server](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="rbac-roles-to-manage-database-copy"></a>RBAC-roller för att hantera databas kopia

Om du vill skapa en databas kopia måste du ha följande roller

- Prenumerations ägare eller
- SQL Server deltagar roll eller
- Anpassad roll för käll-och mål databaserna med följande behörighet:

   Microsoft. SQL/Servers/databaser/läsa Microsoft. SQL/Servers/databaser/skriva

Om du vill avbryta en databas kopia måste du ha följande roller

- Prenumerations ägare eller
- SQL Server deltagar roll eller
- Anpassad roll för käll-och mål databaserna med följande behörighet:

   Microsoft. SQL/Servers/databaser/läsa Microsoft. SQL/Servers/databaser/skriva

Om du vill hantera databas kopiering med Azure Portal måste du också ha följande behörigheter:

   Microsoft. Resources/Subscriptions/resurser/Läs Microsoft. Resources/Subscriptions/Resources/Write Microsoft. Resources/Deployments/Read Microsoft. Resources/Deployments/Write Microsoft. Resources/operationstatuses/Read

Om du vill se åtgärder under distributioner i resurs gruppen på portalen, så behöver du flera olika resurs leverantörer, inklusive SQL-åtgärder, du behöver dessa ytterligare RBAC-roller:

   Microsoft. Resources/Subscriptions/ResourceGroups/Deployments/Operations/Read Microsoft. Resources/Subscriptions/ResourceGroups/distributions/operationstatuses/Read

## <a name="copy-a-database-by-using-transact-sql"></a>Kopiera en databas med hjälp av Transact-SQL

Logga in på huvud databasen med huvud inloggningen på server nivå eller den inloggning som skapade databasen som du vill kopiera. För att kopieringen av databasen ska lyckas måste inloggningar som inte är huvudobjektet på server nivå vara medlemmar i DBManager-rollen. Mer information om inloggningar och anslutning till-servern finns i [Hantera inloggningar](sql-database-manage-logins.md).

Starta kopieringen av käll databasen med instruktionen [create Database](https://msdn.microsoft.com/library/ms176061.aspx) . Om du kör den här instruktionen initieras databas kopierings processen. Eftersom kopiering av en databas är en asynkron process returneras CREATE DATABASE-uttrycket innan databas kopieringen har slutförts.

### <a name="copy-a-sql-database-to-the-same-server"></a>Kopiera en SQL-databas till samma server

Logga in på huvud databasen med huvud inloggningen på server nivå eller den inloggning som skapade databasen som du vill kopiera. För att kopieringen av databasen ska lyckas måste inloggningar som inte är huvudobjektet på server nivå vara medlemmar i DBManager-rollen.

Detta kommando kopierar Databas1 till en ny databas med namnet Databas2 på samma server. Beroende på databasens storlek kan kopierings åtgärden ta lite tid att slutföra.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopiera en SQL-databas till en annan server

Logga in på mål serverns huvud databas, den SQL Database Server där den nya databasen ska skapas. Använd en inloggning som har samma namn och lösen ord som databas ägaren till käll databasen på käll SQL Databases servern. Inloggningen på mål servern måste också vara medlem i DBManager-rollen eller vara huvudsaklig inloggning på server nivå.

Detta kommando kopierar Databas1 på Server1 till en ny databas med namnet Databas2 på Server2. Beroende på databasens storlek kan kopierings åtgärden ta lite tid att slutföra.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Båda servrarnas brand väggar måste konfigureras för att tillåta inkommande anslutning från IP-adressen för klienten som utfärdar T-SQL COPY-kommandot.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Kopiera en SQL-databas till en annan prenumeration

Du kan använda stegen som beskrivs i föregående avsnitt för att kopiera databasen till en SQL Database-Server i en annan prenumeration. Se till att du använder en inloggning som har samma namn och lösen ord som databas ägaren till käll databasen och att den är medlem i DBManager-rollen eller är primär inloggning på server nivå. 

> [!NOTE]
> [Azure Portal](https://portal.azure.com) stöder inte kopiering till en annan prenumeration eftersom portalen anropar arm-API: et och använder prenumerations certifikat för att få åtkomst till båda servrarna som ingår i geo-replikering.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>Övervaka förloppet för kopierings åtgärden

Övervaka kopierings processen genom att skicka frågor till vyerna sys. databases och sys. dm_database_copies. När kopieringen pågår är kolumnen **state_desc** i vyn sys. Databass för den nya databasen som **kopia.**

* Om kopieringen Miss lyckas är kolumnen **state_desc** i vyn sys. Databass för den nya databasen **misstänkt**. Kör DROP-instruktionen på den nya databasen och försök igen senare.
* Om kopieringen lyckas anges kolumnen **state_desc** i vyn sys. Databass för den nya databasen som **online**. Kopieringen är klar och den nya databasen är en vanlig databas som kan ändras oberoende av käll databasen.

> [!NOTE]
> Om du väljer att avbryta kopieringen medan den pågår kör du [Drop Database](https://msdn.microsoft.com/library/ms178613.aspx) -instruktionen på den nya databasen. Om du kör DROP DATABASE-instruktionen på käll databasen avbryts även kopierings processen.

> [!IMPORTANT]
> Om du behöver skapa en kopia med ett betydligt mindre service nivå mål än källan, kanske mål databasen inte har tillräckligt med resurser för att slutföra initierings processen och det kan leda till att kopierings åtgärden Miss lyckas. I det här scenariot använder du en geo-Restore-begäran för att skapa en kopia på en annan server och/eller en annan region. Mer information finns i [återställa en Azure SQL-databas med hjälp av databas säkerhets kopior](sql-database-recovery-using-backups.md#geo-restore) .

## <a name="resolve-logins"></a>Lösa inloggningar

När den nya databasen är online på mål servern använder du instruktionen [Alter User](https://msdn.microsoft.com/library/ms176060.aspx) för att mappa om användarna från den nya databasen till inloggningar på mål servern. Information om hur du löser överblivna användare finns i [Felsöka överblivna användare](https://msdn.microsoft.com/library/ms175475.aspx). Se även [hur du hanterar Azure SQL Database-säkerhet efter haveri beredskap](sql-database-geo-replication-security-config.md).

Alla användare i den nya databasen behåller de behörigheter som de hade i käll databasen. Användaren som initierade databas kopian blir databas ägaren till den nya databasen och tilldelas en ny säkerhets identifierare (SID). När kopieringen är klar och innan andra användare mappas om, så kan databas ägaren logga in på den nya databasen.

Information om hur du hanterar användare och inloggningar när du kopierar en databas till en annan SQL Database-Server finns i [så här hanterar du Azure SQL Database-säkerhet efter haveri beredskap](sql-database-geo-replication-security-config.md).

## <a name="database-copy-errors"></a>Databas kopierings fel

Följande fel kan uppstå när du kopierar en databas i Azure SQL Database. Mer information finns i [Kopiera en Azure SQL Database](sql-database-copy.md).

| Felkod | Allvarsgrad | Beskrivning |
| ---:| ---:|:--- |
| 40635 |16 |Klienten med IP-adressen (%.&#x2a;ls) är tillfälligt inaktiverad. |
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

- Information om inloggningar finns i [Hantera inloggningar](sql-database-manage-logins.md) och [Hantera Azure SQL Database-säkerhet efter haveri beredskap](sql-database-geo-replication-security-config.md).
- Information om hur du exporterar en databas finns i [Exportera databasen till en BACPAC](sql-database-export.md).
