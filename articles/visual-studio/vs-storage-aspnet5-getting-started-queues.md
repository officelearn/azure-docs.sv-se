---
title: Kom igång med queue storage och Visual Studio-anslutna tjänster (ASP.NET Core) | Microsoft Docs
description: Hur du kommer igång med Azure queue storage i ett ASP.NET Core-projekt i Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 8a91614e7dfb804e6a902967ce60f898ed0e54ad
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999760"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Kom igång med queue storage och Visual Studio-anslutna tjänster (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Den här artikeln beskriver hur du kommer igång med Azure Queue storage i Visual Studio när du har skapat eller refererar till ett Azure storage-konto i ett ASP.NET Core-projekt med hjälp av Visual Studio **Connected Services** funktionen. Den **Connected Services** åtgärden installerar lämpligt NuGet-paket för att komma åt Azure storage i ditt projekt och lägger till anslutningssträngen för lagringskontot i konfigurationsfilerna projekt. (Se [dokumentation om Storage](https://azure.microsoft.com/documentation/services/storage/) allmän information om Azure Storage.)

Azure queue storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop via HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 kilobyte (KB) och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Se även [Kom igång med Azure Queue storage med hjälp av .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) för information om hur du programmässigt hantera köer.

Kom igång genom att först skapa en Azure-kö i ditt storage-konto. Den här artikeln visar sedan hur du skapar en kö i C# och hur du utför grundläggande köåtgärder, till exempel att lägga till, ändra, läsa och ta bort Kömeddelanden.  Koden använder Azure Storage-klientbiblioteket för .NET. Läs mer om ASP.NET [ASP.NET](https://www.asp.net).

Några av de API: erna för Azure Storage är asynkrona och koden i den här artikeln förutsätter att asynkrona metoder som används. Se [asynkron programmering](https://docs.microsoft.com/dotnet/csharp/async) för mer information.

## <a name="access-queues-in-code"></a>Åtkomst till köer i kod

För att komma åt köer i ASP.NET Core-projekt, inkluderar du följande objekt i alla C# källfilen som har åtkomst till Azure-kölagring. Använda för den här koden framför koden i avsnitten som följer.

1. Lägg till nödvändiga `using` instruktioner:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Hämta en `CloudStorageAccount` objekt som representerar information på lagringskontot. Använd följande kod för att hämta anslutningssträngen för lagring och lagringskontoinformation från konfigurationen i Azure service:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta en `CloudQueueClient` objekt för att referera till kö-objekt i ditt storage-konto:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Hämta en `CloudQueue` objektet att referera till en särskild kö:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Skapa en kö i kod

För att skapa Azure-kö i kod, anropa `CreateIfNotExistsAsync`:

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Lägg till ett meddelande till en kö

Om du vill infoga ett meddelande i en befintlig kö, skapa en ny `CloudQueueMessage` objekt och sedan anropa den `AddMessageAsync` metoden. En `CloudQueueMessage` objekt kan skapas från en sträng (i UTF-8-format) eller en bytematris.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Läs ett meddelande i en kö

Du kan kika på meddelandet först i en kö utan att ta bort det från kön genom att anropa den `PeekMessageAsync` metoden:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Läsa och ta bort ett meddelande i en kö

Din kod kan ta bort (ta bort från kön) ett meddelande från en kö i två steg.

1. Anropa `GetMessageAsync` att hämta nästa meddelande i en kö. Ett meddelande som returneras från `GetMessageAsync` blir osynligt för andra kod som läser meddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder.
1. Slutför borttagningen av meddelandet från kön genom att anropa `DeleteMessageAsync`.

Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. I följande kod anropar `DeleteMessageAsync` direkt efter att meddelandet har bearbetats:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Ytterligare alternativ för meddelanden mellan köer

Det finns två sätt att anpassa meddelandehämtningen från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande. Följande kodexempel används den `GetMessages` metod för att hämta 20 meddelanden i ett anrop. Sedan bearbetas varje meddelande med hjälp av en `foreach` loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande. Observera att fem minuters timern startar för alla meddelanden samtidigt, efter fem minuter har gått några meddelanden som inte har tagits bort bli synliga igen.

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Hämta kölängden

Du kan hämta en uppskattning av antalet meddelanden i en kö. Den `FetchAttributes` metoden ber kötjänsten att hämta köattributen, inklusive meddelandeantalet. Den `ApproximateMethodCount` egenskapen returnerar det sista värdet som hämtas av den `FetchAttributes` metoden, utan att kötjänsten.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Använda Async-Await-mönstret med vanliga queue API: er

Det här exemplet visar hur du använder async-await-mönstret med vanliga queue API: er som slutar med `Async`. När en async-metod används, async-await mönstret pausar lokala körningen tills anropet har slutförts. Det här innebär att den aktuella tråden att utföra annat arbete som hjälper till att undvika flaskhalsar i prestanda och förbättrar den övergripande svarstiden hos ditt program.

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Ta bort en kö

Ta bort en kö och alla meddelanden som finns i den genom att anropa den `Delete` metoden för köobjektet:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
