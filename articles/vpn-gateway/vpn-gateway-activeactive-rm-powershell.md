---
title: 'Konfigurera aktiv-aktiv S2S VPN-anslutningar för VPN-gatewayer: Azure Resource Manager: PowerShell | Microsoft Docs'
description: Den här artikeln beskriver hur du konfigurerar aktiv-aktiv-anslutningar med Azure VPN gateway med Azure Resource Manager och PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang, cherylmc
ms.openlocfilehash: 4c5a7a138a2b491867c5c4ba7234415036c8ba0e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100844"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Konfigurera aktiv-aktiv S2S VPN-anslutningar med Azure VPN gateway

Den här artikeln vägleder dig igenom stegen för att skapa aktiv-aktiv på flera platser och VNet-till-VNet-anslutningar med hjälp av Resource Manager-distributionsmodellen och PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-highly-available-cross-premises-connections"></a>Om anslutningar med hög tillgänglighet på flera platser
För att uppnå hög tillgänglighet för flera platser och VNet-till-VNet-anslutning, bör du distribuera flera VPN-gatewayer och upprätta flera parallella anslutningar mellan ditt nätverk och Azure. Se [med hög tillgänglighet på flera platser och VNet-till-VNet-anslutning](vpn-gateway-highlyavailable.md) för en översikt över alternativ för nätverksanslutning och topologi.

Den här artikeln innehåller instruktioner för att konfigurera en aktiv-aktiv mellan lokala VPN-anslutning och aktiv-aktiv anslutning mellan två virtuella nätverk.

* [Del 1 – Skapa och konfigurera din Azure VPN-gateway i aktivt-aktivt läge](#aagateway)
* [Del 2 – upprättar aktiv-anslutningar på flera platser](#aacrossprem)
* [Del 3 – upprättar aktiv-aktiv VNet-till-VNet-anslutningar](#aav2v)

Om du redan har en VPN-gateway kan du:
* [Uppdatera en befintlig VPN-gateway från aktiv-standby till aktiv-aktiv eller tvärtom](#aaupdate)

Du kan kombinera dessa tillsammans för att skapa en mer komplex, med hög tillgänglighet nätverkstopologi som uppfyller dina behov.

> [!IMPORTANT]
> Aktivt-aktivt läge använder endast följande SKU: er: 
>   * VpnGw1 VpnGw2, VpnGw3
>   * HighPerformance (för gamla äldre SKU: er)

## <a name ="aagateway"></a>Del 1 – Skapa och konfigurera aktiv-aktiv VPN-gatewayer
Följande steg konfigurerar din Azure VPN-gateway i aktiv-aktiv-lägen. De viktigaste skillnaderna mellan aktiv-aktiv och aktiv-standby-gatewayer:

* Du måste skapa två Gateway-IP-konfigurationer med två offentliga IP-adresser
* Du måste ställa in flaggan EnableActiveActiveFeature
* Gateway-SKU måste vara VpnGw1, VpnGw2, VpnGw3 eller HighPerformance (äldre SKU).

De andra egenskaperna är samma som icke-aktiv-aktiv-gateways. 

### <a name="before-you-begin"></a>Innan du börjar
* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Du måste installera Azure Resource Managers PowerShell-cmdletar. Se [översikt av Azure PowerShell](/powershell/azure/overview) för mer information om hur du installerar PowerShell-cmdlets.

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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Anslut till din prenumeration och skapa en ny resursgrupp.
Se till att växla till PowerShell-läget för att kunna använda Resource Manager-cmdletarna. Mer information finns i [Använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Skapa TestVNet1
Exemplet nedan skapar ett virtuellt nätverk med namnet TestVNet1 och tre undernät, där ett kallas GatewaySubnet, ett kallas FrontEnd och ett kallas BackEnd. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt till GatewaySubnet. Om du ger det något annat namn går det inte att skapa gatewayen.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Steg 2 – skapa VPN-gatewayen för TestVNet1 med aktivt-aktivt läge
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Skapa offentlig IP-adresser och gateway-IP-konfigurationer
Begära två offentliga IP-adresser som ska allokeras till den gateway som du skapar för ditt virtuella nätverk. Du kan också definiera undernätet och IP-konfigurationer som krävs.

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Skapa en VPN-gateway med aktiv-aktiv konfiguration
Skapa den virtuella nätverksgatewayen för TestVNet1. Observera att det finns två GatewayIpConfig poster och EnableActiveActiveFeature-flagga har angetts. Det kan ta en stund att skapa en gateway (45 minuter eller mer).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Hämta gateway-offentliga IP-adresser och BGP-Peer-IP-adress
När gatewayen har skapats måste du hämta den BGP-Peer-IP-adressen på Azure VPN Gateway. Den här adressen behövs för att konfigurera Azure VPN-gatewayen som en BGP-Peer för dina lokala VPN-enheter.

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
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

Ordningen för den offentliga IP-adresser för gateway-instanser och motsvarande BGP-Peering adresserna är samma. I det här exemplet använder gateway-datorn med offentliga IP-Adressen för 40.112.190.5 10.12.255.4 som sin BGP-Peering-adress och gateway med 138.91.156.129 använder 10.12.255.5. Den här informationen behövs när du ställer in din på lokala VPN-enheter som ansluter till aktiv-aktiv-gateway. Gatewayen visas i diagrammet nedan med alla adresser:

![aktiv-aktiv-gateway](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

När gatewayen har skapats kan använda du denna gateway för att upprätta aktiv-aktiv på olika platser eller VNet-till-VNet-anslutning. I följande avsnitt beskriver stegen för att slutföra den här övningen.

## <a name ="aacrossprem"></a>Del 2 – ansluta en aktiv-aktiv på olika platser
För att upprätta en anslutning mellan olika platser, måste du skapa en lokal nätverksgateway som representerar din lokala VPN-enhet och en anslutning för att ansluta Azure VPN-gateway med den lokala nätverksgatewayen. I det här exemplet är Azure VPN-gatewayen i aktivt-aktivt läge. Även om det finns bara en lokal VPN-enhet (lokal nätverksgateway) och en connection-resursen, kommer båda Azure VPN gateway-instanser därför upprätta S2S VPN-tunnlar med den lokala enheten.

Innan du fortsätter, kontrollera att du har slutfört [del 1](#aagateway) av den här övningen.

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

Några saker att tänka på angående lokala gateway-parametrar:

* Den lokala nätverksgatewayen kan vara i samma eller en annan plats och resursgrupp som VPN-gateway. Det här exemplet visar dem i olika resursgrupper, men i samma Azure-plats.
* Om det finns bara en lokal VPN-enhet enligt ovan, kan aktiv-aktiv anslutning arbeta med eller utan BGP-protokollet. Det här exemplet använder BGP för anslutningen mellan olika platser.
* Om BGP är aktiverat är prefixet måste du deklarera för den lokala nätverksgatewayen värdadress din BGP-Peer-IP-adress på VPN-enheten. I det här fallet är det en /32 prefixet för ”10.52.255.253/32”.
* Du måste använda olika BGP ASN-nummer mellan ditt lokala nätverk och virtuella Azure-nätverket som en påminnelse. Om de är likadana, måste du ändra din VNet ASN om den lokala VPN-enheten redan använder ASN att peer-kopplas med andra BGP-grannar.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Skapa den lokala nätverksgatewayen för Site5
Innan du fortsätter kontrollerar du att du fortfarande är ansluten till Prenumeration 1. Skapa resursgruppen om den inte har skapat.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Steg 2 – ansluta VNet-gateway och lokal nätverksgateway
#### <a name="1-get-the-two-gateways"></a>1. Få två gateways

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Skapa TestVNet1-till Site5 anslutning
I det här steget skapar du anslutningen från TestVNet1 till Site5_1 med ”EnableBGP” ange som $True.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN och BGP parametrar för din lokala VPN-enhet
I exemplet nedan visas de parametrar som du ska ange i konfigurationsavsnittet BGP på din lokala VPN-enhet för den här övningen:

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

Anslutningen bör vara upprättad efter några minuter och BGP-peeringsessionen startar när IPsec-anslutning har upprättats. Det här exemplet konfigureras hittills har endast en lokal VPN-enhet, vilket resulterar i diagrammet nedan:

![active-active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Steg 3 – ansluta två lokala VPN-enheter till aktiv-aktiv VPN-gateway
Om du har två VPN-enheter på samma lokala nätverk kan få du dubbel redundans genom att ansluta Azure VPN-gateway och den andra VPN-enheten.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Skapa den andra lokala nätverksgatewayen för Site5
Gatewayens IP-adress, adressprefixet och BGP-peering adress för andra lokala nätverksgateway får inte överlappa med den tidigare lokala nätverksgatewayen för samma lokala nätverk.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Ansluta VNet-gateway och andra lokala nätverksgateway
Skapa anslutningen från TestVNet1 till Site5_2 med ”EnableBGP” ange som $True

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. Parametrarna för VPN och BGP för andra lokala VPN-enheten
På samma sätt nedan listas parametrarna du ska ange i den andra VPN-enheten:

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

När anslutningen (tunnlar) har upprättats kan har du dubbel redundant VPN-enheter och tunnlar ansluta ditt lokala nätverk och Azure:

![dual-redundancy-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>Del 3 – upprätta en aktiv-aktiv VNet-till-VNet-anslutning
Det här avsnittet skapar du en aktiv-aktiv VNet-till-VNet-anslutning med BGP. 

Anvisningarna nedan fortsätter från föregående steg ovan. Du måste slutföra [del 1](#aagateway) att skapa och konfigurera TestVNet1 och VPN-Gateway med BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Steg 1 – Skapa TestVNet2 och VPN-gateway
Det är viktigt att se till att IP-adressutrymmet för det nya virtuella nätverket TestVNet2, inte överlappar med något av dina VNet-intervall.

I det här exemplet är tillhöra de virtuella nätverken samma prenumeration. Du kan konfigurera VNet-till-VNet-anslutningar mellan olika prenumerationer; Se [konfigurera en VNet-till-VNet-anslutning](vpn-gateway-vnet-vnet-rm-ps.md) vill veta mer. Kontrollera att du lägger till den ”-EnableBgp $True” när du skapar anslutningar för att aktivera BGP.

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
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Skapa en aktiv-aktiv VPN-gateway för TestVNet2
Begära två offentliga IP-adresser som ska allokeras till den gateway som du skapar för ditt virtuella nätverk. Du kan också definiera undernätet och IP-konfigurationer som krävs.

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Skapa en VPN-gateway med AS-nummer och flaggan ”EnableActiveActiveFeature”. Observera att du måste åsidosätta standard-ASN på Azure VPN-gatewayer. ASN: er för anslutna virtuella nätverk måste vara olika för att aktivera BGP och transit routning.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Steg 2 – ansluta TestVNet1 och TestVNet2 gateway
I det här exemplet finns båda gatewayerna i samma prenumeration. Du kan slutföra det här steget i samma PowerShell-session.

#### <a name="1-get-both-gateways"></a>1. Hämta båda gatewayerna
Kontrollera att du har loggat in och anslutit till Prenumeration 1.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Skapa båda anslutningarna
I det här steget skapar du anslutningen från TestVNet1 till TestVNet2 och anslutningen från TestVNet2 till TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Glöm inte att aktivera BGP för båda anslutningarna.
> 
> 

När du har slutfört dessa steg ska upprätta en anslutning i ett par minuter och BGP-är peering session upp när VNet-till-VNet-anslutningen är klar med dubbel redundans:

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Uppdatera en befintlig VPN-gateway

Det här avsnittet hjälper dig att ändra en befintlig Azure VPN-gateway från aktiv-standby till aktivt-aktivt läge eller vice versa.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Ändra en aktiv-standby-gateway till en aktiv-aktiv-gateway

I följande exempel konverterar en aktiv-standby-gateway till en aktiv-aktiv-gateway. När du ändrar en aktiv-standby-gateway till aktiv-aktiv kan du skapa en annan offentlig IP-adress och sedan lägga till en andra Gateway-IP-konfiguration.

#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler

Ersätt följande parametrar används i exemplen med de inställningar du behöver för din egen konfiguration och sedan deklarera variablerna.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Du kan kopiera och klistra in det här exemplet till PowerShell-konsolen efter att deklarera variabler.

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Skapa den offentliga IP-adressen och sedan lägga till den andra IP-gatewaykonfigurationen

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Aktivera aktivt-aktivt läge och uppdatering av gateway

I det här steget ska du aktivera aktivt-aktivt läge och uppdatering av gateway. I det här exemplet använder just nu VPN-gateway är en äldre Standard-SKU. Aktiv-aktiv stöder dock inte Standard-SKU. Om du vill ändra storlek på den äldre SKU: N till ett som stöds (i det här fallet HighPerformance), anger du bara stöds äldre SKU: N som du vill använda.

* Du kan inte ändra en äldre SKU till en av de nya SKU: er med hjälp av det här steget. Du kan bara ändra storlek på en äldre SKU till en annan stöds äldre SKU. Exempelvis kan ändra du inte SKU: N från Standard till VpnGw1 (även om VpnGw1 stöds för aktiv-aktiv) eftersom Standard är en äldre SKU och VpnGw1 är en aktuella SKU: N. Läs mer om storleksändring och migrera SKU: er, [Gateway SKU: er](vpn-gateway-about-vpngateways.md#gwsku).

* Om du vill ändra storlek på en aktuella SKU, till exempel VpnGw1 till VpnGw3, kan du göra det med hjälp av det här steget eftersom SKU: er finns i samma SKU-familj. Om du vill göra det använder du värdet: ```-GatewaySku VpnGw3```

När du använder detta i din miljö, om du inte behöver ändra storlek på gatewayen, behöver du inte ange GatewaySku. Observera att i det här steget måste du ange gateway-objekt i PowerShell för att utlösa den faktiska uppdateringen. Den här uppdateringen kan ta 30 till 45 minuter, även om du inte ändrar storlek på din gateway.

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Ändra en aktiv-aktiv-gateway till en aktiv-standby-gateway
#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler

Ersätt följande parametrar används i exemplen med de inställningar du behöver för din egen konfiguration och sedan deklarera variablerna.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Hämta namnet på IP-konfiguration som du vill ta bort efter att deklarera variabler.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Ta bort IP-gatewaykonfiguration och inaktivera aktivt-aktivt läge

Använd det här exemplet för att ta bort IP-gatewaykonfiguration och inaktivera aktivt-aktivt läge. Observera att du ställer in gateway-objekt i PowerShell för att utlösa den faktiska uppdateringen.

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Den här uppdateringen kan ta upp till 30 till 45 minuter.

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för anvisningar.
