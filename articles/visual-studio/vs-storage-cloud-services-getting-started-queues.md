---
title: Kom igång med queue storage- och Visual Studio anslutna tjänster (molntjänster) | Microsoft Docs
description: Hur du kommer igång med Azure Queue storage i ett molntjänstprojekt i Visual Studio efter anslutning till ett lagringskonto med hjälp av Visual Studio anslutna tjänster
services: storage
author: ghogen
manager: douge
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: c856bfb691c8d1b43822718bbfb86ff2122f4988
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Komma igång med Azure Queue storage och Visual Studio anslutna tjänster (cloud services-projekt)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du kommer igång med Azure Queue storage i Visual Studio när du har skapat eller refererar till ett Azure storage-konto i cloud services-projekt med hjälp av Visual Studio **Lägg till anslutna tjänster** dialogrutan.

Vi ska visa dig hur du skapar en kö i koden. Vi också lära dig hur du utför grundläggande kön åtgärder, till exempel lägga till, ändra, läsa och tar bort Kömeddelanden. Exemplen är skrivna i C#-kod och använda den [Microsoft Azure Storage-klientbibliotek för .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Den **Lägg till anslutna tjänster** åtgärden installerar lämpligt NuGet-paket för att komma åt Azure-lagring i ditt projekt och lägger till anslutningssträngen för lagringskontot konfigurationsfilerna projektet.

* Se [komma igång med Azure Queue storage med hjälp av .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) mer information om manipulering köer i kod.
* Se [Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/) allmän information om Azure Storage.
* Se [molntjänster dokumentationen](https://azure.microsoft.com/documentation/services/cloud-services/) allmän information om Azure-molntjänster.
* Se [ASP.NET](http://www.asp.net) för mer information om programmering i ASP.NET-program.

Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 KB stort och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto.

## <a name="access-queues-in-code"></a>Ha åtkomst till serviceköer i koden
Om du vill komma åt köer i molntjänster i Visual Studio-projekt, måste du inkludera följande objekt är en C#-källfilen som har åtkomst till Azure Queue storage.

1. Se till att namnrymdsdeklarationer överst i C#-filen med dessa **med** instruktioner.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använd följande kod för att få den dina anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Hämta en **CloudQueueClient** objektet att referera till köobjekt i ditt lagringskonto.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Hämta en **CloudQueue** objektet att referera till en särskild kö.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**Obs:** använda alla koden ovan framför koden i följande exempel.

## <a name="create-a-queue-in-code"></a>Skapa en kö i koden
Om du vill skapa kön i koden, Lägg till ett anrop till **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande till en kö
Om du vill infoga ett meddelande i en befintlig kö, skapa en ny **CloudQueueMessage** objekt och sedan anropa den **AddMessage** metod.

En **CloudQueueMessage** objekt kan skapas från en sträng (i UTF-8-format) eller en byte-matris.

Här är ett exempel som infogar meddelandet ”Hello World”.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Läs ett meddelande i en kö
Du kan kika på meddelandet först i en kö utan att ta bort det från kön genom att anropa metoden **PeekMessage**.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Läsa och ta bort ett meddelande i en kö
Koden kan ta bort (Frigör kö) ett meddelande från en kö i två steg.

1. Anropa **GetMessage** att hämta nästa meddelande i en kö. Ett meddelande som returneras från **GetMessage** blir osynligt för andra meddelanden som läser kod i den här kön. Som standard är det här meddelandet osynligt i 30 sekunder.
2. Om du vill slutföra borttagningen av meddelandet från kön, anropa **DeleteMessage**.

Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Följande koden anropar **DeleteMessage** direkt efter att meddelandet har bearbetats.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Använd ytterligare alternativ för att bearbeta och tar bort Kömeddelanden
Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö.

* Du kan hämta en grupp med meddelanden (upp till 32).
* Du kan ange en tidsgräns för osynlighet längre eller kortare för att ge koden mer eller mindre tid att bearbeta klart varje meddelande. I följande kodexempel används metoden **GetMessage** för att hämta 20 meddelanden i ett anrop. Sedan bearbetas varje meddelande med hjälp av en **foreach**-loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande. Observera att de fem minuterna startar för alla meddelanden samtidigt, vilket betyder att när det här har gått fem minuter sedan anropet till **GetMessage** så blir alla meddelanden som inte har tagits bort synliga igen.

Här är ett exempel:

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>Hämta kölängden
Du kan hämta en uppskattning av antalet meddelanden i en kö. **FetchAttributes**-metoden ber kötjänsten att hämta köattributen, inklusive antalet meddelanden. Den **ApproximateMethodCount** egenskapen returnerar det sista värdet som hämtas av den **FetchAttributes** metoden, utan att kötjänsten.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Använda Async-Await-mönstret med vanliga Azure kön API: er
Det här exemplet visar hur du använder Async-Await-mönstret med vanliga Azure kön API: er. Exemplet anropar den asynkron versionen av var och en av de angivna metoderna, detta kan ses av den **asynkrona** efter korrigering för varje metod. När en asynkron metod används async-await mönster pausar lokala körningen tills anropet har slutförts. Det här innebär att den aktuella tråden utföra annat arbete som hjälper till att undvika flaskhalsar och förbättrar den övergripande svarstiden för programmet. Mer information om hur du använder Async-Await-mönstret i .NET finns i [Async och Await (C# och Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

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

