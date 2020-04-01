---
title: 'Snabbstart: Använd ämnen och prenumerationer på Azure Service Bus med Python'
description: Den här artikeln visar hur du skapar ett Azure Service Bus-ämne, prenumeration, skicka meddelanden till ett ämne och ta emot meddelanden från prenumerationen.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 4745d675086f1b07bf7fccf17c14c76e4b18fba2
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478070"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Snabbstart: Använd avsnitt och prenumerationer på Service Bus med Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

I den här artikeln beskrivs hur du använder Python med Azure Service Bus-ämnen och prenumerationer. Exemplen använder [Azure Python SDK-paketet][Azure Python package] för att: 

- Skapa ämnen och prenumerationer på ämnen
- Skapa prenumerationsfilter och regler
- Skicka meddelanden till ämnen 
- Ta emot meddelanden från prenumerationer
- Ta bort ämnen och prenumerationer

## <a name="prerequisites"></a>Krav
- En Azure-prenumeration. Du kan aktivera dina fördelar för [Visual Studio- eller MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ett servicebussnamnområde som skapats genom att följa stegen i [Snabbstart: Använd Azure-portalen för att skapa ett servicebussämne och prenumerationer](service-bus-quickstart-topics-subscriptions-portal.md). Kopiera namnområdesnamnet, namnet på nyckeln för delad åtkomst och primärnyckelvärdet från skärmen Principer för **delad åtkomst** som ska användas senare i den här snabbstarten. 
- Python 3.4x eller högre, med [Azure Python SDK-paketet][Azure Python package] installerat. Mer information finns i [installationsguiden](/azure/developer/python/azure-sdk-install)för Python .

## <a name="create-a-servicebusservice-object"></a>Skapa ett ServiceBusService-objekt

Med ett **ServiceBusService-objekt** kan du arbeta med ämnen och prenumerationer på ämnen. Om du vill komma åt Service Bus programmatiskt lägger du till följande rad högst upp i Python-filen:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Lägg till följande kod för att skapa ett **ServiceBusService-objekt.** Ersätt `<namespace>` `<sharedaccesskeyname>`, `<sharedaccesskeyvalue>` och med namnet Service Bus namn, SAS-nyckelnamn (Shared Access Signature) och primärnyckelvärde. Du hittar dessa värden under Principer för **delad åtkomst** i tjänstbussens namnområde i [Azure-portalen][Azure portal].

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Skapa ett ämne

I följande kod `create_topic` används metoden för att `mytopic`skapa ett Service Bus-ämne som heter , med standardinställningar:

```python
bus_service.create_topic('mytopic')
```

Du kan använda ämnesalternativ för att åsidosätta standardinställningarna för ämne, till exempel meddelandetid att leva (TTL) eller maximal ämnesstorlek. I följande exempel skapas `mytopic` ett ämne med en maximal ämnesstorlek på 5 GB och standardmeddelande TTL på en minut:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Skapa prenumerationer

Du kan också använda **ServiceBusService-objektet** för att skapa prenumerationer på ämnen. En prenumeration kan ha ett filter för att begränsa meddelandeuppsättningen som levereras till den virtuella kön. Om du inte anger något filter använder nya prenumerationer standardfiltret **MatchAll,** som placerar alla meddelanden som publicerats i avsnittet i prenumerationens virtuella kö. I följande exempel skapas `mytopic` `AllMessages` en prenumeration på namngivna som använder **Filtret MatchAll:**

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Använda filter med prenumerationer

Använd `create_rule` metoden för **ServiceBusService-objektet** för att filtrera de meddelanden som visas i en prenumeration. Du kan ange regler när du skapar prenumerationen eller lägger till regler i befintliga prenumerationer.

Den mest flexibla typen av filter är ett **SqlFilter**, som använder en delmängd av SQL-92. SQL-filter fungerar baserat på egenskaperna för meddelanden som publicerats i ämnet. Mer information om de uttryck som du kan använda med ett SQL-filter finns i syntaxen [sqlfilter.sqlexpression.][SqlFilter.SqlExpression]

Eftersom **standardfiltret MatchAll** gäller automatiskt för alla nya prenumerationer måste du ta bort det från prenumerationer som du vill filtrera, annars åsidosätter **MatchAll** alla andra filter som du anger. Du kan ta bort standardregeln med hjälp av `delete_rule` metoden för **ServiceBusService-objektet.**

I följande exempel skapas `mytopic` `HighMessages`en prenumeration på namnet `HighMessageFilter`, med en **SqlFilter-regel** med namnet . Regeln `HighMessageFilter` väljer bara meddelanden med `messageposition` en anpassad egenskap som är större än 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

I följande exempel skapas `mytopic` `LowMessages`en prenumeration på namnet `LowMessageFilter`, med en **SqlFilter-regel** med namnet . Regeln `LowMessageFilter` väljer bara meddelanden med `messageposition` en egenskap som är mindre än eller lika med 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Med `AllMessages` `HighMessages`, `LowMessages` och alla i praktiken `mytopic` levereras meddelanden som skickas `AllMessages` till alltid till mottagare av prenumerationen. Meddelanden levereras också selektivt `HighMessages` `LowMessages` till prenumerationen eller, `messageposition` beroende på meddelandets egenskapsvärde. 

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne

Program använder `send_topic_message` metoden för **ServiceBusService-objektet** för att skicka meddelanden till ett Service Bus-ämne.

I följande exempel skickas fem `mytopic` testmeddelanden till ämnet. Det `messageposition` anpassade egenskapsvärdet beror på hur loopen är hög och avgör vilka prenumerationer som tar emot meddelandena. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Begränsningar och kvoter för meddelandestorlek

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för hur många meddelanden ett ämne kan innehålla, men det finns ett tak för den totala storleken på de meddelanden som ämnet innehåller. Du kan definiera ämnesstorlek vid skapande, med en övre gräns på 5 GB. 

Mer information om kvoter finns i [Service Bus-kvoter][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration

Program använder `receive_subscription_message` metoden på **ServiceBusService-objektet** för att ta emot meddelanden från en prenumeration. I följande exempel tar `LowMessages` du emot meddelanden från prenumerationen och tar bort dem när de läs.:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Den `peek_lock` valfria `receive_subscription_message` parametern för avgör om Service Bus tar bort meddelanden från prenumerationen när de läs.The optional parameter of determines whether Service Bus deletes messages from the subscription as they're read. Standardläget för meddelandemottagande är `peek_lock` *PeekLock*eller inställt på **Sant**, som läser (kikar) och låser meddelanden utan att ta bort dem från prenumerationen. Varje meddelande måste sedan uttryckligen fyllas i för att ta bort det från prenumerationen.

Om du vill ta bort meddelanden från prenumerationen `peek_lock` när de läses kan du ange parametern till **Falskt**, som i föregående exempel. Att ta bort meddelanden som en del av mottagningsåtgärden är den enklaste modellen och fungerar bra om programmet kan tolerera saknade meddelanden om det finns ett fel. Om du vill förstå det här beteendet bör du överväga ett scenario där programmet utfärdar en mottagningsbegäran och sedan kraschar innan den bearbetas. Om meddelandet togs bort när det togs emot, när programmet startas om och börjar konsumera meddelanden igen, har det missat meddelandet som det tog emot före kraschen.

Om ditt program inte kan tolerera missade meddelanden blir mottagningen en tvåstegsåtgärd. PeekLock hittar nästa meddelande som ska förbrukas, låser det för att förhindra att andra konsumenter tar emot det och returnerar det till programmet. När meddelandet har bearbetats eller lagrats slutförs den andra `complete` fasen av mottagningsprocessen genom att anropa metoden på **meddelandeobjektet.**  Metoden `complete` markerar meddelandet som förbrukat och tar bort det från prenumerationen.

I följande exempel visas ett peek lock-scenario:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsliga meddelanden

Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram av någon anledning inte kan `unlock` bearbeta ett meddelande kan det anropa metoden på **meddelandeobjektet.** Service Bus låser upp meddelandet i prenumerationen och gör det tillgängligt för att tas emot igen, antingen av samma eller ett annat tidskrävande program.

Det finns också en timeout för meddelanden som är låsta i prenumerationen. Om ett program inte behandlar ett meddelande innan låstidsgränsen går ut, till exempel om programmet kraschar, låser Service Bus upp meddelandet automatiskt och gör det tillgängligt för att tas emot igen.

Om ett program kraschar efter bearbetning av `complete` ett meddelande men innan du anropar metoden levereras meddelandet till programmet igen när det startas om. Det här beteendet kallas ofta *Minst en gång Bearbetning*. Varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras på nytt. Om ditt scenario inte kan tolerera dubblettbearbetning kan du använda egenskapen **MessageId** för meddelandet, som förblir konstant över leveransförsök, för att hantera dubblettmeddelandeleverans. 

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer

Om du vill ta bort ämnen och `delete_topic` prenumerationer använder du [Azure-portalen][Azure portal] eller metoden. Följande kod tar bort `mytopic`ämnet med namnet :

```python
bus_service.delete_topic('mytopic')
```

Om du tar bort ett ämne tas alla prenumerationer på ämnet bort. Du kan också ta bort prenumerationer oberoende av dem. Följande kod tar bort `HighMessages` prenumerationen `mytopic` som heter från ämnet:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Som standard är ämnen och prenumerationer beständiga och finns tills du tar bort dem. Om du automatiskt vill ta bort prenumerationer efter en viss tidsperiod kan du ange [parametern auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) på prenumerationen. 

> [!TIP]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Med Service Bus Explorer kan du ansluta till ett servicebussnamnområde och enkelt administrera meddelandeentiteter. Verktyget innehåller avancerade funktioner som import-/exportfunktioner och möjligheten att testa ämnen, köer, prenumerationer, relätjänster, meddelandehubbar och händelsehubbar. 

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna i Service Bus-ämnen följer du de här länkarna för att läsa mer:

* [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions]
* [SqlFilter.SqlExpression-referens][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
