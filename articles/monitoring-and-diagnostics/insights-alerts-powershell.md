---
title: Använda PowerShell för att skapa klassisk aviseringar för Azure-tjänster | Microsoft Docs
description: 'Aktivera e-post eller meddelanden eller anropa webbplats-URL: er (webhooks) eller automation när du anger villkor är uppfyllda.'
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: b08a8f66add45d64085ac05605fe3c7d7f91b705
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286207"
---
# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Använd PowerShell för att skapa aviseringar för Azure-tjänster
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

> [!NOTE]
> Den här artikeln beskriver hur du skapar äldre klassiska mått aviseringar. Stöd för Azure övervakaren [nyare, bättre mått aviseringar](monitoring-near-real-time-metric-alerts.md). Dessa aviseringar kan du övervaka flera och tillåter aviseringar om dimensionell mått. PowerShell-stöd för nyare mått aviseringar kommer snart.
>
>

Den här artikeln visar hur du ställer in Azure klassiska mått aviseringar med hjälp av PowerShell.  

Du kan ta emot varningar baserat på mått för din Azure-tjänster eller du kan ta emot aviseringar om händelser som inträffar i Azure.

* **Måttvärden**: aviseringen utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar i båda riktningarna. Det vill säga den utlöser både när villkoret uppfylls först och sedan när den är inte längre är uppfyllt.    
* **Aktiviteten logghändelser**: en avisering kan utlösa på *varje* händelse eller när vissa händelser inträffar. Mer information om aktiviteten loggen aviseringar finns [och skapa aktiviteten Logga varningar (klassisk)](monitoring-activity-log-alerts.md).

Du kan konfigurera en avisering om klassiska mått om du vill utföra följande uppgifter när den utlöser:

* Skicka e-postmeddelanden till tjänstadministratören och medadministratörer.
* Skicka e-post till ytterligare e-postadresser som du anger.
* Anropa en webhook.
* Starta körning av en Azure-runbook (endast från Azure portal).

Du kan konfigurera och få information om aviseringen regler från följande platser: 

* [Azure Portal](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure-kommandoradsgränssnittet (CLI)](insights-alerts-command-line-interface.md)
* [Azure-Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

För ytterligare information, kan du alltid skriva ```Get-Help``` följt av PowerShell-kommando som du behöver hjälp med.

## <a name="create-alert-rules-in-powershell"></a>Skapa Varningsregler i PowerShell
1. Logga in i Azure.   

    ```PowerShell
    Connect-AzureRmAccount

    ```
2. Hämta en lista över prenumerationer som är tillgängliga för dig. Kontrollera att du arbetar med rätt prenumerationen. Om inte, kommer till rätt prenumerationen genom att använda utdata från `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Visa en lista med befintliga regler på en resursgrupp med hjälp av följande kommando:

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Om du vill skapa en regel som du behöver ha flera viktiga uppgifter för först.

    - Den **resurs-ID** för den resurs som du vill aktivera en avisering för.
    - Den **måttdefinitioner** som är tillgängliga för den här resursen.

     Ett sätt att hämta resurs-ID är att använda Azure-portalen. Förutsatt att resursen har redan skapats, markerar du den i portalen. Sedan i bladet nästa i den **inställningar** väljer **egenskaper**. **RESURS-ID** är ett fält i bladet nästa. 
     
     Du kan också få resurs-ID med hjälp av [resursutforskaren Azure](https://resources.azure.com/).

     Följande är ett exempel resurs-ID för ett webbprogram:

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Du kan använda `Get-AzureRmMetricDefinition` att visa en lista över alla måttdefinitioner för en viss resurs:

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     I följande exempel skapar en tabell med måttnamnet och enhet för att mått:

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     För att få en fullständig lista över tillgängliga alternativ för Get-AzureRmMetricDefinition köra `Get-Help Get-AzureRmMetricDefinition -Detailed`.
5. I följande exempel ställer in en avisering för en resurs för webbplatsen. Aviseringen utlösare när den får konsekvent all trafik för 5 minuter och igen när den tar emot någon trafik i 5 minuter.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. Om du vill skapa en webhook eller skicka e-postmeddelande när en avisering utlöser måste du först skapa e-post eller webhook. Direkt skapa regeln efteråt med-tagg för åtgärder som visas i följande exempel. Du kan inte associera webhooks eller e-postmeddelanden med regler som redan har skapats.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Titta på enskilda regler för att verifiera att aviseringar har skapats korrekt.

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
* [Få en översikt över Azure övervakning](monitoring-overview.md), inklusive typerna av information som du kan samla in och övervaka.
* Lär dig hur du [konfigurerar webhooks i aviseringar](insights-webhooks-alerts.md).
* Lär dig hur du [konfigurera aviseringar på aktiviteten logghändelser](monitoring-activity-log-alerts.md).
* Lär dig mer om [Azure Automation-runbooks](../automation/automation-starting-a-runbook.md).
* Hämta en [översikt över att samla in diagnostikloggar](monitoring-overview-of-diagnostic-logs.md) att samla in detaljerade hög frekvens mått på din tjänst.
* Hämta en [översikt över mått samling](insights-how-to-customize-monitoring.md) att kontrollera att tjänsten är tillgänglig och svarstid.
