---
title: "Kom igång med queue storage- och Visual Studio anslutna tjänster (ASP.NET Core) | Microsoft Docs"
description: "Hur du kommer igång med Azure queue storage i ASP.NET Core-projekt i Visual Studio"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: kraigb
ms.openlocfilehash: 394344c0e126472b97c2e8f721c8c8d6514a17dc
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Kom igång med queue storage- och Visual Studio anslutna tjänster (ASP.NET kärnor)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du kommer igång med Azure Queue storage i Visual Studio när du har skapat eller refererar till ett Azure storage-konto i ASP.NET Core-projekt med hjälp av Visual Studio **Lägg till anslutna tjänster** dialogrutan. Den **Lägg till anslutna tjänster** åtgärden installerar lämpligt NuGet-paket för att komma åt Azure-lagring i ditt projekt och lägger till anslutningssträngen för lagringskontot konfigurationsfilerna projektet.

Azure queue storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 kilobyte (KB) och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto.

Om du vill komma igång, måste du först skapa en Azure-kö på ditt lagringskonto. Vi ska visa dig hur du skapar en kö i koden. Vi också lära dig hur du utför grundläggande kön åtgärder, till exempel att lägga till, ändra, läsa och tar bort Kömeddelanden. Exemplen är skrivna i C\# code och använda Azure Storage-klientbiblioteket för .NET. Läs mer om ASP.NET [ASP.NET](http://www.asp.net).

**Obs:** några av de API: er som utför anrop till Azure-lagring i ASP.NET Core är asynkron. Se [asynkron programmering med Async och Await](http://msdn.microsoft.com/library/hh191443.aspx) för mer information. Koden nedan förutsätter asynkrona programming metoder som används.

* Se [komma igång med Azure Queue storage med hjälp av .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) mer information om programmässigt manipulering köer.
* Se [Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/) allmän information om Azure Storage.
* Se [molntjänster dokumentationen](https://azure.microsoft.com/documentation/services/cloud-services/) allmän information om Azure-molntjänster.
* Se [ASP.NET](http://www.asp.net) för mer information om programmering i ASP.NET-program.

## <a name="access-queues-in-code"></a>Ha åtkomst till serviceköer i koden
Om du vill komma åt köer i ASP.NET Core projekt måste du inkludera följande objekt till en C# som har åtkomst till Azure queue storage.

1. Se till att namnrymdsdeklarationer överst i C#-filen med dessa **med** instruktioner.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Hämta en **CloudStorageAccount** objekt som representerar din kontoinformation för lagring. Använd följande kod för att få den dina anslutningssträngen för lagring och information om lagringskonto från Azure tjänstkonfiguration.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Hämta en **CloudQueueClient** objektet att referera till köobjekt i ditt lagringskonto.  
   
        // Create the CloudQueueClient object for the storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Hämta en **CloudQueue** objektet att referera till en särskild kö.
   
        // Get a reference to the CloudQueue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**Obs:** använda alla koden ovan framför koden i följande exempel.

### <a name="create-a-queue-in-code"></a>Skapa en kö i koden
Om du vill skapa Azure kön i koden, Lägg till ett anrop till **CreateIfNotExistsAsync**.

    // Create the CloudQueue if it does not exist.
    await messageQueue.CreateIfNotExistsAsync();

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande till en kö
Om du vill infoga ett meddelande i en befintlig kö, skapa en ny **CloudQueueMessage** objekt och sedan anropa den **AddMessageAsync** metod.

En **CloudQueueMessage** objekt kan skapas från en sträng (i UTF-8-format) eller en byte-matris.

Här är ett exempel som infogar meddelandet ”Hello World”.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    await messageQueue.AddMessageAsync(message);

## <a name="read-a-message-in-a-queue"></a>Läs ett meddelande i en kö
Du kan kika på meddelandet först i en kö utan att ta bort det från kön genom att anropa den **PeekMessageAsync** metod.

    // Peek the next message in the queue. 
    CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();


## <a name="read-and-remove-a-message-in-a-queue"></a>Läsa och ta bort ett meddelande i en kö
Koden kan ta bort (status Created) ett meddelande från en kö i två steg.

1. Anropa **GetMessageAsync** att hämta nästa meddelande i en kö. Ett meddelande som returneras från **GetMessageAsync** blir osynligt för andra koder som läsa meddelanden från den här kön. Som standard är det här meddelandet osynligt i 30 sekunder.
2. Om du vill slutföra borttagningen av meddelandet från kön, anropa **DeleteMessageAsync**.

Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Följande koden anropar **DeleteMessageAsync** direkt efter att meddelandet har bearbetats.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

    // Process the message in less than 30 seconds.

    // Then delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);

## <a name="leverage-additional-options-for-dequeuing-messages"></a>Använda fler alternativ för mellan köer meddelanden
Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö.
För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande. I följande kodexempel används metoden **GetMessage** för att hämta 20 meddelanden i ett anrop. Sedan bearbetas varje meddelande med hjälp av en **foreach**-loop. Den anger också tidsgränsen för osynlighet till 5 minuter för varje meddelande. Observera att 5 minuter startar för alla meddelanden samtidigt, efter 5 minuter har gått efter anropet till **GetMessage**, alla meddelanden som inte har tagits bort blir synliga igen.

    // Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
    //   delete each message after processing.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>Hämta kölängden
Du kan hämta en uppskattning av antalet meddelanden i en kö. Den **FetchAttributes** metoden ber kötjänsten att hämta köattributen, inklusive antalet meddelanden. Den **ApproximateMethodCount** egenskapen returnerar det sista värdet som hämtas av den **FetchAttributes** metoden, utan att kötjänsten.

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display the number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Använda Async-Await-mönstret med vanliga queue API: er
Det här exemplet visar hur du använder Async-Await-mönstret med vanliga queue API: er. Exemplet anropar den asynkron versionen av var och en av de angivna metoderna. Detta kan ses av asynkrona efter korrigering för varje metod. När en asynkron metod används pausar Async-Await-mönstret lokala körningen tills anropet har slutförts. Det här innebär att den aktuella tråden utföra annat arbete som hjälper till att undvika flaskhalsar och förbättrar den övergripande svarstiden för programmet. Mer information om hur du använder Async-Await-mönstret i .NET finns [Async och Await (C# och Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

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
## <a name="delete-a-queue"></a>Ta bort en kö
Om du vill ta bort en kö och alla meddelanden i den anropar du **Delete**-metoden för köobjektet.

    // Delete the queue.
    messageQueue.Delete();


## <a name="next-steps"></a>Nästa steg
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

