---
title: 'Anslut Azure VPN-gatewayer till flera lokala principbaserade VPN-enheter: Azure Resource Manager: PowerShell | Microsoft Docs'
description: Konfigurera en Azure-vägbaserade VPN-gateway till flera principbaserade VPN-enheter med Azure Resource Manager och PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: yushwang
ms.openlocfilehash: 4996fa23e28b4ba840cc8c97e167484be08a0573
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509294"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Ansluta Azure VPN-gatewayer till flera lokala principbaserade VPN-enheter med hjälp av PowerShell

Den här artikeln hjälper dig att konfigurera en Azure-vägbaserade VPN-gateway att ansluta till flera lokala principbaserade VPN-enheter att använda anpassade IPsec/IKE-principer på S2S VPN-anslutningar.

## <a name="about"></a>Om principbaserad och routningsbaserad VPN-gatewayer

Datorprincip - *jämfört med* routningsbaserad VPN-enheter skiljer sig åt i hur trafikväljare IPsec anges för en anslutning:

* **Principbaserad** VPN-enheter använder kombinationer av prefix från båda nätverken för att definiera hur trafik är krypterad/dekrypteras via IPsec-tunnlar. Den bygger vanligtvis på för brandväggsenheter som utför filtrering av nätverkspaket. IPsec-tunnel, kryptering och dekryptering läggs till i paketet filtrering och motor för händelsebearbetning.
* **Ruttbaserad** VPN-enheter använder alla-till-alla (med jokertecken) trafikväljare och låta routning/vidarebefordran tabeller dirigera trafik till olika IPsec-tunnlar. Den bygger vanligtvis på routern plattformar där varje IPsec-tunneln modelleras som ett nätverksgränssnitt eller VTI (virtuella tunnelgränssnitt).

Följande diagram markerar du de två modellerna:

### <a name="policy-based-vpn-example"></a>Principbaserad VPN-exempel
![principbaserad](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Ruttbaserad VPN-exempel
![routningsbaserad](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Azure-stöd för principbaserad VPN
Azure stöder för närvarande båda lägena VPN-gatewayer: routningsbaserade VPN-gatewayer och principbaserad VPN-gatewayer. De bygger på olika interna plattformar, vilket resulterar i olika specifikationer:

|                          | **Principbaserad VPN-Gateway** | **Routningsbaserad VPN-Gateway**               |
| ---                      | ---                         | ---                                      |
| **Azure Gateway SKU**    | Basic                       | Basic, Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3 |
| **IKE-version**          | IKEv1                       | IKEv2                                    |
| **Max. S2S-anslutningar** | **1**                       | Basic/Standard: 10<br> HighPerformance: 30 |
|                          |                             |                                          |

Med den anpassade IPsec/IKE-principen, kan du nu konfigurera Azure-vägbaserade VPN-gateways för att använda prefix-baserade trafikväljare med alternativet ”**PolicyBasedTrafficSelectors**”, för att ansluta till den lokala principbaserad VPN-enheter. Den här funktionen kan du ansluta från Azure-nätverk och VPN-gateway till flera lokala principbaserade VPN/brandväggsenheter, ta bort gränsen enkel anslutning från de aktuella Azure principbaserade VPN-gatewayerna.

> [!IMPORTANT]
> 1. Om du vill aktivera den här anslutningen, dina lokala principbaserad VPN-enheter måste ha stöd för **IKEv2** att ansluta till Azure-vägbaserade VPN-gateways. Kontrollera dina specifikationer för VPN-enheten.
> 2. De lokala nätverk som ansluter via principbaserad VPN-enheter med den här mekanismen kan bara ansluta till Azure-nätverk; **de kan inte överföra till andra lokala nätverk eller virtuella nätverk via samma Azure VPN-gateway**.
> 3. Konfigurationsalternativet är en del av principen för anpassade IPsec/IKE. Om du aktiverar alternativet principbaserad trafik väljare, måste du ange hela policyn (IPsec/IKE-kryptering och integritet algoritmer, nyckellängder och SA-livstider).

Följande diagram visar varför transit Routning via Azure VPN-gateway inte fungerar med alternativet principbaserad:

![principbaserad överföring](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

I diagrammet visas Azure VPN-gatewayen har trafikväljare från det virtuella nätverket till alla lokala nätverksprefix, men inte korsanslutning prefix. Till exempel en lokal plats 2, plats 3 och plats 4 kan varje kommunicera till VNet1 respektive, men det går inte att ansluta via Azure VPN-gatewayen till varandra. Diagrammet visar cross-connect-trafikväljare som inte är tillgängliga i Azure VPN-gatewayen under den här konfigurationen.

## <a name="configurepolicybased"></a>Konfigurera trafikväljare med principbaserad på en anslutning

Anvisningarna i den här artikeln följer samma exempel som beskrivs i [konfigurera IPsec/IKE-principer för S2S eller VNet-till-VNet-anslutningar](vpn-gateway-ipsecikepolicy-rm-powershell.md) att upprätta en S2S VPN-anslutning. Detta illustreras i följande diagram:

![s2s-policy](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Arbetsflöde för att aktivera den här anslutningen:
1. Skapa virtuellt nätverk, VPN-gateway och lokal nätverksgateway för anslutningen mellan flera platser
2. Skapa en IPsec/IKE-princip
3. Tillämpa principen när du skapar en S2S- eller VNet-till-VNet-anslutning och **aktivera principbaserade trafikväljare** på anslutningen
4. Om anslutningen har redan skapats, kan du tillämpa eller uppdatera principen till en befintlig anslutning

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>Aktivera principbaserade trafikväljare på en anslutning

Kontrollera att du har slutfört [del 3 av artikeln konfigurera IPsec/IKE policy](vpn-gateway-ipsecikepolicy-rm-powershell.md) för det här avsnittet. I följande exempel används samma parametrar och steg:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Steg 1 – Skapa virtuella nätverk, VPN-gateway och lokal nätverksgateway

#### <a name="1-connect-to-your-subscription-and-declare-your-variables"></a>1. Ansluta till din prenumeration och deklarera dina variabler

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

Deklarera dina variabler. I den här övningen använder vi följande variabler:

```azurepowershell-interactive
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
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
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Skapa virtuellt nätverk, VPN-gateway och lokal nätverksgateway

Skapa en resursgrupp.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

Använd följande exempel för att skapa det virtuella nätverket TestVNet1 med tre undernät och en VPN-gateway. Om du vill ersätta värden är det viktigt att du alltid namnger gateway-undernätet specifikt ”GatewaySubnet”. Om du ger det något annat namn går det inte att skapa gatewayen.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Steg 2 – Skapa en S2S VPN-anslutning med en IPsec/IKE-princip

#### <a name="1-create-an-ipsecike-policy"></a>1. Skapa en IPsec/IKE-princip

> [!IMPORTANT]
> Du behöver skapa en IPsec/IKE-princip för att aktivera alternativet ”UsePolicyBasedTrafficSelectors” på anslutningen.

I följande exempel skapar en IPsec/IKE-princip med dessa algoritmer och parametrar:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS24, SA-livstid 3600 sekunder och 2 048 KB

```azurepowershell-interactive
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup PFS24 -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Skapa S2S VPN-anslutningen med principbaserade trafikväljare och IPsec/IKE-princip
Skapa en S2S VPN-anslutning och använda IPsec/IKE-principer som skapats i föregående steg. Vara medveten om ytterligare parametern ”-UsePolicyBasedTrafficSelectors $True” som gör det möjligt för principbaserade trafikväljare för anslutningen.

```azurepowershell-interactive
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

När du har slutfört stegen, S2S VPN-anslutningen använder IPsec/IKE-principer som definierats, och aktivera principbaserade trafikväljare för anslutningen. Du kan upprepa samma steg för att lägga till fler anslutningar till ytterligare lokala principbaserad VPN-enheter från samma Azure VPN-gateway.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Uppdatera principbaserade trafikväljare för en anslutning
Det sista avsnittet visar hur du uppdaterar alternativet principbaserad trafik väljare för en befintlig S2S VPN-anslutning.

### <a name="1-get-the-connection"></a>1. Hämta
Hämta anslutningsresurs.

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Kontrollera alternativet principbaserad trafik väljare
Följande rad visar om används de principbaserade trafikväljare för anslutningen:

```azurepowershell-interactive
$connection6.UsePolicyBasedTrafficSelectors
```

Om raden returnerar ”**SANT**”, sedan principbaserade trafikväljare konfigureras på anslutningen; annars returneras ”**FALSKT**”.

### <a name="3-enabledisable-the-policy-based-traffic-selectors-on-a-connection"></a>3. Aktivera/inaktivera principbaserade trafikväljare på en anslutning
Du kan aktivera eller inaktivera alternativet när du har hämtat connection-resursen.

#### <a name="to-enable-usepolicybasedtrafficselectors"></a>Aktivera UsePolicyBasedTrafficSelectors
I följande exempel aktiverar principbaserad trafik väljare alternativ men lämnar IPsec/IKE-principen har inte ändrats:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

#### <a name="to-disable-usepolicybasedtrafficselectors"></a>Inaktivera UsePolicyBasedTrafficSelectors
I följande exempel inaktiverar alternativet principbaserad trafik väljare, men lämnar IPsec/IKE-principen har inte ändrats:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för anvisningar.

Granska även [konfigurera IPsec/IKE-principer för S2S VPN- eller VNet-till-VNet-anslutningar](vpn-gateway-ipsecikepolicy-rm-powershell.md) för mer information om anpassade IPsec/IKE-principer.
