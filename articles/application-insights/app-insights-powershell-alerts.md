---
title: "Använda Powershell för att ställa in aviseringar i Application Insights | Microsoft Docs"
description: "Automatisera konfigurationen av Application Insights som ska få e-postmeddelanden om ändringar av mått."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 05d6a9e0-77a2-4a35-9052-a7768d23a196
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: b90a540afd1c2815db8f5a99ee210ce21ea4d874
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Använd PowerShell för att ställa in aviseringar i Application Insights
Du kan automatisera konfigurationen av [aviseringar](app-insights-alerts.md) i [Programinsikter](app-insights-overview.md).

Dessutom kan du [ange webhooks att automatisera dina svar på en avisering](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

> [!NOTE]
> Om du vill skapa resurser och -varningar samtidigt bör [med en Azure Resource Manager-mall](app-insights-powershell.md).
>
>

## <a name="one-time-setup"></a>Enstaka installationen
Om du inte har använt PowerShell med din Azure-prenumeration innan du:

Installera Azure Powershell-modulen på datorn där du vill köra skripten.

* Installera [Microsoft Web Platform Installer (v5 eller högre)](http://www.microsoft.com/web/downloads/platform.aspx).
* Du installerar Microsoft Azure Powershell

## <a name="connect-to-azure"></a>Anslut till Azure
Starta Azure PowerShell och [ansluta till din prenumeration](/powershell/azure/overview):

```PowerShell

    Add-AzureAccount
```


## <a name="get-alerts"></a>Få aviseringar
    Get-AzureAlertRmRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Lägg till avisering
    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
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
E-posta mig om serverns svar på HTTP-begäran var i genomsnitt över 5 minuter är långsammare än 1 sekund. Application Insights-resurs kallas IceCreamWebApp och den är i resursgruppen Fabrikam. Jag är ägare till Azure-prenumerationen.

GUID är prenumerations-ID (inte instrumentation nyckeln för programmet).

    Add-AlertRule -Name "slow responses" `
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
Jag har ett program som jag använder [TrackMetric()](app-insights-api-custom-events-metrics.md#trackmetric) att rapportera ett mått med namnet ”salesPerHour”. Skicka ett e-postmeddelande till Mina kollegor om ”salesPerHour” sjunker under 100, ett genomsnitt räknas ut under 24 timmar.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

Samma regel kan användas för det mått som rapporteras med hjälp av den [mätning parametern](app-insights-api-custom-events-metrics.md#properties) för spårning av ett annat anrop till exempel TrackEvent eller trackPageView.

## <a name="metric-names"></a>Tjänstmåttets namn
| Måttnamnet | Skärmnamn på | Beskrivning |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Webbläsarundantag |Antal undantagsfel utan felhantering utlöstes i webbläsaren. |
| `basicExceptionServer.count` |Undantag för |Antal undantagsfel utan felhantering utlöstes av appen |
| `clientPerformance.clientProcess.value` |Bearbetningstid för klienten |Tiden mellan tar emot de sista byten av ett dokument till dess att DOM har lästs. Asynkrona förfrågningar kan fortfarande vara under bearbetning. |
| `clientPerformance.networkConnection.value` |Nätverket Anslut sidinläsningstiden |Tid som webbläsaren tar för att ansluta till nätverket. Kan vara 0 om cachelagrade. |
| `clientPerformance.receiveRequest.value` |Ta emot svarstid |Tiden mellan webbläsare som begäran skickades till togs emot svar. |
| `clientPerformance.sendRequest.value` |Skicka tid för begäran |Tid som webbläsaren att skicka begäran. |
| `clientPerformance.total.value` |Sidhämtningstid |Tiden från användarförfrågan till dess att DOM, formatmallar, skript och bilder har lästs in. |
| `performanceCounter.available_bytes.value` |Tillgängligt minne |Fysiskt minne som är direkt tillgängligt för en process eller för systemanvändning. |
| `performanceCounter.io_data_bytes_per_sec.value` |Process-i/o-hastighet |Totalt antal byte per sekund som läses och skrivs till filer, nätverk och enheter. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |hastighet för undantag |Undantag per sekund. |
| `performanceCounter.percentage_processor_time.value` |Processen CPU |Procentandelen av förfluten tid som alla processens trådar använda processorn för att köra instruktioner för hur program. |
| `performanceCounter.percentage_processor_total.value` |Processortid |Procentandel av tiden som processorn ägnat åt icke-inaktiva trådar. |
| `performanceCounter.process_private_bytes.value` |Processen för privata byte |Minne som tilldelats exklusivt för att övervaka programprocesser. |
| `performanceCounter.request_execution_time.value` |Körningstid för ASP.NET-begäran |Körningstid för den senaste begäranden. |
| `performanceCounter.requests_in_application_queue.value` |ASP.NET-begäranden i kö för körning |Längden på programbegärandekön. |
| `performanceCounter.requests_per_sec.value` |ASP.NET-begärandehastighet |Hastighet för alla förfrågningar till programmet per sekund från ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Beroende fel |Antal misslyckade anrop gjorda av serverprogrammet till externa resurser. |
| `request.duration` |Serversvarstid |Tiden mellan ta emot en HTTP-begäran och slutför skickar svar. |
| `request.rate` |Begärandehastighet |Hastighet för alla förfrågningar till programmet per sekund. |
| `requestFailed.count` |Misslyckade begäranden |Antal HTTP-begäranden som resulterade i en svarskoden > = 400 |
| `view.count` |Sidvisningar |Antal klientens användarförfrågningar för en webbsida. Syntetisk trafik filtreras bort. |
| {din anpassade mått name} |{Måttnamnet} |Värdet för din mått som rapporteras av [TrackMetric](app-insights-api-custom-events-metrics.md#trackmetric) eller i den [mätningar parametern för ett anrop för spårning av](app-insights-api-custom-events-metrics.md#properties). |

Mätvärdena som skickas av olika telemetri moduler:

| Mått grupp | Insamlaren modul |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>vy |[Webbläsaren JavaScript](app-insights-javascript.md) |
| performanceCounter |[Prestanda](app-insights-configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Beroende](app-insights-configuration-with-applicationinsights-config.md) |
| begäran<br/>requestFailed |[Serverbegäran](app-insights-configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooks
Du kan [automatisera dina svar på en avisering](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure anropar en webbadress som du väljer när en avisering utlöses.

## <a name="see-also"></a>Se även
* [Skript för att konfigurera Application Insights](app-insights-powershell-script-create-resource.md)
* [Skapa Application Insights och testa webbresurser från mallar](app-insights-powershell.md)
* [Automatisera koppling Microsoft Azure-diagnostik till Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Automatisera dina svar på en avisering](../monitoring-and-diagnostics/insights-webhooks-alerts.md)
