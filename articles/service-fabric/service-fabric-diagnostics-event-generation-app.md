---
title: "Azure Service Fabric-programnivå övervakning | Microsoft Docs"
description: "Läs mer om programmet och service händelser och loggar som används för att övervaka och diagnostisera Azure Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 9b07c7cd256cea31c5654f0c3325e9fb675b39f8
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="application-and-service-level-event-and-log-generation"></a>Program- och nivå händelse och logg

## <a name="instrumenting-the-code-with-custom-events"></a>Instrumentering koden med anpassade händelser

Instrumentering koden är grunden för de flesta andra aspekter av övervaka dina tjänster. Instrumentation är det enda sättet du kan veta att något är fel, och diagnostisera vad som behöver åtgärdas. Även om det är tekniskt möjligt att ansluta en felsökare till en tjänst för produktion, men det är inte vanligt. Därför är har detaljerad instrumentationsdata viktigt.

Vissa produkter instrumentera automatiskt din kod. Även om dessa lösningar kan också fungera, krävs nästan alltid manuell instrumentation. Du måste ha tillräckligt med information för att felsöka kriminalteknikers programmet i slutet. Här beskrivs olika sätt att instrumentering koden och när du ska välja en metod över en annan.

## <a name="eventsource"></a>eventSource
När du skapar en Service Fabric-lösning från en mall i Visual Studio en **EventSource**-härledd klass (**ServiceEventSource** eller **ActorEventSource**) genereras. En mall skapas, där du kan lägga till händelser för programmet eller tjänsten. Den **EventSource** namn **måste** vara unikt och bör ändras från mallen standardsträngen mitt företag -&lt;lösning&gt;-&lt;projekt&gt;. Att flera **EventSource** definitioner som använder samma namn leder till problem vid körning. Varje definierad händelse måste ha en unik identifierare. Ett körningsfel inträffar om en identifierare som inte är unikt. Vissa organisationer preassign intervall med värden för identifierare för att undvika konflikter mellan separat utvecklingsgrupper. Mer information finns i [Vance's blog](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) eller [MSDN-dokumentationen](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

### <a name="using-structured-eventsource-events"></a>Med hjälp av strukturerade EventSource händelser

Var och en av händelser i kodexemplen i det här avsnittet har definierats för specialfall, till exempel när en service type har registrerats. När du definierar meddelanden av användningsfall, data kan paketeras med text till felet och mer kan du söka enkelt och filtrera baserat på namn eller värde för de angivna egenskaperna. Strukturera instrumentation utdata blir kräver det lättare att använda, men mer tankar och tid för att definiera en ny händelse för varje användningsfall. Vissa Händelsedefinitioner kan delas över hela programmet. Till exempel en metod starta eller stoppa skulle händelsen återanvändas i många tjänster i ett program. En domänspecifika-tjänst som ett order-system kan ha en **CreateOrder** händelsen, som har en egen unik händelse. Den här metoden kan generera många händelser och potentiellt kräver samordning av identifierare i projektgruppen. 

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
```

Med hjälp av en kombination av strukturerade och allmänna instrumentation kan fungerar också bra. Strukturerade instrumentation används för att rapportera fel och mått. Allmänna händelser kan användas för detaljerad loggning som används av tekniker för felsökning.

## <a name="aspnet-core-logging"></a>ASP.NET Core loggning

Det är viktigt att planera noggrant hur du kommer instrumentera din kod. Höger instrumentation planen kan hjälpa dig att undvika potentiell destabilizing din kodbas och sedan behöver reinstrument koden. För att minska risken kan du välja en instrumentation bibliotek som [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), vilket är en del av Microsoft ASP.NET Core. ASP.NET Core har en [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) gränssnitt som du kan använda med leverantören av ditt val när minimera effekten på befintlig kod. Du kan använda koden i ASP.NET Core på Windows och Linux och i fullständig .NET Framework, så instrumentation koden är standardiserad. Detta är ytterligare utforskade nedan:

### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Med hjälp av Microsoft.Extensions.Logging i Service Fabric

1. Lägg till Microsoft.Extensions.Logging NuGet-paketet i projektet som du vill betalningsinstrument. Lägg även till eventuella provider-paket (för ett paket från tredje part, se följande exempel). Mer information finns i [loggning i ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Lägg till en **med** direktiv för Microsoft.Extensions.Logging till service-fil.
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

## <a name="using-other-logging-providers"></a>Med hjälp av andra leverantörer av loggning

Vissa leverantörer används metoden som beskrivs i föregående avsnitt, inklusive [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/), och [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Du kan ansluta var och en av dessa i ASP.NET Core loggning eller du kan använda dem separat. Serilog har en funktion som förbättra alla meddelanden från en logg. Den här funktionen kan vara användbara för utgående namn, typ och partitionsinformation. Om du vill använda den här funktionen i ASP.NET grundläggande infrastruktur för att utföra de här stegen:

1. Lägg till Serilog och Serilog.Extensions.Logging Serilog.Sinks.Observable NuGet-paket i projektet. I nästa exempel också lägga till Serilog.Sinks.Literate. Bättre visas nedan.
2. I Serilog, skapar du en LoggerConfiguration och transaktionsloggfiler-instans.

  ```csharp
  Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
  ```

3. Lägg till ett Serilog.ILogger argument för konstruktorn för tjänsten, och nyskapade loggaren.

  ```csharp
  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
  ```

4. Lägg till följande kod, vilket skapar egenskapen enrichers för i konstruktorn service den **ServiceTypeName**, **ServiceName**, **PartitionId**, och **InstanceId** egenskaper för tjänsten. Det lägger till en egenskap enricher ASP.NET Core loggning fabriken, så att du kan använda Microsoft.Extensions.Logging.ILogger i koden.

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
  >Vi rekommenderar att du inte använder statiska Log.Logger med föregående exempel. Service Fabric kan vara värd för flera instanser av samma service-typen i en enda process. Om du använder statisk Log.Logger visas den senaste skrivaren av egenskapen enrichers värden för alla instanser som körs. Det här är ett skäl till varför _logger variabeln är en privat medlemsvariabel av tjänstklassen. Dessutom måste du _logger tillgänglig för vanliga kod som kan användas för tjänster.

## <a name="choosing-a-logging-provider"></a>Att välja en provider för loggning

Om programmet är beroende av hög prestanda, **EventSource** vanligtvis är en bra metod. **EventSource** *vanligtvis* använder färre resurser och presterar bättre än ASP.NET Core loggning eller någon av de tillgängliga lösningarna från tredje part.  Detta är inte ett problem för många tjänster, men om tjänsten är inriktade på prestanda, med hjälp av **EventSource** kan vara ett bättre alternativ. Emellertid att hämta dessa fördelarna med strukturerade loggning, **EventSource** kräver en större från din teknikteamet. Om möjligt gör en snabb prototyp av några alternativ för loggning och sedan välja den som bäst uppfyller dina behov.

## <a name="next-steps"></a>Nästa steg

När du har valt loggning-providern kan instrumentera dina program och tjänster, måste din loggar och händelser ska aggregeras innan de kan skickas till alla analysplattform. Läs mer om [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) och [BOMULLSTUSS](service-fabric-diagnostics-event-aggregation-wad.md) att bättre förstå några av de rekommenderade alternativ.
