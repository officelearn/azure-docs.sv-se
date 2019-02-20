---
title: Partner konfigurationer för VPN-enhet för att ansluta till Azure VPN-gatewayer | Microsoft Docs
description: Den här artikeln innehåller en översikt över VPN-enhetskonfigurationer för partner för att ansluta till Azure VPN-gatewayer.
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
ms.openlocfilehash: 07f2e46198118530de5a2163480eb44575891c4b
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415284"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Översikt över VPN-enhetskonfigurationer för partner
Den här artikeln innehåller en översikt över hur du konfigurerar den lokala VPN-enheter för att ansluta till Azure VPN-gatewayer. Ett exempel på Azure-nätverk och VPN gateway-konfigurationen används för att visa dig hur du ansluter till olika lokala VPN-enhetens konfigurationer med hjälp av samma parametrar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="device-requirements"></a>Krav för enheter
Azure VPN-gateway använder standard IPsec/IKE-protokollet krypteringssviter för plats-till-plats (S2S) VPN-tunnlar. En lista med IPsec/IKE-parametrar och kryptografiska algoritmer för Azure VPN-gatewayer finns i [om VPN-enheter](vpn-gateway-about-vpn-devices.md). Du kan också ange de exakta algoritmer och nyckellängder för ett särskilt projekt enligt beskrivningen i [om kryptografikrav](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>En VPN-tunnel
Den första konfigurationen i det här exemplet består av en S2S VPN-tunnel mellan en Azure VPN-gateway och en lokal VPN-enhet. Du kan också konfigurera den [Border Gateway Protocol (BGP) via VPN-tunnel](#bgp).

![Diagram över en S2S VPN-tunnel](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Stegvisa anvisningar om hur du konfigurerar en VPN-tunnel finns i [konfigurera en plats-till-plats-anslutning](vpn-gateway-howto-site-to-site-resource-manager-portal.md). I följande avsnitt ange anslutningsparametrar för exempel-konfigurationen och tillhandahålla en PowerShell-skript för att hjälpa dig att komma igång.

### <a name="connection-parameters"></a>Anslutningsparametrar
Det här avsnittet visas parametrarna i exemplen som beskrivs i föregående avsnitt.

| **Parametern**                | **Värde**                    |
| ---                          | ---                          |
| Vnet-adressprefix        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN gateway-IP         | Azure VPN Gateway IP         |
| Lokala adressprefix | 10.51.0.0/16<br>10.52.0.0/16 |
| Den lokala VPN-enhetens IP    | Den lokala VPN-enhetens IP    |
| * Virtual network BGP ASN                | 65010                        |
| * Azure BGP peer IP           | 10.12.255.30                 |
| * On-premises BGP ASN         | 65050                        |
| * On-premises BGP peer IP     | 10.52.255.254                |

\* Valfri parameter för BGP endast.

### <a name="sample-powershell-script"></a>Exempel på PowerShell-skript
Det här avsnittet innehåller ett exempelskript för att komma igång. Detaljerade anvisningar finns i [skapa en S2S VPN-anslutning med hjälp av PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subscription_Name"
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

Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a>(Valfritt) Använda anpassade IPsec/IKE-princip med UsePolicyBasedTrafficSelectors
Om din VPN-enheter inte stöder alla-till-alla trafikväljare, till exempel routningsbaserad eller VTI-baserade konfigurationer, skapa en anpassad IPsec/IKE-princip med de [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) alternativet.

> [!IMPORTANT]
> Du måste skapa en IPsec/IKE-principen för att aktivera den **UsePolicyBasedTrafficSelectors** alternativet på anslutningen.


Exempelskriptet skapar en IPsec/IKE-princip med följande algoritmer och parametrar:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA-livstid 7200 sekunder och 20,480,000 KB (20 GB)

Skriptet använder IPsec/IKE-principer och gör den **UsePolicyBasedTrafficSelectors** alternativet på anslutningen.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>(Valfritt) Använda BGP för S2S VPN-anslutning
När du skapar S2S VPN-anslutningen kan du också använda [BGP för VPN-gateway](vpn-gateway-bgp-resource-manager-ps.md). Den här metoden har två skillnader:

* Den lokala IP-adressprefixen kan vara en enda värd-adress. Den lokala BGP-peer IP-adress har angetts på följande sätt:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* När du har skapat anslutningen, måste du ange den **- EnableBGP** alternativet som $True:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Nästa steg
Stegvisa anvisningar om hur du konfigurerar aktiv-aktiv VPN-gatewayer finns i [konfigurera aktiv-aktiv VPN-gatewayer för flera platser och VNet-till-VNet-anslutningar](vpn-gateway-activeactive-rm-powershell.md).

