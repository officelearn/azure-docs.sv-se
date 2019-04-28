---
title: Hur du ställer in aviseringar för Azure VPN Gateway diagnostiklogg händelser
description: Steg för att konfigurera aviseringar för VPN-Gateway diagnostiklogg händelser
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 39a6d2e6201dd0635149159cb3727fd3c40f710a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769743"
---
# <a name="setting-up-alerts-on-vpn-gateway-diagnostic-log-events"></a>Konfigurera aviseringar för VPN-Gateway diagnostiklogg händelser

Den här artikeln kan du ställa in aviseringar baserat på VPN-Gateway diagnostiklogg händelser.


## <a name="setup"></a>Konfigurera Azure Monitor-aviseringar baserat på diagnostiklogg händelser med hjälp av portalen

Exempel stegen nedan skapar en avisering för en plats-till-plats VPN-tunnel frånkoppling av händelse



1. Sök efter ”Log Analytics” under alla tjänster och välj ”Log Analytics-arbetsytor” ![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "skapa")

2. Klicka på ”Skapa” på sidan Log Analytics.
![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Välj")

3. Kontrollera ”Skapa ny” arbetsyta och Fyll i information.
![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Välj")

4. Hitta din VPN-gateway under ”övervakaren” > ”diagnostikinställningar” bladet ![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Välj")

5. Om du vill slå på diagnostik, dubbelklicka på gatewayen och klicka sedan på ”Aktivera diagnostik” ![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Välj")

6. Fyll i uppgifter och se till att ”skicka till Log Analytics” och ”TunnelDiagnosticLog” kontrolleras. Välj Log Analytics-arbetsyta som skapades i steg 3.
![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Välj")

7. Navigera till Resursöversikt för vnet-gateway och välj ”aviseringar” på fliken övervakning och sedan skapa en ny varningsregel eller redigera en befintlig aviseringsregel.
![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Välj")

8. Välj Log Analytics-arbetsytan och resursen.
![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Välj")

9. Välj ”anpassade log search” som signallogiken under ”Lägg till villkor” ![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Välj")

10. Ange följande fråga i textrutan ”sökfrågan” där du ersätter värdena i <> efter behov.

    AzureDiagnostics | där kategori == ”TunnelDiagnosticLog” och ResourceId == toupper (”<RESOURCEID OF GATEWAY>”) och TimeGenerated > ago(5m) och remoteIP_s == ”<REMOTE IP OF TUNNEL>” och status_s == ”frånkopplad”

    Ange tröskelvärdet på 0 och klicka på ”klar”

    ![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Välj")

11. Klicka på ”Skapa ny” på sidan ”Skapa regeln” under avsnittet ÅTGÄRDSGRUPPER. Fyll i informationen och klicka på OK

![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Välj")

12. På sidan ”Skapa regeln” Fyll sedan i informationen för ”Anpassa Action” och kontrollera att namnet på rätt åtgärd visas i avsnittet ”namn på åtgärdsgrupp”. Klicka på ”Skapa varningsregel” att skapa regeln.
![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Välj")

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera aviseringar för tunnel mått, se [ställa in aviseringar på VPN-Gateway-mått](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
