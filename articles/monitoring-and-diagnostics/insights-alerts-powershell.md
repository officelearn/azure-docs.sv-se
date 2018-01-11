---
title: "Skapa aviseringar för Azure-tjänster - PowerShell | Microsoft Docs"
description: "Utlösaren e-postmeddelanden meddelanden, anropa webbplatser URL: er (webhooks) eller automation när angivna villkor uppfylls."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d26ab15b-7b7e-42a9-81c8-3ce9ead5d252
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
ms.openlocfilehash: d3fca8675c1f15b8fd0f952cfbf520f5c68478b3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="create-metric-alerts-in-azure-monitor-for-azure-services---powershell"></a>Skapa mått aviseringar i Azure-Monitor för Azure-tjänster - PowerShell
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Översikt
Den här artikeln visar hur du ställer in Azure mått aviseringar med hjälp av PowerShell.  

Du kan ta emot en avisering baserat på övervakning mätvärden för eller händelser på Azure-tjänster.

* **Måttvärden** -aviseringen utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar i båda riktningarna. Det vill säga den utlöser både när villkoret uppfylls först och sedan efteråt när villkor som inte längre är uppfyllt.    
* **Aktiviteten logghändelser** -utlösa en avisering på *varje* händelse eller bara när en vissa händelser inträffar. Mer information om aktiviteten loggen aviseringar [Klicka här](monitoring-activity-log-alerts.md)

Du kan konfigurera en mått avisering när den utlöser gör du följande:

* Skicka e-postmeddelanden till tjänstadministratören och medadministratörer
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* anropa en webhook
* Starta körning av en Azure-runbook (endast från Azure portal)

Du kan konfigurera och få information om aviseringen regler med hjälp av

* [Azure-portalen](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [kommandoradsgränssnittet (CLI)](insights-alerts-command-line-interface.md)
* [Azure-Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

För ytterligare information, kan du alltid skriva ```Get-Help``` och sedan av PowerShell-kommando som du vill ha hjälp med.

## <a name="create-alert-rules-in-powershell"></a>Skapa Varningsregler i PowerShell
1. Logga in på Azure.   

    ```PowerShell
    Login-AzureRmAccount

    ```
2. Hämta en lista över prenumerationerna som finns tillgängliga. Kontrollera att du arbetar med rätt prenumerationen. Om inte, anger du det högra använda utdata från `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Om du vill visa en lista med befintliga regler på en resursgrupp, använder du följande kommando:

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Om du vill skapa en regel som du behöver ha flera viktiga uppgifter för först.

  * Den **resurs-ID** för den resurs som du vill aktivera en avisering för
  * Den **måttdefinitioner** tillgänglig för den här resursen

     Ett sätt att hämta resurs-ID är att använda Azure-portalen. Under förutsättning att resursen har redan skapats, väljer du den i portalen. Välj sedan i bladet nästa *egenskaper* under den *inställningar* avsnitt. **RESURS-ID** är ett fält i bladet nästa. Ett annat sätt är att använda den [resursutforskaren Azure](https://resources.azure.com/).

     Är ett exempel resurs-ID för en webbapp

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Du kan använda `Get-AzureRmMetricDefinition` att visa en lista över alla måttdefinitioner för en viss resurs.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     I följande exempel genererar en tabell med måttet namn och enhet för att mått.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     En fullständig lista över tillgängliga alternativ för Get-AzureRmMetricDefinition är tillgänglig genom att köra `Get-Help Get-AzureRmMetricDefinition -Detailed`.
5. I följande exempel ställer in en avisering för en resurs för webbplatsen. Aviseringen utlösare när den får konsekvent all trafik för 5 minuter och igen när den tar emot någon trafik i 5 minuter.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. För att skapa webhooken eller skicka e-postmeddelande när en avisering utlöser, först skapa e-post och/eller webhooks. Sedan omedelbart skapa regel efteråt med taggen - åtgärder och som visas i följande exempel. Du kan inte associera webhook eller e-post med redan skapat regler via PowerShell.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Kontrollera att dina aviseringar genom att titta på enskilda regler har skapats korrekt.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. Ta bort aviseringarna. Dessa kommandon för att ta bort regler som har skapats tidigare i den här artikeln.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Nästa steg
* [Få en översikt över Azure övervakning](monitoring-overview.md) inklusive typerna av information som du kan samla in och övervaka.
* Lär dig mer om [hur du konfigurerar webhooks i aviseringar](insights-webhooks-alerts.md).
* Lär dig mer om [konfigurera aviseringar på aktiviteten logghändelser](monitoring-activity-log-alerts.md).
* Lär dig mer om [Azure Automation-Runbooks](../automation/automation-starting-a-runbook.md).
* Hämta en [översikt över att samla in diagnostikloggar](monitoring-overview-of-diagnostic-logs.md) att samla in detaljerade hög frekvens mått på din tjänst.
* Hämta en [översikt över mått samling](insights-how-to-customize-monitoring.md) att kontrollera att tjänsten är tillgänglig och svarstid.
