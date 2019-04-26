---
title: Förbereda för Azure Monitor klassiska aviseringar migrering genom att uppdatera dina logic apps och runbooks
description: Lär dig hur du ändrar din webhook, logikapp och runbooks för att förbereda för frivillig migrering.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 3c47404826d5055d4a82d4842523f790fb11f000
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346891"
---
# <a name="prepare-your-logic-apps-and-run-books-for-classic-alert-rules-migration"></a>Förbereda dina logic apps och köra böcker för klassiska Varningsregler migrering

Som [tidigare meddelats](monitoring-classic-retirement.md), klassiska aviseringar i Azure Monitor är dras tillbaka i juli 2019. Migreringsverktyget att utlösa migrering frivilligt är tillgänglig i Azure portal och inför för kunder som använder klassiska Varningsregler.

Om du väljer att migrera din klassiska Varningsregler frivilligt i nya Varningsregler, finns det några skillnader mellan de två systemen som du bör känna till. Den här artikeln vägleder dig igenom skillnaderna mellan de två systemen och hur du kan förbereda för att ändringen.

## <a name="api-changes"></a>API-ändringar

API: er som används för att skapa/hantera klassiska Varningsregler (`microsoft.insights/alertrules`) skiljer sig från API: er som används för att skapa/hantera nya måttaviseringar (`microsoft.insights/metricalerts`). Om du programmässigt skapa/hantera klassiska Varningsregler idag, kan du uppdatera din distributionsskript för nya API: erna.

Följande tabell innehåller en referens till programgränssnitt för både klassiska och nya aviseringar.

|         |Klassiska aviseringar  |Nya aviseringar för mått |
|---------|---------|---------|
|REST-API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [avisering för AZ-Övervakare](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [AZ monitor metrics avisering](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referens](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Azure Resource Manager-mall | [För klassiska aviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[För nya måttaviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Meddelandet nyttolast ändras

Meddelandeformat för nyttolasten skiljer sig mellan [klassiska Varningsregler](alerts-webhooks.md) och [nya måttaviseringar](alerts-metric-near-real-time.md#payload-schema). Om du har några webhook, logikapp eller runbook-åtgärder som utlöses av klassiska Varningsregler, måste du uppdatera dessa aviseringsslutpunkter för att godkänna nyttolastformatet av nya måttaviseringar.

Du kan använda tabellen nedan för mappning mellan klassiska varningsregel webhook-nyttolasten och nya metrisk varning webhook-nyttolasten.

|  |Klassiska aviseringar  |Nya aviseringar för mått |
|---------|---------|---------|
|Aviseringen var aktiveras eller löst     | status       | data.status |
|Sammanhangsbaserad information om aviseringen     | Kontext        | data.context        |
|Tidsstämpel som aviseringen har aktiverats eller löst      | context.timestamp       | data.context.timestamp        |
| Varningsregeln-ID | Context.ID | data.context.id |
| Namn på Aviseringsregel | Context.Name | data.context.name |
| Beskrivning av regeln | context.description | data.context.description |
| Varningsregeln villkor | context.condition | data.context.condition|
| Måttnamn | context.condition.metricName| data.context.condition.allOf[0].metricName|
| Tidsmängd (hur måttet slås samman under fönstret utvärdering)|data.context.condition.timeAggregation|data.context.condition.timeAggregation|
| Utvärderingsperioden har löpt ut | context.condition.windowSize | data.context.condition.windowSize|
| Operatorn (hur aggregerade mätvärdet jämföras med tröskelvärdet) | context.condition.operator | data.context.condition.operator|
| Tröskelvärde | Context.condition.Threshold| data.context.condition.allOf[0].threshold|
| Måttvärde | context.condition.metricValue | data.context.condition.allOf[0].metricValue|
| Prenumerations-ID:t | context.subscriptionId | data.context.subscriptionId|
| Resursgruppen för resursen som påverkas | context.resourceGroup | data.context.resourceGroup|
| Namnet på resursen som påverkas | context.resourceName | data.context.resourceName |
| Typ av resurs som påverkas | context.resourceType | data.context.resourceType |
|  Resurs-ID för resursen som påverkas | context.resourceId | data.context.resourceId |
| En direktlänk till sammanfattningssidan portal resurs | context.portalLink | data.context.portalLink|
| Anpassad nyttolast fält som ska skickas till webhook eller logikappen | properties |data.Properties |

Som du kan se är båda nyttolasterna liknande. Följande avsnitt innehåller information om exemplet logikappar och en exempel-runbook för att parsa meddelandets nyttolast för nya aviseringar.

## <a name="using-a-logic-app-that-receives-a-metric-alert-notification"></a>Med hjälp av en logikapp som tar emot en avisering om mått

Om du använder logikappar med klassiska aviseringar, behöver du ändra din logikapp för att analysera nya måttaviseringar nyttolasten.

1. Skapa en ny logikapp.

2. Med mallen ”Azure Monitor – mått avisering hanteraren”. Den här mallen har en **HTTP-begäran** utlösare med lämpligt schema som definierats

    ![-mall för logikapp](media/alerts-migration/logic-app-template.png "metrisk varning mall")

3. Lägg till en åtgärd för att vara värd för bearbetning av dataströmmar.

## <a name="using-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Med hjälp av en automation-runbook som tar emot en avisering om mått

I följande exempel innehåller PowerShell-kod som kan användas i din runbook som kan parsa nyttolaster för både klassiska måttaviseringsregler och nya måttaviseringsregler.

```PS
## Sample PowerShell code to be used in a runbook to handle parsing of both classic and new metric alerts

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Identify if the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is not either a classic metric alert or a new metric alert
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    #parse fields related to resource affected
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Se ett fullständigt exempel av en runbook som stoppar en virtuell dator när en avisering har utlösts i [dokumentation om Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Partnerintegrering via webhooks

De flesta av [våra partners som integreras med klassiska aviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/partners) stöder redan nyare måttaviseringar via sina integreringar. Kända integreringar som redan fungerar med nya måttaviseringar finns nedan.

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Om du använder en partnerintegration som inte listas här, kontrollera med integration-leverantören att integrationen fungerar med nya måttaviseringar.

## <a name="next-steps"></a>Nästa steg

- [Så här använder du Migreringsverktyget](alerts-using-migration-tool.md)
- [Förstå hur Migreringsverktyget fungerar](alerts-understand-migration.md)
