---
title: Ställa in aviseringar på Azure VPN Gateway-mått
description: Steg för att konfigurera aviseringar för VPN Gateway-mått
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: e54dadbda0582095e8152ea30376d369177bfd86
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65509894"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Ställa in aviseringar på VPN-Gateway-mått

Den här artikeln kan du ställa in aviseringar i Azure VPN Gateway-mått. Azure Monitor innehåller möjligheten att ställa in aviseringar för Azure-resurser. Du kan ställa in aviseringar för virtuella nätverksgatewayer av typen ”VPN”.


|**Mått**   | **Enhet** | **Kornighet** | **Beskrivning** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Byte/s  | 5 minuter| Genomsnittlig kombinerade bandbreddsanvändningen för alla plats-till-plats-anslutningar på gatewayen.     |
|**P2SBandwidth**| Byte/s  | 1 minut  | Genomsnittlig kombinerade bandbreddsanvändningen för alla punkt-till-plats-anslutningar på gatewayen.    |
|**P2SConnectionCount**| Count  | 1 minut  | Antal punkt-till-plats-anslutningar på gatewayen.   |
|**TunnelAverageBandwidth** | Byte/s    | 5 minuter  | Genomsnittlig bandbreddsanvändningen av tunnlar som skapats på gatewayen. |
|**TunnelEgressBytes** | Byte | 5 minuter | Utgående trafik på tunnlar som skapats på gatewayen.   |
|**TunnelEgressPackets** | Count | 5 minuter | Antal utgående paket i tunnlar som skapats på gatewayen.   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 minuter | Antal utgående paket släpps på tunnlar på grund av trafikväljaren matchningsfel. |
|**TunnelIngressBytes** | Byte | 5 minuter | Inkommande trafik på tunnlar som skapats på gatewayen.   |
|**TunnelIngressPackets** | Count | 5 minuter | Antal inkommande paket i tunnlar som skapats på gatewayen.   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 minuter | Antal inkommande paket som tas bort i tunnlar på grund av trafikväljaren matchningsfel. |


## <a name="setup"></a>Konfigurera Azure Monitor-aviseringar baserat på mått med hjälp av Azure portal

Följande exempel skapar en avisering på en gateway för:

- **Mått:** TunnelAverageBandwidth
- **Villkor:** Bandbredd > 10 byte per sekund
- **Window:** 5 minuter
- **Aviseringsåtgärd:** E-post



1. Gå till resurs för gatewayen för virtuella nätverk och välj **aviseringar** från den **övervakning** fliken. Sedan skapar en ny varningsregel eller redigera en befintlig aviseringsregel.

   ![Val för att skapa en aviseringsregel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "skapa")

2. Välj din VPN-gateway som resursen.

   ![Knappen Välj och VPN-gateway i listan över resurser](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Välj")

3. Välj ett mått för att konfigurera för aviseringen.

   ![Valt mått i listan över mått](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Välj")
4. Konfigurera signallogiken. Det finns tre komponenter till den:

    a. **Dimensioner**: Om måttet har dimensioner kan välja du specifika dimensionsvärden så att aviseringen utvärderar endast data av dimensionen. Det här är valfritt.

    b. **Villkor**: Det här är åtgärden att utvärdera mätvärdet.

    c. **Tid**: Ange Granulariteten för mätvärden och tid för att utvärdera aviseringen.

   ![Information för att konfigurera signalera logic](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Välj")

5. Om du vill visa de konfigurerade reglerna, Välj **hantera Varningsregler**.

   ![Knappen för att hantera aviseringsregler](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Välj")

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera aviseringar för tunnel diagnostikloggar, se [ställa in aviseringar på VPN-gatewayens diagnostikloggar](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
