---
title: PowerShell för VNet-slutpunkter och regler för enskilda och poolade databaser
description: Tillhandahåller PowerShell-skript för att skapa och hantera slutpunkter för virtuella tjänster för din Azure SQL-databas och Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, vanto
ms.date: 03/12/2019
tags: azure-synapse
ms.openlocfilehash: 1e8ec394eab1df0aebe394b8acebc74c7ed49e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124690"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>PowerShell: Skapa en slutpunkt för virtuell tjänst och VNet-regel för SQL

*Virtuella nätverksregler* är en brandväggssäkerhetsfunktion som styr om databasservern för dina enskilda databaser och elastiskpool i Azure [SQL Database](sql-database-technical-overview.md) eller för dina databaser i [Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) accepterar kommunikation som skickas från vissa undernät i virtuella nätverk.

> [!IMPORTANT]
> Den här artikeln gäller Azure SQL-server och både SQL Database och datalager i Azure Synapse som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när du refererar till både SQL Database och Azure Synapse. Den här artikeln gäller *inte* för en **hanterad instansdistribution** i Azure SQL Database eftersom den inte har en tjänstslutpunkt associerad med den.

Den här artikeln innehåller och förklarar ett PowerShell-skript som vidtar följande åtgärder:

1. Skapar en *Slutpunkt för* Microsoft Azure Virtual Service i undernätet.
2. Lägger till slutpunkten i brandväggen för din Azure SQL Database-server för att skapa en *virtuell nätverksregel*.

Dina motiv för att skapa en regel förklaras i: [Slutpunkter för virtuella tjänster för Azure SQL Database][sql-db-vnet-service-endpoint-rule-overview-735r].

> [!TIP]
> Om allt du behöver är att bedöma eller lägga till *slutpunktstypen för* virtuell tjänst för SQL Database i ditt undernät kan du gå vidare till vårt mer [direkta PowerShell-skript](#a-verify-subnet-is-endpoint-ps-100).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

## <a name="major-cmdlets"></a>Större cmdlets

Den här artikeln betonar cmdleten **New-AzSqlServerVirtualNetworkRule** som lägger till undernätslutpunkten i åtkomstkontrolllistan (ACL) på azure SQL Database-servern och skapar därmed en regel.

Följande lista visar sekvensen av andra *större* cmdlets som du måste köra för att förbereda för ditt samtal till **New-AzSqlServerVirtualNetworkRule**. I den här artikeln sker dessa anrop i [skript 3 "Virtual Network rule":](#a-script-30)

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig): Skapar ett undernätsobjekt.
2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork): Skapar ditt virtuella nätverk, vilket ger det undernätet.
3. [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig): Tilldelar en slutpunkt för virtuell tjänst till undernätet.
4. [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork): Kvarstår uppdateringar som gjorts i ditt virtuella nätverk.
5. [Ny-AzSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule): När undernätet är en slutpunkt lägger du till ditt undernät som en virtuell nätverksregel i ACL för din Azure SQL Database-server.
   - Den här cmdleten erbjuder parametern **-IgnoreMissingVNetServiceEndpoint**, med början i Azure RM PowerShell Module version 5.1.1.

## <a name="prerequisites-for-running-powershell"></a>Förutsättningar för att köra PowerShell

- Du kan redan logga in på Azure, till exempel via [Azure-portalen][http-azure-portal-link-ref-477t].
- Du kan redan köra PowerShell-skript.

> [!NOTE]
> Kontrollera att tjänstslutpunkter är aktiverade för det virtuella nätverk/undernät som du vill lägga till på servern annars misslyckas skapandet av VNet-brandväggsregeln.

## <a name="one-script-divided-into-four-chunks"></a>Ett skript uppdelat i fyra bitar

Vårt powershell-skript för demonstration är indelat i en sekvens av mindre skript. Divisionen underlättar inlärningen och ger flexibilitet. Skripten måste köras i den angivna sekvensen. Om du inte har tid nu att köra skripten visas vår faktiska testutdata efter skript 4.

<a name="a-script-10" />

### <a name="script-1-variables"></a>Skript 1: Variabler

Det här första PowerShell-skriptet tilldelar variabler värden. De efterföljande skripten beror på dessa variabler.

> [!IMPORTANT]
> Innan du kör skriptet kan du redigera värdena om du vill. Om du till exempel redan har en resursgrupp kanske du vill redigera resursgruppsnamnet som tilldelat värde.
>
> Ditt prenumerationsnamn ska redigeras till skriptet.

### <a name="powershell-script-1-source-code"></a>PowerShell-skript 1 källkod

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```

<a name="a-script-20" />

### <a name="script-2-prerequisites"></a>Manus 2: Förutsättningar

Det här skriptet förbereder för nästa skript, där slutpunktsåtgärden är. Det här skriptet skapar följande listade objekt, men bara om de inte redan finns. Du kan hoppa över skript 2 om du är säker på att dessa objekt redan finns:

- Azure-resursgrupp
- Server för Azure SQL Database

### <a name="powershell-script-2-source-code"></a>PowerShell-skript 2 källkod

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";
    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else {
    Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; 
}

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Skript 3: Skapa en slutpunkt och en regel

Skriptet skapar ett virtuellt nätverk med ett undernät. Sedan tilldelar skriptet slutpunktstypen **Microsoft.Sql** till ditt undernät. Slutligen lägger skriptet till ditt undernät i åtkomstkontrollistan (ACL) på SQL Database-servern och skapar därmed en regel.

### <a name="powershell-script-3-source-code"></a>PowerShell-skript 3 källkod

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40" />

## <a name="script-4-clean-up"></a>Manus 4: Sanering

Det här slutliga skriptet tar bort de resurser som tidigare skript har skapats för demonstrationen. Skriptet ber dock om bekräftelse innan följande tas bort:

- Server för Azure SQL Database
- Azure-resursgrupp

Du kan köra skript 4 när som helst efter att skript 1 har slutförts.

### <a name="powershell-script-4-source-code"></a>PowerShell-skript 4 källkod

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno) {
    Write-Host "Remove the Azure SQL DB server.";

    Remove-AzSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;

    Write-Host "Remove the Azure Resource Group.";

    Remove-AzResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Verifiera att undernätet är en slutpunkt

Du kanske har ett undernät som redan har tilldelats **microsoft.Sql-typnamnet,** vilket innebär att det redan är en slutpunkt för virtuell tjänst. Du kan använda [Azure-portalen][http-azure-portal-link-ref-477t] för att skapa en virtuell nätverksregel från slutpunkten.

Du kanske inte är osäker på om undernätet har namnet på **typen Microsoft.Sql.** Du kan köra följande PowerShell-skript för att vidta följande åtgärder:

1. Ta reda på om ditt undernät har namnet på **typen Microsoft.Sql.**
2. Du kan också tilldela typnamnet om det saknas.
    - Skriptet ber dig att *bekräfta*, innan det gäller frånvarande typ namn.

### <a name="phases-of-the-script"></a>Faser av skriptet

Här är faserna i PowerShell-skriptet:

1. LOGGA in på ditt Azure-konto, behövs bara en gång per PS-session.  Tilldela variabler.
2. Sök efter ditt virtuella nätverk och sedan efter ditt undernät.
3. Är undernätet taggat som Microsoft.Sql-slutpunktsservertyp? **Microsoft.Sql**
4. Lägg till en slutpunkt för virtual service av typen **Microsoft.Sql**i undernätet.

> [!IMPORTANT]
> Innan du kör skriptet måste du redigera de värden som tilldelats $-variablerna, högst upp i skriptet.

### <a name="direct-powershell-source-code"></a>Direkt PowerShell-källkod

Det här PowerShell-skriptet uppdaterar ingenting, såvida du inte svarar ja om is ber dig om bekräftelse. Skriptet kan lägga till typnamnet **Microsoft.Sql** i undernätet. Men skriptet försöker bara lägga till om ditt undernät saknar typnamnet.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
