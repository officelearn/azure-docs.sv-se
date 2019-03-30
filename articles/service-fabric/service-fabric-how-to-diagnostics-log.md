---
title: Generera händelser från en .NET Service Fabric-app i Azure eller en fristående-kluster
description: Mer information om hur du lägger till loggning i .NET Service Fabric-programmet finns på ett Azure-kluster eller en fristående-kluster.
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
ms.date: 03/27/2018
ms.author: srrengar
ms.openlocfilehash: d1b3dc25dd9bda9d7f9d9152c2a94cea8321f5cf
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660859"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Lägg till loggning i Service Fabric-programmet

Programmet måste ge tillräcklig information för att felsöka det kriminalteknikers när problem uppstår. Loggning är en av de viktigaste sakerna som du kan lägga till Service Fabric-programmet. När ett fel inträffar, kan bra loggning ge dig ett sätt att undersöka fel. Genom att analysera log mönster kan du hitta sätt att förbättra prestanda eller utformningen av programmet. Det här dokumentet visar några olika loggningsalternativen.

## <a name="eventflow"></a>EventFlow

Den [EventFlow biblioteket](https://github.com/Azure/diagnostics-eventflow) suite gör att program kan definiera vilka diagnostikdata för att samla in och där de bör utdata till. Diagnostics-data kan vara allt från prestandaräknarna och programspårningar. Den körs i samma process som programmet, så kommunikation kostnader minimeras. Mer information om EventFlow och Service Fabric finns i [Azure Service Fabric händelse aggregering med EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Med hjälp av strukturerade EventSource händelser

Du kan paketdata om händelsen, i samband med händelsen definierar meddelandet händelser efter användningsfall. Du kan enkelt söka och filtrera baserat på namn eller värdena för de angivna händelseegenskaperna. Strukturera instrumentation utdata gör kräver det lättare att läsa, men mer ansedda och tid för att definiera en händelse för varje användningsfall. 

Vissa Händelsedefinitioner kan delas över hela programmet. Till exempel en metod starta eller stoppa skulle händelse kan återanvändas i många tjänster i ett program. En domänspecifika tjänst, till exempel en order-system kan ha en **CreateOrder** händelse, som har sin egen unika evenemanget. Den här metoden kan generera många händelser och potentiellt kräver samordning över identifierare över projektteam. 

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

Eftersom det kan vara svårt att definiera specifika händelser, definiera många några händelser med en gemensam uppsättning parametrar som är allmänt utdata sin information som en sträng. Mycket av den strukturerade aspekten går förlorad och det är svårare att söka efter och filtrera resultaten. Några händelser som vanligtvis motsvarar loggningsnivåerna definieras i den här metoden. Följande kodavsnitt definierar ett meddelande för felsökning och fel:

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

Med hjälp av en kombination av strukturerade och allmänna instrumentation kan också fungera bra. Strukturerade instrumentation används för att rapportera fel och mått. Allmänna händelser kan användas för detaljerad loggning som förbrukas av tekniker för felsökning.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

ASP.NET Core loggning ([Microsoft.Extensions.Logging NuGet-paketet](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) är ett loggningsramverk som har ett API som standard loggning för ditt program. Stöd för andra serverdelar för loggning kan anslutas till ASP.NET Core som loggning. Detta ger dig en mängd olika stöd för loggning i ditt program har bearbetats, utan att behöva ändra mycket kod.

1. Lägg till den **Microsoft.Extensions.Logging** NuGet paket till projektet som du vill betalningsinstrument. Lägg även till några provider-paket. Mer information finns i [loggning i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Lägg till en **med** direktivet för **Microsoft.Extensions.Logging** till tjänstfilen med din.
3. Definiera en privat variabel i din tjänstklass.

   ```csharp
   private ILogger _logger = null;
   ```

4. Lägg till den här koden i konstruktorn för din tjänstklassen:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Starta arrangera din kod i din metoder. Här följer några exempel:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Med hjälp av andra leverantörer av loggning

Vissa leverantörer Använd metoden som beskrivs i föregående avsnitt, inklusive [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/), och [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Du kan ansluta var och en av dessa i ASP.NET Core loggning eller du kan använda dem separat. Serilog har en funktion som får alla meddelanden som skickas från en loggare. Den här funktionen kan vara användbart att mata ut namn, typ och partitionsinformation. Om du vill använda den här funktionen i ASP.NET Core-infrastrukturen, gör du följande:

1. Lägg till den **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate**, och **Serilog.Sinks.Observable** NuGet-paket i projektet. 
2. Skapa en `LoggerConfiguration` och logger-instans.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Lägg till en `Serilog.ILogger` argumentet för konstruktorn för tjänsten och pass nyligen skapade loggaren.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. I konstruktorn service skapar egenskapen enrichers för **ServiceTypeName**, **ServiceName**, **PartitionId**, och **InstanceId** .

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
   >Vi rekommenderar att du *inte* använder du statiskhet `Log.Logger` med föregående exempel. Service Fabric kan vara värd för flera instanser av samma tjänsttyp inom en enda process. Om du använder statiskhet `Log.Logger`, den senaste skrivaren av egenskapen enrichers visas värdena för alla instanser som körs. Det här är ett skäl till varför _logger variabeln är en privat medlemsvariabel i klassen service. Dessutom måste du se den `_logger` tillgängliga för gemensam kod som kan användas för tjänster.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [program övervakning i Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Läs mer om loggning med [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) och [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).










