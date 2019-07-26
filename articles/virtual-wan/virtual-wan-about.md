---
title: Översikt över Azure Virtual WAN | Microsoft Docs
description: Lär dig mer om det virtuella WAN-nätverket med automatisk skalbar anslutning från gren till gren, tillgängliga regioner och partner.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 07/22/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: f1576e963f9c25821b5e3f57907662e3d86df4e0
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406343"
---
# <a name="what-is-azure-virtual-wan"></a>Vad är Azure Virtual WAN?

Azure Virtual WAN är en nätverks tjänst som tillhandahåller optimerad och automatiserad gren anslutning till och via Azure. Azure-regioner fungerar som hubbar som du kan välja att ansluta dina grenar till. Du kan använda Azures stamnät för att även ansluta grenar och dra nytta av anslutningar från förgrening till VNet. Vi har en lista över partners som stöder anslutnings automatisering med Azure Virtual WAN VPN. Mer information finns i artikeln om [virtuella WAN-partners och platser](virtual-wan-locations-partners.md) .

Azure Virtual WAN ger tillsammans många Azure-Molntjänster som VPN-tjänster, till exempel plats-till-plats-VPN och ExpressRoute till ett enda drifts gränssnitt. Anslutning till Azure-virtuella nätverk upprättas med hjälp av virtuella nätverks anslutningar.

ExpressRoute för virtuellt WAN är för närvarande en för hands version.

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan1.png)

Den här artikeln ger en snabb överblick över nätverks anslutningen i Azure Virtual WAN. Virtual WAN har följande fördelar:

* **Integrerade anslutnings lösningar i hubb och eker:** Automatisera plats-till-plats-konfiguration och anslutning mellan lokala platser och en Azure-hubb.
* **Automatiserad eker-installation och konfiguration:** Anslut dina virtuella nätverk och arbets belastningar till Azure-hubben sömlöst.
* **Intuitiv fel sökning:** Du kan se flödet från slut punkt till slut punkt i Azure och sedan använda den här informationen för att utföra nödvändiga åtgärder.

## <a name="resources"></a>Virtual WAN-resurser

Om du vill konfigurera ett virtuellt WAN-nätverk från slut punkt till slut punkt skapar du följande resurser:

* **virtualWAN:** VirtualWAN-resursen representerar ett virtuellt överlägg för ditt Azure-nätverk och är en samling av flera resurser. Den innehåller länkar till alla virtuella nav som ska ingå i det virtuella WAN-nätverket. Virtual WAN-resurser är isolerade från varandra och kan inte innehålla ett gemensamt nav. Virtuella nav i Virtual WAN-nätverk kommunicerar inte med varandra. Egenskapen "Tillåt gren till gren trafik" aktiverar trafik mellan VPN-platser samt VPN-till-ExpressRoute (för närvarande i för hands version) aktiverade platser.

* **)** En virtuell hubb är ett Microsoft-hanterat virtuellt nätverk. Navet innehåller olika tjänstslutpunkter för anslutning från ditt lokala nätverk (vpnsite). Navet är kärnan i ditt nätverk i en region. Det kan bara finnas ett nav per Azure-region. När du skapar ett nav med hjälp av Azure-portalen skapas ett virtuellt navnätverk och en virtuell nav-vpngateway.

  En nav-gateway är inte detsamma som en virtuell nätverksgateway som du använder för ExpressRoute och VPN Gateway. När du använder ett virtuellt WAN-nätverk kan du till exempel inte skapa en plats-till-plats-anslutning från din lokala plats direkt till ditt VNet. I stället skapar du en plats-till-plats-anslutning till hubben. Trafiken passerar alltid nav-gatewayen. Det innebär att dina virtuella nätverk inte behöver egna gateways för virtuella nätverk. Med Virtual WAN kan dina virtuella nätverk enkelt dra nytta av skalning via det virtuella navet och den virtuella nav-gatewayen.

* **Hubb anslutning till virtuellt nätverk:** Nätverks anslutnings resursen för hubben används för att ansluta navet sömlöst till det virtuella nätverket. För närvarande kan du bara ansluta till virtuella nätverk som finns i samma navregion.

* **NAV väg tabell:**  Du kan skapa en virtuell nav väg och tillämpa den på väg tabellen för virtuella hubbar. Du kan använda flera vägar i routningstabellen för den virtuella hubben.

**Ytterligare virtuella WAN-resurser**

  * **Plats** Den här resursen används endast för plats-till-plats-anslutningar. Plats resursen är **vpnsite**. Den representerar din lokala VPN-enhet och dess inställningar. Om du arbetar med en Virtual WAN-partner har du en inbyggd lösning som hjälper dig att automatiskt exportera den här informationen till Azure.

## <a name="connectivity"></a>Koppling

Det virtuella WAN-nätverket tillåter två typer av anslutningar: Plats-till-plats och ExpressRoute (för hands version).

### <a name="s2s"></a>Plats-till-plats-VPN-anslutningar

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan.png)

När du skapar en virtuell WAN-plats-till-plats-anslutning kan du arbeta med en tillgänglig partner. Om du inte vill använda en partner kan du konfigurera anslutningen manuellt. Mer information finns i [skapa en plats-till-plats-anslutning med hjälp av virtuellt WAN](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Arbets flöde för virtuell WAN-partner

När du arbetar med en virtuell WAN-partner är arbets flödet:

1. Platsenhetens (VPN/SDWAN) kontrollant autentiseras för export av platsspecifik information till Azure med hjälp av ett [Azure-tjänstobjekt](../active-directory/develop/howto-create-service-principal-portal.md).
2. Platsenhetens (VPN/SDWAN) kontrollant tar emot Azure-anslutningskonfigurationen och uppdaterar den lokala enheten. På så vis automatiseras konfigurationshämtningen, redigeringen och uppdateringen av den lokala VPN-enheten.
3. När enheten har rätt Azure-konfiguration upprättas en plats-till-plats-anslutning (två aktiva tunnlar) till Azures WAN-nätverk. Azure stöder både IKEv1 och IKEv2. BGP är valfritt.

#### <a name="partners"></a>Partner för virtuella WAN-anslutningar från plats till plats

En lista över tillgängliga partners och platser finns i artikeln [virtuella WAN-partners och platser](virtual-wan-locations-partners.md) .


### <a name="er"></a>ExpressRoute-anslutningar (förhandsversion)

Med ExpressRoute kan du ansluta ett lokalt nätverk till Azure över en privat anslutning. Information om hur du skapar anslutningen finns i [skapa en ExpressRoute-anslutning med hjälp av virtuellt WAN](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Platser

Plats information finns i artikeln [virtuella WAN-partners och platser](virtual-wan-locations-partners.md) .

## <a name="faq"></a>Vanliga frågor och svar

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Nästa steg

[Skapa en plats-till-plats-anslutning med hjälp av virtuellt WAN](virtual-wan-site-to-site-portal.md)
