---
title: Förbereda för Azure Monitor klassiska aviseringar migrering genom att uppdatera dina logic apps och runbooks
description: Lär dig hur du ändrar din webhooks, logic apps och runbooks för att förbereda för frivillig migrering.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 347c89991cbb4d28b46eafff0a783148793ad2f7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727481"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Förbered logikappar och runbooks för migrering av klassiska Varningsregler

Som [tidigare meddelats](monitoring-classic-retirement.md), klassiska aviseringar i Azure Monitor är dras tillbaka i juli 2019. Migreringsverktyg finns i Azure portal för att kunder som använder klassiska Varningsregler och som vill aktivera migrering själva.

Om du väljer att migrera din klassiska Varningsregler frivilligt i nya Varningsregler, Tänk på att det inte finns några skillnader mellan de två systemen. Den här artikeln förklarar dessa skillnader och hur du kan förbereda för att ändringen.

## <a name="api-changes"></a>API-ändringar

API: er som skapar och hanterar klassiska Varningsregler (`microsoft.insights/alertrules`) skiljer sig från API: er som skapar och hanterar nya måttaviseringar (`microsoft.insights/metricalerts`). Om du programmässigt skapar och hanterar klassiska Varningsregler idag, uppdaterar du din distributionsskript för den nya API: erna.

I följande tabell är en referens till programgränssnitt för både klassiska och nya aviseringar:

|         |Klassiska aviseringar  |Nya aviseringar för mått |
|---------|---------|---------|
|REST-API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [avisering för AZ-Övervakare](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [AZ monitor metrics avisering](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referens](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Azure Resource Manager-mall | [För klassiska aviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[För nya måttaviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Meddelandet nyttolast ändras

Meddelandeformat för nyttolasten skiljer sig mellan [klassiska Varningsregler](alerts-webhooks.md) och [nya måttaviseringar](alerts-metric-near-real-time.md#payload-schema). Om du har webhook, logikapp eller runbook-åtgärder som utlöses av klassiska Varningsregler, måste du uppdatera dessa aviseringsslutpunkter för att godkänna nyttolastformatet av nya måttaviseringar.

Använd följande tabell för att mappa fälten webhook-nyttolasten från det klassiska formatet till det nya formatet:

|  |Klassiska aviseringar  |Nya aviseringar för mått |
|---------|---------|---------|
|Var aviseringen aktiveras eller löst?    | **status**       | **data.status** |
|Sammanhangsbaserad information om aviseringen     | **context**        | **data.context**        |
|Tidsstämpeln då aviseringen har aktiverats eller löst     | **context.timestamp**       | **data.context.timestamp**        |
| Varningsregeln-ID | **context.id** | **data.context.id** |
| Namn på aviseringsregel | **context.name** | **data.context.name** |
| Beskrivning av regeln | **context.description** | **data.context.description** |
| Varningsregeln villkor | **context.condition** | **data.context.condition** |
| Måttnamn | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Tidsmängd (hur måttet slås samman under fönstret utvärdering)| **data.context.condition.timeAggregation** | **data.context.condition.timeAggregation** |
| Utvärderingsperioden har löpt ut | **context.condition.windowSize** | **data.context.condition.windowSize** |
| Operatorn (hur aggregerade mätvärdet jämföras med tröskelvärdet) | **context.condition.operator** | **data.context.condition.operator** |
| Tröskelvärde | **Context.condition.Threshold** | **data.context.condition.allOf[0].threshold** |
| Måttvärde | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| Prenumerations-ID:t | **context.subscriptionId** | **data.context.subscriptionId** |
| Resursgruppen för resursen som påverkas | **context.resourceGroup** | **data.context.resourceGroup** |
| Namnet på resursen som påverkas | **context.resourceName** | **data.context.resourceName** |
| Typ av resurs som påverkas | **context.resourceType** | **data.context.resourceType** |
| Resurs-ID för resursen som påverkas | **context.resourceId** | **data.context.resourceId** |
| Direktlänk till sammanfattningssidan portal resurs | **context.portalLink** | **data.context.portalLink** |
| Anpassad nyttolast fält som ska skickas till webhook eller logic app | **Egenskaper** | **data.Properties** |

Nyttolasterna är liknande, som du kan se. Följande avsnitt innehåller:

- Information om hur du ändrar logikappar att arbeta med det nya formatet.
- En runbook-exempel som Parsar meddelandets nyttolast för nya aviseringar.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Ändra en logikapp för att ta emot en avisering om mått

Om du använder logic apps med klassiska aviseringar, måste du ändra din logikapp kod för att analysera nya måttaviseringar nyttolasten. Följ de här stegen:

1. Skapa en ny logikapp.

1. Med mallen ”Azure Monitor – mått avisering hanteraren”. Den här mallen har en **HTTP-begäran** utlösare med lämpligt schema som definierats.

    ![-mall för logikapp](media/alerts-migration/logic-app-template.png "metrisk varning mall")

1. Lägg till en åtgärd för att vara värd för bearbetning av dataströmmar.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Använda en automation-runbook som tar emot en avisering om mått

I följande exempel innehåller PowerShell-kod för användning i din runbook. Den här koden kan parsa nyttolaster för både klassiska måttaviseringsregler och nya måttaviseringsregler.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

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

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
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

Ett fullständigt exempel av en runbook som stoppar en virtuell dator när en avisering har utlösts finns den [dokumentation om Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Partnerintegrering via webhooks

De flesta av [våra partners som integreras med klassiska aviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/partners) stöder redan nyare måttaviseringar via sina integreringar. Kända integreringar som redan fungerar med nya måttaviseringar är:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Om du använder en partnerintegration som inte listas här, kontrollera med integration-leverantören att integrationen fungerar med nya måttaviseringar.

## <a name="next-steps"></a>Nästa steg

- [Så här använder du Migreringsverktyget](alerts-using-migration-tool.md)
- [Förstå hur Migreringsverktyget fungerar](alerts-understand-migration.md)
