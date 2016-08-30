<properties
   pageTitle="Skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats med Azure Resource Manager och PowerShell | Microsoft Azure"
   description="Den här artikeln visar hur du skapar ett VNet med hjälp av Resource Manager-modellen och ansluter det till ditt lokala nätverk via en S2S VPN Gateway-anslutning."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/13/2016"
   ms.author="cherylmc"/>

# Skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats med PowerShell och Azure Resource Manager

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Klassisk Azure-portal](vpn-gateway-site-to-site-create.md)
- [PowerShell – Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Den här artikeln visar dig hur du skapar ett virtuellt nätverk och en VPN-anslutning från plats till plats till ditt lokala nätverk via Azure Resource Manager-distributionsmodellen. Plats-till-plats-anslutningar kan användas för flera platser och hybridkonfigurationer. 


**Om Azures distributionsmodeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Anslutningsdiagram 

![Plats-till-plats-diagram](./media/vpn-gateway-create-site-to-site-rm-powershell/site2site.png "site-to-site")

**Distributionsmodeller och verktyg för plats-till-plats-anslutningar**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

Om du vill koppla ihop VNets, men inte skapar någon anslutning till en lokal plats, kan du läsa mer i [Konfigurera en VNet-till-VNet-anslutning](vpn-gateway-vnet-vnet-rm-ps.md). Om du letar efter en annan typ av anslutningskonfiguration, kan du läsa mer i [Anslutningstopologier för VPN Gateway](vpn-gateway-topology.md).


## Innan du börjar

Kontrollera att du har följande innan du påbörjar konfigurationen.

- En kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Om du inte vet hur man konfigurerar VPN-enheten eller inte känner till IP-adressintervallen i din lokala nätverkskonfiguration, måste du vända dig till någon som kan ge den informationen till dig.

- En extern offentlig IP-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
    
- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
    
- Du måste installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](../powershell-install-configure.md).


## 1. Ansluta till din prenumeration 

Se till att växla till PowerShell-läget för att kunna använda Resource Manager-cmdletarna. Mer information finns i [Använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

    Login-AzureRmAccount

Kontrollera prenumerationerna för kontot.

    Get-AzureRmSubscription 

Ange den prenumeration som du vill använda.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 2. Skapa ett virtuellt nätverk och ett gateway-undernät.

Exemplen nedan visar ett gateway-undernät på /28. Även om det går att skapa ett gateway-undernät som är så litet som /29 rekommenderas det inte. Vi rekommenderar att du skapar ett gateway-undernät på /27 eller större (/26, /25 osv.) för att kunna hantera framtida funktionskrav. 

Om du redan har ett virtuellt nätverk med ett gateway-undernät som är /29 eller större, kan du gå vidare till [Lägg till din lokala nätverksgateway](#localnet).


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]  

### Så här skapar du ett virtuellt nätverk och ett gateway-undernät

Använd exemplet nedan för att skapa ett virtuellt nätverk och ett gateway-undernät. Byt ut värdena mot dina egna. 

Skapa först en resursgrupp:
    
    New-AzureRmResourceGroup -Name testrg -Location 'West US'

Skapa därefter det virtuella nätverket. Kontrollera att de adressutrymmen du anger inte överlappar några adressutrymmen som du har i det lokala nätverket.

Exemplet nedan skapar ett virtuellt nätverk med namnet *testvnet* och två undernät, där det ena kallas *GatewaySubnet* och det andra *Subnet1*. Det är viktigt att du skapar ett undernät med det specifika namnet *GatewaySubnet*. Om du ger det något annat namn misslyckas konfigurationen av anslutningen. 

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Så här lägger du till ett gateway-undernät i ett virtuellt nätverk som du redan har skapat

Det här steget krävs bara om du behöver lägga till ett gateway-undernät i ett VNet som du skapat tidigare.

Du kan skapa gateway-undernätet med hjälp av exemplet nedan. Var noga med att namnge gateway-undernätet ”GatewaySubnet”. Om du ger det något annat namn kan du skapa undernätet, men Azure behandlar det inte som ett gateway-undernät.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Nu ska du ange konfigurationen. 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3. <a name="localnet"></a>Lägg till din lokala nätverks-gateway

I ett virtuellt nätverk refererar den lokala gatewayen vanligtvis till den lokala platsen. Du måste ge platsen ett namn som Azure kan referera till och även ange adressutrymmets prefix för den lokala nätverksgatewayen. 

Azure använder det IP-adressprefix som du anger till att identifiera vilken trafik som ska skickas till den lokala platsen. Det innebär att du måste ange varje adressprefix som du vill ska kopplas till din lokala nätverksgateway. Det är enkelt att uppdatera dessa prefix om det lokala nätverket ändras. 

Observera följande när du använder PowerShell-exempel:
    
- *GatewayIPAddress* är IP-adressen till den lokala VPN-enheten. VPN-enheten får inte finnas bakom en NAT. 
- *AddressPrefix* är ditt lokala adressutrymme.

Så här lägger du till en lokal nätverksgateway med ett enda adressprefix:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Så här lägger du till en lokal nätverksgateway med flera adressprefix:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Så här ändrar du IP-adressprefixen för din lokala nätverksgateway

Ibland ändras prefixen för din lokala nätverksgateway. Vilka steg du utför för att ändra IP-adressprefixen beror på om du har skapat en VPN Gateway-anslutning eller ej. Se artikelavsnittet [Ändra IP-adressprefix för en lokal nätverksgateway](#modify).


## 4. Begär en offentlig IP-adress för VPN-gatewayen

Därefter ska du begära en offentlig IP-adress som ska allokeras till din Azure VNet VPN-gateway. Detta är inte samma IP-adress som är tilldelad till din VPN-enhet, den är i stället tilldelad till själva Azure VPN-gatewayen. Du kan inte ange den IP-adress som du vill använda. Den tilldelas dynamiskt till din gateway. Du använder denna IP-adress när du konfigurerar den lokala VPN-enheten till att ansluta till gatewayen.

Använd PowerShell-exemplet nedan. Allokeringsmetoden för den här adressen måste vara dynamisk. 

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

>[AZURE.NOTE] Azure VPN-gatewayen för Resource Manager-distributionsmodellen stöder för närvarande endast offentliga IP-adresser med hjälp av dynamisk fördelning. Detta betyder dock inte IP-adressen kan ändras. Den enda gången Azure VPN-gatewayens IP-adress ändras är när gatewayen tas bort och återskapas. Gatewayens offentliga IP-adress ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din Azure VPN-gateway.

## 5. Skapa gatewayens konfiguration av IP-adressering

Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. Använd exemplet nedan för att skapa din gateway-konfiguration. 

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6. Skapa den virtuella nätverksgatewayen

I det här steget ska du skapa den virtuella nätverksgatewayen. Observera att det kan ta tid att skapa en gateway. Ofta 20 minuter eller mer. 

Ange följande värden:

- *-GatewayType* för en plats-till-plats-konfiguration är *Vpn*. Gateway-typen gäller alltid den konfiguration som du implementerar. Andra gateway-konfigurationer kan kräva -GatewayType ExpressRoute. 

- *-VpnType* kan vara *RouteBased* (kallas en dynamisk gateway i viss dokumentation) eller *PolicyBased* (kallas en statisk gateway i viss dokumentation). Mer information om VPN Gateway-typer finns i [Om VPN Gateway](vpn-gateway-about-vpngateways.md#vpntype).
- *-GatewaySKU* kan vara *Basic*, *Standard* eller *HighPerformance*.   

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

## 7. Konfigurera din VPN-enhet

I det här läget kommer du att behöva den offentliga IP-adressen för den virtuella nätverksgatewayen för att kunna konfigurera din lokala VPN-enhet. Be din enhetstillverkare om specifik konfigurationsinformation. Se även [VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information.

Använd följande exempel för att hitta den offentliga IP-adressen till din virtuella nätverksgateway:

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8. Skapa VPN-anslutningen

Därefter skapar du VPN-anslutningen från plats till plats mellan din virtuella nätverksgateway och din VPN-enhet. Glöm inte att byta ut värdena mot dina egna. Den delade nyckeln måste överensstämma med värdet som du använde vid din konfiguration av VPN-enheten. Observera att `-ConnectionType` för plats-till-plats är *IPsec*. 

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Efter en kort stund har anslutningen upprättats. 

## 9. Verifiera en VPN-anslutning

Det finns några olika metoder för att verifiera VPN-anslutningen. Nedan beskriver vi hur du gör en grundläggande verifiering med hjälp av Azure-portalen och med hjälp av PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Så här ändrar du IP-adressprefixen för en lokal nätverksgateway

Använd anvisningarna nedan om du behöver ändra prefixen för din lokala nätverksgateway.  Det finns två uppsättningar med instruktioner. Vilken du väljer beror på om du redan har skapat din VPN Gateway-anslutning eller ej. 

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]


## Nästa steg

- När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md) för anvisningar.

- Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).




<!--HONumber=jun16_HO2-->


