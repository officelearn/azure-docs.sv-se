---
title: Hur du konfigurerar aviseringar i Azure VPN Gateway-mått
description: Steg för att konfigurera aviseringar för VPN Gateway-mått
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 890b096acba601ec20efaac21155da84e77a1f31
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769473"
---
# <a name="setting-up-alerts-on-vpn-gateway-metrics"></a>Konfigurera aviseringar i mått för VPN-Gateway

Den här artikeln kan du ställa in aviseringar för VPN Gateway-mått. Azure monitor innehåller möjligheten att ställa in aviseringar för Azure-resurser. Aviseringar kan ställas in för virtuella nätverksgatewayer av typen ”VPN”.


|**Mått**   | **Enhet** | **Kornighet** | **Beskrivning** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Byte/s  | 5 minuter| Genomsnittlig kombinerade bandbreddsanvändningen för alla plats-till-plats-anslutningar på gatewayen.     |
|**P2SBandwidth**| Byte/s  | 1 minut  | Genomsnittlig kombinerade bandbreddsanvändningen för alla punkt-till-plats-anslutningar på gatewayen.    |
|**P2SConnectionCount**| Antal  | 1 minut  | Antal P2S-anslutningar på gatewayen.   |
|**TunnelAverageBandwidth** | Byte/s    | 5 minuter  | Genomsnittlig bandbreddsanvändningen av tunnlar som skapats på gatewayen. |
|**TunnelEgressBytes** | Byte | 5 minuter | Utgående trafik på tunnlar som skapats på gatewayen.   |
|**TunnelEgressPackets** | Antal | 5 minuter | Antal utgående paket i tunnlar som skapats på gatewayen.   |
|**TunnelEgressPacketDropTSMismatch** | Antal | 5 minuter | Antal utgående paket tas bort i på grund av felaktig matchning av TS-tunnlar. |
|**TunnelIngressBytes** | Byte | 5 minuter | Inkommande trafik på tunnlar som skapats på gatewayen.   |
|**TunnelIngressPackets** | Antal | 5 minuter | Antal inkommande paket i tunnlar som skapats på gatewayen.   |
|**TunnelIngressPacketDropTSMismatch** | Antal | 5 minuter | Antal inkommande paket som tas bort i på grund av felaktig matchning av TS-tunnlar. |


## <a name="setup"></a>Konfigurera Azure Monitor-aviseringar baserat på mått med hjälp av portalen

Exempel stegen nedan skapar en avisering på en gateway för: <br>

**Mått:** Tunnel Genomsnittlig bandbredd <br>
**Villkor:** bandbredd > 10 byte per sekund <br>
**Window:** 5 minuter <br>
**Aviseringsåtgärd:** E-post <br>



1. Navigera till den virtuella gateway-nätverksresursen och välj ”aviseringar” på fliken övervakning och sedan skapa en ny varningsregel eller redigera en befintlig aviseringsregel.

![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "skapa")

2. Välj din VPN-gateway som resursen.

![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Välj")

3. Välj ett mått för att konfigurera för aviseringen ![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Välj")
4. Konfigurera signallogiken. Det finns tre komponenter till signallogiken:

    a. Dimensioner: Om måttet har dimensioner, kan specifika dimensionsvärden väljas så att aviseringen utvärderar endast data av dimensionen. Det här är valfritt.<br>
    b. Villkor: Åtgärden att utvärdera mätvärdet.<br>
    c. Tid: Ange Granulariteten för mätvärden och tid för att utvärdera aviseringen på.<br>

![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Välj")

5. Om du vill visa de konfigurerade reglerna, klicka på ”Hantera Varningsregler” ![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Välj")

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera aviseringar för tunnel diagnostikloggar, se [ställa in aviseringar på VPN-gatewayens diagnostikloggar](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
