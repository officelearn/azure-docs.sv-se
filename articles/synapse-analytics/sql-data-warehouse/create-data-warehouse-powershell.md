---
title: Skapa och fråga en Synapse SQL-pool med Azure PowerShell
description: Skapa snabbt en logiska synaps-SQL-poolserver med en brandväggsregel på servernivå med Azure PowerShell.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 45982c0761fecdb456dba5dc4a5d604972b9c3e5
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349316"
---
# <a name="quickstart-create-and-query-a-synapse-sql-pool-with-azure-powershell"></a>Snabbstart: Skapa och fråga en Synapse SQL-pool med Azure PowerShell

Skapa en Synapse SQL-pool (informationslager) i Azure Synapse Analytics med Azure PowerShell.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!IMPORTANT]
> Om du skapar en SQL-pool kan det leda till en ny fakturerbar tjänst.  Mer information finns i [Azure Synapse Analytics-priser](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

Om du vill se vilken prenumeration du använder kör du [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Om du behöver använda en annan prenumeration än standard kör du [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

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

Skapa en [Azure-resursgrupp](../../azure-resource-manager/management/overview.md) med kommandot [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-logical-server"></a>Skapa en logisk server

Skapa en [logiska Azure SQL-server](../../sql-database/sql-database-logical-servers.md) med kommandot [New-AzSqlServer.](/powershell/module/az.sql/new-azsqlserver) En logisk server innehåller en uppsättning databaser som hanteras som en grupp. I följande exempel skapas en slumpmässigt namngiven server `ServerAdmin` i resursgruppen `ChangeYourAdminPassword1`med en administratörsanvändare namngiven och ett lösenord för . Ersätt dessa fördefinierade värden efter behov.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Konfigurera en serverbrandväggsregel

Skapa en [brandväggsregel på Azure SQL-servernivå](../../sql-database/sql-database-firewall-configure.md) med kommandot [New-AzSqlServerFirewallRule.](/powershell/module/az.sql/new-azsqlserverfirewallrule) Med en brandväggsregel på servernivå kan ett externt program, till exempel SQL Server Management Studio eller SQLCMD-verktyget, ansluta till en SQL-pool via SQL-pooltjänstbrandväggen. 

I följande exempel öppnas brandväggen bara för andra Azure-resurser. Aktivera extern anslutning, ändra IP-adressen till en adress som är lämplig för din miljö. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL-slutpunkter kommunicerar över port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 bli nekad av nätverkets brandvägg. I så fall kan du inte ansluta till din Azure SQL-server om inte IT-avdelningen öppnar port 1433.
>


## <a name="create-a-sql-pool"></a>Skapa en SQL-pool
I följande exempel skapas en SQL-pool med hjälp av de tidigare definierade variablerna.  Den anger tjänstmålet som DW100c, vilket är en startpunkt för lägre kostnader för SQL-poolen. 

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

* **RequestedServiceObjectiveName**: Mängden [informationslagerenheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) som du begär. Om du ökar det här beloppet ökar beräkningskostnaden. En lista över värden som stöds finns i [begränsningar för minne och samtidighet](memory-concurrency-limits.md).
* **Databasnamn:** Namnet på den SQL-pool som du skapar.
* **ServerNamn:** Namnet på den server som du använder för att skapa.
* **ResourceGroupName**: Resursgrupp som du använder. Använd Get-AzureResource för att hitta tillgängliga resursgrupper i din prenumeration.
* **Utgåva:** Måste vara "DataWarehouse" för att skapa en SQL-pool.

Valfria parametrar är:

- **CollationName**: Standardsortering om inte annat anges är SQL_Latin1_General_CP1_CI_AS. Sortering kan inte ändras i en databas.
- **MaxSizeBytes**: Standardstorleken för en databas är 240 TB. Maxstorleken begränsar radbutiksdata. Det finns obegränsad lagring för columnar-data.

Mer information om parameteralternativen finns i [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).


## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstartsguiderna i den här samlingen bygger på den här snabbstarten. 

> [!TIP]
> Om du planerar att fortsätta att arbeta med senare snabbstartsstudier ska du inte rensa de resurser som skapas i den här snabbstarten. Om du inte planerar att fortsätta följer du följande steg för att ta bort alla resurser som skapats av den här snabbstarten i Azure-portalen.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Nästa steg

Du har nu skapat en SQL-pool, skapat en brandväggsregel, anslutit till din SQL-pool och kört några frågor. Om du vill veta mer fortsätter du till [artikeln Läs in data i SQL Pool.](load-data-from-azure-blob-storage-using-polybase.md)
