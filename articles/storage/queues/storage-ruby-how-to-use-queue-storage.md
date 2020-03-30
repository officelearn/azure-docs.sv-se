---
title: Så här använder du kölagring från Ruby - Azure Storage
description: Lär dig hur du använder Azure Queue-tjänsten för att skapa och ta bort köer och infoga, hämta och ta bort meddelanden. Prover skrivna i Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: c7211bc805f4ed1d026faedbfdc9d53d3c1dfd93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68721296"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Använda Queue Storage från Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av Tjänsten Microsoft Azure Queue Storage. Exemplen skrivs med Ruby Azure API.
Scenarierna som omfattas omfattar **att infoga,** **kika,** **hämta**och **ta bort** kömeddelanden samt skapa och ta **bort köer**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Skapa ett Ruby-program
Skapa ett Ruby-program. Instruktioner finns [i Skapa en Ruby App i App Service på Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Konfigurera ditt program för åtkomst till lagring
Om du vill använda Azure-lagring måste du hämta och använda Ruby azure-paketet, som innehåller en uppsättning bekvämlighetsbibliotek som kommunicerar med REST-lagringstjänsterna.

### <a name="use-rubygems-to-obtain-the-package"></a>Hämta paketet med hjälp av RubyGems
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix).
2. Skriv "gem install azure" i kommandofönstret för att installera pärla och beroenden.

### <a name="import-the-package"></a>Importera paketet
Använd din favorittextredigerare, lägg till följande högst upp i Ruby-filen där du tänker använda lagring:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Konfigurera en Azure Storage-anslutning
Azure-modulen läser miljövariablerna **AZURE\_STORAGE\_ACCOUNT** och AZURE **\_STORAGE\_ACCESS_KEY** för information som krävs för att ansluta till ditt Azure-lagringskonto. Om dessa miljövariabler inte har angetts måste du ange kontoinformationen innan du använder **Azure::QueueService** med följande kod:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Du kan hämta dessa värden från ett klassiskt eller Resource Manager-baserat lagringskonto på Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till det lagringskonto som du vill använda.
3. Klicka på **Åtkomstnycklar** till höger på bladet Inställningar.
4. Åtkomstnyckel 1 och åtkomstnyckel 2 visas på bladet som öppnas. Du kan använda vilken av nycklarna du vill. 
5. Kopiera nyckeln till Urklipp genom att klicka på kopieringsikonen. 

## <a name="how-to-create-a-queue"></a>Så här skapar du en kö
Följande kod skapar ett **Azure::QueueService-objekt,** vilket gör att du kan arbeta med köer.

```ruby
azure_queue_service = Azure::QueueService.new
```

Använd metoden **create_queue()** för att skapa en kö med det angivna namnet.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Så här infogar du ett meddelande i en kö
Om du vill infoga ett meddelande i en kö använder du metoden **create_message()** för att skapa ett nytt meddelande och lägga till det i kön.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Så här: Kika på nästa meddelande
Du kan granska meddelandet framför en kö utan att ta bort det från kön genom att anropa metoden **Peek\_messages().** Som **standard,\_peek messages()** kikar på ett enda meddelande. Du kan också ange hur många meddelanden du vill granska.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Så här: Dequeue nästa meddelande
Du kan ta bort ett meddelande från en kö i två steg.

1. När du ringer **listmeddelanden()\_** visas nästa meddelande i en kö som standard. Du kan också ange hur många meddelanden du vill få. De meddelanden som returneras från **listmeddelanden()\_** blir osynliga för alla andra kodläsningsmeddelanden från den här kön. Du passerar i synlighet timeout i sekunder som en parameter.
2. Om du vill ta bort meddelandet från kön måste du också ringa **delete_message()**.

Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att när koden inte bearbetar ett meddelande på grund av maskinvaru- eller programvarufel kan en annan instans av koden få samma meddelande och försöka igen. Dina kodanrop **tar bort\_message()** direkt efter att meddelandet har bearbetats.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Så här ändrar du innehållet i ett köat meddelande
Du kan ändra innehållet i ett meddelande direkt i kön. Koden nedan använder metoden **update_message()** för att uppdatera ett meddelande. Metoden returnerar en tuppel som innehåller popup-inleverans av kömeddelandet och ett UTC-datumtidsvärde som representerar när meddelandet ska visas i kön.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Så här: Ytterligare alternativ för att skicka meddelanden
Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö.

1. Du kan få en bunt meddelande.
2. Du kan ställa in en längre eller kortare tidsgränsen för osynlighet, så att koden mer eller mindre tid kan bearbeta varje meddelande.

I följande kodexempel används **metoden list\_messages()** för att hämta 15 meddelanden i ett samtal. Sedan skrivs ut och tas bort varje meddelande. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Så här: Få kölängd
Du kan få en uppskattning av antalet meddelanden i kön. Metoden **\_get\_queue metadata()** uppmanar kötjänsten att returnera det ungefärliga antalet meddelanden och metadata om kön.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Så här tar du bort en kö
Om du vill ta bort en kö och alla meddelanden som finns i den anropar du metoden **delete\_queue()** i köobjektet.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Efterföljande moment
Nu när du har lärt dig grunderna i kölagring följer du de här länkarna för att lära dig mer komplexa lagringsuppgifter.

* Besök [Azure Storage Team Blog](https://blogs.msdn.com/b/windowsazurestorage/)
* Besök [Azure SDK för Ruby-databasen](https://github.com/WindowsAzure/azure-sdk-for-ruby) på GitHub

En jämförelse mellan Azure Queue Service som beskrivs i den här artikeln och Azure Service Bus-köer som beskrivs i artikeln [Hur du använder bussköer](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) för tjänsten finns i [Azure-köer och servicebussköer – Jämfört och kontrasterat](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
