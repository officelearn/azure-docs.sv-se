---
title: 'Azure PowerShell: Skapa en SQL-databas | Microsoft Docs'
description: Lär dig att skapa en logisk SQL Database-server, brandväggsregel på servernivå och databaser i Azure Portal.
keywords: sql database-självstudier, skapa en sql-databas
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,DBs & servers
ms.devlang: PowerShell
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 5daead37e77bb798e8c870ea3c071823e4d03dce
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-single-azure-sql-database-using-powershell"></a>Skapa en enskild Azure SQL-databas med PowerShell

PowerShell används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. I den här handboken får du information om hur du använder PowerShell för att distribuera en Azure SQL-databas i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) i en [logisk Azure SQL Database-server](sql-database-features.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här självstudien kräver Azure PowerShell-modul version 4.0 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) och följ anvisningarna på skärmen.

```powershell
Connect-AzureRmAccount
```

## <a name="create-variables"></a>Skapa variabler

Definiera variabler för användning i skripten i den här snabbstartsguiden.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDatabase"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Skapa en logisk server

Skapa en logisk [Azure SQL Database-server](sql-database-features.md) med kommandot [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver). En logisk server innehåller en uppsättning databaser som hanteras som en grupp. I följande exempel skapas en server med ett slumpmässigt namn i resursgruppen med en administratörsinloggning med namnet `ServerAdmin` och lösenordet `ChangeYourAdminPassword1`. Ersätt dessa fördefinierade värden efter behov.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en ny brandväggsregel på [Azure SQL Database-servernivå](sql-database-firewall-configure.md) med kommandot [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule). En brandväggsregel på servernivå tillåter att ett externt program, t.ex. SQL Server Management Studio eller SQLCMD-verktyget, ansluter till en SQL-databas visa SQL Database-tjänstens brandvägg. I följande exempel öppnas brandväggen bara för andra Azure-resurser. Aktivera extern anslutning, ändra IP-adressen till en adress som är lämplig för din miljö. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 nekas av nätverkets brandvägg. I så fall kan du inte ansluta till din Azure SQL Database-server om din IT-avdelning inte öppnar port 1433.
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>Skapa en databas på servern med exempeldata

Skapa en databas med en [S0-prestandanivå](sql-database-service-tiers.md) i servern med kommandot [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase). Följande exempel skapar en databas med namnet `mySampleDatabase` och läser in AdventureWorksLT-exempeldata till den här databasen. Ersätt de fördefinierade värdena efter behov (andra snabbstarter i den här samlingen bygger på värdena i den här snabbstarten).

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -SampleName "AdventureWorksLT" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna i den här samlingen bygger på den här snabbstarten.

> [!TIP]
> Om du planerar att fortsätta att arbeta med efterföljande snabbstarter, ska du inte rensa upp resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten i Azure-portalen.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Nästa steg

- Nu när du har en databas kan du [ansluta och fråga](sql-database-connect-query.md) med dina favoritverktyg och språk.
- I följande självstudier lär du dig hur du utformar din första databas, skapar tabeller och infogar data:
 - [Utforma din första Azure SQL-databas med SSMS](sql-database-design-first-database.md)
  - [Utforma en Azure SQL databas och ansluta med C# och ADO.NET](sql-database-design-first-database-csharp.md)

