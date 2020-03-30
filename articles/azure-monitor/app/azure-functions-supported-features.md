---
title: Azure Application Insights – Funktioner som stöds av Azure-funktioner
description: Funktioner som stöds av Application Insights för Azure-funktioner
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: cf0c97fd65f9966bf42fa22e2c8f92263952cb7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655658"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Programinsikter för Azure Functions-funktioner som stöds

Azure Functions erbjuder [inbyggd integrering](../../azure-functions/functions-monitoring.md) med Application Insights, som är tillgängligt via ILogger-gränssnittet. Nedan visas en lista över funktioner som stöds för närvarande. Granska Azure Functions guide för [att komma igång](../../azure-functions/functions-monitoring.md#enable-application-insights-integration).

Mer information om Funktioner körtid versioner, se [här](../../azure-functions/functions-versions.md).

Mer information om kompatibla versioner av Application Insights finns i [Beroenden](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

## <a name="supported-features"></a>Funktioner som stöds

| Azure Functions                       | V1                | V2 & V3   | 
|-----------------------------------    |---------------    |------------------ |
| | | | 
| **Automatisk insamling av**        |                 |                   |               
| &bull;Begäranden                     | Ja             | Ja               | 
| &bull;Undantag                   | Ja             | Ja               | 
| &bull;Prestandaräknare         | Ja             | Ja               |
| &bull;Beroenden                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash;HTTP      |                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash;ServiceBuss|                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash;EventHub  |                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash;Sql       |                 | Ja               | 
| | | | 
| **Funktioner som stöds**                |                   |                   |               
| &bull;QuickPulse/LiveMetri       | Ja             | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash;Säker kontrollkanal|                 | Ja               | 
| &bull;Provtagning                     | Ja             | Ja               | 
| &bull;Hjärtslag                   |                 | Ja               | 
| | | | 
| **Korrelation**                       |                   |                   |               
| &bull;ServiceBuss                     |                   | Ja               | 
| &bull;EventHub                       |                   | Ja               | 
| | | | 
| **Konfigurerbara**                      |                   |                   |           
| &bull;Fullt konfigurerbar.<br/>Se [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) för instruktioner.<br/>Se [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) för alla alternativ.               |                   | Ja                   | 


## <a name="performance-counters"></a>Prestandaräknare

Automatisk samling av prestandaräknare fungerar bara Windows-datorer.


## <a name="live-metrics--secure-control-channel"></a>Live-mått & secure control-kanal

De anpassade filterkriterier som du anger skickas tillbaka till komponenten Live Metrics i SDK för programinsikter. Filtren kan eventuellt innehålla känslig information, till exempel kund-ID: er. Du kan göra kanalen säker med en hemlig API-nyckel. Se [Säkra kontrollkanalen](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) för instruktioner.

## <a name="sampling"></a>Samling

Azure Functions aktiverar Sampling som standard i konfigurationen. Mer information finns i [Konfigurera sampling](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Om ditt projekt är beroende av SDK för programinsikter för manuell telemetrispårning kan det uppstå ett konstigt beteende om samplingskonfigurationen skiljer sig från functions samplingskonfiguration. 

Vi rekommenderar att du använder samma konfiguration som Funktioner. Med **Functions v2**kan du få samma konfiguration med hjälp av beroendeinjektion i konstruktorn:

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
