---
title: 'Snabbstart: Skapa en Azure SQL Data Warehouse – Azure Powershell | Microsoft Docs'
description: Skapa snabbt en logisk SQL Database-server, brandväggsregel på servernivå och data warehouse med Azure PowerShell.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/15/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 22ccc11d1eb74fd868fd740f02311baeb5ac5987
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54433619"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Snabbstart: Skapa och fråga en Azure SQL data warehouse med Azure PowerShell

Snabbt skapa ett Azure SQL data warehouse med Azure PowerShell.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

För den här självstudien krävs Azure PowerShell-modul version 5.1.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul). 


> [!NOTE]
> Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Mer information finns på [prissidan för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med den [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) och följer den på skärmen riktningar.

```powershell
Add-AzureRmAccount
```

Om du vill se vilken prenumeration som du använder kör [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Om du behöver använda en annan prenumeration än standardprenumerationen kör du [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

```powershell
Set-AzureRmContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Skapa variabler

Definiera variabler för användning i skripten i den här snabbstartsguiden.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Skapa en logisk server

Skapa en [logiska Azure SQL-servern](../sql-database/sql-database-logical-servers.md) med hjälp av den [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) kommando. En logisk server innehåller en uppsättning databaser som hanteras som en grupp. I följande exempel skapas en slumpmässigt namn i resursgruppen med en administratörsanvändare med namnet `ServerAdmin` och lösenordet `ChangeYourAdminPassword1`. Ersätt dessa fördefinierade värden efter behov.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en [brandväggsregel för Azure SQL-servernivå](../sql-database/sql-database-firewall-configure.md) med hjälp av den [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) kommando. En brandväggsregel på servernivå gör att externa program, till exempel SQL Server Management Studio eller SQLCMD-verktyget för att ansluta till ett SQL data warehouse via SQL Data Warehouse-tjänstens brandvägg. I följande exempel öppnas brandväggen bara för andra Azure-resurser. Aktivera extern anslutning, ändra IP-adressen till en adress som är lämplig för din miljö. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database och SQL Data Warehouse kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 bli nekad av nätverkets brandvägg. I så fall kan du inte ansluta till Azure SQL-servern om inte din IT-avdelning öppnar port 1433.
>


## <a name="create-a-data-warehouse-with-sample-data"></a>Skapa ett data warehouse med exempeldata
Det här exemplet skapar ett data warehouse med hjälp av tidigare definierade variablerna.  Den anger tjänstmålet som DW400, vilket är en lägre kostnad startpunkt för ditt informationslager. 

```Powershell
New-AzureRmSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW400" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Erfordrade parametrar är:

* **RequestedServiceObjectiveName**: Mängden [data informationslagerenheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) du begär. Öka den mängden ökar beräkningskostnader. En lista över värden som stöds finns i [minne och samtidighet gränser](memory-and-concurrency-limits.md).
* **DatabaseName**: Namnet på SQL Data Warehouse som du skapar.
* **ServerName**: Namnet på den server som du använder för att skapa.
* **ResourceGroupName**: Resursgrupp som du använder. Använd Get-AzureResource för att hitta tillgängliga resursgrupper i din prenumeration.
* **Edition**: Måste vara ”DataWarehouse” skapa ett SQL Data Warehouse.

Valfria parametrar är:

- **Sorteringsnamnet**: Standardsortering om inte anges är SQL_Latin1_General_CP1_CI_AS. Sortering kan inte ändras på en databas.
- **MaxSizeBytes**: Maximal standardstorlek för en databas är 10 GB.

Mer information om parameteralternativen finns i [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).


## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstartsguiderna i den här samlingen bygger på den här snabbstarten. 

> [!TIP]
> Om du planerar att fortsätta att arbeta med senare snabbstartsguider skapas inte rensa upp resurserna i den här snabbstarten. Om du inte planerar att fortsätta, kan du använda följande steg för att ta bort alla resurser som skapades i snabbstarten i Azure-portalen.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Nästa steg

Du har nu skapat ett informationslager, skapat en brandväggsregel som är anslutna till ditt informationslager och kört några frågor. Om du vill veta mer om Azure SQL Data Warehouse kan fortsätta med självstudiekursen om att läsa in data.
> [!div class="nextstepaction"]
>[Läsa in data i ett SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
