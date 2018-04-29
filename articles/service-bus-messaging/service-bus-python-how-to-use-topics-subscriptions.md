---
title: Hur du använder Azure Service Bus-ämnen med Python | Microsoft Docs
description: Lär dig hur du använder Azure Service Bus-ämnen och prenumerationer från Python.
services: service-bus-messaging
documentationcenter: python
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/20/2018
ms.author: sethm
ms.openlocfilehash: 6f262a63de9409d6b355d9783ca958e4715b1ea5
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Hur du använder Service Bus-ämnen och prenumerationer med Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

I den här artikeln beskrivs hur du använder Service Bus-ämnen och -prenumerationer. Exemplen är skrivna i Python och Använd den [Azure Python SDK-paketet][Azure Python package]. Scenarier som tas upp inkluderar **skapa ämnen och prenumerationer**, **skapa prenumerationsfilter**, **skicka meddelanden till ett ämne**, **ta emot meddelanden från en prenumeration**, och **ta bort ämnen och prenumerationer**. Mer information om ämnen och prenumerationer finns i [nästa steg](#next-steps) avsnitt.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

> [!NOTE] 
> Om du behöver installera Python eller [Azure Python-paketet][Azure Python package], finns det [Python installationsguiden](../python-how-to-install.md).

## <a name="create-a-topic"></a>Skapa ett ämne

Den **ServiceBusService** objekt kan du arbeta med ämnen. Lägg till följande kod i den övre delen av Python-fil som du vill komma åt Service Bus:

```python
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Följande kod skapar en **ServiceBusService** objekt. Ersätt `mynamespace`, `sharedaccesskeyname`, och `sharedaccesskey` med det faktiska namnområdet delade signatur åtkomst (SAS) nyckelvärdet namn och nyckel.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Du kan ge värdena för SAS-nyckelnamnet och värdet från den [Azure-portalen][Azure portal].

```python
bus_service.create_topic('mytopic')
```

Den `create_topic` metoden stöder också ytterligare alternativ som gör att du kan åsidosätta standardinställningar för ämnet, till exempel time to live-meddelande eller avsnittet maximala storlek. I följande exempel anger maximal avsnittet storleken till 5 GB och en gång till live (TTL) på en minut:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Skapa prenumerationer

Prenumerationer till avsnitt skapas också med den **ServiceBusService** objekt. Prenumerationer är namngivna och kan ha ett valfritt filter som begränsar uppsättningen av meddelanden som skickas till prenumerationens virtuella kö.

> [!NOTE]
> Prenumerationer är beständiga och fortsätter att existera tills de, eller i avsnittet som de prenumererar, tas bort.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)

**MatchAll**-filtret är det standardfilter som används om inget filter anges när en ny prenumeration skapas. När den **MatchAll** filter används, alla meddelanden som publiceras till ämnet placeras i prenumerationens virtuella kö. I följande exempel skapas en prenumeration med namnet `AllMessages` och använder förvalet **MatchAll** filter.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Skapa prenumerationer med filter

Du kan också definiera filter som gör att du kan ange vilka meddelanden som skickas till ett ämne visas inom en viss ämnesprenumeration.

Den mest flexibla typen av filter som stöds av prenumerationerna är en **SqlFilter**, som implementerar en deluppsättning av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Mer information om uttryck som kan användas med ett SQL-filter finns i syntaxen [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Du kan lägga till filter till en prenumeration med hjälp av den **skapa\_regeln** metod för den **ServiceBusService** objekt. Den här metoden kan du lägga till nya filter till en befintlig prenumeration.

> [!NOTE]
> Eftersom standardfiltret används automatiskt för alla nya prenumerationer, måste du först ta bort standardfiltret eller **MatchAll** åsidosätter eventuella filter som du kan ange. Du kan ta bort Standardregeln med hjälp av den `delete_rule` metod för den **ServiceBusService** objekt.
> 
> 

I följande exempel skapas en prenumeration med namnet `HighMessages` med en **SqlFilter** som endast väljer meddelanden som har en anpassad `messagenumber` egenskap som är större än 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

På samma sätt i följande exempel skapas en prenumeration med namnet `LowMessages` med en **SqlFilter** som endast väljer meddelanden som har en `messagenumber` egenskap som är mindre än eller lika med 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Nu när ett meddelande skickas till `mytopic` levereras det alltid till mottagare som prenumererar på den **AllMessages** avsnittet prenumeration, och levereras selektivt till mottagare som prenumererar på den **HighMessages**och **Highmessages** (beroende på innehållet i meddelandet).

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne

Om du vill skicka ett meddelande till en Service Bus-ämne, ditt program måste använda den `send_topic_message` metod för den **ServiceBusService** objekt.

I följande exempel visar hur du skickar fem testmeddelanden till `mytopic`. Den `messagenumber` -värdet för varje meddelande varierar på upprepning av loopen (detta avgör vilka prenumerationer som får det):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i ett ämne men det finns ett tak för den totala storleken för de meddelanden som ligger i ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB. Mer information om kvoter finns [Service Bus-kvoter][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration

Meddelanden tas emot från en prenumeration med hjälp av den `receive_subscription_message` -metoden i den **ServiceBusService** objekt:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Meddelanden tas bort från prenumerationen som de läses när parametern `peek_lock` är inställd på **FALSKT**. Du kan läsa (titt) och låsa meddelandet utan att ta bort det från kön genom att ange parametern `peek_lock` till **SANT**.

Beteendet för läsning och ta bort meddelandet som en del av receive-åtgärden är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera inte bearbetning av ett meddelande om det uppstår ett fel. Överväg ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan bearbetningen för att förstå problemet. Eftersom Service Bus kommer att ha markerat meddelandet som Förbrukat, och sedan när programmet startas om och börjar förbruka meddelanden igen, att ha missat meddelandet som förbrukades innan kraschen.

Om den `peek_lock` parametern anges till **SANT**, inleveransen en åtgärd i två steg, vilket gör det möjligt att stödprogram som inte tolererar att ett meddelande saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har avslutat bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning), Slutför det andra steget i processen genom att anropa `delete` -metoden i den **meddelandet** objekt. Den `delete` metoden markerar meddelandet som Förbrukat och tar bort det från prenumerationen.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden

Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram går inte att bearbeta meddelandet av någon anledning, så kan det anropa den `unlock` -metoden i den **meddelandet** objekt. Den här metoden gör att Service Bus låser upp meddelandet i prenumerationen och göra den tillgänglig för tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerad med ett meddelande som ligger låst i prenumerationen om programmet misslyckas med att bearbeta meddelandet innan timeout för lås går ut (till exempel om programmet kraschar), kommer Service Bus låser upp meddelandet automatiskt och gör det tillgängligt att tas emot igen.

I händelse av att programmet kraschar efter att meddelandet har bearbetats men innan den `delete` metoden anropas sedan meddelandet att levereras till programmet när den startas om. Det här beteendet kallas ofta *At Least Once Processing*; som är varje meddelande bearbetas minst en gång men i vissa situationer kan samma meddelande levereras. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Om du vill göra det, kan du använda den **MessageId** för meddelandet, som förblir konstant under alla leveransförsök.

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer

Ämnen och prenumerationer är beständiga och måste vara explicit bort antingen via den [Azure-portalen] [ Azure portal] eller programmässigt. I följande exempel visas hur du tar bort avsnittet med namnet `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

Om du tar bort ett ämne så tar du även bort alla prenumerationer som är registrerade på det ämnet. Prenumerationer kan även tas bort separat. Följande kod visar hur du tar bort en prenumeration med namnet `HighMessages` från den `mytopic` avsnittet:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna om Service Bus-ämnen kan du följa dessa länkar om du vill veta mer.

* Se [köer, ämnen och prenumerationer][Queues, topics, and subscriptions].
* Referens för [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
