---
title: Så här använder du Azure Service Bus-köer med Ruby
description: I den här självstudien får du lära dig hur du skapar Ruby-program för att skicka meddelanden till och ta emot meddelanden från en servicebusskö.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: a699543bb442e7c57d57e72acb2cdf6ac40159c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760597"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Snabbstart: Så här använder du servicebussköer med Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

I den här självstudien får du lära dig hur du skapar Ruby-program för att skicka meddelanden till och ta emot meddelanden från en servicebusskö. Exemplen är skrivna i Ruby och använder Azure-pärlan.

## <a name="prerequisites"></a>Krav
1. En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [msdn-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Följ stegen i [Använda Azure-portalen för att skapa en köartikel för servicebuss.](service-bus-quickstart-portal.md)
    1. Läs snabb **översikten över** servicebussköer . **queues** 
    2. Skapa ett **servicebussnamnområde**. 
    3. Hämta **anslutningssträngen**. 

        > [!NOTE]
        > Du skapar en **kö** i servicebussens namnområde med Ruby i den här självstudien. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Så skapar du en resurs
**Azure::ServiceBusService-objektet** gör att du kan arbeta med köer. Om du vill skapa `create_queue()` en kö använder du metoden. I följande exempel skapas en kö eller så skrivs eventuella fel ut.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Du kan också skicka ett **Azure::ServiceBus::Queue-objekt** med ytterligare alternativ, vilket gör att du kan åsidosätta standardköinställningarna, till exempel meddelandetid för att leva eller maximal köstorlek. I följande exempel visas hur du ställer in den maximala köstorleken till 5 GB och tid att leva till 1 minut:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Så här skickar du meddelanden till en kö
Om du vill skicka ett meddelande till `send_queue_message()` en servicebusskö anropar ditt program metoden på **Azure::ServiceBusService-objektet.** Meddelanden som skickas till (och tas emot från) Service Bus-köer är **Azure::ServiceBus::BrokeredMessage-objekt** och har en uppsättning standardegenskaper (till exempel och `label` `time_to_live`), en ordlista som används för att lagra anpassade programspecifika egenskaper och en samling godtyckliga programdata. Ett program kan ange meddelandets brödtext genom att skicka ett strängvärde som meddelande och alla nödvändiga standardegenskaper fylls i med standardvärden.

I följande exempel visas hur du skickar ett `test-queue` `send_queue_message()`testmeddelande till kön med namnet:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Meddelanden tas emot från en `receive_queue_message()` kö med metoden på **Azure::ServiceBusService-objektet.** Som standard läses och låses meddelanden utan att tas bort från kön. Du kan dock ta bort meddelanden från kön när `:peek_lock` de läs avstönare genom att ange alternativet till **false**.

Standardbeteendet gör att läsningen och borttagningen av en tvåstegsåtgärd, vilket också gör det möjligt att stödja program som inte tolererar meddelanden som saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det på ett tillförlitligt sätt `delete_queue_message()` för framtida bearbetning) slutförs den andra fasen av mottagningsprocessen genom att anropa metoden och tillhandahålla meddelandet som ska tas bort som en parameter. Metoden `delete_queue_message()` markerar meddelandet som förbrukat och tar bort det från kön.

Om `:peek_lock` parametern är inställd på **false**blir läsning och borttagning av meddelandet den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera att inte bearbeta ett meddelande i händelse av ett fel. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus har markerat meddelandet som förbrukat, när programmet startas om och börjar konsumera meddelanden igen, kommer det att ha missat meddelandet som förbrukades före kraschen.

I följande exempel visas hur du får `receive_queue_message()`och bearbetar meddelanden med . Exemplet tar först emot och tar `:peek_lock` bort ett meddelande med hjälp av inställd på `delete_queue_message()` **false**, då det tar emot ett annat meddelande och sedan tar bort meddelandet med:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram inte kan bearbeta meddelandet av någon `unlock_queue_message()` anledning kan det anropa metoden på **Azure::ServiceBusService-objektet.** Det här anropet gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt för att tas emot igen, antingen av samma tidskrävande program eller av ett annat tidskrävande program.

Det finns också en timeout som är associerad med ett meddelande låst i kön, och om programmet inte kan bearbeta meddelandet innan låstidsgränsen upphör att gälla (till exempel om programmet kraschar), låses Service Bus upp meddelandet automatiskt och gör det tillgängliga för att tas emot igen.

I händelse av att programmet kraschar efter bearbetning `delete_queue_message()` av meddelandet men innan metoden anropas, levereras meddelandet till programmet när det startas om. Denna process kallas ofta *minst en gång bearbetning;* det vill säga varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras på nytt. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta `message_id` med hjälp av egenskapen för meddelandet, som förblir konstant över leveransförsök.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer tillåter användare att ansluta till ett servicebussnamnområde och administrera meddelandeenheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjligheten att testa ämne, köer, prenumerationer, relätjänster, meddelandehubbar och händelsehubbar. 

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Service Bus-köer, kan du följa dessa länkar om du vill veta mer.

* Översikt över [köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md).
* Besök [Azure SDK för Ruby-databasen](https://github.com/Azure/azure-sdk-for-ruby) på GitHub.

En jämförelse mellan azure service bus-köerna som beskrivs i den här artikeln och Azure-köer som beskrivs i artikeln [Hur du använder kölagring från Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) finns i [Azure-köer och Azure Service Bus-köer – Jämfört och kontrasterat](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

