<properties 
   pageTitle="Anslutningstopologier för VPN Gateway | Microsoft Azure"
   description="Visa anslutningstopologier för VPN Gateway samt tillgängliga konfigurationsverktyg och distributionsmodeller."
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
   ms.date="07/19/2016"
   ms.author="cherylmc" />

# Anslutningar för Azure VPN Gateway

Den här artikeln visar grundläggande anslutningstopologier för VPN Gateway. Du kan använda figurer och beskrivningar för att välja den konfigurationstopologi som motsvarar dina behov. Även om den här artikeln i huvudsak beskriver grundläggande topologier, går det att skapa mer komplexa topologier med diagrammen som en riktlinje.

Varje topologi innehåller en tabell som visar vilken distributionsmodell topologin är tillgänglig för, distributionsverktyg som du kan använda för att konfigurera varje topologi, samt länkar direkt till en artikel om det finns någon. Vi uppdaterar tabellerna när det finns nya artiklar och distributionsverktyg som du kan använda.

När du har bestämt dig för den anslutning som du vill skapa, beror de anvisningar som du använder för att skapa din VPN-gateway på vilken distributionsmodell som användes för att skapa det virtuella nätverket. Om du exempelvis har skapat ditt VNet med den klassiska distributionsmodellen, ska du använda riktlinjer och instruktioner för den modellen när du skapar och konfigurerar din VPN-gateway. Du kan inte skapa en VPN-gateway i Resource Manager för ett virtuellt nätverk med den klassiska distributionsmodellen. Mer information om distributionsmodellerna finns i [Förstå Resource Manager- och klassiska distributionsmodeller](../resource-manager-deployment-model.md).

## Plats-till-plats och flera platser

En plats-till-plats-anslutning är en anslutning via IPsec/IKE (IKEv1 eller IKEv2) VPN-tunnel. Den här typen av anslutning kräver en VPN-enhet eller Windows Server RRAS lokalt. Plats-till-plats-anslutningar kan användas för flera platser och hybridkonfigurationer.   


**S2S-diagram**

![S2S-anslutning](./media/vpn-gateway-topology/site2site.png "site-to-site")

Du kan skapa och konfigurera flera S2S VPN-anslutningar till dina lokala nätverk om du använder Azures routningsbaserade VPN-anslutningar. Den här typen av konfiguration kallas ofta en ”anslutning till flera platser”.
 

**Diagram för flera platser**

![Anslutning till flera platser](./media/vpn-gateway-topology/multisite.png "multi-site")


**Tillgängliga distributionsmodeller och metoder**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 

## VNet-till-VNet

Att ansluta ett virtuellt nätverk till ett annat virtuellt nätverk (VNet-till-VNet) liknar att ansluta ett VNet till en lokal plats. Båda typerna av anslutning använder Azures VPN-gateway för att få en säker tunnel med IPsec/IKE. De VNets du ansluter kan finnas i olika regioner eller i olika prenumerationer. Du kan till och med kombinera VNet-till-VNet-kommunikation med konfigurationer för flera platser. Det innebär att du kan etablera nätverkstopologier som kombinerar anslutningen för flera platser med en intern virtuell nätverksanslutning.

Azure har två distributionsmodeller: Azure Service Management (kallas för klassisk) och Azure Resource Manager. Om du har använt Azure under en längre tid har du förmodligen virtuella Azure-datorer och instansroller som körs på ett klassiskt VNet, medan dina nyare virtuella datorer och rollinstanser kanske körs på ett VNet som skapats i ARM. Du kan skapa en anslutning mellan virtuella nätverk, även om de finns i olika distributionsmodeller.


**VNet2VNet-diagram**

![VNet-till-VNet-anslutning](./media/vpn-gateway-topology/vnet2vnet.png "vnet-to-vnet")


**Tillgängliga distributionsmodeller och metoder**

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 


## Punkt-till-plats

Med en punkt-till-plats-konfiguration kan du skapa en säker anslutning till ditt virtuella nätverk från en klientdator separat. VPN-anslutningen upprättas genom att du startar anslutningen från klientdatorn. Detta är en användbar lösning när du vill ansluta till ditt VNet från en annan plats, t.ex. hemifrån eller från en konferens, eller när du bara har ett fåtal klienter som behöver kunna ansluta till ett virtuellt nätverk. 

En punkt-till-plats-anslutning är en VPN-anslutning över SSTP (Secure Socket Tunneling Protocol). Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress för att fungera. 

**P2S-diagram**

![Punkt-till-plats-anslutning](./media/vpn-gateway-topology/point2site.png "point-to-site")

**Tillgängliga distributionsmodeller och metoder**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 


##ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Mer information om ExpressRoute finns i [Teknisk översikt över ExpressRoute](../expressroute/expressroute-introduction.md).



## Plats-till-plats- och samexisterande ExpressRoute-anslutningar

ExpressRoute är en direkt, dedikerad anslutning till Microsoft Services, inklusive Azure, från ditt WAN i stället för över offentligt Internet. VPN-trafiken från plats-till-plats transporteras krypterat över offentligt Internet. Att kunna konfigurera VPN-anslutningar för plats till plats och ExpressRoute för samma virtuella nätverk har flera fördelar. Du kan konfigurera VPN för plats till plats som en säker redundanssökväg för ExpressRoute, eller använda VPN för plats till plats till att ansluta till platser som inte ingår i ditt nätverk, men som är anslutna via ExpressRoute. 


**Diagram över samexisterande anslutningar**

![Samexisterande anslutning](./media/vpn-gateway-topology/expressroutes2s.png "expressroute-site2site")


**Tillgängliga distributionsmodeller och metoder**

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)] 




## Nästa steg

Bekanta dig med objekten i artiklarna [Om VPN Gateway](vpn-gateway-about-vpngateways.md) och [Vanliga frågor och svar om VPN Gateway](vpn-gateway-vpn-faq.md) för att bättre förstå inställningarna för VPN-gatewayer.





 



<!--HONumber=sep16_HO1-->


