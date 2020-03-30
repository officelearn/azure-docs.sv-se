---
title: Azure Service Bus spårning och diagnostik från slut-till-slut | Microsoft-dokument
description: Översikt över Service Bus-klientdiagnostik och spårning från på sluten tid (klient via alla tjänster som ingår i bearbetningen.)
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
ms.openlocfilehash: 7c2efc9c736097873201505f280af5d47bed4847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294182"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Distribuerad spårning och korrelation via Service Bus-meddelanden

Ett av de vanligaste problemen i mikrotjänstutveckling är möjligheten att spåra drift från en klient genom alla tjänster som är involverade i bearbetningen. Det är användbart för felsökning, prestandaanalys, A/B-testning och andra typiska diagnostikscenarier.
En del av detta problem är att spåra logiska bitar av arbete. Den innehåller meddelandebearbetningsresultat och svarstid och externa beroendeanrop. En annan del är korrelationen mellan dessa diagnostikhändelser utanför processgränserna.

När en producent skickar ett meddelande via en kö sker det vanligtvis i omfattningen av någon annan logisk åtgärd, initierad av någon annan klient eller tjänst. Samma åtgärd fortsätter av konsumenten när den får ett meddelande. Både producent och konsument (och andra tjänster som behandlar transaktionen), förmodligen avger telemetri händelser för att spåra driftflödet och resultatet. För att korrelera sådana händelser och spåra operation end-to-end, varje tjänst som rapporterar telemetri måste stämpla varje händelse med en spårningskontext.

Microsoft Azure Service Bus messaging har definierat nyttolastegenskaper som producenter och konsumenter bör använda för att passera en sådan spårningskontext.
Protokollet baseras på [HTTP-korrelationsprotokollet](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

| Egenskapsnamn        | Beskrivning                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostik-Id       | Unik identifierare för ett externt anrop från producent till kön. Se [Request-Id i HTTP-protokollet](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) för motiv, överväganden och format |
|  Korrelation-kontext | Åtgärdskontext, som sprids över alla tjänster som ingår i bearbetningen av åtgärden. Mer information finns [i Korrelationskontext i HTTP-protokollet](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-autotracing"></a>Autotrahering av Service Bus .NET-klient

Från och med version 3.0.0 [Microsoft Azure ServiceBus Client för .NET](/dotnet/api/microsoft.azure.servicebus.queueclient) tillhandahåller spårningsinstrumenteringspunkter som kan anslutas genom att spåra system eller klientkod.
Instrumenteringen gör det möjligt att spåra alla samtal till servicebussmeddelandetjänsten från klientsidan. Om meddelandebehandlingen görs med [meddelandehanterarens mönster](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)är meddelandebearbetning också instrumenterad

### <a name="tracking-with-azure-application-insights"></a>Spårning med Azure Application Insights

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) tillhandahåller omfattande prestandaövervakningsfunktioner, inklusive automagiska begäranden och beroendespårning.

Beroende på projekttypen installerar du Application Insights SDK:
- [ASP.NET](../azure-monitor/app/asp-net.md) - installera version 2.5-beta2 eller senare
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - installera version 2.2.0-beta2 eller senare.
Dessa länkar innehåller information om hur du installerar SDK, skapar resurser och konfigurerar SDK (om det behövs). Information om non-ASP.NET program finns i artikeln [Azure Application Insights for Console Applications.](../azure-monitor/app/console.md)

Om du använder [meddelandehanterarens mönster](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) för att bearbeta meddelanden är du klar: alla Service Bus-samtal som utförs av din tjänst spåras automatiskt och korreleras med andra telemetriartiklar. I annat fall finns följande exempel för manuell spårning av meddelandebearbetning.

#### <a name="trace-message-processing"></a>Spåra meddelandebearbetning

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

I det `RequestTelemetry` här exemplet rapporteras för varje bearbetat meddelande med en tidsstämpel, varaktighet och resultat (framgång). Telemetrin har också en uppsättning korrelationsegenskaper.
Kapslade spår och undantag som rapporteras under meddelandebearbetning stämplas också med `RequestTelemetry`korrelationsegenskaper som representerar dem som "underordnade" av .

Om du ringer samtal till externa komponenter som stöds under meddelandebearbetning spåras och korreleras de också automatiskt. Se [Spåra anpassade åtgärder med Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md) för manuell spårning och korrelation.

Om du kör någon extern kod utöver SDK för Programinsikter förväntar du dig att se längre **varaktighet** när du visar Application Insights-loggar. 

![Längre varaktighet i application insights-loggen](./media/service-bus-end-to-end-tracing/longer-duration.png)

Det betyder inte att det fanns en fördröjning i att ta emot meddelandet. I det här fallet har meddelandet redan tagits emot sedan meddelandet skickas in som en parameter till SDK-koden. Och **namntaggen** i App Insights-loggarna (**Process**) anger att meddelandet nu bearbetas av din externa händelsebearbetningskod. Det här problemet är inte Azure-relaterat. I stället refererar dessa mått till effektiviteten i din externa kod med tanke på att meddelandet redan har tagits emot från Service Bus. Se [den här filen på GitHub](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) för att se var **processtaggen** genereras och tilldelas när meddelandet har tagits emot från Service Bus. 

### <a name="tracking-without-tracing-system"></a>Spårning utan spårningssystem
Om ditt spårningssystem inte stöder automatisk Service Bus-samtalsspårning kanske du funderar på att lägga till sådant stöd i ett spårningssystem eller i ditt program. I det här avsnittet beskrivs diagnostikhändelser som skickas av Service Bus .NET-klient.  

Service Bus .NET-klienten är instrumenterad med .NET-spårningsprimitiver [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) och [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity`fungerar som ett spårningskontext medan `DiagnosticSource` det är en anmälningsmekanism. 

Om det inte finns någon lyssnare för DiagnosticSource-händelserna är instrumenteringen avstängd och behåller noll instrumenteringskostnader. DiagnosticSource ger all kontroll till lyssnaren:
- lyssnaren styr vilka källor och händelser som ska lyssnas på
- lyssnaren kontrollerar händelsefrekvens och sampling
- händelser skickas med en nyttolast som ger fullständig kontext så att du kan komma åt och ändra meddelandeobjekt under händelsen

Bekanta dig med [Användarhandboken](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) för DiagnosticSource innan du fortsätter med implementeringen.

Nu ska vi skapa en lyssnare för Service Bus-händelser i ASP.NET Core-appen som skriver loggar med Microsoft.Extension.Logger.
Den använder [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) bibliotek för att prenumerera på DiagnosticSource (det är också lätt att prenumerera på DiagnosticSource utan det)

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

I det här exemplet loggar lyssnaren varaktighet, resultat, unik identifierare och starttid för varje Service Bus-åtgärd.

#### <a name="events"></a>Händelser

För varje operation skickas två händelser: "Start" och "Stopp". Troligtvis är du bara intresserad av "Stop" händelser. De ger resultatet av åtgärden, samt starttid och varaktighet som aktivitetsegenskaper.

Händelse nyttolast ger en lyssnare med kontexten för åtgärden, replikerar API inkommande parametrar och returvärde. Stop-händelsenyttolasten har alla egenskaper för starthändelsens nyttolast, så du kan ignorera "Start"-händelsen helt.

Alla händelser har också egenskaper för entitet och slutpunkt, de utelämnas i tabellen nedan
  * `string Entity`- - Namn på entiteten (kö, ämne, etc.)
  * `Uri Endpoint`- Url till servicebussslutpunkt

Varje "Stop"-händelse `Status` `TaskStatus` har egenskap med async-åtgärd slutfördes med, som också utelämnas i följande tabell för enkelhetens skull.

Här är den fullständiga listan över instrumenterade operationer:

| Namn på operation | Spårat API | Specifika nyttolastegenskaper|
|----------------|-------------|---------|
| Microsoft.Azure.serviceBus.send | [MessageSender.sendasync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages`- Lista över meddelanden som skickas |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message`- Meddelande som bearbetas<br/>`DateTimeOffset ScheduleEnqueueTimeUtc`- Schemalagd meddelandeförskjutning<br/>`long SequenceNumber`- Sekvensnummer för schemalagt meddelande ("Stopp" händelse nyttolast) |
| Microsoft.Azure.servicebus.avbryt | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber`- Sekvensnummer för te meddelande som ska avbrytas | 
| Microsoft.Azure.servicebus.receive | [MessageReceiver.receiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount`- Det maximala antalet meddelanden som kan tas emot.<br/>`IList<Message> Messages`- Lista över mottagna meddelanden ("Stop" händelse nyttolast) |
| Microsoft.Azure.servicebus.peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber`- Utgångspunkten för att bläddra i en bunt meddelanden.<br/>`int RequestedMessageCount`- Antalet meddelanden att hämta.<br/>`IList<Message> Messages`- Lista över mottagna meddelanden ("Stop" händelse nyttolast) |
| Microsoft.Azure.servicebus.receiveDeferred | [MessageReceiver.receiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers`- Listan som innehåller de sekvensnummer som ska ta emot.<br/>`IList<Message> Messages`- Lista över mottagna meddelanden ("Stop" händelse nyttolast) |
| Microsoft.Azure.servicebus.complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens`- Listan som innehåller låstoken för motsvarande meddelanden som ska slutföras.|
| Microsoft.Azure.servicebus.abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken`- Låstoken för motsvarande meddelande att överge. |
| Microsoft.Azure.serviceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken`- Låstoken för motsvarande meddelande att skjuta upp. | 
| Microsoft.Azure.servicebus.deadletter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken`- Låstoken för motsvarande meddelande till död bokstav. | 
| Microsoft.Azure.serviceBus.renewLock | [MessageReceiver.renewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken`- Låstoken för motsvarande meddelande för att förnya låset på.<br/>`DateTime LockedUntilUtc`- Nytt låstokens utgångsdatum och -tid i UTC-format. ('Stop' händelse nyttolast)|
| Microsoft.Azure.servicebus.process | Meddelandehanterare lambda funktion som tillhandahålls i [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message`- Meddelande som behandlas. |
| Microsoft.Azure.servicebus.processsession | Funktionen Message Session Handler lambda som finns i [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message`- Meddelande som behandlas.<br/>`IMessageSession Session`- Sessionen som behandlas |
| Microsoft.Azure.serviceBus.AddRule | [PrenumerationClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule`- Regelbeskrivningen som ger regeln att lägga till. |
| Microsoft.Azure.serviceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName`- Namnet på regeln att ta bort. |
| Microsoft.Azure.serviceBus.getRules | [PrenumerationClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules`- Alla regler som är associerade med prenumerationen. (endast stoppnyttolast) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId`- Sessionen Som finns i meddelandena. |
| Microsoft.Azure.serviceBus.getsessionstate | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId`- Sessionen Som finns i meddelandena.<br/>`byte [] State`- Sessionstillstånd ("Stopp" händelse nyttolast) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId`- Sessionen Som finns i meddelandena.<br/>`byte [] State`- Sessionstillstånd |
| Microsoft.Azure.serviceBus.renewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId`- Sessionen Som finns i meddelandena. |
| Undantaget microsoft.Azure.servicebus.exception | alla instrumenterade API| `Exception Exception`- Undantagsinstans |

I varje händelse kan `Activity.Current` du komma åt den aktuella operationskontexten.

#### <a name="logging-additional-properties"></a>Logga ytterligare egenskaper

`Activity.Current`ger detaljerad kontext för den aktuella driften och dess föräldrar. Mer information finns i [Aktivitetsdokumentation](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) för mer information.
Service Bus instrumentering ger `Activity.Current.Tags` ytterligare `MessageId` information `SessionId` i - de håller och när de är tillgängliga.

Aktiviteter som spårar händelsen Ta emot, "Peek" och "ReceiveDeferred" kan också ha `RelatedTo` taggen. Den innehåller en `Diagnostic-Id`eller flera olika lista över meddelanden som tagits emot som ett resultat.
En sådan åtgärd kan resultera i flera icke-relaterade meddelanden som ska tas emot. Dessutom är `Diagnostic-Id` det inte känt när åtgärden startar, så "Ta emot"-åtgärder kan korreleras till processåtgärder som endast använder den här taggen. Det är användbart när du analyserar prestandaproblem för att kontrollera hur lång tid det tog att ta emot meddelandet.

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

I vissa fall är det önskvärt att bara logga en del av händelserna för att minska prestandakostnader eller lagringsförbrukning. Du kan bara logga stopphändelser (som i föregående exempel) eller exempelprocent av händelserna. 
`DiagnosticSource`sätt att uppnå `IsEnabled` det med predikat. Mer information finns [i Kontextbaserad filtrering i DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled`kan anropas flera gånger för en enda åtgärd för att minimera prestandapåverkan.

`IsEnabled`kallas i följande sekvens:

1. `IsEnabled(<OperationName>, string entity, null)`till exempel `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`. Observera att det inte finns någon "Start" eller "Stopp" i slutet. Använd den för att filtrera bort vissa åtgärder eller köer. Om motringningsreturer `false`skickas inte händelser för åtgärden

   * För åtgärderna Process och "ProcessSession" får `IsEnabled(<OperationName>, string entity, Activity activity)` du också motringning. Använd den för att `activity.Id` filtrera händelser baserat på eller taggar egenskaper.
  
2. `IsEnabled(<OperationName>.Start)`till exempel `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`. Kontrollerar om "Start"-händelsen ska avfyras. Resultatet påverkar bara "Start"-händelsen, men ytterligare instrumentering beror inte på den.

Det finns `IsEnabled` inget för "Stop"-händelsen.

Om något åtgärdsresultat `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` är undantag anropas det. Du kunde bara prenumerera på "Undantag" händelser och förhindra resten av instrumenteringen. I det här fallet måste du fortfarande hantera sådana undantag. Eftersom andra instrument är inaktiverat bör du inte förvänta dig att spårningskontexten flödar med meddelandena från konsument till producent.

Du kan `IsEnabled` också använda också implementera provtagningsstrategier. Provtagning baserad `Activity.Id` på `Activity.RootId` eller säkerställer konsekvent provtagning över alla däck (så länge det sprids genom att spåra systemet eller med din egen kod).

I närvaro `DiagnosticSource` av flera lyssnare för samma källa, det räcker för bara `IsEnabled` en lyssnare att acceptera händelsen, så är inte garanterat att kallas,

## <a name="next-steps"></a>Nästa steg

* [Korrelation för programinsikter](../azure-monitor/app/correlation.md)
* [Application Insights Övervaka beroenden](../azure-monitor/app/asp-net-dependencies.md) för att se om REST, SQL eller andra externa resurser saktar ner dig.
* [Spåra anpassade åtgärder med Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md)
