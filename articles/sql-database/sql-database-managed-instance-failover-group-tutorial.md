---
title: 'Självstudie: lägga till en hanterad instans i en failover-grupp'
description: Lär dig hur du konfigurerar en failover-grupp för din Azure SQL Database hanterade instans.
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
ms.openlocfilehash: bf83155e971061f22e5f5fc33d216b58621c9249
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462657"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Självstudie: Lägg till en SQL Database Hanterad instans i en failover-grupp

Lägg till en SQL Database Hanterad instans i en failover-grupp. I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> - Skapa en primär hanterad instans
> - Skapa en sekundär hanterad instans som en del av en [grupp för växling vid fel](sql-database-auto-failover-group.md). 
> - Redundanstest

  > [!NOTE]
  > - När du går igenom den här självstudien kontrollerar du att du konfigurerar dina resurser med [förutsättningarna för att konfigurera grupper för hantering av redundans](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Det kan ta lång tid att skapa en hanterad instans. Därför kan det ta flera timmar att slutföra den här självstudien. Mer information om etablerings tider finns i [hanterings åtgärder för hanterade instanser](sql-database-managed-instance.md#managed-instance-management-operations). 
  > - Hanterade instanser som ingår i en failover-grupp kräver antingen [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) eller två anslutna VPN-gatewayer. Den här självstudien innehåller steg för att skapa och ansluta VPN-gatewayer. Hoppa över de här stegen om du redan har konfigurerat ExpressRoute. 


## <a name="prerequisites"></a>Förutsättningar

# <a name="portal"></a>[Portal](#tab/azure-portal)
För att kunna följa den här självstudien måste du ha: 

- En Azure-prenumeration. [Skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) om du inte redan har ett.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Kontrol lera att du har följande objekt för att slutföra självstudien:

- En Azure-prenumeration. [Skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) om du inte redan har ett.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1 – Skapa en resurs grupp och en primär hanterad instans
I det här steget ska du skapa resurs gruppen och den primära hanterade instansen för din failover-grupp med hjälp av Azure Portal eller PowerShell. 


# <a name="portal"></a>[Portal](#tab/azure-portal) 

Skapa resurs gruppen och den primära hanterade instansen med hjälp av Azure Portal. 

1. Välj **Azure SQL** i den vänstra menyn i Azure Portal. Om **Azure SQL** inte finns i listan väljer du **alla tjänster**och skriver sedan Azure SQL i sökrutan. Valfritt Välj stjärnan bredvid **Azure SQL** för att Favorita den och lägga till den som ett objekt i navigeringen till vänster. 
1. Välj **+ Lägg** till för att öppna **alternativ sidan Välj SQL-distribution** . Du kan visa ytterligare information om de olika databaserna genom att välja Visa information på panelen databaser.
1. Välj **skapa** på panelen **SQL Managed instances** . 

    ![Välj hanterad instans](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. På sidan **skapa Azure SQL Database Hanterad instans** på fliken **grundläggande**
    1. Under **projekt information**väljer du din **prenumeration** i list rutan och väljer sedan att skapa en **ny** resurs grupp. Ange ett namn för resurs gruppen, t. ex. `myResourceGroup`. 
    1. Under **information om hanterade instanser**anger du namnet på den hanterade instansen och den region där du vill distribuera din hanterade instans. Lämna kvar **beräkningen + lagringen** med standardvärdena. 
    1. Under **administratörs konto**anger du en Administratörs inloggning, till exempel `azureuser`och ett komplext administratörs lösen ord. 

    ![Skapa primär MI](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Lämna resten av inställningarna med standardvärden och välj **Granska + skapa** för att granska inställningarna för hanterade instanser. 
1. Välj **skapa** för att skapa din primära hanterade instans. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa din resurs grupp och den primära hanterade instansen med PowerShell. 

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

I den här delen av självstudien används följande PowerShell-cmdletar:

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en Azure-resursgrupp.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Lägger till en under näts konfiguration i ett virtuellt nätverk. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hämtar ett virtuellt nätverk i en resursgrupp. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar ett undernät i ett virtuellt nätverk. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Skapar en nätverkssäkerhetsgrupp. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Skapar en routningstabell. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Uppdaterar en under näts konfiguration för ett virtuellt nätverk.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Uppdaterar ett virtuellt nätverk.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hämtar en nätverks säkerhets grupp. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Lägger till en nätverks säkerhets regel konfiguration i en nätverks säkerhets grupp. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Uppdaterar en nätverks säkerhets grupp.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Lägger till en väg i en routningstabell. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Uppdaterar en routningstabell.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Skapar en Azure SQL Database Hanterad instans.  |

---

## <a name="2---create-secondary-virtual-network"></a>2 – Skapa ett sekundärt virtuellt nätverk
Om du använder Azure Portal för att skapa din hanterade instans måste du skapa det virtuella nätverket separat eftersom det finns ett krav att under nätet för den primära och sekundära hanterade instansen inte har några överlappande intervall. Om du använder PowerShell för att konfigurera din hanterade instans går du vidare till steg 3. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 
Följ dessa steg om du vill kontrol lera under näts intervallet för ditt primära virtuella nätverk:
1. I [Azure Portal](https://portal.azure.com)navigerar du till din resurs grupp och väljer det virtuella nätverket för din primära instans. 
1. Välj **undernät** under **Inställningar** och notera **adress intervallet**. Under nätets adress intervall för det virtuella nätverket för den sekundära hanterade instansen får inte överlappa detta. 


   ![Primärt undernät](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Följ dessa steg om du vill skapa ett virtuellt nätverk:

1. I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs** och söker efter *virtuellt nätverk*. 
1. Välj alternativet **Virtual Network** som publicerats av Microsoft och välj sedan **skapa** på nästa sida. 
1. Fyll i de obligatoriska fälten om du vill konfigurera det virtuella nätverket för den sekundära hanterade instansen och välj sedan **skapa**. 

   Följande tabell visar de värden som krävs för det sekundära virtuella nätverket:

    | **Fält** | Värde |
    | --- | --- |
    | **Namn** |  Namnet på det virtuella nätverk som ska användas av den sekundära hanterade instansen, t. ex. `vnet-sql-mi-secondary`. |
    | **Adressutrymme** | Adress utrymmet för det virtuella nätverket, t. ex. `10.128.0.0/16`. | 
    | **Prenumeration** | Den prenumeration där den primära hanterade instansen och resurs gruppen finns. |
    | **Region** | Den plats där du ska distribuera den sekundära hanterade instansen. |
    | **Undernät** | Namnet på under nätet. `default` tillhandahålls som standard. |
    | **Adressintervall**| Adress intervallet för ditt undernät. Detta måste vara ett annat än det under näts adress intervall som används av den primära hanterade instansens virtuella nätverk, till exempel `10.128.0.0/24`.  |
    | &nbsp; | &nbsp; |

    ![Sekundära virtuella nätverks värden](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Det här steget behövs bara om du använder Azure Portal för att distribuera den hanterade instansen. Gå vidare till steg 3 om du använder PowerShell. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3 – skapa en sekundär hanterad instans
I det här steget ska du skapa en sekundär hanterad instans i Azure Portal, vilket även konfigurerar nätverket mellan de två hanterade instanserna. 

Din andra hanterade instans måste:
- Vara tomt. 
- Ha ett annat undernät och IP-intervall än den primära hanterade instansen. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Skapa den sekundära hanterade instansen med hjälp av Azure Portal. 

1. Välj **Azure SQL** i den vänstra menyn i Azure Portal. Om **Azure SQL** inte finns i listan väljer du **alla tjänster**och skriver sedan Azure SQL i sökrutan. Valfritt Välj stjärnan bredvid **Azure SQL** för att Favorita den och lägga till den som ett objekt i navigeringen till vänster. 
1. Välj **+ Lägg** till för att öppna **alternativ sidan Välj SQL-distribution** . Du kan visa ytterligare information om de olika databaserna genom att välja Visa information på panelen databaser.
1. Välj **skapa** på panelen **SQL Managed instances** . 

    ![Välj hanterad instans](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. På fliken **grundläggande** på sidan **Skapa Azure SQL Database Hanterad instans** fyller du i fälten som krävs för att konfigurera din sekundära hanterade instans. 

   Följande tabell visar de värden som krävs för den sekundära hanterade instansen:
 
    | **Fält** | Värde |
    | --- | --- |
    | **Prenumeration** |  Den prenumeration där din primära hanterade instans är. |
    | **Resursgrupp**| Resurs gruppen där den primära hanterade instansen finns. |
    | **Namn på hanterad instans** | Namnet på den nya sekundära hanterade instansen, till exempel `sql-mi-secondary`  | 
    | **Region**| Platsen för den sekundära hanterade instansen.  |
    | **Administratörsinloggning för hanterad instans** | Den inloggning som du vill använda för den nya sekundära hanterade instansen, till exempel `azureuser`. |
    | **Lösenord** | Ett komplext lösen ord som ska användas av administratörs inloggningen för den nya sekundära hanterade instansen.  |
    | &nbsp; | &nbsp; |

1. Under fliken **nätverk** väljer du det virtuella nätverk som du skapade för den sekundära hanterade instansen i list rutan för **Virtual Network**.

   ![Sekundärt MI-nätverk](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. Under fliken **ytterligare inställningar** , för **geo-replikering**väljer du **Ja** för att _använda som sekundär redundans_. Välj den primära hanterade instansen i list rutan. 
    1. Se till att sorterings-och tids zonen matchar den primära hanterade instansen. Den primära hanterade instans som skapas i den här självstudien använde standardvärdet för `SQL_Latin1_General_CP1_CI_AS` sortering och `(UTC) Coordinated Universal Time` tidszon. 

   ![Sekundärt MI-nätverk](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. Välj **Granska + skapa** för att granska inställningarna för din sekundära hanterade instans. 
1. Välj **skapa** för att skapa en sekundär hanterad instans. 

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

I den här delen av självstudien används följande PowerShell-cmdletar:

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en Azure-resursgrupp.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Lägger till en under näts konfiguration i ett virtuellt nätverk. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hämtar ett virtuellt nätverk i en resursgrupp. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar ett undernät i ett virtuellt nätverk. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Skapar en nätverkssäkerhetsgrupp. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Skapar en routningstabell. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Uppdaterar en under näts konfiguration för ett virtuellt nätverk.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Uppdaterar ett virtuellt nätverk.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hämtar en nätverks säkerhets grupp. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Lägger till en nätverks säkerhets regel konfiguration i en nätverks säkerhets grupp. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Uppdaterar en nätverks säkerhets grupp.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Lägger till en väg i en routningstabell. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Uppdaterar en routningstabell.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Skapar en Azure SQL Database Hanterad instans.  |

---

## <a name="4---create-primary-gateway"></a>4 – Skapa primär Gateway 
För två hanterade instanser att delta i en grupp för redundans måste det finnas antingen ExpressRoute eller en gateway som kon figurer ATS mellan de virtuella nätverken i de två hanterade instanserna för att tillåta nätverkskommunikation. Om du väljer att konfigurera [ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) i stället för att ansluta två VPN-gatewayer kan du gå vidare till [steg 7](#7---create-a-failover-group).  

Den här artikeln innehåller steg för att skapa de två VPN-gatewayerna och ansluta dem, men du kan gå vidare till att skapa en failover-grupp om du har konfigurerat ExpressRoute i stället. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa gatewayen för det virtuella nätverket för den primära hanterade instansen med hjälp av Azure Portal. 


1. I [Azure Portal](https://portal.azure.com)går du till din resurs grupp och väljer den **virtuella nätverks** resursen för din primära hanterade instans. 
1. Välj **undernät** under **Inställningar** och välj sedan för att lägga till ett nytt **Gateway-undernät**. Låt standardvärdena vara kvar. 

   ![Lägg till gateway för primär hanterad instans](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. När du har skapat en gateway för undernät väljer du **skapa en resurs** i det vänstra navigerings fönstret och skriver `Virtual network gateway` i sökrutan. Välj den **virtuella nätverks-Gateway** -resurs som publicerats av **Microsoft**. 

   ![Skapa en ny virtuell nätverksgateway](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Fyll i de obligatoriska fälten om du vill konfigurera gatewayen för den primära hanterade instansen. 

   I följande tabell visas de värden som krävs för gatewayen för den primära hanterade instansen:
 
    | **Fält** | Värde |
    | --- | --- |
    | **Prenumeration** |  Den prenumeration där din primära hanterade instans är. |
    | **Namn** | Namnet på den virtuella Nätverksgatewayen, t. ex. `primary-mi-gateway`. | 
    | **Region** | Den region där den sekundära hanterade instansen är. |
    | **Typ av Gateway** | Välj **VPN**. |
    | **VPN-typ** | Välj **Route-baserad** |
    | **SKU**| Lämna standardvärdet för `VpnGw1`. |
    | **Plats**| Den plats där din primära hanterade instans och det primära virtuella nätverket är.   |
    | **Virtuellt nätverk**| Välj det virtuella nätverk som skapades i avsnitt 2, t. ex. `vnet-sql-mi-primary`. |
    | **Offentlig IP-adress**| Välj **Skapa ny**. |
    | **Namn på offentlig IP-adress**| Ange ett namn för din IP-adress, t. ex. `primary-gateway-IP`. |
    | &nbsp; | &nbsp; |

1. Lämna de andra värdena som standard och välj sedan **Granska + skapa** för att granska inställningarna för din virtuella nätverksgateway.

   ![Inställningar för primär Gateway](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Välj **skapa** för att skapa din nya virtuella nätverksgateway. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa gatewayen för det virtuella nätverket för den primära hanterade instansen med PowerShell. 

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

I den här delen av självstudien används följande PowerShell-cmdletar:

| Kommando | Anteckningar |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hämtar ett virtuellt nätverk i en resursgrupp. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Lägger till en under näts konfiguration i ett virtuellt nätverk. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Uppdaterar ett virtuellt nätverk.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar ett undernät i ett virtuellt nätverk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Skapar en IP-konfiguration för en Virtual Network Gateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Skapar en Virtual Network Gateway |


---


## <a name="5---create-secondary-gateway"></a>5 – skapa en sekundär Gateway 
I det här steget skapar du en gateway för det virtuella nätverket för den sekundära hanterade instansen med hjälp av Azure Portal, 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Använd Azure Portal och upprepa stegen i föregående avsnitt för att skapa det virtuella nätverkets undernät och gateway för den sekundära hanterade instansen. Fyll i de obligatoriska fälten om du vill konfigurera gatewayen för din sekundära hanterade instans. 

   I följande tabell visas de värden som krävs för gatewayen för den sekundära hanterade instansen:

   | **Fält** | Värde |
   | --- | --- |
   | **Prenumeration** |  Prenumerationen där den sekundära hanterade instansen är. |
   | **Namn** | Namnet på den virtuella Nätverksgatewayen, t. ex. `secondary-mi-gateway`. | 
   | **Region** | Den region där den sekundära hanterade instansen är. |
   | **Typ av Gateway** | Välj **VPN**. |
   | **VPN-typ** | Välj **Route-baserad** |
   | **SKU**| Lämna standardvärdet för `VpnGw1`. |
   | **Plats**| Den plats där den sekundära hanterade instansen och det sekundära virtuella nätverket är.   |
   | **Virtuellt nätverk**| Välj det virtuella nätverk som skapades i avsnitt 2, t. ex. `vnet-sql-mi-secondary`. |
   | **Offentlig IP-adress**| Välj **Skapa ny**. |
   | **Namn på offentlig IP-adress**| Ange ett namn för din IP-adress, t. ex. `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Inställningar för sekundär Gateway](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa gatewayen för det virtuella nätverket för den sekundära hanterade instansen med hjälp av PowerShell. 

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

I den här delen av självstudien används följande PowerShell-cmdletar:

| Kommando | Anteckningar |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hämtar ett virtuellt nätverk i en resursgrupp. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Lägger till en under näts konfiguration i ett virtuellt nätverk. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Uppdaterar ett virtuellt nätverk.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar ett undernät i ett virtuellt nätverk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Skapar en IP-konfiguration för en Virtual Network Gateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Skapar en Virtual Network Gateway |

---


## <a name="6---connect-the-gateways"></a>6 – Anslut gatewayerna
I det här steget skapar du en dubbelriktad anslutning mellan de två gatewayerna för de två virtuella nätverken. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Anslut de två gatewayerna med hjälp av Azure Portal. 


1. Välj **skapa en resurs** från [Azure Portal](https://portal.azure.com).
1. Skriv `connection` i sökrutan och tryck sedan på RETUR för att söka, som tar dig till **anslutnings** resursen som publicerats av Microsoft.
1. Välj **skapa** för att skapa din anslutning. 
1. På fliken **grundläggande** inställningar väljer du följande värden och väljer sedan **OK**. 
    1. Välj `VNet-to-VNet` för **anslutnings typen**. 
    1. Välj din prenumeration från listrutan. 
    1. Välj resurs grupp för din hanterade instans i list rutan. 
    1. Välj platsen för din primära hanterade instans i list rutan 
1. På fliken **Inställningar** väljer eller anger du följande värden och väljer sedan **OK**:
    1. Välj den primära Nätverksgatewayen för den **första virtuella Nätverksgatewayen**, till exempel `Primary-Gateway`.  
    1. Välj den sekundära Nätverksgatewayen för den **andra virtuella Nätverksgatewayen**, till exempel `Secondary-Gateway`. 
    1. Markera kryss rutan bredvid **upprätta dubbelriktad anslutning**. 
    1. Lämna antingen standard namnet för primär anslutning eller Byt namn på det till önskat värde. 
    1. Ange en **delad nyckel (PSK)** för anslutningen, till exempel `mi1m2psk`. 

   ![Skapa Gateway-anslutning](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. På fliken **Sammanfattning** granskar du inställningarna för din dubbelriktade anslutning och väljer sedan **OK** för att skapa anslutningen. 


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

I den här delen av självstudien används följande PowerShell-cmdlet:

| Kommando | Anteckningar |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Skapar en anslutning mellan de två virtuella Nätverksgatewayen.   |

---


## <a name="7---create-a-failover-group"></a>7 – skapa en grupp för redundans
I det här steget skapar du gruppen redundans och lägger till båda hanterade instanser till den. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Skapa gruppen redundans med hjälp av Azure Portal. 


1. Välj **Azure SQL** i den vänstra menyn i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **alla tjänster**och skriver sedan Azure SQL i sökrutan. Valfritt Välj stjärnan bredvid **Azure SQL** för att Favorita den och lägga till den som ett objekt i navigeringen till vänster. 
1. Välj den primära hanterade instans som du skapade i det första avsnittet, t. ex. `sql-mi-primary`. 
1. Under **Inställningar**navigerar du till **instans grupper för redundans** och väljer sedan att **lägga till grupp** för att öppna sidan **instans redundans grupp** . 

   ![Lägg till en grupp för redundans](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. På sidan **instans redundans** anger du namnet på din grupp för redundans, till exempel `failovergrouptutorial` och väljer sedan den sekundära hanterade instansen, till exempel `sql-mi-secondary` i list rutan. Välj **skapa** för att skapa din grupp för redundans. 

   ![Skapa redundans grupp](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. När redundansväxlingen är klar kommer du tillbaka till sidan för **redundans** . 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Skapa gruppen för växling vid fel med hjälp av PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

I den här delen av självstudien används följande PowerShell-cmdlet:

| Kommando | Anteckningar |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Skapar en ny Azure SQL Database Hanterad instans redundans grupp.  |


---


## <a name="8---test-failover"></a>8-testa redundans
I det här steget kommer du inte att kunna redundansväxla gruppen till den sekundära servern och sedan växla tillbaka med Azure Portal. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Testa redundans med Azure Portal. 


1. Navigera till den _sekundära_ hanterade instansen i [Azure Portal](https://portal.azure.com) och välj **instans grupper för redundans** under Inställningar. 
1. Granska vilken hanterad instans som är primär och vilken hanterad instans som är sekundär. 
1. Välj **redundans** och välj sedan **Ja** på varningen om TDS-sessioner som kopplas från. 

   ![Redundansväxla failover-gruppen](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Granska vilken hanterade-instans som är primär och vilken instans som är sekundär. Om redundans har slutförts ska de två instanserna ha växlade roller. 

   ![Hanterade instanser har växlat roller efter redundans](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Gå till den nya _sekundära_ hanterade instansen och välj **redundans** igen så att den primära instansen inte kan återställas till den primära rollen. 


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


Återställ redundansväxlingen tillbaka till den primära servern:

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

I den här delen av självstudien används följande PowerShell-cmdletar:

| Kommando | Anteckningar |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hämtar eller listar hanterade instanser av redundans.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Kör en redundansväxling av en redundansväxling av en hanterad instans. | 

---



## <a name="clean-up-resources"></a>Rensa resurser
Rensa resurser genom att först ta bort den hanterade instansen, sedan det virtuella klustret, sedan eventuella återstående resurser och slutligen resurs gruppen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navigera till din resurs grupp i [Azure Portal](https://portal.azure.com). 
1. Välj de hanterade instanserna och välj sedan **ta bort**. Skriv `yes` i text rutan för att bekräfta att du vill ta bort resursen och välj sedan **ta bort**. Den här processen kan ta lite tid att slutföra i bakgrunden och tills den är klar kommer du inte att kunna ta bort det *virtuella klustret* eller andra beroende resurser. Övervaka borttagningen på fliken aktivitet för att bekräfta att den hanterade instansen har tagits bort. 
1. När den hanterade instansen har tagits bort tar du bort det *virtuella klustret* genom att markera det i resurs gruppen och sedan välja **ta bort**. Skriv `yes` i text rutan för att bekräfta att du vill ta bort resursen och välj sedan **ta bort**. 
1. Ta bort eventuella återstående resurser. Skriv `yes` i text rutan för att bekräfta att du vill ta bort resursen och välj sedan **ta bort**. 
1. Ta bort resurs gruppen genom att välja **ta bort resurs**grupp, Skriv namnet på resurs gruppen `myResourceGroup`och välj sedan **ta bort**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du måste ta bort resurs gruppen två gånger. Om du tar bort resurs gruppen första gången tas den hanterade instansen och virtuella kluster bort, men det går inte att köra fel meddelandet `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`. Kör kommandot Remove-AzResourceGroup en andra gång för att ta bort eventuella kvarvarande resurser samt resurs gruppen.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

I den här delen av självstudien används följande PowerShell-cmdlet:

| Kommando | Anteckningar |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resurs grupp. |

---

## <a name="full-script"></a>Fullständigt skript

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en Azure-resursgrupp.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Lägger till en under näts konfiguration i ett virtuellt nätverk. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hämtar ett virtuellt nätverk i en resursgrupp. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hämtar ett undernät i ett virtuellt nätverk. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Skapar en nätverkssäkerhetsgrupp. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Skapar en routningstabell. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Uppdaterar en under näts konfiguration för ett virtuellt nätverk.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Uppdaterar ett virtuellt nätverk.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hämtar en nätverks säkerhets grupp. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Lägger till en nätverks säkerhets regel konfiguration i en nätverks säkerhets grupp. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Uppdaterar en nätverks säkerhets grupp.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Lägger till en väg i en routningstabell. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Uppdaterar en routningstabell.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Skapar en Azure SQL Database Hanterad instans.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Returnerar information om Azure SQL-hanterad databas instans. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Skapar en IP-konfiguration för en Virtual Network Gateway |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Skapar en Virtual Network Gateway |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Skapar en anslutning mellan de två virtuella Nätverksgatewayen.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Skapar en ny Azure SQL Database Hanterad instans redundans grupp.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hämtar eller listar hanterade instanser av redundans.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Kör en redundansväxling av en redundansväxling av en hanterad instans. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resurs grupp. | 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Det finns inga tillgängliga skript för Azure Portal.

---

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du en redundans grupp mellan två hanterade instanser. Du har lärt dig att:

> [!div class="checklist"]
> - Skapa en primär hanterad instans
> - Skapa en sekundär hanterad instans som en del av en [grupp för växling vid fel](sql-database-auto-failover-group.md). 
> - Redundanstest

Gå vidare till nästa snabb start när du ansluter till din hanterade instans och hur du återställer en databas till din hanterade instans: 

> [!div class="nextstepaction"]
> [Anslut till din hanterade instans](sql-database-managed-instance-configure-vm.md)
> [återställa en databas till en hanterad instans](sql-database-managed-instance-get-started-restore.md)


