---
title: 'Snabbstart: Så här använder du Service Bus-ämnen (Ruby)'
description: 'Snabbstart: Lär dig hur du använder Service Bus-ämnen och prenumerationer i Azure. Kodexempel är skrivna för Ruby-applikationer.'
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: b5401eae844ed2113a9fbc07c8b3ad8601709d43
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73718941"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Snabbstart: Så här använder du Service Bus-ämnen och prenumerationer med Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

I den här artikeln beskrivs hur du använder Service Bus-ämnen och prenumerationer från Ruby-program. De scenarier som behandlas omfattar:

- Skapa ämnen och prenumerationer 
- Skapa prenumerationsfilter 
- Skicka meddelanden till ett ämne 
- Ta emot meddelanden från en prenumeration
- Ta bort ämnen och prenumerationer


## <a name="prerequisites"></a>Krav
1. En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina fördelar för [Visual Studio- eller MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Följ stegen i [snabbstart: Använd Azure-portalen för att skapa ett Service Bus-ämne och prenumerationer på ämnet](service-bus-quickstart-topics-subscriptions-portal.md) för att skapa ett **servicebussnamnområde** och hämta **anslutningssträngen**. 

    > [!NOTE]
    > Du skapar ett **ämne** och en **prenumeration** på ämnet med **ruby** i den här snabbstarten. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Skapa ett ämne
**Med Azure::ServiceBusService-objektet** kan du arbeta med ämnen. Följande kod skapar ett **Azure::ServiceBusService-objekt.** Använd metoden om `create_topic()` du vill skapa ett ämne. I följande exempel skapas ett ämne eller så skrivs eventuella fel ut.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Du kan också skicka ett **Azure::ServiceBus::Topic-objekt** med ytterligare alternativ, som gör att du kan åsidosätta standardämnesinställningar som meddelandetid till live eller maximal köstorlek. I följande exempel visas inställningen av den maximala köstorleken till 5 GB och tid att leva till 1 minut:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Skapa prenumerationer
Ämnesprenumerationer skapas också med **Azure::ServiceBusService-objektet.** Prenumerationer namnges och kan ha ett valfritt filter som begränsar uppsättningen meddelanden som levereras till prenumerationens virtuella kö.

Som standard är prenumerationer beständiga. De fortsätter att existera tills antingen de, eller det ämne de är associerade med, tas bort. Om ditt program innehåller logik för att skapa en prenumeration bör det först kontrollera om prenumerationen redan finns med hjälp av metoden getSubscription.

Du kan ta bort prenumerationerna automatiskt genom att ange [egenskapen AutoDeleteOnIdle](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)
Om inget filter anges när en ny prenumeration skapas används **Filtret MatchAll** (standard). När **Filtret MatchAll** används placeras alla meddelanden som publiceras i ämnet i prenumerationens virtuella kö. I följande exempel skapas en prenumeration med namnet "alla meddelanden" och standardfiltret **MatchAll** används.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Skapa prenumerationer med filter
Du kan också definiera filter som gör att du kan ange vilka meddelanden som skickas till ett ämne som ska visas i en viss prenumeration.

Den mest flexibla typen av filter som stöds av prenumerationer är **Azure::ServiceBus::SqlFilter**, som implementerar en delmängd av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Mer information om de uttryck som kan användas med ett SQL-filter finns i [syntaxen SqlFilter.](service-bus-messaging-sql-filter.md)

Du kan lägga till filter `create_rule()` i en prenumeration med hjälp av metoden för **Azure::ServiceBusService-objektet.** Med den här metoden kan du lägga till nya filter i en befintlig prenumeration.

Eftersom standardfiltret tillämpas automatiskt på alla nya prenumerationer måste du först ta bort standardfiltret, eller så åsidosätter **MatchAll** alla andra filter som du kan ange. Du kan ta bort standardregeln med hjälp av `delete_rule()` metoden på **Azure::ServiceBusService-objektet.**

I följande exempel skapas en prenumeration med namnet "högmeddelanden" med en **Azure::ServiceBus::SqlFilter** som bara väljer meddelanden som har en anpassad `message_number` egenskap som är större än 3:

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

I följande exempel skapas en `low-messages` prenumeration med namnet **Azure::ServiceBus::SqlFilter** som bara `message_number` väljer meddelanden som har en egenskap som är mindre än eller lika med 3:

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

När ett meddelande nu `test-topic`skickas till levereras det alltid till `all-messages` mottagare som prenumererar på ämnesprenumerationen och levereras selektivt till mottagare `high-messages` som prenumererar på och `low-messages` ämnesprenumerationer (beroende på meddelandeinnehållet).

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
Om du vill skicka ett meddelande till ett `send_topic_message()` Service Bus-ämne måste ditt program använda metoden för **Azure::ServiceBusService-objektet.** Meddelanden som skickas till Service Bus-ämnen är instanser av **Azure::ServiceBus::BrokeredMessage-objekt.** **Azure::ServiceBus::BrokeredMessage-objekt** har en uppsättning standardegenskaper (till exempel `label` och `time_to_live`), en ordlista som används för att lagra anpassade programspecifika egenskaper och en samling strängdata. Ett program kan ange meddelandets brödtext genom `send_topic_message()` att skicka ett strängvärde till metoden och alla nödvändiga standardegenskaper fylls i som standardvärden.

I följande exempel visas hur du skickar `test-topic`fem testmeddelanden till . Det `message_number` anpassade egenskapsvärdet för varje meddelande varierar beroende på hur loopen är i sak (det avgör vilken prenumeration som tar emot den):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i ett ämne men det finns ett tak för den totala storleken för de meddelanden som ligger i ett ämne. Den här ämnesstorleken definieras när ämnet skapas, med en övre gräns på 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Meddelanden tas emot från en `receive_subscription_message()` prenumeration med metoden på **Azure::ServiceBusService-objektet.** Som standard läses meddelanden(topp) och låses utan att ta bort dem från prenumerationen. Du kan läsa och ta bort meddelandet `peek_lock` från prenumerationen genom att ange alternativet till **false**.

Standardbeteendet gör att läsningen och borttagningen av en tvåstegsåtgärd, vilket också gör det möjligt att stödja program som inte tolererar meddelanden som saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt `delete_subscription_message()` för framtida bearbetning) slutförs den andra fasen av mottagningsprocessen genom att anropa metoden och tillhandahålla meddelandet som ska tas bort som en parameter. Metoden `delete_subscription_message()` markerar meddelandet som förbrukat och tar bort det från prenumerationen.

Om `:peek_lock` parametern är inställd på **false**blir läsning och borttagning av meddelandet den enklaste modellen och fungerar bäst för scenarier där ett program inte kan tolerera att ett meddelande inte bearbetas när ett fel inträffar. Tänk dig ett scenario där konsumenten utfärdar mottagningsbegäran och sedan kraschar innan den bearbetas. Eftersom Service Bus har markerat meddelandet som förbrukat, då när programmet startas om och börjar konsumera meddelanden igen, har det missat meddelandet som förbrukades före kraschen.

Följande exempel visar hur meddelanden kan tas emot `receive_subscription_message()`och bearbetas med . Exemplet tar först emot och tar `low-messages` bort `:peek_lock` ett meddelande från prenumerationen med `high-messages` hjälp av inställd på `delete_subscription_message()` **false**, då det tar emot ett annat meddelande från och sedan tar bort meddelandet med:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram inte kan bearbeta meddelandet av någon `unlock_subscription_message()` anledning kan det anropa metoden på **Azure::ServiceBusService-objektet.** Det gör att Service Bus låser upp meddelandet i prenumerationen och gör det tillgängligt för att tas emot igen, antingen av samma tidskrävande program eller av ett annat tidskrävande program.

Det finns också en timeout som är associerad med ett meddelande som är låst i prenumerationen, och om programmet inte behandlar meddelandet innan låstidsgränsen upphör att gälla (till exempel om programmet kraschar), låses Service Bus upp meddelandet automatiskt och göra den tillgänglig för att tas emot igen.

I händelse av att programmet kraschar efter bearbetning `delete_subscription_message()` av meddelandet men innan metoden anropas, levereras meddelandet till programmet när det startas om. Det kallas ofta *minst en gång bearbetning*; det vill säga varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras på nytt. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Denna logik uppnås ofta `message_id` med hjälp av egenskapen för meddelandet, som förblir konstant över leveransförsök.

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer
Ämnen och prenumerationer är beständiga om inte [egenskapen AutoDeleteOnIdle](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) har angetts. De kan tas bort antingen via [Azure-portalen][Azure portal] eller programmässigt. I följande exempel visas hur du `test-topic`tar bort ämnet .

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Om du tar bort ett ämne så tar du även bort alla prenumerationer som är registrerade på det ämnet. Prenumerationer kan även tas bort separat. Följande kod visar hur du tar `high-messages` bort `test-topic` prenumerationen som heter från ämnet:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer tillåter användare att ansluta till ett servicebussnamnområde och administrera meddelandeenheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjligheten att testa ämne, köer, prenumerationer, relätjänster, meddelandehubbar och händelsehubbar. 

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Service Bus ämnen, följ dessa länkar för att lära dig mer.

* Se [Köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md).
* API-referens för [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Besök [Azure SDK för Ruby-databasen](https://github.com/Azure/azure-sdk-for-ruby) på GitHub.

[Azure portal]: https://portal.azure.com
