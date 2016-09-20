<properties 
   pageTitle="Om VPN Gateway| Microsoft Azure"
   description="Lär dig om VPN Gateway-anslutningar för Azure Virtual Networks."
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
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# Om VPN Gateway


VPN Gateway är en samling resurser som används för att skicka nätverkstrafik mellan virtuella nätverk och lokala platser. Gateways används för plats-till-plats-, punkt-till-plats- och ExpressRoute-anslutningar. VPN Gateway används också för att skicka trafik mellan flera virtuella nätverk i Azure (VNet-till-VNet). 

Om du vill skapa en anslutning lägger du till en gateway för virtuella nätverk (VNet-gateway) till ett VNet och konfigurerar ytterligare VPN Gateway-resurser och deras inställningar. Varje virtuellt nätverk kan bara ha en VNet-gateway per gateway-typ. Du kan exempelvis ha en VNet-gateway som använder -GatewayType Vpn och en som använder -GatewayType ExpressRoute.

Information om gateway-kraven finns i [Krav för gateways](vpn-gateway-about-vpn-gateway-settings.md#requirements). Information om uppskattat datagenomflöde finns i [Om VPN Gateway-inställningar](vpn-gateway-about-vpn-gateway-settings.md#aggthroughput). Prisinformation finns i [Priser för VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway). Information om prenumerationer och tjänstbegränsningar finns i [Nätverksgränser](../articles/azure-subscription-service-limits.md#networking-limits).

När du konfigurerar VPN Gateway varierar instruktionerna som du följer beroende på vilken distributionsmodell du använde för att skapa det virtuella nätverket. Om du exempelvis har skapat ditt VNet med den klassiska distributionsmodellen, ska du använda riktlinjerna och instruktionerna för den modellen när du skapar och konfigurerar dina VPN Gateway-inställningar. Mer information finns i [Förstå den klassiska distributionsmodellen och Resource Manager-distributionsmodellen](../resource-manager-deployment-model.md).

Avsnitten nedan innehåller tabeller med följande information för konfigurationen:

- tillgänglig distributionsmodell
- tillgängliga konfigurationsverktyg
- länkar som tar dig direkt till en artikel om en sådan finns


Använd diagrammen och beskrivningarna för att välja den konfigurationstopologi som passar dina behov. Diagrammen visar de viktigaste grundläggande topologierna, men du kan skapa mer komplexa konfigurationer och använda diagrammen som utgångspunkt. Varje konfiguration är beroende av de VPN Gateway-inställningar som du väljer.

### Konfigurera VPN Gateway-inställningar

Eftersom VPN Gateway är en samling resurser kan du konfigurera vissa resurser med hjälp av ett enda verktyg och sedan växla till ett annat för att konfigurera andra resursinställningar. För närvarande går det inte att konfigurera alla VPN Gateway-resursinställningar på Azure-portalen. Anvisningarna i artiklarna för varje konfiguration anger om ett specifikt verktyg behövs. Om du arbetar med den klassiska distributionsmodellen kanske du vill använda den klassiska portalen eller PowerShell. Information om de enskilda tillgängliga inställningarna finns i [Om VPN Gateway-inställningar](vpn-gateway-about-vpn-gateway-settings.md).



## Plats-till-plats och flera platser

### Plats-till-plats

En plats-till-plats-anslutning (S2S) är en anslutning via en VPN-tunnel med IPsec/IKE (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats en offentlig IP-adress och som inte finns bakom en NAT. S2S-anslutningar kan användas för konfigurationer mellan platser och för hybridkonfigurationer.   

![S2S-anslutning](./media/vpn-gateway-about-vpngateways/demos2s.png "site-to-site")


### Flera platser

Du kan skapa och konfigurera en VPN-anslutning mellan ditt VNet och flera lokala nätverk. När du arbetar med flera anslutningar måste du använda en routningsbaserad VPN-typ (dynamisk gateway för klassiska virtuella nätverk). Eftersom ett VNet bara kan ha en VNet-gateway delar alla anslutningar genom gatewayen på den tillgängliga bandbredden. Den här typen av konfiguration kallas ofta en ”anslutning till flera platser”.
 

![Anslutning till flera platser](./media/vpn-gateway-about-vpngateways/demomulti.png "multi-site")

### Distributionsmodeller och distributionsmetoder

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 

## VNet-till-VNet

Du ansluter ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) på nästan samma sätt som du ansluter ett VNet till en lokal plats. Båda typerna av anslutning använder Azures VPN-gateway för att få en säker tunnel med IPsec/IKE. Du kan till och med kombinera VNet-till-VNet-kommunikation med konfigurationer för flera platser. Det innebär att du kan etablera nätverkstopologier som kombinerar anslutningen för flera platser med en intern virtuell nätverksanslutning.

De virtuella nätverk som du ansluter kan finnas:

- i samma eller olika regioner
- i samma eller olika prenumerationer 
- i samma eller olika distributionsmodeller



![VNet-till-VNet-anslutning](./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-to-vnet")



### Anslutningar mellan distributionsmodeller

Azure har för närvarande två distributionsmodeller: klassisk och Resource Manager. Om du har använt Azure ett tag har du förmodligen virtuella Azure-datorer och instansroller som kör i ett klassiskt VNet. Dina nyare virtuella datorer och rollinstanser kanske körs i ett VNet som skapats i Resource Manager. Du kan skapa en anslutning mellan virtuella nätverk så att resurserna i ett VNet kan kommunicera direkt med resurserna i ett annat.


### Distributionsmodeller och distributionsmetoder

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

### VNet-peering

Du kan använda VNet-peering för att skapa anslutningen, förutsatt att VNet-konfigurationen uppfyller vissa krav. Ingen VNet-gateway används för VNet-peering. [VNet-peering](../virtual-network/virtual-network-peering-overview.md) finns endast som förhandsversion för närvarande.



## Punkt-till-plats

Med en P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. P2S är en VPN-anslutning över SSTP (Secure Socket Tunneling Protocol). P2S-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress för att fungera. Du kan upprätta VPN-anslutningen genom att starta den från klientdatorn. Detta är en användbar lösning när du vill ansluta till ditt VNet från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett VNet. 


![Punkt-till-plats-anslutning](./media/vpn-gateway-about-vpngateways/demop2s.png "point-to-site")

### Distributionsmodeller och distributionsmetoder

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


## ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Mer information om ExpressRoute finns i [Teknisk översikt över ExpressRoute](../expressroute/expressroute-introduction.md).


## Plats-till-plats- och samexisterande ExpressRoute-anslutningar

ExpressRoute är en direkt, dedikerad anslutning från ditt WAN (inte över offentliga Internet) till Microsoft Services, inklusive Azure. VPN-trafiken från plats-till-plats transporteras krypterat över offentligt Internet. Det finns flera fördelar med att kunna konfigurera VPN-anslutningar för plats-till-plats- och ExpressRoute-anslutningar för samma virtuella nätverk.

Du kan konfigurera ett VPN för plats-till-plats som en säker redundansväxlingsväg för ExpressRoute, eller använda VPN för plats-till-plats för att ansluta till platser som inte finns i ditt nätverk, men som ansluts via ExpressRoute. Den här konfigurationen kräver två VNet-gateways för samma virtuella nätverk, en med -GatewayType Vpn och en annan med -GatewayType ExpressRoute.


![Samexisterande anslutning](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")


### Distributionsmodeller och distributionsmetoder

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)] 


## Nästa steg

Mer information om VPN-gateways finns i [Vanliga frågor och svar om VPN Gateway](vpn-gateway-vpn-faq.md)

Anslut din lokala plats till ett VNet. Mer information finns i [Skapa en plats-till-plats-anslutning](vpn-gateway-howto-site-to-site-resource-manager-portal.md).





 



<!--HONumber=sep16_HO1-->


