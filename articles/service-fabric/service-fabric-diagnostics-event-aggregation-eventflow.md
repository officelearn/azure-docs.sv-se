---
title: Azure Service Fabric-händelseaggregering med EventFlow
description: Lär dig mer om hur du samlar in och samlar in händelser med EventFlow för övervakning och diagnostik av Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: cde24657cc8ed78b91e72df16d51df4077a6e030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463088"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Händelseaggregering och samling med EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) kan dirigera händelser från en nod till ett eller flera övervakningsmål. Eftersom det ingår som ett NuGet-paket i ditt serviceprojekt, eventflow-kod och konfigurationsresor med tjänsten, vilket eliminerar konfigurationsproblemet per nod som nämndes tidigare om Azure Diagnostics. EventFlow körs inom din tjänstprocess och ansluter direkt till de konfigurerade utgångarna. På grund av den direkta anslutningen fungerar EventFlow för Azure, behållare och lokala tjänstdistributioner. Var försiktig om du kör EventFlow i scenarier med hög densitet, till exempel i en behållare, eftersom varje EventFlow-pipeline gör en extern anslutning. Så, om du är värd för flera processer, får du flera utgående anslutningar! Detta är inte lika mycket ett problem för Service Fabric-program, eftersom alla repliker av en `ServiceType` körning i samma process, och detta begränsar antalet utgående anslutningar. EventFlow erbjuder också händelsefiltrering, så att endast de händelser som matchar det angivna filtret skickas.

## <a name="set-up-eventflow"></a>Konfigurera EventFlow

EventFlow binärfiler är tillgängliga som en uppsättning NuGet-paket. Om du vill lägga till EventFlow i ett serviceinfrastrukturprojekt högerklickar du på projektet i Lösningsutforskaren och väljer Hantera NuGet-paket. Växla till fliken "Bläddra" och`Diagnostics.EventFlow`sök efter " ":

![EventFlow NuGet-paket i Visual Studio NuGet pakethanterare UI](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Du kommer att se en lista över olika paket dyker upp, märkta med "Ingångar" och "Utdata". EventFlow stöder olika loggningsleverantörer och analysatorer. Tjänsten som är värd för EventFlow bör innehålla lämpliga paket beroende på källan och målet för programloggarna. Förutom det centrala ServiceFabric-paketet behöver du också minst en in- och utdata som konfigurerats. Du kan till exempel lägga till följande paket för att skicka EventSource-händelser till Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource`för att samla in data från tjänstens EventSource-klass och från vanliga EventSources som *Microsoft-ServiceFabric-Services* och *Microsoft-ServiceFabric-Actors*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`(vi kommer att skicka loggarna till en Azure Application Insights-resurs)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(möjliggör initiering av EventFlow-pipelinen från servicekonfigurationen för Service Fabric och rapporterar eventuella problem med att skicka diagnostikdata som hälsorapporter för Service Fabric)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource`kräver att serviceprojektet inriktas på .NET Framework 4.6 eller nyare. Se till att du anger lämpligt målramverk i projektegenskaper innan du installerar det här paketet.

När alla paket har installerats är nästa steg att konfigurera och aktivera EventFlow i tjänsten.

## <a name="configure-and-enable-log-collection"></a>Konfigurera och aktivera loggsamling
EventFlow-pipelinen som ansvarar för att skicka loggarna skapas från en specifikation som lagras i en konfigurationsfil. Paketet `Microsoft.Diagnostics.EventFlow.ServiceFabric` installerar en startad EventFlow-konfigurationsfil under `PackageRoot\Config` lösningsmappen med namnet `eventFlowConfig.json`. Den här konfigurationsfilen måste ändras för `EventSource` att samla in data från standardtjänstklassen och alla andra indata som du vill konfigurera och skicka data till rätt plats.

>[!NOTE]
>Om din projektfil har VisualStudio 2017-format läggs `eventFlowConfig.json` filen inte till automatiskt. Så här åtgärdar du `Config` den här filen `Copy if newer`i mappen och ställer in byggåtgärden på . 

Här är ett exempel *eventFlowConfig.json* baserat på NuGet-paketen som nämns ovan:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

Namnet på tjänstens ServiceEventSource är värdet för egenskapen Name för den `EventSourceAttribute` tillämpade på klassen ServiceEventSource. Allt anges i `ServiceEventSource.cs` filen, som ingår i servicekoden. I följande kodavsnitt är namnet på ServiceEventSource till exempel *MyCompany-Application1-Stateless1:*

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Observera `eventFlowConfig.json` att filen är en del av tjänstkonfigurationspaketet. Ändringar av den här filen kan inkluderas i fullständiga eller konfigurationsmässiga uppgraderingar av tjänsten, med förbehåll för hälsokontroller av Uppgradering av Service Fabric och automatisk återställning om det finns uppgraderingsfel. Mer information finns i [Uppgradering av Service Fabric-program](service-fabric-application-upgrade.md).

*Filteravsnittet* i config kan du ytterligare anpassa den information som kommer att gå igenom EventFlow-pipelinen till utdata, så att du kan släppa eller inkludera viss information, eller ändra strukturen på händelsedata. Mer information om filtrering finns i [EventFlow-filter](https://github.com/Azure/diagnostics-eventflow#filters).

Det sista steget är att instansiera EventFlow-pipelinen i `Program.cs` tjänstens startkod, som finns i filen:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

Namnet som skickas som `CreatePipeline` parameter för `ServiceFabricDiagnosticsPipelineFactory` metoden för är namnet på *hälsoentiteten* som representerar Pipelinen för EventFlow-loggsamling. Det här namnet används om EventFlow uppstår och fel och rapporterar det via hälsoundersystemet Service Fabric.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Använd inställningar och programparametrar för Service Fabric i eventFlowConfig

EventFlow stöder användning av inställningar för Service Fabric och programparametrar för att konfigurera EventFlow-inställningar. Du kan referera till parametrar för inställningar för Service Fabric med den här speciella syntaxen för värden:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>`är namnet på konfigurationsavsnittet `<setting-name>` för Service Fabric och är konfigurationsinställningen som anger det värde som ska användas för att konfigurera en EventFlow-inställning. Mer information om hur du gör detta finns i [Inställningar för Support för Service Fabric och programparametrar](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Verifiering

Starta tjänsten och observera fönstret Felsökningsutdata i Visual Studio. När tjänsten har startats bör du börja se bevis på att tjänsten skickar poster till utdata som du har konfigurerat. Navigera till din händelseanalys- och visualiseringsplattform och bekräfta att loggarna har börjat visas (kan ta några minuter).

## <a name="next-steps"></a>Nästa steg

* [Händelseanalys och visualisering med programinsikter](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Händelseanalys och visualisering med Azure Monitor-loggar](service-fabric-diagnostics-event-analysis-oms.md)
* [Dokumentation för EventFlow](https://github.com/Azure/diagnostics-eventflow)
