---
title: Anslutningskonfiguration från virtuellt nätverk till SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Anslutningskonfiguration från virtuellt nätverk för att använda SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5efdda485e4e1f5013948c6636b267f0d388f4d5
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164735"
---
# <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Ansluta ett virtuellt nätverk till HANA stora instanser ExpressRoute

Som du har definierat alla IP-adressintervall och nu fick tillbaka data från Microsoft, kan du ansluta det virtuella nätverket som du skapade innan till stora HANA-instanser. När Azure VNet har skapats kan måste en ExpressRoute-gateway skapas på det virtuella nätverket att länka det virtuella nätverket till ExpressRoute-krets som ansluter till kundklient på stämpel för stor instans.

> [!NOTE] 
> Det här steget kan ta upp till 30 minuter att slutföra, eftersom den nya gatewayen skapas i den avsedda Azure-prenumerationen och sedan är anslutna till det angivna virtuella Azure-nätverket.

Om det finns redan en gateway, kontrollerar du om det är en ExpressRoute-gateway eller inte. Om inte gatewayen måste tas bort och återskapas som en ExpressRoute-gateway. Om en ExpressRoute-gateway är redan etablerad, eftersom den virtuella Azure-nätverket är redan ansluten till ExpressRoute-kretsen för lokal anslutning, kan du gå vidare till avsnittet länka virtuella nätverk.

- Använd antingen den (ny) [Azure-portalen](https://portal.azure.com/), eller PowerShell för att skapa en ExpressRoute VPN-gateway som är ansluten till ditt VNet.
  - Om du använder Azure-portalen, lägga till en ny **virtuell nätverksgateway** och välj sedan **ExpressRoute** som gateway-typen.
  - Om du har valt PowerShell i stället först ladda ned och använda senast [Azure PowerShell SDK](https://azure.microsoft.com/downloads/) att säkerställa en optimal upplevelse. Följande kommandon för skapar en ExpressRoute-gateway. Texten som föregås av ett _$_ är användardefinierade variabler som måste uppdateras med din specifika information.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

I det här exemplet användes HighPerformance-gateway-SKU. Alternativen är HighPerformance eller UltraPerformance som en enda gateway SKU: er som stöds för SAP HANA på Azure (stora instanser).

> [!IMPORTANT]
> Användningen av UltraPerformance Gateway-SKU är obligatoriskt för HANA stora instanser av typ II classs SKU.

**Länka virtuella nätverk**

Nu när den virtuella Azure-nätverket har en ExpressRoute-gateway kan använda du auktoriseringsinformation som tillhandahålls av Microsoft för att ansluta ExpressRoute-gatewayen till SAP HANA på Azure (stora instanser) ExpressRoute-krets som skapats för den här anslutningen. Det här steget kan utföras med hjälp av Azure portal eller PowerShell. Portalen rekommenderas, men PowerShell-instruktioner finns på följande sätt. 

- Du kan köra följande kommandon för varje VNet-gateway med hjälp av en annan AuthGUID för varje anslutning. De två första posterna visas i följande skript kommer från information som tillhandahålls av Microsoft. Dessutom är AuthGUID specifika för varje virtuellt nätverk och dess gateway. Innebär att, om du vill lägga till ett annat Azure VNet, måste du hämta en annan AuthID för din ExpressRoute-krets som ansluter stora HANA-instanser till Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Om du vill ansluta gatewayen till flera ExpressRoute-kretsar som är associerade med din prenumeration kan du behöva köra det här steget mer än en gång. Exempelvis kan kommer du förmodligen att ansluta samma VNet-gatewayen till ExpressRoute-krets som ansluter det virtuella nätverket till ditt lokala nätverk.

**Nästa steg**

- Se [ytterligare krav för HLI](hana-additional-network-requirements.md).