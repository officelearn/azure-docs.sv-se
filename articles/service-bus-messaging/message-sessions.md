---
title: Azure Service Bus-meddelandesessioner | Microsoft-dokument
description: I den här artikeln beskrivs hur du använder sessioner för att möjliggöra gemensam och ordnad hantering av obundna sekvenser av relaterade meddelanden.
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
ms.openlocfilehash: 1e22641e9d4f9959c26cd2043ea2acd7e260e0f0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314045"
---
# <a name="message-sessions"></a>Meddelandesessioner
Microsoft Azure Service Bus-sessioner möjliggör gemensam och ordnad hantering av obegränsade sekvenser av relaterade meddelanden. Sessioner kan användas i först in, först ut (FIFO) och mönster för begäran-svar. Den här artikeln visar hur du använder sessioner för att implementera dessa mönster när du använder Service Bus. 

## <a name="first-in-first-out-fifo-pattern"></a>Först in, först ut (FIFO) mönster
Använd sessioner för att realisera en FIFO-garanti i Service Bus. Service Bus är inte föreskrivande om vilken typ av relation mellan meddelandena, och definierar inte heller en viss modell för att avgöra var en meddelandesekvens startar eller slutar.

> [!NOTE]
> Den grundläggande nivån på Service Bus stöder inte sessioner. Standard- och premiumnivåerna stöder sessioner. Skillnader mellan dessa nivåer finns i [Service Bus-priser](https://azure.microsoft.com/pricing/details/service-bus/).

Alla avsändare kan skapa en session när de skickar meddelanden till ett ämne eller en kö genom att ange egenskapen [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) till någon programdefinierad identifierare som är unik för sessionen. På protokollnivån AMQP 1.0 mappas det här värdet till egenskapen *group-id.*

På sessionsmedvetna köer eller prenumerationer uppstår sessioner när det finns minst ett meddelande med sessionens [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId). När en session finns finns det ingen definierad tid eller API för när sessionen upphör eller försvinner. Teoretiskt kan ett meddelande tas emot för en session i dag, nästa meddelande om ett år, och om **SessionId** matchar är sessionen densamma ur servicebussperspektivet.

Vanligtvis har dock ett program en tydlig uppfattning om var en uppsättning relaterade meddelanden börjar och slutar. Service Bus anger inga specifika regler.

Ett exempel på hur du beskriver en sekvens för överföring av en fil är att ange egenskapen **Label** för det första meddelandet som **ska startas,** för mellanliggande meddelanden till **innehåll**och för det sista meddelandet att **sluta**. Den relativa positionen för innehållsmeddelandena kan beräknas som det aktuella meddelandet *SequenceNumber* delta från **startmeddelandet** *SequenceNumber*.

Sessionsfunktionen i Service Bus möjliggör en specifik mottagningsåtgärd i form av [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) i C# och Java API:er. Du aktiverar funktionen genom att ange egenskapen [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) i kön eller prenumerationen via Azure Resource Manager eller genom att ange flaggan i portalen. Det krävs innan du försöker använda relaterade API-åtgärder.

Ange flaggan med följande kryss i portalen:

![][2]

> [!NOTE]
> När Sessioner är aktiverade i en kö eller en prenumeration kan klientprogrammen ***inte längre*** skicka/ta emot vanliga meddelanden. Alla meddelanden måste skickas som en del av en session (genom att ange sessions-ID) och tas emot genom att ta emot sessionen.

API:erna för sessioner finns i kö- och prenumerationsklienter. Det finns en tvingande modell som styr när sessioner och meddelanden tas emot, och en hanterare-baserad modell, liknande *OnMessage*, som döljer komplexiteten i att hantera mottagningsloopen.

### <a name="session-features"></a>Sessionsfunktioner

Sessioner ger samtidig av multiplexering av interfolierade meddelandeströmmar samtidigt som den ordnade och garanterar beställd leverans.

![][1]

En [MessageSession-mottagare](/dotnet/api/microsoft.servicebus.messaging.messagesession) skapas av klienten som accepterar en session. Klienten anropar [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) eller [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) i C#. I den reaktiva motringningsmodellen registreras en sessionshanterare.

När [MessageSession-objektet](/dotnet/api/microsoft.servicebus.messaging.messagesession) accepteras och medan det innehas av en klient har klienten ett exklusivt lås på alla meddelanden med sessionens [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) som finns i kön eller prenumerationen, och även på alla meddelanden med **den SessionId** som fortfarande anländer medan sessionen hålls.

Låset frigörs när **Close** eller **CloseAsync** anropas, eller när låset upphör att gälla i fall då programmet inte kan utföra stängningsåtgärden. Sessionslåset ska behandlas som ett exklusivt lås på en fil, vilket innebär att programmet ska stänga sessionen så snart det inte längre behöver det och/eller inte förväntar sig några ytterligare meddelanden.

När flera samtidiga mottagare hämtar från kön skickas meddelandena som tillhör en viss session till den specifika mottagare som för närvarande innehåller låset för den sessionen. Med den åtgärden, en interfolierad meddelandeström i en kö eller prenumeration är rent de-multiplexed till olika mottagare och dessa mottagare kan också leva på olika klientmaskiner, eftersom låshanteringen händer service-side, inuti Service Bus.

Den föregående bilden visar tre samtidiga sessionsmottagare. En session `SessionId` med = 4 har ingen aktiv, ägande klient, vilket innebär att inga meddelanden levereras från den här specifika sessionen. En session fungerar på många sätt som en underkö.

Sessionslåset som hålls av sessionsmottagaren är ett paraply för de meddelandelås som används av peek-lock-kvittningsläget. *peek-lock* En mottagare kan inte ha två meddelanden samtidigt "under flygning", men meddelandena måste bearbetas i ordning. Ett nytt meddelande kan endast erhållas när föregående meddelande har slutförts eller skrivs av obeskrivet. Om du överger ett meddelande visas samma meddelande igen med nästa mottagningsåtgärd.

### <a name="message-session-state"></a>Meddelandesessionstillstånd

När arbetsflöden bearbetas i molnsystem med hög skala och hög tillgänglighet måste arbetsflödeshanteraren som är associerad med en viss session kunna återställas från oväntade fel och kan återuppta delvis slutfört arbete på en annan process eller dator från där arbetet påbörjades.

Sessionstillståndsfunktionen möjliggör en programdefinierad anteckning av en meddelandesession inuti mäklaren, så att det inspelade bearbetningstillståndet i förhållande till den sessionen blir omedelbart tillgängligt när sessionen hämtas av en ny processor.

Från servicebussperspektivet är meddelandesessionstillståndet ett ogenomskinligt binärt objekt som kan innehålla data om storleken på ett meddelande, vilket är 256 KB för Service Bus Standard och 1 MB för Service Bus Premium. Bearbetningstillståndet i förhållande till en session kan hållas i sessionstillståndet, eller så kan sessionstillståndet peka på en lagringsplats eller databaspost som innehåller sådan information.

API:erna för hantering av sessionstillstånd, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) och [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), finns på [MessageSession-objektet](/dotnet/api/microsoft.servicebus.messaging.messagesession) i både C# och Java API:er. En session som tidigare inte har någon sessionstillståndsuppsättning returnerar en **null-referens** för **GetState**. Rensa det tidigare inställda sessionstillståndet görs med [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Sessionstillståndet kvarstår så länge det inte rensas upp (returnerar **null),** även om alla meddelanden i en session förbrukas.

Alla befintliga sessioner i en kö eller prenumeration kan räknas upp med **metoden SessionBrowser** i Java-API:et och med [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) i [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) och [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) i .NET Framework-klienten.

Sessionstillståndet som finns i en kö eller i en prenumeration räknas mot den entitetens lagringskvot. När programmet är klart med en session rekommenderas därför att programmet rensar sitt behållna tillstånd för att undvika externa hanteringskostnader.

### <a name="impact-of-delivery-count"></a>Effekten av antalet leveranser

Definitionen av leveransantal per meddelande i samband med sessioner varierar något från definitionen i avsaknad av sessioner. Här är en tabell som sammanfattar när antalet leveranser ökas.

| Scenario | Är meddelandets leveransantal ökat |
|----------|---------------------------------------------|
| Sessionen accepteras, men sessionslåset upphör att gälla (på grund av timeout) | Ja |
| Sessionen accepteras, meddelandena i sessionen slutförs inte (även om de är låsta) och sessionen stängs | Inga |
| Sessionen accepteras, meddelanden slutförs och sedan stängs sessionen uttryckligen | N/A (Det är standardflödet. Här tas meddelanden bort från sessionen) |

## <a name="request-response-pattern"></a>Mönster för begäran-svar
[Mönstret för begäran-svar](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) är ett väletablerat integrationsmönster som gör att avsändanprogrammet kan skicka en begäran och ger mottagaren ett sätt att skicka tillbaka ett svar till avsändaprogrammet på rätt sätt. Det här mönstret behöver vanligtvis en kortlivad kö eller ett ämne som programmet kan skicka svar till. I det här fallet ger sessioner en enkel alternativ lösning med jämförbar semantik. 

Flera program kan skicka sina begäranden till en enda begäranskön, med en specifik rubrikparameter inställd på att unikt identifiera avsändningsprogrammet. Mottagarprogrammet kan bearbeta de begäranden som kommer i kön och skicka svar på en session aktiverad kö och ange sessions-ID:t till den unika identifierare som avsändaren hade skickat på begäran. Programmet som skickade begäran kan sedan ta emot meddelanden om ett visst sessions-ID och korrekt bearbeta svaren.

> [!NOTE]
> Programmet som skickar de första begärandena bör `SessionClient.AcceptMessageSession(SessionID)` känna till sessions-ID:et och använda för att låsa sessionen där svaret förväntas. Det är en bra idé att använda ett GUID som unikt identifierar instansen av programmet som ett sessions-ID. Det bör inte finnas `AcceptMessageSession(timeout)` någon sessionshanterare eller i kön för att säkerställa att svaren är tillgängliga för att låsas och bearbetas av specifika mottagare.

## <a name="next-steps"></a>Nästa steg

- Se antingen [exempelexemplen microsoft.azure.servicebus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) eller [Microsoft.ServiceBus.Messaging för](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) ett exempel som använder .NET Framework-klienten för att hantera sessionsmedvetna meddelanden. 

Mer information om Service Bus-meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
