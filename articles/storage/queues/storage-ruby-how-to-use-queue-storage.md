---
title: Hur du använder Queue storage från Ruby - Azure Storage
description: Lär dig hur du använder Azure-Kötjänsten för att skapa och ta bort köer, infoga, hämta och ta bort meddelanden. Exemplen är skrivna i Ruby.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 30a090aeb2d66c732e70a9acce67d5f3374c32fa
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153155"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Använda Queue Storage från Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med Microsoft Azure Queue Storage-tjänsten. Exemplen är skrivna med hjälp av Ruby Azure API.
Scenarier som omfattas är **infoga**, **granskning**, **komma**, och **tar bort** köa meddelanden, samt  **Skapa och ta bort köer**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Skapa en Ruby-program
Skapa en Ruby-programmet. Anvisningar finns i [skapa en Ruby-App i App Service i Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Konfigurera ditt program för att komma åt lagringsutrymme
För att använda Azure storage, måste du ladda ned och använda Ruby azure-paketet, som innehåller en uppsättning bekvämlighet bibliotek som kommunicerar med storage REST-tjänster.

### <a name="use-rubygems-to-obtain-the-package"></a>Hämta paketet med hjälp av RubyGems
1. Använd ett kommandoradsgränssnitt som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix).
2. Skriv ”gem installera azure” i kommandofönstret att installera gem och beroenden.

### <a name="import-the-package"></a>Importera paketet
Använd valfri textredigerare, Lägg till följande överst i filen Ruby där du tänker använda lagring:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Skapa ett Azure Storage-anslutning
Azure-modulen läses miljövariablerna **AZURE\_STORAGE\_konto** och **AZURE\_STORAGE\_ACCESS_KEY** information krävs för att ansluta till Azure storage-kontot. Om de här miljövariablerna inte har angetts måste du ange informationen innan du använder **Azure::QueueService** med följande kod:

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

## <a name="how-to-create-a-queue"></a>Instruktioner: Skapa en kö
Följande kod skapar en **Azure::QueueService** -objektet, vilket gör det möjligt att arbeta med köer.

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

## <a name="how-to-insert-a-message-into-a-queue"></a>Instruktioner: Infoga ett meddelande i en kö
Om du vill infoga ett meddelande i en kö, använda den **create_message()** metod för att skapa ett nytt meddelande och lägga till den i kön.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Instruktioner: En titt på nästa meddelande
Du kan kika på meddelandet först i en kö utan att ta bort det från kön genom att anropa den **peek\_messages()** metod. Som standard **peek\_messages()** peeks på ett enda meddelande. Du kan även ange hur många meddelanden som du vill granska.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Instruktioner: Ta bort från kön nästa meddelande
Du kan ta bort ett meddelande från en kö i två steg.

1. När du anropar **lista\_messages()**, du får nästa meddelande i en kö som standard. Du kan även ange hur många meddelanden som du vill hämta. Meddelanden som returneras från **lista\_messages()** blir osynligt för andra kod som läser meddelanden från den här kön. Du skickar i synlighet tidsgräns i sekunder som en parameter.
2. Om du vill slutföra borttagningen av meddelandet från kön, måste du även anropa **delete_message()**.

Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att när din kod inte kan bearbeta ett meddelande på grund av maskin- eller programvarufel, kan en annan instans av koden hämta samma meddelande och försök igen. Koden anropar **ta bort\_message()** direkt efter att meddelandet har bearbetats.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Instruktioner: Ändra innehållet i ett meddelande i kön
Du kan ändra innehållet i ett meddelande direkt i kön. Koden nedan används den **update_message()** metod för att uppdatera ett meddelande. Metoden returnerar en tuppel som innehåller pop mottagandet av kömeddelandet och ett tidsvärde för UTC-datum som representerar när meddelandet kommer att visas i kön.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Instruktioner: Ytterligare alternativ för meddelanden mellan köer
Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö.

1. Du kan få en batch med meddelandet.
2. Du kan ange en tidsgräns för osynlighet längre eller kortare att ge koden mer eller mindre tid att bearbeta varje meddelande.

Följande kodexempel används den **lista\_messages()** metod för att hämta 15 meddelanden i ett anrop. Sedan skriver ut och tar bort alla meddelanden. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Instruktioner: Hämta kölängden
Du kan få en uppskattning av antalet meddelanden i kön. Den **hämta\_kö\_metadata()** metoden ber kötjänsten att returnera antalet ungefärliga meddelanden och metadata om kön.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Instruktioner: Ta bort en kö
Ta bort en kö och alla meddelanden som finns i den genom att anropa den **ta bort\_queue()** metoden för köobjektet.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i queue storage kan du följa dessa länkar om du vill lära dig mer komplexa lagringsuppgifter.

* Gå till den [Azure Storage-teamets blogg](https://blogs.msdn.com/b/windowsazurestorage/)
* Gå till den [Azure SDK för Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) arkivet på GitHub

En jämförelse mellan Azure-kötjänsten som beskrivs i den här artikeln och Azure Service Bus-köer som beskrivs i den [hur du använder Service Bus-köer](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) artikel, se [Azure-köer och Service Bus-köer – jämfört med och Skillnader](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
