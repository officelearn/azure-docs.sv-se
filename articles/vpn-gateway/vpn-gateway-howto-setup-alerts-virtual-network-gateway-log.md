---
title: Konfigurera aviseringar för diagnostiklogg händelser från Azure VPN Gateway
description: Steg för att konfigurera aviseringar för VPN-Gateway diagnostiklogg händelser
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 3880c847c54136dfd3ba1ecfe0178565091e229f
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510209"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Konfigurera aviseringar för diagnostiklogg händelser från VPN-Gateway

Den här artikeln kan du ställa in aviseringar baserat på diagnostiklogg händelser från Azure VPN Gateway.

## <a name="setup"></a>Ställa in aviseringar

Följande exempel skapar en avisering för en frånkoppling av händelse som inbegriper en plats-till-plats VPN-tunnel:


1. I Azure-portalen, söker du efter **Log Analytics** under **alla tjänster** och välj **Log Analytics-arbetsytor**.

   ![Val för att gå till Log Analytics-arbetsytor](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "skapa")

2. Välj **skapa** på den **Log Analytics** sidan.

   ![Log Analytics-sida med knappen Skapa](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Välj")

3. Välj **Skapa ny** och Fyll i information.

   ![Information för att skapa en Log Analytics-arbetsyta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Välj")

4. Hitta din VPN-gateway på den **övervakaren** > **diagnostikinställningar** bladet.

   ![Val för att hitta VPN-gatewayen i diagnostikinställningar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Välj")

5. Om du vill slå på diagnostik, dubbelklicka på gatewayen och välj sedan **slå på diagnostik**.

   ![Val för att aktivera diagnostik](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Välj")

6. Fyll i uppgifter och se till att **skicka till Log Analytics** och **TunnelDiagnosticLog** har valts. Välj Log Analytics-arbetsyta som du skapade i steg 3.

   ![Markerade kryssrutor](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Välj")

7. Gå till översikten för den virtuella gateway-nätverksresursen och välj **aviseringar** från den **övervakning** fliken. Sedan skapar en ny varningsregel eller redigera en befintlig aviseringsregel.

   ![Val för att skapa en ny varningsregel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Välj")

   ![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Välj")
8. Välj Log Analytics-arbetsytan och resursen.

   ![Val för arbetsytan och resursen](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Välj")

9. Välj **anpassade loggsökning** som signallogiken under **Lägg till villkor**.

   ![Val för en anpassad loggsökning](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Välj")

10. Ange sedan följande fråga i textrutan **Sökfråga**. Ersätt värdena i <> efter behov.

     `AzureDiagnostics |
     where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
     remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"`

    Ange tröskelvärdet till 0 och välj **klar**.

    ![Att ange en fråga och välja ett tröskelvärde](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Välj")

11. På den **skapa regeln** väljer **Skapa ny** under den **ÅTGÄRDSGRUPPER** avsnittet. Fyll i information och välj **OK**.

    ![Information om en ny åtgärdsgrupp](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Välj")

12. På den **skapa regeln** fyller du i informationen för **anpassa åtgärder** och se till att rätt namn visas i den **namn på ÅTGÄRDSGRUPP** avsnittet. Välj **skapa varningsregel** att skapa regeln.

    ![Val för att skapa en regel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Välj")

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera aviseringar för tunnel mått, se [ställa in aviseringar på VPN-Gateway-mått](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
