---
title: "Azure Service Bus-slutpunkt till slutpunkt-spårning och diagnostik | Microsoft Docs"
description: "Översikt över Service Bus klientdiagnostik och spårning för slutpunkt till slutpunkt"
services: service-bus-messaging
documentationcenter: 
author: lmolkova
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova
ms.openlocfilehash: 847056acd2d97391782dcac1874a2739b7f5825c
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Distribuerade spårning och korrelation via Service Bus-meddelanden

Vanliga problem i mikrotjänster utveckling är möjligheten att spåra åtgärden från en klient via alla tjänster som ingår i bearbetningen. Det är användbart för felsökning prestandaanalys för A / B-testning och andra vanliga diagnostik-scenarier.
En del av det här problemet är att spåra logiska arbeten. Den innehåller resultatet och svarstid och externa beroendeanrop för meddelandebehandling. En annan del är korrelation av händelserna diagnostik utanför processgränser.

När en producent skickar ett meddelande via en kö, sker det vanligtvis i omfånget för en annan logisk åtgärd, initieras av vissa klienten eller tjänsten. Konsumenten fortsätter samma åtgärd när den tar emot ett meddelande. Både tillverkare och konsumenter (och andra tjänster som bearbetar åtgärden), förmodligen genererar telemetriska händelser för att spåra åtgärden flödet och resultat. För att korrelera sådana händelser och spåra åtgärden slutpunkt till slutpunkt, måste varje tjänst som rapporterar telemetri stämpel varje händelse med en trace-kontext.

Microsoft Azure Service Bus-meddelanden har definierats nyttolast egenskaper som producenter och konsumenter ska använda för att skicka sådana trace-kontexten.
Protokollet är baserat på den [korrelation HTTP-protokollet](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Egenskapsnamn        | Beskrivning                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostik-Id       | Unik identifierare för ett externt anrop från producenten till kön. Referera till [Request-Id i HTTP-protokollet](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) för motiveringen, överväganden och format |
|  Korrelations-kontexten | Åtgärden kontext som sprids över alla tjänster som ingår i åtgärden bearbetning. Mer information finns i [Korrelations-kontexten i HTTP-protokoll](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-auto-tracing"></a>Service Bus .NET klienten automatiskt-spårning

Från och med version 3.0.0 [Microsoft Azure ServiceBus-klient för .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) tillhandahåller spårning instrumentation punkter som kan ansluta genom spårning system eller typ av klientkod.
Instrumentationen kan spåra alla anrop till Service Bus meddelandetjänsten från klienten. Om meddelandet bearbetas med den [meddelandet hanterare mönster](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), meddelandebehandling också instrumenterats

### <a name="tracking-with-azure-application-insights"></a>Spårning med Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) ger omfattande funktioner inklusive automagical begäran och beroende spårning för prestandaövervakning.

Installera Application Insights SDK beroende på ditt projekttyp:
- [ASP.NET](../application-insights/app-insights-asp-net.md) version 2.5-beta2 eller högre
- [ASP.NET Core](../application-insights/app-insights-asp-net-core.md) version 2.2.0-beta2 eller högre.
Dessa länkar innehåller information om att installera SDK, skapa resurser och konfigurera SDK (vid behov). För icke-ASP.NET-program, se [Azure Application Insights för konsolprogram](../application-insights/application-insights-console.md) artikel.

Om du använder [meddelandet hanterare mönster](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) för att bearbeta meddelanden du är klar: alla Service Bus-anrop utfördes av tjänsten automatiskt spåras och samverkar med andra telemetri-objekt. Annars finns i följande exempel för manuell meddelandebehandling spårning.

#### <a name="trace-message-processing"></a>Spåra meddelandebehandling

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you are using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

I det här exemplet `RequestTelemetry` rapporteras för varje bearbetade meddelande med en tidsstämpel, varaktighet och resultatet (klart). Telemetrin har också en uppsättning egenskaper för korrelation.
Kapslade spårningar och undantag som rapporterats vid behandlingen av meddelandet också stämplad med korrelation egenskaper som representerar dem som underordnade om du till den `RequestTelemetry`.

Om du gör anrop till stöds externa komponenter vid behandlingen av meddelandet, men de är också automagically spåras och korrelerade. Referera till [spåra anpassade åtgärder med Application Insights SDK för .NET](../application-insights/application-insights-custom-operations-tracking.md) för manuell spårning och korrelation.

### <a name="tracking-without-tracing-system"></a>Spåra utan spårningssystemet
Om spårningssystemet inte stöder automagical Service Bus-anrop spårning söker du kanske till att lägga till stödet i ett spårningssystem eller i ditt program. Det här avsnittet beskrivs diagnostikhändelser skickas av Service Bus .NET-klienten.  

Service Bus .NET klienten är instrumenterats med hjälp av .NET spårning primitiver [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) och [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity`fungerar som en trace kontext när `DiagnosticSource` är en mekanism för meddelande. 

Om det finns ingen lyssnare för DiagnosticSource händelser, är instrumentation inaktiverat, som noll instrumentation kostnaderna hålls. DiagnosticSource ger alla kontroll till lyssnaren:
- lyssnare styr vilka datakällor och händelser för att lyssna på
- lyssnare händelsehastighet för kontroller och provtagning
- händelser skickas med en nyttolast som ger fullständig kontext så att du kan komma åt och ändra meddelandeobjektet under händelsen

Bekanta dig med [DiagnosticSource användarhandboken](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) innan du fortsätter med implementeringen.

Nu ska vi skapa en lyssnare för Service Bus-händelser i ASP.NET Core-app som skriver loggar med Microsoft.Extension.Logger.
Den använder [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) biblioteket för att prenumerera på DiagnosticSource (det är också lätt att prenumerera på DiagnosticSource utan den)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

I det här exemplet loggar lyssnare varaktighet, resultat, unik identifierare och starttiden för varje Service Bus-åtgärd.

#### <a name="events"></a>Händelser

För varje åtgärd två händelser skickas: 'Start' och 'Stop'. Du är mest förmodligen bara intresserad 'Stop-händelser. De ger resultatet av åtgärden, samt starttid och varaktighet som Aktivitetsegenskaper för en.

Händelsenyttolast innehåller en lyssnare med kontexten för åtgärden, den replikerar API inkommande parametrar och returnera värdet. 'Stop-händelsenyttolast har alla egenskaper av 'Start' händelsenyttolast så att du kan ignorera 'Start' händelse helt.

Alla händelser har också 'Entity' och 'slutpunkt-egenskaper, de har uteslutits i tabellen nedan
  * `string Entity`– Namnet på entiteten (kö, ämne, osv.)
  * `Uri Endpoint`-Service Bus slutpunkts-URL

Varje 'Stop-händelse har `Status` egenskap med `TaskStatus` asynkrona åtgärden slutfördes, som också tas bort i tabellen nedan för enkelhetens skull.

Här är en fullständig lista över instrumenterade åtgärder:

| Åtgärdsnamn | Spårade API | Egenskaper för specifika nyttolast|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | IList<Message> meddelanden - listan över meddelanden som skickas |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | Meddelandet meddelande - meddelandet som bearbetas<br/>DateTimeOffset ScheduleEnqueueTimeUtc - schemalagda meddelandet förskjutning<br/>lång SequenceNumber - sekvensnumret för schemalagda meddelande ('Stop-händelsenyttolast) |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | lång SequenceNumber - sekvensnumret för te meddelandet avbrytas | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) |int RequestedMessageCount - det maximala antalet meddelanden som kan tas emot.<br/>IList<Message> meddelanden - lista över mottagna meddelanden ('Stop-händelsenyttolast) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | int FromSequenceNumber - startpunkt som du vill bläddra i en grupp med meddelanden.<br/>int RequestedMessageCount - antal meddelanden som ska hämtas.<br/>IList<Message> meddelanden - lista över mottagna meddelanden ('Stop-händelsenyttolast) |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | IEnumerable<long> SequenceNumbers - listan som innehåller sekvensnummer att ta emot.<br/>IList<Message> meddelanden - lista över mottagna meddelanden ('Stop-händelsenyttolast) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | IList<string> LockTokens - listan som innehåller lock-token av motsvarande meddelanden ska slutföras.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | strängen LockToken - Lås token av motsvarande meddelande om du vill avbryta. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | strängen LockToken - Lås token av motsvarande meddelandet ska skjutas upp. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | strängen LockToken - lock-token för obeställbara motsvarande meddelandet. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | strängen LockToken - lock-token för den motsvarande meddelande om att förnya lås på.<br/>DateTime LockedUntilUtc - nya Lås token upphör att gälla datum och tid i UTC-format. ('Stop-händelsenyttolast)|
| Microsoft.Azure.ServiceBus.Process | Meddelandet hanterare lambda funktion i [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | Meddelandet meddelande - meddelandet som bearbetas. |
| Microsoft.Azure.ServiceBus.ProcessSession | Meddelandet Session hanterare lambda funktion i [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | Meddelandet meddelande - meddelandet som bearbetas.<br/>IMessageSession Session - Session som bearbetas |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | RuleDescription regel - regelbeskrivningen som innehåller regeln för att lägga till. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | sträng RuleName - namnet för regeln som ska tas bort. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | IEnumerable<RuleDescription> regler för alla regler som är associerade med prenumerationen. (Endast 'stop-nyttolasten) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | sträng SessionId - sessionId finns i meddelanden. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | sträng SessionId - sessionId finns i meddelanden.<br/>byte [] tillstånd - sessionstillstånd ('Stop-händelsenyttolast) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | sträng SessionId - sessionId finns i meddelanden.<br/>byte [] tillstånd - sessionens tillstånd |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | sträng SessionId - sessionId finns i meddelanden. |
| Microsoft.Azure.ServiceBus.Exception | någon instrumenterats API| Undantag undantag - undantag instans |

I varje händelse, kan du komma åt `Activity.Current` som innehåller den aktuella åtgärden kontexten.

#### <a name="logging-additional-properties"></a>Ytterligare egenskaper för loggning

`Activty.Current`ger detaljerad kontexten för den aktuella åtgärden och dess överordnade klasser. Mer information finns i [aktivitet dokumentationen](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) för mer information.
Service Bus instrumentation tillhandahåller ytterligare information i den `Activity.Current.Tags` -de håller `MessageId` och `SessionId` när de är tillgängliga.

Aktiviteter som spårar 'Får', 'Titt' och 'ReceiveDeferred' händelse kan också ha `RelatedTo` tagg. Den innehåller olika lista över `Diagnostic-Id`(s) för meddelanden som tagits emot som ett resultat.
Detta kan resultera i flera orelaterade meddelanden som ska tas emot. Dessutom den `Diagnostic-Id` är inte känt när åtgärden startar så 'Receive-åtgärder kan korreleras till ”Process” åtgärder med hjälp av den här taggen endast. Det är användbart när du analyserar prestandaproblem för att kontrollera hur lång tid det tog att ta emot meddelandet.

Effektivt sätt att logga taggar är att iterera över dem, så att lägga till taggar i föregående exempel ser ut som 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", "{tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtrering och provtagning

I vissa fall är det klokt att logga endast en del av händelserna som ska minska prestanda kostnader eller lagring. Du kan logga 'Stop-händelser endast (som i föregående exempel) eller exempel procent av händelser. 
`DiagnosticSource`Ange sättet att uppnå den med `IsEnabled` predikat. Mer information finns i [sammanhangsberoende filtrering i DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled`kan anropas flera gånger för en enda åtgärd att minimera inverkan på prestanda.

`IsEnabled`anropas i följande ordning:

1. `IsEnabled(<OperationName>, string entity, null)`till exempel `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Observera att det finns ingen 'Start' eller 'Avbryt' i slutet. Du kan använda den för att filtrera bort särskilt åtgärder eller köer. Om motringning returnerar `false`, skickas inte händelser för åtgärden

  * Åtgärderna 'Processen' och 'ProcessSession' om du även får `IsEnabled(<OperationName>, string entity, Activity activity)` återanrop. Filtrera händelser utifrån `activity.Id` eller taggar egenskaper.
  
2. `IsEnabled(<OperationName>.Start)`till exempel `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Kontrollerar om 'Start' händelse att utlösa. Resultatet påverkar endast 'Start' händelse, men ytterligare instrumentation är inte beroende av den.

Det finns ingen `IsEnabled` för 'Stop-händelse.

Om vissa resultat är undantag, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` anropas. Du kan bara prenumerera på ' undantagshändelser ' och förhindra att resten av instrumentationen. I det här fallet har du fortfarande hantera sådana undantag. Spåra kontext för att flöda med meddelanden från klienten till producenten förvänta inte eftersom andra instrumentation är inaktiverad.

Du kan använda `IsEnabled` även genomföra provtagning strategier. Sampling baserat på de `Activity.Id` eller `Activity.RootId` säkerställer konsekvent provtagning över alla kolla (så länge det sprids genom spårningssystemet eller egen kod).

I förekomsten av flera `DiagnosticSource` -lyssnare för samma källa det räcker för en lyssnare för att acceptera händelsen, så `IsEnabled` är inte säkert att anropas,

## <a name="next-steps"></a>Nästa steg

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Application Insights korrelation](../application-insights/application-insights-correlation.md)
* [Programberoenden insikter övervakaren](../application-insights/app-insights-asp-net-dependencies.md) att se om REST, SQL eller andra externa resurser saktar ner dig.
* [Spåra anpassade åtgärder med Application Insights SDK för .NET](../application-insights/application-insights-custom-operations-tracking.md)
