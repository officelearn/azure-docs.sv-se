---
title: Översikt över Azure Virtual WAN | Microsoft Docs
description: Läs mer om virtuella WAN automatiserad skalbar gren till grenen-anslutning, tillgängliga regioner och partner.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 03/20/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 6f2f002c5ff08e21741927d07a0facfd09ec0914
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295699"
---
# <a name="what-is-azure-virtual-wan"></a>Vad är Azure Virtual WAN?

Azure virtuellt WAN-nätverk är en nätverkstjänst som tillhandahåller optimerade och automatiserade gren anslutningar till och via Azure. Azure-regioner fungerar som hubbar som du kan välja att ansluta din grenar kan. Du kan utnyttja Azures stamnät för att upprätta en anslutning för gren-till-VNet och gren till grenen när grenarna är ansluten. En lista över partner och platser som har stöd för virtuella WAN VPN finns i den [virtuella WAN-partner och platser](virtual-wan-locations-partners.md) artikeln.

Azure virtuellt WAN-nätverk innehåller många Azure-molnet anslutning tjänster, till exempel plats-till-plats VPN (allmänt tillgänglig), ExpressRoute (förhandsversion) användare punkt-till-plats VPN (förhandsversion) i ett enda gränssnitt som fungerar. Upprätta en anslutning till virtuella Azure-nätverk är med hjälp av virtuella nätverksanslutningar.

![Virtual WAN-diagram](./media/virtual-wan-about/vwangraphic.png)

Den här artikeln ger en snabb överblick över nätverksanslutningar i Azure virtuellt WAN-nätverk. Virtual WAN har följande fördelar:

* **Integrerad anslutningslösningar i NAV och ekrar:** Automatisera plats-till-plats-konfiguration och anslutning mellan lokala platser och en Azure-hubb.
* **Automatiserad eker installation och konfiguration:** Ansluta virtuella nätverk och arbetsbelastningar till Azure hubben sömlöst.
* **Intuitiv Felsökning:** Du kan se flödet för slutpunkt till slutpunkt i Azure och sedan använda den här informationen för att utföra nödvändiga åtgärder.

## <a name="resources"></a>Virtual WAN-resurser

Om du vill konfigurera en slutpunkt till slutpunkt virtuellt WAN-nätverk, kan du skapa följande resurser:

* **virtualWAN:** Resursen virtualWAN representerar ett virtuellt överlägg för din Azure-nätverk och består av flera resurser. Den innehåller länkar till alla virtuella nav som ska ingå i det virtuella WAN-nätverket. Virtual WAN-resurser är isolerade från varandra och kan inte innehålla ett gemensamt nav. Virtuella nav i Virtual WAN-nätverk kommunicerar inte med varandra. Egenskapen 'Tillåter gren till grenen trafik ”kan trafik mellan VPN-webbplatser samt VPN till ExpressRoute (för närvarande i förhandsversion) aktiverat platser.

* **Hub:** En virtuell hubb är ett virtuellt nätverk med Microsoft-hanterad. Navet innehåller olika tjänstslutpunkter för anslutning från ditt lokala nätverk (vpnsite). Navet är kärnan i ditt nätverk i en region. Det kan bara finnas ett nav per Azure-region. När du skapar ett nav med hjälp av Azure-portalen skapas ett virtuellt navnätverk och en virtuell nav-vpngateway.

  En nav-gateway är inte detsamma som en virtuell nätverksgateway som du använder för ExpressRoute och VPN Gateway. När du använder virtuellt WAN-nätverk kan skapa du till exempel inte en plats-till-plats-anslutning från din lokala plats direkt till ditt virtuella nätverk. I stället skapar du en plats-till-plats-anslutning till hubben. Trafiken passerar alltid nav-gatewayen. Det innebär att dina virtuella nätverk inte behöver egna gateways för virtuella nätverk. Med Virtual WAN kan dina virtuella nätverk enkelt dra nytta av skalning via det virtuella navet och den virtuella nav-gatewayen.

* **Hub virtuell nätverksanslutning:** Anslutningsresursen Hub virtuellt nätverk används för att smidigt ansluta hubben till det virtuella nätverket. För närvarande kan du bara ansluta till virtuella nätverk som finns i samma navregion.

* **Routningstabellen för Hub:**  Du kan skapa en virtuell hubb-väg och använda vägen i routningstabellen virtuella hubben. Du kan använda flera vägar i routningstabellen för den virtuella hubben.

**Ytterligare resurser för virtuellt WAN-nätverk**

  * **Plats:** Den här resursen används för plats-till-plats-anslutningar. Platsresursen är **vpnsite**. Den motsvarar den lokala VPN-enheten och dess inställningar. Om du arbetar med en Virtual WAN-partner har du en inbyggd lösning som hjälper dig att automatiskt exportera den här informationen till Azure.

## <a name="connectivity"></a>Anslutning

Virtuella WAN-nätverket kan tre typer av anslutningar: plats-till-plats, punkt-till-plats (förhandsversion) och ExpressRoute (förhandsversion).

### <a name="s2s"></a>Plats-till-plats VPN-anslutningar

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan.png)

När du skapar ett virtuellt WAN-nätverk plats-till-plats-anslutning kan du arbeta med en partner som är tillgängliga. Om du inte vill använda en partner, kan du konfigurera anslutningen manuellt. Mer information finns i [skapa en plats-till-plats-anslutning med virtuellt WAN-nätverk](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Virtuellt WAN partner arbetsflöde

När du arbetar med ett virtuellt WAN-nätverk partner är arbetsflödet:

1. Platsenhetens (VPN/SDWAN) kontrollant autentiseras för export av platsspecifik information till Azure med hjälp av ett [Azure-tjänstobjekt](../active-directory/develop/howto-create-service-principal-portal.md).
2. Platsenhetens (VPN/SDWAN) kontrollant tar emot Azure-anslutningskonfigurationen och uppdaterar den lokala enheten. På så vis automatiseras konfigurationshämtningen, redigeringen och uppdateringen av den lokala VPN-enheten.
3. När enheten har rätt Azure-konfiguration upprättas en plats-till-plats-anslutning (två aktiva tunnlar) till Azures WAN-nätverk. Azure stöder både IKEv1 och IKEv2. BGP är valfritt.

#### <a name="partners"></a>För plats-till-plats virtuellt WAN-anslutningar

En lista över tillgängliga partner och platser finns i den [virtuella WAN-partner och platser](virtual-wan-locations-partners.md) artikeln.

### <a name="p2s"></a>Punkt-till-plats VPN-anslutningar (förhandsversion)

En P2S-anslutning (punkt-till-plats) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här lösningen är praktisk för distansarbetare som vill ansluta från en fjärransluten plats, t.ex. hemifrån eller från en konferens. VPN för punkt-till-plats är också ett bra alternativ till VPN för plats-till-plats om du bara har ett fåtal klienter som behöver ansluta.

För att skapa anslutningen, se [skapa en punkt-till-plats-anslutning med virtuellt WAN-nätverk](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>ExpressRoute-anslutningar (förhandsversion)

ExpressRoute kan du ansluta lokala nätverk till Azure över en privat anslutning. För att skapa anslutningen, se [skapa en ExpressRoute-anslutning med hjälp av virtuellt WAN-nätverk](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>platser

Platsinformation finns i den [virtuella WAN-partner och platser](virtual-wan-locations-partners.md) artikeln.

## <a name="faq"></a>Vanliga frågor och svar

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Nästa steg

[Skapa en plats-till-plats-anslutning med virtuellt WAN-nätverk](virtual-wan-site-to-site-portal.md)
