---
title: Azure Service Bus-meddelandesessioner | Microsoft Docs
description: Hantera sekvenser av Azure Service Bus-meddelanden med sessioner.
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: d6c46d6ebfa8ae44c9bfac4929d3478f6701758a
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497847"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Meddelandet sessioner: först in först ut (FIFO) 

Microsoft Azure Service Bus-sessioner gör gemensamma och sorterad hantering obegränsade sekvenser av relaterade meddelanden. Använd sessioner för att använda en FIFO-säkerhet i Service Bus. Service Bus är inte förebyggande om vilken typ av relationen mellan meddelanden och även definierar inte en viss modell för att fastställa där en Meddelandeordningen börjar eller slutar.

> [!NOTE]
> Basic-nivån i Service Bus stöder inte sessioner. Standard- och premiumnivåerna har stöd för sessioner. Mer information finns i [priser för Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Alla avsändare kan skapa en session när skickar meddelanden till ett ämne eller en kö genom att ange den [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) egenskapen till vissa programdefinierade identifierare som är unik för sessionen. På nivån AMQP 1.0-protokollet det här värdet som mappar till den *grupp-id* egenskapen.

På session-medvetna köer eller prenumerationer sessioner uppstå när det finns minst ett meddelande sessionens [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId). När en session finns, det finns ingen angiven tidpunkt eller API för när sessionen upphör att gälla eller försvinner. Teoretiskt sett kan ett meddelande tas emot för en session idag, nästa meddelande i ett år tid, och om den **SessionId** matchar sessionen är detsamma som Service Bus-perspektiv.

Vanligtvis har ett program dock en tydlig begreppet om en uppsättning relaterade meddelanden börjar och slutar. Service Bus anger inte några särskilda regler.

Ett exempel på hur du kan ge en bild av en sekvens för att överföra en fil är att ställa in den **etikett** -egenskapen för det första meddelandet till **starta**, för mellanliggande meddelanden ska **innehåll**, och för det sista meddelandet att **slutet**. Den relativa plats innehåll meddelanden kan beräknas som det aktuella meddelandet *SequenceNumber* delta från den **starta** meddelande *SequenceNumber*.

Session-funktionen i Service Bus möjliggör en specifik mottagningsåtgärd, i form av [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) i C# och Java-API: er. Du aktiverar funktionen genom att ange den [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) egenskap i kö eller prenumeration via Azure Resource Manager eller genom att ange flaggan i portalen. Detta krävs innan du försöker använda de relaterade API-åtgärderna.

Ställa in flaggan med följande kryssruta i portalen:

![][2]

API: er för sessioner finns på klienterna för kön och prenumeration. Det finns en tvingande modell som styr när sessioner och meddelanden tas emot och en hanterare-baserade modellen, liknar *OnMessage*, att döljer komplexiteten i att hantera receive-loop.

## <a name="session-features"></a>Sessionen funktioner

Sessioner ger samtidiga ta bort multiplexering av överlagrad meddelandeströmmar samtidigt bevara och garantera leverans.

![][1]

En [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) mottagare har skapats av klienten tar emot en session. Klienten anropar [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) eller [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) i C#. I modellen reaktiv återanrop registreras en hanterare för sessionen.

När den [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objekt accepteras och medan det lagras av en klient klienten innehåller ett exklusivt lås på alla meddelanden med den aktuella sessionen [SessionId](/en-us/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) som finns i kö eller prenumeration, och även på alla meddelanden med som **SessionId** som fortfarande tas emot medan sessionen lagras.

Låset släpps när **Stäng** eller **CloseAsync** kallas, eller när låset upphör att gälla i fall där programmet kan inte utföra åtgärden Stäng. Låset för sessionen ska behandlas som ett exklusivt lås på en fil, vilket innebär att programmet bör stänga sessionen när den inte längre behöver den och/eller förväntar sig inte några ytterligare meddelanden.

När flera samtidiga mottagare hämta från kön, skickas de meddelanden som tillhör en viss session till specifika mottagare som för närvarande innehar låset för den aktuella sessionen. Med den åtgärden en överlagrad meddelandeströmmen som finns i en kö eller prenumeration är korrekt placerad ta bort multiplex till olika mottagare och dessa mottagare kan också live på olika klientdatorer, eftersom Lås management händer tjänstsidan, inuti Service Bus.

I föregående bild visas tre samtidiga session mottagare. En Session med `SessionId` = 4 har inga aktiva, ägande klienten, vilket innebär att inga meddelanden levereras från den här specifika sessionen. En session fungerar på många sätt som en sub-kö.

Sessionen låset för sessionen mottagaren är en utökning inom för meddelande-lås som används av den *peek-lock* avräkning läge. En mottagare kan inte ha två meddelanden samtidigt ”i vila”, men meddelandena måste bearbetas i ordning. Ett nytt meddelande kan endast hämtas när det föregående meddelandet har slutförts eller dead lettered. Lämna ett meddelande orsaker som har samma meddelande jobbdata igen med nästa mottagningsåtgärd.

## <a name="message-session-state"></a>Sessionstillstånd för meddelande

När arbetsflöden bearbetas i hög skalbarhet, hög tillgänglighet molnsystem, arbetsflöde hanteraren som är associerade med en viss session måste kunna återställa från fel och även kunna återuppta delvis slutförd arbete på en annan processen eller datorn från där arbetet började.

Funktionen session tillstånd kan anteckningen programdefinierade meddelande sessionens inuti den asynkrona meddelandekön så att tillståndet inspelade bearbetning i förhållande till den aktuella sessionen blir omedelbart tillgänglig när sessionen förvärvas av en ny processor.

Service Bus-perspektiv är meddelande sessionens tillstånd en täckande binära objekt som kan innehålla data av storleken på ett meddelande som är 256 KB för Service Bus Standard och 1 MB för Service Bus Premium. Tillståndet för bearbetning i förhållande till en session kan lagras i sessionens tillstånd eller sessionens tillstånd kan peka på vissa lagringsplats eller databaspost som innehåller sådan information.

API: er för att hantera sessionstillstånd, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) och [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), kan hittas på den [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objekt i både C# och Java-API: er. En session som hade tidigare inga sessionstillstånd ange returnerar en **null** referera för **GetState**. Rensar tidigare använda sessionens tillstånd är klar med [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Observera att sessionstillstånd förblir så länge inte rensas (returnerar **null**), även om alla meddelanden i en session förbrukas.

Alla befintliga sessioner i en kö eller prenumeration kan räknas med i **SessionBrowser** metod i Java-API och med [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) på den [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) och [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) i .NET-klient.

Sessionens tillstånd lagras i en kö eller i en prenumeration räknas mot lagringskvoten för denna entitet. När programmet är klar med ett session-bör därför för programmet att rensa det sparade tillståndet att undvika kostnader för extern hanteringsserver.

## <a name="next-steps"></a>Nästa steg

- [Ett komplett exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) skicka och ta emot sessionsbaserad meddelanden från Service Bus köer med .NET Standard-biblioteket.
- [Ett sampel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) som använder .NET Framework-klient för att hantera sessions-anpassade meddelanden. 

Om du vill veta mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png