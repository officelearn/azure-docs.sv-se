---
title: Använda Azure Service Bus ämnen med python | Microsoft Docs
description: Lär dig hur du använder Azure Service Bus ämnen och prenumerationer från python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: d294ceaaf77175a3010131b18864b71c7b26b88b
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360825"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Använda Service Bus ämnen och prenumerationer med python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

I den här artikeln beskrivs hur du använder Service Bus-ämnen och -prenumerationer. Exemplen skrivs i python och använder [Azure python SDK-paketet][Azure Python package]. Scenarierna som omfattas är:

- Skapa ämnen och prenumerationer 
- Skapa prenumerations filter 
- Skicka meddelanden till ett ämne 
- Ta emot meddelanden från en prenumeration
- Ta bort ämnen och prenumerationer

## <a name="prerequisites"></a>Förutsättningar
1. En Azure-prenumeration. Du behöver ett Azure-konto för att slutföra den här självstudien. Du kan aktivera dina [förmåner för Visual Studio eller MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) -prenumeranter eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Följ stegen i [snabb starten: Använd Azure Portal för att skapa ett Service Bus ämne och prenumerationer på avsnittet](service-bus-quickstart-topics-subscriptions-portal.md) för att skapa ett Service Bus- **namnområde** och hämta **anslutnings strängen**.

    > [!NOTE]
    > Du kommer att skapa ett **ämne** och en **prenumeration** på avsnittet med hjälp av **python** i den här snabb starten. 
3. Installera [Azure python-paketet][Azure Python package]. Se [installations guiden för python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Skapa ett ämne

Med **ServiceBusService** -objektet kan du arbeta med ämnen. Lägg till följande kod nära överkanten av en python-fil som du vill ha åtkomst till Service Bus via programmering:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Följande kod skapar ett **ServiceBusService** -objekt. Ersätt `mynamespace`, `sharedaccesskeyname`, och`sharedaccesskey` med ditt faktiska namn område, nyckel namn för delad åtkomst (SAS) och nyckel värde.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Du kan hämta värdena för SAS-nyckelns namn och värde från [Azure Portal][Azure portal].

```python
bus_service.create_topic('mytopic')
```

`create_topic` Metoden stöder också ytterligare alternativ, vilket gör att du kan åsidosätta standard ämnes inställningar, till exempel meddelande tid till Live eller maximal ämnes storlek. I följande exempel anges den maximala storleken för ämnen till 5 GB och ett TTL-värde (Time to Live) på en minut:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Skapa prenumerationer

Prenumerationer på ämnen skapas också med **ServiceBusService** -objektet. Prenumerationer namnges och kan ha ett valfritt filter som begränsar den uppsättning meddelanden som skickas till prenumerationens virtuella kö.

> [!NOTE]
> Som standard är prenumerationerna beständiga och fortsätter att finnas tills de, eller det ämne som de prenumererar på, tas bort.
> 
> Du kan ta bort prenumerationerna automatiskt genom att ange [egenskapen auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)

Om inget filter anges när en ny prenumeration skapas, används **MatchAll** -filtret (standard). När **MatchAll** -filtret används placeras alla meddelanden som publiceras till ämnet i prenumerationens virtuella kö. I följande exempel skapas en prenumeration med `AllMessages` namnet och standard filtret används i **MatchAll** .

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Skapa prenumerationer med filter

Du kan också definiera filter som gör att du kan ange vilka meddelanden som skickas till ett ämne som ska visas i en speciell ämnes prenumeration.

Den mest flexibla typen av filter som stöds av prenumerationer är en **SqlFilter**, som implementerar en delmängd av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Mer information om uttryck som kan användas med ett SQL-filter finns i syntaxen [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Du kan lägga till filter i en prenumeration med hjälp av metoden **\_Skapa regel** för **ServiceBusService** -objektet. Med den här metoden kan du lägga till nya filter till en befintlig prenumeration.

> [!NOTE]
> Eftersom standard filtret används automatiskt för alla nya prenumerationer måste du först ta bort standard filtret eller så åsidosätter **MatchAll** eventuella andra filter som du kan ange. Du kan ta bort standard regeln med hjälp `delete_rule` av metoden för **ServiceBusService** -objektet.
> 
> 

I följande exempel skapas en prenumeration med `HighMessages` namnet med en **SqlFilter** som endast väljer meddelanden som har en `messagenumber` anpassad egenskap som är större än 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

På samma sätt skapar följande exempel en prenumeration med namnet `LowMessages` med en **SqlFilter** som endast väljer meddelanden som har en `messagenumber` egenskap som är mindre än eller lika med 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

När ett `mytopic` meddelande skickas till det alltid skickas till mottagare som prenumererar på **AllMessages** -ämnes prenumerationen och selektivt levereras till mottagare som prenumererar på **HighMessages** -och **LowMessages** -ämnet prenumerationer (beroende på meddelandets innehåll).

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne

Om du vill skicka ett meddelande till ett Service Bus ämne måste programmet använda `send_topic_message` metoden för **ServiceBusService** -objektet.

Följande exempel visar hur du skickar fem test meddelanden till `mytopic`. `messagenumber` Egenskap svärdet för varje meddelande varierar beroende på loopens iteration (detta avgör vilka prenumerationer som tar emot det):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messagenumber': i})
    bus_service.send_topic_message('mytopic', msg)
```

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i ett ämne men det finns ett tak för den totala storleken för de meddelanden som ligger i ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB. Mer information om kvoter finns i [Service Bus kvoter][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration

Meddelanden tas emot från en prenumeration med hjälp `receive_subscription_message` av metoden på **ServiceBusService** -objektet:

```python
msg = bus_service.receive_subscription_message(
    'mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Meddelanden tas bort från prenumerationen när de läses när parametern `peek_lock` har angetts till **false**. Du kan läsa (granska) och låsa meddelandet utan att ta bort det från kön genom att ange parametern `peek_lock` till **True**.

Beteendet att läsa och ta bort meddelandet som en del av Receive-åtgärden är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera att inte bearbeta ett meddelande när det uppstår ett fel. För att förstå det här beteendet bör du överväga ett scenario där klienten utfärdar Receive-begäran och sedan kraschar innan den bearbetas. Eftersom Service Bus har markerat meddelandet som förbrukat, när programmet startas om och börjar förbruka meddelanden igen, har det fått meddelandet som förbrukades innan kraschen.

Om parametern är inställd på Sant blir mottagningen en åtgärd i två steg, vilket gör det möjligt att stödja program som inte kan tolerera meddelanden som saknas.  `peek_lock` När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det tillförlitligt för framtida bearbetning) slutförs det andra steget i Receive-processen genom `delete` att anropa metoden i objektet **Message** . `delete` Metoden markerar meddelandet som förbrukat och tar bort det från prenumerationen.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden

Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagar program inte kan bearbeta meddelandet av någon anledning, kan det anropa `unlock` metoden i objektet **Message** . Den här metoden gör att Service Bus låser upp meddelandet i prenumerationen och gör det tillgängligt att tas emot igen, antingen genom samma användnings program eller ett annat användnings program.

Det finns också en tids gräns som är kopplad till ett meddelande som är låst i prenumerationen och om programmet inte kan bearbeta meddelandet innan tids gränsen för låsning går ut (till exempel om programmet kraschar), så Service Bus låser upp meddelandet automatiskt och gör det tillgängligt att tas emot igen.

I händelse av att programmet kraschar när meddelandet har bearbetats men innan `delete` metoden anropas, kommer meddelandet att skickas vidare till programmet när det startas om. Det här beteendet kallas ofta. Minst en gång\*, det vill säga varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Om du vill göra det kan du använda meddelandets **messageid** -egenskap, som är konstant över leverans försök.

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer

Ämnen och prenumerationer är permanenta om inte [auto_delete_on_idle-egenskapen](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) har angetts. De kan tas bort antingen via [Azure Portal][Azure portal] eller program mässigt. I följande exempel visas hur du tar bort ämnet med `mytopic`namnet:

```python
bus_service.delete_topic('mytopic')
```

Om du tar bort ett ämne så tar du även bort alla prenumerationer som är registrerade på det ämnet. Prenumerationer kan även tas bort separat. Följande kod visar hur du tar bort en prenumeration med `HighMessages` namnet `mytopic` från avsnittet:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer gör det möjligt för användare att ansluta till en Service Bus namnrymd och administrera meddelande enheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub. 

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna om Service Bus ämnen kan du följa dessa länkar om du vill veta mer.

* Se [köer, ämnen och prenumerationer][Queues, topics, and subscriptions].
* Referens för [SqlFilter. SqlExpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
