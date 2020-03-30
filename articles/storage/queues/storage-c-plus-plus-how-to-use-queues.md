---
title: Så här använder du kölagring (C++) – Azure Storage
description: Lär dig hur du använder tjänsten Kölagring i Azure. Prover skrivs i C++.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 4fe543010df9514cb2b22c56482a4b592574e917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941768"
---
# <a name="how-to-use-queue-storage-from-c"></a>Så använder du Queue Storage från C++
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga scenarier med hjälp av Azure Queue storage-tjänsten. Exemplen är skrivna i C++ och använder [Azure Storage-klientbiblioteket för C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Scenarierna som omfattas omfattar **att infoga,** **kika,** **hämta**och **ta bort** kömeddelanden samt skapa och ta **bort köer**.

> [!NOTE]
> För den här guiden krävs Azure Storage-klientbiblioteket för C++ version 1.0.0 eller senare. Den rekommenderade versionen är Storage-klientbibliotek 2.2.0, som är tillgängligt via [NuGet](https://www.nuget.org/packages/wastorage) eller [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Skapa ett C++-program
I den här guiden använder du lagringsfunktioner som kan köras i ett C++-program.

För att göra det måste du installera Azure Storage-klientbiblioteket för C++ och skapa ett Azure Storage-konto i din Azure-prenumeration.

Du kan installera Azure Storage-klientbiblioteket för C++ med någon av följande metoder:

* **Linux:** Följ instruktionerna i [Azure Storage Client Library for C++ README: Komma igång på Linux-sidan.](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux)
* **Windows:** I Windows använder du [vcpkg](https://github.com/microsoft/vcpkg) som beroendehanterare. Följ [snabbstarten](https://github.com/microsoft/vcpkg#quick-start) för att initiera vcpkg. Använd sedan följande kommando för att installera biblioteket:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Du kan hitta en guide för hur du skapar källkoden och exporterar till NuGet i [README-filen.](https://github.com/Azure/azure-storage-cpp#download--install)

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurera ditt program för åtkomst till kölagring
Lägg till följande inkludera-satser högst upp i C++-filen där du vill använda Azure storage API:er för att komma åt köer:  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en Azure-lagringsanslutningssträng
En Azure Storage-klient använder en förvaringsanslutningssträng för att lagra slutpunkter och autentiseringsuppgifter för åtkomst av datahanteringstjänster. När du kör i ett klientprogram måste du ange lagringsanslutningssträngen i följande format med namnet på ditt lagringskonto och lagringsåtkomstnyckeln för lagringskontot som anges i [Azure Portal](https://portal.azure.com) för *accountName-* och *AccountKey-värdena.* Information om lagringskonton och åtkomstnycklar finns i [Om Azure Storage Accounts](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Det här exemplet visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Om du vill testa ditt program på din lokala Windows-dator kan du använda Microsoft [Azure-lagringsemulatorn](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) som är installerad med [Azure SDK](https://azure.microsoft.com/downloads/). Lagringsemulatorn är ett verktyg som simulerar Blob-, kö- och tabelltjänsterna som är tillgängliga i Azure på din lokala utvecklingsdator. Följande exempel visar hur du kan deklarera ett statiskt fält för lagring av anslutningssträngen i den lokala lagringsemulatorn:  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Om du vill starta Azure-lagringsemulatorn väljer du **Start-knappen** eller trycker på Windows-tangenten. **Windows** Börja skriva **Azure Storage Emulator**och välj **Microsoft Azure Storage Emulator** i listan över program.

Följande exempel förutsätter att du har använt någon av dessa två metoder för att hämta Azure Storage-anslutningssträngen.

## <a name="retrieve-your-connection-string"></a>Hämta anslutningssträngen
Du kan använda **klassen cloud_storage_account** för att representera din lagringskontoinformation. Du hämtar informationen om lagringskontot från Azure Storage-anslutningssträngen med hjälp av metoden **parse**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Så här skapar du en kö
Med **ett cloud_queue_client** objekt kan du hämta referensobjekt för köer. Följande kod skapar ett **cloud_queue_client** objekt.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Använd **det cloud_queue_client** objektet för att få en referens till den kö du vill använda. Du kan skapa kön om den inte finns.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Så här infogar du ett meddelande i en kö
Om du vill infoga ett meddelande i en befintlig kö skapar du först ett nytt **cloud_queue_message**. Anropa sedan **add_message** metoden. En **cloud_queue_message** kan skapas från antingen en sträng eller en **bytematris.** Här är kod som skapar en kö (om den inte finns) och som infogar meddelandet ”Hello World”:

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

## <a name="how-to-peek-at-the-next-message"></a>Så här: Kika på nästa meddelande
Du kan granska meddelandet framför en kö utan att ta bort **peek_message** det från kön genom att anropa peek_message-metoden.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Så här ändrar du innehållet i ett köat meddelande
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

## <a name="how-to-de-queue-the-next-message"></a>Så här köar du nästa meddelande
Koden tar bort ett meddelande från en kö i två steg. När du ringer **get_message**får du nästa meddelande i en kö. Ett meddelande som returneras från **get_message** blir osynligt för andra kodläsningsmeddelanden från den här kön. Om du vill ta bort meddelandet från kön måste du också ringa **delete_message**. Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Dina kodanrop **delete_message** direkt efter att meddelandet har bearbetats.

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

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Så här: Utnyttja ytterligare alternativ för deköingsmeddelanden
Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande. I följande kodexempel används **get_messages** metod för att hämta 20 meddelanden i ett samtal. Sedan bearbetar varje **for** meddelande med hjälp av en för-loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande. Observera att 5 minuter börjar för alla meddelanden samtidigt, så efter 5 minuter har gått sedan samtalet till **get_messages**, alla meddelanden som inte har tagits bort kommer att bli synliga igen.

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

## <a name="how-to-get-the-queue-length"></a>Så här: Få kölängden
Du kan hämta en uppskattning av antalet meddelanden i en kö. Metoden **download_attributes** ber kötjänsten att hämta köattributen, inklusive antalet meddelanden. Metoden **approximate_message_count** får det ungefärliga antalet meddelanden i kön.

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

## <a name="how-to-delete-a-queue"></a>Så här tar du bort en kö
Om du vill ta bort en kö och alla meddelanden som finns i den anropar du **delete_queue_if_exists** metoden i köobjektet.

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
Nu när du har lärt dig grunderna i kölagring följer du dessa länkar för att lära dig mer om Azure Storage.

* [Så här använder du Blob Storage från C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Så här använder du Tabelllagring från C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Lista Azure Storage-resurser i C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Lagringsklientbibliotek för C++-referens](https://azure.github.io/azure-storage-cpp)
* [Azure Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/)
