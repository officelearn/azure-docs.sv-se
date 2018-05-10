---
title: Skapa partitionerade Azure Service Bus-köer och ämnen | Microsoft Docs
description: Beskriver hur du partitionera Service Bus-köer och ämnen med hjälp av flera meddelandet mäklare.
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 05/08/2016
ms.author: sethm
ms.openlocfilehash: 0759decec9d80f1f836110a8907049213ca1eed6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="partitioned-queues-and-topics"></a>Partitionerade köer och ämnen

Azure Service Bus använder flera meddelandet mäklare att bearbeta meddelanden och flera meddelandearkiv för att lagra meddelanden. En vanlig kö eller ett ämne hanteras av en enda meddelande broker och lagras i ett meddelandearkiv. Service Bus *partitioner* aktivera köer och ämnen, eller *meddelandeentiteter*, som ska partitioneras över flera meddelandet mäklare och meddelandearkiv. Partitionering innebär det totala genomflödet av en partitionerad entitet begränsas inte längre av prestanda i ett enda meddelande broker eller meddelandearkiv. Dessutom kan återges ett tillfälligt avbrott i ett meddelandearkiv inte en partitionerad kö eller ett ämne inte tillgänglig. Partitionerade köer och ämnen kan innehålla alla avancerade Service Bus-funktioner, till exempel stöd för transaktioner och sessioner.

Information om Service Bus internals finns i [Service Bus-arkitektur] [ Service Bus architecture] artikel.

> [!NOTE]
> Partitionering är tillgänglig vid skapande av entiteten för alla köer och ämnen i Basic eller Standard SKU: er. Det är inte tillgängligt för Premium meddelande-SKU, men alla befintliga partitionerade enheter i Premium fungerar som förväntat.
 
Det går inte att ändra alternativet partitionering på en befintlig kö eller ett ämne; Du kan bara ange alternativet när du skapar entiteten.

## <a name="how-it-works"></a>Hur det fungerar

Varje partitionerade kö eller ett ämne består av flera fragment. Varje fragment lagras i en annan meddelandearkiv och hanteras av ett annat meddelande broker. När ett meddelande skickas till en partitionerad kö eller ett ämne, tilldelar Service Bus meddelandet till en av fragment. Markeringen är klar slumpmässigt med Service Bus eller en partitionsnyckel som avsändaren kan ange.

När en klient vill få ett meddelande från en partitionerad kö eller från en prenumeration på en partitionerad ämne, Service Bus frågor alla fragment för meddelanden, returnerar du det första meddelandet som erhålls från någon av meddelandearkiven på mottagaren. Service Bus cacheminnen den andra meddelanden och returnerar dem när den tar emot ytterligare ta emot begäranden. En mottagande klienten är inte medveten om partitionering; klientriktade beteendet för en partitionerad kö eller ett ämne (till exempel läsa, slutföra, skjuta upp systemkön, förhämtar) är identisk med beteendet för en vanlig entitet.

Det finns inga extra kostnad när de skickar ett meddelande till eller ta emot ett meddelande från en partitionerad kö eller ett ämne.

## <a name="enable-partitioning"></a>Aktivera partionering

Partitionerade köer och ämnen med Azure Service Bus kan använda Azure SDK version 2.2 eller senare, eller ange `api-version=2013-10` eller senare i HTTP-förfrågningar.

### <a name="standard"></a>Standard

I meddelanden standardnivån, kan du skapa Service Bus-köer och ämnen i 1, 2, 3, 4 och 5 GB-storlekar (standardvärdet är 1 GB). Med partitionering aktiverad, skapar Service Bus 16 kopior (16 partitioner) för entiteten för varje GB som du anger. Så om du skapar en kö är 5 GB i storlek med 16 partitioner största köstorlek blir (5 \* 16) = 80 GB. Du kan se den maximala storleken på din partitionerade kö eller ett ämne genom att titta på posten den [Azure-portalen][Azure portal]i den **översikt** bladet för den enheten.

### <a name="premium"></a>Premium

I ett namnområde för Premium-nivån, kan du skapa Service Bus-köer och ämnen i 1, 2, 3, 4, 5, 10, 20, 40 eller 80 GB-storlekar (standardvärdet är 1 GB). Med partitionering aktiverad som standard skapar Service Bus två partitioner per enhet. Du kan se den maximala storleken på din partitionerade kö eller ett ämne genom att titta på posten den [Azure-portalen][Azure portal]i den **översikt** bladet för den enheten.

Mer information om partitionering i Premium messaging nivån finns [Service Bus Premium- och Standard-meddelandenivåer](service-bus-premium-messaging.md). 

### <a name="create-a-partitioned-entity"></a>Skapa en partitionerad entitet

Det finns flera sätt att skapa en partitionerad kö eller ett ämne. När du skapar en kö eller ett ämne från ditt program kan du aktivera partitionering för kö eller avsnittet genom att ange respektive den [QueueDescription.EnablePartitioning] [ QueueDescription.EnablePartitioning] eller [TopicDescription.EnablePartitioning] [ TopicDescription.EnablePartitioning] egenskapen **SANT**. Dessa egenskaper måste anges när kön eller avsnittet skapas. Som nämnts tidigare anger, går det inte att ändra egenskaperna för en befintlig kö eller ett ämne. Exempel:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Du kan också skapa en partitionerad kö eller ett ämne i den [Azure-portalen] [ Azure portal] eller i Visual Studio. När du skapar en kö eller ett ämne i portalen på **aktivera partitionering** alternativ i en kö eller avsnittet **skapa** dialogrutan är markerad som standard. Du kan bara inaktivera det här alternativet i en standardnivån enheten. i Premium-nivån är partitionering alltid aktiverat. I Visual Studio klickar du på den **aktivera partitionering** kryssrutan i den **ny kö** eller **ny artikel** dialogrutan.

## <a name="use-of-partition-keys"></a>Användning av partitionsnycklar
När ett meddelande är i kö i en partitionerad kö eller ett ämne, kontrollerar Service Bus förekomsten av en partitionsnyckel. Om den hittar en väljs fragment baserat på nyckeln. Om det inte hittar en partitionsnyckel, väljs fragment baserat på en intern algoritm.

### <a name="using-a-partition-key"></a>Med hjälp av en partitionsnyckel
Vissa scenarier, till exempel sessioner eller transaktioner, kräva att meddelanden ska sparas i en specifik fragmentet. Dessa scenarier kräver användning av en partitionsnyckel. Alla meddelanden som använder samma partitionsnyckel har tilldelats samma fragment. Om avsnittet är inte tillgänglig för tillfället, returneras ett fel i Service Bus.

Beroende på scenario används olika meddelandeegenskaper som en partitionsnyckel:

**Sessions-ID**: om ett meddelande i [BrokeredMessage.SessionId] [ BrokeredMessage.SessionId] egenskapsuppsättning och Service Bus använder den här egenskapen som partitionsnyckel. På så sätt kan hanteras alla meddelanden som tillhör samma session av samma meddelande Service broker. Sessioner aktivera Service Bus att garantera meddelandet ordning samt konsekvenskontroll av sessionstillstånd.

**PartitionKey**: om ett meddelande i [BrokeredMessage.PartitionKey] [ BrokeredMessage.PartitionKey] egenskapen men inte den [BrokeredMessage.SessionId] [ BrokeredMessage.SessionId] egenskapsuppsättning och Service Bus använder sedan den [PartitionKey] [ PartitionKey] egenskap som partitionsnyckel. Om meddelandet har både den [SessionId] [ SessionId] och [PartitionKey] [ PartitionKey] egenskaper, båda egenskaperna måste vara identiska. Om den [PartitionKey] [ PartitionKey] egenskap är inställd på ett annat värde än den [SessionId] [ SessionId] Service Bus-egenskapen returnerar en ogiltig åtgärd undantag. Den [PartitionKey] [ PartitionKey] egenskapen ska användas om en avsändare skickar-session medveten transaktionella meddelanden. Partitionsnyckeln säkerställer att alla meddelanden som skickas i en transaktion som hanteras av samma asynkrona Service broker.

**MessageId**: om en kö eller avsnittet har den [QueueDescription.RequiresDuplicateDetection] [ QueueDescription.RequiresDuplicateDetection] egenskapen **SANT** och [ BrokeredMessage.SessionId] [ BrokeredMessage.SessionId] eller [BrokeredMessage.PartitionKey] [ BrokeredMessage.PartitionKey] egenskaper inte har angetts i [ BrokeredMessage.MessageId] [ BrokeredMessage.MessageId] egenskapen fungerar som partitionsnyckel. (Bibliotek för Microsoft .NET och AMQP tilldelar automatiskt ett meddelande-ID om avsändarprogrammet inte.) I så fall måste hanteras alla kopior av samma meddelande av samma meddelande Service broker. Detta ID kan Service Bus att identifiera och undvika dubbletter av meddelanden. Om den [QueueDescription.RequiresDuplicateDetection] [ QueueDescription.RequiresDuplicateDetection] inte egenskapen **SANT**, Service Bus betraktar inte den [MessageId] [ MessageId] egenskapen som en partitionsnyckel.

### <a name="not-using-a-partition-key"></a>Inte använder en partitionsnyckel
Saknas en partitionsnyckel distribuerar Service Bus meddelanden i en resursallokering sätt som alla delar av den partitionerade kö eller ett ämne. Om det valda fragmentet inte är tillgänglig tilldelar Service Bus meddelandet till en annan fragment. Det här sättet send-åtgärden lyckas trots temporär otillgänglighet ett meddelandearkiv. Men du kommer inte att uppnå garanterad ordningen som ger en partitionsnyckel.

En mer detaljerad beskrivning av förhållandet mellan tillgänglighet (ingen partitionsnyckel) och konsekvenskontroll (med en partitionsnyckel) finns [i den här artikeln](../event-hubs/event-hubs-availability-and-consistency.md). Den här informationen gäller lika partitionerade Service Bus-enheter.

För att ge Service Bus finns tillräckligt med tid för att sätta meddelandet till en annan fragment den [MessagingFactorySettings.OperationTimeout] [ MessagingFactorySettings.OperationTimeout] värdet som angetts för den klient som skickar meddelandet måste vara större än 15 sekunder. Vi rekommenderar att du ställer in den [OperationTimeout] [ OperationTimeout] egenskapen till standardvärdet 60 sekunder.

En partitionsnyckel PIN ”” ett meddelande till en specifik fragment. Om meddelandearkiv som innehåller det här fragmentet är tillgänglig, returneras ett fel i Service Bus. Om en partitionsnyckel, Service Bus kan välja ett annat fragment och åtgärden slutförs. Vi rekommenderar därför att du inte anger en partitionsnyckel om det inte krävs.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Avancerade alternativ: använda transaktioner med partitionerade enheter
Meddelanden som skickas som del i en transaktion måste ange en partitionsnyckel. Nyckeln kan vara något av följande egenskaper: [BrokeredMessage.SessionId][BrokeredMessage.SessionId], [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey], eller [ BrokeredMessage.MessageId][BrokeredMessage.MessageId]. Alla meddelanden som skickas som en del av samma transaktion måste ange samma partitionsnyckel. Om du försöker skicka ett meddelande utan någon partitionsnyckel inom en transaktion returnerar en ogiltig åtgärd undantag i Service Bus. Om du försöker skicka flera meddelanden inom samma transaktion som har olika partitionsnycklar returnerar en ogiltig åtgärd undantag i Service Bus. Exempel:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Om någon av egenskaperna som fungerar som en partitionsnyckel konfigureras PIN Service Bus-meddelande till en specifik fragment. Detta inträffar oavsett används för en transaktion. Vi rekommenderar att du inte anger en partitionsnyckel om det inte är nödvändigt.

## <a name="using-sessions-with-partitioned-entities"></a>Sessioner med partitionerade enheter
Om du vill skicka ett transaktionsmeddelande till en session-medvetna avsnittet eller kö, måste meddelandet ha den [BrokeredMessage.SessionId] [ BrokeredMessage.SessionId] egenskapsuppsättning. Om den [BrokeredMessage.PartitionKey] [ BrokeredMessage.PartitionKey] egenskapen anges också måste den vara identisk med den [SessionId] [ SessionId] egenskapen. Om de skiljer sig returnerar en ogiltig åtgärd undantag i Service Bus.

Till skillnad från vanliga (partitionerade) köer och ämnen går det inte att använda en enda transaktion för att skicka flera meddelanden till olika sessioner. Om försöktes, returnerar Service Bus en ogiltig åtgärd undantag. Exempel:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatisk vidarebefordring med partitionerade enheter
Service Bus stöder automatisk meddelandet vidarebefordran från, eller för mellan partitionerade enheter. Om du vill aktivera automatisk vidarebefordring, ange den [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] -egenskapen i källkön eller prenumeration. Om meddelandet anger en partitionsnyckel ([SessionId][SessionId], [PartitionKey][PartitionKey], eller [MessageId][MessageId]), som partitionsnyckel används för mål-entiteten.

## <a name="considerations-and-guidelines"></a>Information och riktlinjer
* **Hög konsekvenskontroll funktioner**: om en enhet använder funktioner som sessioner, identifiering av dubbletter eller explicit kontroll över partitionering nyckel och asynkrona åtgärder alltid dirigeras till specifika fragment. Om någon av fragment drabbas av hög trafik eller det underliggande arkivet är i feltillstånd, dessa åtgärder misslyckas och tillgänglighet minskar. Generellt sett är konsekvenskontrollen fortfarande mycket högre än partitionerade enheter. endast en del av trafiken upplever problem, till skillnad från all trafik. Mer information finns i det här [beskrivning av tillgänglighet och konsekvenskontroll](../event-hubs/event-hubs-availability-and-consistency.md).
* **Hantering av**: åtgärder, till exempel skapa, uppdatera och ta bort måste utföras på alla fragment för entiteten. Om några fragment är felfri, kan det resultera i fel för dessa åtgärder. För Get-åtgärden måste information såsom meddelandet räknar aggregeras från alla fragment. Om några fragment är felfri rapporteras tillgänglighetsstatus entitet som begränsad.
* **Volymen meddelandet scenarier med låg**: för sådana scenarier, särskilt om du använder HTTP-protokollet, du kan behöva utföra flera ta emot åtgärder för att hämta alla meddelanden. Ta emot förfrågningar klientdelen utför en receive på alla fragment och cachelagrar alla svar som tagits emot. En efterföljande receive-begäran på samma anslutning kan dra nytta av den här cachelagring och ta emot svarstiderna blir lägre. Men om du har flera anslutningar eller använda HTTP upprättar som en ny anslutning för varje begäran. Det finns därför ingen garanti för att den skulle hamna på samma nod. Om alla befintliga meddelanden är låst och cachelagras i en annan klientdelen, receive-åtgärd returnerar **null**. Meddelanden till slut upphör och du kan ta emot dem igen. HTTP keep-alive rekommenderas.
* **Bläddra/titt meddelanden**: [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) inte alltid Returnerar antal meddelanden som anges i den [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) egenskapen. Det finns två vanliga orsaker till problemet. En orsak är att samlingen för meddelanden aggregerade storlek överskrider den maximala storleken på 256 KB. En annan orsak är att om en kö eller avsnittet har den [EnablePartitioning egenskapen](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) inställd på **SANT**, en partition kan inte ha tillräckligt många meddelanden för att slutföra det begärda antalet meddelanden. I allmänhet om ett program att ta emot ett visst antal meddelanden, den ska anropa [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) upprepade gånger tills den hämtar det antalet meddelanden eller så finns det inga fler meddelanden att granska. Mer information, inklusive kodexempel, finns det [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) eller [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) API-dokumentationen.

## <a name="latest-added-features"></a>Senaste extrafunktioner
* Lägg till eller ta bort regeln stöds nu med partitionerade enheter. Skiljer sig från partitionerade enheter, dessa åtgärder stöds inte under transaktioner. 
* AMQP stöds nu för att skicka och ta emot meddelanden till och från en partitionerad entitet.
* AMQP har nu stöd för följande åtgärder: [Batch skicka](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [Batch får](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [ta emot med sekvensnumret](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [granska](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [förnya Lås](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [schema meddelandet](/dotnet/api/microsoft.servicebus.messaging.queueclient.schedulemessageasync), [Avbryt schemalagda meddelande](/dotnet/api/microsoft.servicebus.messaging.queueclient.cancelscheduledmessageasync), [Lägg till regel](/dotnet/api/microsoft.servicebus.messaging.ruledescription), [ta bort regeln](/dotnet/api/microsoft.servicebus.messaging.ruledescription), [Session förnya Lås](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [Set sessionstillstånd](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [Get sessionstillstånd](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate), och [räkna upp sessioner](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Begränsningar för partitionerade enheter
För närvarande medför Service Bus följande begränsningar på partitionerade köer och ämnen:

* Partitionerade köer och ämnen stöder inte skicka meddelanden som hör till olika sessioner i en enda transaktion.
* Service Bus tillåter för närvarande upp till 100 partitionerade köer och ämnen per namnområde. Varje partitionerade kö eller ett ämne räknar mot kvoten för 10 000 enheter per namnområde (inte gäller för Premium-nivån).

## <a name="next-steps"></a>Nästa steg
Läs mer om grundläggande begrepp för den AMQP 1.0 messaging specifikation i den [AMQP 1.0-protokollet guiden](service-bus-amqp-protocol-guide.md).

[Service Bus architecture]: service-bus-architecture.md
[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[BrokeredMessage.SessionId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid
[BrokeredMessage.PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey
[SessionId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey
[QueueDescription.RequiresDuplicateDetection]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid
[MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid
[MessagingFactorySettings.OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
