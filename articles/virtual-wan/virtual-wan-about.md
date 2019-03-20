---
title: Översikt över Azure Virtual WAN | Microsoft Docs
description: Läs mer om virtuella WAN automatiserad skalbar gren till grenen-anslutning, tillgängliga regioner och partner.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 03/19/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 5c6e69e05eaa036e140d7275b4e66930a3e5be7a
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225305"
---
# <a name="what-is-azure-virtual-wan"></a>Vad är Azure Virtual WAN?

Azure virtuellt WAN-nätverk är en nätverkstjänst som tillhandahåller optimerade och automatiserade gren anslutningar till och via Azure. Azure-regioner fungerar som hubbar som du kan välja att ansluta din grenar kan. Du kan utnyttja Azures stamnät för att upprätta en anslutning för gren-till-VNet och gren till grenen när grenarna är ansluten.

Azure virtuellt WAN-nätverk innehåller många Azure-molnet anslutning tjänster, till exempel plats-till-plats VPN (allmänt tillgänglig), ExpressRoute (förhandsversion) användare punkt-till-plats VPN (förhandsversion) i ett enda gränssnitt som fungerar. Anslutning till virtuella Azure-nätverk upprättas med hjälp av virtuella nätverksanslutningar.

![Virtual WAN-diagram](./media/virtual-wan-about/vwangraphic.png)

Den här artikeln ger en snabb överblick över nätverksanslutningar i Azure virtuellt WAN-nätverk. Virtual WAN har följande fördelar:

* **Integrerad anslutningslösningar i NAV och ekrar:** Automatisera plats-till-plats-konfiguration och anslutning mellan lokala platser och en Azure-hubb.
* **Automatiserad eker installation och konfiguration:** Ansluta virtuella nätverk och arbetsbelastningar till Azure hubben sömlöst.
* **Intuitiv Felsökning:** Du kan se flödet för slutpunkt till slutpunkt i Azure och använda den här informationen för att utföra nödvändiga åtgärder.

## <a name="partner-region"></a>Partner och platser

Mer information finns i den [virtuella WAN-partner och platser](virtual-wan-locations-partners.md) artikeln.

### <a name="partner"></a>Partner

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

### <a name="locations"></a>platser

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="s2s"></a>Plats-till-plats-anslutningar

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan.png)

Om du vill skapa en plats-till-plats-anslutning med Virtual WAN kan du antingen arbeta med en [Virtual WAN-partner](virtual-wan-locations-partners.md) eller skapa anslutningen manuellt.

### <a name="s2spartner"></a>Arbetsflöde för partner

När du arbetar med ett virtuellt WAN-nätverk partner är arbetsflödet:

1. Platsenhetens (VPN/SDWAN) kontrollant autentiseras för export av platsspecifik information till Azure med hjälp av ett [Azure-tjänstobjekt](../active-directory/develop/howto-create-service-principal-portal.md).
2. Platsenhetens (VPN/SDWAN) kontrollant tar emot Azure-anslutningskonfigurationen och uppdaterar den lokala enheten. På så vis automatiseras konfigurationshämtningen, redigeringen och uppdateringen av den lokala VPN-enheten.
3. När enheten har rätt Azure-konfiguration upprättas en plats-till-plats-anslutning (två aktiva tunnlar) till Azures WAN-nätverk. Azure stöder både IKEv1 och IKEv2. BGP är valfritt.

Om du inte vill använda en partner, kan du konfigurera anslutningen manuellt, finns i [skapa en plats-till-plats-anslutning med virtuellt WAN-nätverk](virtual-wan-site-to-site-portal.md).

## <a name="p2s"></a>Punkt-till-plats-anslutningar (förhandsversion)

En P2S-anslutning (punkt-till-plats) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här lösningen är praktisk för distansarbetare som vill ansluta från en fjärransluten plats, t.ex. hemifrån eller från en konferens. VPN för punkt-till-plats är också ett bra alternativ till VPN för plats-till-plats om du bara har ett fåtal klienter som behöver ansluta.

Om du vill skapa anslutningen manuellt läser du [Skapa en punkt-till-plats-anslutning med hjälp av Virtual WAN](virtual-wan-point-to-site-portal.md).

## <a name="er"></a>ExpressRoute-anslutningar (förhandsversion)

Information om hur du skapar anslutningen manuellt finns i [Skapa en ExpressRoute-anslutning med Virtual WAN](virtual-wan-expressroute-portal.md).

## <a name="resources"></a>Virtual WAN-resurser

Om du vill konfigurera ett virtuellt WAN från slutpunkt till slutpunkt skapar du följande resurser:

* **virtualWAN:** Resursen virtualWAN representerar ett virtuellt överlägg för din Azure-nätverk och består av flera resurser. Den innehåller länkar till alla virtuella nav som ska ingå i det virtuella WAN-nätverket. Virtual WAN-resurser är isolerade från varandra och kan inte innehålla ett gemensamt nav. Virtuella nav i Virtual WAN-nätverk kommunicerar inte med varandra. Egenskapen ”Allow branch to branch traffic” (Tillåt gren till gren-trafik) möjliggör trafik mellan VPN-webbplatser och VPN till ExpressRoute-aktiverade webbplatser. Tänk på att ExpressRoute ii Azure Virtual WAN för närvarande är en förhandsversion.

* **Plats:** Platsresursen kallas vpnsite representerar den lokala VPN-enheten och dess inställningar. Om du arbetar med en Virtual WAN-partner har du en inbyggd lösning som hjälper dig att automatiskt exportera den här informationen till Azure.

* **Hub:** En virtuell hubb är ett virtuellt nätverk med Microsoft-hanterad. Navet innehåller olika tjänstslutpunkter för anslutning från ditt lokala nätverk (vpnsite). Navet är kärnan i ditt nätverk i en region. Det kan bara finnas ett nav per Azure-region. När du skapar ett nav med hjälp av Azure-portalen skapas ett virtuellt navnätverk och en virtuell nav-vpngateway.

  En nav-gateway är inte detsamma som en virtuell nätverksgateway som du använder för ExpressRoute och VPN Gateway. När du använder Virtual WAN skapar du exempelvis inte en plats-till-plats-anslutning från din lokala plats direkt till ditt virtuella nätverk. I stället skapar du en plats-till-plats-anslutning till navet. Trafiken passerar alltid nav-gatewayen. Det innebär att dina virtuella nätverk inte behöver egna gateways för virtuella nätverk. Med Virtual WAN kan dina virtuella nätverk enkelt dra nytta av skalning via det virtuella navet och den virtuella nav-gatewayen. 

* **Hub virtuell nätverksanslutning:** Anslutningsresursen Hub virtuellt nätverk används för att smidigt ansluta hubben till det virtuella nätverket. För närvarande kan du bara ansluta till virtuella nätverk som finns i samma navregion.

* **Routningstabellen för Hub:**  Du kan skapa en virtuell hubb-väg och använda vägen i routningstabellen virtuella hubben. Du kan använda flera vägar i routningstabellen för den virtuella hubben.

## <a name="faq"></a>Vanliga frågor och svar

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Nästa steg

Visa den [virtuella WAN-partner och platser](virtual-wan-locations-partners.md) för ytterligare information om våra virtuella WAN-partner och platser.
