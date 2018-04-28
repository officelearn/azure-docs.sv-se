---
title: Samarbeta konfigurationer för VPN-enhet för att ansluta till Azure VPN-gatewayer | Microsoft Docs
description: Den här artikeln innehåller en översikt över partner VPN enhetskonfigurationer för att ansluta till Azure VPN-gatewayer.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: ''
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: dd9ca3937d688170798c42fce45dbcd7711773d1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Översikt över konfigurationen av partner VPN-enheter
Den här artikeln innehåller en översikt över hur du konfigurerar lokala VPN-enheter för att ansluta till Azure VPN-gatewayer. Ett exempel på virtuella Azure-nätverket och installationsprogram för VPN-gateway används för att visa hur du ansluter till olika lokala VPN-enhetskonfigurationer med samma parametrar.

## <a name="device-requirements"></a>Enhetskrav på
Azure VPN-gatewayer använder standard IPsec/IKE-protokoll-paket för plats-till-plats (S2S) VPN-tunnlar. En lista över IPsec/IKE parametrar och kryptografiska algoritmer för Azure VPN-gatewayer finns [om VPN-enheter](vpn-gateway-about-vpn-devices.md). Du kan också ange exakt algoritmer och viktiga fördelar för en viss anslutning enligt beskrivningen i [om krav för kryptografiska](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>En VPN-tunnel
Den första konfigurationen i samplet som består av en S2S VPN-tunnel mellan en Azure VPN-gateway och en lokal VPN-enhet. Du kan också konfigurera den [protokollet BGP (Border Gateway) över VPN-tunnel](#bgp).

![Diagram över en S2S VPN-tunnel](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Stegvisa anvisningar om hur du ställer in en VPN-tunnel finns [konfigurerar du en plats-till-plats-anslutning](vpn-gateway-howto-site-to-site-resource-manager-portal.md). I följande avsnitt ange anslutningsparametrar för exempelkonfiguration och ange ett PowerShell-skript som hjälper dig att komma igång.

### <a name="connection-parameters"></a>Anslutningsparametrar
Det här avsnittet visas parametrarna som exempel som beskrivs i föregående avsnitt.

| **Parametern**                | **Värde**                    |
| ---                          | ---                          |
| Adressprefix för virtuellt nätverk        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN gateway-IP         | Azure VPN-Gateway IP         |
| Lokala adressprefix | 10.51.0.0/16<br>10.52.0.0/16 |
| Lokala VPN-enhetens IP    | Lokala VPN-enhetens IP    |
| * Virtuellt nätverk BGP ASN                | 65010                        |
| * Azure BGP-peer-IP           | 10.12.255.30                 |
| * Lokala BGP ASN         | 65050                        |
| * Lokala BGP-peer-IP     | 10.52.255.254                |

\* Valfri parameter för BGP endast.

### <a name="sample-powershell-script"></a>PowerShell-exempelskript
Det här avsnittet innehåller ett exempelskript för att komma igång. Detaljerade instruktioner finns [skapa S2S VPN-anslutningar med hjälp av PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subcription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a>(Valfritt) Använda anpassade IPsec/IKE-princip med UsePolicyBasedTrafficSelectors
Om VPN-enheter inte stöder alla-till-alla trafik väljare, till exempel väg eller VTI konfigurationer, skapa en anpassad IPsec/IKE-princip med de [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) alternativet.

> [!IMPORTANT]
> Du måste skapa en IPsec/IKE-princip för att aktivera den **UsePolicyBasedTrafficSelectors** alternativet på anslutningen.


Exempelskript som skapar en IPsec/IKE-princip med de följande algoritmer och parametrar:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA livstid 7,200 sekunder och 20,480,000 KB (20 GB)

Skriptet använder IPsec/IKE-principen och gör den **UsePolicyBasedTrafficSelectors** alternativet på anslutningen.

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>(Valfritt) Använda BGP för S2S VPN-anslutning
När du skapar S2S VPN-anslutningen kan du alternativt använda [BGP för VPN-gateway](vpn-gateway-bgp-resource-manager-ps.md). Den här metoden har två skillnader:

* Adressprefix lokalt kan vara en enda värd-adress. Lokala BGP peer-IP-adress har angetts på följande sätt:

    ```powershell
    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* När du skapar anslutningen måste du ange den **- Enablegpg** alternativet till $True:

    ```powershell
    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Nästa steg
Stegvisa instruktioner för att ställa in VPN-gatewayer för aktiv-aktiv finns [konfigurera aktiv-aktiv VPN-gatewayer för anslutningar mellan platser och VNet-till-VNet-anslutningar](vpn-gateway-activeactive-rm-powershell.md).

