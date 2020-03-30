---
title: PowerShell för DNS-alias
description: PowerShell-cmdletar som New-AzSqlServerDNSAlias gör att du kan omdirigera nya klientanslutningar till en annan Azure SQL Database-server, utan att behöva röra någon klientkonfiguration.
keywords: dns sql-databas
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420398"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell för DNS-alias till Azure SQL Database

Den här artikeln innehåller ett PowerShell-skript som visar hur du kan hantera ett DNS-alias för Azure SQL Database.

> [!NOTE]
> Den här artikeln har uppdaterats för att använda azure powershell Az-modulen eller Azure CLI. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre.
>
> Mer information om Az-modulen och AzureRM-kompatibiliteten finns i [Introduktion till Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az). Installationsinstruktioner finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps) eller [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>DNS-alias i anslutningssträng

Om du vill ansluta en viss Azure SQL Database-server kan en klient som SQL Server Management Studio (SSMS) ange DNS-aliasnamnet i stället för det sanna servernamnet. I följande exempelserversträng ersätter alias *any-unique-alias-name* den första punktavgränsade noden i den fyra nodserversträngen:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Krav

Om du vill köra det demo-Skriptet för PowerShell som ges i den här artikeln gäller följande förutsättningar:

- En Azure-prenumeration och ett konto, för kostnadsfri utvärderingsversion, se [Azure-utvärderingsversioner](https://azure.microsoft.com/free/)
- Två Azure SQL-databasservrar

## <a name="example"></a>Exempel

Följande kodexempel börjar med att tilldela litterala värden till flera variabler.

Om du vill köra koden redigerar du platshållarvärdena så att de matchar verkliga värden i systemet.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

De cmdlets som används är följande:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Skapar ett DNS-alias i Azure SQL Database-tjänstsystemet. Aliaset refererar till databasserver 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Hämta och lista alla alias som tilldelats SQL DB server 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Ändrar servernamnet som aliaset är konfigurerat för att referera till, från server 1 till server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Ta bort aliaset från databasserver 2 med hjälp av namnet på aliaset.

Information om att installera och uppgradera finns i [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul).

Använd `Get-Module -ListAvailable Az` i *powershell\_ise.exe*för att hitta versionen.

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

De kommandon som används är följande:

- [az sql server dns-alias create](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Skapar ett DNS-alias i Azure SQL Database service system. Aliaset refererar till databasserver 1.
- [az sql server dns-alias visa:](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias)Hämta och lista alla alias som tilldelats SQL DB server 1.
- [az sql server dns-alias set](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Ändrar servernamnet som aliaset är konfigurerat för att referera till, från server 1 till server 2.
- [az sql server dns-alias delete](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Remove the alias from database server 2, by using the name of the alias.

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

En fullständig förklaring av DNS-aliasfunktionen för SQL Database finns i [DNS-alias för Azure SQL-databas](dns-alias-overview.md).
