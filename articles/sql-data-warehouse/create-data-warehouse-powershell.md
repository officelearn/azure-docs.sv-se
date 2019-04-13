---
title: 'Snabbstart: Skapa en Azure SQL Data Warehouse – Azure Powershell | Microsoft Docs'
description: Skapa snabbt en logisk SQL Database-server, brandväggsregel på servernivå och data warehouse med Azure PowerShell.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 4/11/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: d76f7ac6c8b60e2dec7d7d95cf419e1352b97f15
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545136"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Snabbstart: Skapa och fråga en Azure SQL data warehouse med Azure PowerShell

Snabbt skapa ett Azure SQL data warehouse med Azure PowerShell.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!NOTE]
> Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Mer information finns på [prissidan för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med den [Connect AzAccount](/powershell/module/az.accounts/connect-azaccount) och följer den på skärmen riktningar.

```powershell
Connect-AzAccount
```

Om du vill se vilken prenumeration som du använder kör [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Om du vill använda en annan prenumeration än standard, kör [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
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

Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med hjälp av den [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) kommando. En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Skapa en logisk server

Skapa en [logiska Azure SQL-servern](../sql-database/sql-database-logical-servers.md) med hjälp av den [New AzSqlServer](/powershell/module/az.sql/new-azsqlserver) kommando. En logisk server innehåller en uppsättning databaser som hanteras som en grupp. I följande exempel skapas en slumpmässigt namn i resursgruppen med en administratörsanvändare med namnet `ServerAdmin` och lösenordet `ChangeYourAdminPassword1`. Ersätt dessa fördefinierade värden efter behov.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en [brandväggsregel för Azure SQL-servernivå](../sql-database/sql-database-firewall-configure.md) med hjälp av den [New AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) kommando. En brandväggsregel på servernivå gör att externa program, till exempel SQL Server Management Studio eller SQLCMD-verktyget för att ansluta till ett SQL data warehouse via SQL Data Warehouse-tjänstens brandvägg. I följande exempel öppnas brandväggen bara för andra Azure-resurser. Aktivera extern anslutning, ändra IP-adressen till en adress som är lämplig för din miljö. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL Database och SQL Data Warehouse kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 bli nekad av nätverkets brandvägg. I så fall kan du inte ansluta till Azure SQL-servern om inte din IT-avdelning öppnar port 1433.
>


## <a name="create-a-data-warehouse"></a>Skapa ett datalager
Det här exemplet skapar ett data warehouse med hjälp av tidigare definierade variablerna.  Den anger tjänstmålet som DW100c, vilket är en lägre kostnad startpunkt för ditt informationslager. 

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
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
- **MaxSizeBytes**: Maximal standardstorlek för en databas är 240TB. Max storlek begränsar rowstore data. Det finns obegränsad lagring för petabyteskalade kolumndata.

Mer information om parameteralternativen finns i [New AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).


## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstartsguiderna i den här samlingen bygger på den här snabbstarten. 

> [!TIP]
> Om du planerar att fortsätta att arbeta med senare snabbstartsguider skapas inte rensa upp resurserna i den här snabbstarten. Om du inte planerar att fortsätta, kan du använda följande steg för att ta bort alla resurser som skapades i snabbstarten i Azure-portalen.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Nästa steg

Du har nu skapat ett informationslager, skapat en brandväggsregel som är anslutna till ditt informationslager och kört några frågor. Om du vill veta mer om Azure SQL Data Warehouse kan fortsätta med självstudiekursen om att läsa in data.
> [!div class="nextstepaction"]
>[Läsa in data i ett SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
