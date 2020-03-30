---
title: Generera logghändelser från en .NET-app
description: Lär dig mer om hur du lägger till loggning i .NET Service Fabric-programmet som finns i ett Azure-kluster eller ett fristående kluster.
author: srrengar
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: srrengar
ms.openlocfilehash: 8c4721584e74bd7f7111c516f2d16bd190392bb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614374"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Lägga till loggning för ditt Service Fabric-program

Ditt program måste ge tillräckligt med information för att kriminaltekniskt felsöka den när problem uppstår. Loggning är en av de viktigaste sakerna du kan lägga till i ditt Service Fabric-program. När ett fel inträffar kan bra loggning ge dig ett sätt att undersöka fel. Genom att analysera loggmönster kan du hitta sätt att förbättra programmets prestanda eller design. Det här dokumentet visar några olika loggningsalternativ.

## <a name="eventflow"></a>EventFlow (Händelseflöde)

[Med EventFlow-bibliotekspaketet](https://github.com/Azure/diagnostics-eventflow) kan program definiera vilka diagnostikdata som ska samlas in och var de ska matas ut. Diagnostikdata kan vara allt från prestandaräknare till programspårningar. Den körs i samma process som programmet, så kommunikation overhead minimeras. Mer information om EventFlow och Service Fabric finns i [Azure Service Fabric Event Aggregation med EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Använda strukturerade EventSource-händelser

Genom att definiera meddelandehändelser efter användningsfall kan du paketera data om händelsen i samband med händelsen. Du kan lättare söka och filtrera baserat på namnen eller värdena för de angivna händelseegenskaperna. Strukturera instrumenteringsutgången gör det lättare att läsa, men kräver mer eftertanke och tid för att definiera en händelse för varje användningsfall. 

Vissa händelsedefinitioner kan delas över hela programmet. En metodstart- eller stopphändelse skulle till exempel återanvändas för många tjänster i ett program. En domänspecifik tjänst, som ett ordersystem, kan ha en **CreateOrder-händelse** som har en egen unik händelse. Den här metoden kan generera många händelser och eventuellt kräva samordning av identifierare mellan projektgrupper. 

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

### <a name="using-eventsource-generically"></a>Använda EventSource allmänt

Eftersom det kan vara svårt att definiera specifika händelser definierar många några händelser med en gemensam uppsättning parametrar som i allmänhet matar ut sin information som en sträng. Mycket av den strukturerade aspekten går förlorad, och det är svårare att söka och filtrera resultaten. I den här metoden definieras några händelser som vanligtvis motsvarar loggningsnivåerna. Följande kodavsnitt definierar ett felsöknings- och felmeddelande:

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

Med hjälp av en hybrid av strukturerad och generisk instrumentering kan också fungera bra. Strukturerad instrumentering används för att rapportera fel och mått. Allmänna händelser kan användas för detaljerad loggning som förbrukas av tekniker för felsökning.

## <a name="microsoftextensionslogging"></a>Loggning av Microsoft.Extensions.Logging

ASP.NET Core-loggning ([Microsoft.Extensions.Logging NuGet-paketet)](https://www.nuget.org/packages/Microsoft.Extensions.Logging)är ett loggningsramverk som tillhandahåller ett standardloggnings-API för ditt program. Stöd för andra loggningsstöd kan anslutas till ASP.NET Core-loggning. Detta ger dig ett brett utbud av stöd för att logga in ditt program behandlas, utan att behöva ändra mycket kod.

1. Lägg till **paketet Microsoft.Extensions.Logging** NuGet i det projekt som du vill instrumentera. Lägg också till alla leverantörspaket. Mer information finns [i Logga in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Lägg till ett **direktiv med användning för** **Microsoft.Extensions.Logging** i tjänstfilen.
3. Definiera en privat variabel i din serviceklass.

   ```csharp
   private ILogger _logger = null;
   ```

4. Lägg till den här koden i konstruktorn för din serviceklass:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Börja instrumentera koden i dina metoder. Här är några exempel:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Använda andra loggningsleverantörer

Vissa tredjepartsleverantörer använder den metod som beskrivs i föregående avsnitt, inklusive [Serilog,](https://serilog.net/) [NLog](https://nlog-project.org/)och [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Du kan ansluta var och en av dessa till ASP.NET Core-loggning, eller så kan du använda dem separat. Serilog har en funktion som berikar alla meddelanden som skickas från en logger. Den här funktionen kan vara användbar för att mata ut servicenamn, typ och partitionsinformation. Så här använder du den här funktionen i infrastrukturen för ASP.NET Core:

1. Lägg till **Serilog,** **Serilog.Extensions.Logging,** **Serilog.Sinks.Skrivkunnig**och **Serilog.Sinks.Observable** NuGet paket till projektet. 
2. Skapa `LoggerConfiguration` en och logger-instansen.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Lägg `Serilog.ILogger` till ett argument i servicekonstruktorn och skicka den nyskapade loggern.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. I tjänstkonstruktorn skapar egenskapsberikare för **ServiceTypeName**, **ServiceName**, **PartitionId**och **InstanceId**.

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

5. Instrumentera koden på samma sätt som om du använde ASP.NET Core utan Serilog.

   >[!NOTE]
   >Vi rekommenderar att du *inte* `Log.Logger` använder den statiska med föregående exempel. Service Fabric kan vara värd för flera instanser av samma tjänsttyp i en enda process. Om du använder `Log.Logger`den statiska visas värden för alla instanser som körs den sista författaren av egenskapen. Detta är en orsak till att variabeln _logger är en privat medlemsvariabel för serviceklassen. Du måste också `_logger` göra den tillgängliga för gemensam kod, som kan användas mellan tjänster.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [programövervakning i Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Läs om loggning med [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) och [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).










