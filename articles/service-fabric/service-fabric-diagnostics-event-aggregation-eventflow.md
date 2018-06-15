---
title: Azure Service Fabric händelse aggregeringen med EventFlow | Microsoft Docs
description: Läs mer om sammanställa och samlar in händelser med hjälp av EventFlow för övervakning och diagnostik av Azure Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 9b851b2d75cf78a02dd223788085ac9a0963376e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211362"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Aggregering av händelse och med EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) kan vidarebefordra händelser från en nod till en eller flera övervakning mål. Eftersom den ingår som en NuGet-paket i projektet service färdas EventFlow koden och konfigurationen med tjänsten, vilket eliminerar per nod konfigurationsproblem som tidigare nämnts om Azure-diagnostik. EventFlow körs i tjänstprocessen och ansluter direkt till de konfigurerade utdata. På grund av direkt anslutning fungerar EventFlow för Azure-behållaren och lokala distributioner. Var försiktig om du kör EventFlow i scenarier med hög densitet som i en behållare, eftersom varje EventFlow pipeline gör en extern anslutning. Så om du har flera processer får du flera utgående anslutningar! Detta är inte lika mycket problem för Service Fabric-program, eftersom alla repliker av en `ServiceType` körs i samma process och det begränsar antalet utgående anslutningar. EventFlow erbjuder också händelsefiltrering så att bara de händelser som matchar det angivna filtret skickas.

## <a name="set-up-eventflow"></a>Ställ in EventFlow

EventFlow binärfiler är tillgängliga som en uppsättning NuGet-paket. Om du vill lägga till EventFlow till ett Service Fabric service-projekt, högerklicka på projektet i Solution Explorer och välj ”Hantera NuGet-paket”. Gå till fliken ”Bläddra” och Sök efter ”`Diagnostics.EventFlow`”:

![EventFlow NuGet-paket i Visual Studio-NuGet-Pakethanteraren UI](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

En lista över olika paket visas, märkt med ”anger” och ”utdata” visas. EventFlow stöder olika providrar för olika loggning och analyzers. Tjänsten värd för EventFlow bör inkludera paket beroende på käll- och mål för programloggarna. Förutom core ServiceFabric paketet, du måste också ha minst ett indata och utdata har konfigurerats. Du kan till exempel lägga till följande paket för att skicka EventSource händelser till Application Insights:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` att samla in data från EventSource tjänstklass och standard EventSources som *Microsoft ServiceFabric Services* och *Microsoft-ServiceFabric-aktörer*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (vi ska skicka loggar till en Azure Application Insights-resurs)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(gör det möjligt för initiering av EventFlow pipeline från Service Fabric tjänstkonfiguration och rapporterar eventuella problem med att skicka diagnostikdata som Service Fabric hälsorapporter)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` paketet kräver service-projekt att rikta .NET Framework 4.6 eller senare. Kontrollera att du anger rätt Målversionen av framework i projektegenskaperna innan du installerar det här paketet.

När alla paket som är installerade, är nästa steg att konfigurera och aktivera EventFlow i tjänsten.

## <a name="configure-and-enable-log-collection"></a>Konfigurera och aktivera Logginsamling
Ansvarar för att skicka loggar EventFlow pipelinen har skapats från en specifikation som lagras i en konfigurationsfil. Den `Microsoft.Diagnostics.EventFlow.ServiceFabric` paketet installerar en första EventFlow konfigurationsfil under `PackageRoot\Config` lösningsmapp som heter `eventFlowConfig.json`. Den här konfigurationsfilen måste ändras för att samla in data från standardtjänsten `EventSource` klass och andra indata som du vill konfigurera och skicka data till rätt plats.

>[!NOTE]
>Om projektfilen har VisualStudio 2017 format på `eventFlowConfig.json` kommer inte att automatiskt lägga till filen. Åtgärda detta skapa filen i den `Config` mapp och ange build-åtgärd till `Copy if newer`. 

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

Namnet på tjänstens ServiceEventSource är värdet för egenskapen Name för den `EventSourceAttribute` tillämpas på ServiceEventSource-klass. Det har angetts i den `ServiceEventSource.cs` fil som är en del av kod. Till exempel i följande kodavsnitt i ServiceEventSource heter *mitt företag-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Observera att `eventFlowConfig.json` filen är en del av tjänsten konfigurationspaket. Ändringar i den här filen kan ingå i fullständig eller configuration-endast uppgraderingar av tjänsten, omfattas Service Fabric-uppgradera hälsokontroller och automatisk återställning om uppgraderingen skulle misslyckas. Mer information finns i [uppgradering av Service Fabric-programmet](service-fabric-application-upgrade.md).

Den *filter* avsnittet av konfig kan du ytterligare anpassa den information som kommer att gå via EventFlow pipeline till utdata, så att du kan släppa eller innehålla viss information eller ändra strukturen för informationen om händelsen. Mer information om filtrering finns [EventFlow filter](https://github.com/Azure/diagnostics-eventflow#filters).

Det sista steget är att skapa en instans av EventFlow pipeline i din tjänst startkoden finns i `Program.cs` fil:

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

Namnet som angavs som parameter för den `CreatePipeline` metod för den `ServiceFabricDiagnosticsPipelineFactory` är namnet på den *hälsa entiteten* som representerar EventFlow loggen samling pipeline. Det här namnet används om EventFlow påträffar och felmeddelandet och rapporter via undersystemet health Service Fabric.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Använda Service Fabric-inställningar och programparametrar i eventFlowConfig

EventFlow stöder användningen av Service Fabric-inställningar och program parametrarna för att konfigurera inställningar för EventFlow. Du kan referera till Service Fabric inställningar parametrar med den här särskilda syntaxen för värden:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` är namnet på konfigurationsavsnittet Service Fabric och `<setting-name>` är Konfigurationsinställningen med ett värde som ska användas för att konfigurera en EventFlow inställning. Att läsa mer om hur du gör detta, gå till [stöd för Service Fabric-inställningar och programparametrar](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Verifiering

Starta tjänsten och notera att felsöka utdatafönstret i Visual Studio. När tjänsten har startats borde du se bevis som din tjänst skickar poster till utdata som du har konfigurerat. Navigera till din händelse analys och visualisering plattform och bekräftar du att loggarna har startats att visa upp (kan ta några minuter).

## <a name="next-steps"></a>Nästa steg

* [Händelseanalys och visualisering med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Händelseanalys och visualisering med logganalys](service-fabric-diagnostics-event-analysis-oms.md)
* [EventFlow dokumentation](https://github.com/Azure/diagnostics-eventflow)
