---
title: 'Snabb start: skapa ett informations lager (PowerShell)'
description: Skapa snabbt en logisk server för Azure Synapse Analytics-data lagret med en brand Väggs regel på server nivå med hjälp av Azure PowerShell.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9df9b4b1bdb33a856d9e31d65981e8654af049d2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200013"
---
# <a name="quickstart-create--query-a-data-warehouse-with-azure-powershell"></a>Snabb start: skapa & fråga ett informations lager med Azure PowerShell

Skapa ett informations lager för Azure Synapse Analytics genom att tillhandahålla en SQL-pool med hjälp av Azure PowerShell.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!NOTE]
> Att skapa ett lager kan resultera i en ny fakturerbar tjänst.  Mer information finns i [priser för Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

Om du vill se vilken prenumeration du använder kör du [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Om du behöver använda en annan prenumeration än standardinställningen kör du [set-AzContext](/powershell/module/az.accounts/set-azcontext).

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
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resurs grupp](../azure-resource-manager/management/overview.md) med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-logical-server"></a>Skapa en logisk server

Skapa en [logisk Azure SQL-Server](../sql-database/sql-database-logical-servers.md) med kommandot [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) . En logisk server innehåller en uppsättning databaser som hanteras som en grupp. I följande exempel skapas en slumpmässigt namngiven server i resurs gruppen med en administratörs användare med namnet `ServerAdmin` och ett lösen ord för `ChangeYourAdminPassword1`. Ersätt dessa fördefinierade värden efter behov.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en [brand Väggs regel för Azure SQL Server-nivå](../sql-database/sql-database-firewall-configure.md) med kommandot [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) . En brand Väggs regel på server nivå gör det möjligt för ett externt program, till exempel SQL Server Management Studio eller SQLCMD-verktyget att ansluta till en SQL Data Warehouse via SQL Data Warehouse tjänstens brand vägg. I följande exempel öppnas brandväggen bara för andra Azure-resurser. Aktivera extern anslutning, ändra IP-adressen till en adress som är lämplig för din miljö. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL-slutpunkter kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 bli nekad av nätverkets brandvägg. I så fall kan du inte ansluta till din Azure SQL-Server om inte din IT-avdelning öppnar port 1433.
>


## <a name="create-a-data-warehouse"></a>Skapa ett datalager
I det här exemplet skapas ett informations lager med hjälp av de tidigare definierade variablerna.  Det anger tjänst målet som DW100c, som är en lägre start punkt för ditt informations lager. 

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

* **RequestedServiceObjectiveName**: mängden [data lager enheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) som du begär. Om du ökar värdet ökar beräknings kostnaden. En lista över värden som stöds finns i [minnes-och samtidiga gränser](memory-concurrency-limits.md).
* **Databasename**: namnet på det informations lager som du skapar.
* **Server**namn: namnet på den server som du använder för att skapa.
* **ResourceGroupName**: resurs gruppen som du använder. Använd Get-AzureResource för att hitta tillgängliga resursgrupper i din prenumeration.
* **Utgåva**: måste vara "DataWarehouse" för att skapa ett informations lager.

Valfria parametrar är:

- **CollationName**: Standardsortering om inte annat anges är SQL_Latin1_General_CP1_CI_AS. Det går inte att ändra sorteringen för en databas.
- **MaxSizeBytes**: standard Max storleken för en databas är 240TB. Max storleken begränsar rowstore-data. Det finns obegränsad lagring för kolumn data.

Mer information om parameter alternativen finns i [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).


## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstartsguiderna i den här samlingen bygger på den här snabbstarten. 

> [!TIP]
> Om du planerar att fortsätta att arbeta med senare snabb starts guider ska du inte rensa upp resurserna som du skapade i den här snabb starten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som skapats i den här snabb starten i Azure Portal.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Nästa steg

Nu har du skapat ett informations lager, skapat en brand Väggs regel, anslutit till ditt data lager och kört några frågor. Om du vill veta mer kan du fortsätta till självstudien för att läsa in data.
> [!div class="nextstepaction"]
>[Läs in data i ett informations lager](load-data-from-azure-blob-storage-using-polybase.md)
