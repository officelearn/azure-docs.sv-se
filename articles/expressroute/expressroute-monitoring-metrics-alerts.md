---
title: Övervakning, mått och aviseringar – Azure ExpressRoute | Microsoft Docs
description: Den här sidan innehåller information om ExpressRoute-övervakning
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 40e5561c9a55595340568ec660cbc6dd6e1eab51
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672077"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Övervakning, mått och aviseringar i ExpressRoute

Den här artikeln hjälper dig att förstå ExpressRoute-övervakning, mått och aviseringar med Azure Monitor. Azure Monitor är en plats du behöver för alla mått, aviseringar, diagnostikloggar för alla Azure.
 
>[!NOTE]
>Med hjälp av **klassiska mått** rekommenderas inte.
>

## <a name="expressroute-metrics"></a>ExpressRoute mått

Om du vill visa **måtten**navigerar du till *Azure Monitor* sidan och klickar på *mått*. Om du vill visa **ExpressRoute** -mått, filer av resurs typ *ExpressRoute-kretsar*. Om du vill visa **Global Reach** mått filtrerar du efter resurs typ *ExpressRoute-kretsar* och väljer en ExpressRoute krets resurs som har Global Reach aktiverat. Om du vill visa **ExpressRoute Direct** -mått, filtrera resurs typ efter *ExpressRoute-portar*. 

När ett mått har valts tillämpas standard agg regeringen. Du kan också använda dela upp som visar måttet med olika dimensioner.

### <a name="available-metrics"></a>Tillgängliga mått
|**Mått**|**Kategori**|**Dimension (er)**|**Funktion (er)**|
| --- | --- | --- | --- |
|ARP-tillgänglighet|Tillgänglighet|<ui><li>Peer (primär/sekundär ExpressRoute-router)</ui></li><ui><li> Peering-typ (privat/offentlig/Microsoft)</ui></li>|ExpressRoute|
|BGP-tillgänglighet|Tillgänglighet|<ui><li> Peer (primär/sekundär ExpressRoute-router)</ui></li><ui><li> Peering-typ</ui></li>|ExpressRoute|
|BitsInPerSecond|Trafik|<ui><li> Peering-typ (ExpressRoute)</ui></li><ui><li>Länk (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Trafik| <ui><li>Peering-typ (ExpressRoute)</ui></li><ui><li> Länk (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|Trafik|<ui><li>Skey krets-(tjänst nyckel)</ui></li>|Global räckvidd|
|GlobalReachBitsOutPerSecond|Trafik|<ui><li>Skey krets-(tjänst nyckel)</ui></li>|Global räckvidd|
|AdminState|Fysisk anslutning|Länka|ExpressRoute Direct|
|LineProtocol|Fysisk anslutning|Länka|ExpressRoute Direct|
|RxLightLevel|Fysisk anslutning|<ui><li>Operationsföljdslänkkod</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fysisk anslutning|<ui><li>Operationsföljdslänkkod</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Användning av *GlobalGlobalReachBitsInPerSecond* och *GlobalGlobalReachBitsOutPerSecond* visas bara om minst en global Reach anslutning har upprättats.
>

## <a name="circuits-metrics"></a>Krets mått

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>BITS in och ut-mått för alla peer kopplingar

Du kan visa mått för alla peer kopplingar på en angiven ExpressRoute-krets.

![Kretsen mått](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>BITS in och ut-mått per peering

Du kan visa mått för privat, offentlig och Microsoft-peering i bitar per sekund.

![Mått per peering](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>BGP-tillgänglighet – dela efter peer  

Du kan visa nära real tids tillgänglighet för BGP över peering och peer-datorer (primära och sekundära ExpressRoute-routrar). Den här instrument panelen visar primär BGP-sessionen för privat peering och den andra BGP-sessionen för privat peering. 

![BGP-tillgänglighet per peer](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>ARP-tillgänglighet – dela via peering  

Du kan visa nära real tids tillgänglighet för [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) över peering och peer-datorer (primära och sekundära ExpressRoute-routrar). Den här instrument panelen visar ARP-sessionen för privata peering på båda peer-datorerna, men slutfört för Microsoft-peering mellan peering. Standard agg regeringen (Average) utnyttjades över båda peer-datorerna.  

![ARP-tillgänglighet per peer](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>ExpressRoute direkt mått

### <a name="admin-state---split-by-link"></a>Admin-tillstånd – dela med länk
Du kan visa admin-tillstånd för varje länk i ExpressRoute Direct-port paret.

![status för er Direct-administratör](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bitar i per sekund – dela med länk
Du kan visa bitarna i per sekund över båda länkarna i ExpressRoute Direct-port paret. 

![er direkt bitar per sekund](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bitar ut per sekund – dela med länk
Du kan också Visa bitarna per sekund över båda länkarna i ExpressRoute Direct-port paret. 

![antal återställnings direkt bitar per sekund](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Rad protokoll – dela med länk
Du kan visa rad protokollet för varje länk i ExpressRoute Direct-port paret.

![direkt anslutnings protokoll för er](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>RX ljus nivå – dela med länk
Du kan visa mottagnings ljus nivån (den ljus nivå som ExpressRoute Direct-porten **tar emot**) för varje port. Friska mottagnings ljus nivåer ligger vanligt vis inom intervallet-10 till 0 dBm

![återställnings ljus nivå för er direkt linje](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>TX ljus nivå – dela med länk
Du kan visa avsändnings ljus nivån (den ljus nivå som ExpressRoute Direct-porten **överför**) för varje port. Felfria TX-ljusnivå ligger vanligt vis inom intervallet-10 till 0 dBm

![återställnings ljus nivå för er direkt linje](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

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
