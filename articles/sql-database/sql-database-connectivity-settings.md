---
title: Anslutnings inställningar för Azure SQL Database och informations lager
description: Det här dokumentet förklarar alternativen för TLS-version och proxy vs. Redirect för Azure SQL
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096661"
---
# <a name="azure-sql-connectivity-settings"></a>Inställningar för Azure SQL-anslutning
> [!NOTE]
> Den här artikeln gäller för Azure SQL Server och för både SQL Database och SQL Data Warehouse databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

> [!IMPORTANT]
> Den här artikeln gäller *inte* för **Azure SQL Database Hanterad instans**

Den här artikeln beskriver inställningar som styr anslutningen till Azure SQL Database på server nivå. Dessa inställningar gäller för **alla** SQL Database och SQL Data Warehouse databaser som är associerade med servern.

> [!NOTE]
> När de här inställningarna har tillämpats **börjar de gälla omedelbart** och kan leda till förlust av anslutningar för dina klienter om de inte uppfyller kraven för varje inställning.

Anslutnings inställningarna är tillgängliga från bladet **brand väggar och virtuella nätverk** så som visas på skärm bilden nedan:

 ![Skärm bild av anslutnings inställningar][1]


## <a name="deny-public-network-access"></a>Neka offentlig nätverks åtkomst
När inställningen **neka offentlig nätverks åtkomst** är inställd på **Ja**i Azure Portal, tillåts bara anslutningar via privata slut punkter. När den här inställningen är inställd på **Nej**kan klienter ansluta med hjälp av den privata eller offentliga slut punkten.

När du har angett **neka offentlig nätverks åtkomst** till **Ja**kommer inloggnings försök från klienter som använder den offentliga slut punkten att Miss lyckas med följande fel:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Ändra offentlig nätverks åtkomst via PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Följande skript kräver Azure PowerShell- [modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar hur du `Get` och `Set` den **offentliga nätverks åtkomst** egenskapen på den logiska Server nivån:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Ändra offentlig nätverks åtkomst via CLI
> [!IMPORTANT]
> Alla skript i det här avsnittet kräver [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI i ett bash-gränssnitt
Följande CLI-skript visar hur du ändrar **åtkomst till offentliga nätverk** i ett bash-gränssnitt:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Anslutnings princip
[Anslutnings principen](sql-database-connectivity-architecture.md#connection-policy) fastställer hur klienter ansluter till Azure SQL Server. 

## <a name="change-connection-policy-via-powershell"></a>Ändra anslutnings princip via PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Följande skript kräver Azure PowerShell- [modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar hur du ändrar anslutnings principen med hjälp av PowerShell:

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

## <a name="change-connection-policy-via-azure-cli"></a>Ändra anslutnings princip via Azure CLI
> [!IMPORTANT]
> Alla skript i det här avsnittet kräver [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI i ett bash-gränssnitt
Följande CLI-skript visar hur du ändrar anslutnings principen i ett bash-gränssnitt: 

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI från en kommando tolk i Windows
Följande CLI-skript visar hur du ändrar anslutnings principen från en kommando tolk i Windows (med Azure CLI installerat).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Nästa steg
- En översikt över hur anslutningen fungerar i Azure SQL Database finns i arkitekturen för [Azure SQL-anslutning](sql-database-connectivity-architecture.md)
- Information om hur du ändrar Azure SQL Database anslutnings princip för en Azure SQL Database-Server finns i avsnittet om att ansluta [-princip](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
