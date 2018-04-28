---
title: 'Konfigurera aktiv-aktiv S2S VPN-anslutningar för VPN-gatewayer: Azure Resource Manager: PowerShell | Microsoft Docs'
description: Den här artikeln beskriver hur du konfigurerar aktiv-aktiv anslutningar med Azure VPN-gatewayer med Azure Resource Manager och PowerShell.
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
ms.date: 01/24/2018
ms.author: yushwang
ms.openlocfilehash: c09abe97d34b7220d76481a403165f1b7e07fcaa
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Konfigurera aktiv-aktiv S2S VPN-anslutningar med Azure VPN-gatewayer

Den här artikeln vägleder dig genom stegen för att skapa aktiv-aktiv mellan platser och VNet-till-VNet-anslutningar med Resource Manager-distributionsmodellen och PowerShell.

## <a name="about-highly-available-cross-premises-connections"></a>Om hög tillgänglighet anslutningar mellan platser
För att uppnå hög tillgänglighet för anslutningar mellan platser och VNet-till-VNet-anslutningar bör du distribuera flera VPN-gatewayer och upprätta flera parallella anslutningar mellan ditt nätverk och Azure. Se [hög tillgänglighet mellan platser och VNet-till-VNet-anslutningar](vpn-gateway-highlyavailable.md) en översikt över alternativ för nätverksanslutning och topologi.

Den här artikeln innehåller instruktioner för att ställa in en aktiv-aktiv mellan lokala VPN-anslutning och aktiv-aktiv anslutning mellan två virtuella nätverk.

* [Del 1 - Skapa och konfigurera Azure VPN-gateway i aktivt-aktivt läge](#aagateway)
* [Del 2 – upprätta aktiv-aktiv anslutningar mellan platser](#aacrossprem)
* [Del 3 – skapa aktiv-aktiv VNet-till-VNet-anslutningar](#aav2v)

Om du redan har en VPN-gateway kan du:
* [Uppdatera en befintlig VPN-gateway från aktivt vänteläge till aktiv-aktiv eller tvärtom](#aaupdate)

Du kan kombinera dem tillsammans för att skapa en mer komplexa, hög tillgänglighet nätverkstopologi som uppfyller dina behov.

> [!IMPORTANT]
> Aktiv-aktiv läge använder bara de följande SKU: er: 
  * VpnGw1 VpnGw2, VpnGw3
  * HighPerformance (för gamla äldre SKU: er)
> 
> 

## <a name ="aagateway"></a>Del 1 - Skapa och konfigurera VPN-gatewayer för aktiv-aktiv
Följande steg konfigurerar Azure VPN-gateway i lägena för aktiv-aktiv. De viktigaste skillnaderna mellan aktiv-aktiv och aktivt vänteläge gateway:

* Du måste skapa två Gateway IP-konfigurationer med två offentliga IP-adresser
* Du måste ange flaggan EnableActiveActiveFeature
* Gateway-SKU: N måste vara VpnGw1 VpnGw2, VpnGw3 eller HighPerformance (äldre SKU).

De andra egenskaperna är samma som aktiv aktiv gateway. 

### <a name="before-you-begin"></a>Innan du börjar
* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Du måste installera Azure Resource Managers PowerShell-cmdletar. Se [översikt av Azure PowerShell](/powershell/azure/overview) för mer information om installation av PowerShell-cmdlets.

### <a name="step-1---create-and-configure-vnet1"></a>Steg 1 – Skapa och konfigurera VNet1
#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler
I den här övningen börjar vi med att deklarera våra variabler. I exemplet nedan deklarerar vi variablerna med de värden som gäller för den här övningen. Se till att ersätta värdena med dina egna när du konfigurerar för produktion. Du kan använda dessa variabler om du använder anvisningarna för att bekanta dig med den här typen av konfiguration. Ändra variablerna samt kopiera och klistra in dem i PowerShell-konsolen.

```powershell
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Anslut till din prenumeration och skapa en ny resursgrupp
Se till att växla till PowerShell-läget för att kunna använda Resource Manager-cmdletarna. Mer information finns i [Använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Skapa TestVNet1
Exemplet nedan skapar ett virtuellt nätverk med namnet TestVNet1 och tre undernät, där ett kallas GatewaySubnet, ett kallas FrontEnd och ett kallas BackEnd. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt till GatewaySubnet. Om du ger det något annat namn går det inte att skapa gatewayen.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Steg 2 – skapa VPN-gateway för TestVNet1 med aktiv-aktiv läge
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Skapa offentliga IP-adresser och gateway IP-konfigurationer
Begäran om två offentliga IP-adresser som ska allokeras till den gateway som du skapar för din VNet. Du måste också definiera undernätet och IP-konfigurationer som krävs.

```powershell
$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Skapa VPN-gateway med aktiv-aktiv konfiguration
Skapa den virtuella nätverksgatewayen för TestVNet1. Observera att det finns två GatewayIpConfig poster och flaggan EnableActiveActiveFeature har angetts. Det kan ta en stund att skapa en gateway (45 minuter eller mer).

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Hämta gateway offentliga IP-adresser och BGP-Peer-IP-adress
När du har skapat, behöver du skaffa BGP-Peer-IP-adressen på Azure VPN-Gateway. Den här adressen behövs för att konfigurera Azure VPN-Gateway som en BGP-Peer för dina lokala VPN-enheter.

```powershell
$gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Du kan använda följande cmdletar för att visa de två offentliga IP-adresser som har allokerats för din VPN-gateway och deras motsvarande BGP-Peer-IP-adresser för varje gateway-instans:

```powershell
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

Ordningen för den offentliga IP-adresserna för gateway-instanser och motsvarande BGP-Peering adresserna är samma. I det här exemplet använder gateway VM med en offentlig IP för 40.112.190.5 10.12.255.4 som sin BGP-Peering adress och gateway med 138.91.156.129 använder 10.12.255.5. Den här informationen behövs när du konfigurerar din på lokala VPN-enheter som ansluter till aktiv-aktiv-gateway. Gatewayen visas i diagrammet nedan med alla adresser:

![aktiv-aktiv gateway](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

När gatewayen är skapad, kan du använda den här gatewayen för att etablera aktiv-aktiv mellan lokala eller VNet-till-VNet-anslutningen. I följande avsnitt gå igenom stegen för att slutföra den här övningen.

## <a name ="aacrossprem"></a>Del 2 – upprätta en anslutning för aktiv-aktiv mellan platser
För att upprätta en anslutning mellan platser, måste du skapa en lokal nätverksgateway som representerar din lokala VPN-enhet och en anslutning för att ansluta Azure VPN-gateway med den lokala nätverksgatewayen. I det här exemplet är Azure VPN-gateway i aktivt-aktivt läge. Även om det finns endast en lokal VPN-enheter (lokal nätverksgateway) och en anslutning resurser, kommer båda Azure VPN gateway-instanser därför upprätta S2S VPN-tunnlar med den lokala enheten.

Innan du fortsätter, kontrollera att du har slutfört [del 1](#aagateway) för den här övningen.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Steg 1 – Skapa och konfigurera den lokala nätverksgatewayen
#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler
Den här övningen kommer att fortsätta att skapa den konfiguration som visas i diagrammet. Ersätt värdena med de som du vill använda för din konfiguration.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Några saker att Observera för de lokala nätverket gateway-parametrarna:

* Den lokala nätverksgatewayen kan vara i samma eller en annan plats och resursgruppen som VPN-gateway. Det här exemplet visas de i olika resursgrupper men i samma Azure-plats.
* Om det finns bara en lokal VPN-enhet som du ser ovan, kan aktiv-aktiv anslutning arbeta med eller utan BGP-protokollet. Det här exemplet använder BGP för anslutningen mellan platser.
* Om BGP är aktiverat, är det prefix som du måste deklarera för den lokala nätverksgatewayen värdadressen av BGP-Peer-IP-adress på VPN-enhet. I det här fallet är det en /32 prefixet ”10.52.255.253/32”.
* Du måste använda olika BGP, ASN: er, mellan ditt lokala nätverk och Azure VNet som en påminnelse. Om de är samma som du behöver ändra ditt VNet ASN om din lokala VPN-enhet använder redan ASN till peer-datorn med andra BGP-grannar.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Skapa lokal nätverksgateway för Site5
Innan du fortsätter kontrollerar du att du fortfarande är ansluten till Prenumeration 1. Skapa resursgruppen om det inte har skapats ännu.

```powershell
New-AzureRmResourceGroup -Name $RG5 -Location $Location5
New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Steg 2 – ansluta VNet gateway och lokal nätverksgateway
#### <a name="1-get-the-two-gateways"></a>1. Hämta två gateways

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzureRmLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Skapa TestVNet1 till Site5 anslutning
I det här steget skapar du anslutningen från TestVNet1 till Site5_1 med värdet $True ”Enablegpg”.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN och BGP parametrar för din lokala VPN-enhet
Exemplet nedan visas de parametrar som du ska ange i konfigurationsavsnittet BGP på din lokala VPN-enhet för den här övningen:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Anslutningen ska upprättas efter några minuter och BGP-peeringsessionen startar när IPsec-anslutning har upprättats. Hittills har det här exemplet konfigureras bara en lokal VPN-enhet, vilket resulterar i diagrammet nedan:

![aktiv-aktiv-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Steg 3 – ansluta två lokala VPN-enheter till aktiv-aktiv VPN-gateway
Om du har två VPN-enheter på samma lokala nätverk kan du uppnå dubbla redundans genom att ansluta Azure VPN-gatewayen till andra VPN-enheten.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Skapa den andra lokala nätverksgatewayen för Site5
IP-adressen för gateway, adressprefix och adress för BGP-peering för andra lokala nätverksgateway får inte överlappa med föregående lokal nätverksgateway för samma lokala nätverk.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Ansluta en gateway för virtuellt nätverk och andra lokal nätverksgateway
Skapa anslutningen från TestVNet1 till Site5_2 med värdet $True ”EnableBGP”

```powershell
$lng5gw2 = Get-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. Parametrarna för VPN och BGP för andra lokala VPN-enheten
På liknande sätt nedan visar parametrarna skriver du in i andra VPN-enhet:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

När anslutningen (tunnlar) är klar har du dubbla redundant VPN-enheter och ansluter dina lokala nätverk och Azure-tunnlar:

![dubbla-redundans-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>Del 3: upprätta en anslutning för aktiv-aktiv VNet-till-VNet
Det här avsnittet skapar du en aktiv-aktiv VNet-till-VNet-anslutning med BGP. 

Anvisningarna nedan fortsätter från föregående steg ovan. Du måste slutföra [del 1](#aagateway) att skapa och konfigurera TestVNet1 och VPN-Gateway med BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Steg 1 – Skapa TestVNet2 och VPN-gateway
Det är viktigt att se till att IP-adressutrymmet för det nya virtuella nätverket TestVNet2, inte överlappar med någon av VNet-intervall.

I det här exemplet tillhör de virtuella nätverken samma prenumeration. Du kan konfigurera VNet-till-VNet-anslutningar mellan olika prenumerationer; Se [konfigurera VNet-till-VNet-anslutningen](vpn-gateway-vnet-vnet-rm-ps.md) att lära dig mer information. Kontrollera att du lägger till den ”-Enablegpg $True” när du skapar anslutningar för att aktivera BGP.

#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler
Ersätt värdena med de som du vill använda för din konfiguration.

```powershell
$RG2 = "TestAARG2"
$Location2 = "East US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Skapa TestVNet2 i den nya resursgruppen

```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Skapa VPN-gateway för aktiv-aktiv för TestVNet2
Begäran om två offentliga IP-adresser som ska allokeras till den gateway som du skapar för din VNet. Du måste också definiera undernätet och IP-konfigurationer som krävs.

```powershell
$gw2pip1 = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Skapa VPN-gateway med många AS och flaggan ”EnableActiveActiveFeature”. Observera att du måste åsidosätta standardvärdet ASN på Azure VPN-gatewayer. ASN: er för det anslutna Vnet måste vara olika för att aktivera BGP och transitroutning.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Steg 2 – ansluta TestVNet1 och TestVNet2 gateway
I det här exemplet är både gateways i samma prenumeration. Du kan slutföra det här steget i samma PowerShell-sessionen.

#### <a name="1-get-both-gateways"></a>1. Hämta båda gateways
Kontrollera att du har loggat in och anslutit till Prenumeration 1.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Skapa både anslutningar
I det här steget skapar du anslutningen från TestVNet1 till TestVNet2 och anslutningen från TestVNet2 till TestVNet1.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Glöm inte att aktivera BGP för både anslutningar.
> 
> 

När du har slutfört de här stegen anslutningen upprättas i några minuter och BGP-är peering-session när VNet-till-VNet-anslutningen har slutförts med dubbla redundans:

![aktiv-aktiv-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Uppdatera en befintlig VPN-gateway

Det här avsnittet hjälper dig att ändra en befintlig Azure VPN-gateway från aktivt vänteläge till aktiv-aktiv läge, och vice versa.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Ändra ett aktivt vänteläge gateway till en aktiv-aktiv-gateway

I följande exempel konverterar ett aktivt vänteläge gateway till en aktiv-aktiv gateway. När du ändrar en aktivt vänteläge gateway till aktiv-aktiv kan du skapa en annan offentlig IP-adress och sedan lägga till en andra Gateway IP-konfiguration.

#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler

Ersätt följande parametrar som används för exemplen med de inställningar du behöver för din egen konfiguration och sedan deklarera variablerna.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Du kan kopiera och klistra in det här exemplet att PowerShell-konsolen efter deklarera variablerna.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Skapa den offentliga IP-adressen och lägger till andra gateway IP-konfiguration

```powershell
$gwpip2 = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Aktivera läget för aktiv-aktiv och uppdatera gatewayen

I det här steget aktivera läget för aktiv-aktiv och uppdatera gatewayen. I det här exemplet använder just nu VPN-gateway är en äldre Standard SKU. Aktiv-aktiv stöder dock inte Standard-SKU. Om du vill ändra storlek på äldre SKU: N till en som stöds (i det här fallet HighPerformance), anger du bara stöds äldre SKU: N som du vill använda.

* Du kan inte ändra en äldre SKU till en ny SKU: er med hjälp av det här steget. Du kan bara ändra storlek på en äldre SKU till en annan stöds äldre SKU. Till exempel ändra du inte SKU: N från Standard till VpnGw1 (även om VpnGw1 stöds för aktiv-aktiv) eftersom Standard är en äldre SKU och VpnGw1 är aktuella SKU. Mer information om storleksändring och migrera SKU: er finns [Gateway-SKU: er](vpn-gateway-about-vpngateways.md#gwsku).

* Om du vill ändra storlek på en aktuell SKU, till exempel VpnGw1 till VpnGw3, kan du göra detta med hjälp av det här steget eftersom de SKU: er med samma familj av SKU. Om du vill göra det använder värdet: ```-GatewaySku VpnGw3```

När du använder detta i din miljö, om du inte behöver ändra storlek på gatewayen, behöver du inte ange - GatewaySku. Observera att i det här steget måste du ange gateway-objektet i PowerShell för att utlösa den faktiska uppdateringen. Den här uppdateringen kan ta 30 till 45 minuter, även om du inte ändrar storlek på din gateway.

```powershell
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Ändra en aktiv-aktiv gateway till en gateway i aktivt vänteläge
#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler

Ersätt följande parametrar som används för exemplen med de inställningar du behöver för din egen konfiguration och sedan deklarera variablerna.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Hämta namnet på IP-konfiguration som du vill ta bort efter att deklarera variablerna.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Ta bort gateway-IP-konfigurationen och inaktivera aktiv-aktiv-läge

Använd det här exemplet för att ta bort gateway-IP-konfigurationen och inaktivera aktivt-aktivt läge. Observera att du måste ange gateway-objektet i PowerShell för att utlösa den faktiska uppdateringen.

```powershell
Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Den här uppdateringen kan ta upp till 30 till 45 minuter.

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för anvisningar.
