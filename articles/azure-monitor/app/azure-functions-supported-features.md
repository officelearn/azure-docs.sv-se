---
title: Azure Application Insights – Azure Functions funktioner som stöds | Microsoft Docs
description: Application Insights funktioner som stöds för Azure Functions
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 31f962ca96ca5c47d18f9250e567abb8f4024e6f
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677559"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights för Azure Functions stödda funktioner

Azure Functions erbjuder [inbyggd integrering](../../azure-functions/functions-monitoring.md) med Application Insights, som är tillgänglig via ILogger-gränssnittet. Nedan visas en lista över funktioner som stöds för närvarande. Granska Azure Functionss guide för att [komma igång](../../azure-functions/functions-monitoring.md#enable-application-insights-integration).

## <a name="supported-features"></a>Funktioner som stöds

| Azure Functions                       | V1                | V2 (antändning 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Automatisk insamling av**        |                 |                   |               
| &bull;-begäranden                     | Ja             | Ja               | 
| &bull; undantag                   | Ja             | Ja               | 
| &bull; prestanda räknare         | Ja             | Ja               |
| &bull;-beroenden                   |                   |                   |               
| &nbsp; &nbsp; &nbsp; &mdash; HTTP      |                 | Ja               | 
| &nbsp; &nbsp; &nbsp; &mdash; Service Bus|                 | Ja               | 
| &nbsp; &nbsp; &nbsp; &mdash; EventHub  |                 | Ja               | 
| &nbsp; &nbsp; &nbsp; &mdash; SQL       |                 | Ja               | 
| | | | 
| **Funktioner som stöds**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Ja             | Ja               | 
| &nbsp; &nbsp; &nbsp; &mdash; säker kontroll kanal|                 | Ja               | 
| &bull;-sampling                     | Ja             | Ja               | 
| &bull; pulsslag                   |                 | Ja               | 
| | | | 
| **Korrelation**                       |                   |                   |               
| &bull; Service Bus                     |                   | Ja               | 
| EventHub-&bull;                       |                   | Ja               | 
| | | | 
| **Konfigurerbara**                      |                   |                   |           
| &bull;Fully kan konfigureras.<br/>Se [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) för instruktioner.<br/>Se [ASP.net Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) för alla alternativ.               |                   | Ja                   | 


## <a name="performance-counters"></a>Prestandaräknare

Automatisk insamling av prestanda räknare fungerar bara på Windows-datorer.


## <a name="live-metrics--secure-control-channel"></a>Live Metrics & säker kontroll kanal

De anpassade filter kriterier som du anger skickas tillbaka till komponenten Live Metrics i Application Insights SDK. Filtren kan eventuellt innehålla känslig information, till exempel customerID. Du kan göra kanalen säker med en hemlig API-nyckel. Mer information finns i [skydda kontroll kanalen](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) .

## <a name="sampling"></a>Sampling

Azure Functions aktiverar sampling som standard i konfigurationen. Mer information finns i [Konfigurera sampling](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Om projektet tar ett beroende på Application Insights SDK för att utföra manuell telemetri, kan det uppstå onormalt beteende om samplings konfigurationen skiljer sig från funktionerna i samplings konfigurationen. 

Vi rekommenderar att du använder samma konfiguration som-funktionerna. Med **Functions v2**kan du hämta samma konfiguration med hjälp av beroende inmatning i konstruktorn:

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
