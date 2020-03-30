---
title: 'Snabbstart: Azure Queue storage library v12 - .NET'
description: Lär dig hur du använder Azure Queue .NET v12-biblioteket för att skapa en kö och lägga till meddelanden i kön. Därefter får du lära dig att läsa och ta bort meddelanden från kön. Du får också lära dig hur du tar bort en kö.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/22/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: c69aa91596ff203445aa4fa3ccd59001ffe16649
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78197495"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Snabbstart: Azure Queue storage client library v12 för .NET

Kom igång med Azure Queue storage client library version 12 för .NET. Azure Queue storage är en tjänst för att lagra ett stort antal meddelanden för senare hämtning och bearbetning. Följ dessa steg för att installera paketet och prova exempelkod för grundläggande uppgifter.

> [!NOTE]
> Information om hur du kommer igång med den tidigare SDK-versionen finns i [Snabbstart: Använd Azure Storage SDK v11 för .NET för att hantera en kö](storage-quickstart-queues-dotnet-legacy.md).

Använd Azure Queue storage client library v12 för .NET för att:

* Skapa en kö
* Lägga till meddelanden i en kö
* Granska meddelanden i en kö
* Uppdatera ett meddelande i en kö
* Ta emot meddelanden från en kö
* Ta bort meddelanden från en kö
* Ta bort en kö

[API-referensdokumentation](/dotnet/api/azure.storage.queues) | [Biblioteksnamnpaket](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues) | [(NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0) | [Exempel](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)
* Azure storage-konto - [skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Aktuell [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) för ditt operativsystem. Var noga med att få SDK och inte körningen.

## <a name="setting-up"></a>Inrätta

I det här avsnittet beskrivs förbereder du ett projekt för att arbeta med Azure Queue storage-klientbiblioteket v12 för .NET.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett .NET Core-program med namnet *QueuesQuickstartV12*.

1. I ett konsolfönster (till exempel cmd, PowerShell `dotnet new` eller Bash) använder du kommandot för att skapa en ny konsolapp med namnet *QueuesQuickstartV12*. Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *Program.cs*.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Växla till den nyskapade *queuesQuickstartV12-katalogen.*

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>Installera paketet

Installera Azure Queue storage-klientbiblioteket för .NET-paketet medan `dotnet add package` du fortfarande är i programkatalogen med kommandot.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Konfigurera ramverket för appen

Från projektkatalogen:

1. Öppna *Program.cs-filen* i redigeraren
1. Ta `Console.WriteLine("Hello World!");` bort utdraget
1. Lägga `using` till direktiv
1. Uppdatera `Main` metoddeklarationen för att [stödja asynkron kod](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7-1#async-main)



Här är koden:

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Queue Storage är en tjänst för lagring av ett stort antal meddelanden. Ett kömeddelande kan vara upp till 64 kB i storlek. En kö kan innehålla miljontals meddelanden, upp till den totala kapacitetsgränsen för ett lagringskonto. Köer används ofta för att skapa en eftersläpning av arbete för att bearbeta asynkront. Kölagring erbjuder tre typer av resurser:

* Lagringskontot
* En kö i lagringskontot
* Meddelanden i kön

Följande diagram visar relationen mellan de här resurserna.

![Diagram över lagringsarkitektur för kö](./media/storage-queues-introduction/queue1.png)

Använd följande .NET-klasser för att interagera med dessa resurser:

* [QueueServiceClient](/dotnet/api/azure.storage.queues.queueserviceclient): `QueueServiceClient` Med möjlighet att hantera alla köer i ditt lagringskonto.
* [QueueClient](/dotnet/api/azure.storage.queues.queueclient): `QueueClient` Klassen låter dig hantera och manipulera en enskild kö och dess meddelanden.
* [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage): `QueueMessage` Klassen representerar de enskilda objekt som returneras när [du anropar ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) i en kö.

## <a name="code-examples"></a>Kodexempel

I det här exemplet visar kodavsnitten hur du utför följande åtgärder med Azure Queue storage-klientbiblioteket för .NET:

* [Hämta anslutningssträngen](#get-the-connection-string)
* [Skapa en kö](#create-a-queue)
* [Lägga till meddelanden i en kö](#add-messages-to-a-queue)
* [Granska meddelanden i en kö](#peek-at-messages-in-a-queue)
* [Uppdatera ett meddelande i en kö](#update-a-message-in-a-queue)
* [Ta emot meddelanden från en kö](#receive-messages-from-a-queue)
* [Ta bort meddelanden från en kö](#delete-messages-from-a-queue)
* [Ta bort en kö](#delete-a-queue)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutningssträngen för lagringskontot. Anslutningssträngen lagras i den miljövariabel som skapas i avsnittet [Konfigurera lagringsanslutningssträngen.](#configure-your-storage-connection-string)

Lägg till den `Main` här koden i metoden:

```csharp
Console.WriteLine("Azure Queue storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Skapa en kö

Bestäm ett namn för den nya kön. Koden nedan lägger till ett GUID-värde i könamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Könamn får bara innehålla gemener, siffror och bindestreck och måste börja med en bokstav eller ett tal. Varje bindestreck måste föregås och följas av ett tecken som inte är ett bindestreck. Namnet måste också vara mellan 3 och 63 tecken långt. Mer information om namngivning av köer finns i [Namnge köer och metadata](/rest/api/storageservices/naming-queues-and-metadata).


Skapa en instans av klassen [QueueClient.](/dotnet/api/azure.storage.queues.queueclient) Anropa sedan [metoden CreateAsync](/dotnet/api/azure.storage.queues.queueclient.createasync) för att skapa kön i ditt lagringskonto.

Lägg till den här `Main` koden i slutet av metoden:

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>Lägga till meddelanden i en kö

Följande kodavsnitt läggs asynkront till meddelanden i kö genom att anropa metoden [SendMessageAsync.](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) Det sparar också en [SendReceipt](/dotnet/api/azure.storage.queues.models.sendreceipt) `SendMessageAsync` som returneras från ett samtal. Inleveransen används för att uppdatera meddelandet senare i programmet.

Lägg till den här `Main` koden i slutet av metoden:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Granska meddelanden i en kö

Granska meddelandena i kön genom att anropa metoden [PeekMessagesAsync.](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) Metoden `PeekMessagesAsync` hämtar ett eller flera meddelanden från framsidan av kön men ändrar inte meddelandets synlighet.

Lägg till den här `Main` koden i slutet av metoden:

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>Uppdatera ett meddelande i en kö

Uppdatera innehållet i ett meddelande genom att anropa metoden [UpdateMessageAsync.](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) Metoden `UpdateMessageAsync` kan ändra ett meddelandes synlighetstidsutgång och innehåll. Meddelandeinnehållet måste vara en UTF-8-kodad sträng som är upp till 64 kB i storlek. Tillsammans med det nya innehållet för meddelandet, `SendReceipt` skicka in värdena från som sparades tidigare i koden. Värdena `SendReceipt` identifierar vilket meddelande som ska uppdateras.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

Hämta tidigare tillagda meddelanden genom att anropa metoden [ReceiveMessagesAsync.](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync)

Lägg till den här `Main` koden i slutet av metoden:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Ta bort meddelanden från en kö

Ta bort meddelanden från kön när de har bearbetats. I det här fallet är bearbetning bara visar meddelandet på konsolen.

Appen pausar för användarindata genom att anropa `Console.ReadLine` innan den bearbetar och tar bort meddelandena. Kontrollera i din [Azure-portal](https://portal.azure.com) att resurserna har skapats korrekt innan de tas bort. Alla meddelanden som inte uttryckligen tas bort kommer så småningom att bli synliga i kön igen för en ny chans att bearbeta dem.

Lägg till den här `Main` koden i slutet av metoden:

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>Ta bort en kö

Följande kod rensar de resurser som appen har skapat genom att ta bort kön med metoden [DeleteAsync.](/dotnet/api/azure.storage.queues.queueclient.deleteasync)

Lägg till den här `Main` koden i slutet av metoden:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar och lägger till tre meddelanden i en Azure-kö. Koden visar meddelandena i kön och hämtar och tar sedan bort dem innan du slutligen tar bort kön.

I konsolfönstret navigerar du till programkatalogen och skapar och kör programmet.

```console
dotnet build
```

```console
dotnet run
```

Utdata för appen liknar följande exempel:

```output
Azure Queue storage v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

När appen pausas innan du tar emot meddelanden kontrollerar du ditt lagringskonto i [Azure-portalen](https://portal.azure.com). Kontrollera att meddelandena finns i kön.

Tryck på **Retur** för att ta emot och ta bort meddelandena. När du uppmanas till det trycker du på **Retur** igen för att ta bort kön och slutför demon.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar en kö och lägger till meddelanden i den med asynkron .NET-kod. Sedan lärde du dig att granska, hämta och ta bort meddelanden. Slutligen har du lärt dig hur du tar bort en meddelandekö.

För självstudier, exempel, snabbstarter och annan dokumentation, besök:

> [!div class="nextstepaction"]
> [Azure för .NET- och .NET Core-utvecklare](https://docs.microsoft.com/dotnet/azure/)

* Mer information finns i [Azure Storage-biblioteken för .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage).
* Om du vill se fler exempelappar för Azure Queue storage fortsätter du till [Azure Queue storage v12 .NET-klientbiblioteksexempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples).
* Mer information om .NET Core finns i [Kom igång med .NET på 10 minuter](https://www.microsoft.com/net/learn/get-started/).
