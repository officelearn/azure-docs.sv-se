---
title: "Skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats med Azure Resource Manager och PowerShell | Microsoft Docs"
description: "Den här artikeln visar hur du skapar ett VNet med hjälp av Resource Manager-distributionen och ansluter det till ditt lokala nätverk via en S2S VPN Gateway-anslutning."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: e2c1a99a23f8d2d8ceb1fd8a9fe3c0e86abc0b02


---
# <a name="create-a-vnet-with-a-site-to-site-connection-using-powershell"></a>Skapa ett VNet med en plats-till-plats-anslutning med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Klassisk – Klassisk portal](vpn-gateway-site-to-site-create.md)
>
>

Den här artikeln visar dig hur du skapar ett virtuellt nätverk och en VPN-gateway från plats till plats till ditt lokala nätverk via Azure Resource Manager-distributionsmodellen. Plats-till-plats-anslutningar kan användas för flera platser och hybridkonfigurationer.

![Plats-till-plats-diagram](./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "site-to-site")

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Distributionsmodeller och metoder för plats-till-plats-anslutningar
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Följande tabell visar de tillgängliga distributionsmodellerna och -metoderna för plats-till-plats-konfigurationer. När det finns en artikel med konfigurationssteg tillgänglig länkar vi till den direkt från tabellen.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Ytterligare konfigurationer
Om du vill koppla ihop VNets, men inte skapar någon anslutning till en lokal plats, kan du läsa mer i [Konfigurera en VNet-till-VNet-anslutning](vpn-gateway-vnet-vnet-rm-ps.md). Information om att lägga till en plats-till-plats-anslutning till en VNet som redan har en anslutning finns i [Lägga till en S2S-anslutning till en VNet med en befintlig anslutning för VPN-gateway](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
Kontrollera att du har följande innan du påbörjar konfigurationen.

* En kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Om du inte vet hur man konfigurerar VPN-enheten eller inte känner till IP-adressintervallen i din lokala nätverkskonfiguration måste du vända dig till någon som kan ge den informationen till dig.
* En extern offentlig IP-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).
* Den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="a-namelogina1-connect-to-your-subscription"></a><a name="Login"></a>1. Ansluta till din prenumeration
Se till att växla till PowerShell-läget för att kunna använda Resource Manager-cmdletarna. Mer information finns i [Använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

    Login-AzureRmAccount

Kontrollera prenumerationerna för kontot.

    Get-AzureRmSubscription

Ange den prenumeration som du vill använda.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="a-namevneta2-create-a-virtual-network-and-a-gateway-subnet"></a><a name="VNet"></a>2. Skapa ett virtuellt nätverk och ett gateway-undernät.
I exemplen används ett gateway-undernät på /28. Även om det är möjligt att skapa ett gateway-subnät som är så litet som /29 så rekommenderar vi att du skapar ett större subnät som inkluderar fler adresser genom att välja minst /28 eller /27. Det tillåter tillräckligt med adresser för att rymma möjliga övriga konfigurationer som du kan behöva i framtiden.

Om du redan har ett virtuellt nätverk med ett gateway-undernät som är /29 eller större, kan du gå vidare till [Lägg till din lokala nätverksgateway](#localnet).

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Så här skapar du ett virtuellt nätverk och ett gateway-undernät
Använd följande exempel för att skapa ett virtuellt nätverk och ett gateway-undernät. Byt ut värdena mot dina egna.

Skapa först en resursgrupp:

    New-AzureRmResourceGroup -Name testrg -Location 'West US'

Skapa därefter det virtuella nätverket. Kontrollera att de adressutrymmen du anger inte överlappar några adressutrymmen som du har i det lokala nätverket.

Följande exempel skapar ett virtuellt nätverk med namnet *testvnet* och två undernät, där det ena kallas *GatewaySubnet* och det andra *Subnet1*. Det är viktigt att du skapar ett undernät med det specifika namnet *GatewaySubnet*. Om du ger det något annat namn misslyckas konfigurationen av anslutningen.

Ange variablerna.

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

Skapa VNet.

    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
    -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="a-namegatewaysubnetato-add-a-gateway-subnet-to-a-virtual-network-you-have-already-created"></a><a name="gatewaysubnet"></a>Så här lägger du till ett gateway-undernät i ett virtuellt nätverk som du redan har skapat
Det här steget krävs bara om du behöver lägga till ett gateway-undernät i ett VNet som du skapat tidigare.

Du kan skapa gateway-undernätet med hjälp av följande exempel. Var noga med att namnge gateway-undernätet ”GatewaySubnet”. Om du ger det något annat namn kan du skapa undernätet, men Azure behandlar det inte som ett gateway-undernät.

Ange variablerna.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

Skapa gateway-undernätet.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Ange konfigurationen.

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## <a name="3-a-namelocalnetaadd-your-local-network-gateway"></a>3. <a name="localnet"></a>Lägg till din lokala nätverksgateway
I ett virtuellt nätverk refererar den lokala gatewayen vanligtvis till den lokala platsen. Du ger platsen ett namn som Azure kan referera till och även ange adressutrymmets prefix för den lokala nätverksgatewayen.

Azure använder det IP-adressprefix som du anger till att identifiera vilken trafik som ska skickas till den lokala platsen. Det innebär att du måste ange varje adressprefix som du vill ska kopplas till din lokala nätverksgateway. Det är enkelt att uppdatera dessa prefix om det lokala nätverket ändras.

Observera följande när du använder PowerShell-exempel:

* *GatewayIPAddress* är IP-adressen till den lokala VPN-enheten. VPN-enheten får inte finnas bakom en NAT.
* *AddressPrefix* är ditt lokala adressutrymme.

Så här lägger du till en lokal nätverksgateway med ett enda adressprefix:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Så här lägger du till en lokal nätverksgateway med flera adressprefix:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>Så här ändrar du IP-adressprefixen för din lokala nätverksgateway
Ibland ändras prefixen för din lokala nätverksgateway. Vilka steg du utför för att ändra IP-adressprefixen beror på om du har skapat en VPN Gateway-anslutning. Se artikelavsnittet [Ändra IP-adressprefix för en lokal nätverksgateway](#modify).

## <a name="a-namepublicipa4-request-a-public-ip-address-for-the-vpn-gateway"></a><a name="PublicIP"></a>4. Begär en offentlig IP-adress för VPN-gatewayen
Därefter begär du en offentlig IP-adress som ska allokeras till din Azure VNet VPN-gateway. Detta är inte samma IP-adress som är tilldelad till din VPN-enhet, den är i stället tilldelad till själva Azure VPN-gatewayen. Du kan inte ange den IP-adress som du vill använda. Den allokeras dynamiskt till din gateway. Du använder denna IP-adress när du konfigurerar den lokala VPN-enheten till att ansluta till gatewayen.

Azure VPN-gatewayen för Resource Manager-distributionsmodellen stöder för närvarande endast offentliga IP-adresser med hjälp av dynamisk fördelning. Detta betyder dock inte IP-adressen kan ändras. Den enda gången Azure VPN-gatewayens IP-adress ändras är när gatewayen tas bort och återskapas. Gatewayens offentliga IP-adress ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din Azure VPN-gateway.

Använd följande PowerShell-exempel:

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## <a name="a-namegatewayipconfiga5-create-the-gateway-ip-addressing-configuration"></a><a name="GatewayIPConfig"></a>5. Skapa gatewayens konfiguration av IP-adressering
Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. Använd följande exempel för att skapa din gateway-konfiguration.

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id

## <a name="a-namecreategatewaya6-create-the-virtual-network-gateway"></a><a name="CreateGateway"></a>6. Skapa den virtuella nätverksgatewayen
I det här steget skapar du den virtuella nätverksgatewayen. Det kan ta tid att skapa en gateway. Ofta 45 minuter eller mer.

Ange följande värden:

* *-GatewayType* för en plats-till-plats-konfiguration är *Vpn*. Gateway-typen gäller alltid den konfiguration som du implementerar. Andra gateway-konfigurationer kan kräva -GatewayType ExpressRoute.
* *-VpnType* kan vara *RouteBased* (kallas en dynamisk gateway i viss dokumentation) eller *PolicyBased* (kallas en statisk gateway i viss dokumentation). Mer information om VPN-gatewaytyper finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).
* *-GatewaySKU* kan vara *Basic*, *Standard* eller *HighPerformance*.     

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
        -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

## <a name="a-nameconfigurevpndevicea7-configure-your-vpn-device"></a><a name="ConfigureVPNDevice"></a>7. Konfigurera din VPN-enhet
I det här läget behöver du den offentliga IP-adressen för den virtuella nätverksgatewayen för att kunna konfigurera din lokala VPN-enhet. Be din enhetstillverkare om specifik konfigurationsinformation. Se [VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information.

Använd följande exempel för att hitta den offentliga IP-adressen till din virtuella nätverksgateway:

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## <a name="a-namecreateconnectiona8-create-the-vpn-connection"></a><a name="CreateConnection"></a>8. Skapa VPN-anslutningen
Därefter skapar du VPN-anslutningen från plats till plats mellan din virtuella nätverksgateway och din VPN-enhet. Glöm inte att byta ut värdena mot dina egna. Den delade nyckeln måste överensstämma med värdet som du använde vid din konfiguration av VPN-enheten. Observera att `-ConnectionType` för plats-till-plats är *IPsec*.

Ange variablerna.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

Skapa anslutningen.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Efter en kort stund har anslutningen upprättats.

## <a name="a-nametoverifyato-verify-a-vpn-connection"></a><a name="toverify"></a>Så här verifierar du en VPN-anslutning
Det finns några olika metoder för att verifiera VPN-anslutningen.

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="a-namemodifyato-modify-ip-address-prefixes-for-a-local-network-gateway"></a><a name="modify"></a>Så här ändrar du IP-adressprefixen för en lokal nätverksgateway
Använd följande anvisningar om du behöver ändra prefixen för din lokala nätverksgateway. Det finns två uppsättningar med instruktioner. Vilken du väljer beror på om du redan har skapat din gateway-anslutning.

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="a-namemodifygwipaddressato-modify-the-gateway-ip-address-for-a-local-network-gateway"></a><a name="modifygwipaddress"></a>Så här ändrar du gateway-IP-adressen för en lokal nätverksgateway
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Nästa steg
*  När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).



<!--HONumber=Dec16_HO1-->


