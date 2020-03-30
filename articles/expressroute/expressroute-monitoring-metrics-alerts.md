---
title: 'Azure ExpressRoute: Övervakning, mått och aviseringar'
description: Den här sidan innehåller information om ExpressRoute-övervakning
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436919"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Övervakning, mått och aviseringar i ExpressRoute

Den här artikeln hjälper dig att förstå ExpressRoute-övervakning, mått och aviseringar med Azure Monitor. Azure Monitor är en stop shop för alla mått, aviseringar, diagnostikloggar i hela Azure.
 
>[!NOTE]
>Det rekommenderas inte att använda **klassiska mått.**
>

## <a name="expressroute-metrics"></a>ExpressRoute-mått

Om du vill visa **mått navigerar**du till sidan *Azure Monitor* och klickar på *Mått*. Om du vill visa **ExpressRoute-mått** filtrerar du efter *ExpressRoute-kretsar*för resurstyp . Om du vill visa **globala räckviddsmått** filtrerar du efter *ExpressRoute-kretsar* för resurstyp och väljer en ExpressRoute-kretsresurs som har Global Reach aktiverat. Om du vill visa **ExpressRoute Direct-mått** filtrerar du Resurstyp efter *ExpressRoute-portar*. 

När ett mått har valts tillämpas standardaggregeringen. Du kan också använda delning, som visar måttet med olika dimensioner.

### <a name="available-metrics"></a>Tillgängliga mått
|**Mått**|**Kategori**|**Dimension(er)**|**Funktioner**|
| --- | --- | --- | --- |
|ARP-tillgänglighet|Tillgänglighet|<ui><li>Peer (primär/sekundär ExpressRoute-router)</ui></li><ui><li> Peering-typ (privat/offentlig/Microsoft)</ui></li>|ExpressRoute|
|Bgp-tillgänglighet|Tillgänglighet|<ui><li> Peer (primär/sekundär ExpressRoute-router)</ui></li><ui><li> Peering-typ</ui></li>|ExpressRoute|
|BitsInPerSecond|Trafik|<ui><li> Peering-typ (ExpressRoute)</ui></li><ui><li>Länk (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Trafik| <ui><li>Peering-typ (ExpressRoute)</ui></li><ui><li> Länk (ExpressRoute Direct) | <ui><li>ExpressRoute (ort)<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|Trafik|<ui><li>Peered Circuit Skey (servicenyckel)</ui></li>|Global räckvidd|
|GlobalReachBitsOutPerSecond|Trafik|<ui><li>Peered Circuit Skey (servicenyckel)</ui></li>|Global räckvidd|
|AdminState (administrativa)|Fysisk anslutning|Länk|ExpressRoute Direct|
|LineProtocol (LinjeProtokol)|Fysisk anslutning|Länk|ExpressRoute Direct|
|RxLightLevel|Fysisk anslutning|<ui><li>Länk</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fysisk anslutning|<ui><li>Länk</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Med *GlobalGlobalReachBitsInPerSecond* och *GlobalGlobalReachBitsOutPerSecond* visas endast om minst en Global Reach-anslutning upprättas.
>

## <a name="circuits-metrics"></a>Tkretsmått

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bitar in och ut - Mått för alla peerings

Du kan visa mått över alla peerings på en viss ExpressRoute-krets.

![kretsmått](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Bitar in och ut - mått per peering

Du kan visa mått för privat, offentlig och Microsoft-peering i bitar/sekund.

![mått per peering](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>BGP-tillgänglighet - Delad efter peer  

Du kan visa nära realtidstillgängligheten för BGP över peerings och peer-datorer (primära och sekundära ExpressRoute-routrar). Den här instrumentpanelen visar den primära BGP-sessionen för privat peering och den andra BGP-sessionen nedåt för privat peering. 

![BGP-tillgänglighet per peer](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>ARP-tillgänglighet - Dela efter peering  

Du kan visa nästan realtidstillgänglighet för [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) över peerings och peer -datorer (primära och sekundära ExpressRoute-routrar). Den här instrumentpanelen visar den privata peering-ARP-sessionen uppåt i båda peer-datorerna, men slutförs för Microsoft-peering över peerings. Standardaggregering (Genomsnitt) användes för båda peer-datorerna.  

![ARP-tillgänglighet per peer](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct-mätvärden

### <a name="admin-state---split-by-link"></a>Admin State - Dela efter länk
Du kan visa administratörstillståndet för varje länk i ExpressRoute Direct-portparet.

![direkt admin tillstånd](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bitar i per sekund - Dela efter länk
Du kan visa bitarna per sekund över båda länkarna i ExpressRoute Direct-portparet. 

![er direkta bitar i per sekund](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bitar ut per sekund - Dela efter länk
Du kan också visa bitarna per sekund över båda länkarna i ExpressRoute Direct-portparet. 

![direktbitar ut per sekund](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Linjeprotokoll - Dela efter länk
Du kan visa linjeprotokollet över varje länk till ExpressRoute Direct-portparet.

![direktlinjeprotokoll](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx Ljusnivå - Dela efter länk
Du kan visa Rx-ljusnivån (den ljusnivå som ExpressRoute Direct-porten **tar emot)** för varje port. Friska Rx-ljusnivåer ligger i allmänhet inom ett intervall på -10 till 0 dBm

![direktlinje Rx ljusnivå](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Tx Ljusnivå - Dela efter länk
Du kan visa Tx-ljusnivån (den ljusnivå som ExpressRoute Direct-porten **sänder)** för varje port. Friska Tx-ljusnivåer ligger i allmänhet inom ett intervall på -10 till 0 dBm

![direktlinje Rx ljusnivå](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute gateway-anslutningar i bitar/sekunder

![gateway-anslutningar](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>Aviseringar för ExpressRoute-gatewayanslutningar

1. För att konfigurera aviseringar navigerar du till **Azure Monitor**och klickar sedan på **Aviseringar**.

   ![aviseringar](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Klicka på **+Välj mål** och välj expressroutegatewayanslutningsresursen.

   ![Mål]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Definiera aviseringsinformationen.

   ![åtgärdsgrupp](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Definiera och lägg till åtgärdsgruppen.

   ![lägga till åtgärdsgrupp](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Aviseringar baserade på varje peering

 ![Vad](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurera aviseringar för aktivitetsloggar på kretsar

I **varningsvillkoren**kan du välja **Aktivitetslogg** för signaltypen och välja signalen.

  ![Annan](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Nästa steg

Konfigurera ExpressRoute-anslutningen.
  
  * [Skapa och ändra en krets](expressroute-howto-circuit-arm.md)
  * [Skapa och ändra peering-konfigurationen](expressroute-howto-routing-arm.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
