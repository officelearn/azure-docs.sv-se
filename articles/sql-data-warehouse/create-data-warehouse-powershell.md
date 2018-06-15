---
title: 'Snabbstart: Skapa en Azure SQL Data Warehouse - Azure Powershell | Microsoft Docs'
description: Snabbt skapa en logisk SQL Database-server, brandväggsregel på servernivå och data warehouse med Azure PowerShell.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e0bb014ec0706d458ff2f38e409efba5d66aaf18
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31529522"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Snabbstart: Skapa och läsa ett Azure SQL data warehouse med Azure PowerShell

Snabbt skapa ett Azure SQL data warehouse med hjälp av Azure PowerShell.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här kursen kräver Azure PowerShell Modulversion 5.1.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` reda på vilken version du har för närvarande. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). 


> [!NOTE]
> Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Mer information finns på [prissidan för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) och följ anvisningarna på skärmen.

```powershell
Add-AzureRmAccount
```

Om du vill se vilken prenumeration som du använder kör [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Om du behöver använda en annan prenumeration än standardvärdet kör [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription).

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
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
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Skapa en logisk server

Skapa en [logisk Azure SQL-server](../sql-database/sql-database-servers-databases.md#what-is-an-azure-sql-logical-server) med hjälp av den [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) kommando. En logisk server innehåller en uppsättning databaser som hanteras som en grupp. I följande exempel skapas en server med ett slumpmässigt namn i resursgruppen med en administratörsinloggning med namnet `ServerAdmin` och lösenordet `ChangeYourAdminPassword1`. Ersätt dessa fördefinierade värden efter behov.

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en [Azure SQL-servernivå brandväggsregel](../sql-database/sql-database-firewall-configure.md) med hjälp av den [ny AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) kommando. En brandväggsregel på servernivå kan ett externt program, till exempel SQL Server Management Studio eller SQLCMD-verktyget för att ansluta till ett SQL data warehouse genom brandväggen för SQL Data Warehouse-tjänsten. I följande exempel öppnas brandväggen bara för andra Azure-resurser. Aktivera extern anslutning, ändra IP-adressen till en adress som är lämplig för din miljö. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database och SQL Data Warehouse kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 nekas av nätverkets brandvägg. I så fall, kan du inte ansluta till din Azure SQL-server om din IT-avdelning öppnar port 1433.
>


## <a name="create-a-data-warehouse-with-sample-data"></a>Skapa ett data warehouse med exempeldata
Det här exemplet skapar ett data warehouse med hjälp av de tidigare definierade variablerna.  Den anger tjänstmålet DW400, vilket är en lägre kostnad startpunkt för ditt informationslager. 

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

* **RequestedServiceObjectiveName**: mängden [datalager enheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) du begär. Om du ökar den här mängden ökas beräkna kostnaden. En lista över värden som stöds, se [minne och samtidighet gränser](memory-and-concurrency-limits.md).
* **DatabaseName**: Namnet på det SQL Data Warehouse som du skapar.
* **ServerName**: namnet på den server som du använder för att skapa.
* **ResourceGroupName**: Resursgruppen som du använder dig av. Använd Get-AzureResource för att hitta tillgängliga resursgrupper i din prenumeration.
* **Version**: Måste vara ”DataWarehouse” för att skapa ett SQL Data Warehouse.

Valfria parametrar är:

- **CollationName**: Standardsortering om inte annat anges är SQL_Latin1_General_CP1_CI_AS. Sorteringen kan inte ändras för en databas.
- **MaxSizeBytes**: Maximal standardstorlek för en databas är 10 GB.

Mer information om parameteralternativen finns [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).


## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstartsguiderna i den här samlingen bygger på den här snabbstarten. 

> [!TIP]
> Om du planerar att fortsätta att arbeta med efterföljande snabbstartsguider ska du inte rensa resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten i Azure-portalen.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Nästa steg

Du har nu skapat ett informationslager och en brandväggsregel, anslutit till ditt informationslager och kört några frågor. Om du vill veta mer om Azure SQL Data Warehouse kan fortsätta med självstudiekursen om att läsa in data.
> [!div class="nextstepaction"]
>[Läsa in data i ett SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
