---
title: Använd Powershell för att ställa in aviseringar i Application Insights | Microsoft-dokument
description: Automatisera konfigurationen av Application Insights för att få e-postmeddelanden om måttändringar.
ms.topic: conceptual
ms.date: 10/31/2016
ms.openlocfilehash: c19cb43d08b44b55c786e750e64a83e6f0c67381
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669853"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Använd PowerShell för att ställa in aviseringar i Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan automatisera konfigurationen av [aviseringar](../../azure-monitor/app/alerts.md) i [Application Insights](../../azure-monitor/app/app-insights-overview.md).

Dessutom kan du [ställa in webhooks för att automatisera ditt svar på en varning](../../azure-monitor/platform/alerts-webhooks.md).

> [!NOTE]
> Om du vill skapa resurser och aviseringar samtidigt kan du [använda en Azure Resource Manager-mall](powershell.md).

## <a name="one-time-setup"></a>Engångsinställningar
Om du inte har använt PowerShell med din Azure-prenumeration tidigare:

Installera Azure Powershell-modulen på den dator där du vill köra skripten.

* Installera [Installationsprogrammet för Microsoft Web Platform (v5 eller senare)](https://www.microsoft.com/web/downloads/platform.aspx).
* Använd den för att installera Microsoft Azure Powershell

## <a name="connect-to-azure"></a>Anslut till Azure
Starta Azure PowerShell och [anslut till din prenumeration:](/powershell/azure/overview)

```powershell

    Add-AzAccount
```


## <a name="get-alerts"></a>Få aviseringar
    Get-AzAlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Lägg till avisering
    Add-AzMetricAlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US" // must be East US at present
     -RuleType Metric



## <a name="example-1"></a>Exempel 1
Maila mig om serverns svar på HTTP-begäranden, i genomsnitt över 5 minuter, är långsammare än 1 sekund. Min Application Insights-resurs kallas IceCreamWebApp och finns i resursgruppen Fabrikam. Jag är ägare till Azure-prenumerationen.

GUID är prenumerations-ID (inte instrumenteringsnyckeln för programmet).

    Add-AzMetricAlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Exempel 2
Jag har ett program där jag använder [TrackMetric()](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) för att rapportera ett mått med namnet "salesPerHour". Skicka ett mail till mina kollegor om "salesPerHour" sjunker under 100, i genomsnitt över 24 timmar.

    Add-AzMetricAlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

Samma regel kan användas för det mått som rapporteras med hjälp av [mätparametern](../../azure-monitor/app/api-custom-events-metrics.md#properties) för ett annat spårningsanrop, till exempel TrackEvent eller trackPageView.

## <a name="metric-names"></a>Måttnamn
| Måttnamn | Skärmnamn | Beskrivning |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Webbläsarundantag |Antal undantag som inte har genererats i webbläsaren. |
| `basicExceptionServer.count` |Serverundantag |Antal ohanterade undantag som genereras av appen |
| `clientPerformance.clientProcess.value` |Tid för klientbearbetning |Tid mellan att ta emot den sista bytet i ett dokument tills DOM läses in. Async-begäranden kan fortfarande bearbetas. |
| `clientPerformance.networkConnection.value` |Anslutningstid för sidbelastning av nätverksanslutning |Tid det tar för webbläsaren att ansluta till nätverket. Kan vara 0 om cachelagrade. |
| `clientPerformance.receiveRequest.value` |Ta emot svarstid |Tid mellan webbläsare skicka begäran att börja ta emot svar. |
| `clientPerformance.sendRequest.value` |Skicka tid för begäran |Tid för webbläsaren att skicka begäran. |
| `clientPerformance.total.value` |Inläsningstid för webbläsarsidan |Tid från användarbegäran till DOM, formatmallar, skript och bilder läses in. |
| `performanceCounter.available_bytes.value` |Tillgängligt minne |Fysiskt minne omedelbart tillgängligt för en process eller för systemanvändning. |
| `performanceCounter.io_data_bytes_per_sec.value` |Process IO-hastighet |Totalt antal byte per sekund som lästs och skrivits till filer, nätverk och enheter. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |undantagsfrekvens |Undantag som genereras per sekund. |
| `performanceCounter.percentage_processor_time.value` |Bearbeta CPU |Procentandelen förfluten tid för alla processtrådar som används av processorn för att utföra instruktioner för programprocessen. |
| `performanceCounter.percentage_processor_total.value` |Processortid |Den procentandel av tiden som processorn tillbringar i icke-inaktiva trådar. |
| `performanceCounter.process_private_bytes.value` |Bearbeta privata byte |Minne som uteslutande tilldelats det övervakade programmets processer. |
| `performanceCounter.request_execution_time.value` |ASP.NET körningstid för begäran |Körningstid för den senaste begäran. |
| `performanceCounter.requests_in_application_queue.value` |ASP.NET begäranden i körningskön |Längden på kön för programbegäran. |
| `performanceCounter.requests_per_sec.value` |ASP.NET begäran ränta |Betygsätt alla begäranden till programmet per sekund från ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Beroendefel |Antal misslyckade anrop som gjorts av serverprogrammet till externa resurser. |
| `request.duration` |Svarstid för servern |Tid mellan att ta emot en HTTP-begäran och avsluta sändningen av svaret. |
| `request.rate` |Begär kurs |Betygsätt alla begäranden till programmet per sekund. |
| `requestFailed.count` |Misslyckade förfrågningar |Antal HTTP-begäranden som resulterade i en svarskod >= 400 |
| `view.count` |Sidvisningar |Antal klientanvändarbegäranden för en webbsida. Syntetisk trafik filtreras bort. |
| {ditt anpassade måttnamn} |{Ditt måttnamn} |Ditt måttvärde som rapporteras av [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) eller i [måttparametern för ett spårningsanrop](../../azure-monitor/app/api-custom-events-metrics.md#properties). |

Måtten skickas av olika telemetrimoduler:

| Måttgrupp | Collector modul |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>visa |[Webbläsare JavaScript](../../azure-monitor/app/javascript.md) |
| prestandaRäknare |[Prestanda](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Beroende](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| Begäran<br/>begäranFailerad |[Serverbegäran](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooks
Du kan [automatisera ditt svar på en avisering](../../azure-monitor/platform/alerts-webhooks.md). Azure anropar en valfri webbadress när en avisering utlöses.

## <a name="see-also"></a>Se även
* [Skript för att konfigurera Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Skapa programstatistik och webbtestresurser från mallar](powershell.md)
* [Automatisera koppling av Microsoft Azure Diagnostics till Application Insights](powershell-azure-diagnostics.md)
* [Automatisera ditt svar på en avisering](../../azure-monitor/platform/alerts-webhooks.md)
