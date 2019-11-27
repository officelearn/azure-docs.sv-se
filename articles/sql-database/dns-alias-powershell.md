---
title: PowerShell för DNS-alias
description: PowerShell-cmdletar som New-AzSqlServerDNSAlias gör att du kan omdirigera nya klient anslutningar till en annan Azure SQL Database Server, utan att behöva trycka på någon klient konfiguration.
keywords: DNS SQL-databas
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 9232a99ddd29201e6743c09455d79e9ba22b3b9c
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420398"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell för DNS-alias till Azure SQL Database

Den här artikeln innehåller ett PowerShell-skript som visar hur du kan hantera ett DNS-alias för Azure SQL Database.

> [!NOTE]
> Den här artikeln har uppdaterats med antingen Azure PowerShell AZ-modulen eller Azure CLI. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre.
>
> Mer information om AZ-modulen och AzureRM-kompatibilitet finns i [Introduktion till Azure PowerShell AZ-modulen](/powershell/azure/new-azureps-module-az). Installations anvisningar finns i [installera Azure PowerShell](/powershell/azure/install-az-ps) eller [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>DNS-alias i anslutnings sträng

Om du vill ansluta en viss Azure SQL Database Server kan en klient som SQL Server Management Studio (SSMS) tillhandahålla DNS-aliasnamnet i stället för det faktiska Server namnet. I följande exempel Server sträng ersätter aliaset *any-Unique-Alias-Name* den första punkten-avgränsade noden i den fyra nodens Server sträng:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Krav

Om du vill köra demonstrations PowerShell-skriptet som du fick i den här artikeln gäller följande krav:

- En Azure-prenumeration och ett konto för kostnads fri utvärderings version finns i [Azure](https://azure.microsoft.com/free/) -utvärderingar
- Två Azure SQL Database-servrar

## <a name="example"></a>Exempel

Följande kod exempel börjar genom att tilldela litterala värden till flera variabler.

Om du vill köra koden redigerar du plats hållar värden så att de matchar de verkliga värdena i systemet.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

De cmdletar som används är följande:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): skapar ett DNS-alias i Azure SQL Database tjänst systemet. Aliaset refererar till databas Server 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Hämta och lista alla alias som tilldelats till SQL DB server 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): ändrar Server namnet som aliaset är konfigurerat att referera till, från Server 1 till Server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): ta bort alias från databas server 2 med hjälp av namnet på aliaset.

Information om att installera och uppgradera finns i [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul).

Använd `Get-Module -ListAvailable Az` i *powershell\_ISE. exe*för att hitta versionen.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

De kommandon som används är följande:

- [AZ SQL Server DNS-alias Create](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): skapar ett DNS-alias i det Azure SQL Database tjänst systemet. Aliaset refererar till databas Server 1.
- [AZ SQL Server DNS-alias show](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Hämta och lista alla alias som tilldelats till SQL DB server 1.
- [AZ SQL Server DNS-aliasuppsättning](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): ändrar Server namnet som aliaset är konfigurerat att referera till, från Server 1 till Server 2.
- [AZ SQL Server DNS-alias Delete](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): ta bort alias från databas server 2 med hjälp av namnet på aliaset.

Information om hur du installerar eller uppgraderar finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>Nästa steg

En fullständig förklaring av funktionen DNS-alias för SQL Database finns i [DNS-alias för Azure SQL Database](dns-alias-overview.md).
