---
title: Komma igång med kölagring med Visual Studio (molntjänster)
description: Komma igång med Azure Queue storage i ett molntjänstprojekt i Visual Studio efter anslutning till ett lagringskonto med Hjälp av Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 603bb2b9a862ad4ed2cbde63e2d82b9a82fbeaa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298790"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Komma igång med Azure Queue storage och Visual Studio-anslutna tjänster (molntjänstprojekt)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
I den här artikeln beskrivs hur du kommer igång med Azure Queue storage i Visual Studio när du har skapat eller refererat till ett Azure-lagringskonto i ett molntjänstprojekt med hjälp av dialogrutan **Lägg till anslutna tjänster** i Visual Studio.

Vi visar dig hur du skapar en kö i kod. Vi visar också hur du utför grundläggande köåtgärder, till exempel lägga till, ändra, läsa och ta bort kömeddelanden. Exemplen skrivs i C#-kod och använder [Microsoft Azure Storage Client Library för .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Åtgärden **Lägg till anslutna tjänster** installerar lämpliga NuGet-paket för att komma åt Azure-lagring i projektet och lägger till anslutningssträngen för lagringskontot i projektkonfigurationsfilerna.

* Se [Komma igång med Azure Queue storage med .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) för mer information om hur du ändrar köer i kod.
* Mer information om Azure Storage finns i Dokumentation för [lagring av](https://azure.microsoft.com/documentation/services/storage/) lagring.
* Mer information om Azure-molntjänster finns i Dokumentationen till [Molntjänster](https://azure.microsoft.com/documentation/services/cloud-services/) för Molntjänster.
* Mer information om programmering ASP.NET program finns [i ASP.NET.](https://www.asp.net)

Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 KB stort och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto.

## <a name="access-queues-in-code"></a>Komma åt köer i kod
Om du vill komma åt köer i Visual Studio Cloud Services-projekt måste du inkludera följande objekt i alla C#-källfiler som kommer åt Azure Queue storage.

1. Kontrollera att namnområdesdeklarationerna högst upp i C#-filen innehåller dessa **med hjälp av** satser.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Hämta ett **CloudStorageAccount-objekt** som representerar din lagringskontoinformation. Använd följande kod för att hämta din lagringsanslutningssträng och lagringskontoinformation från Azure-tjänstkonfigurationen.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Hämta ett **CloudQueueClient-objekt** för att referera till köobjekten i ditt lagringskonto.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Hämta ett **CloudQueue-objekt** för att referera till en viss kö.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**OBS:** Använd alla ovanstående kod framför koden i följande exempel.

## <a name="create-a-queue-in-code"></a>Skapa en kö i kod
Om du vill skapa kön i kod lägger du bara till ett anrop i **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande i en kö
Om du vill infoga ett meddelande i en befintlig kö skapar du ett nytt **CloudQueueMessage-objekt** och anropar sedan metoden **AddMessage.**

Ett **CloudQueueMessage-objekt** kan skapas från antingen en sträng (i UTF-8-format) eller en bytematris.

Här är ett exempel som infogar meddelandet "Hello, World".

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Läsa ett meddelande i en kö
Du kan kika på meddelandet först i en kö utan att ta bort det från kön genom att anropa metoden **PeekMessage**.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Läsa och ta bort ett meddelande i en kö
Koden kan ta bort (de-kö) ett meddelande från en kö i två steg.

1. Ring **GetMessage** för att få nästa meddelande i en kö. Ett meddelande som returneras från **GetMessage** blir osynligt för andra meddelanden som läser kod i den här kön. Som standard är det här meddelandet osynligt i 30 sekunder.
2. Om du vill ta bort meddelandet från kön anropar du **DeleteMessage**.

Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Följande kodanrop **Ta bortMessage** direkt efter att meddelandet har bearbetats.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Använda ytterligare alternativ för att bearbeta och ta bort kömeddelanden
Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö.

* Du kan få en bunt meddelanden (upp till 32).
* Du kan ställa in en längre eller kortare tidsgränsen för osynlighet, så att koden mer eller mindre tid kan bearbeta varje meddelande. I följande kodexempel används metoden **GetMessage** för att hämta 20 meddelanden i ett anrop. Sedan bearbetas varje meddelande med hjälp av en **foreach**-loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande. Observera att de fem minuterna startar för alla meddelanden samtidigt, vilket betyder att när det här har gått fem minuter sedan anropet till **GetMessage** så blir alla meddelanden som inte har tagits bort synliga igen.

Här är ett exempel:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Hämta kölängden
Du kan hämta en uppskattning av antalet meddelanden i en kö. **FetchAttributes**-metoden ber kötjänsten att hämta köattributen, inklusive antalet meddelanden. Egenskapen **ApproximateMethodCount** returnerar det senaste värdet som hämtats av metoden **FetchAttributes,** utan att anropa kötjänsten.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Använd Async-Await-mönstret med vanliga Azure Queue API:er
Det här exemplet visar hur du använder mönstret Async-Await med vanliga Azure Queue API:er. Exemplet anropar asynkronversionen av var och en av de angivna metoderna, detta kan ses av **Async** efter fixeringen av varje metod. När en async-metod används pausar asynkroniseringsmönstret lokal körning tills anropet är klart. Detta gör att den aktuella tråden att göra annat arbete som hjälper till att undvika flaskhalsar i prestanda och förbättrar den övergripande svarstiden för ditt program. Mer information om hur du använder Async-Await-mönstret i .NET finns i [Async och Await (C# och Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>Ta bort en kö
Om du vill ta bort en kö och alla meddelanden i den anropar du **Delete**-metoden för köobjektet.

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

