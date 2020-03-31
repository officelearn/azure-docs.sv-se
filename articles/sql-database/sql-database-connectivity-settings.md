---
title: Anslutningsinställningar för Azure SQL Database och Data Warehouse
description: I det här dokumentet beskrivs val av TLS-version och inställningen Proxy jämfört med omdirigering för Azure SQL
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366095"
---
# <a name="azure-sql-connectivity-settings"></a>Inställningar för Azure SQL-anslutning
> [!NOTE]
> Den här artikeln gäller Azure SQL-server och både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

> [!IMPORTANT]
> Den här artikeln gäller *inte* **för Azure SQL Database Managed Instance**

Den här artikeln introducerar inställningar som styr anslutningen till Azure SQL Database på servernivå. Dessa inställningar gäller för **alla** SQL Database- och SQL Data Warehouse-databaser som är associerade med servern.

> [!NOTE]
> När dessa inställningar tillämpas börjar de **gälla omedelbart** och kan resultera i anslutningsförlust för dina kunder om de inte uppfyller kraven för varje inställning.

Anslutningsinställningarna är tillgängliga från **brandväggar och virtuella nätverk** blad som visas i skärmdumpen nedan:

 ![Skärmbild av anslutningsinställningar][1]


## <a name="deny-public-network-access"></a>Neka offentligt nätverksåtkomst
I Azure-portalen tillåts endast anslutningar via privata slutpunkter när inställningen **Neka offentlig nätverksåtkomst** är inställd på **Ja.** När den här inställningen är inställd på **Nej**kan klienter ansluta med den privata eller offentliga slutpunkten.

När du har ställt in **Neka offentlig nätverksåtkomst** till **Ja**misslyckas inloggningsförsök från klienter med hjälp av offentlig slutpunkt med följande fel:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Ändra offentlig nätverksåtkomst via PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska. Följande skript kräver [Azure PowerShell-modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar `Get` `Set` hur du och egenskapen **Public Network Access** på logisk servernivå:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Ändra allmänhetens nätverksåtkomst via CLI
> [!IMPORTANT]
> Alla skript i det här avsnittet kräver [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI i ett bash-skal
Följande CLI-skript visar hur du ändrar **åtkomsten** till det offentliga nätverket i ett bash-skal:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Anslutningsprincip
[Anslutningsprincipen](sql-database-connectivity-architecture.md#connection-policy) avgör hur klienter ansluter till Azure SQL Server. 

## <a name="change-connection-policy-via-powershell"></a>Ändra anslutningsprincip via PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska. Följande skript kräver [Azure PowerShell-modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar hur du ändrar anslutningsprincipen med PowerShell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>Ändra anslutningsprincip via Azure CLI
> [!IMPORTANT]
> Alla skript i det här avsnittet kräver [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI i ett bash-skal
Följande CLI-skript visar hur du ändrar anslutningsprincipen i ett bash-skal: 

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI från en Kommandotolk i Windows
Följande CLI-skript visar hur du ändrar anslutningsprincipen från en Windows-kommandotolk (med Azure CLI installerat).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Nästa steg
- En översikt över hur anslutningen fungerar i Azure SQL Database finns i [Azure SQL Connectivity Architecture](sql-database-connectivity-architecture.md)
- Information om hur du ändrar azure SQL Database-anslutningsprincipen för en Azure SQL Database-server finns i [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
