---
title: Uppdatera logikappar & runbooks för migrering av aviseringar
description: Lär dig hur du ändrar dina webbkrokar, logikappar och runbooks för att förbereda för frivillig migrering.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: f31fcc07bed0287c2f86ca4fe52bf02a2a1d2a71
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114415"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Förbered dina logikappar och runbooks för migrering av klassiska aviseringsregler

Som [tidigare meddelats](monitoring-classic-retirement.md)dras klassiska aviseringar i Azure Monitor tillbaka i september 2019 (var ursprungligen juli 2019). Ett migreringsverktyg är tillgängligt i Azure-portalen för kunder som använder klassiska varningsregler och som vill utlösa migreringen själva.

> [!NOTE]
> På grund av förseningar i utrullningen av migreringsverktyget har pensioneringsdatumet för klassisk registreringsmigrering förlängts till den 31 augusti 2019 från det ursprungligen meddelade datumet den 30 juni 2019.

Om du väljer att frivilligt migrera dina klassiska varningsregler till nya varningsregler bör du vara medveten om att det finns vissa skillnader mellan de två systemen. I den här artikeln beskrivs dessa skillnader och hur du kan förbereda dig för ändringen.

## <a name="api-changes"></a>API-ändringar

Api:erna som skapar och`microsoft.insights/alertrules`hanterar klassiska varningsregler ( ) skiljer sig från`microsoft.insights/metricalerts`de API:er som skapar och hanterar nya måttaviseringar ( ). Om du programmässigt skapar och hanterar klassiska varningsregler idag uppdaterar du distributionsskripten så att de fungerar med de nya API:erna.

Följande tabell är en referens till programmatiska gränssnitt för både klassiska och nya aviseringar:

|         |Klassiska aviseringar  |Nya måttaviseringar |
|---------|---------|---------|
|REST-API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az övervaka varning](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az övervaka mått varning](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Referens](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Referens](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager-mall | [För klassiska varningar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[För nya måttaviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Ändringar av nyttolast för meddelanden

Formatet för meddelandenyttolast skiljer sig något mellan [klassiska varningsregler](alerts-webhooks.md) och [nya måttaviseringar](alerts-metric-near-real-time.md#payload-schema). Om du har några webhook-, logikapp- eller runbook-åtgärder som utlöses av klassiska varningsregler måste du uppdatera dessa aviseringsslutpunkter för att acceptera nyttolastformatet för nya måttaviseringar.

Använd följande tabell för att mappa webhook nyttolastfält från det klassiska formatet till det nya formatet:

|  |Klassiska aviseringar  |Nya måttaviseringar |
|---------|---------|---------|
|Aktiverades eller löstes aviseringen?    | **Status**       | **data.status** |
|Kontextuell information om aviseringen     | **Sammanhang**        | **data.kontext**        |
|Tidsstämpel vid vilken varningen aktiverades eller löstes     | **context.timestamp**       | **data.context.timestamp**        |
| ID för varningsregel | **context.id** | **data.context.id** |
| Namn på aviseringsregel | **context.name** | **data.context.name** |
| Beskrivning av varningsregeln | **context.description (beskrivning)** | **data.context.description** |
| Villkor för varningsregel | **context.condition (villkor)context.condition kontext** | **data.context.condition** |
| Måttnamn | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Tidsaggregering (hur måttet aggregeras över utvärderingsfönstret)| **context.condition.timeAggregation** | **context.condition.timeAggregation** |
| Utvärderingsperiod | **context.condition.windowSize** | **data.context.condition.windowSize** |
| Operator (hur det aggregerade måttvärdet jämförs med tröskelvärdet) | **context.condition.operator** | **data.context.condition.operator** |
| Tröskelvärde | **context.condition.tröskel** | **data.context.condition.allOf[0].tröskel** |
| Måttvärde | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| Prenumerations-ID:t | **context.subscriptionId** | **data.context.subscriptionId** |
| Resursgrupp för den berörda resursen | **context.resourceGroup** | **data.context.resourceGroup** |
| Namnet på den berörda resursen | **context.resourceName** | **data.context.resourceName** |
| Typ av den berörda resursen | **context.resourceType** | **data.context.resourceType** |
| Resurs-ID för den berörda resursen | **context.resourceId** | **data.context.resourceId** |
| Direktlänk till sammanfattningssidan för portalresurser | **context.portalLink** | **data.context.portalLink** |
| Anpassade nyttolastfält som ska skickas till webhook- eller logikappen | **Egenskaper** | **data.egenskaper** |

Nyttolasten är liknande, som du kan se. Följande avsnitt erbjuder:

- Information om hur du ändrar logikappar så att de fungerar med det nya formatet.
- Ett runbook-exempel som tolkar meddelandenyttolasten för nya aviseringar.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Ändra en logikapp för att få ett måttaviseringsmeddelande

Om du använder logikappar med klassiska aviseringar måste du ändra logik-appkoden för att tolka nyttolasten för nya måttaviseringar. Följ de här stegen:

1. Skapa en ny logikapp.

1. Använd mallen "Azure Monitor - Metrics Alert Handler". Den här mallen har en **HTTP-begärandeutlösare** med lämpligt schema definierat.

    ![logik-app-mall](media/alerts-migration/logic-app-template.png "Mall för måttvarning")

1. Lägg till en åtgärd som är värd för din bearbetningslogik.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Använda en automatiseringskörningsbok som tar emot ett måttaviseringsmeddelande

I följande exempel innehåller PowerShell-kod som ska användas i runbooken. Den här koden kan tolka nyttolaster för både klassiska måttvarningsregler och nya måttvarningsregler.

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

Ett fullständigt exempel på en runbook som stoppar en virtuell dator när en avisering utlöses finns i [Azure Automation-dokumentationen](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Partnerintegration via webhooks

De flesta av [våra partner som integreras med klassiska aviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/partners) stöder redan nyare måttaviseringar genom sina integrationer. Kända integreringar som redan fungerar med nya måttaviseringar är:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4 (på andra sätt)](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Om du använder en partnerintegration som inte visas här bekräftar du med integrationsprovidern att integreringen fungerar med nya måttaviseringar.

## <a name="next-steps"></a>Nästa steg

- [Använda migreringsverktyget](alerts-using-migration-tool.md)
- [Förstå hur migreringsverktyget fungerar](alerts-understand-migration.md)
