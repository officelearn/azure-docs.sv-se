---
title: Hur du använder Service Bus-ämnen (Ruby) | Microsoft Docs
description: Lär dig hur du använder Service Bus-ämnen och prenumerationer i Azure. Kodexemplen är skrivna för Ruby program.
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 4a4c9949843b16ae6be2f516de4fd1e3f7415959
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23926797"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Hur du använder Service Bus-ämnen och prenumerationer med Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Den här artikeln beskriver hur du använder Service Bus-ämnen och prenumerationer från Ruby program. Scenarier som tas upp inkluderar **skapa ämnen och prenumerationer, skapa prenumerationsfilter, skicka meddelanden** till ett ämne **ta emot meddelanden från en prenumeration**, och **tas bort ämnen och prenumerationer**. Mer information om ämnen och prenumerationer finns i [nästa steg](#next-steps) avsnitt.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Skapa ett ämne
Den **Azure::ServiceBusService** objekt kan du arbeta med ämnen. Följande kod skapar en **Azure::ServiceBusService** objekt. Du kan skapa ett ämne med den `create_topic()` metoden. I följande exempel skapar ett ämne eller skriver ut felen om det finns några.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

Du kan också skicka ett **Azure::ServiceBus::Topic** objekt med ytterligare alternativ som gör att du kan åsidosätta standardinställningar för ämnet, till exempel tid till live eller maximal köstorlek. I följande exempel visas att ställa in den maximala storleken till 5 GB och -tid live till 1 minut:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Skapa prenumerationer
Ämnesprenumerationer skapas också med den **Azure::ServiceBusService** objekt. Prenumerationer är namngivna och kan ha ett valfritt filter som begränsar uppsättningen av meddelanden som skickas till prenumerationens virtuella kö.

Prenumerationer är beständiga och fortsätter att finns förrän antingen de eller avsnittet som de är kopplade till, tas bort. Om ditt program innehåller logik för att skapa en prenumeration, bör det först kontrollera om prenumerationen finns redan med hjälp av metoden getSubscription.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)
**MatchAll**-filtret är det standardfilter som används om inget filter anges när en ny prenumeration skapas. När den **MatchAll** filter används, alla meddelanden som publiceras till ämnet placeras i prenumerationens virtuella kö. I följande exempel skapar en prenumeration med namnet ”alla meddelanden” och använder förvalet **MatchAll** filter.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Skapa prenumerationer med filter
Du kan också definiera filter som gör att du kan ange vilka meddelanden som skickas till ett ämne visas inom en viss prenumeration.

Den mest flexibla typen av filter som stöds av prenumerationerna är den **Azure::ServiceBus::SqlFilter**, som implementerar en deluppsättning av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Mer information om uttryck som kan användas med ett SQL-filter granskar den [SqlFilter](service-bus-messaging-sql-filter.md) syntax.

Du kan lägga till filter till en prenumeration med hjälp av den `create_rule()` metod för den **Azure::ServiceBusService** objekt. Den här metoden kan du lägga till nya filter till en befintlig prenumeration.

Eftersom standardfiltret används automatiskt för alla nya prenumerationer, måste du först ta bort standardfiltret eller **MatchAll** åsidosätter eventuella filter som du kan ange. Du kan ta bort Standardregeln med hjälp av den `delete_rule()` -metoden i den **Azure::ServiceBusService** objekt.

I följande exempel skapas en prenumeration med namnet ”hög-meddelanden” med en **Azure::ServiceBus::SqlFilter** som endast väljer meddelanden som har en anpassad `message_number` egenskap som är större än 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

På samma sätt i följande exempel skapas en prenumeration med namnet `low-messages` med en **Azure::ServiceBus::SqlFilter** som endast väljer meddelanden som har en `message_number` egenskap som är mindre än eller lika med 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

När ett meddelande skickas nu till `test-topic`, är alltid levereras till mottagare som prenumererar på den `all-messages` avsnittet prenumeration, och levereras selektivt till mottagare som prenumererar på den `high-messages` och `low-messages` ämnesprenumerationer (beroende När innehållet i meddelandet).

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
Om du vill skicka ett meddelande till en Service Bus-ämne, ditt program måste använda den `send_topic_message()` -metoden i den **Azure::ServiceBusService** objekt. Meddelanden som skickas till Service Bus-ämnen är instanser av den **Azure::ServiceBus::BrokeredMessage** objekt. **Azure::ServiceBus::BrokeredMessage** objekt har en uppsättning standardegenskaper (t.ex `label` och `time_to_live`), en ordlista som används för att lagra anpassade programspecifika egenskaper och en brödtext med strängdata. Ett program kan konfigurera meddelandets brödtext för meddelandet genom att skicka ett strängvärde till den `send_topic_message()` metoden och eventuella krävs standardegenskaper fylls i med standardvärden.

I följande exempel visar hur du skickar fem testmeddelanden till `test-topic`. Observera att den `message_number` värdet för anpassad egenskap för varje meddelande varierar på upprepning av loopen (detta avgör vilken prenumeration tar emot det):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i ett ämne men det finns ett tak för den totala storleken för de meddelanden som ligger i ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Meddelanden tas emot från en prenumeration med hjälp av den `receive_subscription_message()` -metoden i den **Azure::ServiceBusService** objekt. Som standard meddelanden read(peak) och låst utan att ta bort den från prenumerationen. Du kan läsa och ta bort meddelandet från prenumerationen genom att ange den `peek_lock` att **FALSKT**.

Standardbeteendet gör läsningen och ta bort en åtgärd i två steg, vilket gör det också möjligt att stödja program som inte tolererar att ett meddelande saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har avslutat bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt för framtida bearbetning), Slutför det andra steget i processen genom att anropa `delete_subscription_message()` metod och ge meddelandet som ska tas bort som en parameter. Den `delete_subscription_message()` metoden att markera meddelandet som Förbrukat och ta bort den från prenumerationen.

Om den `:peek_lock` parametern anges till **FALSKT**, läser och tar bort meddelandet blir den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus kommer att ha markerat meddelandet som Förbrukat, och sedan när programmet startas om och börjar förbruka meddelanden igen, att ha missat meddelandet som förbrukades innan kraschen.

Exemplet nedan visar hur meddelanden kan tas emot och bearbetat använder `receive_subscription_message()`. I exempel först tar emot och tar bort ett meddelande från den `low-messages` prenumeration med hjälp av `:peek_lock` inställd på **FALSKT**, och den får ett nytt meddelande från den `high-messages` och tar bort meddelandet med `delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram går inte att bearbeta meddelandet av någon anledning, så kan det anropa den `unlock_subscription_message()` -metoden i den **Azure::ServiceBusService** objekt. Detta gör att Service Bus låser upp meddelandet i prenumerationen och gör det tillgängligt att tas emot igen, antingen genom att samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerad med ett meddelande som ligger låst i prenumerationen om programmet misslyckas med att bearbeta meddelandet innan timeout för lås går ut (till exempel om programmet kraschar), kommer Service Bus låser upp meddelandet automatiskt och göra det tillgängligt att tas emot igen.

I händelse av att programmet kraschar efter att meddelandet har bearbetats men innan den `delete_subscription_message()` metoden anropas sedan meddelandet är levereras på nytt till programmet när den startas om. Det här kallas ofta *At Least Once Processing*; som är varje meddelande bearbetas minst en gång men i vissa situationer kan samma meddelande levereras. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Den här logiken uppnås ofta med hjälp av den `message_id` för meddelandet, som förblir konstant under alla leveransförsök.

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer
Ämnen och prenumerationer är beständiga och måste vara explicit bort antingen via den [Azure-portalen] [ Azure portal] eller programmässigt. Exemplet nedan visar hur du tar bort ämnet med namnet `test-topic`.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Om du tar bort ett ämne så tar du även bort alla prenumerationer som är registrerade på det ämnet. Prenumerationer kan även tas bort separat. Följande kod visar hur du tar bort en prenumeration med namnet `high-messages` från den `test-topic` avsnittet:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Service Bus-ämnen kan du följa dessa länkar om du vill veta mer.

* Se [köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md).
* API-referens för [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter).
* Besök den [Azure SDK för Ruby](https://github.com/Azure/azure-sdk-for-ruby) databasen på GitHub.

[Azure portal]: https://portal.azure.com
