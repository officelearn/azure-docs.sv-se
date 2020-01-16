---
title: 'Azure VPN Gateway: Konfigurera aviseringar för diagnostiska logg händelser'
description: Steg för att konfigurera aviseringar för VPN Gateway diagnostiska logg händelser
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: eb8f0204771b204af740c4ddc8e359499520a012
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045947"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Konfigurera aviseringar för diagnostiska logg händelser från VPN Gateway

Den här artikeln hjälper dig att ställa in aviseringar baserat på diagnostiska logg händelser från Azure VPN Gateway med Azure Log Analytics. 

Följande loggar är tillgängliga i Azure:

|***Namn*** | ***Beskrivning*** |
|---        | ---               |
|GatewayDiagnosticLog | Innehåller diagnostikloggar för konfigurations händelser för gateway, primära ändringar och underhålls händelser |
|TunnelDiagnosticLog | Innehåller ändringar av tunnel tillstånds händelser. Tunnel anslutning/från kopplings händelser har en sammanfattande orsak för tillstånds ändringen om det är tillämpligt |
|RouteDiagnosticLog | Loggar ändringar av statiska vägar och BGP-händelser som inträffar på gatewayen |
|IKEDiagnosticLog | Loggar IKE-kontrollmeddelanden och-händelser på gatewayen |
|P2SDiagnosticLog | Loggar punkt-till-plats-kontroll meddelanden och händelser på gatewayen |

## <a name="setup"></a>Konfigurera aviseringar

Följande exempel steg skapar en avisering för en från kopplings händelse som omfattar en plats-till-plats-VPN-tunnel:


1. I Azure Portal söker du efter **Log Analytics** under **alla tjänster** och väljer **Log Analytics arbets ytor**.

   ![Val för att gå till Log Analytics arbets ytor](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Create")

2. Välj **skapa** på sidan **Log Analytics** .

   ![Log Analytics sida med knappen Skapa](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Välj")

3. Välj **Skapa ny** och fyll i informationen.

   ![Information om hur du skapar en arbets yta för Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Välj")

4. Hitta din VPN-gateway på bladet **övervaka** > **diagnostikinställningar** .

   ![Val för att hitta VPN-gatewayen i diagnostikinställningar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Välj")

5. Om du vill aktivera diagnostik dubbelklickar du på gatewayen och väljer sedan **Aktivera diagnostik**.

   ![Val för att aktivera diagnostik](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Välj")

6. Fyll i informationen och kontrol lera att **Skicka till Log Analytics** och **TunnelDiagnosticLog** är markerat. Välj Log Analytics arbets ytan som du skapade i steg 3.

   ![Markerade kryss rutor](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Välj")

   > [!NOTE]
   > Det kan ta några timmar innan data visas initialt.

7. Gå till översikten för den virtuella nätverks-Gateway-resursen och välj **aviseringar** från fliken **övervakning** . Skapa sedan en ny varnings regel eller redigera en befintlig aviserings regel.

   ![Val för att skapa en ny varnings regel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Välj")

   ![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Välj")
8. Välj arbets ytan Log Analytics och resursen.

   ![Val för arbets yta och resurs](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Välj")

9. Välj **anpassad loggs ökning** som signal logik under **Lägg till villkor**.

   ![Val för en anpassad loggs ökning](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Välj")

10. Ange sedan följande fråga i textrutan **Sökfråga**. Ersätt värdena i < > och TimeGenerated efter behov.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Ange tröskelvärdet till 0 och välj **klart**.

    ![Ange en fråga och välja ett tröskelvärde](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Välj")

11. På sidan **Skapa regel** väljer du **Skapa ny** under avsnittet **Åtgärds grupper** . Fyll i informationen och välj **OK**.

    ![Information om en ny åtgärds grupp](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Välj")

12. På sidan **Skapa regel** fyller du i informationen för att **Anpassa åtgärder** och kontrollerar att rätt namn visas i avsnittet **Åtgärds grupp namn** . Välj **skapa aviserings regel** för att skapa regeln.

    ![Val för att skapa en regel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Välj")

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar aviseringar för tunnel mått finns i [Konfigurera aviseringar på VPN Gateway mått](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
