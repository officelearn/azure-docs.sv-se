---
title: Azure Service Bus-slutpunkt till slutpunkt-spårning och diagnostik | Microsoft Docs
description: Översikt över Service Bus klientdiagnostik och slutpunkt till slutpunkt-spårning
services: service-bus-messaging
documentationcenter: ''
author: lmolkova
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: lmolkova
ms.openlocfilehash: 5489fa999f3427345c3ee9f07f904296de224e31
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42061100"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Distribuerad spårning och korrelation via Service Bus-meddelanden

En av de vanligaste problemen i utveckling av mikrotjänster är möjligheten att spåra åtgärd från en klient via alla tjänster som är inblandade i bearbetningen. Det är användbart för felsökning, prestandaanalys, A / B-testning och andra vanliga diagnostik-scenarier.
En del av det här problemet för att spåra logiska arbeten. Den innehåller meddelandebehandling resultat och svarstider och externa beroendeanrop. En annan del är korrelation av dessa diagnostikhändelser utanför processgränser.

När en producent skickar ett meddelande i en kö, sker det vanligtvis i omfånget för en annan logisk åtgärd, initieras av några andra klienten eller tjänst. På samma gång är fortsatt av konsument när den får ett meddelande. Både producent och konsument (och andra tjänster som bearbetar åtgärden) antas vara generera telemetrihändelser för att spåra flödet för åtgärden och resultatet. För att korrelera sådana händelser och spår åtgärden slutpunkt till slutpunkt, måste varje tjänst som rapporterar telemetri stämpel varje händelse med en trace-kontext.

Microsoft Azure Service Bus-meddelanden har definierat nyttolast-egenskaper som producenter och konsumenter som ska använda för att skicka sådana spår kontext.
Protokollet som baseras på den [korrelation av HTTP-protokollet](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Egenskapsnamn        | Beskrivning                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostik-Id       | Unik identifierare för ett externt anrop från producent till kön. Referera till [Request-Id i HTTP-protokollet](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) för sker, överväganden och format |
|  Korrelations-kontexten | Åtgärden kontext som sprids över alla tjänster som ingår i åtgärden utförs. Mer information finns i [Korrelations-kontexten i HTTP-protokollet](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-auto-tracing"></a>Service Bus .NET-klient automatiskt-spårning

Från och med version 3.0.0 [Microsoft Azure ServiceBus-klienten för .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) tillhandahåller spårning instrumentationspunkter som kan vara låst av spårning system eller typ av klientkod.
Instrumentationen kan spåra alla anrop till Service Bus messaging-tjänsten från klientsidan. Om meddelandet bearbetningen är klar med den [hanteraren meddelandemönstret](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), meddelandebehandling också har instrumenterats

### <a name="tracking-with-azure-application-insights"></a>Spåra med Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) tillhandahåller omfattande funktioner, inklusive automagical begäran och beroendespårning för prestandaövervakning.

Beroende på din projekttyp installerar du Application Insights SDK:
- [ASP.NET](../application-insights/app-insights-asp-net.md) – installera version 2.5-beta2 eller högre
- [ASP.NET Core](../application-insights/app-insights-asp-net-core.md) – installera version 2.2.0-beta2 eller högre.
Dessa länkar innehåller detaljerad information om att installera SDK: N, skapa resurser och konfigurera SDK (vid behov). Icke-ASP.NET-program finns i [Azure Application Insights för konsolprogram](../application-insights/application-insights-console.md) artikeln.

Om du använder [hanteraren meddelandemönstret](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) för att bearbeta meddelanden du är klar: alla Service Bus-anrop som görs av din tjänst spåras automatiskt och med andra telemetri-objekt. Annars finns i följande exempel för manuell meddelandebehandling spårning.

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

I det här exemplet `RequestTelemetry` rapporteras för varje bearbetade meddelande med en tidsstämpel, varaktighet och resultatet (lyckades). Telemetri har också en uppsättning korrelation egenskaper.
Kapslade spårningar och undantag som har rapporterat under meddelandebehandling också stämplad med Korrelations-egenskaper som representerar dem som underordnade om du till den `RequestTelemetry`.

Om du gör anrop till externa komponenter som stöds under behandlingen av meddelandet, men de är också automagically spåras och korrelerade. Referera till [spåra anpassade åtgärder med Application Insights SDK för .NET](../application-insights/application-insights-custom-operations-tracking.md) för manuell spårning och korrelation.

### <a name="tracking-without-tracing-system"></a>Spåra utan spårningssystemet
Om spårning av systemet inte har stöd för spårning automagical Service Bus-anrop kan till exempel se till att lägga till stödet till ett spårningssystem eller till ditt program. Det här avsnittet beskrivs diagnostikhändelser som skickas av Service Bus .NET-klient.  

Service Bus .NET-klient är utrustade med hjälp av .NET spårning primitiver [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) och [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` fungerar som en spårning kontext när `DiagnosticSource` är ett sätt. 

Om det finns ingen lyssnare för DiagnosticSource-händelser, är instrumentation inaktiverat, håller noll instrumentation kostnaderna. DiagnosticSource ger alla kontroll till lyssnaren:
- lyssnaren styr vilka datakällor och händelser för att lyssna på
- takten för lyssnare kontroller och sampling
- händelser skickas med en nyttolast som tillhandahåller fullständigt sammanhang så att du kan komma åt och ändra meddelandeobjekt under händelsen

Bekanta dig med [DiagnosticSource användarhandboken](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) innan du fortsätter med implementering.

Nu ska vi skapa en lyssnare för Service Bus-händelser i ASP.NET Core-app som skriver loggar med Microsoft.Extension.Logger.
Den använder [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) -biblioteket för att prenumerera på DiagnosticSource (det är också enkelt att prenumerera på DiagnosticSource utan det)

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

I det här exemplet loggar lyssnare varaktighet, resultatet, unik identifierare och starttid för varje Service Bus-åtgärd.

#### <a name="events"></a>Händelser

För varje åtgärd, två händelser skickas: ”Start” och ”stoppa”. Du är mest förmodligen bara intresserad ”stoppa” händelser. De ger resultatet av åtgärden, samt starttid och varaktighet som Aktivitetsegenskaper för en.

Händelsenyttolast innehåller en lyssnare med kontexten för åtgärden, den replikerar inkommande parametrar för API: et och returnerar värdet. ”Stoppa” händelsenyttolast har alla egenskaper för ”Start” händelsenyttolast så att du kan ignorera ”Start” händelse helt.

Alla händelser har också 'Entity' och 'Slutpunkt' egenskaper, de används i tabellen nedan
  * `string Entity` – Namnet på entiteten (kön, ämnet, osv.)
  * `Uri Endpoint` -Service Bus slutpunkts-URL

Varje händelse som ”stoppa” har `Status` egenskap med `TaskStatus` async-åtgärden slutfördes med, som också tas bort i tabellen nedan för enkelhetens skull.

Här är en fullständig lista över instrumenterade åtgärder:

| Åtgärdsnamn | Spårade API | Egenskaper för specifika nyttolast|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | IList<Message> meddelanden - lista med meddelanden som skickas |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | Meddelandet meddelandet - meddelande som bearbetas<br/>DateTimeOffset ScheduleEnqueueTimeUtc - schemalagt meddelande förskjutning<br/>lång SequenceNumber - sekvensnumret för schemalagt meddelande (”stoppa” händelsenyttolast) |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | lång SequenceNumber - meddelandesekvensnummer te så avbryts | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) |int RequestedMessageCount – det maximala antalet meddelanden som kan tas emot.<br/>IList<Message> meddelanden - lista över mottagna meddelanden (”stoppa” händelsenyttolast) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | int FromSequenceNumber - startpunkt som att bläddra i en grupp med meddelanden.<br/>int RequestedMessageCount - antalet meddelanden som ska hämtas.<br/>IList<Message> meddelanden - lista över mottagna meddelanden (”stoppa” händelsenyttolast) |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | IEnumerable<long> SequenceNumbers - listan som innehåller sekvensnummer att ta emot.<br/>IList<Message> meddelanden - lista över mottagna meddelanden (”stoppa” händelsenyttolast) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | IList<string> LockTokens - listan som innehåller Lås token motsvarande meddelanden att slutföra.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | sträng LockToken - låstoken för meddelandet att lämna motsvarande. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | sträng LockToken - låstoken för motsvarande meddelandet som ska skjutas upp. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | sträng LockToken - låstoken för motsvarande meddelandet att obeställbara meddelanden. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | sträng LockToken - låstoken för motsvarande meddelandet att förnya låset.<br/>DateTime LockedUntilUtc - nya Lås token förfallodatum och tid i UTC-format. (”Stoppa” händelsenyttolast)|
| Microsoft.Azure.ServiceBus.Process | Meddelandet hanteraren lambda funktion i [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | Meddelandet meddelande - meddelande som bearbetas. |
| Microsoft.Azure.ServiceBus.ProcessSession | Meddelandet Session hanteraren lambda funktion i [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | Meddelandet meddelande - meddelande som bearbetas.<br/>IMessageSession Session - Session som bearbetas |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | RuleDescription regel - regelbeskrivningen som innehåller regeln för att lägga till. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | sträng Regelnamn – namnet på regeln för att ta bort. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | IEnumerable<RuleDescription> regler för alla regler som är associerade med prenumerationen. (Endast ”stoppa” nyttolast) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | sträng sessions-ID - sessionId finns i meddelandena. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | sträng sessions-ID - sessionId finns i meddelandena.<br/>byte [] tillstånd - sessionens tillstånd (”stoppa” händelsenyttolast) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | sträng sessions-ID - sessionId finns i meddelandena.<br/>byte [] tillstånd - sessionstillstånd |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | sträng sessions-ID - sessionId finns i meddelandena. |
| Microsoft.Azure.ServiceBus.Exception | instrumenterade API: er| Undantag undantag - instans för undantag |

I varje händelse kan du komma åt `Activity.Current` som innehåller den aktuella åtgärdskontexten.

#### <a name="logging-additional-properties"></a>Ytterligare egenskaper för loggning

`Activty.Current` tillhandahåller detaljerad kontexten för den aktuella åtgärden och dess överordnade klasser. Mer information finns i [aktivitet dokumentation](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) för mer information.
Service Bus instrumentation tillhandahåller ytterligare information i den `Activity.Current.Tags` – de har `MessageId` och `SessionId` när de är tillgängliga.

Aktiviteter som spårar 'Får', 'Peek' och 'ReceiveDeferred' händelse kan också ha `RelatedTo` tagg. Den innehåller distinkta lista över `Diagnostic-Id`(s) av meddelandena som tagits emot som ett resultat.
Detta kan resultera i flera orelaterade meddelanden som ska tas emot. Dessutom den `Diagnostic-Id` är inte känd när åtgärden startar så 'Receive-åtgärder kan korreleras till 'Process' åtgärder med hjälp av den här taggen endast. Det är användbart när du analyserar prestandaproblem för att kontrollera hur lång tid det tog att ta emot meddelandet.

Effektiva sättet att logga taggar är att iterera över dem, så att lägga till taggar i föregående exempel ser ut som 

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

#### <a name="filtering-and-sampling"></a>Filtrering och sampling

I vissa fall är det klokt att logga endast en del av händelserna om du vill minska prestanda omkostnader eller lagring. Du kan logga ”stoppa” händelser endast (som i föregående exempel) eller exempel procentandel av händelserna. 
`DiagnosticSource` sätt att uppnå den med `IsEnabled` predikat. Mer information finns i [kontext-baserad filtrering i DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` kan anropas flera gånger för en enda åtgärd att minimera prestandainverkan.

`IsEnabled` kallas i följande ordning:

1. `IsEnabled(<OperationName>, string entity, null)` till exempel `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Observera att det finns ingen ”Start” eller ”sluta” i slutet. Du kan använda den för att filtrera bort vissa åtgärder eller köer. Om motringning returnerar `false`, skickas inte händelser för åtgärden

  * För de 'Process' och 'ProcessSession', du får också `IsEnabled(<OperationName>, string entity, Activity activity)` återanrop. Använda den för att filtrera händelser utifrån `activity.Id` eller taggar egenskaper.
  
2. `IsEnabled(<OperationName>.Start)` till exempel `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Kontrollerar om ”Start” händelse bör vara aktiverade. Resultatet påverkar endast ”Start” händelse, men ytterligare instrumentation är inte beroende av den.

Det finns inga `IsEnabled` för ”stoppa” händelse.

Om vissa Åtgärdsresultat är undantag, `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` anropas. Du kan bara prenumerera på händelser för ”undantag” och förhindra att resten av instrumentationen. I det här fallet har du fortfarande hantera sådana undantag. Eftersom andra instrumentation har inaktiverats kan förvänta dig inte spårningen kontext till flöde med meddelanden från konsumenten till producenten.

Du kan använda `IsEnabled` även implementera sampling strategier. Sampling baserat på den `Activity.Id` eller `Activity.RootId` säkerställer konsekvent sampling över alla däck (förutsatt att det sprids genom spårningssystemet eller genom din egen kod).

I förekomsten av flera `DiagnosticSource` lyssnare för samma källa, det är bara en lyssnare att acceptera händelsen, så `IsEnabled` är inte säkert att anropa

## <a name="next-steps"></a>Nästa steg

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Application Insights korrelation](../application-insights/application-insights-correlation.md)
* [Application Insights övervaka beroenden](../application-insights/app-insights-asp-net-dependencies.md) att se om REST, SQL eller andra externa resurser gör systemet långsammare.
* [Spåra anpassade åtgärder med Application Insights SDK för .NET](../application-insights/application-insights-custom-operations-tracking.md)
