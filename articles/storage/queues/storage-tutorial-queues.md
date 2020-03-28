---
title: Självstudiekurs - Arbeta med Azure-lagringsköer - Azure Storage
description: En självstudiekurs om hur du använder Azure Queue-tjänsten för att skapa köer och infoga, hämta och ta bort meddelanden.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: 9cbdc5231fdc9f836f300b1a3a81a237a9efc123
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75968205"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Självstudiekurs: Arbeta med Azure-lagringsköer

Azure Queue storage implementerar molnbaserade köer för att möjliggöra kommunikation mellan komponenter i ett distribuerat program. Varje kö har en lista över meddelanden som kan läggas till av en avsänarkomponent och bearbetas av en mottagarkomponent. Med en kö kan ditt program skalas omedelbart för att möta efterfrågan. Den här artikeln visar de grundläggande stegen för att arbeta med en Azure-lagringskö.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
>
> - Skapa ett Azure-lagringskonto
> - Skapa appen
> - Lägg till stöd för asynkron kod
> - Skapa en kö
> - Infoga meddelanden i en kö
> - Avköa meddelanden
> - Ta bort en tom kö
> - Sök efter kommandoradsargument
> - Skapa och kör appen

## <a name="prerequisites"></a>Krav

- Få ditt gratis exemplar av Visual Studio Code-redigeraren. [Visual Studio Code](https://code.visualstudio.com/download)
- Hämta och installera [.NET Core SDK](https://dotnet.microsoft.com/download).
- Om du inte har en aktuell Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

Skapa först ett Azure-lagringskonto. En steg-för-steg-guide för att skapa ett lagringskonto finns i snabbstarten [Skapa ett lagringskonto.](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json)

## <a name="create-the-app"></a>Skapa appen

Skapa ett .NET Core-program med namnet **QueueApp**. För enkelhetens skull kommer den här appen både att skicka och ta emot meddelanden via kön.

1. I ett konsolfönster (till exempel CMD, PowerShell `dotnet new` eller Azure CLI) använder du kommandot för att skapa en ny konsolapp med namnet **QueueApp**. Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Växla till den nyskapade **QueueApp-mappen** och skapa appen för att kontrollera att allt är bra.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Du bör se resultat som liknar följande:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>Lägg till stöd för asynkron kod

Eftersom appen använder molnresurser körs koden asynkront. Men C # **async** och **väntar** var inte giltiga sökord i **huvudsakliga** metoder tills C # 7.1. Du kan enkelt växla till kompilatorn genom en flagga i **csproj-filen.**

1. Skriv för `code .` att öppna Visual Studio-kod i den aktuella katalogen på kommandoraden i projektkatalogen. Håll kommandoradsfönstret öppet. Det kommer att finnas fler kommandon att köra senare. Om du uppmanas att lägga till C#-tillgångar som **Yes** krävs för att skapa och felsöka klickar du på ja-knappen.

2. Öppna filen **QueueApp.csproj** i redigeraren.

3. Lägg `<LangVersion>7.1</LangVersion>` till den första **PropertyGroup** i byggfilen. Kontrollera att du bara lägger till **LangVersion-taggen** eftersom **targetframe-spelet** kan vara olika beroende på vilken version av .NET du har installerat.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Spara filen **QueueApp.csproj.**

5. Öppna **källfilen Program.cs** och uppdatera **huvudmetoden** för att köras asynkront. Ersätt **annullering** med ett **async-returvärde för uppgifter.**

   ```csharp
   static async Task Main(string[] args)
   ```

6. Spara **Program.cs-filen.**

## <a name="create-a-queue"></a>Skapa en kö

1. Installera paketen **Microsoft.Azure.Storage.Common** och **Microsoft.Azure.Storage.Queue** till `dotnet add package` projektet med kommandot. Kör följande dotnet-kommandon från projektmappen i konsolfönstret.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. Lägg till följande namnområden högst upp i **Program.cs-filen** `using System;` direkt efter satsen. Den här appen använder typer från dessa namnområden för att ansluta till Azure Storage och arbeta med köer.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Spara **Program.cs-filen.**

### <a name="get-your-connection-string"></a>Hämta anslutningssträngen

Klientbiblioteket använder en anslutningssträng för att upprätta din anslutning. Anslutningssträngen är tillgänglig i avsnittet **Inställningar** i ditt lagringskonto i Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com/)i webbläsaren .

2. Navigera till ditt lagringskonto i Azure-portalen.

3. Välj **Åtkomstnycklar**.

4. Klicka på knappen **Kopiera** till höger om **strängfältet Anslutning.**

![Anslutningssträng](media/storage-tutorial-queues/get-connection-string.png)

Anslutningssträngen har det här formatet:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Lägga till anslutningssträngen i appen

Lägg till anslutningssträngen i appen så att den kan komma åt lagringskontot.

1. Växla tillbaka till Visual Studio-kod.

2. Lägg till en `private const string connectionString =` medlem som innehåller anslutningssträngen i klassen **Program.**

3. Efter likhetstecknet klistrar du in strängvärdet som du kopierade tidigare i Azure-portalen. Värdet **för connectionString** kommer att vara unikt för ditt konto.

4. Ta bort "Hello World"-koden från **Main**. Koden ska se ut ungefär som följande men med det unika anslutningssträngvärdet.

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. Uppdatera **Main** för att skapa ett **CloudQueue-objekt,** som senare skickas till metoderna för skicka och ta emot.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Spara filen.

## <a name="insert-messages-into-the-queue"></a>Infoga meddelanden i kön

Skapa en ny metod för att skicka ett meddelande till kön. Lägg till följande metod i **klassen Program.** Den här metoden hämtar en köreferens och skapar sedan en ny kö om den inte redan finns genom att anropa [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Sedan läggs meddelandet till i kön genom att anropa [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

1. Lägg till följande **SendMessageAsync-metod** i **klassen Program.**

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. Spara filen.

Ett meddelande måste har ett format som stöds av en XML-begäran med UTF-8-kodning, och kan vara upp till 64 kB stort. Om ett meddelande innehåller binära data rekommenderar vi att du Base64-kodar meddelandet.

Som standard är den högsta livslängden för ett meddelande inställt på 7 dagar. Du kan ange valfritt positivt tal för meddelandet time-to-live. Om du vill lägga till `Timespan.FromSeconds(-1)` ett meddelande som inte upphör att gälla använder du i anropet till **AddMessageAsync**.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Avköa meddelanden

Skapa en ny metod som heter **ReceiveMessageAsync**. Den här metoden tar emot ett meddelande från kön genom att anropa [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). När meddelandet har tagits emot är det viktigt att ta bort det från kön så att det inte bearbetas mer än en gång. När meddelandet har tagits emot tar du bort det från kön genom att anropa [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

1. Lägg till följande **ReceiveMessageAsync-metod** i **klassen Program.**

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. Spara filen.

## <a name="delete-an-empty-queue"></a>Ta bort en tom kö

Det är en bra idé i slutet av ett projekt för att identifiera om du fortfarande behöver de resurser du har skapat. Resurser som fortsätter att köras kan medföra kostnader. Om kön finns men är tom frågar du användaren om de vill ta bort den.

1. Expandera metoden **ReceiveMessageAsync** om du vill ta med en uppmaning om att ta bort den tomma kön.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. Spara filen.

## <a name="check-for-command-line-arguments"></a>Sök efter kommandoradsargument

Om det finns några kommandoradsargument som skickas till appen antar du att de är ett meddelande som ska läggas till i kön. Gå med i argumenten tillsammans för att göra en sträng. Lägg till den här strängen i meddelandekön genom att anropa metoden **SendMessageAsync** som vi lade till tidigare.

Om det inte finns några kommandoradsargument kör du en hämtningsåtgärd. Anropa metoden **ReceiveMessageAsync** för att hämta det första meddelandet i kön.

Vänta slutligen på användarindata innan du avslutar genom att ringa **Console.ReadLine**.

1. Expandera **huvudmetoden** för att söka efter kommandoradsargument och vänta på användarindata.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. Spara filen.

## <a name="complete-code"></a>Fullständig kod

Här är den fullständiga kodlistan för det här projektet.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Från kommandoraden i projektkatalogen kör du följande dotnet-kommando för att skapa projektet.

   ```console
   dotnet build
   ```

2. När projektet har skapats kör du följande kommando för att lägga till det första meddelandet i kön.

   ```console
   dotnet run First queue message
   ```

Du bör se dessa utdata:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Kör appen utan kommandoradsargument för att ta emot och ta bort det första meddelandet i kön.

   ```console
   dotnet run
   ```

4. Fortsätt att köra appen tills alla meddelanden har tagits bort. Om du kör den en gång till får du ett meddelande om att kön är tom och en uppmaning om att ta bort kön.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

1. Skapa en kö
2. Lägga till och ta bort meddelanden från en kö
3. Ta bort en Azure-lagringskö

Kolla in snabbstarten för Azure-köer för mer information.

> [!div class="nextstepaction"]
> [Snabbstart för köer](storage-quickstart-queues-portal.md)
