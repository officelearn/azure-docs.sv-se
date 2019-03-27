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
ms.openlocfilehash: 071db2d9aeda2373c85ae62c47bbef175dcb7678
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483411"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Ansluta ett virtuellt nätverk till stora HANA-instanser

När du har skapat ett Azure-nätverk, kan du ansluta nätverket till SAP HANA på Azure stora instanser. Skapa en Azure ExpressRoute-gateway i det virtuella nätverket. Den här gatewayen kan du länka virtuella nätverk till ExpressRoute-krets som ansluter till kundklient på stämpel för stor instans.

> [!NOTE] 
> Det här steget kan ta upp till 30 minuter att slutföra. Ny gateway skapas i den avsedda Azure-prenumerationen och sedan anslutit till den angivna Azure-nätverken.

Om det finns redan en gateway, kontrollerar du om det är en ExpressRoute-gateway eller inte. Om inte, ta bort gatewayen och skapa den igen som en ExpressRoute-gateway. Om en ExpressRoute-gateway är redan etablerad, se följande avsnitt i den här artikeln ”länka virtuella nätverk”. 

- Använd antingen den [Azure-portalen](https://portal.azure.com/) eller PowerShell för att skapa en ExpressRoute VPN-gateway är ansluten till ditt virtuella nätverk.
  - Om du använder Azure-portalen, lägga till en ny **virtuell nätverksgateway**, och välj sedan **ExpressRoute** som gateway-typen.
  - Om du använder PowerShell först ladda ned och använda senast [Azure PowerShell SDK](https://azure.microsoft.com/downloads/). Följande kommandon för skapar en ExpressRoute-gateway. Texten som föregås av ett _$_ är användardefinierade variabler som ska uppdateras med din specifika information.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

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

I det här exemplet användes HighPerformance-gateway-SKU. Alternativen är HighPerformance eller UltraPerformance som de enda gateway-SKU: er som stöds för SAP HANA på Azure (stora instanser).

> [!IMPORTANT]
> Du måste använda SKU för UltraPerformance-Gateway för HANA stora instanser av klassen typ II SKU.

## <a name="link-virtual-networks"></a>Länka virtuella nätverk

Azure-nätverket har nu en ExpressRoute-gateway. Använd auktoriseringsinformation som tillhandahålls av Microsoft för att ansluta ExpressRoute-gatewayen till SAP HANA på Azure (stora instanser) ExpressRoute-krets. Du kan ansluta med hjälp av Azure portal eller PowerShell. Portalen rekommenderas, men om du vill använda PowerShell instruktionerna är följande. 

Kör följande kommandon för varje virtuell nätverksgateway med hjälp av en annan AuthGUID för varje anslutning. De två första posterna visas i följande skript kommer från information som tillhandahålls av Microsoft. Dessutom är AuthGUID specifika för varje virtuellt nätverk och dess gateway. Om du vill lägga till en annan Azure-nätverk måste du hämta en annan AuthID för din ExpressRoute-krets som ansluter stora HANA-instanser till Azure. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
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

För att ansluta gatewayen till mer än en ExpressRoute-krets som är associerad med din prenumeration, kan du behöva köra det här steget mer än en gång. Exempelvis kan kommer du förmodligen att ansluta samma vnet-gatewayen till ExpressRoute-krets som ansluter det virtuella nätverket till ditt lokala nätverk.

## <a name="next-steps"></a>Nästa steg

- [Ytterligare krav för HLI](hana-additional-network-requirements.md)