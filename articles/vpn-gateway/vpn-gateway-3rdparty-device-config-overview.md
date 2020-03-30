---
title: Partner-VPN-enhetskonfigurationer för anslutning till Azure VPN-gateways
description: Den här artikeln innehåller en översikt över partner-VPN-enhetskonfigurationer för anslutning till Azure VPN-gateways.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: b914afaa6725920078da309981bcda5bb765e155
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279408"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Översikt över partner-VPN-enhetskonfigurationer
Den här artikeln innehåller en översikt över hur du konfigurerar lokala VPN-enheter för anslutning till Azure VPN-gateways. Ett exempel på azure-konfiguration för virtuellt nätverk och VPN-gateway används för att visa hur du ansluter till olika lokala VPN-enhetskonfigurationer med samma parametrar.



## <a name="device-requirements"></a>Krav på enheten
Azure VPN-gateways använder standard-IPsec/IKE-protokollsviter för S2S-tunnlar (Site-to-Site). En lista över IPsec/IKE-parametrar och kryptografiska algoritmer för Azure VPN-gateways finns i [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Du kan också ange exakta algoritmer och nyckelstyrkor för en viss anslutning enligt beskrivningen i [Om kryptografiska krav](vpn-gateway-about-compliance-crypto.md).

## <a name="single-vpn-tunnel"></a><a name ="singletunnel"></a>Enkel VPN-tunnel
Den första konfigurationen i exemplet består av en enda S2S VPN-tunnel mellan en Azure VPN-gateway och en lokal VPN-enhet. Du kan också konfigurera [BGP (Border Gateway Protocol) över VPN-tunneln](#bgp).

![Diagram över en enda S2S VPN-tunnel](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Steg-för-steg-instruktioner för att konfigurera en enda VPN-tunnel finns i [Konfigurera en anslutning från plats till plats](vpn-gateway-howto-site-to-site-resource-manager-portal.md). I följande avsnitt anges anslutningsparametrarna för exempelkonfigurationen och ett PowerShell-skript som hjälper dig att komma igång.

### <a name="connection-parameters"></a>Anslutningsparametrar
I det här avsnittet visas parametrarna för de exempel som beskrivs i föregående avsnitt.

| **Parametern**                | **Värde**                    |
| ---                          | ---                          |
| Prefix för virtuell nätverksadress        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN-gateway IP         | Azure VPN Gateway IP         |
| Lokala adressprefix | 10.51.0.0/16<br>10.52.0.0/16 |
| Lokal IP-enhet för VPN-enhet    | Lokal IP-enhet för VPN-enhet    |
| * Virtuellt nätverk BGP ASN                | 65010                        |
| * Azure BGP-peer-IP           | 10.12.255.30                 |
| * Lokala BGP ASN         | 65050                        |
| * Lokal BGP-peer IP     | 10.52.255.254                |

\*Valfri parameter endast för BGP.

### <a name="sample-powershell-script"></a>Exempel på PowerShell-skript
Det här avsnittet innehåller ett exempelskript som hjälper dig att komma igång. Detaljerade instruktioner finns i [Skapa en S2S VPN-anslutning med PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

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

### <a name="optional-use-custom-ipsecike-policy-with-usepolicybasedtrafficselectors"></a><a name ="policybased"></a>(Valfritt) Använda anpassad IPsec/IKE-princip med UsePolicyBasedTrafficSelectors
Om dina VPN-enheter inte stöder trafikväljare, till exempel ruttbaserade eller VTI-baserade konfigurationer, skapar du en anpassad IPsec/IKE-princip med alternativet [UsePolicyBasedTrafficSelectors.](vpn-gateway-connect-multiple-policybased-rm-ps.md)

> [!IMPORTANT]
> Du måste skapa en IPsec/IKE-princip för att aktivera alternativet **UsePolicyBasedTrafficSelectors** på anslutningen.


Exempelskriptet skapar en IPsec/IKE-princip med följande algoritmer och parametrar:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA-livslängd 7 200 sekunder och 20 480 000 KB (20 GB)

Skriptet tillämpar IPsec/IKE-principen och aktiverar alternativet **UsePolicyBasedTrafficSelectors** på anslutningen.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name="optional-use-bgp-on-s2s-vpn-connection"></a><a name ="bgp"></a>(Valfritt) Använda BGP på S2S VPN-anslutning
När du skapar S2S VPN-anslutning kan du eventuellt använda [BGP för VPN-gatewayen](vpn-gateway-bgp-resource-manager-ps.md). Detta tillvägagångssätt har två skillnader:

* De lokala adressprefixen kan vara en enda värdadress. Den lokala BGP-peer-IP-adressen anges på följande sätt:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* När du skapar anslutningen måste du ange alternativet **-EnableBGP** på $True:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Nästa steg
Steg-för-steg-instruktioner för att konfigurera aktiva VPN-gateways finns i [Konfigurera aktiva aktiva VPN-gateways för anslutning mellan lokala och virtuella anslutningar .](vpn-gateway-activeactive-rm-powershell.md)

