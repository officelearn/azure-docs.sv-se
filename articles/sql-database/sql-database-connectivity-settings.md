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
ms.openlocfilehash: ef846b23bf6c9da2b7dd64927eac7cc1a1704dae
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684956"
---
# <a name="azure-sql-connectivity-settings"></a>Inställningar för Azure SQL-anslutning
> [!NOTE]
> Den här artikeln gäller den logiska SQL-Server i Azure som används för både Azure SQL Database och SQL Data Warehouse databaser som skapas på den logiska servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

> [!IMPORTANT]
> Den här artikeln gäller *inte* för **Azure SQL Database Hanterad instans**

Den här artikeln beskriver inställningar som styr anslutningen till Azure SQL Database på server nivå. Dessa inställningar gäller för **alla** SQL Database och SQL Data Warehouse databaser som är associerade med servern.

> [!NOTE]
> När de här inställningarna har tillämpats **börjar de gälla omedelbart** och kan leda till förlust av anslutningar för dina klienter om de inte uppfyller kraven för varje inställning.

Anslutnings inställningarna är tillgängliga från skärmen **brand väggar och virtuella nätverk** , vilket visas på följande skärm bild:

 ![Skärm bild av anslutnings inställningar][1]


## <a name="deny-public-network-access"></a>Neka offentlig nätverks åtkomst

Kunder kan ansluta till SQL Database med hjälp av offentliga slut punkter (IP-baserade brand Väggs regler, VNET-baserade brand Väggs regler) eller privata slut punkter (med privat länk) som beskrivs i [översikten över nätverks åtkomst](sql-database-networkaccess-overview.md). 

Om alternativet **Neka åtkomst till offentliga nätverk** är inställt på **Ja**tillåts bara anslutningar via privata slut punkter och alla anslutningar via offentliga slut punkter nekas med ett fel meddelande som liknar:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
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

## <a name="minimal-tls-version"></a>Minimal TLS-version 

Inställningen minimalt [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) gör det möjligt för kunderna att kontrol lera vilken version av TLS som används av deras Azure SQL Database.

För närvarande har vi stöd för TLS 1,0, 1,1 och 1,2. Genom att ange en minimal TLS-version kan du se till att senare TLS-versioner stöds. T. ex. kan du välja en TLS-version som är större än 1,1. innebär endast anslutningar med TLS 1,1 och 1,2 godkänns och TLS 1,0 avvisas. När du har testat för att bekräfta att programmen har stöd för det rekommenderar vi att du ställer in minimal TLS-version på 1,2 eftersom det innehåller korrigeringar för sårbarheter som finns i tidigare versioner och är den högsta versionen av TLS som stöds i Azure SQL Database.

För kunder med program som förlitar sig på äldre versioner av TLS rekommenderar vi att du ställer in den lägsta TLS-versionen enligt kraven för dina program. För kunder som förlitar sig på att program ska ansluta via en okrypterad anslutning rekommenderar vi inte att du anger någon minimal TLS-version. 

Mer information finns i [TLS-överväganden för SQL Database anslutning](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

När du har angett den lägsta TLS-versionen kommer inloggnings försök från klienter som använder en TLS-version som är lägre än den lägsta TLS-versionen av servern att Miss lyckas med följande fel:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Ange minimal TLS-version via PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Följande skript kräver Azure PowerShell- [modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar hur du `Get` och `Set` egenskapen **minimal TLS-version** på den logiska Server nivån:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Ange minimal TLS-version via Azure CLI

> [!IMPORTANT]
> Alla skript i det här avsnittet kräver [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI i ett bash-gränssnitt

Följande CLI-skript visar hur du ändrar inställningen för **minimal TLS-version** i ett bash-gränssnitt:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
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
