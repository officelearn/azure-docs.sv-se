---
title: Azure Service Bus meddelandet sessioner | Microsoft Docs
description: Hantera sekvenser av Azure Service Bus-meddelanden med sessioner.
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: sethm
ms.openlocfilehash: 7a594e5951f6e90c9151fbaf231675d6ed091d1f
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Meddelande sessioner: först in, först ut (FIFO) 

Microsoft Azure Service Bus-sessioner aktivera gemensamma och beställda hantering av unbounded sekvenser av relaterade meddelanden. Använda sessioner för att använda en FIFO säkerhet i Service Bus. Service Bus är inte normativ om vilken typ av relationen mellan meddelanden och även definierar inte en viss modell för att fastställa om en meddelandet följd börjar eller slutar.

Alla avsändarens kan skapa en session när skickar meddelanden till ett ämne eller en kö genom att ange den [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) egenskapen till vissa tillämpningsdefinierad identifierare som är unik för sessionen. På nivån AMQP 1.0-protokollet värdet mappar till den *grupp-id* egenskapen.

I session-medvetna köer eller prenumerationer sessioner uppstå om det finns minst ett meddelande sessionens [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId). När en session finns, det finns ingen angiven tidpunkt eller API för när sessionen upphör att gälla eller tas bort. Teoretiskt, kan ett meddelande tas emot för en session idag nästa meddelande i ett år tid, och om den **SessionId** matchar, sessionen är samma ur ett Service Bus.

Vanligtvis har ett program dock Radera begreppet där en uppsättning relaterade meddelanden börjar och slutar. Särskilda regler som angetts för Service Bus.

Ett exempel på hur du avgränsa en sekvens för att överföra en fil är att ange den **etikett** -egenskapen för det första meddelandet till **starta**, för mellanliggande meddelanden till **innehåll**, och för det sista meddelandet till **end**. Den relativa positionen för innehåll meddelanden kan beräknas som det aktuella meddelandet *SequenceNumber* delta från den **starta** meddelandet *SequenceNumber*.

Funktionen session i Service Bus gör en specifik mottagningsåtgärd, i form av [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) i C# och Java API: er. Du aktiverar funktionen genom att ange den [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) -egenskapen i kö eller prenumeration via Azure Resource Manager eller genom att ange flaggan i portalen. Detta krävs innan du försöker använda de relaterade API-operationerna.

Ställ in flaggan med följande kryssruta i portalen:

![][2]

API: er för sessioner finns på klienter i kön och prenumeration. Det finns en tvingande modell som styr när sessioner och meddelanden som tas emot och en hanterare-baserade modell, liknar *OnMessage*, att döljer komplexiteten med att hantera tar emot slinga.

## <a name="session-features"></a>Sessionen funktioner

Sessioner ange samtidiga Frigör multiplexering av överlagrad meddelandeströmmar samtidigt bevara och garanterar leverans.

![][1]

En [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) mottagaren har skapats av klienten accepterar en session. Klienten anropar [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) eller [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) i C#. I modellen reaktiv återanrop registreras en session-hanterare.

När den [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objekt accepteras och när den hålls av en klient att klientdatorer har ett exklusivt lås på alla meddelanden med den aktuella sessionen [SessionId](/en-us/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) som finns i kön eller prenumeration, och även om alla meddelanden med som **SessionId** som fortfarande tas emot medan sessionen hålls.

Låset frigörs när **Stäng** eller **CloseAsync** kallas, eller när låset upphör att gälla i fall där programmet kan inte utföra close-åtgärd. Låset session ska behandlas som ett exklusivt lås på en fil, vilket innebär att programmet ska stänga sessionen när den inte längre behöver den och/eller förväntar sig inte några fler meddelanden.

När flera samtidiga mottagare hämtar från kön, skickas de meddelanden som tillhör en viss session till specifika mottagare som för närvarande innehar låset för den aktuella sessionen. Med åtgärden, en överlagrad meddelandeströmmen som finns i en kö eller prenumeration är klar Frigör multiplex till olika mottagare och dessa mottagare kan också live på olika klientdatorer, eftersom Lås management händer tjänsten på klientsidan, inuti Service Bus.

En kö är dock fortfarande en kö: det finns inga direktåtkomst. Om flera samtidiga mottagare väntetiden för att acceptera specifika sessioner eller vänta tills meddelanden från specifika sessioner och det finns ett meddelande längst upp i en kö som hör till en session som har ingen mottagare ännu gällande håller leveranser tills en session mottagare anspråk som sessionen.

I föregående bild visas tre samtidiga session mottagare, som aktivt måste vidta meddelanden från kön för varje mottagare fortsätta. Den senaste sessionen med `SessionId` = 4 har inga aktiva, ägande klienten, vilket innebär att inga meddelanden levereras till någon tills meddelandet har tagits av en nyligen skapade äger sessionen mottagare.

Medan som visas för att begränsa kan en enda mottagare process hantera många samtidiga sessioner enkelt, särskilt när de skrivs med strikt asynkron kod. Samtidig användning av flera dussin samtidiga sessioner är effektivt automatisk med återanrop modellen.

Strategi för hantering av många samtidiga sessioner, där varje session bara ibland tar emot meddelanden, för hanteraren om att ta bort sessionen efter en ledig tid och fortsätta att bearbeta när sessionen accepteras när nästa session anländer.

Låset för session för session mottagaren är ett samlingsnamn för meddelande-lås som används av den *titt Lås* betalning läge. En mottagare kan inte ha två meddelanden samtidigt ”som rör sig”, men meddelandena som måste bearbetas i ordning. Ett nytt meddelande kan endast hämtas när det föregående meddelandet har slutförts eller förlorade lettered. Avbryter en meddelandet orsaker samma meddelande till hanteras igen nästa för att ta emot åtgärden.

## <a name="message-session-state"></a>Meddelandet sessionstillstånd

När arbetsflöden bearbetas i hög skala, molnsystem för hög tillgänglighet, hanterare för arbetsflöde för en viss session måste kunna återställa från oväntade fel och även kunna återuppta delvis slutförd arbete på en annan process eller datorn från där arbetet påbörjades.

Funktionen session tillstånd kan programdefinierade anteckningen sessionens meddelande i Service broker, så att inspelade bearbetningstillstånd i förhållande till den aktuella sessionen blir omedelbart tillgängliga när sessionen förvärvas av en ny processor.

Från Service Bus-perspektiv är meddelandet sessionens tillstånd ett täckande binära objekt som kan innehålla data av storleken på ett meddelande som är 256 KB för Service Bus Standard och 1 MB för Service Bus Premium. Bearbetningstillstånd i förhållande till en session kan lagras i sessionens tillstånd eller sessionens tillstånd kan leda till vissa lagringsplats eller databaspost som innehåller sådan information.

API: er för att hantera sessionens tillstånd [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) och [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), kan hittas på den [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objekt i både C# och Java API: er. En session som hade tidigare inga sessionstillstånd ange returnerar en **null** referera för **GetState**. Rensar tidigare inställd sessionens tillstånd är klar med [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Observera att sessionstillstånd förblir så länge inte rensas (returnerar **null**), även om alla meddelanden i en session förbrukas.

Alla befintliga sessioner i en kö eller en prenumeration kan räknas med i **SessionBrowser** metod i Java-API och med [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) på den [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) och [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) i .NET-klienten.

Sessionens tillstånd lagras i en kö eller i en prenumeration räknar mot lagringskvoten för att vara den entiteten. När programmet avslutas med en session, därför rekommenderas för programmet att rensa det sparade tillståndet att undvika kostnader för extern hanteringsserver.

## <a name="next-steps"></a>Nästa steg

- [En komplett exempel](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) köer med hjälp av .NET standardbibliotek skicka och ta emot sessionsbaserade meddelanden från Service Bus.
- [Ett exempel på en](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) som använder .NET Framework-klienten för att hantera sessions-anpassade meddelanden. 

Om du vill lära dig mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png