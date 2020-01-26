---
title: Azure Service Bus slut punkt till slut punkt för spårning och diagnostik | Microsoft Docs
description: Översikt över Service Bus-klientautentisering och spårning från slut punkt till slut punkt (klient genom alla tjänster som ingår i bearbetningen.)
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: a184e76faa89199d3e13ece3e17f94f73d995a12
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760274"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Distribuerad spårning och korrelation genom Service Bus meddelanden

Ett av de vanliga problemen med att utveckla mikrotjänster är möjligheten att spåra åtgärder från en klient genom alla tjänster som ingår i bearbetningen. Det är användbart för fel sökning, prestanda analys, A/B-testning och andra typiska diagnos scenarier.
En del av det här problemet spårar logiska arbets delar. Det omfattar meddelande behandlings resultat och svars tider och externa beroende anrop. En annan del är en korrelation av dessa diagnostiska händelser utöver process gränser.

När en producent skickar ett meddelande via en kö, sker det vanligt vis i omfånget för en annan logisk åtgärd som initieras av en annan klient eller tjänst. Samma åtgärd fortsätter av konsumenten när den får ett meddelande. Både producent och konsument (och andra tjänster som bearbetar åtgärden) genererar sedan telemetri-händelser för att spåra åtgärds flödet och resultatet. För att kunna korrelera sådana händelser och spåra åtgärder från slut punkt till slut punkt måste varje tjänst som rapporterar telemetri stämpla varje händelse med en spårnings kontext.

Microsoft Azure Service Bus Messaging har definierat nytto Last egenskaper som producenter och konsumenter bör använda för att skicka sådan spårnings kontext.
Protokollet baseras på [http-korrelations protokollet](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Egenskapsnamn        | Beskrivning                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostik-ID       | Unikt ID för ett externt anrop från producent till kön. Se [begärande-ID i HTTP-protokollet](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) för att få rationella, överväganden och format |
|  Korrelations kontext | Åtgärds kontext, som sprids över alla tjänster som ingår i åtgärds bearbetning. Mer information finns i [korrelations kontext i HTTP-protokoll](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-auto-tracing"></a>Service Bus automatisk spårning i .NET-klient

Från och med version 3.0.0 [Microsoft Azure Service Bus-klienten för .net](/dotnet/api/microsoft.azure.servicebus.queueclient) får du spårnings instrument som kan kopplas till spårnings system, eller del av klient kod.
Med instrumentering kan du spåra alla anrop till tjänsten för Service Bus meddelande tjänsten från klient sidan. Om meddelande bearbetning görs med [meddelande hanterarens mönster](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), instrumenteras även meddelande bearbetning

### <a name="tracking-with-azure-application-insights"></a>Spåra med Azure Application insikter

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) ger omfattande prestanda övervaknings funktioner, inklusive automagic-begäran och beroende spårning.

Installera Application Insights SDK, beroende på din projekt typ:
- [ASP.net](../azure-monitor/app/asp-net.md) -installation version 2,5-beta2 eller högre
- [ASP.net Core](../azure-monitor/app/asp-net-core.md) -installera version 2.2.0-beta2 eller högre.
Dessa länkar innehåller information om hur du installerar SDK, skapar resurser och konfigurerar SDK (om det behövs). Information om non-ASP.NET-program finns i artikeln [Azure Application Insights för konsol program](../azure-monitor/app/console.md) .

Om du använder [meddelande hanteraren](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) för att bearbeta meddelanden gör du följande: alla Service Bus samtal som utförs av tjänsten spåras automatiskt och korreleras med andra telemetridata. I annat fall refererar vi till följande exempel för manuell meddelande bearbetnings spårning.

#### <a name="trace-message-processing"></a>Behandling av spåra meddelanden

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

I det här exemplet rapporteras `RequestTelemetry` för varje bearbetat meddelande, med en tidsstämpel, varaktighet och resultat (lyckades). Telemetri har också en uppsättning korrelations egenskaper.
Kapslade spår och undantag som rapporteras vid meddelande bearbetning stämplas också med korrelations egenskaper som representerar dem som underordnade till `RequestTelemetry`.

Om du gör anrop till stödda externa komponenter under meddelande bearbetningen spåras de också automatiskt och korreleras. Se [spåra anpassade åtgärder med Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md) för manuell spårning och korrelation.

### <a name="tracking-without-tracing-system"></a>Spåra utan spårnings system
Om spårnings systemet inte stöder automatisk Service Bus samtals spårning kan du behöva lägga till sådant stöd i ett spårnings system eller i ditt program. I det här avsnittet beskrivs diagnostiska händelser som skickas av Service Bus .NET-klienten.  

Service Bus .NET-klienten instrumenteras med hjälp av .NET tracing primitiver [system. Diagnostics. Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) och [system. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` fungerar som en spårnings kontext när `DiagnosticSource` är en meddelande funktion. 

Om det inte finns någon lyssnare för DiagnosticSource-händelserna, är instrumentering inaktiverat, och behåller inga instrument kostnader. DiagnosticSource ger all kontroll till lyssnaren:
- lyssnare styr vilka källor och händelser som ska bevakas
- lyssnare kontrollerar händelse frekvens och sampling
- händelser skickas med en nytto last som ger fullständig kontext så att du kan komma åt och ändra meddelande objekt under händelsen

Bekanta dig med [användar handboken för DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) innan du fortsätter med implementeringen.

Nu ska vi skapa en lyssnare för Service Bus händelser i ASP.NET Core app som skriver loggar med Microsoft. extension. Logga.
Den använder [system. Reactive. Core](https://www.nuget.org/packages/System.Reactive.Core) -biblioteket för att prenumerera på DiagnosticSource (det är också enkelt att prenumerera på DiagnosticSource utan IT)

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

I det här exemplet loggar Listener varaktighet, resultat, unik identifierare och start tid för varje Service Bus åtgärd.

#### <a name="events"></a>Events

För varje åtgärd skickas två händelser: "starta" och "stoppa". Förmodligen är du bara intresse rad av "stopp"-händelser. De ger resultatet av åtgärden, samt start tid och varaktighet som en aktivitets egenskaper.

Händelse nytto Last tillhandahåller en lyssnare med åtgärdens kontext, den replikerar API-inkommande parametrar och retur värde. Händelse nytto lasten "stoppa" har alla egenskaper för "starta" händelse nytto Last, så att du kan ignorera "Start"-händelsen fullständigt.

Alla händelser har även egenskaperna "entity" och "slut punkt", de utelämnas i tabellen nedan
  * `string Entity`--namnet på entiteten (kö, ämne osv.)
  * URL för `Uri Endpoint`-Service Bus-slutpunkt

Varje "stopp"-händelse har `Status` egenskap med `TaskStatus` asynkron åtgärd slutfördes med, som också utelämnas i följande tabell för enkelhetens skull.

Här är en fullständig lista över instrumenterade åtgärder:

| Åtgärds namn | Spårat API | Egenskaper för vissa nytto Last|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages`-lista över meddelanden som skickas |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` – meddelande som bearbetas<br/>`DateTimeOffset ScheduleEnqueueTimeUtc`-schemalagd meddelande förskjutning<br/>`long SequenceNumber` ordnings nummer för schemalagt meddelande ("stoppa" händelse nytto Last) |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` ordnings nummer för te-meddelande som ska avbrytas | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount`-det maximala antalet meddelanden som kan tas emot.<br/>`IList<Message> Messages` lista över mottagna meddelanden ("stoppa" händelse nytto Last) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber`-start punkten som du kan använda för att bläddra i en batch med meddelanden.<br/>`int RequestedMessageCount` – antalet meddelanden som ska hämtas.<br/>`IList<Message> Messages` lista över mottagna meddelanden ("stoppa" händelse nytto Last) |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` – den lista som innehåller de sekvensnummer som ska tas emot.<br/>`IList<Message> Messages` lista över mottagna meddelanden ("stoppa" händelse nytto Last) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` – den lista som innehåller de lock-token för motsvarande meddelanden som ska slutföras.|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` – lås-token för motsvarande meddelande att överge. |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken`-lås-token för motsvarande meddelande att skjuta upp. | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` – lås-token för motsvarande meddelande till obeställbara meddelanden. | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` – lås-token för motsvarande meddelande för att förnya låset.<br/>`DateTime LockedUntilUtc` – nytt utgångs datum och tid för att låsa token i UTC-format. ("Stoppa" händelse nytto Last)|
| Microsoft.Azure.ServiceBus.Process | Meddelande hanteraren lambda-funktion som finns i [IReceiverClient. RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message` – meddelande som bearbetas. |
| Microsoft.Azure.ServiceBus.ProcessSession | Lambda-funktionen för Message sessions handler finns i [IQueueClient. RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message` – meddelande som bearbetas.<br/>`IMessageSession Session`-sessionen bearbetas |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule`-regel beskrivningen som innehåller den regel som ska läggas till. |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName`-namnet på regeln som ska tas bort. |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient. GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules`-alla regler som är associerade med prenumerationen. (Stoppa endast nytto Last) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId`-sessionId finns i meddelandena. |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId`-sessionId finns i meddelandena.<br/>`byte [] State`-sessionstillstånd ("stoppa" händelse nytto Last) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId`-sessionId finns i meddelandena.<br/>`byte [] State`-sessionstillstånd |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId`-sessionId finns i meddelandena. |
| Microsoft.Azure.ServiceBus.Exception | alla instrumenterade API: er| `Exception Exception` – undantags instans |

I varje händelse kan du komma åt `Activity.Current` som innehåller aktuell åtgärds kontext.

#### <a name="logging-additional-properties"></a>Loggar ytterligare egenskaper

`Activity.Current` innehåller en detaljerad kontext för den aktuella åtgärden och dess överordnade. Mer information finns i [aktivitets dokumentationen](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) .
Service Bus Instrumentation ger ytterligare information i `Activity.Current.Tags` – de innehåller `MessageId` och `SessionId` när de är tillgängliga.

Aktiviteter som spårar Receive-, Peek-och ReceiveDeferred-händelser kan också ha `RelatedTo` tagg. Den innehåller en distinkt lista över `Diagnostic-Id`meddelanden som tagits emot som ett resultat.
Sådan åtgärd kan leda till att flera orelaterade meddelanden tas emot. Dessutom är `Diagnostic-Id` inte känt när åtgärden startar, så att Receive-åtgärder kan korreleras till process åtgärder med endast den här taggen. Det är användbart när du analyserar prestanda problem för att kontrol lera hur lång tid det tog att ta emot meddelandet.

Effektivt sätt att logga taggar är att iterera över dem, så att lägga till taggar i föregående exempel ser ut så här 

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

I vissa fall är det önskvärt att endast logga en del av händelserna för att minska prestanda och lagrings förbrukning. Du kan endast logga "stoppa"-händelser (som i föregående exempel) eller en procentuell sampling av händelserna. 
`DiagnosticSource` ger det möjlighet att uppnå det med `IsEnabled` predikat. Mer information finns i [Sammanhangs beroende filtrering i DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` kan anropas flera gånger för en enskild åtgärd för att minimera prestanda påverkan.

`IsEnabled` anropas i följande ordning:

1. `IsEnabled(<OperationName>, string entity, null)` till exempel `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Observera att det inte finns någon start eller stoppa i slutet. Använd den för att filtrera bort specifika åtgärder eller köer. Om motringning returnerar `false`skickas inte händelser för åtgärden

   * För åtgärderna "process" och "ProcessSession" får du också `IsEnabled(<OperationName>, string entity, Activity activity)` motringning. Använd den för att filtrera händelser baserat på `activity.Id`-eller Tags egenskaper.
  
2. `IsEnabled(<OperationName>.Start)` till exempel `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Kontrollerar om start händelsen ska utlösas. Resultatet påverkar endast händelsen "starta", men ytterligare Instrumentation är inte beroende av det.

Det finns inga `IsEnabled` för händelsen stopp.

Om åtgärds resultatet är ett undantag kallas `IsEnabled("Microsoft.Azure.ServiceBus.Exception")`. Du kan bara prenumerera på "undantags händelser" och förhindra resten av Instrumentation. I så fall måste du fortfarande hantera sådana undantag. Eftersom annan Instrumentation är inaktive rad bör du inte förvänta dig att spåra kontexten för att flöda med meddelanden från konsument till producent.

Du kan använda `IsEnabled` också implementera strategier för insamling. Sampling som baseras på `Activity.Id` eller `Activity.RootId` säkerställer konsekvent provtagning över alla däck (så länge som det sprids av spårnings systemet eller med din egen kod).

I närvaro av flera `DiagnosticSource` lyssnare för samma källa räcker det bara för en lyssnare för att godkänna händelsen, så `IsEnabled` är inte garanterat att anropas,

## <a name="next-steps"></a>Nästa steg

* [Application Insights korrelation](../azure-monitor/app/correlation.md)
* [Application Insights övervakar beroenden](../azure-monitor/app/asp-net-dependencies.md) för att se om rest, SQL eller andra externa resurser saktar ned dig.
* [Spåra anpassade åtgärder med Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md)
