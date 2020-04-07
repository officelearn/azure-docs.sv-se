---
title: Översikt över Azure Virtual WAN | Microsoft Docs
description: Lär dig mer om Virtual WAN-automatisk skalbar anslutning mellan för gren till gren, tillgängliga regioner och partner.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 927c09f61ce0847c72cefb51935116070e956861
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743101"
---
# <a name="about-azure-virtual-wan"></a>Om Azure Virtual WAN

Azure Virtual WAN är en nätverkstjänst som sammanför många nätverks-, säkerhets- och routningsfunktioner för att tillhandahålla ett enda operativt gränssnitt. Dessa funktioner inkluderar Branch-anslutning (via anslutningsautomatisering från Virtuella WAN Partner-enheter som SD-WAN eller VPN CPE), Plats-till-plats VPN-anslutning, Remote User VPN (Point-to-site) anslutning, Privat (ExpressRoute) anslutning, Intra molnanslutning (Transitiv anslutning för virtuella nätverk), VPN ExpressRoute Interconnectivity, Routing, Azure-brandvägg, Kryptering för privat anslutning etc. Du behöver inte ha alla dessa användningsfall för att börja använda Virtual WAN. Du kan helt enkelt komma igång med bara ett användningsfall och justera ditt nätverk när det utvecklas. Virtual WAN-arkitekturen är en hubb- och ekerarkitektur med inbyggd skala och prestanda för grenar (VPN/SD-WAN-enheter), användare (Azure VPN/OpenVPN/IKEv2-klienter), ExpressRoute-kretsar och virtuella nätverk. Det möjliggör [global transitnätverksarkitektur](virtual-wan-global-transit-network-architecture.md) där molnet värdbaserade nätverk "hub" möjliggör transitiv anslutning mellan slutpunkter som kan distribueras över olika typer av "ekrar".

Azure-regioner fungerar som nav som du kan välja att ansluta till. Alla hubbar är anslutna i fullt nät i ett virtuellt standard WAN-minne, vilket gör det enkelt för användaren att använda Microsofts stamnät för alla (alla ekrar) anslutning. För ekeranslutning med SD-WAN/VPN-enheter kan användare antingen manuellt konfigurera den i Azure Virtual WAN eller använda partnerlösningen För virtual WAN CPE (SD-WAN/VPN) för att konfigurera anslutning till Azure. Vi har en lista över partner som stöder automatisering av anslutning (möjlighet att exportera enhetsinformationen till Azure, hämta Azure-konfigurationen och upprätta anslutning) med Azure Virtual WAN. Mer information finns i artikeln [Virtuella WAN-partner och platser.](virtual-wan-locations-partners.md) 

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan1.png)

Den här artikeln innehåller en snabb vy över nätverksanslutningen i Azure Virtual WAN. Virtual WAN har följande fördelar:

* **Integrerade anslutningslösningar i nav och ekrar:** Automatisera konfiguration och anslutning mellan lokala platser och en Azure-hubb.
* **Automatiserad installation och konfiguration av ekerplatser:** Anslut enkelt dina virtuella nätverk och arbetsbelastningar till Azure-navet.
* **Intuitiv felsökning:** Du kan se det end-to-end-flödet i Azure och sedan använda den här informationen för att vidta nödvändiga åtgärder.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Grundläggande och standard virtuella WANs

Det finns två typer av virtuella WANs: Basic och Standard. I följande tabell visas tillgängliga konfigurationer för varje typ.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Steg för att uppgradera ett virtuellt WAN finns i [Uppgradera ett virtuellt WAN från Basic till Standard](upgrade-virtual-wan.md).

## <a name="architecture"></a><a name="architecture"></a>Arkitektur

Information om Virtual WAN-arkitektur och hur du migrerar till Virtuellt WAN finns i följande artiklar:

* [Virtuell WAN-arkitektur](migrate-from-hub-spoke-topology.md)
* [Global Transit Network-arkitektur](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Virtual WAN-resurser

Om du vill konfigurera ett virtuellt WAN från slutpunkt till slutpunkt skapar du följande resurser:

* **virtualWAN:** virtualWAN-resursen representerar ett virtuellt överlägg på ditt Azure-nätverk och består av en samling med flera resurser. Den innehåller länkar till alla virtuella nav som ska ingå i det virtuella WAN-nätverket. Virtual WAN-resurser är isolerade från varandra och kan inte innehålla ett gemensamt nav. Virtuella hubbar i Virtual WAN kommunicerar inte med varandra.

* **Nav:** Ett virtuellt nav är ett Microsoft-hanterat virtuellt nätverk. Navet innehåller olika tjänstslutpunkter för att aktivera anslutning. Från ditt lokala nätverk (vpnsite) kan du ansluta till en VPN-gateway inuti det virtuella navet, ansluta ExpressRoute-kretsar till ett virtuellt nav eller till och med ansluta mobila användare till en point-to-site-gateway i det virtuella navet. Navet är kärnan i ditt nätverk i en region. Det kan bara finnas ett nav per Azure-region.

  En nav-gateway är inte detsamma som en virtuell nätverksgateway som du använder för ExpressRoute och VPN Gateway. När du till exempel använder Virtuellt WAN skapar du inte en plats-till-plats-anslutning från den lokala webbplatsen direkt till ditt virtuella nätverk. I stället skapar du en plats-till-plats-anslutning till navet. Trafiken passerar alltid nav-gatewayen. Det innebär att dina virtuella nätverk inte behöver egna gateways för virtuella nätverk. Med Virtual WAN kan dina virtuella nätverk enkelt dra nytta av skalning via det virtuella navet och den virtuella nav-gatewayen.

* **Anslutning mellan nav och virtuellt nätverk:** Den här resursen används för att sömlöst ansluta navet till ditt virtuella nätverk.

* **(Förhandsgranska) Hub-to-Hub-anslutning** - Hubbar är alla anslutna till varandra i ett virtuellt WAN. Detta innebär att en gren, användare eller VNet som är ansluten till ett lokalt nav kan kommunicera med en annan gren eller VNet med hjälp av den fullständiga mesh-arkitekturen för de anslutna hubbar. Du kan också ansluta virtuella nätverk inom ett nav som transiterar via det virtuella navet, samt virtuella nätverk över hubben, med hjälp av hub-to-hub-anslutna ramverk.

* **Navroutningstabell:** Du kan skapa en virtuell navväg och tillämpa vägen på den virtuella navroutningstabellen. Du kan använda flera vägar i routningstabellen för den virtuella hubben.

**Ytterligare virtuella WAN-resurser**

  * **Webbplats:** Den här resursen används endast för plats-till-plats-anslutningar. Webbplatsen resurs är **vpnsite**. Den representerar din lokala VPN-enhet och dess inställningar. Om du arbetar med en Virtual WAN-partner har du en inbyggd lösning som hjälper dig att automatiskt exportera den här informationen till Azure.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Typer av anslutning

Virtual WAN tillåter följande typer av anslutning: Site-to-Site VPN, User VPN (Point-to-Site) och ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Plats-till-plats-VPN-anslutningar

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan.png)

När du skapar en virtuell WAN-anslutning från plats till plats kan du arbeta med en tillgänglig partner. Om du inte vill använda en partner kan du konfigurera anslutningen manuellt. Mer information finns i [Skapa en plats-till-plats-anslutning med Virtual WAN](virtual-wan-site-to-site-portal.md).

#### <a name="virtual-wan-partner-workflow"></a><a name="s2spartner"></a>Arbetsflöde för virtuell WAN-partner

När du arbetar med en Virtual WAN-partner är arbetsflödet:

1. Platsenhetens (VPN/SDWAN) kontrollant autentiseras för export av platsspecifik information till Azure med hjälp av ett [Azure-tjänstobjekt](../active-directory/develop/howto-create-service-principal-portal.md).
2. Platsenhetens (VPN/SDWAN) kontrollant tar emot Azure-anslutningskonfigurationen och uppdaterar den lokala enheten. På så vis automatiseras konfigurationshämtningen, redigeringen och uppdateringen av den lokala VPN-enheten.
3. När enheten har rätt Azure-konfiguration upprättas en plats-till-plats-anslutning (två aktiva tunnlar) till Azures WAN-nätverk. Azure stöder både IKEv1 och IKEv2. BGP är valfritt.

#### <a name="partners-for-site-to-site-virtual-wan-connections"></a><a name="partners"></a>Partner för virtuella WAN-anslutningar från plats till plats

En lista över tillgängliga partner och platser finns i artikeln [Virtuella WAN-partner och platser.](virtual-wan-locations-partners.md)

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Vpn-anslutningar för användare (punkt till plats)

Du kan ansluta till dina resurser i Azure via en IPsec/IKE (IKEv2) eller OpenVPN-anslutning. Den här typen av anslutning kräver att en VPN-klient konfigureras på klientdatorn. Mer information finns i [Skapa en punkt-till-plats-anslutning](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute-anslutningar
Med ExpressRoute kan du ansluta lokalt nätverk till Azure via en privat anslutning. En anslutningsã¶r att skapa anslutningen finns i [Skapa en ExpressRoute-anslutning med Virtual WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a><a name="locations"></a>Platser

Platsinformation finns i artikeln [Virtuella WAN-partner och platser.](virtual-wan-locations-partners.md)

## <a name="faq"></a><a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Nästa steg

[Skapa en plats-till-plats-anslutning med Virtual WAN](virtual-wan-site-to-site-portal.md)
