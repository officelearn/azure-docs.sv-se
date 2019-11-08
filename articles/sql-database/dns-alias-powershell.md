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
ms.openlocfilehash: cb1854c27a3722bc9c3c682c4787395c680d6241
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808456"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>PowerShell för DNS-alias till Azure SQL Database

Den här artikeln innehåller ett PowerShell-skript som visar hur du kan hantera ett DNS-alias för Azure SQL Database. Skriptet kör följande cmdletar som vidtar följande åtgärder:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De cmdletar som används i kod exemplet är följande:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): skapar ett nytt DNS-alias i Azure SQL Database tjänst systemet. Aliaset refererar till Azure SQL Database Server 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Hämta och lista alla DNS-alias som har tilldelats till SQL DB server 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): ändrar Server namnet som aliaset är konfigurerat att referera till, från Server 1 till SQL DB server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): ta bort DNS-aliaset från SQL DB server 2 med hjälp av namnet på aliaset.

## <a name="dns-alias-in-connection-string"></a>DNS-alias i anslutnings sträng

Om du vill ansluta en viss Azure SQL Database Server kan en klient som SQL Server Management Studio (SSMS) tillhandahålla DNS-aliasnamnet i stället för det faktiska Server namnet. I följande exempel Server sträng ersätter aliaset *any-Unique-Alias-Name* den första punkten-avgränsade noden i den fyra nodens Server sträng:

- Exempel Server sträng: `any-unique-alias-name.database.windows.net`.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill köra demonstrations PowerShell-skriptet som du fick i den här artikeln gäller följande krav:

- En Azure-prenumeration och ett konto. Klicka [https://azure.microsoft.com/free/][https://azure.microsoft.com/free/]om du vill ha en kostnads fri utvärderings version.
- Azure PowerShell modul med cmdlet **New-AzSqlServerDNSAlias**.
  - Information om att installera och uppgradera finns i [Install Azure PowerShell module][install-Az-ps-84p] (Installera Azure PowerShell-modul).
  - Kör `Get-Module -ListAvailable Az;` i PowerShell\_ISE. exe för att hitta versionen.
- Två Azure SQL Database-servrar.

## <a name="code-example"></a>Kodexempel

Följande PowerShell-kod exempel börjar genom att tilldela litterala värden till flera variabler. Om du vill köra koden måste du först redigera alla plats hållarnas värden för att matcha de verkliga värdena i systemet. Eller så kan du bara studera koden. Och även konsolens utdata från koden anges.

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
    Connect-AzAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```

### <a name="actual-console-output-from-the-powershell-example"></a>Faktiska konsol utdata från PowerShell-exemplet

Följande konsol resultat har kopierats och klistrats in från en faktisk körning.

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

En fullständig förklaring av funktionen DNS-alias för SQL Database finns i [DNS-alias för Azure SQL Database][dns-alias-overview-37v].

<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-Az-ps-84p]: https://docs.microsoft.com/powershell/azure/install-az-ps

[dns-alias-overview-37v]: dns-alias-overview.md
