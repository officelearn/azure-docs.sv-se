---
title: "ExpressRoute-anslutningsmodeller: Anslut till Microsoft Azure via nätverkstjänstleverantörer, utbyten och Ethernet-leverantörer | Microsoft Docs"
description: "I den här artikeln beskrivs olika lägen för anslutningen mellan kundens nätverk och Microsoft Azure-, Office 365- och Dynamics 365-tjänster. Kunder kan använda MPLS-leverantörer, molnutbyten och Ethernet-leverantörer."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: cherylmc
ms.openlocfilehash: 00f97da2189491103c461b49ac82feb92d8f4b9b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="expressroute-connectivity-models"></a>ExpressRoute-anslutningsmodeller
Du kan skapa en anslutning mellan ditt lokala nätverk och Microsoft-molnet på tre olika sätt, [CloudExchange-samplacering](#CloudExchange), [Ethernet-anslutning mellan punkter](#Ethernet) och [anslutning alla-till-alla (IPVPN)](#IPVPN). Anslutningsleverantörer kan erbjuda en eller flera modeller för anslutningen. Du kan rådfråga din anslutningsleverantör om vilken modell som passar dig bäst.
<br><br>

![Modelldiagram för ExpressRoute-anslutning](./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png)

## <a name="CloudExchange"></a>Samordnad i ett molnutbyte
Om du är samordnad i en anläggning med ett molnutbyte, kan du beställa virtuella korsanslutningar till Microsoft-molnet via samordningsleverantörens Ethernet-utbyte. Samordningsleverantörer kan erbjuda Layer-2-anslutningar eller hanterade Layer 3-korsanslutningar mellan infrastrukturen i samordningsanläggningen och Microsoft-molnet.

## <a name="Ethernet"></a>Anslutningar mellan punkter med Ethernet
Du kan ansluta dina lokala datacenter/kontor till Microsoft-molnet via ”point-to-point”-länkar med Ethernet. ”Point-to-Point”-leverantörer kan erbjuda Layer-2-anslutningar eller hanterade Layer 3-anslutningar mellan din webbplats och Microsoft-molnet.

## <a name="IPVPN"></a>”Any-to-any”-nätverk (IPVPN)
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