<properties 
   pageTitle="Om VPN Gateway| Microsoft Azure"
   description="Läs mer om VPN Gateway för Azure Virtual Network."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# Om VPN Gateway

VPN Gateway används för att skicka nätverkstrafik mellan virtuella nätverk och lokala platser. Den används också för att skicka trafik mellan flera virtuella nätverk i Azure (VNet till VNet). I avsnitten nedan beskrivs de objekt som är relaterade till VPN Gateway.

Vilka anvisningar du använder för att skapa din VPN-gateway beror på vilken distributionsmodell som användes för att skapa det virtuella nätverket. Om du exempelvis har skapat ditt VNet med den klassiska distributionsmodellen, ska du använda riktlinjer och instruktioner för den modellen när du skapar och konfigurerar din VPN-gateway. Du kan inte skapa en VPN-gateway i Resource Manager för ett virtuellt nätverk med den klassiska distributionsmodellen. 

Mer information om distributionsmodellerna finns i [Förstå Resource Manager- och klassiska distributionsmodeller](../resource-manager-deployment-model.md).


## <a name="gwsub"></a>Gateway-undernät 

Om du vill konfigurera en VPN-gateway, måste du först skapa ett gateway-undernät för ditt VNet. Gateway-undernätet måste ha namnet *GatewaySubnet* för att fungera korrekt. Med det här namnet vet Azure att undernätet ska användas för gatewayen.<BR>Om du använder den klassiska portalen namnges gateway-undernätet automatiskt med namnet *Gateway* i portalgränssnittet. Det här gäller endast visning av gateway-undernätet i den klassiska portalen. I det här fallet har undernätet faktiskt skapats i Azure som *GatewaySubnet* och kan visas på det här sättet i Azure-portalen och i PowerShell.

Den minsta storleken för gateway-undernätet beror helt på den konfiguration som du vill skapa. Även om det är möjligt att skapa ett gateway-undernät som är så litet som /29 för vissa konfigurationer, rekommenderar vi att du skapar ett gateway-undernät på /28 eller större (/28, /27, /26, etc.). 

Genom att skapa en större gateway undviker du att råka ut för storleksbegränsningar i gatewayen. Om du exempelvis har skapat en gateway med en gateway-undernätsstorlek på /29 och du vill konfigurera en samexisterande konfiguration med plats-till-plats/ExpressRoute, skulle du behöva ta bort gatewayen, ta bort gateway-undernätet, skapa gateway-undernätet som en /28 eller större och sedan återskapa gatewayen. 

Genom att skapa ett gateway-undernät som är större redan från början, sparar du tid senare när du lägger till nya konfigurationsfunktioner till din nätverksmiljö. 

Exemplet nedan visar ett gateway-undernät med namnet GatewaySubnet. Du kan se att CIDR-noteringen anger en /27, vilket ger tillräckligt med IP-adresser för de flesta konfigurationer som finns just nu.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Kontrollera att GatewaySubnet inte har någon nätverkssäkerhetsgrupp (NSG), eftersom detta kan medföra att anslutningar misslyckas.

## <a name="gwtype"></a>Gateway-typer

Gateway-typen anger hur gatewayen ansluter och är en obligatorisk konfigurationsinställning i Resource Manager-distributionsmodellen. Blanda inte ihop gateway-typen med VPN-typen, som anger vilken typ av routning din VPN har. De tillgängliga värdena för `-GatewayType` är: 

- Vpn
- ExpressRoute


I det här exemplet för Resource Manager-distributionsmodellen anges -GatewayType som *Vpn*. När du skapar en gateway måste du kontrollera att gateway-typen är rätt för din konfiguration. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="gwsku"></a>Gateway-SKU:er

När du skapar en VPN-gateway måste du ange vilken gateway-SKU som du vill använda. Det finns 3 SKU:er för VPN Gateway:

- Basic
- Standard
- HighPerformance

I exemplet nedan anges `-GatewaySku` som *Standard*.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard -GatewayType Vpn -VpnType RouteBased

###  <a name="aggthroughput"></a>Beräknat aggregerat dataflöde av SKU och gateway-typ


Tabellen nedan visar gateway-typerna och beräknat aggregerat dataflöde. Prissättningen skiljer sig åt mellan gateway-SKU:er. Information om prissättning finns i [Prissättning för VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/). Tabellen gäller både för Resource Manager- och den klassiska distributionsmodellen.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

## <a name="vpntype"></a>VPN-typer

Varje konfiguration kräver en viss typ av VPN för att fungera. Om du kombinerar två konfigurationer, till exempel om du skapar en plats-till-plats-anslutning och en punkt-till-plats-anslutning till samma VNet, måste du använda en VPN-typ som uppfyller båda anslutningskraven. 

Vid samexisterande punkt-till-plats- och plats-till-plats-anslutningar måste du använda en routningsbaserad VPN-typ när du arbetar med Azure Resource Manager-distributionsmodellen, eller en dynamisk gateway om du arbetar i det klassiska distributionsläget.

När du skapar din konfiguration väljer du den VPN-typ som krävs för anslutningen. 

Det finns två typer av VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

I det här exemplet för Resource Manager-distributionsmodellen anges `-VpnType` som *RouteBased*. När du skapar en gateway måste du kontrollera att -VpnType är rätt för din konfiguration. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="connectiontype"></a>Anslutningstyper

Varje konfiguration kräver en viss anslutningstyp. Tillgängliga PowerShell-värden i Resource Manager för `-ConnectionType` är:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

I exemplet nedan skapar vi en plats-till-plats-anslutning som kräver anslutningstypen ”IPsec”.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="lng"></a>Lokala nätverksgatewayer

Den lokala nätverksgatewayen avser vanligtvis din lokala plats. I den klassiska distributionsmodellen kallades den lokala nätverksgatewayen för Lokal plats. Du ger den lokala nätverksgatewayen ett namn, den offentliga IP-adressen för den lokala VPN-enheten och sedan anger du de adressprefix som finns på den lokala platsen. Azure tittar på måladressprefixen för nätverkstrafiken, läser konfigurationen som du har angett för din lokala nätverksgateway och dirigerar paketen enligt detta. Du kan ändra dessa adressprefix efter behov.



### Ändra adressprefix – Resource Manager

När du ändrar adressprefix skiljer sig proceduren åt, beroende på om du redan har skapat din VPN-gateway eller ej. Se artikelavsnittet [Ändra adressprefix för en lokal nätverksgateway](vpn-gateway-create-site-to-site-rm-powershell.md#modify).

I exemplet nedan ser du att en lokal nätverksgateway med namnet MyOnPremiseWest anges. Den kommer att innehålla två IP-adressprefix.

    New-AzureRmLocalNetworkGateway -Name MyOnPremisesWest -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24') 

### Ändra adressprefix – klassisk distribution

Om du behöver ändra dina lokala platser när du använder den klassiska distributionsmodellen kan du använda konfigurationssidan Lokala nätverk i den klassiska portalen, eller ändra i nätverkskonfigurationsfilen NETCFG.XML direkt.


##  <a name="devices"></a> VPN-enheter

Du måste kontrollera att den VPN-enhet som du planerar att använda har stöd för den VPN-typ som krävs för konfigurationen. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter.

##  <a name="requirements"></a>Gateway-krav


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 


## Nästa steg

Se [Vanliga frågor och svar om VPN Gateway](vpn-gateway-vpn-faq.md) för mer information, innan du går vidare med planering och utformning av din konfiguration.





 



<!--HONumber=jun16_HO2-->


