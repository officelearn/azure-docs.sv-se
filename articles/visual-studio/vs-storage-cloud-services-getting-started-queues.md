---
title: Kom igång med queue storage och Visual Studio-anslutna tjänster (molntjänster) | Microsoft Docs
description: Hur du kommer igång med Azure Queue storage i ett molntjänstprojekt i Visual Studio när du har anslutit till ett lagringskonto med hjälp av Visual Studio-anslutna tjänster
services: storage
author: ghogen
manager: douge
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: eb924bcfe3e2545cf6666a19bbb3494c11bc3a48
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42060952"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Komma igång med Azure Queue storage och Visual Studio-anslutna tjänster (cloud services-projekt)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du kommer igång med Azure Queue storage i Visual Studio när du har skapat eller refererar till ett Azure storage-konto i ett cloud services-projekt med hjälp av Visual Studio **Lägg till Connected Services** dialogrutan.

Vi visar dig hur du skapar en kö i kod. Vi kommer också visar hur du utför grundläggande kö åtgärder, till exempel att lägga till, ändra, läsa och ta bort Kömeddelanden. Exemplen är skrivna i C#-kod och använda den [Microsoft Azure Storage-klientbiblioteket för .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Den **Lägg till Connected Services** åtgärden installerar lämpligt NuGet-paket för att komma åt Azure storage i ditt projekt och lägger till anslutningssträngen för lagringskontot i konfigurationsfilerna projekt.

* Se [Kom igång med Azure Queue storage med hjälp av .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) mer information om manipulera köer i kod.
* Se [dokumentation om Storage](https://azure.microsoft.com/documentation/services/storage/) allmän information om Azure Storage.
* Se [dokumentation om Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) allmän information om Azure-molntjänster.
* Se [ASP.NET](http://www.asp.net) för mer information om programmering i ASP.NET-program.

Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett enda kömeddelande kan vara upp till 64 KB stort och en kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto.

## <a name="access-queues-in-code"></a>Åtkomst till köer i kod
För att komma åt köer i Visual Studio Cloud Services-projekt, måste du inkludera följande alla C#-källfilens som har åtkomst till Azure Queue storage.

1. Kontrollera att namnrymdsdeklarationer överst i C#-filen med dessa **med** instruktioner.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. Hämta en **CloudStorageAccount** objekt som representerar information på lagringskontot. Använd följande kod för att hämta den din anslutningssträng för lagring och lagringskontoinformation från konfigurationen av Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. Hämta en **CloudQueueClient** objekt för att referera till kö-objekt i ditt storage-konto.  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. Hämta en **CloudQueue** objektet att referera till en särskild kö.
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**Obs:** använda alla koden ovan framför kod som i följande exempel.

## <a name="create-a-queue-in-code"></a>Skapa en kö i kod
Om du vill skapa kön i kod, ska du lägga till ett anrop till **CreateIfNotExists**.

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>Lägg till ett meddelande till en kö
Om du vill infoga ett meddelande i en befintlig kö, skapa en ny **CloudQueueMessage** objekt och sedan anropa den **AddMessage** metod.

En **CloudQueueMessage** objekt kan skapas från en sträng (i UTF-8-format) eller en bytematris.

Här är ett exempel som infogar meddelandet ”Hello World”.

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>Läs ett meddelande i en kö
Du kan kika på meddelandet först i en kö utan att ta bort det från kön genom att anropa metoden **PeekMessage**.

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>Läsa och ta bort ett meddelande i en kö
Din kod kan ta bort (ta bort kön) ett meddelande från en kö i två steg.

1. Anropa **GetMessage** att hämta nästa meddelande i en kö. Ett meddelande som returneras från **GetMessage** blir osynligt för andra meddelanden som läser kod i den här kön. Som standard är det här meddelandet osynligt i 30 sekunder.
2. Slutför borttagningen av meddelandet från kön genom att anropa **DeleteMessage**.

Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. I följande kod anropar **DeleteMessage** direkt efter att meddelandet har bearbetats.

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Använd ytterligare alternativ för att bearbeta och tar bort Kömeddelanden
Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö.

* Du kan hämta en grupp med meddelanden (upp till 32).
* Du kan ange en tidsgräns för osynlighet längre eller kortare att ge koden mer eller mindre tid att bearbeta varje meddelande. I följande kodexempel används metoden **GetMessage** för att hämta 20 meddelanden i ett anrop. Sedan bearbetas varje meddelande med hjälp av en **foreach**-loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande. Observera att de fem minuterna startar för alla meddelanden samtidigt, vilket betyder att när det här har gått fem minuter sedan anropet till **GetMessage** så blir alla meddelanden som inte har tagits bort synliga igen.

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

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Använda Async-Await-mönstret med vanliga Azure-kö API: er
Det här exemplet visar hur du använder Async-Await-mönstret med vanliga Azure-kö API: er. Exemplet anropar den asynkron versionen av var och en av de angivna metoderna, detta kan ses av den **Async** efter korrigering för varje metod. När en async-metod används async-await mönstret pausar lokala körningen tills anropet har slutförts. Det här innebär att den aktuella tråden att utföra annat arbete som hjälper till att undvika flaskhalsar i prestanda och förbättrar den övergripande svarstiden hos ditt program. Mer information om hur du använder Async-Await-mönstret i .NET finns i [Async och Await (C# och Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

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

