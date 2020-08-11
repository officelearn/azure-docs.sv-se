---
title: 'Snabb start: använda Azure Service Bus ämnen och prenumerationer med python'
description: Den här artikeln visar hur du skapar ett Azure Service Bus ämne, prenumeration, skickar meddelanden till ett ämne och tar emot meddelanden från prenumerationen.
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: f6d1b25cb502b8cb208ba5b59c91667e03c77778
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88064391"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Snabb start: använda Service Bus ämnen och prenumerationer med python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Den här artikeln beskriver hur du använder python med Azure Service Bus ämnen och prenumerationer. I exemplen används [Azure python SDK][Azure Python package] -paketet för att: 

- Skapa ämnen och prenumerationer på ämnen
- Skapa prenumerations filter och regler
- Skicka meddelanden till ämnen 
- Ta emot meddelanden från prenumerationer
- Ta bort ämnen och prenumerationer

## <a name="prerequisites"></a>Förutsättningar
- En Azure-prenumeration. Du kan aktivera dina [förmåner för Visual Studio eller MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ett Service Bus namn område som skapats genom att följa stegen i [snabb starten: använd Azure Portal för att skapa ett Service Bus ämne och prenumerationer](service-bus-quickstart-topics-subscriptions-portal.md). Kopiera namn områdets namn, namnet på den delade åtkomst nyckeln och värdet för primär nyckel från skärmen **principer för delad åtkomst** för senare användning i den här snabb starten. 
- Python 3.4 x eller senare, med [Azure python SDK][Azure Python package] -paketet installerat. Mer information finns i [installations guiden för python](/azure/developer/python/azure-sdk-install).

## <a name="create-a-servicebusservice-object"></a>Skapa ett ServiceBusService-objekt

Med ett **ServiceBusService** -objekt kan du arbeta med ämnen och prenumerationer på ämnen. Lägg till följande rad längst upp i python-filen för att program mässigt komma åt Service Bus:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Lägg till följande kod för att skapa ett **ServiceBusService** -objekt. Ersätt `<namespace>` , `<sharedaccesskeyname>` , och `<sharedaccesskeyvalue>` med namnet på Service Bus namn området, signaturen för signatur för delad åtkomst (SAS) och primär nyckel värde. Du hittar de här värdena under **principer för delad åtkomst** i Service Bus namn området i [Azure Portal][Azure portal].

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Skapa ett ämne

I följande kod används `create_topic` metoden för att skapa ett Service Bus avsnitt `mytopic` som heter, med standardinställningar:

```python
bus_service.create_topic('mytopic')
```

Du kan använda ämnes alternativ om du vill åsidosätta standard ämnes inställningarna, till exempel Time to Live (TTL) eller maximal ämnes storlek. I följande exempel skapas ett ämne `mytopic` med namnet med en maximal ämnes storlek på 5 GB och standard-TTL för meddelande i en minut:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Skapa prenumerationer

Du kan också använda **ServiceBusService** -objektet för att skapa prenumerationer på ämnen. En prenumeration kan ha ett filter för att begränsa meddelande uppsättningen som levereras till den virtuella kön. Om du inte anger ett filter använder nya prenumerationer standard filtret **MatchAll** , som placerar alla meddelanden som publiceras i avsnittet i prenumerationens virtuella kö. I följande exempel skapas en prenumeration med `mytopic` namnet `AllMessages` som använder **MatchAll** -filtret:

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Använda filter med prenumerationer

Använd `create_rule` metoden för **ServiceBusService** -objektet för att filtrera meddelandena som visas i en prenumeration. Du kan ange regler när du skapar prenumerationen eller lägga till regler i befintliga prenumerationer.

Den mest flexibla typen av filter är en **SqlFilter**, som använder en delmängd av SQL-92. SQL-filter körs baserat på egenskaperna för meddelanden som publiceras till ämnet. Mer information om uttrycken som du kan använda med ett SQL-filter finns i syntaxen [SqlFilter. SqlExpression][SqlFilter.SqlExpression] .

Eftersom standard filtret för **MatchAll** automatiskt används för alla nya prenumerationer måste du ta bort det från prenumerationer som du vill filtrera eller så åsidosätter **MatchAll** alla andra filter som du anger. Du kan ta bort standard regeln med hjälp av `delete_rule` metoden för **ServiceBusService** -objektet.

I följande exempel skapas en prenumeration med `mytopic` namnet `HighMessages` med en **SqlFilter** -regel med namnet `HighMessageFilter` . `HighMessageFilter`Regeln väljer endast meddelanden med en anpassad egenskap som är `messageposition` större än 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

I följande exempel skapas en prenumeration med `mytopic` namnet `LowMessages` med en **SqlFilter** -regel med namnet `LowMessageFilter` . `LowMessageFilter`Regeln väljer endast meddelanden med en `messageposition` egenskap som är mindre än eller lika med 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Med `AllMessages` , `HighMessages` och `LowMessages` allt i praktiken, levereras alltid meddelanden som skickas till `mytopic` till mottagare av `AllMessages` prenumerationen. Meddelanden levereras också selektivt till `HighMessages` -eller `LowMessages` -prenumerationen, beroende på meddelandets `messageposition` egenskaps värde. 

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne

Program använder `send_topic_message` metoden för **ServiceBusService** -objektet för att skicka meddelanden till ett Service Bus ämne.

I följande exempel skickas fem test meddelanden till `mytopic` ämnet. Värdet för den anpassade `messageposition` egenskapen beror på loopens iteration och avgör vilka prenumerationer som tar emot meddelandena. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Storleks gränser och kvoter för meddelanden

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som ett ämne kan innehålla, men det finns ett tak för den totala storleken på de meddelanden som ämnet innehåller. Du kan definiera ämnes storlek vid skapande tillfället med en övre gräns på 5 GB. 

Mer information om kvoter finns i [Service Bus kvoter][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration

Program använder `receive_subscription_message` metoden på **ServiceBusService** -objektet för att ta emot meddelanden från en prenumeration. Följande exempel tar emot meddelanden från `LowMessages` prenumerationen och tar bort dem när de läses:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Den valfria `peek_lock` parametern för `receive_subscription_message` avgör om Service Bus tar bort meddelanden från prenumerationen när de läses. Standard läget för att ta emot meddelanden är *PeekLock*, eller `peek_lock` anges till **True**, som läser (tittar) och låser meddelanden utan att ta bort dem från prenumerationen. Varje meddelande måste slutföras explicit för att ta bort det från prenumerationen.

Om du vill ta bort meddelanden från prenumerationen när de är lästa kan du ange `peek_lock` parametern till **false**, som i föregående exempel. Att ta bort meddelanden som en del av Receive-åtgärden är den enklaste modellen och fungerar bra om programmet kan tolerera saknade meddelanden om det uppstår ett fel. För att förstå det här beteendet bör du överväga ett scenario där programmet utfärdar en Receive-begäran och sedan kraschar innan det bearbetas. Om meddelandet togs bort när det togs emot när programmet startas om och börjar förbruka meddelanden igen, har det missa det meddelande som togs emot före kraschen.

Om ditt program inte kan tolerera missade meddelanden blir mottagningen en åtgärd i två steg. PeekLock söker efter nästa meddelande som ska förbrukas, låser det för att hindra andra användare från att ta emot det och returnerar det till programmet. När du har bearbetat eller lagrat meddelandet Slutför programmet det andra steget i Receive-processen genom att anropa `complete` metoden i objektet **Message** .  `complete`Metoden markerar meddelandet som förbrukat och tar bort det från prenumerationen.

I följande exempel visas ett scenario för att granska låset:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Hantera program krascher och oläsbara meddelanden

Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagar program inte kan bearbeta ett meddelande av någon anledning, kan det anropa `unlock` metoden i objektet **Message** . Service Bus låser upp meddelandet i prenumerationen och gör det tillgängligt att tas emot igen, antingen av samma eller ett annat användnings program.

Det finns också en tids gräns för meddelanden som är låsta i prenumerationen. Om ett program inte kan bearbeta ett meddelande innan låsnings tids gränsen går ut, till exempel om programmet kraschar, Service Bus låser upp meddelandet automatiskt och gör det tillgängligt för att tas emot igen.

Om ett program kraschar efter att ett meddelande har bearbetats men innan du anropar `complete` -metoden, kommer meddelandet att skickas vidare till programmet när det startas om. Det här beteendet kallas ofta *för bearbetning med minst en gång*. Varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras igen. Om ditt scenario inte kan tolerera dubbla bearbetningar kan du använda meddelandets **messageid** -egenskap, som förblir konstant över leverans försök, för att hantera dubbla meddelande leveranser. 

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer

Använd [Azure Portal][Azure portal] eller metoden för att ta bort ämnen och prenumerationer `delete_topic` . Följande kod tar bort ämnet med namnet `mytopic` :

```python
bus_service.delete_topic('mytopic')
```

Om du tar bort ett ämne tas alla prenumerationer på ämnet bort. Du kan också ta bort prenumerationer oberoende av varandra. Följande kod tar bort prenumerationen som heter `HighMessages` från `mytopic` avsnittet:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Som standard är ämnen och prenumerationer permanenta och finns tills du tar bort dem. Om du vill ta bort prenumerationer automatiskt efter att en viss tids period har gått ut kan du ange [auto_delete_on_idle](/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) parameter för prenumerationen. 

> [!TIP]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Med Service Bus Explorer kan du ansluta till ett Service Bus-namnområde och enkelt administrera meddelande enheter. Verktyget innehåller avancerade funktioner som import/export-funktioner och möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub. 

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna om Service Bus ämnen kan du följa dessa länkar om du vill veta mer:

* [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions]
* Referens för [SqlFilter. SqlExpression][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md