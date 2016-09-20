<properties
    pageTitle="Komma igång med Azure Queue Storage med hjälp av .NET | Microsoft Azure"
    description="Azure Queues ger tillförlitliga, asynkrona meddelandefunktioner mellan programkomponenter. Med hjälp av molnmeddelanden kan programkomponenter skalas separat."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="07/26/2016"
    ms.author="gusapost"/>

# Komma igång med Azure Queue Storage med hjälp av .NET

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## Översikt

Azure Queue Storage innehåller molnmeddelandehantering mellan programkomponenter. När program utformas för skalning är programkomponenterna ofta fristående, så att de kan skalas oberoende av varandra. Queue Storage är en asynkron meddelandelösning för kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

### Om den här självstudiekursen

I den här kursen lär du dig hur du skriver .NET-kod för några vanliga scenarier med hjälp av Azure Queue Storage. Du lär dig bland annat hur du skapar och tar bort köer och hur du lägger till, läser och tar bort kömeddelanden.

**Uppskattad tidsåtgång:** 45 minuter

**Krav:**

- [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
- [Azure Storage-klientbibliotek för .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Azure Configuration Manager för .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
- Ett [Azure Storage-konto](storage-create-storage-account.md#create-a-storage-account)


[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### Lägga till namnrymdsdeklarationer

Lägg till följande `using`-instruktioner överst i `program.cs`-filen:

    using Microsoft.Azure; // Namespace for CloudConfigurationManager 
    using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
    using Microsoft.WindowsAzure.Storage.Queue; // Namespace for Queue storage types

### Parsa anslutningssträngen

[AZURE.INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### Skapa Queue-tjänstklienten

Med hjälp av **CloudQueueClient**-klassen kan du hämta köer som lagras i Queue Storage. Här är ett sätt att skapa tjänstklienten:

    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Nu är det dags att skriva kod som läser data från och skriver data till Queue Storage.

## Skapa en kö

Det här exemplet illustrerar hur du skapar en kö om den inte redan finns:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a container.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## Infoga ett meddelande i en kö

Om du vill infoga ett meddelande i en befintlig kö börjar du med att skapa ett nytt **CloudQueueMessage**. Därefter anropar du **AddMessage**-metoden. Du kan skapa ett **CloudQueueMessage** antingen från en sträng (i UTF-8-format) eller från en **byte**-matris. Här är kod som skapar en kö (om den inte finns) och som infogar meddelandet ”Hello World”:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## En titt på nästa meddelande

Du kan kika på meddelandet först i en kö utan att ta bort det från kön genom att anropa metoden **PeekMessage**.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

    // Display message.
    Console.WriteLine(peekedMessage.AsString);

## Ändra innehållet i ett meddelande i kön

Du kan ändra innehållet i ett meddelande direkt i kön. Om meddelandet representerar en arbetsuppgift kan du använda den här funktionen för att uppdatera arbetsuppgiftens status. Följande kod uppdaterar kömeddelandet med nytt innehåll och utökar tidsgränsen för visning med ytterligare 60 sekunder. Koden sparar statusen för arbetsuppgiften som associeras med meddelandet och ger klienten ytterligare en minut att fortsätta arbeta med meddelandet. Du kan använda den här tekniken för att spåra arbetsflöden med flera steg i kömeddelanden, utan att behöva börja om från början om ett bearbetningssteg misslyckas på grund av maskin- eller programvarufel. Normalt räknar du även antalet omförsök och tar bort meddelandet om fler än *n* försök misslyckas. Detta skyddar mot meddelanden som utlöser ett programfel varje gång de bearbetas.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.");
    queue.UpdateMessage(message,
        TimeSpan.FromSeconds(60.0),  // Make it visible for another 60 seconds.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## Ta bort nästa meddelande från kön

Koden tar bort ett meddelande från en kö i två steg. När du anropar **GetMessage** hämtas nästa meddelande i en kö. Ett meddelande som returneras från **GetMessage** blir osynligt för andra meddelanden som läser kod i den här kön. Som standard är det här meddelandet osynligt i 30 sekunder. För att slutföra borttagningen av meddelandet från kön måste du även anropa **DeleteMessage**. Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Koden anropar **DeleteMessage** direkt efter att meddelandet har bearbetats.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## Använda Async-Await-mönstret med vanliga Queue Storage-API:er

Det här exemplet illustrerar hur du använder Async-Await-mönstret med vanliga Queue Storage-API:er. Exemplet anropar den asynkrona versionen av var och en av de angivna metoderna, vilket du ser på *Async*-suffixet för varje metod. När en async-metod används pausar async-await-mönstret den lokala körningen tills anropet har slutförts. Detta gör att den aktuella tråden kan arbeta med annat, vilket innebär att flaskhalsar kan undvikas samtidigt som programmets svarstider förbättras. Mer information om hur du använder Async-Await-mönstret i .NET finns i [Async och Await (C# och Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create the queue if it doesn't already exist
    if(await queue.CreateIfNotExistsAsync())
    {
        Console.WriteLine("Queue '{0}' Created", queue.Name);
    }
    else
    {
        Console.WriteLine("Queue '{0}' Exists", queue.Name);
    }

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await queue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await queue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## Använda fler alternativ för att hämta meddelanden ur kön

Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö.
För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande. I följande kodexempel används metoden **GetMessage** för att hämta 20 meddelanden i ett anrop. Sedan bearbetas varje meddelande med hjälp av en **foreach**-loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande. Observera att de fem minuterna startar för alla meddelanden samtidigt, vilket betyder att när det här har gått fem minuter sedan anropet till **GetMessage** så blir alla meddelanden som inte har tagits bort synliga igen.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## Hämta kölängden

Du kan hämta en uppskattning av antalet meddelanden i en kö. **FetchAttributes**-metoden ber kötjänsten att hämta köattributen, inklusive antalet meddelanden. Egenskapen **ApproximateMessageCount** returnerar det sista värdet som hämtas av **FetchAttributes**-metoden, utan att kötjänsten anropas.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Fetch the queue attributes.
    queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Ta bort en kö

Om du vill ta bort en kö och alla meddelanden i den anropar du **Delete**-metoden för köobjektet.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## Nästa steg

Nu när du har lärt dig grunderna i Queue Storage kan du följa dessa länkar för att lära dig mer om komplexa lagringsuppgifter.

- Fullständig information om tillgängliga API:er finns i referensdokumentationen för kötjänsten:
    - [Referens för Azure Storage-klientbibliotek för .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [Referens för REST-API](http://msdn.microsoft.com/library/azure/dd179355)
- Lär dig hur du förenklar koden du skriver så att den fungerar med Azure Storage genom att använda [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md).
- Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.
    - [Kom igång med Azure Table Storage med hjälp av .NET](storage-dotnet-how-to-use-tables.md) om du vill lagra strukturerade data.
    - [Kom igång med Azure Blob Storage med hjälp av .NET](storage-dotnet-how-to-use-blobs.md) om du vill lagra ostrukturerade data.
    - [Använd Azure SQL Database i .NET-program](sql-database-dotnet-how-to-use.md) om du vill lagra relationsdata.

  [Ladda ned och installera Azure SDK för .NET]: /develop/net/
  [Referens för .NET-klientbibliotek]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Skapa ett Azure-projekt i Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  [Azure Storage-teamets blogg]: http://blogs.msdn.com/b/windowsazurestorage/
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [EDM]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2



<!--HONumber=sep16_HO1-->


