---
title: Azure Service Bus messaging – java Message Service-entiteter (för hands version)
description: Den här artikeln innehåller en översikt över Azure Service Bus meddelande enheter som är tillgängliga via Java Message Service API.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 1a7fe3d6355146ccf0fce50266a6f3b8da5231b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87801801"
---
# <a name="java-message-service-jms-20-entities-preview"></a>JMS (Java Message Service) 2,0 entiteter (för hands version)

Klient program som ansluter till Azure Service Bus Premium och använder [Azure Service Bus JMS-biblioteket](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) kan dra nytta av nedanstående entiteter.

## <a name="queues"></a>Köer

Köer i JMS är semantiskt jämförbara med traditionella [Service Bus köer](service-bus-queues-topics-subscriptions.md#queues).

Om du vill skapa en kö använder du metoderna nedan i `JMSContext` klassen-

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Ämnen

Ämnen i JMS är semantiskt jämförbara med traditionella [Service Bus ämnen](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions).

Om du vill skapa ett ämne använder du metoderna nedan i `JMSContext` klassen-

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Tillfälliga köer

När ett klient program kräver en tillfällig entitet som finns för programmets livs längd kan den använda tillfälliga köer. Dessa används i [fråge svars](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) mönstret.

Om du vill skapa en tillfällig kö använder du metoderna nedan i `JMSContext` klassen-

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Tillfälliga ämnen

Precis som temporära köer finns det temporära ämnen för att aktivera publicera/prenumerera via en tillfällig entitet som finns för programmets livs längd.

Om du vill skapa ett tillfälligt ämne använder du metoderna nedan i `JMSContext` klassen-

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>JMS-prenumerationer (Java Message Service)

Dessa är semantiskt likartade med [prenumerationerna](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (dvs. finns på ett ämne och aktiverar publicerings-och prenumerera-semantik), förklarar Java Message Service-specifikationen begreppen **delade**, icke **delade**, **varaktiga** och **icke-varaktiga** attribut för en viss prenumeration.

> [!NOTE]
> Prenumerationerna nedan är tillgängliga i Azure Service Bus Premium-nivån för för hands versionen av klient program som ansluter till Azure Service Bus med hjälp av [Azure Service Bus JMS-biblioteket](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> För den allmänt tillgängliga för hands versionen kan du inte skapa dessa prenumerationer med hjälp av Azure Portal.
>

### <a name="shared-durable-subscriptions"></a>Delade varaktiga prenumerationer

En delad varaktig prenumeration används när alla meddelanden som publicerats på ett ämne tas emot och bearbetas av ett program, oavsett om programmet aktivt konsumeras från prenumerationen hela tiden.

Eftersom det här är en delad prenumeration kan alla program som autentiseras att ta emot från Service Bus tas emot från prenumerationen.

Om du vill skapa en delad varaktig prenumeration kan du använda nedanstående metoder i `JMSContext` klassen-

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

Den delade varaktiga prenumerationen fortsätter att finnas om den inte tas bort med hjälp av- `unsubscribe` metoden i `JMSContext` klassen.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Delade varaktiga prenumerationer

Precis som en delad beständig prenumeration används en resurs som inte delats ut när alla meddelanden som publiceras i ett ämne tas emot och bearbetas av ett program, oavsett om programmet aktivt konsumeras från prenumerationen.

Eftersom det här är en prenumeration som inte är delad, kan endast det program som skapade prenumerationen ta emot från den.

Om du vill skapa en ej delad beständig prenumeration kan du använda nedanstående metoder från `JMSContext` klass- 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal`Funktionen stöds för närvarande inte och ignoreras därför.
>

Den ej delade varaktiga prenumerationen fortsätter att finnas om den inte tas bort med hjälp av- `unsubscribe` metoden i `JMSContext` klassen.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Delade icke-varaktiga prenumerationer

En delad icke-beständig prenumeration används när flera klient program behöver ta emot och bearbeta meddelanden från en enda prenumeration, bara tills de aktivt konsumeras/tas emot från den.

Eftersom prenumerationen inte är beständig är den inte beständig. Meddelanden tas inte emot av den här prenumerationen när det inte finns några aktiva konsumenter på den.

Skapa en delad icke-beständig prenumeration genom att skapa en `JmsConsumer` som visas i nedanstående metoder från `JMSContext` klassen-

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

Den delade icke-varaktiga prenumerationen fortsätter att finnas tills det finns aktiva konsumenter som tar emot dem.

### <a name="unshared-non-durable-subscriptions"></a>Ej delade icke-varaktiga prenumerationer

En icke-delad icke-beständig prenumeration används när klient programmet måste ta emot och bearbeta meddelanden från en prenumeration, tills det aktivt tar emot dem. Det går bara att ha en konsument i den här prenumerationen, d.v.s. den klient som skapade prenumerationen.

Eftersom prenumerationen inte är beständig är den inte beständig. Meddelanden tas inte emot av den här prenumerationen när det inte finns någon aktiv konsument.

Skapa en icke-varaktig prenumeration genom att skapa en `JMSConsumer` som visas i nedanstående metoder från `JMSContext` klassen-

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal`Funktionen stöds för närvarande inte och ignoreras därför.
>

Den ej delade icke-varaktiga prenumerationen fortsätter att finnas tills det finns en aktiv konsument som tar emot den.

### <a name="message-selectors"></a>Meddelande väljare

Precis som **filter och åtgärder** finns för vanliga Service Bus prenumerationer finns **meddelande väljare** för JMS-prenumerationer.

Meddelande väljare kan ställas in på var och en av JMS-prenumerationerna och finns som ett filter villkor för egenskaperna för meddelande rubriken. Endast meddelanden med rubrik egenskaper som matchar uttrycket för meddelande väljaren levereras. Värdet null eller en tom sträng anger att det inte finns någon meddelande väljare för JMS-prenumerationen/konsumenten.

## <a name="next-steps"></a>Nästa steg

Mer information och exempel på hur du använder Service Bus meddelanden finns i följande avancerade avsnitt:

* [Översikt över Service Bus meddelande hantering](service-bus-messaging-overview.md)
* [Använd API för Java Message Service 2,0 med Azure Service Bus Premium (för hands version)](how-to-use-java-message-service-20.md)



