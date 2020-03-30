---
title: Konfigurera aktiva S2S Azure VPN Gateway-anslutningar
description: I den här artikeln beskrivs aktivaktiva anslutningar med Azure VPN Gateways med Azure Resource Manager och PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: ec3697208434eb971e47136416f2c2cc541b5cea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244646"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Konfigurera aktiva S2S VPN-anslutningar med Azure VPN Gateways

I den här artikeln får du lära dig stegen för att skapa aktiva korslokaliserade och VNet-till-VNet-anslutningar med hjälp av Resurshanterarens distributionsmodell och PowerShell.



## <a name="about-highly-available-cross-premises-connections"></a>Om högtillgängade anslutningsförbindelser mellan lokala
För att uppnå hög tillgänglighet för anslutning mellan lokala och virtuella nätverk bör du distribuera flera VPN-gateways och upprätta flera parallella anslutningar mellan dina nätverk och Azure. Se [Högtillgänglig cross-premises och VNet-to-VNet-anslutning](vpn-gateway-highlyavailable.md) för en översikt över anslutningsalternativ och topologi.

Den här artikeln innehåller instruktioner för att konfigurera en aktiv cross-local VPN-anslutning och aktiv-aktiv anslutning mellan två virtuella nätverk.

* [Del 1 - Skapa och konfigurera din Azure VPN-gateway i aktivt aktivt läge](#aagateway)
* [Del 2 - Upprätta aktiva gränsöverskridande anslutningar](#aacrossprem)
* [Del 3 - Upprätta aktiva VNet-till-VNet-anslutningar](#aav2v)

Om du redan har en VPN-gateway kan du:
* [Uppdatera en befintlig VPN-gateway från aktiv vänteläge till aktiv aktiv eller vice versa](#aaupdate)

Du kan kombinera dessa tillsammans för att skapa en mer komplex, högtillgänge om nätverkstopologi som uppfyller dina behov.

> [!IMPORTANT]
> Aktivt läge använder endast följande SKU:er: 
>   * VpnGw1 VpnGw2, VpnGw3
>   * HighPerformance (för gamla äldre SKU:er)

## <a name="part-1---create-and-configure-active-active-vpn-gateways"></a><a name ="aagateway"></a>Del 1 - Skapa och konfigurera aktiva VPN-gateways
Följande steg konfigurerar din Azure VPN-gateway i aktiva-aktiva lägen. De viktigaste skillnaderna mellan de aktiva och aktiva reservgatewayerna:

* Du måste skapa två GATEWAY IP-konfigurationer med två offentliga IP-adresser
* Du måste ställa in flaggan EnableActiveActiveFeature
* Gateway SKU måste vara VpnGw1, VpnGw2, VpnGw3 eller HighPerformance (äldre SKU).

De andra egenskaperna är desamma som de icke-aktiva aktiva gatewayerna. 

### <a name="before-you-begin"></a>Innan du börjar
* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Du måste installera Azure Resource Managers PowerShell-cmdletar. Mer information om hur du installerar PowerShell-cmdlets finns i [översikt över Azure PowerShell.](/powershell/azure/overview)

### <a name="step-1---create-and-configure-vnet1"></a>Steg 1 - Skapa och konfigurera VNet1
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

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Steg 2 - Skapa VPN-gatewayen för TestVNet1 med aktivt aktivt läge
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Skapa offentliga IP-adresser och gateway IP-konfigurationer
Begär att två offentliga IP-adresser ska tilldelas den gateway som du skapar för ditt virtuella nätverk. Du definierar också de undernäts- och IP-konfigurationer som krävs.

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Skapa VPN-gatewayen med aktiv aktiv konfiguration
Skapa den virtuella nätverksgatewayen för TestVNet1. Observera att det finns två GatewayIpConfig-poster och flaggan EnableActiveActiveFeature är inställd. Det kan ta en stund att skapa en gateway (45 minuter eller mer).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Skaffa den offentliga GATEWAY-IP-adressen och BGP Peer IP-adressen
När gatewayen har skapats måste du hämta BGP Peer IP-adressen på Azure VPN Gateway. Den här adressen behövs för att konfigurera Azure VPN Gateway som en BGP-peer för dina lokala VPN-enheter.

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Använd följande cmdlets för att visa de två offentliga IP-adresser som tilldelats för din VPN-gateway och deras motsvarande BGP Peer IP-adresser för varje gateway-instans:

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

Ordningen på de offentliga IP-adresserna för gatewayinstanserna och motsvarande BGP-peeringadresser är desamma. I det här exemplet använder gateway-datorn med offentlig IP på 40.112.190.5 10.12.255.4 som BGP-peering-adress och gatewayen med 138.91.156.129 använder 10.12.255.5. Den här informationen behövs när du konfigurerar dina lokala VPN-enheter som ansluter till den aktiva gatewayen. Gatewayen visas i diagrammet nedan med alla adresser:

![aktiv aktiv gateway](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

När gatewayen har skapats kan du använda den här gatewayen för att upprätta aktiv-aktiv korslokal eller VNet-till-VNet-anslutning. Följande avsnitt går igenom stegen för att slutföra övningen.

## <a name="part-2---establish-an-active-active-cross-premises-connection"></a><a name ="aacrossprem"></a>Del 2 - Upprätta en aktiv-aktiv anslutning mellan lokaler
Om du vill upprätta en anslutning över flera lokaler måste du skapa en port för lokalt nätverk för att representera din lokala VPN-enhet och en anslutning för att ansluta Azure VPN-gatewayen med den lokala nätverksgatewayen. I det här exemplet är Azure VPN-gatewayen i aktivt aktivt läge. Även om det bara finns en lokal VPN-enhet (lokal nätverksgateway) och en anslutningsresurs, upprättar båda Azure VPN-gatewayinstanserna S2S VPN-tunnlar med den lokala enheten.

Innan du fortsätter, se till att du har slutfört [del 1](#aagateway) av denna övning.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Steg 1 - Skapa och konfigurera den lokala nätverksgatewayen
#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler
Den här övningen fortsätter att skapa konfigurationen som visas i diagrammet. Ersätt värdena med de som du vill använda för din konfiguration.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Ett par saker att notera om de lokala parametrarna för nätverksgateway:

* Den lokala nätverksgatewayen kan finnas i samma eller olika plats- och resursgrupp som VPN-gatewayen. Det här exemplet visar dem i olika resursgrupper men på samma Azure-plats.
* Om det bara finns en lokal VPN-enhet som visas ovan kan den aktiva aktiva anslutningen fungera med eller utan BGP-protokoll. I det här exemplet används BGP för anslutning mellan lokala.
* Om BGP är aktiverat är prefixet som du måste deklarera för den lokala nätverksgatewayen värdadressen för din BGP Peer IP-adress på din VPN-enhet. I det här fallet är det ett /32 prefix på "10.52.255.253/32".
* Som en påminnelse måste du använda olika BGP ASN mellan dina lokala nätverk och Azure VNet. Om de är desamma måste du ändra ditt virtuella nätverks-ASN om din lokala VPN-enhet redan använder ASN för att peer med andra BGP-grannar.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Skapa den lokala nätverksgatewayen för Site5
Innan du fortsätter kontrollerar du att du fortfarande är ansluten till Prenumeration 1. Skapa resursgruppen om den ännu inte har skapats.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Steg 2 – Anslut VNet-gatewayen och den lokala nätverksgatewayen
#### <a name="1-get-the-two-gateways"></a>1. Få de två gateways

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Skapa TestVNet1 till Site5-anslutningen
I det här steget skapar du anslutningen från TestVNet1 till Site5_1 med "EnableBGP" inställd på $True.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN- och BGP-parametrar för din lokala VPN-enhet
I exemplet nedan visas de parametrar som du kommer att ange i avsnittet BGP-konfiguration på din lokala VPN-enhet för den här övningen:

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

Anslutningen bör upprättas efter några minuter och BGP-peering-sessionen startar när IPsec-anslutningen har upprättats. Det här exemplet hittills har konfigurerat endast en lokal VPN-enhet, vilket resulterar i diagrammet som visas nedan:

![aktiv-aktiv-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Steg 3 - Anslut två lokala VPN-enheter till den aktiva VPN-gatewayen
Om du har två VPN-enheter i samma lokala nätverk kan du uppnå dubbel redundans genom att ansluta Azure VPN-gatewayen till den andra VPN-enheten.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Skapa den andra lokala nätverksgatewayen för Site5
Gateway-IP-adressen, adressprefixet och BGP-peering-adressen för den andra lokala nätverksgatewayen får inte överlappa den tidigare lokala nätverksgatewayen för samma lokala nätverk.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Anslut VNet-gatewayen och den andra lokala nätverksgatewayen
Skapa anslutningen från TestVNet1 till Site5_2 med "EnableBGP" inställt på $True

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. VPN- och BGP-parametrar för din andra lokala VPN-enhet
På samma sätt listar nedan de parametrar som du kommer att ange i den andra VPN-enheten:

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

När anslutningen (tunnlar) har upprättats har du dubbla redundanta VPN-enheter och tunnlar som ansluter ditt lokala nätverk och Azure:

![dubbel redundans-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name="part-3---establish-an-active-active-vnet-to-vnet-connection"></a><a name ="aav2v"></a>Del 3 - Upprätta en aktiv VNet-till-VNet-anslutning
I det här avsnittet skapas en aktiv VNet-till-VNet-anslutning med BGP. 

Anvisningarna nedan fortsätter från föregående steg ovan. Du måste fylla [i del 1](#aagateway) för att skapa och konfigurera TestVNet1 och VPN Gateway med BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Steg 1 - Skapa TestVNet2 och VPN-gatewayen
Det är viktigt att se till att IP-adressutrymmet för det nya virtuella nätverket, TestVNet2, inte överlappar något av dina virtuella nätverksintervall.

I det här exemplet tillhör de virtuella nätverken samma prenumeration. Du kan ställa in VNet-till-VNet-anslutningar mellan olika prenumerationer. Se [Konfigurera en VNet-till-VNet-anslutning](vpn-gateway-vnet-vnet-rm-ps.md) om du vill veta mer. Se till att du lägger till "-EnableBgp $True" när du skapar anslutningar för att aktivera BGP.

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

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Skapa den aktiva VPN-gatewayen för TestVNet2
Begär att två offentliga IP-adresser ska tilldelas den gateway som du skapar för ditt virtuella nätverk. Du definierar också de undernäts- och IP-konfigurationer som krävs.

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Skapa VPN-gatewayen med AS-numret och flaggan "EnableActiveActiveFeature". Observera att du måste åsidosätta standard-ASN på dina Azure VPN-gateways. ASN för anslutna virtuella nätverk måste vara olika för att aktivera BGP och transitroutning.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Steg 2 - Anslut testVNet1- och TestVNet2-gatewayerna
I det här exemplet finns båda gatewayerna i samma prenumeration. Du kan slutföra det här steget i samma PowerShell-session.

#### <a name="1-get-both-gateways"></a>1. Få båda gateways
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
> Var noga med att aktivera BGP för båda anslutningarna.
> 
> 

När du har slutfört de här stegen upprättas anslutningen inom några minuter och BGP-peering-sessionen kommer att vara uppe när VNet-till-VNet-anslutningen har slutförts med dubbel redundans:

![aktiv-aktiv-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name="update-an-existing-vpn-gateway"></a><a name ="aaupdate"></a>Uppdatera en befintlig VPN-gateway

Det här avsnittet hjälper dig att ändra en befintlig Azure VPN-gateway från aktiv vänteläge till aktivt aktivt läge eller vice versa.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Ändra en gateway med aktiv vänteläge till en aktiv-aktiv gateway

I följande exempel konverteras en gateway med aktiv vänteläge till en aktiv gateway. När du ändrar en aktiv reservgateway till aktiv aktiv skapar du en annan offentlig IP-adress och lägger sedan till en andra GATEWAY IP-konfiguration.

#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler

Ersätt följande parametrar som används för exemplen med de inställningar som du behöver för din egen konfiguration och deklarera sedan dessa variabler.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

När du har deklarerat variablerna kan du kopiera och klistra in det här exemplet på PowerShell-konsolen.

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Skapa den offentliga IP-adressen och lägg sedan till den andra gateway-IP-konfigurationen

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Aktivera aktivt aktivt läge och uppdatera gatewayen

I det här steget aktiverar du aktivt aktivt läge och uppdaterar gatewayen. I exemplet använder VPN-gatewayen för närvarande en äldre Standard SKU. Aktiv aktiv stöder dock inte standard-SKU. Om du vill ändra storlek på den äldre SKU-gruppen till en som stöds (i det här fallet HighPerformance) anger du helt enkelt den äldre SKU som stöds som du vill använda.

* Du kan inte ändra en äldre SKU till en av de nya SKU:erna med det här steget. Du kan bara ändra storlek på en äldre SKU till en annan äldre SKU som stöds. Du kan till exempel inte ändra SKU från Standard till VpnGw1 (även om VpnGw1 stöds för aktiv aktiv) eftersom Standard är en äldre SKU och VpnGw1 är en aktuell SKU. Mer information om hur du ändrar storlek på och migrerar SKU:er finns i [SKU:er för gateway](vpn-gateway-about-vpngateways.md#gwsku).

* Om du vill ändra storlek på en aktuell SKU, till exempel VpnGw1 till VpnGw3, kan du göra det med det här steget eftersom SKU:erna finns i samma SKU-familj. För att göra det använder du värdet:```-GatewaySku VpnGw3```

När du använder detta i din miljö, om du inte behöver ändra storlek på gatewayen, behöver du inte ange -GatewaySku. Observera att du i det här steget måste ange att gateway-objektet i PowerShell ska utlösa den faktiska uppdateringen. Den här uppdateringen kan ta 30 till 45 minuter, även om du inte ändrar storlek på gatewayen.

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Ändra en aktiv aktiv gateway till en gateway med aktiv vänteläge
#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler

Ersätt följande parametrar som används för exemplen med de inställningar som du behöver för din egen konfiguration och deklarera sedan dessa variabler.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

När du har deklarerat variablerna hämtar du namnet på den IP-konfiguration som du vill ta bort.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Ta bort gateway-IP-konfigurationen och inaktivera aktivt aktivt läge

Använd det här exemplet om du vill ta bort gateway-IP-konfigurationen och inaktivera aktivt aktivt läge. Observera att du måste ange att gateway-objektet i PowerShell ska utlösa den faktiska uppdateringen.

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Den här uppdateringen kan ta upp till 30 till 45 minuter.

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för anvisningar.
