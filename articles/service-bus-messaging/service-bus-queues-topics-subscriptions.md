---
title: "Översikt över Azure Service Bus-meddelanden köer, ämnen och prenumerationer | Microsoft Docs"
description: "Översikt över Service Bus meddelandeentiteter."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a306ced4-74e9-47c6-990a-d9c47efa31d5
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: sethm
ms.openlocfilehash: 00f9f38fbae028486270053dedb4df580a3f1a44
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus-köer, -ämnen och -prenumerationer

Microsoft Azure Service Bus stöder en uppsättning molnbaserade, meddelande indatavärdena mellanprogram teknologier, inklusive tillförlitliga message Queuing- och varaktig Publicera/prenumerera på meddelanden. Dessa ”asynkrona” meddelandefunktioner kan betraktas som frikopplad meddelandefunktioner som har stöd för att publicera och prenumerera temporal Frikoppling och scenarier med hjälp av Service Bus-meddelanden fabric för belastningsutjämning. Frikopplad kommunikation har många fördelar: klienter och servrar kan till exempel ansluta efter behov och utföra åtgärder på ett asynkront sätt.

Meddelandeentiteter som utgör kärnan i meddelandefunktioner i Service Bus är köer, ämnen och prenumerationer och regler/en åtgärd.

## <a name="queues"></a>Köer

Köer erbjuder *First In, först ut* (FIFO) meddelandeleverans till en eller flera konkurrerande konsumenter. Det vill säga förväntas meddelanden vanligtvis tas emot och bearbetas av mottagarna i den ordning som de har lagts till i kön, och varje meddelande tas emot och bearbetas av bara en meddelandekonsument. En stor fördel med köer är att uppnå ”temporal Frikoppling” av programkomponenter. Med andra ord behöver producenter (avsändare) och konsumenter (mottagare) inte skicka och ta emot meddelanden på samma gång, eftersom meddelanden lagras varaktigt i kön. Dessutom behöver producenten inte vänta på svar från konsumenten för att kunna fortsätta bearbetningen och skicka meddelanden.

En relaterad fördel är ”belastningsutjämning”, vilket innebär att producenter och konsumenter att skicka och ta emot meddelanden med olika hastigheter. I många program varierar systembelastningen med tiden. dock är den bearbetningstid som krävs för varje arbetsenhet vanligtvis är konstant. Medlingen mellan meddelandeproducenter och konsumenter med hjälp av en kö innebär att den konsumerande appen endast som ska etableras för att kunna hantera en genomsnittlig belastning i stället för belastning. Köns djup växer och dras samman allt eftersom den inkommande belastningen varierar. Detta sparar direkt pengar avseende mängden infrastruktur som krävs för att underhålla. När belastningen ökar kan fler arbetsprocesser läggas för att läsa från kön. Varje meddelande bearbetas bara av en av arbetsprocesserna. Dessutom kan den här pull-baserade belastningsutjämning, för optimal användning av worker-datorer även om worker datorer skiljer sig åt vad gäller processorkraft, som de hämtar meddelanden med sina egna högsta hastighet. Det här mönstret kallas ofta ”konkurrerande konsument”-mönster.

Med hjälp av köer till mellanliggande mellan meddelandeproducenter och konsumenter innehåller en inbyggd lösa kopplingar mellan komponenterna. Eftersom producenter och konsumenter inte är medvetna om varandra, kan en konsument uppgraderas utan någon inverkan på producenten.

Skapa en kö är en process med flera steg. Du kan utföra hanteringsåtgärder för Service Bus meddelandeentiteter (köer och ämnen) via den [Microsoft.ServiceBus.NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) -klassen, som har skapats genom att tillhandahålla basadressen för namnområdet för Service Bus och användarens autentiseringsuppgifter. [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) tillhandahåller metoder för att skapa, räkna upp och ta bort meddelandeentiteter. När du har skapat en [Microsoft.ServiceBus.TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) objekt från SAS-namn och nyckel och ett namnområde servicehantering objekt, kan du använda den [Microsoft.ServiceBus.NamespaceManager.CreateQueue](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateQueue_System_String_)metod för att skapa kön. Exempel:

```csharp
// Create management credentials
TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

Du kan sedan skapa ett köobjekt och en meddelandefabrik med Service Bus-URI som argument. Exempel:

```csharp
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

Du kan skicka meddelanden till kön. Om du har en lista över asynkrona meddelanden som kallas exempelvis `MessageList`, visas den liknar följande:

```csharp
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

Du sedan ta emot meddelanden från kön på följande sätt:

```csharp
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

I den [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) läge receive-åtgärden är en, det vill säga när Service Bus tar emot begäran, den markerar meddelandet som Förbrukat och skickar tillbaka det till programmet. **ReceiveAndDelete** läge är den enklaste modellen och fungerar bäst för scenarier där programmet kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus markerar meddelandet som Förbrukat när programmet startas om och börjar förbruka meddelanden igen, kommer det ha missat meddelandet som förbrukades innan kraschen.

I [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) läge receive-åtgärden blir två steg, vilket gör det möjligt att stödprogram som inte tolererar att ett meddelande saknas. När Service Bus tar emot begäran, den söker efter nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har avslutat bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning), slutför programmet det andra steget i processen genom att anropa [Slutför](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) för det mottagna meddelandet. När Service Bus ser den **Slutför** anrop, markerar den meddelandet som Förbrukat.

Om programmet inte kan bearbeta meddelandet av någon anledning, kan det anropa den [Avbryt](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) metod för det mottagna meddelandet (i stället för [Slutför](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). Detta gör att Service Bus låser upp meddelandet och gör det tillgängligt att tas emot igen, antingen genom samma konsumenten eller av en annan konkurrerande konsument. Det finns en tidsgräns som är kopplade till låset för det andra och om programmet misslyckas med att bearbeta meddelandet innan timeout för lås upphör att gälla (till exempel om programmet kraschar), kommer Service Bus låser upp meddelandet och gör det tillgängligt och kan tas emot igen ( i praktiken utför en [Avbryt](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) åtgärden som standard).

Observera att i händelse av att programmet kraschar efter att meddelandet har bearbetats men innan det **Slutför** begäran har utfärdats, meddelandet är levereras på nytt till programmet när den startas om. Det här kallas ofta *minst när* bearbetning, det vill säga varje meddelande bearbetas minst en gång. I vissa situationer kan dock samma meddelande levereras. Om scenariot inte tolererar duplicerad bearbetning och sedan ytterligare logik som krävs i programmet för att identifiera dubbletter som kan ske, baserat på de **MessageId** för meddelandet, som förblir konstant över leveransförsök. Detta kallas *exakt en gång* bearbetning.

## <a name="topics-and-subscriptions"></a>Ämnen och prenumerationer
Till skillnad från köer, där varje meddelande bearbetas av en enskild konsument *avsnitt* och *prenumerationer* tillhandahålla en en-till-många-kommunikation i en *förPublicera/prenumerera* mönster. Användbar för att skala till ett stort antal mottagare, varje publicerat meddelande görs tillgänglig för varje prenumeration som har registrerats med ämnet. Meddelanden skickas till ett ämne och levereras till en eller flera associerade prenumerationer, beroende på filterregler som kan ställas in på grundval av per prenumeration. Prenumerationerna kan använda ytterligare filter för att begränsa de meddelanden som de vill ha. Meddelanden skickas till ett ämne på samma sätt som de skickas till en kö, men meddelanden tas inte emot i artikeln direkt. I stället tas de emot från prenumerationer. En prenumeration på artikeln liknar en virtuell kö som tar emot kopior av meddelanden som skickas till ämnet. Meddelanden tas emot från en prenumeration identiskt sätt som de tas emot från en kö.

Som jämförelse sändning av meddelande-funktionerna i en kö mappar direkt till ett ämne och dess funktioner för message-mottagning mappar till en prenumeration. Det innebär att prenumerationer stöder samma mönster som beskrivits tidigare i det här avsnittet avseende köer bland annat: konkurrerande konsument, temporal Frikoppling, utjämna belastningen och belastningsutjämning.

Skapa ett ämne liknar att skapa en kö som visas i exemplet i föregående avsnitt. Skapa URI-tjänsten och sedan använda den [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) klassen för att skapa namnområdet klient. Du kan sedan skapa ett avsnitt som använder den [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_) metod. Exempel:

```csharp
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

Lägg till prenumerationer som du vill:

```csharp
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

Du kan sedan skapa en klient i avsnittet. Exempel:

```csharp
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

Med hjälp av avsändaren kan du skicka och ta emot meddelanden till och från avsnittet som visas i föregående avsnitt. Exempel:

```csharp
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

Liknande köer, meddelanden tas emot från en prenumeration med hjälp av en [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) objekt i stället för en [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) objekt. Skapa prenumeration klienten, skickar namnet på avsnittet, namnet på prenumerationen och (valfritt) receive-läge som parametrar. Om till exempel den **inventering** prenumerationen:

```csharp
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Regler och åtgärder
I många fall är måste meddelanden som har specifika egenskaper bearbetas på olika sätt. För att möjliggöra detta, kan du konfigurera prenumerationer för att söka efter meddelanden som har önskade egenskaper och sedan göra vissa ändringar av dessa egenskaper. Du kan bara kopiera en delmängd av dessa meddelanden till prenumerationskön virtuella när Service Bus prenumerationer visas alla meddelanden som skickas till ämnet. Detta åstadkoms med hjälp av prenumerationsfilter. Sådana ändringar kallas *filtrera åtgärder*. När en prenumeration har skapats kan du ange ett filteruttryck som fungerar i egenskaperna för meddelandet, både systemegenskaperna (till exempel **etikett**) och anpassade egenskaper (till exempel  **StoreName**.) Filteruttrycket SQL är valfri i det här fallet; utan en SQL-filteruttrycket utförs något filter som definieras för en prenumeration på alla meddelanden för den prenumerationen.

I föregående exempel, att filtrera meddelanden kommer endast från **Store1**, skulle du skapa instrumentpanelen prenumeration på följande sätt:

```csharp
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Med den här prenumerationsfiltret på plats, endast meddelanden som har den `StoreName` egenskapen `Store1` kopieras till en virtuell kö för den `Dashboard` prenumeration.

Mer information om möjliga filtret värden finns i dokumentationen för den [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) och [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) klasser. Se även den [asynkrona meddelanden: avancerade filter](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) och [avsnittet filter](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters) prover.

## <a name="next-steps"></a>Nästa steg
Se följande avancerade avsnitt för mer information och exempel på hur du använder Service Bus-meddelanden.

* [Översikt över Service Bus-meddelandetjänster](service-bus-messaging-overview.md)
* [Service Bus brokered messaging .NET tutorial](service-bus-brokered-tutorial-dotnet.md)
* [Service Bus självstudiekurs om asynkrona meddelanden REST](service-bus-brokered-tutorial-rest.md)
* [Ämnesfilter](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/TopicFilters)
* [Asynkrona meddelanden: Avancerade filter-exempel](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

