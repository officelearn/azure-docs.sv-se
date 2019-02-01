---
title: 'Konfigurera IPsec/IKE-principer för S2S VPN- eller VNet-till-VNet-anslutningar: Azure Resource Manager: PowerShell | Microsoft Docs'
description: Konfigurera IPsec/IKE-principer för S2S eller VNet-till-VNet-anslutningar med Azure VPN gateway med Azure Resource Manager och PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: c07634e7c75e166b77ecb1defab02b2601af6bb0
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510110"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Konfigurera IPsec/IKE-principer för S2S VPN- eller VNet-till-VNet-anslutningar

Den här artikeln vägleder dig igenom stegen för att konfigurera IPsec/IKE-principer för plats-till-plats-VPN eller VNet-till-VNet-anslutningar med hjälp av Resource Manager-distributionsmodellen och PowerShell.

## <a name="about"></a>Om IPsec och IKE principparametrar för Azure VPN-gatewayer
IPsec och IKE protokollet som standard stöder en mängd olika kryptografiska algoritmer i olika kombinationer. Referera till [om kryptografiska krav och Azure VPN-gatewayer](vpn-gateway-about-compliance-crypto.md) för att se hur detta kan hjälpa att se till att flera platser och VNet-till-VNet-anslutningen uppfyller dina krav på efterlevnad eller säkerhet.

Den här artikeln innehåller instruktioner för att skapa och konfigurera en IPsec/IKE-princip och gäller för en ny eller befintlig anslutning:

* [Del 1 – arbetsflöde för att skapa och ange IPsec/IKE-princip](#workflow)
* [Del 2 – stöds kryptografiska algoritmer och nyckellängder](#params)
* [Del 3 – skapa en ny S2S VPN-anslutning med IPsec/IKE-princip](#crossprem)
* [Del 4 – skapa en ny VNet-till-VNet-anslutning med IPsec/IKE-princip](#vnet2vnet)
* [Del 5 – hantera (skapa, lägga till, ta bort) IPsec/IKE-principer för en anslutning](#managepolicy)

> [!IMPORTANT]
> 1. Observera att IPsec/IKE-principer endast fungerar på följande gateway SKU: er:
>    * ***VpnGw1 VpnGw2, VpnGw3*** (routningsbaserad)
>    * ***Standard*** och ***HighPerformance*** (routningsbaserad)
> 2. Du kan bara ange ***en*** principkombination för en viss anslutning.
> 3. Du måste ange alla algoritmer och parametrar för både IKE (huvudläge) och IPsec (snabbläge). Partiell principspecifikationen tillåts inte.
> 4. Kontakta din VPN-leverantör enhetsspecifikationer för att se till att principen stöds på din lokala VPN-enheter. S2S- eller VNet-till-VNet-anslutningar kan inte upprätta om principerna är inkompatibla.

## <a name ="workflow"></a>Del 1 – arbetsflöde för att skapa och ange IPsec/IKE-princip
Det här avsnittet beskrivs arbetsflödet för att skapa och uppdatera IPsec/IKE-principen på en S2S VPN- eller VNet-till-VNet-anslutning:
1. Skapa ett virtuellt nätverk och en VPN-gateway
2. Skapa en lokal nätverksgateway för kryss anslutning eller ett annat virtuellt nätverk och gateway för VNet-till-VNet-anslutning
3. Skapa en IPsec/IKE-princip med valda algoritmer och parametrar
4. Skapa en anslutning (IPsec eller VNet2VNet) med IPsec/IKE-princip
5. Lägg till/Uppdatera/ta bort en IPsec/IKE-princip för en befintlig anslutning

Anvisningarna i den här artikeln hjälper dig att installera och konfigurera IPsec/IKE-principer som du ser i diagrammet:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>Del 2 – stöds kryptografiska algoritmer och nyckellängder

I följande tabell visas stöds kryptografiska algoritmer och nyckellängder som kan konfigureras av kunden:

| **IPsec/IKEv2**  | **Alternativ**    |
| ---  | --- 
| IKEv2-kryptering | AES256, AES192, AES128, DES3, DES  
| IKEv2 Integrity  | SHA384, SHA256, SHA1, MD5  |
| DH-grupp         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| IPsec-kryptering | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, None    |
| IPsec Integrity  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS-grupp        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, None 
| QM SA-livstid   | (**Valfritt**: standardvärden är används om inget anges)<br>Sekunder (heltal. **min. 300**/standard 27 000 sekunder)<br>Kilobyte (heltal. **min. 1024**/standard 102400000 kilobyte)   |
| Trafikväljare | UsePolicyBasedTrafficSelectors ** ($True/$False; **Valfritt**, $False som standard om inget anges)    |
|  |  |

> [!IMPORTANT]
> 1. **Din lokala VPN-enhetskonfiguration måste matcha eller innehålla följande algoritmer och parametrar som du anger på Azure IPsec/IKE-principer:**
>    * Krypteringsalgoritm för IKE (huvudläge / fas 1)
>    * IKE-integritetsalgoritm (huvudläge / fas 1)
>    * DH-grupp (huvudläge / fas 1)
>    * IPsec-krypteringsalgoritm (snabbläge / fas 2)
>    * IPsec-integritetsalgoritm (snabbläge / fas 2)
>    * PFS-grupp (snabbläge / fas 2)
>    * Trafik väljare (om UsePolicyBasedTrafficSelectors används)
>    * SA-livstider är lokala specifikationer och behöver inte matcha.
>
> 2. **Om GCMAES används som för IPsec-krypteringsalgoritm, måste du välja samma GCMAES-algoritm och nyckellängd för IPsec-integritet; till exempel med hjälp av GCMAES128 för både**
> 3. I tabellen ovan:
>    * IKEv2 motsvarar huvudläge eller fas 1
>    * IPsec motsvarar snabbläge eller fas 2
>    * DH-grupp anger Diffie-Hellmen-grupp som används i huvudläge eller fas 1
>    * PFS-grupp anges Diffie-Hellmen gruppen som används i snabbläge eller fas 2
> 4. IKEv2 Main Mode SA har en livslängd på högst 28 800 sekunder på Azure VPN-gatewayer
> 5. Ange ”UsePolicyBasedTrafficSelectors” till $True för en anslutning konfigurerar Azure VPN-gateway för att ansluta till principbaserad VPN-brandväggen lokalt. Om du aktiverar PolicyBasedTrafficSelectors, måste du se till att din VPN-enhet har matchande trafikväljare som definierats med alla prefixkombinationer i ditt lokala nätverk (lokal nätverksgateway) prefix till och från Azure-nätverksprefixen, i stället för alla-till-alla. Om ditt prefix för det lokala nätverket är 10.1.0.0/16 och 10.2.0.0/16 och ditt prefix för det virtuella nätverket är 192.168.0.0/16 och 172.16.0.0/16, måste du ange följande trafik väljare:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Läs mer om principbaserade trafikväljare [ansluta flera lokala principbaserade VPN-enheter](vpn-gateway-connect-multiple-policybased-rm-ps.md).

I följande tabell visas de motsvarande Diffie-Hellman-grupper som stöds av den anpassade principen:

| **Diffie-Hellman-grupp**  | **DHGroup**              | **PFSGroup** | **Nyckellängd** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768-bitars MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024-bitars MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048-bitars MODP  |
| 19                        | ECP256                   | ECP256       | 256-bitars ECP    |
| 20                        | ECP384                   | ECP284       | 384-bitars ECP    |
| 24                        | DHGroup24                | PFS24        | 2048-bitars MODP  |

Se [RFC3526](https://tools.ietf.org/html/rfc3526) och [RFC5114](https://tools.ietf.org/html/rfc5114) för mer information.

## <a name ="crossprem"></a>Del 3 – skapa en ny S2S VPN-anslutning med IPsec/IKE-princip

Det här avsnittet vägleder dig genom stegen för att skapa en S2S VPN-anslutning med en IPsec/IKE-princip. Följande steg skapar anslutningen som du ser i diagrammet:

![s2s-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Se [skapa en S2S VPN-anslutning](vpn-gateway-create-site-to-site-rm-powershell.md) detaljerade stegvisa instruktioner för att skapa en S2S VPN-anslutning.

### <a name="before"></a>Innan du börjar

* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Installera Azure Resource Managers PowerShell-cmdlets. Se [översikt av Azure PowerShell](/powershell/azure/overview) för mer information om hur du installerar PowerShell-cmdlets.

### <a name="createvnet1"></a>Steg 1 – Skapa virtuella nätverk, VPN-gateway och lokal nätverksgateway

#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler

I den här övningen börjar vi med att deklarera våra variabler. Se till att ersätta värdena med dina egna när du konfigurerar för produktion.

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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Anslut till din prenumeration och skapa en ny resursgrupp.

Se till att växla till PowerShell-läget för att kunna använda Resource Manager-cmdletarna. Mer information finns i [Använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Skapa virtuellt nätverk, VPN-gateway och lokal nätverksgateway

Följande exempel skapar det virtuella nätverket, TestVNet1 med tre undernät och en VPN-gateway. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt till GatewaySubnet. Om du ger det något annat namn går det inte att skapa gatewayen.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="s2sconnection"></a>Steg 2 – Skapa en S2S VPN-anslutning med en IPsec/IKE-princip

#### <a name="1-create-an-ipsecike-policy"></a>1. Skapa en IPsec/IKE-princip

Följande exempelskript skapar en IPsec/IKE-princip med följande algoritmer och parametrar:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS None, SA-livstid 14400 sekunder och 102 400 000 KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Om du använder GCMAES för IPsec, måste du använda samma GCMAES-algoritm och nyckellängd för både IPSec-kryptering och integritet. Till exempel ovan, motsvarande parametrar kommer att ”-IpsecEncryption GCMAES256 - IpsecIntegrity GCMAES256” när du använder GCMAES256.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Skapa S2S VPN-anslutningen med IPsec/IKE-princip

Skapa en S2S VPN-anslutning och använda IPsec/IKE-princip som skapades tidigare.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Du kan du lägga till ”-UsePolicyBasedTrafficSelectors $True” till cmdleten Skapa anslutning för att aktivera Azure VPN-gateway att ansluta till principbaserad VPN-enheter lokalt, enligt beskrivningen ovan.

> [!IMPORTANT]
> När en IPsec/IKE-princip har angetts på en anslutning, kommer Azure VPN-gatewayen endast skicka eller acceptera IPsec/IKE-förslag med angivna kryptografiska algoritmer och nyckellängder för viss anslutningen. Kontrollera att din lokala VPN-enhet för anslutningen använder eller accepterar den exakta principkombination annars S2S VPN-tunneln upprättas inte.


## <a name ="vnet2vnet"></a>Del 4 – skapa en ny VNet-till-VNet-anslutning med IPsec/IKE-princip

Stegen för att skapa en VNet-till-VNet-anslutning med en IPsec/IKE-princip är ungefär som en S2S VPN-anslutning. Följande exempelskript skapar anslutningen som du ser i diagrammet:

![v2v-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Se [skapa en VNet-till-VNet-anslutning](vpn-gateway-vnet-vnet-rm-ps.md) mer detaljerade anvisningar för att skapa en VNet-till-VNet-anslutning. Du måste slutföra [del 3](#crossprem) att skapa och konfigurera TestVNet1 och VPN-gatewayen.

### <a name="createvnet2"></a>Steg 1 – skapa det andra virtuella nätverk och VPN-gateway

#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler

Ersätt värdena med de som du vill använda för din konfiguration.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. Skapa det andra virtuella nätverk och VPN-gateway i den nya resursgruppen

```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Steg 2 – Skapa en VNet-toVNet-anslutning med IPsec/IKE-princip

S2S VPN-anslutningen, ett liknande sätt skapar en IPsec/IKE-princip och sedan tillämpa principen till den nya anslutningen.

#### <a name="1-create-an-ipsecike-policy"></a>1. Skapa en IPsec/IKE-princip

Följande exempelskript skapar en annan IPsec/IKE-princip med följande algoritmer och parametrar:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128 GCMAES128, PFS14, SA-livstid 14400 sekunder & 102 400 000 KB

```powershell
$ipsecpolicy2 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Skapa VNet-till-VNet-anslutningar med IPsec/IKE-princip

Skapa en VNet-till-VNet-anslutning och använda IPsec/IKE-principen som du skapade. I det här exemplet finns båda gatewayerna i samma prenumeration. Så det är möjligt att skapa och konfigurera båda anslutningarna med samma IPsec/IKE-princip i samma PowerShell-session.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> När en IPsec/IKE-princip har angetts på en anslutning, kommer Azure VPN-gatewayen endast skicka eller acceptera IPsec/IKE-förslag med angivna kryptografiska algoritmer och nyckellängder för viss anslutningen. Kontrollera att IPsec-principer för båda anslutningarna är likadana, annars VNet-till-VNet-anslutningen inte upprättas.

Anslutningen har upprättats på några minuter när du har slutfört de här stegen, och du kommer att ha följande nätverkets topologi som visas i början:

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>Del 5 – uppdatera IPsec/IKE-principer för en anslutning

Det sista avsnittet visar hur du hanterar IPsec/IKE-principer för en befintlig S2S eller VNet-till-VNet-anslutning. Den här övningen nedan vägleder dig genom följande åtgärder på en anslutning:

1. Visa IPsec/IKE-principer för en anslutning
2. Lägg till eller uppdatera IPsec/IKE-princip till en anslutning
3. Ta bort IPsec/IKE-princip från en anslutning

Samma steg gäller för både S2S och VNet-till-VNet-anslutningar.

> [!IMPORTANT]
> IPsec/IKE-principer stöds på *Standard* och *HighPerformance* routningsbaserade VPN-gatewayer. Det fungerar inte på Basic gateway-SKU eller principbaserade VPN-gateway.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Visa IPsec/IKE-principer för en anslutning

I följande exempel visar hur du hämtar IPsec/IKE-principer som konfigurerats på en anslutning. Skripten kan också fortsätta från övningarna ovan.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Det sista kommandot visar den aktuella IPsec/IKE-principen som konfigurerats i anslutningen, om sådana finns. Följande är ett exempel på utdata för anslutningen:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

Om det finns ingen IPsec/IKE-princip konfigurerad kommandot (PS > $connection6.policy) hämtar det returnerade en tom. Det innebär inte IPsec/IKE inte har konfigurerats på anslutningen, men att det finns inga anpassade IPsec/IKE-principer. Den aktuella anslutningen använder standardprincipen förhandlas mellan din lokala VPN-enhet och Azure VPN-gatewayen.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Lägg till eller uppdatera en IPsec/IKE-princip för en anslutning

Stegen för att lägga till en ny princip eller uppdatera en befintlig princip på en anslutning är samma: skapa en ny princip och sedan använder den nya principen för anslutningen.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Aktivera ”UsePolicyBasedTrafficSelectors” när du ansluter till en lokal principbaserad VPN-enhet genom att lägga till den ”-UsePolicyBaseTrafficSelectors” parameter till cmdleten, eller anger $False att inaktivera alternativet:

```powershell
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Du kan hämta anslutningen igen för att kontrollera om principen uppdateras.

```powershell
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Du bör se utdata från den sista raden som visas i följande exempel:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Ta bort en IPsec/IKE-princip från en anslutning

När du tar bort den anpassade principen från en anslutning Azure VPN-gatewayen återgår till den [standardlistan med IPsec/IKE-förslag](vpn-gateway-about-vpn-devices.md) och gör en omförhandling igen med din lokala VPN-enhet.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Du kan använda samma skript för att kontrollera om principen har tagits bort från anslutningen.

## <a name="next-steps"></a>Nästa steg

Se [ansluta flera lokala principbaserade VPN-enheter](vpn-gateway-connect-multiple-policybased-rm-ps.md) för mer information om trafikväljare med principbaserad.

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för anvisningar.
