---
title: Azure Service Fabric händelse aggregering med EventFlow | Microsoft Docs
description: Läs mer om sammanställa och samla in händelser med EventFlow för övervakning och diagnostik för Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: bdc6c9476529b986f425d56544fd4b1afd8a864e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60393236"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Händelsen aggregering och samling med EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) kan vidarebefordra händelser från en nod till en eller flera övervakning mål. Eftersom det är inkluderat som NuGet-paket i ditt tjänstprojekt färdas EventFlow kod och konfiguration med tjänsten, vilket eliminerar per nod konfigurationsproblem som vi nämnde tidigare om Azure Diagnostics. EventFlow körs med din tjänst-process och ansluter direkt till de konfigurerade utdata. På grund av direkt anslutning fungerar EventFlow för Azure, behållare och distribution av lokala tjänster. Var försiktig om du kör EventFlow i Högdensitet scenarier, till exempel i en behållare, eftersom varje pipeline som EventFlow gör en extern anslutning. Så om du vara värd för flera processer, får du flera utgående anslutningar! Detta är inte så mycket ett problem för Service Fabric-program, eftersom alla repliker av en `ServiceType` körs i samma process och det begränsar antalet utgående anslutningar. EventFlow erbjuder även händelsefiltrering, så att endast de händelser som matchar det angivna filtret skickas.

## <a name="set-up-eventflow"></a>Konfigurera EventFlow

EventFlow binärfiler är tillgängliga som en uppsättning NuGet-paket. Om du vill lägga till EventFlow i ett Service Fabric-tjänstprojekt, högerklicka på projektet i Solution Explorer och välj ”Hantera NuGet-paket”. Växla till fliken ”Bläddra” och Sök efter ”`Diagnostics.EventFlow`”:

![EventFlow NuGet-paket i Visual Studio-NuGet-Pakethanteraren UI](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Du kommer se en lista över olika paket visas, märkta med ”indata” och ”utdata”. EventFlow har stöd för olika leverantörer av olika loggning och analysverktyg. Den tjänstevärden EventFlow innehålla lämplig paket beroende på källan och målet för programloggarna. Du måste också minst en inmatning och utdata konfigurerad förutom core ServiceFabric-paketet. Du kan till exempel lägga till följande paket för att skicka EventSource händelser till Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` samla in data från tjänstens händelsekälla klass och standard EventSources som *ServiceFabric-Microsoft-tjänster* och *Microsoft-ServiceFabric-aktörer*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (vi kommer att skicka loggarna till en Azure Application Insights-resurs)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(gör initieringen av EventFlow pipeline från Service Fabric-tjänstkonfigurationen och rapporterar eventuella problem med att skicka diagnostikdata som hälsorapporter i Service Fabric)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` service-projekt att fokusera på .NET Framework 4.6 eller senare krävs. Se till att ange lämplig målramverk i projektegenskaperna innan du installerar det här paketet.

När du har installerat alla paket är nästa steg att konfigurera och aktivera EventFlow i tjänsten.

## <a name="configure-and-enable-log-collection"></a>Konfigurera och aktivera Logginsamling
Ansvarar för att skicka loggar EventFlow pipelinen har skapats från en specifikation som lagras i en konfigurationsfil. Den `Microsoft.Diagnostics.EventFlow.ServiceFabric` paketet installerar en från EventFlow konfigurationsfil under `PackageRoot\Config` lösningsmappen, med namnet `eventFlowConfig.json`. Konfigurationsfilen ska ändras för att samla in data från standardtjänsten `EventSource` klass och andra indata som du vill konfigurera och skicka data till rätt plats.

>[!NOTE]
>Om projektfilen har VisualStudio 2017 format den `eventFlowConfig.json` kommer inte att automatiskt lägga till filen. Åtgärda detta skapa filen i den `Config` mapp och ange byggåtgärden till `Copy if newer`. 

Här är ett exempel *eventFlowConfig.json* baserat på NuGet-paket som nämns ovan:
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

Namnet på tjänstens ServiceEventSource är värdet för egenskapen namn för den `EventSourceAttribute` tillämpas på klassen ServiceEventSource. Det har angetts i den `ServiceEventSource.cs` -fil som är en del av kod. Till exempel i följande kodavsnitt namnet på ServiceEventSource är *mitt företag-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Observera att `eventFlowConfig.json` filen är en del av tjänsten konfigurationspaket. Ändringar i den här filen kan ingå i full - eller enbart uppgraderingar av tjänsten, omfattas av Service Fabric uppgradera hälsokontroller och automatisk återställning om uppgraderingen skulle misslyckas. Mer information finns i [Service Fabric-Programuppgradering](service-fabric-application-upgrade.md).

Den *filter* avsnittet av konfig kan du ytterligare anpassa informationen som kommer att gå genom pipelinen EventFlow till utdata, så att du kan släppa eller innehåller viss information eller ändra strukturen för de händelsedata. Mer information om filtrering finns [EventFlow filter](https://github.com/Azure/diagnostics-eventflow#filters).

Det sista steget är att skapa en instans av EventFlow pipeline i tjänstens Start code finns i `Program.cs` fil:

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

Namnet som skickas som parameter för den `CreatePipeline` -metoden för den `ServiceFabricDiagnosticsPipelineFactory` är namnet på den *hälsotillstånd entitet* som representerar EventFlow log samling pipeline. Det här namnet används om EventFlow påträffar och fel och rapporterar via undersystemet för health Service Fabric.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Använda Service Fabric-inställningar och programparametrar i eventFlowConfig

EventFlow har stöd för att konfigurera EventFlow inställningar med Service Fabric-inställningar och parametrar för programmet. Du kan referera till Service Fabric inställningar parametrar med hjälp av den här specialsyntax för värden:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` är namnet på avsnittet för konfiguration av Service Fabric och `<setting-name>` är Konfigurationsinställningen ett värde som ska användas för att konfigurera en EventFlow-inställningen. Att läsa mer om hur du gör detta, gå till [stöd för Service Fabric-inställningar och programparametrar](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Verifiering

Starta tjänsten och noterar felsöka utdatafönstret i Visual Studio. När tjänsten startas, bör du börja se bevis på att tjänsten skickar poster till utdata som du har konfigurerat. Gå till din event analys och visualisering plattform och bekräfta att loggarna har startats att visa upp (du kan ta några minuter).

## <a name="next-steps"></a>Nästa steg

* [Händelseanalys och visualisering med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Händelseanalys och visualisering med Azure Monitor-loggar](service-fabric-diagnostics-event-analysis-oms.md)
* [Dokumentation om EventFlow](https://github.com/Azure/diagnostics-eventflow)
