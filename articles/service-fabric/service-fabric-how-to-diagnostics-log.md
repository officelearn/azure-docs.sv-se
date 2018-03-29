---
title: Generera händelser från en .NET Service Fabric-app i Azure eller en fristående-kluster
description: Läs mer om hur du lägger till loggning ditt .NET Service Fabric-program som finns på ett Azure-kluster eller en fristående-kluster.
services: service-fabric
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/27/2018
ms.author: adegeo
ms.openlocfilehash: 7c1a2330b9eb595d05df31aa8511720911dfee97
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="add-logging-to-your-service-fabric-application"></a>Lägg till loggning till Service Fabric-program

Programmet måste ger tillräcklig information för att felsöka den kriminalteknikers när problem uppstår. Loggning är en av de viktigaste sakerna som du kan lägga till Service Fabric-programmet. När ett fel uppstår kan bra loggning ge dig ett sätt att undersöka fel. Du kan hitta sätten att förbättra prestanda och utformning av ditt program genom att analysera loggen mönster. Det här dokumentet visar några olika alternativ.

## <a name="eventflow"></a>EventFlow

Den [EventFlow biblioteket](https://github.com/Azure/diagnostics-eventflow) suite gör att program kan definiera vilka diagnostikdata att samla in och där de bör utdata till. Diagnostikdata kan vara allt från prestandaräknare som programspårningar. Det körs i samma process som programmet, så overhead kommunikation minimeras. Mer information om EventFlow och Service Fabric finns [Azure Service Fabric händelse aggregeringen med EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Med hjälp av strukturerade EventSource händelser

Du kan paketdata om händelsen i samband med händelsen definierande meddelandet händelser efter användningsfall. Enklare kan du söka och filtrera baserat på namn eller värde med de angivna händelseegenskaperna. Strukturera instrumentation utdata blir kräver det lättare att läsa, men mer tankar och tid för att definiera en händelse för varje användningsfall. 

Vissa Händelsedefinitioner kan delas över hela programmet. Till exempel en metod starta eller stoppa skulle händelsen återanvändas i många tjänster i ett program. En domänspecifika-tjänst som ett order-system kan ha en **CreateOrder** händelsen, som har en egen unik händelse. Den här metoden kan generera många händelser och potentiellt kräver samordning av identifierare i projektgruppen. 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>Med hjälp av EventSource Allmänt

Eftersom kan vara svårt att definiera specifika händelser, definiera många några händelser med en gemensam uppsättning parametrar som vanligtvis utdata av information som en sträng. Mycket av strukturerade aspekten går förlorad och det är svårare att söka och filtrera resultaten. Några händelser som vanligtvis motsvarar loggningsnivåerna definieras i den här metoden. Följande kodavsnitt definierar ett felsöknings- och fel meddelande:

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

Med hjälp av en kombination av strukturerade och allmänna instrumentation kan fungerar också bra. Strukturerade instrumentation används för att rapportera fel och mått. Allmänna händelser kan användas för detaljerad loggning som används av tekniker för felsökning.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

ASP.NET-Core loggning ([Microsoft.Extensions.Logging NuGet-paketet](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) är ett loggningsramverk som tillhandahåller en standard loggning API för ditt program. Stöd för andra loggning serverdelar kan anslutas till ASP.NET Core loggning. Detta ger dig en mängd olika stöd för loggning i ditt program bearbetas, utan att ändra koden för mycket.

1. Lägg till den **Microsoft.Extensions.Logging** NuGet paket i projektet som du vill betalningsinstrument. Lägg även till eventuella provider-paket. Mer information finns i [loggning i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Lägg till en **med** direktiv för **Microsoft.Extensions.Logging** till service-fil.
3. Definiera en privat variabel inom service-klassen.

   ```csharp
   private ILogger _logger = null;
   ```

4. Lägg till den här koden i konstruktorn för tjänstklassen:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Starta instrumentering koden i din metoder. Här följer några exempel:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Med hjälp av andra leverantörer av loggning

Vissa leverantörer används metoden som beskrivs i föregående avsnitt, inklusive [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/), och [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Du kan ansluta var och en av dessa i ASP.NET Core loggning eller du kan använda dem separat. Serilog har en funktion som förbättra alla meddelanden från en logg. Den här funktionen kan vara användbara för utgående namn, typ och partitionsinformation. Om du vill använda den här funktionen i ASP.NET grundläggande infrastruktur för att utföra de här stegen:

1. Lägg till den **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate**, och **Serilog.Sinks.Observable** NuGet-paket i projektet. 
2. Skapa en `LoggerConfiguration` och transaktionsloggfiler-instans.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Lägg till en `Serilog.ILogger` argument för konstruktorn för tjänsten och pass nyskapade loggaren.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. I konstruktorn tjänsten skapar egenskapen enrichers för **ServiceTypeName**, **ServiceName**, **PartitionId**, och **InstanceId** .

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. Instrumentera koden samma som om du använde ASP.NET Core utan Serilog.

   >[!NOTE]
   >Vi rekommenderar att du *inte* använder statiskhet `Log.Logger` med föregående exempel. Service Fabric kan vara värd för flera instanser av samma service-typen i en enda process. Om du använder statiskhet `Log.Logger`, den senaste skrivaren av egenskapen enrichers visas värdena för alla instanser som körs. Det här är ett skäl till varför _logger variabeln är en privat medlemsvariabel av tjänstklassen. Du måste också göra den `_logger` tillgänglig för vanliga kod som kan användas för tjänster.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [program övervakning i Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Läs mer om loggning med [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) och [Windows Azure-diagnostik](service-fabric-diagnostics-event-aggregation-wad.md).










