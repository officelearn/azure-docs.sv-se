---
title: "Använda Queue storage från Ruby | Microsoft Docs"
description: "Lär dig hur du använder Azure-Kötjänsten för att skapa och ta bort köer, infoga, hämta och ta bort meddelanden. Exempel som skrivits i Ruby."
services: storage
documentationcenter: ruby
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 59c2d81b-db9c-46ee-ade2-2f0caae6b1e6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 4a9d431e7bd1f204b9ba21b90d9fd6a0894d5d2d
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Använda Queue Storage från Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av tjänsten Microsoft Azure Queue Storage. Exemplen är skrivna med hjälp av Azure API för Ruby.
Scenarier som tas upp inkluderar **infoga**, **granskning**, **komma**, och **bort** kö meddelanden, samt **skapa och ta bort köer**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Skapa ett Ruby-program
Skapa ett Ruby program. Instruktioner finns i [Ruby på spår webbprogram på en Azure VM](../../virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-access-storage"></a>Konfigurera ditt program för att komma åt lagringsutrymme
Om du vill använda Azure storage, måste du hämtar och använder Ruby azure-paketet, som innehåller en uppsättning bekvämlighet bibliotek som kommunicerar med storage REST-tjänster.

### <a name="use-rubygems-to-obtain-the-package"></a>Använda RubyGems för att hämta paketet
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac), eller **Bash** (Unix).
2. Skriv ”symbolen installera azure” i kommandofönstret att installera symbolen och beroenden.

### <a name="import-the-package"></a>Importera paketet
Använda valfri textredigerare, lägger du till följande upp i filen Ruby där du tänker använda lagring:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Skapa ett Azure Storage-anslutning
Azure-modulen läses miljövariablerna **AZURE\_lagring\_konto** och **AZURE\_lagring\_ACCESS_KEY** information som krävs för att ansluta till Azure storage-konto. Om de här miljövariablerna inte har angetts måste du ange kontoinformation innan du använder **Azure::QueueService** med följande kod:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Du kan hämta dessa värden från en klassiska eller Resource Manager storage-konto i Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till storage-konto som du vill använda.
3. I bladet inställningar till höger klickar du på **åtkomstnycklar**.
4. I åtkomst bladet nycklar som visas, visas den åtkomstnyckel 1 och åtkomstnyckel 2. Du kan använda någon av dessa. 
5. Klicka på Kopiera-ikonen för att kopiera nyckeln till Urklipp. 

## <a name="how-to-create-a-queue"></a>Så här: Skapa en kö
Följande kod skapar en **Azure::QueueService** -objektet, vilket gör att du kan arbeta med köer.

```ruby
azure_queue_service = Azure::QueueService.new
```

Använd den **create_queue()** metod för att skapa en kö med det angivna namnet.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Så här: Infoga ett meddelande i en kö
Om du vill infoga ett meddelande i en kö, använda den **create_message()** metoden för att skapa ett nytt meddelande och lägga till den i kön.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Så här: Granska nästa meddelande
Du kan kika på meddelandet först i en kö utan att ta bort det från kön genom att anropa den **titt\_messages()** metod. Som standard **titt\_messages()** peeks på ett enda meddelande. Du kan även ange hur många meddelanden som du vill granska.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Så här: Status Created nästa meddelande
Du kan ta bort ett meddelande från en kö i två steg.

1. När du anropar **lista\_messages()**, du får nästa meddelande i en kö som standard. Du kan även ange hur många meddelanden som du vill hämta. Meddelanden som returneras från **lista\_messages()** blir osynligt för andra koder som läsa meddelanden från den här kön. Du skickar i synlighet tidsgränsen i sekunder som en parameter.
2. Om du vill slutföra borttagningen av meddelandet från kön, måste du också anropa **delete_message()**.

Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att när din kod inte kan bearbeta ett meddelande på grund av maskinvara eller programvara, kan en annan instans av koden hämta samma meddelande och försök igen. Koden anropar **ta bort\_message()** direkt efter att meddelandet har bearbetats.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Så här: Ändra innehållet i ett meddelande i kön
Du kan ändra innehållet i ett meddelande direkt i kön. Koden nedan används den **update_message()** metod för att uppdatera ett meddelande. Metoden returnerar en tuppel som innehåller pop mottagande av kömeddelandet och ett tidsvärde för UTC-datum som representerar när meddelandet visas i kön.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Så här: Ytterligare alternativ för mellan köer meddelanden
Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö.

1. Du kan få en batch med meddelandet.
2. Du kan ange en tidsgräns för osynlighet längre eller kortare för att ge koden mer eller mindre tid att bearbeta klart varje meddelande.

Följande kodexempel används den **lista\_messages()** metod för att hämta 15 meddelanden i ett anrop. Sedan skriver ut och tar bort varje meddelande. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Så här: Hämta kölängden
Du kan få en uppskattning av antalet meddelanden i kön. Den **hämta\_kön\_metadata()** metoden ber kötjänsten att returnera antalet ungefärliga meddelanden och metadata om kön.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Så här: Ta bort en kö
Om du vill ta bort en kö och alla meddelanden som finns i den anropar den **ta bort\_queue()** metoden för köobjektet.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i queue storage följa dessa länkar för att lära dig mer komplexa lagringsuppgifter.

* Besök den [Azure Storage-teamets blogg](http://blogs.msdn.com/b/windowsazurestorage/)
* Besök den [Azure SDK för Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) databasen på GitHub

En jämförelse mellan Azure-kötjänsten som beskrivs i den här artikeln och Azure Service Bus-köer som beskrivs i den [hur du använder Service Bus-köer](/develop/ruby/how-to-guides/service-bus-queues/) artikel, se [Azure köer och Service Bus-köer - skillnad från och med](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)