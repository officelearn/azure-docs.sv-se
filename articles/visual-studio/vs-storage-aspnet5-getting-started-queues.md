---
title: Kom igång med Queue Storage och Visual Studio Connected Services (ASP.NET Core) | Microsoft Docs
description: Komma igång med Azure Queue Storage i ett ASP.NET Core projekt i Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: d8e370c6f7c59da8522bb4fb1403b6107a9c9c41
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510976"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Kom igång med Queue Storage och Visual Studio Connected Services (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

Den här artikeln beskriver hur du kommer igång med Azure Queue Storage i Visual Studio när du har skapat eller refererat till ett Azure Storage-konto i ett ASP.NET Core projekt med hjälp av funktionen Visual Studio **Connected Services** . Åtgärden **anslutna tjänster** installerar rätt NuGet-paket för att komma åt Azure Storage i projektet och lägger till anslutnings strängen för lagrings kontot i dina projekt konfigurationsfiler. (Se [lagrings dokumentation](https://azure.microsoft.com/documentation/services/storage/) för allmän information om Azure Storage.)

Azure Queue Storage är en tjänst för att lagra ett stort antal meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda Queue-meddelande kan vara upp till 64 KB i storlek, och en kö kan innehålla miljon tals meddelanden, upp till den totala kapacitets gränsen för ett lagrings konto. Se även [Kom igång med Azure Queue Storage med hjälp av .net](../storage/queues/storage-dotnet-how-to-use-queues.md) om du vill ha mer information om hur du programmatiskt hanterar köer.

Börja med att skapa en Azure-kö i ditt lagrings konto för att komma igång. Den här artikeln visar hur du skapar en kö i C# och hur du utför grundläggande åtgärder för kön, till exempel att lägga till, ändra, läsa och ta bort Kömeddelanden.  Koden använder Azure Storage klient biblioteket för .NET. Mer information om ASP.NET finns i [ASP.net](https://www.asp.net).

Några av de Azure Storage API: erna är asynkrona och koden i den här artikeln förutsätter att asynkrona metoder används. Mer information finns i [asynkron programmering](https://docs.microsoft.com/dotnet/csharp/async) .

## <a name="access-queues-in-code"></a>Åtkomst till köer i kod

Om du vill komma åt köer i ASP.NET Core-projekt inkluderar du följande C# objekt i alla käll filer som har åtkomst till Azure Queue Storage. Använd all den här koden framför koden i de avsnitt som följer.

1. Lägg till de `using` nödvändiga instruktionerna:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Hämta ett `CloudStorageAccount` objekt som representerar lagrings konto informationen. Använd följande kod för att hämta lagrings anslutnings strängen och lagrings konto informationen från Azure-tjänst konfigurationen:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta ett `CloudQueueClient` objekt för att referera till köobjektet i ditt lagrings konto:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Hämta ett `CloudQueue` objekt för att referera till en speciell kö:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Skapa en kö i kod

Om du vill skapa Azure-kön i kod `CreateIfNotExistsAsync`anropar du:

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande i en kö

Om du vill infoga ett meddelande i en befintlig kö, skapar `CloudQueueMessage` du ett nytt objekt och `AddMessageAsync` anropar sedan metoden. Ett `CloudQueueMessage` objekt kan skapas från antingen en sträng (i UTF-8-format) eller en byte mat ris.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Läsa ett meddelande i en kö

Du kan titta på meddelandet överst i en kö utan att ta bort det från kön genom att anropa `PeekMessageAsync` metoden:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Läsa och ta bort ett meddelande i en kö

Din kod kan ta bort (ta bort kön) ett meddelande från en kö i två steg.

1. Anrop `GetMessageAsync` för att hämta nästa meddelande i en kö. Ett meddelande som returnerades från `GetMessageAsync` blir osynligt för all annan kod som läser meddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder.
1. Om du vill slutföra borttagningen av meddelandet från kön `DeleteMessageAsync`anropar du.

Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Följande kod anrop `DeleteMessageAsync` direkt efter att meddelandet har bearbetats:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Ytterligare alternativ för demsmq-meddelanden

Det finns två sätt att anpassa meddelande hämtning från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande. I följande kod exempel används `GetMessages` metoden för att få 20 meddelanden i ett anrop. Sedan bearbetar den varje meddelande med en `foreach` loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande. Observera att timern på fem minuter startar för alla meddelanden samtidigt, så att alla meddelanden som inte har tagits bort blir synliga igen när fem minuter har passerat.

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

Du kan hämta en uppskattning av antalet meddelanden i en kö. `FetchAttributes` Metoden uppmanar Queue Service att hämta attributen för kön, inklusive antalet meddelanden. Egenskapen returnerar det sista värdet `FetchAttributes` som hämtades av metoden, utan att köa tjänsten anropas. `ApproximateMethodCount`

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Använda det asynkrona await-mönstret med vanliga API: er för kö

Det här exemplet visar hur du använder det asynkrona await-mönstret med vanliga kö- `Async`API: er som slutar med. När en async-metod används pausar det asynkrona await-mönstret den lokala körningen tills anropet har slutförts. Med det här beteendet kan den aktuella tråden utföra annat arbete som hjälper till att undvika prestanda Flask halsar och förbättra programmets övergripande svars tid.

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

Om du vill ta bort en kö och alla meddelanden som finns i den `Delete` anropar du metoden i objektet köobjekt:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
