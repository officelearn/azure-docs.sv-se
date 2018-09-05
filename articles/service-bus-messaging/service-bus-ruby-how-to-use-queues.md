---
title: Hur du använder Azure Service Bus-köer med Ruby | Microsoft Docs
description: Lär dig hur du använder Service Bus-köer i Azure. Kodexempel som är skrivna i Ruby.
services: service-bus-messaging
documentationcenter: ruby
author: spelluru
manager: timlt
editor: ''
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: 26015ec1a3bf294e466902992ff13f1bc4693f04
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702348"
---
# <a name="how-to-use-service-bus-queues-with-ruby"></a>Hur du använder Service Bus-köer med Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Den här guiden beskriver hur du använder Service Bus-köer. Exemplen är skrivna i Ruby och använder Azure gem. Scenarier som omfattas är **skapande av köer, skicka och ta emot meddelanden**, och **tar bort köer**. Mer information om Service Bus-köer finns i den [nästa steg](#next-steps) avsnittet.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]
   
[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Så här skapar du en kö
Den **Azure::ServiceBusService** objekt kan du arbeta med köer. Du kan skapa en kö med den `create_queue()` metoden. I följande exempel skapar en kö eller skriver ut eventuella fel.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Du kan även skicka en **Azure::ServiceBus::Queue** objekt med ytterligare alternativ, vilket gör att du vill åsidosätta standardinställningarna för kö, till exempel tid till live eller maximal köstorlek. I följande exempel visas hur du ställer in den största köstorleken till 5 GB och tid för TTL-värde till 1 minut:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Hur du skickar meddelanden till en kö
Att skicka ett meddelande till en Service Bus-kö, program-anrop i `send_queue_message()` metoden på den **Azure::ServiceBusService** objekt. Meddelanden skickas till (och tas emot från) Service Bus köer är **Azure::ServiceBus::BrokeredMessage** objekt och har en uppsättning standardegenskaper (t.ex `label` och `time_to_live`), en ordlista som används för att lagra anpassade programspecifika egenskaper och en brödtext med godtyckliga programdata. Ett program kan konfigurera meddelandets brödtext genom att skicka ett strängvärde som meddelandet och eventuella standard egenskaper fylls med standardvärden.

I följande exempel visar hur du skickar ett testmeddelande till kön med namnet `test-queue` med `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Meddelanden tas emot från en kö med hjälp av den `receive_queue_message()` metoden på den **Azure::ServiceBusService** objekt. Som standard läsa meddelanden och låst utan tas bort från kön. Du kan dock ta bort meddelanden från kön som de är skrivskyddade genom att ange den `:peek_lock` alternativet att **FALSKT**.

Standardbeteendet gör medan läsningen och tar bort en åtgärd i två steg, vilket gör det också möjligt att stödja program som inte tolererar att saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört behandlingen av meddelandet (eller lagrar den på ett tillförlitligt sätt för framtida bearbetning), den är klar det andra steget i processen genom att anropa `delete_queue_message()` metod och ge meddelanden som ska tas bort som en parameter. Den `delete_queue_message()` metoden kommer Markera meddelandet som Förbrukat och ta bort det från kön.

Om den `:peek_lock` parametern är inställd på **FALSKT**, läsa och radera meddelandet blir den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus har markerat meddelandet som Förbrukat när programmet startas om och börjar förbruka meddelanden igen – att det ha missat meddelandet som förbrukades innan kraschen.

I följande exempel visar hur du tar emot och bearbetar meddelanden med `receive_queue_message()`. I exempel först tar emot och tar bort ett meddelande med hjälp av `:peek_lock` inställd **FALSKT**, och sedan den tar emot ett nytt meddelande och sedan tar bort meddelandet med hjälp av `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram är det går inte att bearbeta meddelandet av någon anledning så kan det anropa den `unlock_queue_message()` metoden på den **Azure::ServiceBusService** objekt. Det här anropet gör att Service Bus att låsa upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerade med ett meddelande som ligger låst i kön. Om programmet inte kan bearbeta meddelandet innan timeout för lås går ut (till exempel om programmet kraschar), kommer Service Bus låser upp meddelandet automatiskt och gör det tillgängligt att tas emot igen.

I händelse av att programmet kraschar efter behandlingen av meddelandet men innan den `delete_queue_message()` metoden anropas sedan meddelandet once till programmet när den startas om. Den här processen kallas ofta *minst Processing*; det vill säga varje meddelande bearbetas minst en gång men i vissa situationer kan samma meddelande kan levereras. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av den `message_id` för meddelandet, förblir konstant under alla leveransförsök.

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Service Bus-köer, kan du följa dessa länkar om du vill veta mer.

* Översikt över [köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md).
* Gå till den [Azure SDK för Ruby](https://github.com/Azure/azure-sdk-for-ruby) arkivet på GitHub.

En jämförelse mellan Azure Service Bus-köer som beskrivs i den här artikeln och Azure-köer som beskrivs i den [hur du använder Queue storage från Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) artikel, se [Azure-köer och Azure Service Bus-köer – jämfört med och Skillnader](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

