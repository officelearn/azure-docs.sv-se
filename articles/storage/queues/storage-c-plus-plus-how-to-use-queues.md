---
title: Använda Queue storage (C++)-Azure Storage
description: Lär dig hur du använder Queue storage-tjänsten i Azure. Exemplen är skrivna i C++.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: cpp
ms.topic: article
ms.date: 05/11/2017
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 440033233bbd60421cc3245a04544cd04caec6f4
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153128"
---
# <a name="how-to-use-queue-storage-from-c"></a>Använda Queue Storage från C++
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med Azure Queue storage-tjänsten. Exemplen är skrivna i C++ och använder [Azure Storage-klientbiblioteket för C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Scenarier som omfattas är **infoga**, **granskning**, **komma**, och **tar bort** köa meddelanden, samt  **Skapa och ta bort köer**.

> [!NOTE]
> För den här guiden krävs Azure Storage-klientbiblioteket för C++ version 1.0.0 eller senare. Den rekommenderade versionen är Storage-klientbibliotek 2.2.0, som är tillgängligt via [NuGet](https://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Skapa ett C++-program
I den här guiden använder lagringsfunktioner som kan köras i ett C++-program.

För att göra det måste du installera Azure Storage-klientbiblioteket för C++ och skapa ett Azure Storage-konto i din Azure-prenumeration.

Du kan installera Azure Storage-klientbiblioteket för C++ med någon av följande metoder:

* **Linux:** Följ instruktionerna den [Azure Storage-klientbiblioteket för C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) sidan.
* **Windows:** Klicka på **Verktyg > NuGet Package Manager > Package Manager Console** i Visual Studio. Skriv följande kommando i den [NuGet Package Manager-konsolen](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) och tryck på **RETUR**.

```powershell
Install-Package wastorage
```

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurera programmet för att få åtkomst till Queue Storage
Lägg till följande ingår satser överst i filen C++ där du vill använda API: er för Azure storage för att få åtkomst till köer:  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en anslutningssträng för Azure storage
En Azure Storage-klient använder en förvaringsanslutningssträng för att lagra slutpunkter och autentiseringsuppgifter för åtkomst av datahanteringstjänster. När du kör i ett klientprogram kan du måste ange anslutningssträng för lagring i följande format, med hjälp av namnet på ditt lagringskonto och åtkomstnyckel för lagring för lagringskontot som anges i den [Azure-portalen](https://portal.azure.com) för den *AccountName* och *AccountKey* värden. Information om storage-konton och åtkomstnycklar finns [om Azure Storage-konton](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Det här exemplet visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Om du vill testa ditt program i din lokala Windows-dator, kan du använda Microsoft Azure [lagringsemulatorn](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) som installeras med den [Azure SDK](https://azure.microsoft.com/downloads/). Storage-emulatorn är ett verktyg som simulerar Blob, Queue och Table tjänster som är tillgängliga i Azure på din lokala utvecklingsdator. Följande exempel visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen i den lokala lagringsemulatorn:  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Om du vill starta Azure storage-emulatorn, Välj den **starta** knapp eller genom att trycka på den **Windows** nyckel. Börja skriva **Azure Storage-emulatorn**, och välj **Microsoft Azure Storage-emulatorn** från listan med program.

Följande exempel förutsätter att du har använt någon av dessa två metoder för att hämta Azure Storage-anslutningssträngen.

## <a name="retrieve-your-connection-string"></a>Hämta anslutningssträngen
Du kan använda den **cloud_storage_account** klass för att representera kontoinformationen för lagring. Du hämtar informationen om lagringskontot från Azure Storage-anslutningssträngen med hjälp av metoden **parse**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Anvisningar: Skapa en kö
En **cloud_queue_client** objektet kan användas för att hämta referensobjekt för köer. Följande kod skapar en **cloud_queue_client** objekt.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Använd den **cloud_queue_client** objekt för att hämta en referens till den kö som du vill använda. Du kan skapa kön om det inte finns.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Anvisningar: Infoga ett meddelande i en kö
Om du vill infoga ett meddelande i en befintlig kö, först skapa ett nytt **cloud_queue_message**. Därefter anropar den **add_message** metod. En **cloud_queue_message** kan skapas från antingen en sträng eller en **byte** matris. Här är kod som skapar en kö (om den inte finns) och som infogar meddelandet ”Hello World”:

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>Anvisningar: En titt på nästa meddelande
Du kan kika på meddelandet först i en kö utan att ta bort det från kön genom att anropa den **peek_message** metod.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Anvisningar: Ändra innehållet i ett meddelande i kön
Du kan ändra innehållet i ett meddelande direkt i kön. Om meddelandet representerar en arbetsuppgift kan du använda den här funktionen för att uppdatera arbetsuppgiftens status. Följande kod uppdaterar kömeddelandet med nytt innehåll och utökar tidsgränsen för visning med ytterligare 60 sekunder. Koden sparar statusen för arbetsuppgiften som associeras med meddelandet och ger klienten ytterligare en minut att fortsätta arbeta med meddelandet. Du kan använda den här tekniken för att spåra arbetsflöden med flera steg i kömeddelanden, utan att behöva börja om från början om ett bearbetningssteg misslyckas på grund av maskin- eller programvarufel. Normalt ser du till att även antalet omförsök och om meddelandet görs ett nytt fler än n gånger, tar bort den. Detta skyddar mot meddelanden som utlöser ett programfel varje gång de bearbetas.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-de-queue-the-next-message"></a>Anvisningar: Ta bort nästa meddelande från kön
Koden tar bort ett meddelande från en kö i två steg. När du anropar **get_message**, du får nästa meddelande i en kö. Ett meddelande som returneras från **get_message** blir osynligt för andra kod som läser meddelanden från den här kön. Om du vill slutföra borttagningen av meddelandet från kön, måste du även anropa **delete_message**. Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Koden anropar **delete_message** direkt efter att meddelandet har bearbetats.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Anvisningar: Använda fler alternativ för att hämta meddelanden ur kön
Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande. Följande kodexempel används den **get_messages** metod för att hämta 20 meddelanden i ett anrop. Sedan bearbetas varje meddelande med hjälp av en **för** loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande. Observera att de fem minuterna startar för alla meddelanden samtidigt, efter 5 minuter har gått sedan anropet till **get_messages**, alla meddelanden som inte har tagits bort kommer att bli synliga igen.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>Anvisningar: Hämta kölängden
Du kan hämta en uppskattning av antalet meddelanden i en kö. Den **download_attributes** metoden ber kötjänsten att hämta köattributen, inklusive meddelandeantalet. Den **approximate_message_count** metoden hämtar Ungefärligt antal meddelanden i kön.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>Anvisningar: Ta bort en kö
Ta bort en kö och alla meddelanden som finns i den genom att anropa den **delete_queue_if_exists** metoden för köobjektet.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Queue storage kan du följa dessa länkar om du vill veta mer om Azure Storage.

* [Använda Blob Storage från C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Använda Table Storage från C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Lista över Azure Storage-resurser i C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Storage-klientbibliotek för C++-referens](https://azure.github.io/azure-storage-cpp)
* [Azure Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/)
