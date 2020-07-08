---
title: 'Azure-ExpressRoute: anslutnings modeller'
description: I den här artikeln beskrivs olika anslutnings lägen mellan kundens nätverk och Microsoft Azure och Office 365-tjänster. Kunder kan använda MPLS-leverantörer, molnutbyten och Ethernet-leverantörer.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: 375d2f9d3b455c0495c69f2b23d62b1ab6522710
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84710464"
---
# <a name="expressroute-connectivity-models"></a>ExpressRoute-anslutningsmodeller
Du kan skapa en anslutning mellan ditt lokala nätverk och Microsoft-molnet på tre olika sätt, [CloudExchange-samplacering](#CloudExchange), [Ethernet-anslutning mellan punkter](#Ethernet) och [anslutning alla-till-alla (IPVPN)](#IPVPN). Anslutningsleverantörer kan erbjuda en eller flera modeller för anslutningen. Du kan rådfråga din anslutningsleverantör om vilken modell som passar dig bäst.
<br><br>

![Modelldiagram för ExpressRoute-anslutning](./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png)

## <a name="co-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>Samplacerade i ett moln utbyte
Om du är samordnad i en anläggning med ett molnutbyte, kan du beställa virtuella korsanslutningar till Microsoft-molnet via samordningsleverantörens Ethernet-utbyte. Samordningsleverantörer kan erbjuda Layer-2-anslutningar eller hanterade Layer 3-korsanslutningar mellan infrastrukturen i samordningsanläggningen och Microsoft-molnet.

## <a name="point-to-point-ethernet-connections"></a><a name="Ethernet"></a>Punkt-till-punkt-Ethernet-anslutningar
Du kan ansluta dina lokala datacenter/kontor till Microsoft-molnet via ”point-to-point”-länkar med Ethernet. ”Point-to-Point”-leverantörer kan erbjuda Layer-2-anslutningar eller hanterade Layer 3-anslutningar mellan din webbplats och Microsoft-molnet.

## <a name="any-to-any-ipvpn-networks"></a><a name="IPVPN"></a>Alla-till-alla (IPVPN)-nätverk
Du kan integrera ditt WAN med Microsoft-molnet. IPVPN-leverantörer (vanligtvis MPLS VPN) erbjuder ”any-to-any”-anslutningar mellan dina avdelningskontor och datacentra. Microsoft-molnet kan kopplas samman med ditt WAN-nätverk så att det ser ut precis som andra avdelningskontor. WAN-leverantörer erbjuder vanligtvis hanterad Layer 3-anslutning. Alla ExpressRoute-funktioner och egenskaper är identiska i ovanstående anslutningsmodeller. 

## <a name="next-steps"></a>Nästa steg
* Läs mer om ExpressRoute-anslutningar och routningsdomäner. Se [ExpressRoute-anslutningar och routningsdomäner](expressroute-circuit-peerings.md).
* Läs mer om ExpressRoute-funktioner. Se [Teknisk översikt för ExpressRoute](expressroute-introduction.md)
* Hitta en tjänstleverantör. Se [ExpressRoute-partners och peeringplatser](expressroute-locations.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).
* Se kraven för [routning](expressroute-routing.md), [NAT](expressroute-nat.md) och [QoS](expressroute-qos.md).
* Konfigurera ExpressRoute-anslutningen.
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurera routning](expressroute-howto-routing-portal-resource-manager.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md)