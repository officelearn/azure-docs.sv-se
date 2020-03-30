---
title: 'Azure VPN Gateway: Konfigurera BGP: PowerShell'
description: I den här artikeln får du information om hur du konfigurerar BGP med Azure VPN-gateways med Azure Resource Manager och PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: 78147a96d6d9e92c2602b6a83cbed743cf2abf37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152048"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>Konfigurera BGP på Azure VPN Gateways med PowerShell
I den här artikeln beskrivs stegen för att aktivera BGP på en S2S-VPN-anslutning mellan lokala platser (S2S) och en VNet-till-VNet-anslutning med hjälp av Distributionsmodellen för Resurshanteraren och PowerShell.



## <a name="about-bgp"></a>Om BGP
BGP är ett standardroutningsprotokoll som vanligen används på Internet för att utbyta information om routning och åtkomst mellan två eller flera nätverk. BGP gör att Azure VPN Gateway och dina lokala VPN-enheter, som kallas BGP-peers eller grannar, kan utbyta ”vägar” som informerar båda gatewayerna om åtkomsten för de prefix som ska passera genom de gatewayer eller routrar som berörs. BGP kan också möjliggöra överföringsroutning mellan flera nätverk genom att sprida vägar som BGP-gatewayen får information om från en BGP-peer till alla andra BGP-peers.

Se [Översikt över BGP med Azure VPN Gateways](vpn-gateway-bgp-overview.md) för mer diskussion om fördelarna med BGP och för att förstå de tekniska kraven och övervägandena med att använda BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Komma igång med BGP på Azure VPN-gateways

I den här artikeln får du hjälp med stegen för att utföra följande uppgifter:

* [Del 1 - Aktivera BGP på din Azure VPN-gateway](#enablebgp)
* Del 2 - Upprätta en anslutning mellan lokaler med BGP
* [Del 3 - Upprätta en VNet-till-VNet-anslutning med BGP](#v2vbgp)

Varje del av instruktionerna utgör en grundläggande byggsten för att aktivera BGP i nätverksanslutningen. Om du har slutfört alla tre delarna skapar du topologin enligt följande diagram:

![BGP-topologi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Du kan kombinera delar tillsammans för att skapa ett mer komplext transitnätverk med flera hopp som uppfyller dina behov.

## <a name="part-1---configure-bgp-on-the-azure-vpn-gateway"></a><a name ="enablebgp"></a>Del 1 - Konfigurera BGP på Azure VPN Gateway
Konfigurationsstegen ställer in BGP-parametrarna för Azure VPN-gatewayen som visas i följande diagram:

![BGP-gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Innan du börjar
* Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Installera Azure Resource Manager PowerShell-cmdlets. Mer information om hur du installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview). 

### <a name="step-1---create-and-configure-vnet1"></a>Steg 1 - Skapa och konfigurera VNet1
#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler
För denna övning börjar vi med att deklarera våra variabler. I följande exempel deklareras variablerna med hjälp av värdena för den här övningen. Se till att ersätta värdena med dina egna när du konfigurerar för produktion. Du kan använda dessa variabler om du använder anvisningarna för att bekanta dig med den här typen av konfiguration. Ändra variablerna samt kopiera och klistra in dem i PowerShell-konsolen.

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
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
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Anslut till din prenumeration och skapa en ny resursgrupp
Om du vill använda cmdlets i Resource Manager kontrollerar du att du växlar till PowerShell-läge. Mer information finns i [Använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Skapa TestVNet1
I följande exempel skapas ett virtuellt nätverk med namnet TestVNet1 och tre undernät, ett som kallas GatewaySubnet, ett som kallas FrontEnd och ett som kallas Backend. När du ersätter värden är det viktigt att du alltid namnger gateway-undernätet specifikt till GatewaySubnet. Om du ger det något annat namn går det inte att skapa gatewayen.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Steg 2 - Skapa VPN Gateway för TestVNet1 med BGP-parametrar
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Skapa IP- och undernätskonfigurationer
Begär en offentlig IP-adress som ska allokeras till den gateway som du ska skapa för det virtuella nätverket. Du definierar också de nödvändiga undernäts- och IP-konfigurationerna.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Skapa VPN-gatewayen med AS-numret
Skapa den virtuella nätverksgatewayen för TestVNet1. BGP kräver en ruttbaserad VPN-gateway och även tilläggsparametern -Asn för att ställa in ASN (ASN)för TestVNet1. Om du inte anger ASN-parametern tilldelas ASN 65515. Att skapa en gateway kan ta ett tag (30 minuter eller mer att slutföra).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Skaffa Azure BGP Peer IP-adress
När gatewayen har skapats måste du hämta BGP Peer IP-adressen på Azure VPN Gateway. Den här adressen behövs för att konfigurera Azure VPN Gateway som en BGP-peer för dina lokala VPN-enheter.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

Det sista kommandot visar motsvarande BGP-konfigurationer på Azure VPN Gateway. till exempel:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

När gatewayen har skapats kan du använda den här gatewayen för att upprätta anslutning mellan lokala anslutningar eller VNet-till-VNet-anslutning med BGP. Följande avsnitt går igenom stegen för att slutföra övningen.

## <a name="part-2---establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembbgp"></a>Del 2 - Upprätta en anslutning mellan lokaler med BGP

Om du vill upprätta en anslutning över flera lokaler måste du skapa en port för lokalt nätverk för att representera din lokala VPN-enhet och en anslutning för att ansluta VPN-gatewayen till den lokala nätverksgatewayen. Det finns artiklar som går igenom de här stegen, men den här artikeln innehåller ytterligare egenskaper som krävs för att ange BGP-konfigurationsparametrarna.

![BGP för korslokaler](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Innan du fortsätter, se till att du har slutfört [del 1](#enablebgp) av denna övning.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Steg 1 - Skapa och konfigurera den lokala nätverksgatewayen

#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler

Den här övningen fortsätter att skapa konfigurationen som visas i diagrammet. Ersätt värdena med de som du vill använda för din konfiguration.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Ett par saker att notera om de lokala parametrarna för nätverksgateway:

* Den lokala nätverksgatewayen kan finnas i samma eller olika plats- och resursgrupp som VPN-gatewayen. I det här exemplet visas dem i olika resursgrupper på olika platser.
* Prefixet som du måste deklarera för den lokala nätverksgatewayen är värdadressen för din BGP Peer IP-adress på din VPN-enhet. I det här fallet är det ett /32 prefix på "10.52.255.254/32".
* Som en påminnelse måste du använda olika BGP ASN mellan dina lokala nätverk och Azure VNet. Om de är desamma måste du ändra ditt virtuella nätverks-ASN om din lokala VPN-enhet redan använder ASN för att peer med andra BGP-grannar.

Innan du fortsätter kontrollerar du att du fortfarande är ansluten till Prenumeration 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Skapa den lokala nätverksgatewayen för Site5

Var noga med att skapa resursgruppen om den inte skapas innan du skapar den lokala nätverksgatewayen. Lägg märke till de två ytterligare parametrarna för den lokala nätverksgatewayen: Asn och BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Steg 2 – Anslut VNet-gatewayen och den lokala nätverksgatewayen

#### <a name="1-get-the-two-gateways"></a>1. Få de två gateways

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Skapa TestVNet1 till Site5-anslutningen

I det här steget skapar du anslutningen från TestVNet1 till Site5. Du måste ange "-EnableBGP $True" för att aktivera BGP för den här anslutningen. Som diskuterats tidigare är det möjligt att ha både BGP- och icke-BGP-anslutningar för samma Azure VPN Gateway. Om inte BGP är aktiverat i anslutningsegenskapen aktiverar Azure inte BGP för den här anslutningen även om BGP-parametrar redan är konfigurerade på båda gatewayerna.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

I följande exempel visas de parametrar som du anger i avsnittet BGP-konfiguration på din lokala VPN-enhet för den här övningen:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Anslutningen upprättas efter några minuter och BGP-peering-sessionen startar när IPsec-anslutningen har upprättats.

## <a name="part-3---establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Del 3 - Upprätta en VNet-till-VNet-anslutning med BGP

I det här avsnittet läggs en VNet-till-VNet-anslutning till BGP, som visas i följande diagram:

![BGP för VNet-till-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Följande instruktioner fortsätter från föregående steg. Du måste slutföra [del I](#enablebgp) för att skapa och konfigurera TestVNet1 och VPN Gateway med BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Steg 1 - Skapa TestVNet2 och VPN-gatewayen

Det är viktigt att se till att IP-adressutrymmet för det nya virtuella nätverket, TestVNet2, inte överlappar något av dina virtuella nätverksintervall.

I det här exemplet tillhör de virtuella nätverken samma prenumeration. Du kan ställa in VNet-till-VNet-anslutningar mellan olika prenumerationer. Mer information finns i [Konfigurera en VNet-till-VNet-anslutning](vpn-gateway-vnet-vnet-rm-ps.md). Se till att du lägger till "-EnableBgp $True" när du skapar anslutningar för att aktivera BGP.

#### <a name="1-declare-your-variables"></a>1. Deklarera dina variabler

Ersätt värdena med de som du vill använda för din konfiguration.

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
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
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
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

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Skapa VPN-gatewayen för TestVNet2 med BGP-parametrar

Begär att en offentlig IP-adress ska allokeras till den gateway som du skapar för ditt virtuella nätverk och definierar de nödvändiga undernäts- och IP-konfigurationerna.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Skapa VPN-gatewayen med AS-numret. Du måste åsidosätta standard-ASN på dina Azure VPN-gateways. ASN för anslutna virtuella nätverk måste vara olika för att aktivera BGP och transitroutning.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
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

När du har slutfört dessa steg upprättas anslutningen efter några minuter. BGP-peering-sessionen är uppe när VNet-till-VNet-anslutningen är klar.

Om du har slutfört alla tre delarna av den här övningen har du upprättat följande nätverkstopologi:

![BGP för VNet-till-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för anvisningar.
