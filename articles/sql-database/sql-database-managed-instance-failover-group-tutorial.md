---
title: 'Självstudiekurs: Lägga till en hanterad instans i en redundansgrupp'
description: Lär dig att konfigurera en redundansgrupp för din Azure SQL Database-hanterade instans.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: 0f1a56fa6ea38acd8061180407eb47fe416b61e9
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631702"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Självstudiekurs: Lägga till en SQL Database-hanterad instans i en redundansgrupp

Lägg till en SQL Database-hanterad instans i en redundansgrupp. I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> - Skapa en primär hanterad instans
> - Skapa en sekundär hanterad instans som en del av en [redundansgrupp](sql-database-auto-failover-group.md). 
> - Redundanstest

  > [!NOTE]
  > - När du går igenom den här självstudien, se till att du konfigurerar dina resurser med [förutsättningar för att ställa in redundansgrupper för hanterad instans](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Det kan ta lång tid att skapa en hanterad instans. Som ett resultat kan den här guiden ta flera timmar att slutföra. Mer information om etableringstider finns i [hanterade instanshanteringsåtgärder](sql-database-managed-instance.md#managed-instance-management-operations). 
  > - Hanterade instanser som deltar i en redundansgrupp kräver antingen [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) eller två anslutna VPN-gateways. Den här självstudien innehåller steg för att skapa och ansluta VPN-gateways. Hoppa över de här stegen om du redan har Konfigurerat ExpressRoute. 


## <a name="prerequisites"></a>Krav

# <a name="portal"></a>[Portalen](#tab/azure-portal)
För att kunna följa den här självstudien måste du ha: 

- En Azure-prenumeration. [Skapa ett gratis konto](https://azure.microsoft.com/free/) om du inte redan har ett.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
För att slutföra självstudien, se till att du har följande objekt:

- En Azure-prenumeration. [Skapa ett gratis konto](https://azure.microsoft.com/free/) om du inte redan har ett.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1 - Skapa resursgrupp och primär hanterad instans
I det här steget skapar du resursgruppen och den primära hanterade instansen för redundansgruppen med hjälp av Azure-portalen eller PowerShell. 


# <a name="portal"></a>[Portalen](#tab/azure-portal) 

Skapa resursgruppen och din primära hanterade instans med Hjälp av Azure-portalen. 

1. Välj **Azure SQL** i menyn till vänster i Azure-portalen. Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid **Azure SQL** för att göra den favorit och lägg till den som ett objekt i vänsternavigering. 
1. Välj **+ Lägg till** om du vill öppna **alternativsidan Välj SQL-distribution.** Du kan visa ytterligare information om de olika databaserna genom att välja Visa information på panelen Databaser.
1. Välj **Skapa** på panelen **SQL-hanterade instanser.** 

    ![Välj hanterad instans](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. På sidan **Skapa Azure SQL-databashanterad instans** på fliken **Grunderna**
    1. Under **Projektinformation**väljer du din **prenumeration** i listrutan och väljer sedan att **skapa ny** resursgrupp. Skriv in ett namn för resursgruppen, till exempel `myResourceGroup`. 
    1. Under **Hanterad instansinformation**anger du namnet på den hanterade instansen och den region där du vill distribuera den hanterade instansen. Lämna **Beräkning + lagring** till standardvärden. 
    1. Under **Administratörskonto**anger du en `azureuser`administratörsinloggning, till exempel , och ett komplext administratörslösenord. 

    ![Skapa primär felindikator](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Lämna resten av inställningarna vid standardvärden och välj **Granska + skapa** för att granska dina hanterade instansinställningar. 
1. Välj **Skapa** om du vill skapa den primära hanterade instansen. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa resursgruppen och den primära hanterade instansen med PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your managed instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the managed instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your managed instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the managed instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary managed instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary managed instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary managed instance name is" $primaryInstance
   Write-host "Secondary managed instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure primary MI subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create primary managed instance
   
   Write-host "Creating primary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary managed instance created successfully."
   ```

Den här delen av självstudien använder följande PowerShell-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en Azure-resursgrupp.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Lägger till en undernätskonfiguration i ett virtuellt nätverk. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hämtar ett virtuellt nätverk i en resursgrupp. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar ett undernät i ett virtuellt nätverk. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Skapar en nätverkssäkerhetsgrupp. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Skapar en flödestabell. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Uppdaterar en undernätskonfiguration för ett virtuellt nätverk.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Uppdaterar ett virtuellt nätverk.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hämtar en nätverkssäkerhetsgrupp. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Lägger till en nätverkssäkerhetsregelkonfiguration i en nätverkssäkerhetsgrupp. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Uppdaterar en nätverkssäkerhetsgrupp.  | 
| [Tillägg-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Lägger till en rutt i en flödestabell. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Uppdaterar en vägtabell.  |
| [Ny-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Skapar en hanterad Azure SQL-databas-hanterad instans.  |

---

## <a name="2---create-secondary-virtual-network"></a>2 - Skapa sekundärt virtuellt nätverk
Om du använder Azure-portalen för att skapa din hanterade instans måste du skapa det virtuella nätverket separat eftersom det finns ett krav på att undernätet för den primära och sekundära hanterade instansen inte har överlappande områden. Om du använder PowerShell för att konfigurera den hanterade instansen går du vidare till steg 3. 

# <a name="portal"></a>[Portalen](#tab/azure-portal) 
Så här verifierar du undernätsområdet för det primära virtuella nätverket:
1. I [Azure-portalen](https://portal.azure.com)navigerar du till din resursgrupp och väljer det virtuella nätverket för din primära instans. 
1. Välj **Undernät** under **Inställningar** och notera **adressintervallet**. Undernätsadressintervallet för det virtuella nätverket för den sekundära hanterade instansen kan inte överlappa detta. 


   ![Primärt undernät](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Så här skapar du ett virtuellt nätverk:

1. I [Azure-portalen](https://portal.azure.com)väljer du **Skapa en resurs** och söker efter *virtuellt nätverk*. 
1. Välj alternativet **Virtuellt nätverk** som publiceras av Microsoft och välj sedan **Skapa** på nästa sida. 
1. Fyll i de obligatoriska fälten för att konfigurera det virtuella nätverket för den sekundära hanterade instansen och välj sedan **Skapa**. 

   I följande tabell visas de värden som krävs för det sekundära virtuella nätverket:

    | **Field** | Värde |
    | --- | --- |
    | **Namn** |  Namnet på det virtuella nätverket som ska användas av `vnet-sql-mi-secondary`den sekundära hanterade instansen, till exempel . |
    | **Adressutrymme** | Adressutrymmet för det virtuella `10.128.0.0/16`nätverket, till exempel . | 
    | **Prenumeration** | Prenumerationen där din primära hanterade instans och resursgrupp finns. |
    | **Regionen** | Den plats där du ska distribuera din sekundära hanterade instans. |
    | **Undernät** | Namnet på ditt undernät. `default`som standard. |
    | **Adressintervall**| Adressintervallet för ditt undernät. Detta måste vara annorlunda än det undernätsadressintervall som används av `10.128.0.0/24`det virtuella nätverket för den primära hanterade instansen, till exempel .  |
    | &nbsp; | &nbsp; |

    ![Sekundära virtuella nätverksvärden](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Det här steget är bara nödvändigt om du använder Azure-portalen för att distribuera din hanterade instans. Hoppa framåt till steg 3 om du använder PowerShell. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3 - Skapa en sekundär hanterad instans
I det här steget skapar du en sekundär hanterad instans i Azure-portalen, som också konfigurerar nätverken mellan de två hanterade instanserna. 

Din andra hanterade instans måste:
- Var tom. 
- Har ett annat undernät och IP-intervall än den primära hanterade instansen. 

# <a name="portal"></a>[Portalen](#tab/azure-portal) 

Skapa den sekundära hanterade instansen med Azure-portalen. 

1. Välj **Azure SQL** i menyn till vänster i Azure-portalen. Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid **Azure SQL** för att göra den favorit och lägg till den som ett objekt i vänsternavigering. 
1. Välj **+ Lägg till** om du vill öppna **alternativsidan Välj SQL-distribution.** Du kan visa ytterligare information om de olika databaserna genom att välja Visa information på panelen Databaser.
1. Välj **Skapa** på panelen **SQL-hanterade instanser.** 

    ![Välj hanterad instans](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. På fliken **Grunderna** på sidan **Skapa Azure SQL Database Managed Instance** fyller du i de obligatoriska fälten för att konfigurera den sekundära hanterade instansen. 

   I följande tabell visas de värden som krävs för den sekundära hanterade instansen:
 
    | **Field** | Värde |
    | --- | --- |
    | **Prenumeration** |  Prenumerationen där din primära hanterade instans finns. |
    | **Resursgrupp**| Resursgruppen där din primära hanterade instans finns. |
    | **Namn på hanterad instans** | Namnet på den nya sekundära hanterade instansen, till exempel`sql-mi-secondary`  | 
    | **Regionen**| Platsen för din sekundära hanterade instans.  |
    | **Administratörsinloggning för hanterad instans** | Inloggningen som du vill använda för den `azureuser`nya sekundära hanterade instansen, till exempel . |
    | **Lösenord** | Ett komplext lösenord som ska användas av administratörsinloggningen för den nya sekundära hanterade instansen.  |
    | &nbsp; | &nbsp; |

1. Under fliken **Nätverk** väljer du det virtuella nätverk som du skapade för den sekundära hanterade instansen i listrutan för **det virtuella nätverket.**

   ![Sekundärt MI-nätverk](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Under fliken **Ytterligare inställningar** väljer du **Ja** att använda _som redundans sekundärt_för **Geo-Replication.** Välj den primära hanterade instansen i listrutan. 
    1. Se till att sortering och tidszon matchar den primära hanterade instansen. Den primära hanterade instansen som skapades `SQL_Latin1_General_CP1_CI_AS` i den `(UTC) Coordinated Universal Time` här självstudien använde standardsortering och tidszon. 

   ![Sekundärt MI-nätverk](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Välj **Granska + skapa** om du vill granska inställningarna för den sekundära hanterade instansen. 
1. Välj **Skapa** om du vill skapa den sekundära hanterade instansen. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa den sekundära hanterade instansen med PowerShell. 

   ```powershell-interactive
   # Configure secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary managed instance created successfully."
   ```

Den här delen av självstudien använder följande PowerShell-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en Azure-resursgrupp.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Lägger till en undernätskonfiguration i ett virtuellt nätverk. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hämtar ett virtuellt nätverk i en resursgrupp. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar ett undernät i ett virtuellt nätverk. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Skapar en nätverkssäkerhetsgrupp. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Skapar en flödestabell. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Uppdaterar en undernätskonfiguration för ett virtuellt nätverk.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Uppdaterar ett virtuellt nätverk.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hämtar en nätverkssäkerhetsgrupp. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Lägger till en nätverkssäkerhetsregelkonfiguration i en nätverkssäkerhetsgrupp. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Uppdaterar en nätverkssäkerhetsgrupp.  | 
| [Tillägg-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Lägger till en rutt i en flödestabell. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Uppdaterar en vägtabell.  |
| [Ny-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Skapar en hanterad Azure SQL-databas-hanterad instans.  |

---

## <a name="4---create-primary-gateway"></a>4 - Skapa primär gateway 
För att två hanterade instanser ska kunna delta i en redundansgrupp måste det finnas antingen ExpressRoute eller en gateway som konfigurerats mellan de virtuella nätverken för de två hanterade instanserna för att tillåta nätverkskommunikation. Om du väljer att konfigurera [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) i stället för att ansluta två VPN-gateways går du vidare till [steg 7](#7---create-a-failover-group).  

Den här artikeln innehåller steg för att skapa de två VPN-gateways och ansluta dem, men du kan hoppa vidare till att skapa redundansgruppen om du har konfigurerat ExpressRoute istället. 


# <a name="portal"></a>[Portalen](#tab/azure-portal)

Skapa gatewayen för det virtuella nätverket för din primära hanterade instans med hjälp av Azure-portalen. 


1. Gå till resursgruppen i [Azure-portalen](https://portal.azure.com)och välj den **virtuella nätverksresursen** för din primära hanterade instans. 
1. Välj **Undernät** under **Inställningar** och välj sedan om du vill lägga till ett nytt **gateway-undernät**. Lämna standardvärdena. 

   ![Lägga till gateway för primär hanterad instans](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. När nätgatewayen har skapats väljer du **Skapa en** `Virtual network gateway` resurs från det vänstra navigeringsfönstret och skriver sedan i sökrutan. Välj den **virtuella nätverksgatewayresursen** som publiceras av **Microsoft**. 

   ![Skapa en ny virtuell nätverksgateway](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Fyll i de obligatoriska fälten för att konfigurera gatewayen din primära hanterade instans. 

   I följande tabell visas de värden som krävs för gatewayen för den primära hanterade instansen:
 
    | **Field** | Värde |
    | --- | --- |
    | **Prenumeration** |  Prenumerationen där din primära hanterade instans finns. |
    | **Namn** | Namnet på den virtuella nätverksgatewayen, till exempel `primary-mi-gateway`. | 
    | **Regionen** | Den region där din primära hanterade instans finns. |
    | **Gateway-typ** | Välj **VPN**. |
    | **VPN-typ** | Välj **Ruttbaserad** |
    | **Sku**| Lämna standardvärdet `VpnGw1`för . |
    | **Location**| Den plats där din primära hanterade instans och primära virtuella nätverk finns.   |
    | **Virtuellt nätverk**| Välj det virtuella nätverk som skapades i `vnet-sql-mi-primary`avsnitt 2, till exempel . |
    | **Offentlig IP-adress**| Välj **Skapa ny**. |
    | **Namn på offentlig IP-adress**| Ange ett namn på din `primary-gateway-IP`IP-adress, till exempel . |
    | &nbsp; | &nbsp; |

1. Lämna de andra värdena som standard och välj sedan **Granska + skapa** för att granska inställningarna för den virtuella nätverksgatewayen.

   ![Primära gatewayinställningar](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Välj **Skapa** om du vill skapa den nya virtuella nätverksgatewayen. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa gatewayen för det virtuella nätverket för din primära hanterade instans med PowerShell. 

   ```powershell-interactive
   # Create primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

Den här delen av självstudien använder följande PowerShell-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hämtar ett virtuellt nätverk i en resursgrupp. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Lägger till en undernätskonfiguration i ett virtuellt nätverk. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Uppdaterar ett virtuellt nätverk.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar ett undernät i ett virtuellt nätverk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Skapar en IP-konfiguration för en virtuell nätverksgateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Skapar en virtuell nätverksgateway |


---


## <a name="5---create-secondary-gateway"></a>5 - Skapa sekundär gateway 
I det här steget skapar du gatewayen för det virtuella nätverket för din sekundära hanterade instans med hjälp av Azure-portalen, 


# <a name="portal"></a>[Portalen](#tab/azure-portal)

Med hjälp av Azure-portalen upprepar du stegen i föregående avsnitt för att skapa det virtuella nätverksundernätet och gatewayen för den sekundära hanterade instansen. Fyll i de obligatoriska fälten för att konfigurera gatewayen för den sekundära hanterade instansen. 

   I följande tabell visas de värden som krävs för gatewayen för den sekundära hanterade instansen:

   | **Field** | Värde |
   | --- | --- |
   | **Prenumeration** |  Prenumerationen där din sekundära hanterade instans finns. |
   | **Namn** | Namnet på den virtuella nätverksgatewayen, till exempel `secondary-mi-gateway`. | 
   | **Regionen** | Den region där din sekundära hanterade instans finns. |
   | **Gateway-typ** | Välj **VPN**. |
   | **VPN-typ** | Välj **Ruttbaserad** |
   | **Sku**| Lämna standardvärdet `VpnGw1`för . |
   | **Location**| Den plats där din sekundära hanterade instans och sekundära virtuella nätverk finns.   |
   | **Virtuellt nätverk**| Välj det virtuella nätverk som skapades i `vnet-sql-mi-secondary`avsnitt 2, till exempel . |
   | **Offentlig IP-adress**| Välj **Skapa ny**. |
   | **Namn på offentlig IP-adress**| Ange ett namn på din `secondary-gateway-IP`IP-adress, till exempel . |
   | &nbsp; | &nbsp; |

   ![Inställningar för sekundär gateway](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa gatewayen för det virtuella nätverket för den sekundära hanterade instansen med PowerShell. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

Den här delen av självstudien använder följande PowerShell-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hämtar ett virtuellt nätverk i en resursgrupp. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Lägger till en undernätskonfiguration i ett virtuellt nätverk. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Uppdaterar ett virtuellt nätverk.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar ett undernät i ett virtuellt nätverk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Skapar en IP-konfiguration för en virtuell nätverksgateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Skapar en virtuell nätverksgateway |

---


## <a name="6---connect-the-gateways"></a>6 - Anslut gateways
Skapa i det här steget en dubbelriktad anslutning mellan de två gatewayerna för de två virtuella nätverken. 


# <a name="portal"></a>[Portalen](#tab/azure-portal)

Anslut de två gatewayerna med Azure-portalen. 


1. Välj **Skapa en resurs** från [Azure-portalen](https://portal.azure.com).
1. Skriv `connection` i sökrutan och tryck sedan på retur för att söka, vilket tar dig till **anslutningsresursen,** som publiceras av Microsoft.
1. Välj **Skapa** för att skapa anslutningen. 
1. På fliken **Grunderna** markerar du följande värden och väljer sedan **OK**. 
    1. Välj `VNet-to-VNet` för **typen Anslutning**. 
    1. Välj din prenumeration från listrutan. 
    1. Välj resursgruppen för den hanterade instansen i listrutan. 
    1. Välj platsen för din primära hanterade instans i listrutan 
1. Markera eller ange följande värden på fliken **Inställningar** och välj sedan **OK:**
    1. Välj den primära nätverksgatewayen för den `Primary-Gateway`första virtuella **nätverksgatewayen,** till exempel .  
    1. Välj den sekundära nätverksgatewayen för den `Secondary-Gateway`andra virtuella **nätverksgatewayen,** till exempel . 
    1. Markera kryssrutan bredvid **Upprätta dubbelriktad anslutning**. 
    1. Lämna antingen standardnamnet för den primära anslutningen eller byt namn på det till ett värde som du väljer. 
    1. Ange en **delad nyckel (PSK)** för `mi1m2psk`anslutningen, till exempel . 

   ![Skapa gatewayanslutning](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. På fliken **Sammanfattning** granskar du inställningarna för dubbelriktad anslutning och väljer sedan **OK** för att skapa anslutningen. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Anslut de två gatewayerna med PowerShell. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

Den här delen av självstudien använder följande PowerShell-cmdlet:

| Kommando | Anteckningar |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Skapar en anslutning mellan de två virtuella nätverksgatewayerna.   |

---


## <a name="7---create-a-failover-group"></a>7 - Skapa en redundansgrupp
I det här steget skapar du redundansgruppen och lägger till båda hanterade instanser i den. 


# <a name="portal"></a>[Portalen](#tab/azure-portal)
Skapa redundansgruppen med Azure-portalen. 


1. Välj **Azure SQL** i menyn till vänster i [Azure-portalen](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid **Azure SQL** för att göra den favorit och lägg till den som ett objekt i vänsternavigering. 
1. Markera den primära hanterade instans som du `sql-mi-primary`skapade i det första avsnittet, till exempel . 
1. Under **Inställningar**navigerar du till **Instansundanslagsgrupper** och väljer sedan att **lägga till grupp** för att öppna sidan **Instansundanslagsgrupp.** 

   ![Lägga till en redundansgrupp](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. På sidan **Instansundanslagsgrupp** skriver du namnet på `failovergrouptutorial` redundansgruppen, till exempel `sql-mi-secondary` och väljer sedan den sekundära hanterade instansen, till exempel från listrutan. Välj **Skapa** om du vill skapa redundansgruppen. 

   ![Skapa redundansgrupp](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. När redundansgruppsdistributionen är klar kommer du tillbaka till **redundansgruppssidan.** 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Skapa redundansgruppen med PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

Den här delen av självstudien använder följande PowerShell-cmdlet:

| Kommando | Anteckningar |
|---|---|
| [Nya-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Skapar en ny Azure SQL Database-hanterad instans redundansgrupp.  |


---


## <a name="8---test-failover"></a>8 - Testa redundans
I det här steget kommer du att växla över redundansgruppen till den sekundära servern och sedan växla tillbaka med Azure-portalen. 


# <a name="portal"></a>[Portalen](#tab/azure-portal)
Testa redundans med Azure-portalen. 


1. Navigera till din _sekundära_ hanterade instans i [Azure-portalen](https://portal.azure.com) och välj **Instans redundansgrupper** under inställningar. 
1. Granska vilken hanterad instans som är den primära och vilken hanterad instans som är den sekundära. 
1. Välj **Redundans** och välj sedan **Ja** på varningen om att TDS-sessioner kopplas från. 

   ![Växla över redundansgruppen](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Granska vilken manged instans är den primära och vilken instans som är den sekundära. Om fail over lyckades, bör de två instanserna ha bytt roller. 

   ![Hanterade instanser har bytt roller efter redundans](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Gå till den nya _sekundära_ hanterade instansen och välj **Redundans** igen för att misslyckas med den primära instansen tillbaka till den primära rollen. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Testa redundans med PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Återgår till redundansgruppen till den primära servern:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

Den här delen av självstudien använder följande PowerShell-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hämtar eller listar hanterade förekomstöverundansättningar grupper.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Kör en redundansväxling för en hanterad instans redundansgrupp. | 

---



## <a name="clean-up-resources"></a>Rensa resurser
Rensa resurser genom att först ta bort den hanterade instansen, sedan det virtuella klustret, sedan eventuella återstående resurser och slutligen resursgruppen. 

# <a name="portal"></a>[Portalen](#tab/azure-portal)
1. Navigera till din resursgrupp i [Azure-portalen](https://portal.azure.com). 
1. Markera hanterade instanser och välj sedan **Ta bort**. Skriv `yes` i textrutan för att bekräfta att du vill ta bort resursen och välj sedan **Ta bort**. Den här processen kan ta lite tid att slutföra i bakgrunden, och tills det är klart kan du inte ta bort det *virtuella klustret* eller andra beroende resurser. Övervaka borttagningen på fliken Aktivitet för att bekräfta att den hanterade instansen har tagits bort. 
1. När den hanterade instansen har tagits bort tar du bort det *virtuella klustret* genom att markera det i resursgruppen och väljer sedan **Ta bort**. Skriv `yes` i textrutan för att bekräfta att du vill ta bort resursen och välj sedan **Ta bort**. 
1. Ta bort eventuella återstående resurser. Skriv `yes` i textrutan för att bekräfta att du vill ta bort resursen och välj sedan **Ta bort**. 
1. Ta bort resursgruppen genom att välja **Ta bort resursgrupp**, `myResourceGroup`skriva in namnet på resursgruppen och sedan välja **Ta bort**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du måste ta bort resursgruppen två gånger. Om du tar bort resursgruppen första gången tas den hanterade instansen `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`och de virtuella klustren bort, men felmeddelandet misslyckas sedan . Kör kommandot Ta bort AzResourceGroup en andra gång för att ta bort eventuella kvarvarande resurser samt resursgruppen.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

Den här delen av självstudien använder följande PowerShell-cmdlet:

| Kommando | Anteckningar |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp. |

---

## <a name="full-script"></a>Fullständigt skript

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en Azure-resursgrupp.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Lägger till en undernätskonfiguration i ett virtuellt nätverk. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hämtar ett virtuellt nätverk i en resursgrupp. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar ett undernät i ett virtuellt nätverk. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Skapar en nätverkssäkerhetsgrupp. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Skapar en flödestabell. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Uppdaterar en undernätskonfiguration för ett virtuellt nätverk.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Uppdaterar ett virtuellt nätverk.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hämtar en nätverkssäkerhetsgrupp. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Lägger till en nätverkssäkerhetsregelkonfiguration i en nätverkssäkerhetsgrupp. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Uppdaterar en nätverkssäkerhetsgrupp.  | 
| [Tillägg-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Lägger till en rutt i en flödestabell. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Uppdaterar en vägtabell.  |
| [Ny-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Skapar en hanterad Azure SQL-databas-hanterad instans.  |
| [Få-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Returnerar information om Azure SQL-hanterad databasinstans. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Skapar en IP-konfiguration för en virtuell nätverksgateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Skapar en virtuell nätverksgateway |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Skapar en anslutning mellan de två virtuella nätverksgatewayerna.   |
| [Nya-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Skapar en ny Azure SQL Database-hanterad instans redundansgrupp.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hämtar eller listar hanterade förekomstöverundansättningar grupper.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Kör en redundansväxling för en hanterad instans redundansgrupp. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp. | 

# <a name="portal"></a>[Portalen](#tab/azure-portal) 

Det finns inga skript tillgängliga för Azure-portalen.

---

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du konfigurerat en redundansgrupp mellan två hanterade instanser. Du har lärt dig att:

> [!div class="checklist"]
> - Skapa en primär hanterad instans
> - Skapa en sekundär hanterad instans som en del av en [redundansgrupp](sql-database-auto-failover-group.md). 
> - Redundanstest

Gå vidare till nästa snabbstart om hur du ansluter till din hanterade instans och hur du återställer en databas till den hanterade instansen: 

> [!div class="nextstepaction"]
> [Ansluta till den hanterade instansen](sql-database-managed-instance-configure-vm.md)
> [Återställ en databas till en hanterad instans](sql-database-managed-instance-get-started-restore.md)


