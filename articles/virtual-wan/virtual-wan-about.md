---
title: Översikt över Azure Virtual WAN | Microsoft Docs
description: Lär dig mer om automatiserade, skalbara anslutningar mellan olika platser med Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 07/10/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 54cf6c356ec4bb51b123e48c52c5beebc990e59d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009978"
---
# <a name="what-is-azure-virtual-wan-preview"></a>Vad är Azure Virtual WAN? (Förhandsversion)

Azure Virtual WAN är en nätverkstjänst som tillhandahåller optimerade och automatiserade anslutningar mellan olika platser via Azure. Med Virtual WAN kan du ansluta och konfigurera platsspecifika enheter så att de kommunicerar med Azure. Detta kan göras antingen manuellt eller med enheter från önskad leverantör via en Virtual WAN-partner. Leverantörsenheter är enkla att använda, ansluta och hantera. Med de inbyggda instrumentpanelerna i Azure WAN kan du snabbt felsöka problem och enkelt visa anslutningar mellan platser i hög skala.

> [!IMPORTANT]
> Azure Virtual WAN finns för närvarande som en allmänt tillgänglig förhandsversion. Om du vill använda Virtual WAN måste du [registrera dig för förhandsversionen](#enroll).
>
> Den allmänt tillgängliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln ger en snabb överblick över nätverksanslutningarna för arbetsbelastningar i och utanför Azure. Virtual WAN har följande fördelar:

* **Integrerade anslutningslösningar i nav- och ekernätverk:** Automatisera plats-till-plats-konfigurationen och anslutningen mellan lokala platser och ett Azure-nav från flera olika källor, inklusive Virtual WAN-partnerlösningar.
* **Automatiserad installation och konfiguration av ekerplatser:** Anslut enkelt dina virtuella nätverk och arbetsbelastningar till Azure-navet.
* **Intuitiv felsökning:** Du kan följa flödet från slutpunkt till slutpunkt i Azure och använda informationen för att vidta nödvändiga åtgärder.

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan.png)

## <a name="vendor"></a>Arbeta med en Virtual WAN-partner

1. Platsenhetens (VPN/SDWAN) kontrollant autentiseras för export av platsspecifik information till Azure med hjälp av ett Azure-tjänstobjekt.
2. Platsenhetens (VPN/SDWAN) kontrollant tar emot Azure-anslutningskonfigurationen och uppdaterar den lokala enheten. På så vis automatiseras konfigurationshämtningen, redigeringen och uppdateringen av den lokala VPN-enheten.
3. När enheten har rätt Azure-konfiguration upprättas en plats-till-plats-anslutning (två aktiva tunnlar) till Azures WAN-nätverk. Azure kräver att platskontrollanten (VPN/SDWAN) har stöd för IKEv2. BGP är valfritt.

## <a name="resources"></a>Virtual WAN-resurser

Om du vill konfigurera ett virtuellt WAN från slutpunkt till slutpunkt skapar du följande resurser:

* **virtualWAN:** virtualWAN-resursen representerar ett virtuellt överlägg på ditt Azure-nätverk och består av en samling med flera resurser. Den innehåller länkar till alla virtuella nav som ska ingå i det virtuella WAN-nätverket. Virtual WAN-resurser är isolerade från varandra och kan inte innehålla ett gemensamt nav. Virtuella nav i Virtual WAN-nätverk kommunicerar inte med varandra.

* **Plats:** Platsresursen, eller vpnsite, representerar din lokala VPN-enhet och dess inställningar. Om du arbetar med en Virtual WAN-partner har du en inbyggd lösning som hjälper dig att automatiskt exportera den här informationen till Azure.

* **Nav:** Ett virtuellt nav är ett Microsoft-hanterat virtuellt nätverk. Navet innehåller olika tjänstslutpunkter för anslutning från ditt lokala nätverk (vpnsite). Navet är kärnan i ditt nätverk i en region. Det kan bara finnas ett nav per Azure-region. När du skapar ett nav med hjälp av Azure Portal skapas automatiskt ett virtuellt navnätverk och en virtuell nav-vpngateway.

  En nav-gateway är inte detsamma som en virtuell nätverksgateway som du använder för ExpressRoute och VPN Gateway. När du använder Virtual WAN skapar du exempelvis inte en plats-till-plats-anslutning från din lokala plats direkt till ditt virtuella nätverk. I stället skapar du en plats-till-plats-anslutning till navet. Trafiken passerar alltid nav-gatewayen. Det innebär att dina virtuella nätverk inte behöver egna gateways för virtuella nätverk. Med Virtual WAN kan dina virtuella nätverk enkelt dra nytta av skalning via det virtuella navet och den virtuella nav-gatewayen. 

* **Anslutning mellan nav och virtuellt nätverk:** Den här resursen används för att sömlöst ansluta navet till ditt virtuella nätverk. För närvarande kan du bara ansluta till virtuella nätverk som finns i samma navregion.

##<a name="enroll"></a>Registrera dig för förhandsversionen

Du måste registrera din prenumeration i förhandsversionen innan du kan konfigurera Virtual WAN. Annars kan du inte arbeta med Virtual WAN på portalen. Skicka ett e-postmeddelande till <azurevirtualwan@microsoft.com> med ditt prenumerations-ID om du vill registrera dig. Du får ett e-postmeddelande tillbaka när din prenumeration har registrerats.

## <a name="faq"></a>Vanliga frågor och svar

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="feedback"></a>Feedback för förhandsversionen

Vi vill gärna ha din feedback. Skicka ett e-postmeddelande till <azurevirtualwan@microsoft.com> om du vill rapportera problem eller lämna feedback (positiv eller negativ) om Virtual WAN. Ange ditt företagsnamn i ”[ ]” i ämnesraden. Ange även ditt prenumerations-ID om du rapporterar ett problem.

## <a name="next-steps"></a>Nästa steg

Om du vill skapa en plats-till-plats-anslutning med Virtual WAN kan du antingen arbeta med en [Virtual WAN-partner](https://aka.ms/virtualwan) eller skapa anslutningen manuellt. Om du vill skapa anslutningen manuellt läser du [Skapa en plats-till-plats-anslutning med hjälp av Virtual WAN](virtual-wan-site-to-site-portal.md) (Create a Site-to-Site connection using Virtual WAN).