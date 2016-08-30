<properties 
   pageTitle="Om säker anslutning mellan flera platser för virtuella nätverk | Microsoft Azure"
   description="Lär dig mer om olika typer av säkra anslutningar mellan flera platser för virtuella nätverk, inklusive plats-till-plats-, punkt-till-plats- och ExpressRoute-anslutningar."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# Om säker anslutning mellan flera platser för virtuella nätverk

I den här artikeln beskrivs olika metoder för att ansluta din lokala plats till ett virtuellt Azure-nätverk. Artikeln gäller både för Resource Manager- och den klassiska distributionsmodellen. Om du letar efter anslutningsdiagram för VPN Gateway finns de i [Anslutningstopologier för Azure VPN Gateway](vpn-gateway-topology.md).

Det finns tre anslutningsalternativ: plats-till-plats, punkt-till-plats och ExpressRoute. Vilket alternativ du väljer kan bero på olika aspekter, som t.ex.:


- Vilken typ av dataflöde kräver din lösning?
- Vill du kommunicera över offentligt Internet via en säker VPN eller via en privat anslutning?
- Har du en offentlig IP-adress som kan användas?
- Planerar du att använda någon VPN-enhet? I så fall, är den kompatibel?
- Ansluter du bara några få datorer eller vill du ha en permanent anslutning för platsen?
- Vilken typ av VPN-gateway krävs för lösningen som du vill skapa?

Tabellen nedan kan hjälpa dig att bestämma det bästa anslutningsalternativet för din lösning.

[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]                                                                    

## Plats-till-plats-anslutningar

Med VPN för plats-till-plats kan du skapa en säker anslutning mellan din lokala plats och ditt virtuella nätverk. När man skapar en plats-till-plats-anslutning konfigureras en VPN-enhet som finns i det lokala nätverket till att skapa en säker anslutning med Azure VPN Gateway. När anslutningen har skapats kan resurser på det lokala nätverket och resurser som finns i det virtuella nätverket kommunicera direkt och på ett säkert sätt. Plats-till-plats-anslutningar kräver inte att du upprättar en separat anslutning för varje klientdator i det lokala nätverket för att få åtkomst till resurser i det virtuella nätverket.

**Använd en plats-till-plats-anslutning när:**

- Du vill skapa en hybridlösning.
- Du vill ha en anslutning mellan din lokala plats och ditt virtuella nätverk utan att klientsidan måste konfigureras.
- Du vill att anslutningen ska vara permanent. 

**Krav**

- Den lokala VPN-enheten måste ha en Internetuppkopplad IPv4-adress. Den kan inte finnas bakom en NAT.
- Du måste ha en VPN-enhet som är kompatibel. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). 
- VPN-enheten som du använder måste vara kompatibel med gateway-typen som krävs för din lösning. Se [Om VPN Gateway](vpn-gateway-about-vpngateways.md).
- Gateway-SKU:n påverkar också det aggregerade dataflödet. Se [Gateway SKU:er](vpn-gateway-about-vpngateways.md#gwsku) för mer information. 

**Tillgängliga distributionsmodeller och metoder för S2S**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)] 


## Punkt-till-plats-anslutningar

Med en punkt-till-plats-VPN kan du skapa en säker anslutning till det virtuella nätverket. I en punkt-till-plats-konfiguration konfigureras anslutningen separat på varje klientdator som du vill ansluta till det virtuella nätverket. Punkt-till-plats-anslutningar kräver inte någon VPN-enhet. Den här typen av anslutning använder en VPN-klient som du installerar på varje klientdator. VPN-anslutningen upprättas genom att anslutningen startas manuellt från den lokala klientdatorn.

Konfigurationer från punkt till plats och från plats till plats kan finnas samtidigt, men till skillnad från plats-till-plats-anslutningar kan inte punkt-till-plats-anslutningar konfigureras samtidigt med en ExpressRoute-anslutning till samma virtuella nätverk.

**Använd en punkt-till-plats-anslutning när:**

- Du bara vill konfigurera ett fåtal klienter till att ansluta till ett virtuellt nätverk.

- Du vill ansluta till ditt virtuella nätverk från en annan plats. Till exempel från ett kafé eller en konferensanläggning.

- Du har en plats-till-plats-anslutning, men vissa klienter behöver kunna ansluta från en annan plats.

- Du har inte åtkomst till någon VPN-enhet som uppfyller minimikraven för en plats-till-plats-anslutning.

- Du har inte någon Internetuppkopplad IPv4-adress till VPN-enheten.

**Tillgängliga distributionsmodeller och metoder för P2S**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## ExpressRoute-anslutningar

Med Azure ExpressRoute kan du skapa privata anslutningar mellan Azures datacenter och infrastruktur som finns lokalt eller i en samplaceringsmiljö. ExpressRoute-anslutningar går inte via offentligt Internet, samt är tillförlitligare, snabbare, har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet.

I vissa fall kan användningen av ExpressRoute-anslutningar till att överföra data mellan lokala platser och Azure även ge betydande kostnadsfördelar. Med ExpressRoute kan du upprätta anslutningar till Azure på en ExpressRoute-plats (Exchange-leverantörsanläggning) eller ansluta direkt till Azure från ditt befintliga WAN-nätverk (t.ex ett MPLS VPN) som tillhandahålls av en nätverkstjänstleverantör.

Mer information om ExpressRoute finns i [Teknisk översikt](../expressroute/expressroute-introduction.md) för ExpressRoute.


## Nästa steg

- Mer information om VPN Gateway finns i artiklarna [Om VPN Gateway](vpn-gateway-about-vpngateways.md), [Vanliga frågor och svar](vpn-gateway-vpn-faq.md) om VPN Gateway, samt [Planering och design](vpn-gateway-plan-design.md).

- Mer information om ExpressRoute finns i [Teknisk översikt](../expressroute/expressroute-introduction.md) för ExpressRoute, [Vanliga frågor och svar](../expressroute/expressroute-faqs.md) och [Arbetsflöden](../expressroute/expressroute-workflows.md).







<!--HONumber=jun16_HO2-->


