---
title: Övervakning, mått och aviseringar – Azure ExpressRoute | Microsoft Docs
description: Den här sidan innehåller information om ExpressRoute-övervakning
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: d78c110f3317f4dd9f16cbe243aeca437e9890a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60364728"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Övervakning, mått och aviseringar i ExpressRoute

Den här artikeln hjälper dig att förstå ExpressRoute-övervakning, mått och aviseringar med Azure Monitor. Azure Monitor är en plats du behöver för alla mått, aviseringar, diagnostikloggar för alla Azure.
 
>[!NOTE]
>Med hjälp av **klassiska mått** rekommenderas inte.
>

## <a name="circuit-metrics"></a>Kretsen mått

Navigera till **mått**, klickar på sidan ExpressRoute för kretsen som du vill övervaka. Under **övervakning**, du kan visa den **mått**. Välj BitsInPerSecond eller BitsOutPerSecond och aggregering. Du kan också kan du använda uppdelningen som visar mått per typ av peering.

![Kretsen mått](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="metrics-per-peering"></a>Mått per peering

Du kan visa mått för privat, offentlig och Microsoft-peering i bitar per sekund.

![Mått per peering](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

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
