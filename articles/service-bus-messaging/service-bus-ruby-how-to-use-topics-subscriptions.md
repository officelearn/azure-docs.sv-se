---
title: Hur du använder Service Bus-ämnen (Ruby) | Microsoft Docs
description: Lär dig hur du använder Service Bus-ämnen och prenumerationer i Azure. Kodexemplen är skrivna för Ruby-program.
services: service-bus-messaging
documentationcenter: ruby
author: spelluru
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 08/10/2018
ms.author: spelluru
ms.openlocfilehash: 0310b532fae69aa2509a427b73c40ba732a740de
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54102321"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Hur du använder Service Bus-ämnen och prenumerationer med Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Den här artikeln beskriver hur du använder Service Bus-ämnen och prenumerationer från Ruby-program. Scenarier som omfattas är **skapa ämnen och prenumerationer, skapa prenumerationsfilter, skicka meddelanden** till ett ämne **ta emot meddelanden från en prenumeration**, och **tas bort ämnen och prenumerationer**. Mer information om ämnen och prenumerationer finns i den [nästa steg](#next-steps) avsnittet.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Skapa ett ämne
Den **Azure::ServiceBusService** objekt kan du arbeta med ämnen. Följande kod skapar ett **Azure::ServiceBusService** objekt. Du kan skapa ett ämne med den `create_topic()` metoden. I följande exempel skapar ett ämne eller skriver ut eventuella fel.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Du kan även skicka en **Azure::ServiceBus::Topic** objekt med ytterligare alternativ som gör att du kan åsidosätta standardinställningarna för ämnet, till exempel tid till live eller maximal köstorlek. I följande exempel visas 5 GB och tid för TTL-värde till 1 minut att ställa in den största köstorleken:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Skapa prenumerationer
Ämnesprenumerationer skapas också med den **Azure::ServiceBusService** objekt. Prenumerationer är namngivna och kan ha ett valfritt filter som begränsar uppsättningen av meddelanden som levereras till prenumerationens virtuella kö.

Prenumerationer är permanent. De fortsätter att existera tills antingen de eller ämnet som de är associerade med, tas bort. Om programmet innehåller logik för att skapa en prenumeration, bör det först kontrollera om den redan finns med hjälp av metoden getSubscription.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)
Om inget filter anges när en ny prenumeration skapas den **MatchAll** filter (standard) används. När den **MatchAll** filter används, alla meddelanden som publiceras till ämnet placeras i prenumerationens virtuella kö. I följande exempel skapar en prenumeration med namnet ”alla meddelanden” och använder förvalet **MatchAll** filter.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Skapa prenumerationer med filter
Du kan också definiera filter som gör det möjligt att ange vilka meddelanden som skickas till ett ämne visas inom en viss prenumeration.

Den mest flexibla typen av filter som stöds av prenumerationerna är den **Azure::ServiceBus::SqlFilter**, som implementerar en deluppsättning av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Mer information om uttryck som kan användas med ett SQL-filter granskar den [SqlFilter](service-bus-messaging-sql-filter.md) syntax.

Du kan lägga till filter till en prenumeration med hjälp av den `create_rule()` -metoden för den **Azure::ServiceBusService** objekt. Den här metoden kan du lägga till nya filter i en befintlig prenumeration.

Eftersom standardfiltret används automatiskt på alla nya prenumerationer, måste du först ta bort standardfiltret eller **MatchAll** åsidosätter eventuella filter som du kan ange. Du kan ta bort Standardregeln med hjälp av den `delete_rule()` metoden på den **Azure::ServiceBusService** objekt.

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

När ett meddelande skickas nu till `test-topic`, det är alltid ska levereras till mottagare som prenumererar på den `all-messages` ämnesprenumerationen levereras selektivt till mottagare som prenumererar på den `high-messages` och `low-messages` ämnesprenumerationer (beroende vid meddelandeinnehåll).

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
Om du vill skicka ett meddelande till en Service Bus-ämne, ditt program måste använda den `send_topic_message()` metoden på den **Azure::ServiceBusService** objekt. Meddelanden som skickas till Service Bus-ämnen är instanser av den **Azure::ServiceBus::BrokeredMessage** objekt. **Azure::ServiceBus::BrokeredMessage** objekt har en uppsättning standardegenskaper (t.ex `label` och `time_to_live`), en ordlista som används för att lagra anpassade egenskaper för programspecifika och en brödtext med strängdata. Ett program kan konfigurera meddelandets brödtext genom att skicka ett strängvärde till den `send_topic_message()` metoden och eventuella nödvändiga standardegenskaper fylls med standardvärden.

I följande exempel visar hur du skickar fem testmeddelanden till `test-topic`. Den `message_number` anpassade egenskapsvärdet för varje meddelande varierar på upprepning av loopen (det avgör vilken prenumeration som tar emot det):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i ett ämne men det finns ett tak för den totala storleken för de meddelanden som ligger i ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Meddelanden tas emot från en prenumeration med hjälp av den `receive_subscription_message()` metoden på den **Azure::ServiceBusService** objekt. Som standard meddelanden read(peak) och låst utan att ta bort den från prenumerationen. Du kan läsa och ta bort meddelandet från prenumerationen genom att ange den `peek_lock` alternativet att **FALSKT**.

Standardbeteendet gör medan läsningen och tar bort en åtgärd i två steg, vilket gör det också möjligt att stödja program som inte tolererar att saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört behandlingen av meddelandet (eller lagrar den på ett tillförlitligt sätt för framtida bearbetning), den är klar det andra steget i processen genom att anropa `delete_subscription_message()` metod och ge meddelanden som ska tas bort som en parameter. Den `delete_subscription_message()` metoden markerar meddelandet som Förbrukat och ta bort den från prenumerationen.

Om den `:peek_lock` parametern är inställd på **FALSKT**, läsa och radera meddelandet blir den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande när ett fel uppstår. Tänk dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan bearbetningen. Eftersom Service Bus har markerat meddelandet som Förbrukat, har sedan när programmet startas om och börjar förbruka meddelanden igen, det missat meddelandet som förbrukades innan kraschen.

Följande exempel visar hur meddelanden kan tas emot och bearbetat använder `receive_subscription_message()`. I exempel först tar emot och tar bort ett meddelande från den `low-messages` prenumeration med hjälp av `:peek_lock` inställd **FALSKT**, och sedan den får ett nytt meddelande från den `high-messages` och tar bort meddelandet med `delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram är det går inte att bearbeta meddelandet av någon anledning så kan det anropa den `unlock_subscription_message()` metoden på den **Azure::ServiceBusService** objekt. Det gör att Service Bus att låsa upp meddelandet i prenumerationen och gör det tillgängligt att tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerade med ett meddelande som ligger låst i prenumerationen om programmet misslyckas med att bearbeta meddelandet innan låset tidsgränsen har nåtts (till exempel om programmet kraschar), så att Service Bus låser upp meddelandet automatiskt och gör det tillgängligt att tas emot igen.

I händelse av att programmet kraschar efter behandlingen av meddelandet men innan den `delete_subscription_message()` metoden anropas sedan meddelandet once till programmet när den startas om. Det kallas ofta *bearbetning minst en gång*; dvs varje meddelande bearbetas minst en gång men i vissa situationer kan samma meddelande kan levereras. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Den här logiken uppnås ofta med hjälp av den `message_id` för meddelandet, förblir konstant under alla leveransförsök.

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer
Ämnen och prenumerationer är beständiga och måste vara explicit bort antingen via den [Azure-portalen] [ Azure portal] eller programmässigt. I följande exempel visar hur du tar bort ämnet med namnet `test-topic`.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Om du tar bort ett ämne så tar du även bort alla prenumerationer som är registrerade på det ämnet. Prenumerationer kan även tas bort separat. Följande kod visar hur du tar bort prenumeration med namnet `high-messages` från den `test-topic` avsnittet:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Service Bus-ämnen kan du följa dessa länkar om du vill veta mer.

* Se [köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md).
* API-referens för [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter).
* Gå till den [Azure SDK för Ruby](https://github.com/Azure/azure-sdk-for-ruby) arkivet på GitHub.

[Azure portal]: https://portal.azure.com
