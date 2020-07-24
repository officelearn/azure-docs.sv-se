---
title: Använd PowerShell för att ställa in aviseringar i Application Insights | Microsoft Docs
description: Automatisera konfigurationen av Application Insights för att få e-postmeddelanden om mått ändringar.
ms.topic: conceptual
ms.date: 07/23/2016
ms.openlocfilehash: 00212aa8783a6bfc8e46d325a882781e33b7de51
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87117169"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Använd PowerShell för att ställa in aviseringar i Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan automatisera konfigurationen av [aviseringar](../../azure-monitor/platform/alerts-log.md) i [Application Insights](../../azure-monitor/app/app-insights-overview.md).

Dessutom kan du [ställa in Webhooks för att automatisera ditt svar på en avisering](../../azure-monitor/platform/alerts-webhooks.md).

> [!NOTE]
> Överväg att [använda en Azure Resource Manager mall](powershell.md)om du vill skapa resurser och aviseringar samtidigt.

## <a name="one-time-setup"></a>Konfiguration vid ett tillfälle
Om du inte har använt PowerShell med din Azure-prenumeration tidigare än:

Installera Azure PowerShell-modulen på den dator där du vill köra skripten.

* Installera [installations programmet för Microsoft Web Platform (V5 eller högre)](https://www.microsoft.com/web/downloads/platform.aspx).
* Använd den för att installera Microsoft Azure PowerShell

## <a name="connect-to-azure"></a>Anslut till Azure
Starta Azure PowerShell och [Anslut till din prenumeration](/powershell/azure/):

```azurepowershell
Add-AzAccount
```


## <a name="get-alerts"></a>Hämta aviseringar

```azurepowershell
Get-AzAlertRule -ResourceGroup "Fabrikam" `
  [-Name "My rule"] `
  [-DetailedOutput]
```

## <a name="add-alert"></a>Lägg till avisering

```azurepowershell
Add-AzMetricAlertRule -Name "{ALERT NAME}" `
  -Description "{TEXT}" `
  -ResourceGroup "{GROUP NAME}" `
  -TargetResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
  -MetricName "{METRIC NAME}" `
  -Operator GreaterThan `
  -Threshold {NUMBER}  `
  -WindowSize {HH:MM:SS} `
  [-SendEmailToServiceOwners] `
  [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM"] `
  -Location "East US" // must be East US at present `
  -RuleType Metric
```


## <a name="example-1"></a>Exempel 1
E-posta mig om serverns svar på HTTP-begäranden, i genomsnitt över 5 minuter, är långsammare än 1 sekund. Min Application Insights-resurs kallas IceCreamWebApp och finns i resurs gruppen fabrikam. Jag är ägare till Azure-prenumerationen.

GUID är prenumerations-ID (inte Instrumentation-nyckeln för programmet).

```azurepowershell
Add-AzMetricAlertRule -Name "slow responses" `
  -ResourceGroup "Fabrikam" `
  -TargetResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
  -MetricName "request.duration" `
  -Operator GreaterThan `
  -Threshold 1 `
  -WindowSize 00:05:00 `
  -SendEmailToServiceOwners `
  -Location "East US" `
  -RuleType Metric
```

## <a name="example-2"></a>Exempel 2
Jag har ett program där jag använder [TrackMetric ()](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) för att rapportera ett mått med namnet "salesPerHour". Skicka ett e-postmeddelande till mina kollegor om "salesPerHour" sjunker under 100, i genomsnitt över 24 timmar.

```azurepowershell
Add-AzMetricAlertRule -Name "poor sales" `
  -Description "slow sales alert" `
  -ResourceGroup "Fabrikam" `
  -TargetResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
  -MetricName "salesPerHour" `
  -Operator LessThan `
  -Threshold 100 `
  -WindowSize 24:00:00 `
  -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
  -Location "East US" `
  -RuleType Metric
```

Samma regel kan användas för måttet som rapporteras med hjälp av [mått parametern](../../azure-monitor/app/api-custom-events-metrics.md#properties) för ett annat spårnings anrop, till exempel TrackEvent eller trackPageView.

## <a name="metric-names"></a>Mått namn
| Måttnamn | Skärm namn | Beskrivning |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Webbläsarundantag |Antal ej fångade undantag som har utlösts i webbläsaren. |
| `basicExceptionServer.count` |Server undantag |Antal ohanterade undantag som har utlösts av appen |
| `clientPerformance.clientProcess.value` |Klient bearbetnings tid |Tiden mellan att ta emot sista byten i ett dokument tills DOM har lästs in. Asynkrona begär Anden kan fortfarande bearbetas. |
| `clientPerformance.networkConnection.value` |Nätverks anslutnings tid för sid inläsning |Tid som webbläsaren tar att ansluta till nätverket. Kan vara 0 om cachelagrat. |
| `clientPerformance.receiveRequest.value` |Tar emot svars tid |Tid mellan webbläsare som skickar begäran om att börja ta emot svar. |
| `clientPerformance.sendRequest.value` |Tid för att skicka begäran |Tiden det tar för en webbläsare att skicka begäran. |
| `clientPerformance.total.value` |Sid inläsnings tid för webbläsare |Tid från användar förfrågan tills DOM, formatmallar, skript och bilder har lästs in. |
| `performanceCounter.available_bytes.value` |Tillgängligt minne |Fysiskt minne som är omedelbart tillgängligt för en process eller för system användning. |
| `performanceCounter.io_data_bytes_per_sec.value` |Process-IO-hastighet |Totalt antal byte per sekund som har lästs och skrivits till filer, nätverk och enheter. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |undantags frekvens |Genererade undantag per sekund. |
| `performanceCounter.percentage_processor_time.value` |Processor-CPU |Procent andelen förfluten tid för alla process trådar som används av processorn för att köra instruktioner för program processen. |
| `performanceCounter.percentage_processor_total.value` |Processor tid |Den procent andel av tiden som processorn ägnat åt icke-inaktiva trådar. |
| `performanceCounter.process_private_bytes.value` |Privata byte för process |Minne som tilldelats exklusivt för de övervakade program processerna. |
| `performanceCounter.request_execution_time.value` |Körnings tid för ASP.NET-begäran |Körnings tid för den senaste begäran. |
| `performanceCounter.requests_in_application_queue.value` |ASP.NET-begäranden i körnings kön |Längden på program begär ande kön. |
| `performanceCounter.requests_per_sec.value` |ASP.NET begär ande frekvens |Takten för alla förfrågningar till programmet per sekund från ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Beroende problem |Antal misslyckade anrop gjorda av serverprogrammet till externa resurser. |
| `request.duration` |Server svars tid |Tiden mellan att ta emot en HTTP-förfrågan och avsluta sändningen av svaret. |
| `request.rate` |Begär ande frekvens |Antal begär anden till programmet per sekund. |
| `requestFailed.count` |Misslyckade förfrågningar |Antal HTTP-begäranden som resulterade i svars koden >= 400 |
| `view.count` |Sid visningar |Antal klient användar förfrågningar för en webb sida. Syntetisk trafik filtreras bort. |
| {ditt anpassade mått namn} |{Ditt mått namn} |Ditt Metric-värde som rapporteras av [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) eller i [parametern mätningar i ett spårnings anrop](../../azure-monitor/app/api-custom-events-metrics.md#properties). |

Måtten skickas av olika moduler för telemetri:

| Mått grupp | Insamlings modul |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>visa |[Webb läsar skript](../../azure-monitor/app/javascript.md) |
| performanceCounter |[Prestanda](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Beroende](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| anmoda<br/>requestFailed |[Serverbegäran](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooks
Du kan [Automatisera ditt svar på en avisering](../../azure-monitor/platform/alerts-webhooks.md). Azure kommer att anropa en webb adress som du väljer när en avisering aktive ras.

## <a name="see-also"></a>Se även
* [Skript för att konfigurera Application Insights](./create-new-resource.md#creating-a-resource-automatically)
* [Skapa Application Insights-och webb test resurser från mallar](powershell.md)
* [Automatisera kopplings Microsoft Azure Diagnostics till Application Insights](powershell-azure-diagnostics.md)
* [Automatisera ditt svar på en avisering](../../azure-monitor/platform/alerts-webhooks.md)
