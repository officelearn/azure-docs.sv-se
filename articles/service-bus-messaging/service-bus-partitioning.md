---
title: Skapa partitionerade Azure Service Bus-köer och ämnen | Microsoft-dokument
description: Beskriver hur du partitionerar servicebussköer och ämnen med hjälp av flera meddelandemäklare.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 02/06/2020
ms.author: aschhab
ms.openlocfilehash: 671368993acb43c0d55eca73119effa934e3cff8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260948"
---
# <a name="partitioned-queues-and-topics"></a>Partitionerade köer och ämnen

Azure Service Bus använder flera meddelandemäklare för att bearbeta meddelanden och flera meddelandearkiv för att lagra meddelanden. En vanlig kö eller ett vanligt ämne hanteras av en enda meddelandemäklare och lagras i ett meddelandearkiv. Service *Bus-partitioner* gör det möjligt att dela köer och ämnen, eller *meddelandeentiteter,* som ska delas över flera meddelandemäklare och meddelandearkiv. Partitionering innebär att det övergripande dataflödet för en partitionerad entitet inte längre begränsas av prestanda för en enda meddelandemäklare eller meddelandearkiv. Dessutom gör ett tillfälligt avbrott i ett meddelandearkiv inte en partitionerad kö eller ett ämne otillgängligt. Partitionerade köer och ämnen kan innehålla alla avancerade Service Bus-funktioner, till exempel stöd för transaktioner och sessioner.

> [!NOTE]
> Partitionering är tillgängligt när entiteten skapas för alla köer och ämnen i grundläggande skusor eller standardseku:er. Det är inte tillgängligt för Premium messaging SKU, men alla tidigare partitionerade enheter i Premium-namnområden fortsätter att fungera som förväntat.
 
Det går inte att ändra partitioneringsalternativet i någon befintlig kö eller något befintligt ämne. Du kan bara ange alternativet när du skapar entiteten.

## <a name="how-it-works"></a>Hur det fungerar

Varje partitionerad kö eller ämne består av flera partitioner. Varje partition lagras i ett annat meddelandearkiv och hanteras av en annan meddelandemäklare. När ett meddelande skickas till en partitionerad kö eller ett ämne tilldelar Service Bus meddelandet till en av partitionerna. Valet görs slumpmässigt av Service Bus eller med hjälp av en partitionsnyckel som avsändaren kan ange.

När en klient vill ta emot ett meddelande från en partitionerad kö, eller från en prenumeration på ett partitionerat ämne, frågar Service Bus alla partitioner efter meddelanden och returnerar sedan det första meddelandet som hämtas från något av meddelandearkiven till mottagaren. Service Bus cachelagrar de andra meddelandena och returnerar dem när den tar emot ytterligare mottagningsbegäranden. En mottagande klient är inte medveten om partitioneringen. klientlägesbeteendet för en partitionerad kö eller ett ämne (till exempel läsa, slutföra, skjuta upp, deadletter, prefetching) är identisk med beteendet hos en vanlig entitet.

Det finns ingen extra kostnad när du skickar ett meddelande till eller tar emot ett meddelande från en partitionerad kö eller ett ämne.

## <a name="enable-partitioning"></a>Aktivera partitionering

Om du vill använda partitionerade köer och ämnen med Azure Service Bus använder du `api-version=2013-10` Azure SDK version 2.2 eller senare, eller anger eller senare i dina HTTP-begäranden.

### <a name="standard"></a>Standard

I standardmeddelandenivån kan du skapa servicebussköer och avsnitt i storlekarna 1, 2, 3, 4 eller 5 GB (standardvärdet är 1 GB). Med partitionering aktiverat skapar Service Bus 16 kopior (16 partitioner) av entiteten, var och en av samma angivna storlek. Om du skapar en kö som är 5 GB stor, med 16 partitioner \* blir den maximala köstorleken (5 16) = 80 GB. Du kan se den maximala storleken på din partitionerade kö eller ämne genom att titta på dess post på [Azure-portalen][Azure portal], i **bladet Översikt** för den entiteten.

### <a name="premium"></a>Premium

I ett namnområde på Premium-nivå stöds inte partitioneringsentiteter. Du kan dock fortfarande skapa servicebussköer och avsnitt i storlekarna 1, 2, 3, 4, 5, 10, 20, 40 eller 80 GB (standardvärdet är 1 GB). Du kan se storleken på din kö eller ditt ämne genom att titta på dess post på [Azure-portalen][Azure portal]i **bladet Översikt** för den entiteten.

### <a name="create-a-partitioned-entity"></a>Skapa en partitionerad entitet

Det finns flera sätt att skapa en partitionerad kö eller ett ämne. När du skapar kön eller ämnet från ditt program kan du aktivera partitionering för kön eller ämnet genom att ange egenskapen [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] eller [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] till **true**. Dessa egenskaper måste anges när kön eller ämnet skapas och är endast tillgängliga i det äldre [WindowsAzure.ServiceBus-biblioteket.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) Som tidigare nämnts är det inte möjligt att ändra dessa egenskaper i en befintlig kö eller ett befintligt ämne. Ett exempel:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Du kan också skapa en partitionerad kö eller ett ämne i [Azure-portalen][Azure portal]. När du skapar en kö eller ett ämne i portalen markeras alternativet **Aktivera partitionering** i dialogrutan Skapa i kön eller ämne **dialogrutan Skapa** som standard. Du kan bara inaktivera det här alternativet i en entitet på standardnivå. i partitioneringen på Premium-nivån stöds inte och kryssrutan har ingen effekt. 

## <a name="use-of-partition-keys"></a>Användning av partitionsnycklar

När ett meddelande är enqueued i en partitionerad kö eller ämne, Service Bus söker efter förekomsten av en partitionsnyckel. Om den hittar en, väljer den partitionen baserat på den nyckeln. Om den inte hittar en partitionsnyckel väljer den partitionen baserat på en intern algoritm.

### <a name="using-a-partition-key"></a>Använda en partitionsnyckel

Vissa scenarier, till exempel sessioner eller transaktioner, kräver att meddelanden lagras i en viss partition. Alla dessa scenarier kräver användning av en partitionsnyckel. Alla meddelanden som använder samma partitionsnyckel tilldelas samma partition. Om partitionen inte är tillgänglig för tillfället returnerar Service Bus ett fel.

Beroende på scenariot används olika meddelandeegenskaper som en partitionsnyckel:

**SessionId**: Om ett meddelande har egenskapen [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) inställt använder Service Bus **SessionID** som partitionsnyckel. På så sätt hanteras alla meddelanden som tillhör samma session av samma meddelandemäklare. Sessioner gör det möjligt för Service Bus att garantera meddelandebeställning samt konsekvensen i sessionstillstånd.

**PartitionKey**: Om ett meddelande har egenskapen [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) men inte [egenskapen SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) använder Service Bus egenskapsvärdet [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) som partitionsnyckel. Om meddelandet har både [egenskaperna SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) och [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) måste båda egenskaperna vara identiska. Om egenskapen [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) är inställd på ett annat värde än egenskapen [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) returnerar Service Bus ett ogiltigt åtgärdsundantag. Egenskapen [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ska användas om en avsändare skickar icke-sessionsmedvetna transaktionsmeddelanden. Partitionsnyckeln säkerställer att alla meddelanden som skickas inom en transaktion hanteras av samma meddelandemäklare.

**MessageId**: Om kön eller avsnittet har egenskapen [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) inställt på **true** och egenskaperna [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) eller [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) inte har angetts, fungerar egenskapsvärdet [för MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) som partitionsnyckel. (Microsoft .NET- och AMQP-biblioteken tilldelar automatiskt ett meddelande-ID om det sändande programmet inte gör det.) I det här fallet hanteras alla kopior av samma meddelande av samma meddelandemäklare. Med det här ID:t kan Service Bus identifiera och eliminera dubblettmeddelanden. Om egenskapen [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) inte är inställd på **true**betraktas inte [egenskapen MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) som en partitionsnyckel.

### <a name="not-using-a-partition-key"></a>Du använder inte en partitionsnyckel

I avsaknad av en partitionsnyckel distribuerar Service Bus meddelanden på ett round-robin-sätt till alla partitioner i den partitionerade kön eller det partitionerade ämnet. Om den valda partitionen inte är tillgänglig tilldelar Service Bus meddelandet till en annan partition. På så sätt lyckas skicka-åtgärden trots att en meddelandebutik är tillfällig. Du kommer dock inte att uppnå den garanterade ordning som en partitionsnyckel ger.

En mer djupgående diskussion om avvägningen mellan tillgänglighet (ingen partitionsnyckel) och konsekvens (med hjälp av en partitionsnyckel) finns i den [här artikeln](../event-hubs/event-hubs-availability-and-consistency.md). Den här informationen gäller även för partitionerade Service Bus-entiteter.

Om du vill ge Service Bus tillräckligt med tid för att köa meddelandet till en annan partition måste [operationTimeout-värdet](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) som anges av klienten och som skickar meddelandet vara större än 15 sekunder. Vi rekommenderar att du ställer in egenskapen [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) till standardvärdet 60 sekunder.

En partitionsnyckel "fäster" ett meddelande till en viss partition. Om meddelandearkivet som innehåller den här partitionen inte är tillgängligt returnerar Service Bus ett fel. I avsaknad av en partitionsnyckel kan Service Bus välja en annan partition och åtgärden lyckas. Därför rekommenderas att du inte anger en partitionsnyckel om det inte krävs.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Avancerade ämnen: använda transaktioner med partitionerade entiteter

Meddelanden som skickas som del i en transaktion måste ange en partitionsnyckel. Nyckeln kan vara en av följande egenskaper: [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)eller [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Alla meddelanden som skickas som en del av samma transaktion måste ange samma partitionsnyckel. Om du försöker skicka ett meddelande utan en partitionsnyckel i en transaktion returnerar Service Bus ett ogiltigt åtgärdsundantag. Om du försöker skicka flera meddelanden inom samma transaktion som har olika partitionsnycklar returnerar Service Bus ett ogiltigt åtgärdsundantag. Ett exempel:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

Om någon av de egenskaper som fungerar som en partitionsnyckel är inställda, fäster Service Bus meddelandet till en viss partition. Detta beror på om en transaktion används eller inte. Vi rekommenderar att du inte anger en partitionsnyckel om det inte är nödvändigt.

## <a name="using-sessions-with-partitioned-entities"></a>Använda sessioner med partitionerade entiteter

Om du vill skicka ett transaktionsmeddelande till ett sessionsmedvetna ämne eller en sessionskö måste meddelandet ha egenskapen [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) inställt. Om egenskapen [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) också anges måste den vara identisk med egenskapen [SessionId.](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Om de skiljer sig åt returnerar Service Bus ett ogiltigt åtgärdsundantag.

Till skillnad från vanliga (icke-partitionerade) köer eller ämnen är det inte möjligt att använda en enda transaktion för att skicka flera meddelanden till olika sessioner. Om ett försök görs returnerar Service Bus ett ogiltigt åtgärdsundantag. Ett exempel:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatisk vidarebefordran av meddelanden med partitionerade entiteter

Service Bus stöder automatisk vidarebefordran av meddelanden från, till eller mellan partitionerade entiteter. Om du vill aktivera automatisk vidarebefordran av meddelanden anger du egenskapen [QueueDescription.ForwardTo][QueueDescription.ForwardTo] i källkön eller prenumerationen. Om meddelandet anger en partitionsnyckel ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)eller [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)) används den partitionsnyckeln för målentiteten.

## <a name="considerations-and-guidelines"></a>Överväganden och riktlinjer
* **Funktioner med hög konsekvens**: Om en entitet använder funktioner som sessioner, dubblettidentifiering eller explicit kontroll av partitioneringsnyckeln dirigeras meddelandeåtgärderna alltid till specifik partition. Om någon av partitionerna upplever hög trafik eller det underliggande arkivet är felfritt, misslyckas dessa åtgärder och tillgängligheten minskas. Sammantaget är konsekvensen fortfarande mycket högre än icke-partitionerade enheter. Endast en delmängd av trafiken har problem, i motsats till all trafik. Mer information finns i den här [diskussionen om tillgänglighet och konsekvens](../event-hubs/event-hubs-availability-and-consistency.md).
* **Hantering**: Åtgärder som Skapa, Uppdatera och Ta bort måste utföras på alla partitioner för entiteten. Om någon partition är fel, kan det resultera i fel för dessa åtgärder. För åtgärden Hämta måste information som meddelandeantal aggregeras från alla partitioner. Om någon partition är felfritt rapporteras entitetstillgänglighetsstatusen som begränsad.
* **Meddelandescenarier med låg volym**: För sådana scenarier, särskilt när du använder HTTP-protokollet, kan du behöva utföra flera mottagningsåtgärder för att hämta alla meddelanden. För mottagningsbegäranden utför klientdelen ett mottagningssvar på alla partitioner och cachelagrar alla mottagna svar. En efterföljande begäran om att få samma anslutning skulle dra nytta av denna cachelagring och ta emot svarstider kommer att vara lägre. Men om du har flera anslutningar eller använder HTTP upprättar den en ny anslutning för varje begäran. Det finns därför ingen garanti för att det skulle landa på samma nod. Om alla befintliga meddelanden är låsta och cachelagrade i en annan frontsida returnerar mottagningsåtgärden **null**. Meddelanden upphör så småningom att gälla och du kan ta emot dem igen. HTTP keep-alive rekommenderas. När du använder partitionering i scenarier med låg volym kan det ta längre tid än förväntat att ta emot åtgärder. Därför rekommenderar vi att du inte använder partitionering i dessa scenarier. Ta bort alla befintliga partitionerade entiteter och återskapa dem med partitionering inaktiverade för att förbättra prestanda.
* **Bläddra/kika meddelanden:** Endast tillgängligt i det äldre [WindowsAzure.ServiceBus-biblioteket.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) returnerar inte alltid antalet meddelanden som anges i egenskapen [MessageCount.](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) Det finns två vanliga orsaker till detta beteende. En orsak är att den aggregerade storleken på samlingen av meddelanden överstiger den maximala storleken på 256 KB. En annan orsak är att om kön eller ämnet har [egenskapen EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) inställd på **true**kanske en partition inte har tillräckligt med meddelanden för att slutföra det begärda antalet meddelanden. I allmänhet, om ett program vill ta emot ett visst antal meddelanden, bör det ringa [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) upprepade gånger tills det blir det antalet meddelanden, eller det finns inga fler meddelanden att kika. Mer information, inklusive kodexempel, finns i dokumentationen [queueclient.peekbatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) eller [subscriptionclient.peekbatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) API.

## <a name="latest-added-features"></a>Senaste tillagda funktioner

* Lägga till eller ta bort regeln stöds nu med partitionerade entiteter. Dessa åtgärder stöds inte under transaktioner, skiljer sig från icke-partitionerade entiteter. 
* AMQP stöds nu för att skicka och ta emot meddelanden till och från en partitionerad entitet.
* AMQP stöds nu för följande åtgärder: [Batch Skicka](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), Batch [ta emot](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), Ta emot [efter sekvensnummer](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [Peek](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [Förnya lås](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [Schemalagt](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync) [meddelande](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), Lägg till regel , Ta [bort regel](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Sessions förnya lås,](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock) [Ange sessionstillstånd](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [Hämta sessionstillstånd](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)och [Räkna upp sessioner](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions). [Add Rule](/dotnet/api/microsoft.azure.servicebus.ruledescription)

## <a name="partitioned-entities-limitations"></a>Begränsningar av partitionerade enheter

För närvarande innebär Service Bus följande begränsningar för partitionerade köer och ämnen:

* Partitionerade köer och ämnen stöds inte på Premium-meddelandenivån. Sessioner stöds på den främsta nivån med sessionid. 
* Partitionerade köer och ämnen stöder inte att skicka meddelanden som tillhör olika sessioner i en enda transaktion.
* Service Bus tillåter för närvarande upp till 100 partitionerade köer och ämnen per namnområde. Varje partitionerad kö eller ämne räknas mot kvoten på 10 000 entiteter per namnområde (gäller inte premiumnivå).

## <a name="next-steps"></a>Nästa steg

Läs om de centrala begreppen i AMQP 1.0 meddelandespecifikationen i [AMQP 1.0-protokollguiden](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
