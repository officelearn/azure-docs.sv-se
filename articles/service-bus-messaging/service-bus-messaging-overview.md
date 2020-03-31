---
title: Översikt över Azure Service Bus-meddelanden | Microsoft Docs
description: Den här artikeln ger en översikt på hög nivå över Azure Service Bus, en fullständigt hanterad meddelandemäklare för företagsintegrering.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 11/04/2019
ms.custom: mvc
ms.author: aschhab
ms.openlocfilehash: 49a54491c36ef29209d1a53094cc5baf57057557
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240634"
---
# <a name="what-is-azure-service-bus"></a>Vad är Azure Service Bus?

Microsoft Azure Service Bus är en helt hanterad integrerad meddelandekoordinator för företag. Service Bus kan frikoppla program och tjänster. Service Bus erbjuder en tillförlitlig och säker plattform för asynkron överföring av data och tillstånd.

Data överförs mellan olika program och tjänster med *meddelanden*. Ett meddelande är i binärt format och kan innehålla JSON, XML eller bara text. Mer information finns i [Integrationstjänster](https://azure.com/integration).

Några vanliga scenarier för meddelanden är:

* *Meddelanden*. Överför affärsdata, till exempel försäljnings- eller inköpsorder, journaler eller lagerförflyttningar.
* *Frikoppla applikationer*. Förbättra tillförlitligheten och skalbarheten för program och tjänster. Klient och service behöver inte vara online samtidigt.
* *Ämnen och prenumerationer*. Aktivera 1:*n* relationer mellan utgivare och prenumeranter.
* *Meddelandesessioner*. Implementera arbetsflöden som kräver meddelandebeställning eller meddelandeuppskov.

## <a name="namespaces"></a>Namnområden

Ett namnområde är en behållare för alla meddelandekomponenter. Flera köer och ämnen kan finnas i ett enda namnområde, och namnområden fungerar ofta som programbehållare.

## <a name="queues"></a>Köer

Meddelanden skickas till och tas emot från *köer*. Köer lagrar meddelanden tills det mottagande programmet är tillgängligt för att ta emot och bearbeta dem.

![Kö](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Meddelanden i köer beställs och tidsstämplade vid ankomsten. När de har godkänts lagras meddelanden på ett säkert sätt i redundant lagring. Meddelanden levereras i *pull-läge* och levererar bara meddelanden när de begärs.

## <a name="topics"></a>Ämnen

Du kan också använda *ämnen* för att skicka och ta emot meddelanden. Medan en kö oftast används för kommunikation från punkt till punkt är ämnen användbara i scenarier med publicering/prenumeration.

![Hjälpavsnitt](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Ämnen kan ha flera oberoende prenumerationer. En prenumerant på ett ämne får en kopia av varje meddelande. Prenumerationer heter entiteter. Prenumerationer kvarstår, men kan upphöra att gälla eller ta bort automatiskt.

Du kanske inte vill att enskilda prenumerationer ska ta emot alla meddelanden som skickas till ett ämne. Om så är fallet kan du använda *regler* och *filter* för att definiera villkor som utlöser valfria *åtgärder*. Du kan filtrera angivna meddelanden och ange eller ändra meddelandeegenskaper. Mer information finns i [Ämnesfilter och åtgärder](topic-filters.md).

## <a name="advanced-features"></a>Avancerade funktioner

Service Bus innehåller avancerade funktioner som gör att du kan lösa mer komplexa meddelandeproblem. I följande avsnitt beskrivs flera av dessa funktioner.

### <a name="message-sessions"></a>Meddelandesessioner

Om du vill skapa en första in- och ut-garanti (FIFO) i Service Bus använder du sessioner. Meddelandesessioner aktiverar gemensamma och organiserad hantering av frigjorda sekvenser av relaterade meddelanden. Mer information finns [i Meddelandesessioner: först in, först ut (FIFO)](message-sessions.md).

### <a name="autoforwarding"></a>Automatisk vidarebefordran

Funktionen för automatisk vidarebefordran kedjorar en kö eller prenumeration på en annan kö eller ett annat ämne. De måste vara en del av samma namnområde. Med automatisk vidarebefordran tar Service Bus automatiskt bort meddelanden från en kö eller prenumeration och placerar dem i en annan kö eller ett annat ämne. Mer information finns i [Chaining Service Bus-entiteter med automatisk vidarebefordran](service-bus-auto-forwarding.md).

### <a name="dead-letter-queue"></a>Kö för obeställbara meddelanden

Service Bus stöder en kö för obeställbara meddelanden (DLQ). En DLQ innehåller meddelanden som inte kan levereras till någon mottagare. Den innehåller meddelanden som inte kan bearbetas. Med Service Bus kan du ta bort meddelanden från DLQ och inspektera dem. Mer information finns i [Översikt över köer för obeställbara meddelanden i Service Bus](service-bus-dead-letter-queues.md).

### <a name="scheduled-delivery"></a>Schemalagd leverans

Du kan skicka meddelanden till en kö eller ett ämne för fördröjd bearbetning. Du kan schemalägga ett jobb så att det blir tillgängligt för bearbetning av ett system vid en viss tidpunkt. Mer information finns i [Schemalagda meddelanden](message-sequencing.md#scheduled-messages).

### <a name="message-deferral"></a>Skjut upp meddelanden

En kö- eller prenumerationsklient kan skjuta upp hämtningen av ett meddelande tills ett senare tillfälle. Detta uppskov kan bero på särskilda omständigheter i ansökan. Meddelandet finns kvar i kön eller prenumerationen, men det är avsätts. Mer information finns i [Meddelandeuppskov](message-deferral.md).

### <a name="batching"></a>Batchbearbetning

Med klientsidans batchbearbetning förhindras en kö- eller ämnesklient från att skicka ett meddelande under en viss tidsperiod. Om klienten skickar ytterligare meddelanden under den här tiden överförs dessa meddelanden i en enskild batch. Mer information finns i [Batchbearbetning på klientsidan](service-bus-performance-improvements.md#client-side-batching).

### <a name="transactions"></a>Transaktioner

En transaktion grupperar två eller flera operationer tillsammans i ett *körningsomfång*. Service Bus stöder grupperingsåtgärder mot en enda meddelandeentitet inom ramen för en enskild transaktion. En meddelandeentitet kan vara en kö, ett ämne eller en prenumeration. Mer information finns i [Översikt över bearbetning av Service Bus-transaktioner](service-bus-transactions.md).

### <a name="filtering-and-actions"></a>Filtrering och åtgärder

Prenumeranter kan definiera vilka meddelanden som de vill ta emot från ett ämne. Dessa meddelanden anges i form av en eller flera namngivna prenumerationsregler. För varje matchningsregelvillkor skapar prenumerationen en kopia av meddelandet, som kan kommenteras på olika sätt för varje matchningsregel. Mer information finns i [Ämnesfilter och åtgärder](topic-filters.md).

### <a name="autodelete-on-idle"></a>Autodelete på tomgång

Med automatisk inaktivitet på inaktiv kan du ange ett inaktivt intervall efter vilket en kö tas bort automatiskt. Minimilängden är 5 minuter. Mer information finns i [egenskapen QueueDescription.AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle).

### <a name="duplicate-detection"></a>Dubblettidentifiering

Ett fel kan orsaka att klienten tvivlar på resultatet av en skicka-åtgärd. Dubblettidentifiering gör det möjligt för avsändaren att skicka samma meddelande igen. Ett annat alternativ är att kön eller avsnittet ska ignorera dubblettkopior. Mer information finns i [Duplicera identifiering](duplicate-detection.md).

### <a name="security-protocols"></a>Säkerhetsprotokoll
<a name="sas-rbac-and-managed-identities-for-azure-resources"></a>

Service Bus stöder säkerhetsprotokoll som [signaturer för delad åtkomst](service-bus-sas.md) (SAS), [rollbaserad åtkomstkontroll](authenticate-application.md) (RBAC) och [hanterade identiteter för Azure-resurser](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Geohaveriberedskap

När Azure-regioner eller datacenter drabbas av driftstopp låter geohaveriberedskap databearbetningen fortsätta i en annan region eller datacenter. Mer information finns i [Azure Service Bus Geo-disaster recovery](service-bus-geo-dr.md).

### <a name="security"></a>Säkerhet

Service Bus stöder standardprotokoll [AMQP 1.0](service-bus-amqp-overview.md) och [HTTP-REST](/rest/api/servicebus/).

## <a name="client-libraries"></a>Klientbibliotek

Service Bus stöder klientbibliotek för [.NET,](https://github.com/Azure/azure-service-bus-dotnet/tree/master) [Java](https://github.com/Azure/azure-service-bus-java/tree/master)och [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Integrering

Service Bus är helt integrerad med följande Azure-tjänster:

* [Event Grid](https://azure.microsoft.com/services/event-grid/)
* [Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Dynamics 365](https://dynamics.microsoft.com)
* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)

## <a name="next-steps"></a>Nästa steg

Om du vill komma igång med Service Bus-meddelanden, kan du läsa följande artiklar:

* Information om hur du jämför Azure-meddelandetjänster finns [i Jämförelse av tjänster](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json).
* Prova snabbstarterna för [.NET,](service-bus-dotnet-get-started-with-queues.md) [Java](service-bus-java-how-to-use-queues.md)eller [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Om du vill hantera Service Bus-resurser finns i [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases).
* Mer information om standard- och premiumnivåer och priser finns i [Service Bus-priser](https://azure.microsoft.com/pricing/details/service-bus/).
* Mer information om prestanda och svarstid för Premium-nivån finns i [Premium Messaging](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722).
