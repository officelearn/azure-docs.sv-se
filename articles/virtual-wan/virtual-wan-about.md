---
title: Översikt över Azure Virtual WAN | Microsoft Docs
description: Lär dig mer om automatiserade, skalbara anslutningar mellan olika platser med Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/25/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 54d177d7542d7501a91a955be20af776b16657a2
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182373"
---
# <a name="what-is-azure-virtual-wan"></a>Vad är Azure Virtual WAN?

Azure Virtual WAN är en nätverkstjänst som tillhandahåller optimerade och automatiserade anslutningar mellan olika platser via Azure. Med Virtual WAN kan du ansluta och konfigurera platsspecifika enheter så att de kommunicerar med Azure. Detta kan göras antingen manuellt eller med enheter från en prioriterad partner via en Virtual WAN-partner. Information finns i artikeln om [prioriterade partners](https://go.microsoft.com/fwlink/p/?linkid=2019615). Enheter från prioriterade partners är enkla att använda, ansluta och hantera. Med de inbyggda instrumentpanelerna i Azure WAN kan du snabbt felsöka problem och enkelt visa anslutningar i hög skala.

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan.png)

Den här artikeln ger en snabb överblick över nätverksanslutningarna för arbetsbelastningar i och utanför Azure. Virtual WAN har följande fördelar:

* **Integrerade anslutningslösningar i nav och eker:** Automatisera plats-till-plats-konfiguration och anslutningar mellan lokala platser och ett Azure-nav.
* **Automatiserad installation och konfiguration av ekerplatser:** Anslut enkelt dina virtuella nätverk och arbetsbelastningar till Azure-navet.
* **Intuitiv felsökning:** Du kan följa flödet från slutpunkt till slutpunkt i Azure och använda informationen för att vidta nödvändiga åtgärder.

## <a name="s2s"></a>Plats-till-plats-anslutningar

Om du vill skapa en plats-till-plats-anslutning med Virtual WAN kan du antingen arbeta med en [Virtual WAN-partner](https://go.microsoft.com/fwlink/p/?linkid=2019615) eller skapa anslutningen manuellt.

### <a name="s2spartner"></a>Arbeta med en Virtual WAN-partner

När du arbetar med en Virtual WAN-partner är processen:

1. Platsenhetens (VPN/SDWAN) kontrollant autentiseras för export av platsspecifik information till Azure med hjälp av ett [Azure-tjänstobjekt](../azure-resource-manager/resource-group-create-service-principal-portal.md).
2. Platsenhetens (VPN/SDWAN) kontrollant tar emot Azure-anslutningskonfigurationen och uppdaterar den lokala enheten. På så vis automatiseras konfigurationshämtningen, redigeringen och uppdateringen av den lokala VPN-enheten.
3. När enheten har rätt Azure-konfiguration upprättas en plats-till-plats-anslutning (två aktiva tunnlar) till Azures WAN-nätverk. Azure stöder både IKEv1 och IKEv2. BGP är valfritt.


Om du inte vill använda en prioriterad partner kan du konfigurera anslutningen manuellt, se [Skapa en plats-till-plats-anslutning med Virtual WAN](virtual-wan-site-to-site-portal.md).

## <a name="p2s"></a>Punkt-till-plats-anslutningar (förhandsversion)

En P2S-anslutning (punkt-till-plats) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. En P2S-anslutning upprättas genom att du startar den från klientdatorn. Den här lösningen är praktisk för distansarbetare som vill ansluta från en fjärransluten plats, t.ex. hemifrån eller från en konferens. VPN för punkt-till-plats är också ett bra alternativ till VPN för plats-till-plats om du bara har ett fåtal klienter som behöver ansluta.

Om du vill skapa anslutningen manuellt läser du [Skapa en punkt-till-plats-anslutning med hjälp av Virtual WAN](https://go.microsoft.com/fwlink/p/?linkid=2020051&clcid).

## <a name="er"></a>ExpressRoute-anslutningar (förhandsversion)

Information om hur du skapar anslutningen manuellt finns i [Skapa en ExpressRoute-anslutning med Virtual WAN](https://go.microsoft.com/fwlink/p/?linkid=2020148&clcid).


## <a name="resources"></a>Virtual WAN-resurser

Om du vill konfigurera ett virtuellt WAN från slutpunkt till slutpunkt skapar du följande resurser:

* **virtualWAN:** virtualWAN-resursen representerar ett virtuellt överlägg på ditt Azure-nätverk och består av en samling med flera resurser. Den innehåller länkar till alla virtuella nav som ska ingå i det virtuella WAN-nätverket. Virtual WAN-resurser är isolerade från varandra och kan inte innehålla ett gemensamt nav. Virtuella nav i Virtual WAN-nätverk kommunicerar inte med varandra. Egenskapen ”Allow branch to branch traffic” (Tillåt gren till gren-trafik) möjliggör trafik mellan VPN-webbplatser och VPN till ExpressRoute-aktiverade webbplatser. Tänk på att ExpressRoute ii Azure Virtual WAN för närvarande är en förhandsversion.

* **Plats:** Platsresursen, eller vpnsite, representerar din lokala VPN-enhet och dess inställningar. Om du arbetar med en Virtual WAN-partner har du en inbyggd lösning som hjälper dig att automatiskt exportera den här informationen till Azure.

* **Nav:** Ett virtuellt nav är ett Microsoft-hanterat virtuellt nätverk. Navet innehåller olika tjänstslutpunkter för anslutning från ditt lokala nätverk (vpnsite). Navet är kärnan i ditt nätverk i en region. Det kan bara finnas ett nav per Azure-region. När du skapar ett nav med hjälp av Azure-portalen skapas ett virtuellt navnätverk och en virtuell nav-vpngateway.

  En nav-gateway är inte detsamma som en virtuell nätverksgateway som du använder för ExpressRoute och VPN Gateway. När du använder Virtual WAN skapar du exempelvis inte en plats-till-plats-anslutning från din lokala plats direkt till ditt virtuella nätverk. I stället skapar du en plats-till-plats-anslutning till navet. Trafiken passerar alltid nav-gatewayen. Det innebär att dina virtuella nätverk inte behöver egna gateways för virtuella nätverk. Med Virtual WAN kan dina virtuella nätverk enkelt dra nytta av skalning via det virtuella navet och den virtuella nav-gatewayen. 

* **Anslutning mellan nav och virtuellt nätverk:** Den här resursen används för att sömlöst ansluta navet till ditt virtuella nätverk. För närvarande kan du bara ansluta till virtuella nätverk som finns i samma navregion.

* **Navroutningstabell:** Du kan skapa en virtuell navväg och tillämpa vägen på den virtuella navroutningstabellen. Du kan använda flera vägar i routningstabellen för den virtuella hubben.

## <a name="faq"></a>Vanliga frågor och svar

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]


## <a name="next-steps"></a>Nästa steg

* Visa [Virtual WAN-partners och -platser](https://aka.ms/virtualwan)