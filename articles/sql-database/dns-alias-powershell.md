---
title: PowerShell för DNS-alias Azure SQL | Microsoft Docs
description: PowerShell-cmdletar, till exempel New-AzureRMSqlServerDNSAlias kan du omdirigera nya klientanslutningar till en annan Azure SQL Database-server utan att behöva röra alla klientkonfigurationen.
keywords: DNS-sql-databas
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi,amagarwa,maboja
manager: craigg
ms.date: 02/05/2018
ms.openlocfilehash: 095e6c6d59bf73bb74e2d8fbe3d1506601ab533e
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471126"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell för DNS-Alias till Azure SQL Database

Den här artikeln innehåller ett PowerShell-skript som visar hur du kan hantera ett DNS-alias för Azure SQL Database. Skriptet kör du följande cmdlets som utförs följande åtgärder:

De cmdletar som används i kodexempel är följande:

- [Ny-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): skapar en ny DNS-alias i systemet för Azure SQL Database-tjänsten. Aliaset som refererar till Azure SQL Database-server 1.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): hämta och visa alla DNS-alias som är kopplade till SQL DB server 1.
- [Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): ändrar servernamnet som alias har konfigurerats för att referera till, från 1 till SQL DB server 2-servern.
- [Remove-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): ta bort DNS-alias från SQL DB server 2, med hjälp av namnet på aliaset.

Föregående PowerShell-cmdletar har lagts till i den **i AzureRm.Sql** modul från och med version 5.1.1.

## <a name="dns-alias-in-connection-string"></a>DNS-alias i anslutningssträngen

Om du vill ansluta en viss Azure SQL Database-server, kan en klient, till exempel SQL Server Management Studio (SSMS) Ange DNS-aliasnamn i stället för namnet på true. I följande exempel server strängen är alias *any-unika-aliasnamn* ersätter den första avgränsade med punkt-noden i fyra noder server sträng:

- Exempel server sträng: `any-unique-alias-name.database.windows.net`.

## <a name="prerequisites"></a>Förutsättningar

Om du vill köra demo PowerShell-skriptet som anges i den här artikeln gäller följande krav:

- En Azure-prenumeration och konto. För en kostnadsfri utvärderingsversion, klickar du på [ https://azure.microsoft.com/free/ ] [ https://azure.microsoft.com/free/].
- Azure PowerShell-modulen med cmdleten **New-AzureRMSqlServerDNSAlias**.
  - Om du vill installera eller uppgradera, se [installera Azure PowerShell-modulen][install-azurerm-ps-84p].
  - Kör `Get-Module -ListAvailable AzureRM;` i powershell\_ise.exe att hitta versionen.
- Två Azure SQL Database-servrar.

## <a name="code-example"></a>Kodexempel

Följande PowerShell kodexempel startar genom att tilldela flera variabler litterala värden. Du måste redigera platshållarens värden för att matcha verkliga värden i systemet för att köra koden. Eller du kan bara läser koden. Och du får också konsolens utdata av koden.

```powershell
################################################################
###    Assign prerequisites.                                 ###
################################################################
cls;

$SubscriptionName             = '<EDIT-your-subscription-name>';
[string]$SubscriptionGuid_Get = '?'; # The script assigns this value, not you.

$SqlServerDnsAliasName = '<EDIT-any-unique-alias-name>';

$1ResourceGroupName = '<EDIT-rg-1>';  # Can be same or different than $2ResourceGroupName.
$1SqlServerName     = '<EDIT-sql-1>'; # Must differ from $2SqlServerName.

$2ResourceGroupName = '<EDIT-rg-2>';
$2SqlServerName     = '<EDIT-sql-2>';

# Login to your Azure subscription, first time per session.
Write-Host "You must log into Azure once per powershell_ise.exe session,";
Write-Host "  thus type 'yes' only the first time.";
Write-Host " ";
$yesno = Read-Host '[yes/no]  Do you need to log into Azure now?';
if ('yes' -eq $yesno)
{
    Connect-AzureRmAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzureRmSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzureRMSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzureRMSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzureRMSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```

### <a name="actual-console-output-from-the-powershell-example"></a>Faktiska konsolens utdata från PowerShell-exempel

Följande konsolens utdata har kopieras och klistras in från en faktisk körning.

```powershell
You must log into Azure once per powershell_ise.exe session,
  thus type 'yes' only the first time.

[yes/no]  Do you need to log into Azure now?: yes


Environment           : AzureCloud
Account               : gm@acorporation.com
TenantId              : 72f988bf-1111-1111-1111-111111111111
SubscriptionId        : 45651c69-2222-2222-2222-222222222222
SubscriptionName      : mysubscriptionname
CurrentStorageAccount :

[1] Assign a DNS alias to SQL DB server 1.
[2] Get the DNS alias that is assigned to SQL DB server 1.
[3] Move the DNS alias from 1 to SQL DB server 2.
[4] Remove the DNS alias from SQL DB server 2.
ResourceGroupName ServerName         ServerDNSAliasName
----------------- ----------         ------------------
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-2       gm-sqldb-dns-2     unique-alias-name-food


[C:\windows\system32\]
>>
```

## <a name="next-steps"></a>Nästa steg

En fullständig förklaring av funktionen för DNS-Alias för SQL Database finns i [DNS-alias för Azure SQL Database][dns-alias-overview-37v].

<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-azurerm-ps-84p]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps

[dns-alias-overview-37v]: dns-alias-overview.md
