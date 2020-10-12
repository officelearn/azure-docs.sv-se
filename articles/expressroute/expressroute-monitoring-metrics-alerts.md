---
title: 'Azure-ExpressRoute: övervakning, mått och aviseringar'
description: Lär dig mer om Azure ExpressRoute-övervakning, mått och aviseringar med hjälp av Azure Monitor, det enda steget att handla för alla mått, varningar, diagnostikloggar i Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 08/25/2020
ms.author: duau
ms.openlocfilehash: 6f502b8ad8ac268cc937150f4effdf9edf8eef15
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91252637"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Övervakning, mått och aviseringar i ExpressRoute

Den här artikeln hjälper dig att förstå ExpressRoute-övervakning, mått och aviseringar med hjälp av Azure Monitor. Azure Monitor är ett stopp för alla mät värden, varningar, diagnostikloggar i hela Azure.
 
>[!NOTE]
>Det rekommenderas inte att använda **klassiska mått** .
>

## <a name="expressroute-metrics"></a>ExpressRoute mått

Om du vill visa **måtten**navigerar du till *Azure Monitor* sidan och klickar på *mått*. Om du vill visa **ExpressRoute** -mått filtrerar du efter resurs typ *ExpressRoute-kretsar*. Om du vill visa **Global Reach** mått filtrerar du efter resurs typ *ExpressRoute-kretsar* och väljer en ExpressRoute krets resurs som har Global Reach aktiverat. Om du vill visa **ExpressRoute Direct** -mått, filtrera resurs typ efter *ExpressRoute-portar*. 

När ett mått har valts tillämpas standard agg regeringen. Du kan också använda dela upp som visar måttet med olika dimensioner.

### <a name="available-metrics"></a>Tillgängliga mått

|**Mått**|**Kategori**|**Dimension (er)**|**Funktion (er)**|
| --- | --- | --- | --- |
|ARP-tillgänglighet|Tillgänglighet|<ui><li>Peer (primär/sekundär ExpressRoute-router)</ui></li><ui><li> Peering-typ (privat/offentlig/Microsoft)</ui></li>|ExpressRoute|
|BGP-tillgänglighet|Tillgänglighet|<ui><li> Peer (primär/sekundär ExpressRoute-router)</ui></li><ui><li> Peering-typ</ui></li>|ExpressRoute|
|BitsInPerSecond|Trafik|<ui><li> Peering-typ (ExpressRoute)</ui></li><ui><li>Länk (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Trafik| <ui><li>Peering-typ (ExpressRoute)</ui></li><ui><li> Länk (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|CPU-användning|Prestanda| <ui><li>Instans</ui></li>|ExpressRoute Virtual Network-Gateway|
|Paket per sekund|Prestanda| <ui><li>Instans</ui></li>|ExpressRoute Virtual Network-Gateway|
|GlobalReachBitsInPerSecond|Trafik|<ui><li>Skey krets-(tjänst nyckel)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Trafik|<ui><li>Skey krets-(tjänst nyckel)</ui></li>|Global Reach|
|AdminState|Fysisk anslutning|Länk|ExpressRoute Direct|
|LineProtocol|Fysisk anslutning|Länk|ExpressRoute Direct|
|RxLightLevel|Fysisk anslutning|<ui><li>Operationsföljdslänkkod</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fysisk anslutning|<ui><li>Operationsföljdslänkkod</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Användning av *GlobalGlobalReachBitsInPerSecond* och *GlobalGlobalReachBitsOutPerSecond* visas bara om minst en global Reach anslutning har upprättats.
>

## <a name="circuits-metrics"></a>Krets mått

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>BITS in och ut-mått för alla peer kopplingar

Du kan visa mått för alla peer kopplingar på en angiven ExpressRoute-krets.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="krets mått":::

### <a name="bits-in-and-out---metrics-per-peering"></a>BITS in och ut-mått per peering

Du kan visa mått för privat, offentlig och Microsoft-peering i bitar per sekund.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="krets mått":::

### <a name="bgp-availability---split-by-peer"></a>BGP-tillgänglighet – dela efter peer  

Du kan visa nära real tids tillgänglighet för BGP över peering och peer-datorer (primära och sekundära ExpressRoute-routrar). Den här instrument panelen visar primär BGP-sessionen för privat peering och den andra BGP-sessionen för privat peering. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="krets mått":::

### <a name="arp-availability---split-by-peering"></a>ARP-tillgänglighet – dela via peering  

Du kan visa nära real tids tillgänglighet för [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) över peering och peer-datorer (primära och sekundära ExpressRoute-routrar). Den här instrument panelen visar ARP-sessionen för privata peering på båda peer-datorerna, men slutfört för Microsoft-peering mellan peering. Standard agg regeringen (Average) utnyttjades över båda peer-datorerna.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="krets mått":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute direkt mått

### <a name="admin-state---split-by-link"></a>Admin-tillstånd – dela med länk

Du kan visa admin-tillstånd för varje länk i ExpressRoute Direct-port paret.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="krets mått":::

### <a name="bits-in-per-second---split-by-link"></a>Bitar i per sekund – dela med länk

Du kan visa bitarna i per sekund över båda länkarna i ExpressRoute Direct-port paret.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="krets mått":::

### <a name="bits-out-per-second---split-by-link"></a>Bitar ut per sekund – dela med länk

Du kan också Visa bitarna per sekund över båda länkarna i ExpressRoute Direct-port paret.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="krets mått":::

### <a name="line-protocol---split-by-link"></a>Rad protokoll – dela med länk

Du kan visa rad protokollet för varje länk i ExpressRoute Direct-port paret.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="krets mått":::

### <a name="rx-light-level---split-by-link"></a>RX ljus nivå – dela med länk

Du kan visa mottagnings ljus nivån (den ljus nivå som ExpressRoute Direct-porten **tar emot**) för varje port. Friska mottagnings ljus nivåer ligger vanligt vis inom intervallet-10 till 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="krets mått":::

### <a name="tx-light-level---split-by-link"></a>TX ljus nivå – dela med länk

Du kan visa avsändnings ljus nivån (den ljus nivå som ExpressRoute Direct-porten **överför**) för varje port. Felfria TX-ljusnivå ligger vanligt vis inom intervallet-10 till 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="krets mått":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute Virtual Network Gateway-mått

### <a name="cpu-utilization---split-instance"></a>PROCESSOR belastning – dela instans

Du kan visa processor användningen för gateway-instanserna.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="krets mått":::

### <a name="packets-per-second---split-by-instance"></a>Paket per sekund – dela per instans

Du kan visa paket per sekund som passerar gatewayen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="krets mått":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute Gateway-anslutningar i bitar/sekunder

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="krets mått":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Aviseringar för ExpressRoute Gateway-anslutningar

1. För att konfigurera aviseringar går du till **Azure Monitor**och väljer **aviseringar**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="krets mått":::
2. Klicka på **+ Välj mål** och välj anslutnings resurs för ExpressRoute Gateway.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="krets mått":::
3. Definiera aviserings informationen.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="krets mått":::
4. Definiera och Lägg till åtgärds gruppen.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="krets mått":::

## <a name="alerts-based-on-each-peering"></a>Aviseringar baserade på varje peering

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="krets mått":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurera aviseringar för aktivitets loggar på kretsar

I **aviserings villkoren**kan du välja **aktivitets logg** för signal typen och välja signalen.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="krets mått":::

## <a name="additional-metrics-in-log-analytics"></a>Ytterligare mått i Log Analytics

Du kan också Visa ExpressRoute-mått genom att gå till ExpressRoute-krets-resursen och välja fliken *loggar* . För alla mät värden som du frågar innehåller utdata de kolumner som visas nedan.

|**Kolumn**|**Typ**|**Beskrivning**|
| --- | --- | --- |
|TimeGrain|sträng|PT1M (metriska värden skickas varje minut)|
|Antal|real|Vanligt vis lika med 2 (varje MSEE: N pushar ett enda mått värde varje minut)|
|Minimum|real|Det minsta värdet av de två mät värdena som pushas av de två msee|
|Maximal|real|Högsta för de två metriska värdena som pushas av de två msee|
|Genomsnitt|real|Lika med (minst + max)/2|
|Totalt|real|Summan av de två mått värdena från båda msee (det huvudsakliga värdet som ska fokuseras på för det mått som har frågats)|
  
## <a name="next-steps"></a>Nästa steg

Konfigurera ExpressRoute-anslutningen.
  
* [Skapa och ändra en krets](expressroute-howto-circuit-arm.md)
* [Skapa och ändra peering-konfiguration](expressroute-howto-routing-arm.md)
* [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
