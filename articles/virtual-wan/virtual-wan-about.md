---
title: Översikt över Azure Virtual WAN | Microsoft Docs
description: Lär dig mer om det virtuella WAN-nätverket med automatisk skalbar anslutning från gren till gren, tillgängliga regioner och partner.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 9ac70252ce7c818ccbdecfd996b9970f011aa967
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241418"
---
# <a name="about-azure-virtual-wan"></a>Om Azure Virtual WAN

Azure Virtual WAN är en nätverks tjänst som tillhandahåller optimerad och automatiserad gren anslutning till och via Azure. Azure-regioner fungerar som hubbar som du kan välja att ansluta dina grenar till. Du kan använda Azures stamnät för att även ansluta grenar och dra nytta av anslutningar från förgrening till VNet. Vi har en lista över partners som stöder anslutnings automatisering med Azure Virtual WAN VPN. Mer information finns i artikeln om [virtuella WAN-partners och platser](virtual-wan-locations-partners.md) .

Azure Virtual WAN ger tillsammans många Azure Cloud Connectivity-tjänster, till exempel plats-till-plats-VPN, användar-VPN (punkt-till-plats) och ExpressRoute till ett enda drifts gränssnitt. Anslutning till Azure-virtuella nätverk upprättas med hjälp av virtuella nätverks anslutningar. Den möjliggör [Global transit nätverks arkitektur](virtual-wan-global-transit-network-architecture.md) baserat på en klassisk nav-och-eker-nätverksanslutning där det molnbaserade nätverkets hubb möjliggör transitiv anslutning mellan slut punkter som kan fördelas mellan olika typer av ekrar.

![Virtuellt WAN-diagram](./media/virtual-wan-about/virtualwan1.png)

Den här artikeln ger en snabb överblick över nätverks anslutningen i Azure Virtual WAN. Virtual WAN har följande fördelar:

* **Integrerade anslutnings lösningar i hubb och eker:** Automatisera plats-till-plats-konfiguration och anslutning mellan lokala platser och en Azure-hubb.
* **Automatiserad installation och konfiguration av ekerplatser:** Anslut enkelt dina virtuella nätverk och arbetsbelastningar till Azure-navet.
* **Intuitiv fel sökning:** Du kan se flödet från slut punkt till slut punkt i Azure och sedan använda den här informationen för att utföra nödvändiga åtgärder.

## <a name="basicstandard"></a>Basic-och standard-virtuella WAN

Det finns två typer av virtuella WAN-enheter: Basic och standard. I följande tabell visas de tillgängliga konfigurationerna för respektive typ.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Anvisningar för hur du uppgraderar ett virtuellt WAN finns i [uppgradera ett virtuellt WAN-nätverk från Basic till standard](upgrade-virtual-wan.md).

## <a name="architecture"></a>Designen

Information om virtuella WAN-arkitektur och hur du migrerar till virtuellt WAN finns i följande artiklar:

* [Virtuell WAN-arkitektur](migrate-from-hub-spoke-topology.md)
* [Nätverks arkitektur för global överföring](virtual-wan-global-transit-network-architecture.md)

## <a name="resources"></a>Virtual WAN-resurser

Om du vill konfigurera ett virtuellt WAN från slutpunkt till slutpunkt skapar du följande resurser:

* **virtualWAN:** virtualWAN-resursen representerar ett virtuellt överlägg på ditt Azure-nätverk och består av en samling med flera resurser. Den innehåller länkar till alla virtuella nav som ska ingå i det virtuella WAN-nätverket. Virtual WAN-resurser är isolerade från varandra och kan inte innehålla ett gemensamt nav. Virtuella hubbar i virtuella WAN-nätverk kommunicerar inte med varandra.

* **Nav:** Ett virtuellt nav är ett Microsoft-hanterat virtuellt nätverk. Navet innehåller olika tjänst slut punkter för att möjliggöra anslutning. Från ditt lokala nätverk (vpnsite) kan du ansluta till en VPN Gateway inuti den virtuella hubben, ansluta ExpressRoute-kretsar till en virtuell hubb eller till och med ansluta mobila användare till en punkt-till-plats-gateway i den virtuella hubben. Navet är kärnan i ditt nätverk i en region. Det kan bara finnas ett nav per Azure-region.

  En nav-gateway är inte detsamma som en virtuell nätverksgateway som du använder för ExpressRoute och VPN Gateway. När du använder ett virtuellt WAN-nätverk kan du till exempel inte skapa en plats-till-plats-anslutning från din lokala plats direkt till ditt VNet. I stället skapar du en plats-till-plats-anslutning till hubben. Trafiken passerar alltid nav-gatewayen. Det innebär att dina virtuella nätverk inte behöver egna gateways för virtuella nätverk. Med Virtual WAN kan dina virtuella nätverk enkelt dra nytta av skalning via det virtuella navet och den virtuella nav-gatewayen.

* **Anslutning mellan nav och virtuellt nätverk:** Den här resursen används för att sömlöst ansluta navet till ditt virtuella nätverk.

* **(För hands version) nav-till-hubb-anslutningar** – NAV är anslutna till varandra i ett virtuellt WAN-nätverk. Detta innebär att en gren, användare eller VNet som är ansluten till en lokal hubb kan kommunicera med en annan gren eller VNet med den kompletta nät arkitekturen i de anslutna hubbarna. Du kan också ansluta virtuella nätverk i en hubb-överföring via det virtuella navet, samt virtuella nätverk över hubben, med hjälp av det anslutna nav-till-hubb-ramverket.

* **Navroutningstabell:** Du kan skapa en virtuell navväg och tillämpa vägen på den virtuella navroutningstabellen. Du kan använda flera vägar i routningstabellen för den virtuella hubben.

**Ytterligare virtuella WAN-resurser**

  * **Plats:** Den här resursen används endast för plats-till-plats-anslutningar. Plats resursen är **vpnsite**. Den representerar din lokala VPN-enhet och dess inställningar. Om du arbetar med en Virtual WAN-partner har du en inbyggd lösning som hjälper dig att automatiskt exportera den här informationen till Azure.

## <a name="connectivity"></a>Typer av anslutningar

Virtuella WAN-nätverk möjliggör följande typer av anslutningar: plats-till-plats-VPN, användar-VPN (punkt-till-plats) och ExpressRoute.

### <a name="s2s"></a>Plats-till-plats-VPN-anslutningar

![Virtuellt WAN-diagram](./media/virtual-wan-about/virtualwan.png)

När du skapar en virtuell WAN-plats-till-plats-anslutning kan du arbeta med en tillgänglig partner. Om du inte vill använda en partner kan du konfigurera anslutningen manuellt. Mer information finns i [skapa en plats-till-plats-anslutning med hjälp av virtuellt WAN](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Arbets flöde för virtuell WAN-partner

När du arbetar med en virtuell WAN-partner är arbets flödet:

1. Platsenhetens (VPN/SDWAN) kontrollant autentiseras för export av platsspecifik information till Azure med hjälp av ett [Azure-tjänstobjekt](../active-directory/develop/howto-create-service-principal-portal.md).
2. Platsenhetens (VPN/SDWAN) kontrollant tar emot Azure-anslutningskonfigurationen och uppdaterar den lokala enheten. På så vis automatiseras konfigurationshämtningen, redigeringen och uppdateringen av den lokala VPN-enheten.
3. När enheten har rätt Azure-konfiguration upprättas en plats-till-plats-anslutning (två aktiva tunnlar) till Azures WAN-nätverk. Azure stöder både IKEv1 och IKEv2. BGP är valfritt.

#### <a name="partners"></a>Partner för virtuella WAN-anslutningar från plats till plats

En lista över tillgängliga partners och platser finns i artikeln [virtuella WAN-partners och platser](virtual-wan-locations-partners.md) .

### <a name="uservpn"></a>Användares VPN-anslutningar (punkt-till-plats)

Du kan ansluta till dina resurser i Azure via en IPsec/IKE (IKEv2) eller OpenVPN-anslutning. Den här typen av anslutning kräver att en VPN-klient konfigureras på klient datorn. Mer information finns i [skapa en punkt-till-plats-anslutning](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>ExpressRoute-anslutningar
Med ExpressRoute kan du ansluta ett lokalt nätverk till Azure över en privat anslutning. Information om hur du skapar anslutningen finns i [skapa en ExpressRoute-anslutning med hjälp av virtuellt WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Platser

Plats information finns i artikeln [virtuella WAN-partners och platser](virtual-wan-locations-partners.md) .

## <a name="faq"></a>Vanliga frågor och svar

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Nästa steg

[Skapa en plats-till-plats-anslutning med hjälp av virtuellt WAN](virtual-wan-site-to-site-portal.md)
