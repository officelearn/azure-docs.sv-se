---
title: Övervakning, mått och aviseringar – Azure ExpressRoute | Microsoft Docs
description: Den här sidan innehåller information om ExpressRoute-övervakning
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 92ec03e20fb6e681a0afd14048449ad004ebca0c
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991499"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Övervakning, mått och aviseringar i ExpressRoute

Den här artikeln hjälper dig att förstå ExpressRoute-övervakning, mått och aviseringar med Azure Monitor. Azure Monitor är en plats du behöver för alla mått, aviseringar, diagnostikloggar för alla Azure.
 
>[!NOTE]
>Med hjälp av **klassiska mått** rekommenderas inte.
>

## <a name="circuit-metrics"></a>Kretsen mått

Navigera till **mått**, klickar på sidan ExpressRoute för kretsen som du vill övervaka. Under **övervakning**, du kan visa den **mått**. Välj bland måtten i listan nedan. Standard agg regeringen används. Alternativt kan du använda delning, som visar måtten med olika dimensioner.

### <a name="metrics-available"></a>Tillgängliga mått: 
* **Tillgänglighet** 
    * ARP-tillgänglighet
      * Tillgängliga dimensioner:
        * Peer (primär/sekundär ExpressRoute-router)
        * Peering-typ (privat/offentlig/Microsoft)
    * BGP-tillgänglighet
      * Tillgängliga dimensioner:
        * Peer (primär/sekundär ExpressRoute-router)
        * Peering-typ (privat/offentlig/Microsoft)
* **Aktiviteten**
    * BitsInPerSecond
      * Tillgängliga dimensioner:
        * Peering-typ (privat/offentlig/Microsoft)
    * BitsOutPerSecond
      * Tillgängliga dimensioner:
        * Peering-typ (privat/offentlig/Microsoft)
    * GlobalReachBitsInPerSecond
      * Tillgängliga dimensioner:
        * Skey krets-(tjänst nyckel)
    * GlobalReachBitsOutPerSecond
      * Tillgängliga dimensioner:
        * Skey krets-(tjänst nyckel)

>[!NOTE]
>Användning av *GlobalGlobalReachBitsInPerSecond* och *GlobalGlobalReachBitsOutPerSecond* visas bara om minst en global Reach anslutning har upprättats.
>

## <a name="bits-in-and-out---metrics-across-all-peerings"></a>BITS in och ut-mått för alla peer kopplingar

Du kan visa mått för alla peer kopplingar på en angiven ExpressRoute-krets.

![Kretsen mått](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="bits-in-and-out---metrics-per-peering"></a>BITS in och ut-mått per peering

Du kan visa mått för privat, offentlig och Microsoft-peering i bitar per sekund.

![Mått per peering](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="bgp-availability---split-by-peer"></a>BGP-tillgänglighet – dela efter peer  

Du kan visa nära real tids tillgänglighet för BGP över peering och peer-datorer (primära och sekundära ExpressRoute-routrar). Den här instrument panelen visar primär BGP-sessionen för privat peering och den andra BGP-sessionen för privat peering. 

![BGP-tillgänglighet per peer](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

## <a name="arp-availability---split-by-peering"></a>ARP-tillgänglighet – dela via peering  

Du kan visa nära real tids tillgänglighet för [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) över peering och peer-datorer (primära och sekundära ExpressRoute-routrar). Den här instrument panelen visar ARP-sessionen för privata peering på båda peer-datorerna, men slutfört för Microsoft-peering mellan peering. Standard agg regeringen (Average) utnyttjades över båda peer-datorerna.  

![ARP-tillgänglighet per peer](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute-gateway-anslutningar i bitar/sekunder

![gateway-anslutningar](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>Aviseringar för ExpressRoute-gateway-anslutningar

1. Om du vill konfigurera aviseringar, gå till **Azure Monitor**, klicka sedan på **aviseringar**.

   ![aviseringar](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Klicka på **+ Välj mål** och välj resursen som ExpressRoute-gateway-anslutning.

   ![mål]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Definiera Aviseringsinformationen.

   ![Åtgärdsgrupp](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Definiera och lägga till åtgärdsgruppen.

   ![Lägg till åtgärdsgrupp](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Aviseringar baserat på varje peering

 ![Vad](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurera aviseringar för aktivitetsloggar i kretsar

I den **aviseringsvillkoren**, kan du välja **aktivitetsloggen** för signaltyp och välj signalen.

  ![en annan](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Nästa steg

Konfigurera ExpressRoute-anslutningen.
  
  * [Skapa och ändra en krets](expressroute-howto-circuit-arm.md)
  * [Skapa och ändra peering-konfigurationen](expressroute-howto-routing-arm.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
