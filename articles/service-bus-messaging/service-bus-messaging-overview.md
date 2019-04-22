---
title: Översikt över Azure Service Bus-meddelanden | Microsoft Docs
description: Beskrivning av Service Bus-meddelanden
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: overview
ms.date: 09/22/2018
ms.custom: mvc
ms.author: aschhab
ms.openlocfilehash: dd73fd8105de2a5fd24ffa8ad5db85da5e52228c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58918849"
---
# <a name="what-is-azure-service-bus"></a>Vad är Azure Service Bus?

Microsoft Azure Service Bus är en fullständigt hanterad [integrations](https://azure.com/integration)meddelandekoordinator för företag. Service Bus används oftast att frikoppla program och tjänster från varandra och är en tillförlitlig och säker plattform för asynkrona data och överföring av tillstånd. Data överförs mellan olika program och tjänster med *meddelanden*. Meddelanden är i binärt format som kan innehålla JSON, XML eller endast text. 

Några vanliga scenarier för meddelanden är:

* Meddelanden: Överför affärsdata, till exempel försäljning eller inköpsordrar, journaler eller ändringar i inventarier.
* Frikoppla program: förbättra tillförlitligheten och skalbarheten för program och tjänster (klienten tjänsten behöver inte vara online samtidigt).
* Ämnen och prenumerationer: aktivera 1:*n* relationer mellan utgivare och prenumeranter.
* Meddelandesessioner: Implementera arbetsflöden som kräver meddelandebeställning eller uppskjutning av meddelanden.

## <a name="namespaces"></a>Namnområden

Ett namnområde är en omfångscontainer för alla meddelandekomponenter. Flera köer och ämnen kan finnas i ett enda namnområde och namnområden fungerar ofta som programcontainer.

## <a name="queues"></a>Köer

Meddelanden skickas till och tas emot från *köer*. Med köer kan du lagra meddelanden tills det mottagande programmet kan ta emot och bearbeta dem.

![Kö](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Meddelanden i köer sorteras och tidsstämplad anländer. När de har godkänts lagras meddelanden på ett säkert sätt i redundant lagring. Meddelanden levereras i *pull* läge, som levererar meddelanden på begäran.

## <a name="topics"></a>Ämnen

Du kan också använda *ämnen* för att skicka och ta emot meddelanden. Medan en kö oftast används för kommunikation från punkt till punkt är ämnen användbara i scenarier med publicering/prenumeration.

![Avsnitt](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Ämnen kan ha flera oberoende prenumerationer. En prenumerant på ett ämne får en kopia av varje meddelande. Prenumerationer är namngivna enheter som skapas varaktigt men kan eventuellt utgå eller tas bort automatiskt.

I vissa fall vill du kanske inte att individuella prenumerationer ska ta emot alla ämnen som skickas till ett ämne. Om så är fallet bör du använda [regler och filter](topic-filters.md) för att definiera villkor som utlöser valfria [åtgärder](topic-filters.md#actions), filtrera angivna meddelanden och ange eller ändra egenskaperna för meddelanden.

## <a name="advanced-features"></a>Avancerade funktioner

Service Bus har också avancerade funktioner som hjälper dig att lösa problem med mer komplexa meddelanden. I följande avsnitt beskrivs dessa nyckelfunktioner:

### <a name="message-sessions"></a>Meddelandesessioner

Använda sessioner för att använda en först in-, först ut-garanti (FIFO) i Service Bus. [Meddelandesessioner](message-sessions.md) aktiverar gemensamma och organiserad hantering av frigjorda sekvenser av relaterade meddelanden. 

### <a name="auto-forwarding"></a>Automatisk vidarebefordring

Med funktionen [automatisk vidarebefordring](service-bus-auto-forwarding.md) kan du koppla en kö eller en prenumeration på en annan kö eller ett ämne som ingår i samma namnområde. När automatisk vidarebefordring är aktiverat tar Service Bus automatiskt bort meddelanden som har placerats i den första kön eller prenumerationen (källan) och placerar dem i den andra kön eller ämnet (målet).

### <a name="dead-lettering"></a>Olevererade brev

Service Bus stöder en [kö med olevererade brev](service-bus-dead-letter-queues.md) (DQL) för meddelanden som inte kan levereras till alla mottagare eller meddelanden som inte kan bearbetas. Du kan ta bort meddelanden från DQL och inspektera dem.

### <a name="scheduled-delivery"></a>Schemalagd leverans

Du kan skicka meddelanden till en kö eller ett ämne [för fördröjd bearbetning](message-sequencing.md#scheduled-messages), till exempel för att schemalägga ett jobb som ska bli tillgänglig för bearbetning av ett system vid en viss tidpunkt.

### <a name="message-deferral"></a>Skjut upp meddelanden

När en kö- eller prenumerationsklient tar emot ett meddelande som den är villig att bearbeta men det för tillfället inte är möjligt på grund av särskilda omständigheter i programmet, kan enheten [skjuta upp hämtningen av meddelandet ](message-deferral.md) till ett senare tillfälle. Meddelandet finns kvar i kön eller prenumerationen, men det ställs åt sidan.

### <a name="batching"></a>Batchbearbetning

Med [klientsidans batchbearbetning](service-bus-performance-improvements.md#client-side-batching) förhindras en kö- eller ämnesklient från att skicka ett meddelande under en viss tidsperiod. Om klienten skickar ytterligare meddelanden under den här tiden överförs dessa meddelanden i en enskild batch. 

### <a name="transactions"></a>Transaktioner

En [transaktion](service-bus-transactions.md) grupperar två eller flera åtgärder tillsammans i en körning. Service Bus stöder grupperingsåtgärder mot en enskild meddelandeenhet (kö, ämne, prenumeration) inom en transaktion.

### <a name="filtering-and-actions"></a>Filtrering och åtgärder

Prenumeranter kan definiera vilka meddelanden som de vill ta emot från ett ämne. Dessa meddelanden anges i form av en eller flera [namngivna prenumerationsregler](topic-filters.md). Prenumerationen skapar en kopia av meddelandet som får kommenteras på olika sätt för varje matchande regel för varje matchande regelvillkor.

### <a name="auto-delete-on-idle"></a>Automatisk borttagning vid inaktivitet

Med [automatisk borttagning vid inaktivitet](/dotnet/api/microsoft.servicebus.messaging.queuedescription.autodeleteonidle) kan du ange ett intervall för inaktivitet varefter kön tas bort automatiskt. Minimilängden är 5 minuter.

### <a name="duplicate-detection"></a>Dubblettidentifiering

Om ett fel som leder till att klienten är osäker på resultatet av en sändningsåtgärd kan [dubblettidentifiering](duplicate-detection.md) eliminera osäkerheten genom att låta avsändaren skicka om samma meddelande. Kön eller ämnet tar bort eventuella dubbletter.

### <a name="sas-rbac-and-managed-identities-for-azure-resources"></a>SAS, RBAC och hanterade identiteter för Azure-resurser

Service Bus stöder säkerhetsprotokoll som [signaturer för delad åtkomst](service-bus-sas.md) (SAS), [rollbaserad åtkomstkontroll](service-bus-role-based-access-control.md) (RBAC) och [hanterade identiteter för Azure-resurser](service-bus-managed-service-identity.md).

### <a name="geo-disaster-recovery"></a>Geohaveriberedskap

När Azure-regioner eller datacenter drabbas av driftstopp låter [geohaveriberedskap](service-bus-geo-dr.md) databearbetningen fortsätta i en annan region eller datacenter.

### <a name="security"></a>Säkerhet

Service Bus stöder standardprotokoll [AMQP 1.0](service-bus-amqp-overview.md) och [HTTP-REST](/rest/api/servicebus/).

## <a name="client-libraries"></a>Klientbibliotek

Service Bus stöder klientbibliotek för [.NET](https://github.com/Azure/azure-service-bus-dotnet/tree/master), [Java](https://github.com/Azure/azure-service-bus-java/tree/master), [JMS](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client).

## <a name="integration"></a>Integrering

Service Bus är helt integrerad med följande Azure-tjänster:

- [Event Grid](https://azure.microsoft.com/services/event-grid/) 
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 
- [Funktioner](https://azure.microsoft.com/services/functions/) 
- [Dynamics 365](https://dynamics.microsoft.com)
- [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)
 
## <a name="next-steps"></a>Nästa steg

Om du vill komma igång med Service Bus-meddelanden, kan du läsa följande artiklar:

* [Jämföra Azure-meddelandetjänster](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json)
* Lär dig mer om Azure Service Bus [Standard och Premium](https://azure.microsoft.com/pricing/details/service-bus/)-nivåer och deras prisnivå
* [Prestanda och fördröjning av Azure Service Bus Premium-nivån](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722)
* Prova snabbstart i [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md) eller [JMS](service-bus-java-how-to-use-jms-api-amqp.md)
