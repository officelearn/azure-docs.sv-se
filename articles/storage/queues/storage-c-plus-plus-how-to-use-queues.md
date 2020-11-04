---
title: Så här använder du Queue Storage (C++) – Azure Storage
description: Lär dig hur du använder Queue Storage-tjänsten i Azure. Exempel skrivs i C++.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: f31a883a2b10f37d6a4a7a91fff37739e340ac60
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348856"
---
# <a name="how-to-use-queue-storage-from-c"></a>Så använder du Queue Storage från C++

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt

I den här guiden får du lära dig hur du utför vanliga scenarier med Azure Queue Storage-tjänsten. Exemplen är skrivna i C++ och använder [Azure Storage-klientbiblioteket för C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). De scenarier som beskrivs är att **Infoga** , **Granska** , **Hämta** och **ta bort** Kömeddelanden, samt **skapa och ta bort köer**.

> [!NOTE]
> För den här guiden krävs Azure Storage-klientbiblioteket för C++ version 1.0.0 eller senare. Den rekommenderade versionen är Storage-klientbibliotek 2.2.0, som är tillgängligt via [NuGet](https://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp/).

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Skapa ett C++-program

I den här guiden ska du använda lagrings funktioner som kan köras i ett C++-program.

För att göra det måste du installera Azure Storage-klientbiblioteket för C++ och skapa ett Azure Storage-konto i din Azure-prenumeration.

Du kan installera Azure Storage-klientbiblioteket för C++ med någon av följande metoder:

- **Linux:** Följ anvisningarna i avsnittet [Azure Storage klient bibliotek för C++ viktigt: komma igång på Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) -sidan.
- **Windows:** Använd [vcpkg](https://github.com/microsoft/vcpkg) som beroende hanterare i Windows. Starta vcpkg genom att följa [snabb](https://github.com/microsoft/vcpkg#quick-start) starten. Använd sedan följande kommando för att installera biblioteket:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Du hittar en guide för hur du skapar käll koden och exporterar till NuGet i [README](https://github.com/Azure/azure-storage-cpp#download--install) -filen.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurera ditt program till att få åtkomst till Queue Storage

Lägg till följande include-instruktioner överst i C++-filen där du vill använda Azure Storage-API: er för att komma åt köer:

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en anslutnings sträng för Azure Storage

En Azure Storage-klient använder en förvaringsanslutningssträng för att lagra slutpunkter och autentiseringsuppgifter för åtkomst av datahanteringstjänster. När du kör i ett klient program måste du ange lagrings anslutnings strängen i följande format, med namnet på ditt lagrings konto och lagrings åtkomst nyckeln för det lagrings konto som anges i [Azure Portal](https://portal.azure.com) för värdena *AccountName* och *AccountKey* . Information om lagrings konton och åtkomst nycklar finns i [om Azure Storage-konton](../common/storage-account-create.md?toc=%252fazure%252fstorage%252fqueues%252ftoc.json). Det här exemplet visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen:

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Om du vill testa programmet på den lokala Windows-datorn kan du använda [Azurite Storage-emulatorn](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Azurite är ett verktyg som simulerar blob-och Queue Services som är tillgängliga i Azure på din lokala utvecklings dator. Följande exempel visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen i den lokala lagringsemulatorn:

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Information om hur du startar Azurite finns i [använda Azurite-emulatorn för lokal Azure Storage utveckling](../common/storage-use-azurite.md).

Följande exempel förutsätter att du har använt någon av dessa två metoder för att hämta Azure Storage-anslutningssträngen.

## <a name="retrieve-your-connection-string"></a>Hämta anslutningssträngen

Du kan använda **cloud_storage_account** -klassen för att representera din lagrings konto information. Du hämtar informationen om lagringskontot från Azure Storage-anslutningssträngen med hjälp av metoden **parse**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Gör så här: skapa en kö

Med ett **cloud_queue_client** objekt kan du hämta referens objekt för köer. Följande kod skapar ett **cloud_queue_client** -objekt.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Använd **cloud_queue_client** -objektet för att få en referens till den kö som du vill använda. Du kan skapa kön om den inte finns.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Så här gör du: infoga ett meddelande i en kö

Om du vill infoga ett meddelande i en befintlig kö skapar du först ett nytt **cloud_queue_message**. Anropa sedan metoden **add_message** . En **cloud_queue_message** kan skapas antingen från en sträng eller en **byte** mat ris. Här är kod som skapar en kö (om den inte finns) och som infogar meddelandet ”Hello World”:

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

## <a name="how-to-peek-at-the-next-message"></a>Gör så här: granska vid nästa meddelande

Du kan titta på meddelandet överst i en kö utan att ta bort det från kön genom att anropa metoden **peek_message** .

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Så här gör du: ändra innehållet i ett köat meddelande

Du kan ändra innehållet i ett meddelande direkt i kön. Om meddelandet representerar en arbetsuppgift kan du använda den här funktionen för att uppdatera arbetsuppgiftens status. Följande kod uppdaterar kömeddelandet med nytt innehåll och utökar tidsgränsen för visning med ytterligare 60 sekunder. Koden sparar statusen för arbetsuppgiften som associeras med meddelandet och ger klienten ytterligare en minut att fortsätta arbeta med meddelandet. Du kan använda den här tekniken för att spåra arbetsflöden med flera steg i kömeddelanden, utan att behöva börja om från början om ett bearbetningssteg misslyckas på grund av maskin- eller programvarufel. Normalt räknar du även antalet omförsök och tar bort meddelandet om fler än n försök misslyckas. Detta skyddar mot meddelanden som utlöser ett programfel varje gång de bearbetas.

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

## <a name="how-to-de-queue-the-next-message"></a>Så här gör du: ta bort nästa meddelande i kön

Koden tar bort ett meddelande från en kö i två steg. När du anropar **get_message** får du nästa meddelande i en kö. Ett meddelande som returnerades från **get_message** blir osynligt för all annan kod som läser meddelanden från den här kön. Om du vill slutföra borttagningen av meddelandet från kön måste du också anropa **delete_message**. Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Dina kod anrop **delete_message** direkt efter att meddelandet har bearbetats.

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

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Så här gör du: Använd ytterligare alternativ för meddelanden i kön

Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande. I följande kod exempel används metoden **get_messages** för att få 20 meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en **for** -slinga. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande. Observera att 5 minuter startar för alla meddelanden samtidigt, så att när fem minuter har gått sedan anropet till **get_messages** visas alla meddelanden som inte har tagits bort.

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

## <a name="how-to-get-the-queue-length"></a>Så här gör du: Hämta Kölängd

Du kan hämta en uppskattning av antalet meddelanden i en kö. Metoden **download_attributes** ber kötjänst att hämta attributen för kön, inklusive antalet meddelanden. Metoden **approximate_message_count** hämtar det ungefärliga antalet meddelanden i kön.

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

## <a name="how-to-delete-a-queue"></a>Så här gör du: ta bort en kö

Om du vill ta bort en kö och alla meddelanden som finns i den anropar du metoden **delete_queue_if_exists** på objektet köobjekt.

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

Nu när du har lärt dig grunderna i Queue Storage kan du följa dessa länkar om du vill veta mer om Azure Storage.

- [Använda Blob Storage från C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
- [Använda Table Storage från C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [Visa Azure Storage resurser i C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Lagrings klient bibliotek för C++-referens](https://azure.github.io/azure-storage-cpp)
- [Azure Storage dokumentation](https://azure.microsoft.com/documentation/services/storage/)
