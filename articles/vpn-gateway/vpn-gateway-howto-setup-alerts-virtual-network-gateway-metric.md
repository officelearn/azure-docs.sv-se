---
title: Konfigurera aviseringar för Azure VPN Gateway-mått
description: Steg för att konfigurera aviseringar om VPN Gateway-mått
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67605219"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Konfigurera aviseringar om MÄTvärden för VPN Gateway

Den här artikeln hjälper dig att ställa in aviseringar på Azure VPN Gateway-mått. Azure Monitor ger möjlighet att ställa in aviseringar för Azure-resurser. Du kan ställa in aviseringar för virtuella nätverksgateways av typen "VPN".


|**Mått**   | **Enhet** | **Precision** | **Beskrivning** | 
|---       | ---        | ---       | ---            | ---       |
|**GenomsnittligtBredd**| Byte/s  | 5 minuter| Genomsnittligt kombinerat bandbreddsutnyttjande av alla plats-till-plats-anslutningar på gatewayen.     |
|**P2SBandbredd**| Byte/s  | 1 minut  | Genomsnittligt kombinerat bandbreddsutnyttjande av alla point-to-site-anslutningar på gatewayen.    |
|**P2SConnectionCount**| Antal  | 1 minut  | Antal point-to-site-anslutningar på gatewayen.   |
|**TunnelAverageBandbredd** | Byte/s    | 5 minuter  | Genomsnittlig bandbreddsanvändning av tunnlar som skapats på gatewayen. |
|**TunnelEgressBytes** | Byte | 5 minuter | Utgående trafik på tunnlar som skapats på gatewayen.   |
|**TunnelEgressPackets** | Antal | 5 minuter | Antal utgående paket i tunnlar som skapats på gatewayen.   |
|**TunnelEgressPacketDroptSMismatch** | Antal | 5 minuter | Antal utgående paket som har tappats på tunnlar som orsakas av trafikväljaren matchar inte. |
|**TunnelIngressBytes** | Byte | 5 minuter | Inkommande trafik på tunnlar som skapats på gatewayen.   |
|**TunnelIngressPackets** | Antal | 5 minuter | Antal inkommande paket i tunnlar som skapats på gatewayen.   |
|**TunnelIngressPacketDroptSMismatch** | Antal | 5 minuter | Antal inkommande paket som har tappats på tunnlar som orsakas av trafikväljaren matchar inte. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Konfigurera Azure Monitor-aviseringar baserat på mått med hjälp av Azure-portalen

Följande exempelsteg skapar en avisering på en gateway för:

- **Mått:** TunnelAverageBandbredd
- **Skick:** Bandbredd > 10 byte/sekund
- **Fönster:** 5 minuter
- **Varningsåtgärd:** Email



1. Gå till den virtuella nätverksgatewayresursen och välj **Aviseringar** på fliken **Övervakning.** Skapa sedan en ny varningsregel eller redigera en befintlig varningsregel.

   ![Val för att skapa en varningsregel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Skapa")

2. Välj din VPN-gateway som resurs.

   ![Knappen Välj och VPN-gatewayen i listan över resurser](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Välj")

3. Välj ett mått som ska konfigureras för aviseringen.

   ![Valt mått i listan över mått](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Välj")
4. Konfigurera signallogiken. Det finns tre komponenter till den:

    a. **Dimensioner**: Om måttet har dimensioner kan du välja specifika dimensionsvärden så att aviseringen endast utvärderar data för den dimensionen. Dessa är valfria.

    b. **Villkor:** Det här är åtgärden för att utvärdera måttvärdet.

    c. **Tid**: Ange granulariteten för måttdata och tidsperioden för att utvärdera aviseringen.

   ![Information om hur du konfigurerar signallogik](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Välj")

5. Om du vill visa de konfigurerade reglerna väljer du **Hantera varningsregler**.

   ![Knapp för hantering av varningsregler](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Välj")

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera aviseringar för tunneldiagnostikloggar finns i [Konfigurera aviseringar på diagnostikloggar för VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
