---
title: Komma igång med kölagring med Visual Studio (ASP.NET Core)
description: Komma igång med Azure-kölagring i ett ASP.NET Core-projekt i Visual Studio
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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5cdf6f2644788674df91b533c9444fc88ab30b09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300018"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Komma igång med kölagring och Anslutna Visual Studio-tjänster (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

I den här artikeln beskrivs hur du kommer igång med Azure Queue storage i Visual Studio när du har skapat eller refererat till ett Azure-lagringskonto i ett ASP.NET Core-projekt med hjälp av Funktionen Visual Studio **Connected Services.** Åtgärden **Connected Services** installerar lämpliga NuGet-paket för att komma åt Azure-lagring i projektet och lägger till anslutningssträngen för lagringskontot i projektkonfigurationsfilerna. (Se [Lagringsdokumentation](https://azure.microsoft.com/documentation/services/storage/) för allmän information om Azure Storage.)

Azure kölagring är en tjänst för lagring av ett stort antal meddelanden som kan nås från var som helst i världen via autentiserade samtal med HTTP eller HTTPS. Ett enskilt kömeddelande kan vara upp till 64 kilobyte (KB) i storlek, och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Se även [Komma igång med Azure Queue storage med .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) för information om programmatiskt manipulera köer.

För att komma igång skapar du först en Azure-kö i ditt lagringskonto. Den här artikeln visar sedan hur du skapar en kö i C# och hur du utför grundläggande köåtgärder som att lägga till, ändra, läsa och ta bort kömeddelanden.  Koden använder Azure Storage Client Library för .NET. Mer information om ASP.NET finns [i ASP.NET](https://www.asp.net).

Vissa azure storage API:er är asynkrona och koden i den här artikeln förutsätter att asynkrometoder används. Mer information [finns i Asynkron programmering.](https://docs.microsoft.com/dotnet/csharp/async)

## <a name="access-queues-in-code"></a>Komma åt köer i kod

Om du vill komma åt köer i ASP.NET Core-projekt inkluderar du följande objekt i alla C#-källfiler som har åtkomst till Azure-kölagring. Använd all den här koden framför koden i de avsnitt som följer.

1. Lägg till `using` nödvändiga satser:
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Hämta `CloudStorageAccount` ett objekt som representerar din lagringskontoinformation. Använd följande kod för att hämta information om lagringsanslutningssträng och lagringskonto från Azure-tjänstkonfigurationen:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Hämta `CloudQueueClient` ett objekt som refererar till köobjekten i ditt lagringskonto:

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Hämta `CloudQueue` ett objekt som refererar till en viss kö:

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>Skapa en kö i kod

Om du vill skapa Azure-kön i kod ringer du: `CreateIfNotExistsAsync`

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande i en kö

Om du vill infoga ett meddelande `CloudQueueMessage` i en befintlig `AddMessageAsync` kö skapar du ett nytt objekt och anropar sedan metoden. Ett `CloudQueueMessage` objekt kan skapas antingen från en sträng (i UTF-8-format) eller en bytematris.

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>Läsa ett meddelande i en kö

Du kan granska meddelandet framför en kö utan att ta bort `PeekMessageAsync` det från kön genom att anropa metoden:

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Läsa och ta bort ett meddelande i en kö

Koden kan ta bort (dequeue) ett meddelande från en kö i två steg.

1. Ring `GetMessageAsync` för att få nästa meddelande i en kö. Ett meddelande som `GetMessageAsync` returneras från blir osynligt för alla andra kodläsningsmeddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder.
1. Om du vill ta bort `DeleteMessageAsync`meddelandet från kön ringer du .

Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Följande kodanrop direkt `DeleteMessageAsync` efter att meddelandet har bearbetats:

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>Ytterligare alternativ för att avknämna meddelanden

Det finns två sätt att anpassa meddelandehämtning från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande. I följande kodexempel används `GetMessages` metoden för att hämta 20 meddelanden i ett samtal. Sedan bearbetar varje `foreach` meddelande med hjälp av en loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande. Observera att femminuters timern startar för alla meddelanden samtidigt, så efter fem minuter har gått blir alla meddelanden som inte har tagits bort synliga igen.

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

Du kan hämta en uppskattning av antalet meddelanden i en kö. Metoden `FetchAttributes` ber kötjänsten att hämta köattributen, inklusive antalet meddelanden. Egenskapen `ApproximateMethodCount` returnerar det senaste `FetchAttributes` värdet som hämtats av metoden, utan att anropa kötjänsten.

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Använd mönstret Async-Await med vanliga kö-API:er

Det här exemplet visar hur du använder asynkroniseringsmönstret med vanliga kö-API:er som slutar med `Async`. När en async-metod används pausar asynkroniseringsmönstret lokal körning tills anropet är slutfört. Med det här beteendet kan den aktuella tråden utföra annat arbete som hjälper till att undvika flaskhalsar i prestanda och förbättrar programmets övergripande svarstider.

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

Om du vill ta bort en kö och `Delete` alla meddelanden som finns i den anropar du metoden i köobjektet:

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
