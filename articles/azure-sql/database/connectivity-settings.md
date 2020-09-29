---
title: Anslutnings inställningar för Azure SQL Database och informations lager
description: Det här dokumentet beskriver Transport Layer Security (TLS) versions val och proxy vs. Redirect-inställning för Azure SQL Database och Azure Synapse Analytics
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics (formerly SQL Data Warehouse)
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: eab9004b37da83b5d571ff700c32215354286c94
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443861"
---
# <a name="azure-sql-connectivity-settings"></a>Inställningar för Azure SQL-anslutning
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Den här artikeln beskriver inställningar som styr anslutningen till servern för Azure SQL Database och Azure Synapse Analytics. Dessa inställningar gäller för **alla** SQL Database-och Azure Synapse-databaser som är kopplade till servern.

> [!IMPORTANT]
> Den här artikeln gäller *inte* för **Azure SQL-hanterade instanser**

Anslutnings inställningarna är tillgängliga från skärmen **brand väggar och virtuella nätverk** , vilket visas på följande skärm bild:

 ![Skärm bild av anslutnings inställningar][1]

> [!NOTE]
> När de här inställningarna har tillämpats **börjar de gälla omedelbart** och kan leda till förlust av anslutningar för dina klienter om de inte uppfyller kraven för varje inställning.

## <a name="deny-public-network-access"></a>Neka åtkomst till offentligt nätverk

Om alternativet **Neka åtkomst till offentligt nätverk** är inställt på **Ja**, tillåts bara anslutningar via privata slut punkter. När den här inställningen är **Nej** (standard) kan klienter ansluta med hjälp av antingen offentliga slut punkter (IP-baserade brand Väggs regler, VNet-baserade brand Väggs regler) eller privata slut punkter (med privat länk) enligt beskrivningen i [Översikt över nätverks åtkomst](network-access-controls-overview.md). 

 ![Skärm bild av anslutning med neka offentlig nätverks åtkomst][2]

Försök att ställa in **neka offentlig nätverks åtkomst** inställning till **Ja** utan att befintliga privata slut punkter på den logiska servern kommer att Miss lyckas med ett fel meddelande som liknar:  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server. 
Please set up private endpoints and retry the operation. 
```

Om alternativet **Neka åtkomst till offentliga nätverk** är inställt på **Ja**tillåts bara anslutningar via privata slut punkter och alla anslutningar via offentliga slut punkter nekas med ett fel meddelande som liknar:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Om alternativet **Neka åtkomst till offentliga nätverk** är inställt på **Ja**nekas försök att lägga till eller uppdatera brand Väggs regler med ett fel meddelande som liknar följande:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Ändra offentlig nätverks åtkomst via PowerShell

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Följande skript kräver Azure PowerShell- [modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar hur du `Get` och `Set` den **offentliga nätverks åtkomst** egenskapen på server nivå:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
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

Vi har nu stöd för TLS 1,0, 1,1 och 1,2. Genom att ange en minimal TLS-version kan du se till att senare TLS-versioner stöds. Du kan till exempel välja en TLS-version som är större än 1,1. innebär endast anslutningar med TLS 1,1 och 1,2 godkänns och TLS 1,0 avvisas. När du har testat för att bekräfta att programmen har stöd för det rekommenderar vi att du ställer in minimal TLS-version på 1,2 eftersom den innehåller korrigeringar för sårbarheter som finns i tidigare versioner och är den högsta versionen av TLS som stöds i Azure SQL Database.

> [!IMPORTANT]
> Standardvärdet för minimal TLS-version är att tillåta alla versioner. Men när du tillämpar en version av TLS går det inte att återgå till standardvärdet.

För kunder med program som förlitar sig på äldre versioner av TLS rekommenderar vi att du ställer in den lägsta TLS-versionen enligt kraven för dina program. För kunder som förlitar sig på att program ska ansluta via en okrypterad anslutning rekommenderar vi inte att du anger någon minimal TLS-version.

Mer information finns i [TLS-överväganden för SQL Database anslutning](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

När du har angett den lägsta TLS-versionen kommer inloggnings försök från klienter som använder en TLS-version som är lägre än den lägsta TLS-versionen av servern att Miss lyckas med följande fel:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Ange minimal TLS-version via PowerShell

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Följande skript kräver Azure PowerShell- [modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar hur du `Get` och `Set` egenskapen **minimal TLS-version** på den logiska Server nivån:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# # Update Minimal TLS Version to 1.2
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


## <a name="change-connection-policy"></a>Ändra anslutnings princip

[Anslutnings principen](connectivity-architecture.md#connection-policy) fastställer hur klienter ansluter till Azure SQL Database.


## <a name="change-connection-policy-via-powershell"></a>Ändra anslutnings princip via PowerShell

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Följande skript kräver Azure PowerShell- [modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar hur du ändrar anslutnings principen med hjälp av PowerShell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

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

- En översikt över hur anslutningen fungerar i Azure SQL Database finns i [anslutnings arkitektur](connectivity-architecture.md)
- Information om hur du ändrar anslutnings principen för en server finns i avsnittet om att ansluta [-princip](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
