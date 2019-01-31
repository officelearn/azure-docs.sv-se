---
title: Konfigurera plats-till-plats VPN-anslutningar för Azure Stack | Microsoft Docs
description: Lär dig mer om IPsec/IKE-principer för plats-till-plats VPN- eller VNet-till-VNet-anslutningar i Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: 9a14c819acbc46d8a281f73fd4f3185caa8a1e02
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478159"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>Konfigurera IPsec/IKE-princip för plats-till-plats VPN-anslutningar

Den här artikeln beskriver hur du konfigurerar en IPsec/IKE-princip för plats-till-plats (S2S) VPN-anslutningar i Azure Stack.

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>IPsec och IKE principparametrar för VPN-gatewayer

IPsec och IKE-protokollet standard stöder en mängd olika kryptografiska algoritmer i olika kombinationer. Vilka parametrar stöds i Azure Stack finns i [IPsec/IKE-parametrar](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), som kan hjälpa dig uppfylla dina krav på efterlevnad eller säkerhet.

Den här artikeln innehåller instruktioner om hur du skapar och konfigurera en IPsec/IKE-princip och tillämpa på en ny eller befintlig anslutning.

## <a name="considerations"></a>Överväganden

Observera följande viktiga överväganden när du använder dessa principer:

- IPsec/IKE-principer fungerar bara på den *Standard* och *HighPerformance* (routningsbaserad) gateway SKU: er.

- Du kan bara ange **en** principkombination för en viss anslutning.

- Du måste ange alla algoritmer och parametrar för både IKE (huvudläge) och IPsec (snabbläge). Partiell principspecifikationen tillåts inte.

- Kontakta din VPN-leverantör enhetsspecifikationer för att se till att principen stöds på din lokala VPN-enheter. Plats-till-plats-anslutningar kan inte skapas om principerna som är inkompatibla.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>Del 1 – arbetsflöde för att skapa och ange IPsec/IKE-princip

Det här avsnittet beskrivs arbetsflödet som krävs för att skapa och uppdatera IPsec/IKE-principen på en plats-till-plats VPN-anslutning:

1. Skapa ett virtuellt nätverk och en VPN-gateway.

2. Skapa en lokal nätverksgateway för anslutning för flera platser.

3. Skapa en IPsec/IKE-princip med valda algoritmer och parametrar.

4. Skapa en IPSec-anslutning med IPsec/IKE-principer.

5. Lägg till/Uppdatera/ta bort en IPsec/IKE-princip för en befintlig anslutning.

Anvisningarna i den här artikeln hjälp du installera och konfigurera IPsec/IKE-principer, enligt följande bild:

![Installera och konfigurera IPsec/IKE-principer](media/azure-stack-vpn-s2s/site-to-site.png)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>Del 2 – stöds kryptografiska algoritmer och nyckellängder

I följande tabell visas stöds kryptografiska algoritmer och nyckellängder som kan konfigureras av Azure Stack-kunder:

| IPsec/IKEv2                                          | Alternativ                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| IKEv2-kryptering                                     | AES256, AES192, AES128, DES3, DES                                        |
| IKEv2 Integrity                                      | SHA384, SHA256, SHA1, MD5                                                |
| DH-grupp                                             | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None         |
| IPsec-kryptering                                     | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, None |
| IPsec Integrity                                      | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5                       |
| PFS-grupp                                            | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, None                         |
| QM SA-livstid                                       | (Valfritt: standardvärden är används om inget anges)<br />                         Sekunder (heltal; min. 300/standard 27 000 sekunder)<br />                         Kbyte (heltal; min. 1024/standard 102400000 kilobyte) |                                                                          |
| Trafikväljare                                     | Principen baserat Trafikväljare stöds inte i Azure Stack.         |

- Din lokala konfiguration för VPN-enheten måste stämma överens med eller innehålla följande algoritmer och parametrar som du anger på Azure IPsec/IKE-principen:

  - Krypteringsalgoritm för IKE (huvudläge / fas 1)
  - IKE-integritetsalgoritm (huvudläge / fas 1)
  - DH-grupp (huvudläge / fas 1)
  - IPsec-krypteringsalgoritm (snabbläge / fas 2)
  - IPsec-integritetsalgoritm (snabbläge / fas 2)
  - PFS-grupp (snabbläge / fas 2)
  - SA-livstider är lokala specifikationer och behöver inte matcha.

- Om GCMAES används som för IPsec-krypteringsalgoritm, måste du välja samma GCMAES-algoritm och nyckellängd för IPsec-integritet; till exempel använder GCMAES128 för båda.

- I tabellen ovan:

  - IKEv2 motsvarar huvudläge eller fas 1
  - IPsec motsvarar snabbläge eller fas 2
  - DH-grupp anger Diffie-Hellmen-grupp som används i huvudläge eller fas 1
  - PFS-grupp anges Diffie-Hellmen gruppen som används i snabbläge eller fas 2

- IKEv2 Main Mode SA har en livslängd på högst 28 800 sekunder på Azure Stack VPN-gatewayer.

I följande tabell visas de motsvarande Diffie-Hellman-grupper som stöds av den anpassade principen:

| Diffie-Hellman Group | DHGroup   | PFSGroup      | Nyckellängd    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | 768-bitars MODP  |
| 2                    | DHGroup2  | PFS2          | 1024-bitars MODP |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | 2048-bitars MODP |
| 19                   | ECP256    | ECP256        | 256-bitars ECP   |
| 20                   | ECP384    | ECP384        | 384-bitars ECP   |
| 24                   | DHGroup24 | PFS24         | 2048-bitars MODP |

Mer information finns i [RFC3526](https://tools.ietf.org/html/rfc3526) och [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>Del 3 – skapa en ny plats-till-plats-VPN-anslutning med IPsec/IKE-princip

Det här avsnittet vägleder dig genom stegen för att skapa en plats-till-plats VPN-anslutning med en IPsec/IKE-princip. Följande steg skapar anslutningen, enligt följande bild:

![plats-till-plats-policy](media/azure-stack-vpn-s2s/site-to-site.png)

Mer detaljerade stegvisa instruktioner för att skapa en plats-till-plats VPN-anslutning finns i [skapa en plats-till-plats VPN-anslutning](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

### <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du kontrollera att du har följande krav:

- En Azure-prenumeration. Om du inte redan har en Azure-prenumeration kan du aktivera din [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), eller registrera dig för en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

- Azure Resource Managers PowerShell-cmdletar. Se [installera PowerShell för Azure Stack](../azure-stack-powershell-install.md) för mer information om hur du installerar PowerShell-cmdlets.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Steg 1 – Skapa virtuella nätverk, VPN-gateway och lokal nätverksgateway

#### <a name="1-declare-variables"></a>1. Deklarera variabler

Börja med att deklarera följande variabler i den här övningen. Tänk på att ersätta platshållarna med dina egna värden när du konfigurerar för produktion:

```powershell
$Sub1 = "\<YourSubscriptionName\>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Anslut till din prenumeration och skapa en ny resursgrupp.

Se till att växla till PowerShell-läget för att kunna använda Resource Manager-cmdletarna. Mer information finns i [Anslut till Azure Stack med PowerShell som en användare](azure-stack-powershell-configure-user.md).

Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Skapa virtuellt nätverk, VPN-gateway och lokal nätverksgateway

I följande exempel skapas ett virtuellt nätverk och **TestVNet1**med tre undernät och en VPN-gateway. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt **GatewaySubnet**. Om du ger det något annat namn går det inte att skapa gatewayen.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>Steg 2 – Skapa en plats-till-plats VPN-anslutning med en IPsec/IKE-princip

#### <a name="1-create-an-ipsecike-policy"></a>1. Skapa en IPsec/IKE-princip

Det här exempelskriptet skapar en IPsec/IKE-princip med följande algoritmer och parametrar:

- IKEv2: AES128, SHA1, DHGroup14
- IPsec: AES256, SHA256, none, SA-livstid 14400 sekunder och 102 400 000 KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Om du använder GCMAES för IPsec, måste du använda samma GCMAES-algoritm och nyckellängd för både IPSec-kryptering och integritet.

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. Skapa plats-till-plats VPN-anslutning med IPsec/IKE-princip

Skapa en plats-till-plats VPN-anslutning och använda IPsec/IKE-principen som du skapade tidigare.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> När en IPsec/IKE-princip har angetts på en anslutning, kommer Azure VPN-gatewayen endast skicka eller acceptera IPsec/IKE-förslag med angivna kryptografiska algoritmer och nyckellängder för viss anslutningen. Kontrollera att din lokala VPN-enhet för anslutningen använder eller accepterar den exakta principkombination annars inte upprättas plats-till-plats VPN-tunneln.

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>Del 4 – uppdatera IPsec/IKE-principer för en anslutning

Föregående avsnitt visade hur du hanterar IPsec/IKE-principer för en befintlig plats-till-plats-anslutning. Följande avsnitt beskriver följande åtgärder på en anslutning:

1. Visa IPsec/IKE-principer för en anslutning
2. Lägg till eller uppdatera IPsec/IKE-princip till en anslutning
3. Ta bort IPsec/IKE-princip från en anslutning

> [!NOTE]
> IPsec/IKE-principer stöds på *Standard* och *HighPerformance* routningsbaserade VPN-gatewayer. Det fungerar inte på den *grundläggande* gateway-SKU.

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Visa IPsec/IKE-principer för en anslutning

I följande exempel visar hur du hämtar IPsec/IKE-principer som konfigurerats på en anslutning. Skripten kan också fortsätta från föregående övning:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Det sista kommandot visar den aktuella IPsec/IKE-principen som konfigurerats i anslutningen, om sådana. I följande exempel är ett exempel på utdata för anslutningen:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

Om det finns ingen IPsec/IKE-princip konfigurerad kommandot `$connection6.policy` hämtar en tom returnerade. Det innebär inte att IPsec/IKE inte har konfigurerats på anslutningen. Det innebär att det finns inga anpassade IPsec/IKE-principer. Den aktuella anslutningen använder standardprincipen förhandlas mellan din lokala VPN-enhet och Azure VPN-gatewayen.

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Lägg till eller uppdatera en IPsec/IKE-princip för en anslutning

Stegen för att lägga till en ny princip eller uppdatera en befintlig princip på en anslutning är samma: skapa en ny princip och tillämpa den nya principen för anslutningen.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Du kan få anslutningen igen för att kontrollera om principen uppdateras:

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Du bör se utdata från den sista raden som visas i följande exempel:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Ta bort en IPsec/IKE-princip från en anslutning

När du tar bort den anpassade principen från en anslutning Azure VPN-gatewayen återgår till den [standard IPsec/IKE-förslag](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), och gör en omförhandling med din lokala VPN-enhet.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = “AzS123”
$currentpolicy = $connection6.IpsecPolicies\[0\]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Du kan använda samma skript för att kontrollera om principen har tagits bort från anslutningen.

## <a name="next-steps"></a>Nästa steg

- [Konfigurationsinställningar för VPN-gateway för Azure Stack](azure-stack-vpn-gateway-settings.md)
