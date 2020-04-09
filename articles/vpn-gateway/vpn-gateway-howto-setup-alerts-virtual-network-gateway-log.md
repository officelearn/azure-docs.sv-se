---
title: 'Azure VPN Gateway: Konfigurera aviseringar om diagnostiklogghändelser'
description: Steg för att konfigurera aviseringar om diagnostiklogghändelser för VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 49510b26e0b2a9c69dd65faf0f343e86d1a068db
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878909"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Konfigurera aviseringar om diagnostiska logghändelser från VPN Gateway

Den här artikeln hjälper dig att ställa in aviseringar baserat på diagnostiska logghändelser från Azure VPN Gateway med Hjälp av Azure Log Analytics. 

Följande loggar är tillgängliga i Azure:

|***Namn*** | ***Beskrivning*** |
|---        | ---               |
|GatewayDiagnosticLog | Innehåller diagnostikloggar för gatewaykonfigurationshändelser, primära ändringar och underhållshändelser |
|TunnelDiagnosticLog | Innehåller händelser för ändring av tunneltillstånd. Tunnelanslutnings-/frånkopplingshändelser har en sammanfattad orsak till tillståndsändringen om tillämpligt |
|RouteDiagnosticLog | Loggar ändringar i statiska vägar och BGP-händelser som inträffar på gatewayen |
|IKEDiagnosticLog | Loggar IKE-kontrollmeddelanden och händelser på gatewayen |
|P2SDiagnosticLog | Loggar punkt-till-plats-kontrollmeddelanden och händelser på gatewayen. Information om anslutningskällan tillhandahålls endast för IKEv2-anslutningar |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Konfigurera aviseringar i Azure-portalen

I följande exempelsteg skapas en avisering för en frånkopplingshändelse som involverar en VPN-tunnel från plats till plats:


1. Sök efter **Logganalys** i Azure-portalen under **Alla tjänster** och välj **Log Analytics-arbetsytor**.

   ![Val för att gå till Log Analytics-arbetsytor](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Skapa")

2. Välj **Skapa** på sidan **Logganalys.**

   ![Logga analytics-sida med knappen Skapa](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Välj")

3. Välj **Skapa ny** och fyll i informationen.

   ![Information om hur du skapar en Log Analytics-arbetsyta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Välj")

4. Hitta din VPN-gateway på**inställningsbladet För bildskärmsdiagnostik.** **Monitor** > 

   ![Val för att hitta VPN-gatewayen i diagnostikinställningar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Välj")

5. Om du vill aktivera diagnostik dubbelklickar du på gatewayen och väljer sedan **Aktivera diagnostik**.

   ![Val för att aktivera diagnostik](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Välj")

6. Fyll i informationen och se till att **Skicka till Log Analytics** och **TunnelDiagnosticLog** är valda. Välj arbetsytan Logganalys som du skapade i steg 3.

   ![Markerade kryssrutor](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Välj")

   > [!NOTE]
   > Det kan ta några timmar innan data visas från början.

7. Gå till översikten för den virtuella nätverksgatewayresursen och välj **Aviseringar** på fliken **Övervakning.** Skapa sedan en ny varningsregel eller redigera en befintlig varningsregel.

   ![Val för att skapa en ny varningsregel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Välj")

   ![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Välj")
8. Välj arbetsytan Logganalys och resursen.

   ![Val för arbetsyta och resurs](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Välj")

9. Välj **Anpassad loggsökning** som signallogik under **Lägg till villkor**.

   ![Val för en anpassad loggsökning](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Välj")

10. Ange sedan följande fråga i textrutan **Sökfråga**. Ersätt värdena i <> och TimeGenerated efter behov.

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

    Ange tröskelvärdet till 0 och välj **Klar**.

    ![Ange en fråga och välja ett tröskelvärde](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Välj")

11. På sidan **Skapa regel** väljer du **Skapa ny** under avsnittet **ÅTGÄRDSGRUPPER.** Fyll i detaljerna och välj **OK**.

    ![Information om en ny åtgärdsgrupp](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Välj")

12. På sidan **Skapa regel** fyller du i informationen för **Anpassa åtgärder** och kontrollerar att rätt namn visas i avsnittet **ÅTGÄRDSGRUPPNAMN.** Välj **Skapa aviseringsregel** om du vill skapa regeln.

    ![Val för att skapa en regel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Välj")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Konfigurera aviseringar med powershell

I följande exempelsteg skapas en avisering för en frånkopplingshändelse som involverar en VPN-tunnel från plats till plats.

1. Skapa en log analytics-arbetsyta:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. Aktivera diagnostik för VPN-gatewayen:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Skapa en åtgärdsgrupp.

   Den här koden skapar en åtgärdsgrupp som skickar ett e-postmeddelande när en avisering utlöses:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Skapa en aviseringsregel baserat på en anpassad loggsökning:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera aviseringar om tunnelmått finns i [Konfigurera aviseringar om VPN Gateway-mått](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
