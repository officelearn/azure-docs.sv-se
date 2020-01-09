---
title: Kom igång med Azure Queue Storage med hjälp av .NET – Azure Storage
description: Azure Queues ger tillförlitliga, asynkrona meddelandefunktioner mellan programkomponenter. Med hjälp av molnmeddelanden kan programkomponenter skalas separat.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 5ffee146bdbd666d4175af2f49f6b447743b2bc0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457678"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Kom igång med Azure Queue Storage med .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Översikt

Azure Queue Storage innehåller molnmeddelandehantering mellan programkomponenter. När program utformas för skalning är programkomponenterna ofta fristående, så att de kan skalas oberoende av varandra. Queue Storage är en asynkron meddelandelösning för kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

### <a name="about-this-tutorial"></a>Om den här självstudiekursen

I den här kursen lär du dig hur du skriver .NET-kod för några vanliga scenarier med hjälp av Azure Queue Storage. Du lär dig bland annat hur du skapar och tar bort köer och hur du lägger till, läser och tar bort kömeddelanden.

**Uppskattad tidsåtgång:** 45 minuter

### <a name="prerequisites"></a>Krav

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage vanliga klient bibliotek för .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
* [Klient bibliotek för Azure Storage kö för .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
* [Azure Configuration Manager för .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
* Ett [Azure Storage-konto](../common/storage-quickstart-create-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Konfigurera utvecklingsmiljön

Konfigurera sedan din utvecklingsmiljö i Visual Studio så att du är redo att testa kodexemplen i den här guiden.

### <a name="create-a-windows-console-application-project"></a>Skapa ett Windows-konsolprogramprojekt

Skapa ett nytt Windows-konsolprogram i Visual Studio. Följande steg visar hur du skapar ett konsol program i Visual Studio 2019. Stegen är ungefär som i andra versioner av Visual Studio.

1. Välj **Arkiv** > **Nytt** > **Projekt**
2. Välj **plattform** > **Windows**
3. Välj **Konsolprogram (.NET Framework)**
4. Välj **Nästa**
5. Ange ett namn för ditt program i fältet **projekt namn**
6. Välj **Skapa**

Alla kod exempel i den här självstudien kan läggas till i **Main ()-** metoden i konsol programmets **program.cs** -fil.

Du kan använda Azure Storage klient bibliotek i vilken typ av .NET-program som helst, inklusive en Azure-moln tjänst eller webbapp, samt Skriv bords-och mobil program. I den här guiden använder vi oss av en konsolapp för enkelhetens skull.

### <a name="use-nuget-to-install-the-required-packages"></a>Använd NuGet för att installera de paket som behövs

Du måste referera till följande tre paket i projektet för att slutföra den här självstudien:

* [Microsoft Azure Storage vanliga klient bibliotek för .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): det här paketet ger programmatisk åtkomst till data resurser i ditt lagrings konto.
* [Microsoft Azure Storage Queue-bibliotek för .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): det här klient biblioteket gör det möjligt att arbeta med Microsoft Azure Storage-kötjänst för att lagra meddelanden som kan nås av en klient.
* [Microsoft Azure Configuration Manager-biblioteket för .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): det här paketet tillhandahåller en klass för parsning av en anslutningssträng i en konfigurationsfil, oavsett var ditt program körs.

Du kan använda NuGet för att hämta paketen. Följ de här stegen:

1. Högerklicka på ditt projekt i **Solution Explorer** och välj **Hantera NuGet-paket**.
2. Välj **Bläddra**
3. Sök online efter "Microsoft. Azure. Storage. Queue" och välj **Installera** för att installera lagrings klient biblioteket och dess beroenden. Detta kommer också att installera Microsoft. Azure. Storage. common-biblioteket, vilket är ett beroende av Queue-biblioteket.
4. Sök online efter "Microsoft. Azure. ConfigurationManager" och välj **Installera** för att installera Azure-Configuration Manager.

> [!NOTE]
> Paket för lagrings klient bibliotek ingår också i [Azure SDK för .net](https://azure.microsoft.com/downloads/). Vi rekommenderar dock att du även installerar lagrings klient biblioteken från NuGet för att säkerställa att du alltid har de senaste versionerna.
>
> ODataLib-beroendena i lagrings klient biblioteken för .NET löses av ODataLib-paketen som är tillgängliga på NuGet, inte från WCF Data Services. ODataLib-biblioteken kan hämtas direkt eller refereras till i ditt kodprojekt via NuGet. De speciella ODataLib-paket som används av lagrings klient biblioteken är [OData](https://nuget.org/packages/Microsoft.Data.OData/), [EDM](https://nuget.org/packages/Microsoft.Data.Edm/)och [spatial](https://nuget.org/packages/System.Spatial/). Även om dessa bibliotek används av Azure Table Storage-klasser, är de nödvändiga beroenden för programmering med lagrings klient biblioteken.

### <a name="determine-your-target-environment"></a>Fastställ målmiljön

Du har två miljöalternativ för att köra exemplen i den här guiden:

* Du kan köra din kod mot ett Azure Storage-konto i molnet.
* Du kan köra din kod mot en Azure-lagringsemulator. Lagringsemulatorn är en lokal miljö som emulerar ett Azure Storage-konto i molnet. Emulatorn är ett kostnadsfritt alternativ för att testa och felsöka din kod medan ditt program är under utveckling. Emulatorn använder sig av ett välkänt konto och nyckel. Mer information finns i [Använd Azure Storage-emulatorn för utveckling och testning](../common/storage-use-emulator.md).

Om målet är ett lagringskonto i molnet kopierar du den primära åtkomstnyckeln för ditt lagringskonto från Azure Portal. Mer information finns i [Hantera åtkomst nycklar för lagrings konton](../common/storage-account-keys-manage.md).

> [!NOTE]
> Du kan använda lagringsemulatorn för att undvika kostnader associerade med Azure Storage. Men även om du väljer att använda ett Azure-lagringskonto i molnet, kommer kostnaderna för att genomföra den här guiden vara minimala.

### <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

Azure Storage klient bibliotek för .NET-stöd med en lagrings anslutnings sträng för att konfigurera slut punkter och autentiseringsuppgifter för åtkomst till lagrings tjänster. Det bästa sättet att underhålla anslutningssträngen för lagring är i en konfigurationsfil.

Mer information om anslutningssträngar finns i [Konfigurera en anslutningssträng för Azure Storage](../common/storage-configure-connection-string.md).

> [!NOTE]
> Din nyckel för lagringskontot liknar rotlösenordet för lagringskontot. Var alltid noga med att skydda din lagringskontonyckel. Undvik att dela ut den till andra användare, hårdkoda den eller spara den i en oformaterad textfil som andra har åtkomst till. Återskapa din nyckel med hjälp av Azure Portal om du misstänker att den komprometterats.

Om du vill konfigurera anslutnings strängen öppnar du filen **app. config** från Solution Explorer i Visual Studio. Lägg till innehållet i **\<appSettings\>** -elementet som visas nedan. Ersätt *Account-name* med namnet på ditt lagrings konto och *konto nyckeln* med din konto åtkomst nyckel:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Din konfigurationsinställning kan till exempel se ut så här:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

För att använda lagringsemulatorn kan du använda ett kortkommando som mappar till det välkända kontonamnet och nyckeln. I så fall är din inställning för anslutningssträngen:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Lägga till med hjälp av direktiv

Lägg till följande `using`-direktiv längst upp i filen `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiera dina autentiseringsuppgifter från Azure-portalen

Exempelkoden måste autentisera åtkomsten till lagringskontot. När du vill autentisera tillhandahåller du autentiseringsuppgifterna för ditt lagringskonto i form av en anslutningssträng i programmet. Visa autentiseringsuppgifterna för lagringskontot:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Leta rätt på ditt lagringskonto.
3. Välj **Åtkomstnycklar** i avsnittet **Inställningar** i lagringskontoöversikten. Åtkomstnycklarna för kontot visas, samt den fullständiga anslutningssträngen för varje nyckel.
4. Sök efter värdet för **Anslutningssträng** under **key1** och kopiera anslutningssträngen genom att klicka på **Kopiera**. Du lägger till strängvärdet för anslutningen till en miljövariabel i nästa steg.

    ![Skärmbild som visar hur man kopierar en anslutningssträng från Azure-portalen](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

### <a name="parse-the-connection-string"></a>Parsa anslutningssträngen

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Skapa Queue-tjänstklienten

Med hjälp av [CloudQueueClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet)-klassen kan du hämta köer som lagras i Queue Storage. Här är ett sätt att skapa tjänstklienten:

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

Nu är det dags att skriva kod som läser data från och skriver data till Queue Storage.

## <a name="create-a-queue"></a>Skapa en kö

Det här exemplet illustrerar hur du skapar en kö om den inte redan finns:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a container.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

## <a name="insert-a-message-into-a-queue"></a>Infoga ett meddelande i en kö

Om du vill infoga ett meddelande i en befintlig kö börjar du med att skapa ett nytt [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet). Därefter anropar du [AddMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet)-metoden. Du kan skapa ett **CloudQueueMessage** antingen från en sträng (i UTF-8-format) eller från en **byte**-matris. Här är kod som skapar en kö (om den inte finns) och som infogar meddelandet ”Hello World”:

```csharp
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
```

## <a name="peek-at-the-next-message"></a>En titt på nästa meddelande

Du kan kika på meddelandet först i en kö utan att ta bort det från kön genom att anropa metoden [PeekMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet).

```csharp
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
```

## <a name="change-the-contents-of-a-queued-message"></a>Ändra innehållet i ett meddelande i kön

Du kan ändra innehållet i ett meddelande direkt i kön. Om meddelandet representerar en arbetsuppgift kan du använda den här funktionen för att uppdatera arbetsuppgiftens status. Följande kod uppdaterar kömeddelandet med nytt innehåll och utökar tidsgränsen för visning med ytterligare 60 sekunder. Koden sparar statusen för arbetsuppgiften som associeras med meddelandet och ger klienten ytterligare en minut att fortsätta arbeta med meddelandet. Du kan använda den här tekniken för att spåra arbetsflöden med flera steg i kömeddelanden, utan att behöva börja om från början om ett bearbetningssteg misslyckas på grund av maskin- eller programvarufel. Normalt räknar du även antalet omförsök och tar bort meddelandet om fler än *n* försök misslyckas. Detta skyddar mot meddelanden som utlöser ett programfel varje gång de bearbetas.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

## <a name="de-queue-the-next-message"></a>Ta bort nästa meddelande från kön

Koden tar bort ett meddelande från en kö i två steg. När du anropar [GetMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet) hämtas nästa meddelande i en kö. Ett meddelande som returneras från **GetMessage** blir osynligt för andra meddelanden som läser kod i den här kön. Som standard är det här meddelandet osynligt i 30 sekunder. För att slutföra borttagningen av meddelandet från kön måste du även anropa [DeleteMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet). Den här tvåstegsprocessen för att ta bort ett meddelande säkerställer att om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel så kan en annan instans av koden hämta samma meddelande och försöka igen. Koden anropar **DeleteMessage** direkt efter att meddelandet har bearbetats.

```csharp
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
```

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Använda Async-Await-mönstret med vanliga Queue Storage-API:er

Det här exemplet illustrerar hur du använder Async-Await-mönstret med vanliga Queue Storage-API:er. Exemplet anropar den asynkrona versionen av var och en av de angivna metoderna, vilket du ser på *Async*-suffixet för varje metod. När en async-metod används pausar async-await-mönstret den lokala körningen tills anropet har slutförts. Detta gör att den aktuella tråden kan arbeta med annat, vilket innebär att flaskhalsar kan undvikas samtidigt som programmets svarstider förbättras. Mer information om hur du använder Async-Await-mönstret i .NET finns i [Async och Await (C# och Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

```csharp
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
```

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Använda fler alternativ för att hämta meddelanden ur kön

Det finns två metoder som du kan använda för att anpassa meddelandehämtningen från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tidsgräns för osynlighet för att ge koden mer eller mindre tid att bearbeta klart varje meddelande. I följande kodexempel används metoden [GetMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet) för att hämta 20 meddelanden i ett anrop. Sedan bearbetas varje meddelande med hjälp av en **foreach**-loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande. Observera att de fem minuterna startar för alla meddelanden samtidigt, vilket betyder att när det här har gått fem minuter sedan anropet till **GetMessage** så blir alla meddelanden som inte har tagits bort synliga igen.

```csharp
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
```

## <a name="get-the-queue-length"></a>Hämta kölängden

Du kan hämta en uppskattning av antalet meddelanden i en kö. [FetchAttributes](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet)-metoden ber kötjänsten att hämta köattributen, inklusive antalet meddelanden. Egenskapen [ApproximateMessageCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet) returnerar det sista värdet som hämtas av **FetchAttributes**-metoden, utan att kötjänsten anropas.

```csharp
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
```

## <a name="delete-a-queue"></a>Ta bort en kö

Om du vill ta bort en kö och alla meddelanden i den anropar du [Delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet)-metoden för köobjektet.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna i Queue Storage kan du följa dessa länkar för att lära dig mer om komplexa lagringsuppgifter.

* Fullständig information om tillgängliga API:er finns i referensdokumentationen för kötjänsten:
  * [Storage-klientbibliotek för .NET-referens](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [REST API-referens](https://msdn.microsoft.com/library/azure/dd179355)
* Lär dig hur du förenklar koden du skriver så att den fungerar med Azure Storage genom att använda [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
* Visa fler funktionsguider och lär dig mer om andra alternativ för att lagra data i Azure.
  * [Kom igång med Azure Table Storage med hjälp av .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) om du vill lagra strukturerade data.
  * [Kom igång med Azure Blob Storage med hjälp av .NET](../blobs/storage-dotnet-how-to-use-blobs.md) om du vill lagra ostrukturerade data.
  * [Anslut till SQL Database med hjälp av .NET (C#)](../../sql-database/sql-database-connect-query-dotnet-core.md) för att lagra relationsdata.

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2
