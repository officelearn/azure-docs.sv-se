<properties
   pageTitle="Konfigurera ExpressRoute och VPN-anslutningar för plats till plats som kan samexistera för distributionsmodellen i Resource Manager | Microsoft Azure"
   description="Den här artikeln visar hur du konfigurerar ExpressRoute och en VPN-anslutning för plats till plats som kan samexistera för Resource Manager-modellen."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/04/2016"
   ms.author="charleywen"/>

# Konfigurera ExpressRoute och samexisterande plats-till-plats-anslutningar för distributionsmodellen i Resource Manager

> [AZURE.SELECTOR]
- [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
- [PowerShell – Klassisk](expressroute-howto-coexist-classic.md)

Att kunna konfigurera VPN för plats till plats och ExpressRoute har flera fördelar. Du kan konfigurera VPN för plats till plats som en säker redundanssökväg för ExpressRoute, eller använda VPN för plats till plats för att ansluta till platser som inte är anslutna via ExpressRoute. Vi beskriver stegen för att konfigurera båda scenarierna i den här artikeln. Den här artikeln gäller distributionsmodellen i Resource Manager. Den här konfigurationen är inte tillgänglig i Azure-portalen.


**Om Azures distributionsmodeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

>[AZURE.IMPORTANT] ExpressRoute-kretsarna måste förkonfigureras innan du följer anvisningarna nedan. Kontrollera att du har följt riktlinjerna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och [konfigurera routning](expressroute-howto-routing-arm.md) innan du följer stegen nedan.

## Gränser och begränsningar

- **Överföringsroutning stöds inte:** Du kan inte skicka (via Azure) mellan ditt lokala nätverk som är anslutet via VPN för plats till plats och ditt lokala nätverk som är anslutet via ExpressRoute.
- **Tvingad tunneltrafik kan inte aktiveras för en VPN-gateway för plats till plats:** Du kan bara ”tvinga” all Internetbunden trafik till det lokala nätverket via ExpressRoute. 
- **Endast gateways med standardmässig eller hög prestanda:** Du måste använda en gateway med standardmässig eller hög prestanda för både ExpressRoute-gatewayen och VPN-gatewayen för plats till plats. Se [Gateway-SKU:er](../vpn-gateway/vpn-gateway-about-vpngateways.md) för information om gateway-SKU:er.
- **Endast routningsbaserad VPN-gateway:** Du måste använda en routningsbaserad VPN-gateway. Se [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) för information om routningsbaserad VPN-gateway.
- **Statiskt routningskrav:** Om nätverket är anslutet till både ExpressRoute och en VPN för plats till plats, måste du ha en statisk routning som konfigurerats i det lokala nätverket för att kunna dirigera din VPN-anslutning för plats till plats till Internet.
- **ExpressRoute-gateway måste konfigureras först:** Du måste skapa ExpressRoute-gatewayen först innan du lägger till en VPN-gateway för plats till plats.


## Konfigurationsdesign

### Konfigurera en VPN för plats till plats som en redundanssökväg för ExpressRoute

Du kan konfigurera en VPN-anslutning för plats till plats som en säkerhetskopia av ExpressRoute. Detta gäller endast virtuella nätverk som är länkade till Azures privata peering-sökväg. Det finns ingen VPN-baserad redundanslösning för tjänster som är tillgängliga via Azures offentliga och Microsofts peerings. ExpressRoute-kretsen är alltid den primära länken. Data flödar endast via VPN-sökvägen för plats till plats om ExpressRoute-kretsen misslyckas. 

![Samexistera](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### Konfigurera en VPN för plats till plats för att ansluta till platser som inte är anslutna via ExpressRoute

Du kan konfigurera nätverket där vissa platser ansluter direkt till Azure via VPN för plats till plats och vissa platser ansluter via ExpressRoute. 

![Samexistera](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

>[AZURE.NOTE] Det går inte att konfigurera ett virtuellt nätverk som en överföringsrouter.

## Välj vilka steg du ska använda

Det finns två uppsättningar procedurer att välja mellan för att konfigurera anslutningar som kan existera tillsammans. Vilken konfigurationsprocedur du väljer beror på om du har ett befintligt virtuellt nätverk som du vill ansluta till, eller om du vill skapa ett nytt virtuellt nätverk.


- Jag har inte något VNet och behöver skapa ett.
    
    Om du inte redan har ett virtuellt nätverk kan den här proceduren hjälpa dig att skapa ett nytt virtuellt nätverk med hjälp av distributionsmodellen i Resource Manager samt skapa nya ExpressRoute- och VPN-anslutningar för plats till plats. Om du vill konfigurera följer du stegen i artikelavsnittet [Så här skapar du ett nytt virtuellt nätverk och samtidiga anslutningar](#new).

- Jag har redan en distributionsmodell för Resource Manager i VNet.

    Du kanske redan har ett virtuellt nätverk på plats med en befintlig VPN-anslutning för plats till plats eller en ExpressRoute-anslutning. Avsnittet [Så här konfigurerar du samexisterande anslutningar för ett befintligt VNet](#add) visar hur du tar bort gatewayen och sedan skapar nya ExpressRoute- och VPN-anslutningar för plats till plats.  Observera att när du skapar nya anslutningar måste stegen utföras i en mycket specifik ordning. Följ inte anvisningarna i andra artiklar när du skapar dina gateways och anslutningar.

    I den här proceduren skapar du anslutningar som kan samexistera, vilket kräver att du tar bort din gateway och sedan konfigurerar nya gateways. Detta innebär att du har stilleståndstid för anslutningar på flera platser när du tar bort och återskapar din gateway och dina anslutningar, men du behöver inte migrera några virtuella datorer eller tjänster till ett nytt virtuellt nätverk. Dina virtuella datorer och tjänster kommer fortfarande att kunna kommunicera ut via belastningsutjämnaren när du konfigurerar din gateway, om de är konfigurerade för att göra det.


## <a name="new"></a>Så här skapar du ett nytt virtuellt nätverk och samtidiga anslutningar

Den här proceduren vägleder dig genom att skapa ett VNet samt plats-till-plats- och ExpressRoute-anslutningar som ska finnas samtidigt.
    
1. Du måste installera den senaste versionen av Azure PowerShell-cmdletarna. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](../powershell-install-configure.md). Observera att de cmdletar som du använder för den här konfigurationen kan se annorlunda ut än de du tidigare använt. Var noga med att använda de cmdletar som anges i instruktionerna.

2. Logga in på ditt konto och konfigurera miljön.
    
        login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
        $location = "Central US"
        $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location

3. Skapa ett virtuellt nätverk, inklusive gateway-undernätet. Mer information om den virtuella nätverkskonfigurationen finns i [Konfiguration av Azure Virtual Network](../virtual-network/virtual-networks-create-vnet-arm-ps.md).

    >[AZURE.IMPORTANT] Gateway-undernätet måste vara /27 eller ett kortare prefix (till exempel /26 eller /25).
    
    Skapa ett nytt VNet.

        $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 

    Lägg till undernät.

        Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

    Spara VNet-konfigurationen.

        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

4. <a name="gw"></a>Skapa en ExpressRoute-gateway. Mer information om konfiguration av ExpressRoute-gateways finns i [Konfiguration av ExpressRoute-gateway](expressroute-howto-add-gateway-resource-manager.md). GatewaySKU måste vara *Standard* eller *HighPerformance*.

        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 

5. Länka ExpressRoute-gatewayen till ExpressRoute-kretsen. När det här steget har slutförts har anslutningen mellan ditt lokala nätverk och Azure, via ExpressRoute, upprättats. Mer information om länken finns i [Länka VNets till ExpressRoute](expressroute-howto-linkvnet-arm.md).

        $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
        New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute

6. <a name="vpngw"></a>Skapa sedan din VPN-gateway för plats till plats. Mer information om konfigurationen av VPN-gateway finns i [Konfigurera en VNet till VNet-anslutning](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md). GatewaySKU måste vara *Standard* eller *HighPerformance*. VpnType måste vara *RouteBased*.

        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"

7. Skapa en lokal plats för VPN-gatewayen. Det här kommandot konfigurerar inte din lokala VPN-gateway. I stället kan du ange lokala gateway-inställningar, som t.ex. offentlig IP-adress och lokalt adressutrymme, så att Azure VPN-gatewayen kan ansluta till den. 
    >[AZURE.NOTE] Om det lokala nätverket har flera routningar kan du ange dem i en matris.  $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")  

        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix '10.100.0.0/16'

8. Konfigurera din lokala VPN-enhet till att ansluta till en ny Azure VPN-gateway. Mer information om VPN-enhetskonfiguration finns i [VPN-enhetskonfiguration](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

9. Länka VPN-gatewayen för plats till plats på Azure till den lokala gatewayen.

        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>


## <a name="add"></a>Så här konfigurerar du samexisterande anslutningar för ett befintligt VNet

Om du har ett befintligt virtuellt nätverk, kontrollerar du storleken på gateway-undernätet. Om gateway-undernätet är /28 eller /29, måste du först ta bort den virtuella nätverksgatewayen och öka storleken för gateway-undernätet. Stegen i det här avsnittet visar hur du gör.

Om gateway-undernätet är /27 eller större och det virtuella nätverket är anslutet via ExpressRoute, kan du hoppa över stegen nedan och gå vidare till [”Steg 6 – Skapa en VPN-gateway för plats till plats”](#vpngw) i föregående avsnitt.  

>[AZURE.NOTE] När du tar bort den befintliga gatewayen förlorar dina lokala platser anslutningen till ditt virtuella nätverk medan du arbetar med konfigurationen. 

1. Du måste installera den senaste versionen av Azure PowerShell-cmdletarna. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](../powershell-install-configure.md). Observera att de cmdletar som du använder för den här konfigurationen kan se annorlunda ut än de du tidigare använt. Var noga med att använda de cmdletar som anges i instruktionerna. 

2. Ta bort den befintliga ExpressRoute- eller VPN-gatewayen för plats till plats. 

        Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>

3. Ta bort gateway-undernätet. 
        
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
        Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet

4. Lägg till ett Gateway-undernät som är /27 eller större.
    >[AZURE.NOTE] Om du inte har tillräckligt med IP-adresser kvar i det virtuella nätverket för att kunna öka storleken på gateway-undernätet, måste du lägga till mer IP-adressutrymme.

        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

    Spara VNet-konfigurationen.

        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

5. Just nu har du ett VNet utan gateways. Om du vill slutföra dina anslutningar och skapa nya gateways kan du fortsätta med [Steg 4 – Skapa en ExpressRoute-gateway](#gw), som finns i den föregående uppsättningen med steg.

## Så här lägger du till punkt-till-plats-konfiguration till VPN-gateway
Du kan följa stegen nedan om du vill lägga till punkt-till-plats-konfiguration för VPN-gateway i en samexisterande inställning.

1. Lägga till VPN-klientadresspoolen. 

        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"

2. Överför VPN-rotcertifikatet till Azure för din VPN-gateway. I det här exemplet förutsätts att rotcertifikatet lagras i den lokala datorn där följande PowerShell-cmdletar körs. 

        $p2sCertFullName = "RootErVpnCoexP2S.cer"
        $p2sCertMatchName = "RootErVpnCoexP2S"
        $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
        if ($p2sCertToUpload.count -eq 1){
            write-host "cert found"
        } else {
            write-host "cert not found"
            exit
        } 
        $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

Mer information om punkt-till-plats-VPN finns i [Konfigurera en punkt-till-plats-anslutning](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## Nästa steg

Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).



<!--HONumber=jun16_HO2-->


