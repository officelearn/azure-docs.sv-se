---
title: Använd Powershell för att ställa in aviseringar i Application Insights | Microsoft Docs
description: Automatisera konfigurationen av Application Insights för att få e-postmeddelanden om ändringar av mått.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 05d6a9e0-77a2-4a35-9052-a7768d23a196
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: b94136f063f9d4793ce4c8a03c17454df920af26
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117562"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Använd PowerShell för att ställa in aviseringar i Application Insights
Du kan automatisera konfigurationen av [aviseringar](../../azure-monitor/app/alerts.md) i [Application Insights](../../azure-monitor/app/app-insights-overview.md).

Dessutom kan du [ange webhooks för att automatisera dina svar på en avisering](../../azure-monitor/platform/alerts-webhooks.md).

> [!NOTE]
> Om du vill skapa resurser och aviseringar på samma gång kan du överväga att [med en Azure Resource Manager-mall](powershell.md).
>
>

## <a name="one-time-setup"></a>Konfigurationen gång
Om du inte har använt PowerShell med Azure-prenumerationen innan du:

Installera Azure Powershell-modulen på datorn där du vill köra skripten.

* Installera [Microsoft Web Platform Installer (v5 eller högre)](https://www.microsoft.com/web/downloads/platform.aspx).
* Använda den för att installera Microsoft Azure Powershell

## <a name="connect-to-azure"></a>Anslut till Azure
Starta Azure PowerShell och [ansluta till din prenumeration](/powershell/azure/overview):

```PowerShell

    Add-AzureRmAccount
```


## <a name="get-alerts"></a>Få aviseringar
    Get-AzureRmAlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Lägg till avisering
    Add-AzureRmMetricAlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
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
E-posta mig om serverns svar på HTTP-begäran än 5 minuter, i genomsnitt är långsammare än 1 sekund. Min Application Insights-resurs kallas IceCreamWebApp och det är i resursgruppen Fabrikam. Jag är ägaren av Azure-prenumeration.

GUID är prenumerations-ID (inte instrumenteringsnyckeln för programmet).

    Add-AzureRmMetricAlertRule -Name "slow responses" `
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
Jag har ett program som jag använder [TrackMetric()](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) att rapportera ett mått med namnet ”salesPerHour”. Skicka ett e-postmeddelande till min kollegor om ”salesPerHour” sjunker under 100, som ett genomsnitt under 24 timmar.

    Add-AzureRmMetricAlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

Samma regel kan användas för det mått som rapporteras med hjälp av den [mätning parametern](../../azure-monitor/app/api-custom-events-metrics.md#properties) i en annan spårnings-anrop, till exempel TrackEvent eller flyttat trackPageView.

## <a name="metric-names"></a>Tjänstmåttets namn
| Måttnamn | Skärmnamn | Beskrivning |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Webbläsarundantag |Antal undantagsfel utan felhantering som har utlösts i webbläsaren. |
| `basicExceptionServer.count` |Serverundantag |Antal ohanterade undantag som utlöses av appen |
| `clientPerformance.clientProcess.value` |Klientbehandlingstid |Tiden mellan ta emot de sista byten av ett dokument till dess att DOM har lästs. Asynkrona begäranden kan fortfarande vara under bearbetning. |
| `clientPerformance.networkConnection.value` |Nätverksanslutningstid för sidhämtning |Tid som webbläsaren tar för att ansluta till nätverket. Kan vara 0 om cachelagrade. |
| `clientPerformance.receiveRequest.value` |Tar emot svarstid |Tiden mellan webbläsaren skickar begäran till början på svar. |
| `clientPerformance.sendRequest.value` |Tid för att skicka förfrågan |Åtgången tid för webbläsaren att skicka begäran. |
| `clientPerformance.total.value` |Sidhämtningstid för webbläsare |Tiden från användarförfrågan till dess att DOM, formatmallar, skript och bilder har lästs in. |
| `performanceCounter.available_bytes.value` |Ledigt minne |Fysiskt minne som är omedelbart tillgängligt för en process eller för systemanvändning. |
| `performanceCounter.io_data_bytes_per_sec.value` |IO-frekvens för process |Totalt antal byte per sekund som har lästs och skrivits till filer, nätverk och enheter. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |antal undantag |Undantag per sekund. |
| `performanceCounter.percentage_processor_time.value` |Processoranvändning för process |Procentandelen av förfluten tid som alla processens trådar använda processorn för att köra instruktioner för hur program. |
| `performanceCounter.percentage_processor_total.value` |Processortid |Den procentandel av tiden som processorn ägnat åt icke-inaktiva trådar. |
| `performanceCounter.process_private_bytes.value` |Privata byte för process |Minne som har tilldelats exklusivt för att övervaka programprocesser. |
| `performanceCounter.request_execution_time.value` |Körningstid för ASP.NET-begäran |Körningstid för de senaste förfrågningarna. |
| `performanceCounter.requests_in_application_queue.value` |ASP.NET-begäranden i kö för körning |Programfrågeköns längd |
| `performanceCounter.requests_per_sec.value` |ASP.NET-begärandehastighet |Hastigheten per sekund för alla förfrågningar till programmet från ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Beroendefel |Antal misslyckade anrop gjorda av serverprogrammet till externa resurser. |
| `request.duration` |Serversvarstid  |Tid från det att en HTTP-förfrågning mottagits till dess att svaret har skickats. |
| `request.rate` |Förfrågningsfrekvens |Hastighet för alla förfrågningar till programmet per sekund. |
| `requestFailed.count` |Misslyckade förfrågningar |Antal HTTP-begäranden som resulterade i en svarskoden > = 400 |
| `view.count` |Sidvisning |Antal klientens användarbegäranden för en webbsida. Syntetisk trafik filtreras. |
| {din anpassade Måttnamn} |{Måttnamnet} |Din måttvärde rapporteras av [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) eller i den [mätningar av parametern för ett spårnings-anrop](../../azure-monitor/app/api-custom-events-metrics.md#properties). |

Mått som skickas av flera telemetriska moduler:

| Metrisk grupp | Insamlaren modul |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>vy |[Webbläsaren JavaScript](../../azure-monitor/app/javascript.md) |
| performanceCounter |[Prestanda](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Beroende](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| begäran<br/>requestFailed |[Serverbegäran](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooks
Du kan [automatisera dina svar på en avisering](../../azure-monitor/platform/alerts-webhooks.md). Azure anropar en webbadress för valfri när en avisering genereras.

## <a name="see-also"></a>Se också
* [Skript för att konfigurera Application Insights](powershell-script-create-resource.md)
* [Skapa Application Insights och testa webbresurser från mallar](powershell.md)
* [Automatisera koppling Microsoft Azure Diagnostics-data till Application Insights](powershell-azure-diagnostics.md)
* [Automatisera dina svar på en avisering](../../azure-monitor/platform/alerts-webhooks.md)
