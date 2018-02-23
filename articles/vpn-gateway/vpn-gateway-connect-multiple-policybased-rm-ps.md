---
title: 'Anslut Azure VPN-gatewayer till flera lokala principbaserad VPN-enheter: Azure Resource Manager: PowerShell | Microsoft Docs'
description: "Konfigurera en Azure ruttbaserad VPN-gateway för flera principbaserad VPN-enheter med Azure Resource Manager och PowerShell."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: 90c855e768f403098e535391afb55e3c78044b0a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Anslut Azure VPN-gatewayer till flera lokala principbaserad VPN-enheter med hjälp av PowerShell

Den här artikeln hjälper dig att konfigurera en Azure ruttbaserad VPN-gateway för att ansluta till flera lokala principbaserad VPN-enheter genom att använda anpassade IPsec/IKE-principer för S2S VPN-anslutningar.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a>Om principbaserade och ruttbaserade VPN-gatewayer

Princip - *kontra* ruttbaserad VPN-enheter skiljer sig åt i hur IPSec-trafik väljare anges för en anslutning:

* **Principbaserad** VPN-enheter använder kombinationer av adressprefix från båda nätverken för att definiera hur trafiken är krypterad/dekrypteras via IPsec-tunnlar. Den bygger vanligtvis på brandväggsenheter som utför filtrering av nätverkspaket. IPSec-tunneln kryptering och dekryptering läggs till paketet filtrering och bearbetning av motorn.
* **Ruttbaserad** VPN-enheter med hjälp av alla-till-alla (jokertecken) trafik väljare och låta routning/vidarebefordran tabeller trafiken till olika IPsec-tunnlar. Den bygger vanligtvis på routern plattformar, där varje IPsec-tunneln modelleras som ett nätverksgränssnitt eller VTI (virtuell tunnelgränssnitt).

Följande diagram markerar du de två modellerna:

### <a name="policy-based-vpn-example"></a>Principbaserad VPN-exempel
![principbaserad](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Ruttbaserad VPN-exempel
![ruttbaserad](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Azure-stöd för principbaserad VPN
Azure stöder för närvarande, båda lägena för VPN-gatewayer: ruttbaserad VPN-gatewayer och policy-baserad VPN-gatewayer. De bygger på olika interna plattformar, vilket resulterar i olika specifikationer:

|                          | **PolicyBased VPN-Gateway** | **RouteBased VPN-Gateway**               |
| ---                      | ---                         | ---                                      |
| **Azure Gateway SKU**    | Basic                       | Basic, Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3 |
| **IKE-version**          | IKEv1                       | IKEv2                                    |
| **Max. S2S-anslutningar** | **1**                       | Basic/Standard: 10<br> HighPerformance: 30 |
|                          |                             |                                          |

Med den anpassade IPsec/IKE-principen, kan du nu konfigurera Azure ruttbaserad VPN-gatewayer för att använda prefix-baserad trafik väljare med alternativet ”**PolicyBasedTrafficSelectors**”, för att ansluta till lokal policy-baserad VPN-enheter. Den här funktionen kan du ansluta från Azure-nätverk och VPN-gateway till flera lokala principbaserad VPN-brandväggen enheter, ta bort den enda anslutningsgränsen från de aktuella Azure principbaserade VPN-gatewayerna.

> [!IMPORTANT]
> 1. Om du vill aktivera den här anslutningen dina lokala principbaserad VPN-enheter måste ha stöd för **IKEv2** att ansluta till Azure ruttbaserad VPN-gateway. Kontrollera din VPN-enhetsspecifikationer.
> 2. De lokala nätverk som ansluter via principbaserad VPN-enheter med den här funktionen kan bara ansluta till virtuella Azure-nätverket; **de inte transit till andra lokala nätverk eller virtuella nätverk via samma Azure VPN-gatewayen**.
> 3. Konfigurationsalternativet är en del av den anpassade principen för IPsec/IKE-anslutning. Om du aktiverar alternativet principbaserad trafik selector måste du ange hela policyn (IPsec/IKE algoritmer för kryptering och integritet, viktiga fördelar och säkerhetsassociationer).

Följande diagram visar varför transitroutning via Azure VPN-gateway inte fungerar med alternativet principbaserad:

![principbaserad överföring](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

I diagrammet visas har Azure VPN-gatewayen trafik väljare från det virtuella nätverket för varje lokal nätverksprefixet, men inte anslutningen mellan prefix. Till exempel en lokal plats 2, 3, och plats 4 kan varje kommunicera till VNet1 respektive, men det går inte att ansluta via Azure VPN-gatewayen till varandra. Diagrammet visar cross-connect trafik väljare som inte är tillgängliga i Azure VPN-gatewayen under den här konfigurationen.

## <a name="configure-policy-based-traffic-selectors-on-a-connection"></a>Konfigurera principbaserad trafik väljare på en anslutning

Det här exemplet följer du anvisningarna i den här artikeln som beskrivs i [konfigurera IPsec/IKE-principen för S2S eller VNet-till-VNet-anslutningar](vpn-gateway-ipsecikepolicy-rm-powershell.md) att upprätta en S2S VPN-anslutning. Detta visas i följande diagram:

![s2s-policy](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Arbetsflödet för att aktivera den här anslutningen:
1. Skapa virtuella nätverk, VPN-gateway och lokal nätverksgateway för anslutningen mellan platser
2. Skapa en IPsec/IKE-princip
3. Tillämpa principen när du skapar en S2S- eller VNet-till-VNet-anslutning och **aktivera principbaserad trafik väljare** för anslutningen
4. Om anslutningen redan har skapats kan du tillämpa eller uppdatera principen till en befintlig anslutning

## <a name="enable-policy-based-traffic-selectors-on-a-connection"></a>Aktivera principbaserad trafik väljare på en anslutning

Kontrollera att du har slutfört [del 3 Konfigurera IPsec/IKE princip artikel](vpn-gateway-ipsecikepolicy-rm-powershell.md) för det här avsnittet. I följande exempel används samma parametrar och steg:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Steg 1 – Skapa virtuella nätverk, VPN-gateway och lokal nätverksgateway

#### <a name="1-declare-your-variables--connect-to-your-subscription"></a>1. Deklarera variablerna och ansluta till din prenumeration
Den här övningen starta genom att deklarera våra variabler. Se till att ersätta värdena med dina egna när du konfigurerar för produktion.

```powershell
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
Kontrollera att du växlar till PowerShell-läge för att använda Resource Manager-cmdletar. Mer information finns i [Använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Skapa virtuella nätverk, VPN-gateway och lokal nätverksgateway
I följande exempel skapar det virtuella nätverket, TestVNet1 med tre undernät och VPN-gatewayen. När du ersätter värdena är det viktigt att du alltid namnger gateway-undernätet specifikt 'GatewaySubnet'. Om du ger det något annat namn går det inte att skapa gatewayen.

```powershell
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
> Du måste skapa en IPsec/IKE-princip för att aktivera alternativet ”UsePolicyBasedTrafficSelectors” på anslutningen.

I följande exempel skapar en IPsec/IKE-princip med dessa algoritmer och parametrar:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS24 SA livstid 3600 sekunder & 2048KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup PFS24 -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Skapa S2S VPN-anslutningen med principbaserad trafik väljare och IPsec/IKE-princip
Skapa en S2S VPN-anslutning och använda IPsec/IKE-principen som skapades i föregående steg. Tänk på att parametern ytterligare ”-UsePolicyBasedTrafficSelectors $True” som gör det möjligt för principbaserad trafik väljare för anslutningen.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

När du har slutfört stegen S2S VPN-anslutningen använder IPsec/IKE-princip som har definierats, och aktivera principbaserad trafik väljare för anslutningen. Du kan upprepa samma steg för att lägga till fler anslutningar till ytterligare lokala principbaserad VPN-enheter från samma Azure VPN-gatewayen.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Uppdatera principbaserad trafik väljare för en anslutning
Det sista avsnittet visar hur du uppdaterar alternativet principbaserad trafik väljare för en befintlig S2S VPN-anslutning.

### <a name="1-get-the-connection"></a>1. Hämta anslutningen
Hämta resursen anslutning.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Markera alternativet principbaserad trafik väljare
Följande rad visar om principbaserad trafik väljare som används för anslutningen:

```powershell
$connection6.UsePolicyBasedTrafficSelectors
```

Om raden returnerar ”**SANT**”, sedan principbaserad trafik väljare konfigureras på anslutningen; annars returneras ”**FALSKT**”.

### <a name="3-update-the-policy-based-traffic-selectors-on-a-connection"></a>3. Uppdatera väljare principbaserad trafik på en anslutning
När du har fått resursen anslutning, kan du aktivera eller inaktivera alternativet.

#### <a name="disable-usepolicybasedtrafficselectors"></a>Inaktivera UsePolicyBasedTrafficSelectors
I följande exempel inaktiverar alternativet principbaserad trafik väljare, men lämnar oförändrade IPsec/IKE-principen:

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

#### <a name="enable-usepolicybasedtrafficselectors"></a>Aktivera UsePolicyBasedTrafficSelectors
I följande exempel aktiverar alternativet principbaserad trafik väljare, men lämnar oförändrade IPsec/IKE-principen:

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för anvisningar.

Granska även [konfigurera IPsec/IKE-principen för S2S VPN- eller VNet-till-VNet-anslutningar](vpn-gateway-ipsecikepolicy-rm-powershell.md) för mer information om anpassade IPsec/IKE-principer.
