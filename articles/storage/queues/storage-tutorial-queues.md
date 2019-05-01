---
title: Självstudie – arbeta med Azure storage-köer
description: En självstudiekurs om hur du använder Azure-Kötjänsten för att skapa köer och insert-, hämta och ta bort meddelanden.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.date: 04/24/2019
ms.openlocfilehash: 5aa12c39b951ce65c4ef0a3f44de381c8961ed7f
ms.sourcegitcommit: 1aab8b764d767d86fbf262bda34996189d00c7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "64406170"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Självstudier: Arbeta med Azure storage-köer

Azure-kölagring implementerar molnbaserade köer för att aktivera kommunikation mellan komponenter i ett distribuerat program. Varje kö har en lista med meddelanden som kan läggas till av en sändarkomponenten och bearbetas av en komponent som mottagare. Med en kö skala ditt program direkt för att möta efterfrågan. Den här artikeln visar de grundläggande stegen för att arbeta med en Azure storage-kö.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> - Skapa ett Azure-lagringskonto
> - Skapa appen
> - Lägg till stöd för asynkron kod
> - Skapa en kö
> - Infoga meddelanden i en kö
> - Ut meddelanden ur kön
> - Ta bort en tom kö
> - Sök efter kommandoradsargument
> - Skapa och kör appen

## <a name="prerequisites"></a>Nödvändiga komponenter

- Hämta en kostnadsfri kopia av plattformsoberoende [Visual Studio Code](https://code.visualstudio.com/download) redigeraren.
- Ladda ned och installera den [.NET Core SDK](https://dotnet.microsoft.com/download).
- Om du inte har en aktuell Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

Skapa först ett Azure storage-konto. En stegvis guide till att skapa ett lagringskonto finns det [skapa ett lagringskonto](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) Snabbstart.

## <a name="create-the-app"></a>Skapa appen

Skapa ett .NET Core-program med namnet **QueueApp**. För enkelhetens skull ska den här appen både skicka och ta emot meddelanden via kön.

1. I ett konsolfönster (till exempel CMD, PowerShell eller Azure CLI), använder den `dotnet new` kommando för att skapa en ny konsolapp med namnet **QueueApp**. Det här kommandot skapar en enkel ”Hello World” C# projekt med en enskild källfil: **Program.CS**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Växla till den nyligen skapade **QueueApp** mappen och build app för att kontrollera att allt är korrekt.

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

Eftersom appen använder molnresurser måste körs koden asynkront. Dock C#'s **async** och **await** inte var giltigt nyckelord i **Main** metoder tills C# 7.1. Du kan enkelt växla till den kompilatorn via en flagga i den **csproj** fil.

1. Ange från kommandoraden i projektkatalogen `code .` att öppna Visual Studio Code i den aktuella katalogen. Lämna Kommandotolken öppen. Det blir fler kommandon för att köra senare. Om du uppmanas att lägga till C# tillgångar som krävs för att skapa och Felsök, klickar du på den **Ja** knappen.

2. Öppna den **QueueApp.csproj** filen i redigeraren.

3. Lägg till `<LangVersion>7.1</LangVersion>` första **PropertyGroup** i build-filen. Se till att du bara lägga till den **LangVersion** taggas som din **TargetFramework** kan skilja sig beroende på vilken version av .NET som du har installerat.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Spara den **QueueApp.csproj** fil.

5. Öppna den **Program.cs** käll-filen och uppdatera den **Main** metod som ska köras asynkront. Ersätt **void** med en **asynkrona aktiviteten** returvärde.

   ```csharp
   static async Task Main(string[] args)
   ```

6. Spara den **Program.cs** fil.

## <a name="create-a-queue"></a>Skapa en kö

1. Installera den **Azure visade sig vara. Storage** paketet i projektet med den `dotnet add package` kommando. Kör följande kommando för dotnet från projektmappen i konsolfönstret.

   ```console
   dotnet add package WindowsAzure.Storage
   ```

2. Överst på den **Program.cs** Lägg till följande namnrymder direkt efter den `using System;` instruktionen. Den här appen använder typer från dessa för att ansluta till Azure Storage och arbeta med köer.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.WindowsAzure.Storage;
   using Microsoft.WindowsAzure.Storage.Queue;
   ```

3. Spara den **Program.cs** fil.

### <a name="get-your-connection-string"></a>Hämta anslutningssträngen

Klientbiblioteket använder en anslutningssträng för att upprätta anslutningen. Anslutningssträngen är tillgänglig i den **inställningar** avsnitt på ditt lagringskonto i Azure-portalen.

1. I webbläsaren, logga in på den [Azure-portalen](https://portal.azure.com/).

2. Navigera till ditt lagringskonto i Azure-portalen.

3. Välj **åtkomstnycklar**.

4. Klicka på den **kopia** knappen till höger om den **anslutningssträngen** fält.

![Anslutningssträng](media/storage-tutorial-queues/get-connection-string.png)

Anslutningssträngen har det här formatet:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Lägga till anslutningssträngen i appen

Lägg till anslutningssträngen i appen så att den kan komma åt lagringskontot.

1. Gå tillbaka till Visual Studio Code.

2. I den **programmet** klass, lägga till en `private const string connectionString =` medlem för anslutningssträngen.

3. Efter likhetstecknet, klistrar du in det strängvärde som du kopierade tidigare i Azure portal. Den **connectionString** värdet ska vara unikt för ditt konto.

4. Ta bort ”Hello World”-kod från **Main**. Koden bör se ut ungefär så här men med din unika Anslutningssträngens värde.

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

5. Uppdatera **Main** att skapa en **CloudQueue** objekt som senare skickas till skicka och ta emot metoder.

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

Skapa en ny metod för att skicka ett meddelande i kön. Lägg till följande metod för att din **programmet** klass. Den här metoden hämtar en referens för kön och sedan skapar en ny kö om den inte redan finns genom att anropa [CreateIfNotExistsAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.createifnotexistsasync?view=azure-dotnet). Sedan det lägger till meddelandet i kön genom att anropa [AddMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.addmessageasync?view=azure-dotnet).

1. Lägg till följande **SendMessageAsync** metod för att din **programmet** klass.

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

## <a name="dequeue-messages"></a>Ut meddelanden ur kön

Skapa en ny metod som kallas **ReceiveMessageAsync**. Den här metoden tar emot ett meddelande från kön genom att anropa [GetMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.getmessageasync?view=azure-dotnet). När meddelandet har tagits emot, är det viktigt att ta bort det från kön så att den inte bearbetas mer än en gång. När meddelandet tas emot, ta bort det från kön genom att anropa [DeleteMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.deletemessageasync?view=azure-dotnet).

1. Lägg till följande **ReceiveMessageAsync** metod för att din **programmet** klass.

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

Det är bästa praxis i slutet av ett projekt att identifiera om du fortfarande behöver de resurser som du skapade. Resurser vänstra som körs kan kostar pengar. Om kön finns men är tom, be användaren om de vill ta bort den.

1. Expandera den **ReceiveMessageAsync** metoden och ta en uppmaning om att ta bort tom kö.

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

Om det finns några kommandoradsargument som skickas till appen, förutsätter att de är ett meddelande som ska läggas till i kön. Anslut argument tillsammans för att göra en sträng. Lägg till den här strängen till meddelandekön genom att anropa den **SendMessageAsync** metoden som vi lade till tidigare.

Om det finns inga argument på kommandoraden, kör du en hämta-åtgärden. Anropa den **ReceiveMessageAsync** metod för att hämta det första meddelandet i kön.

Slutligen, vänta tills indata från användaren innan du avslutar genom att anropa **Console.ReadLine**.

1. Expandera den **Main** metod för att söka efter kommandoradsargument och vänta på användarindata.

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

Här är den fullständiga koden för det här projektet.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.WindowsAzure.Storage;
   using Microsoft.WindowsAzure.Storage.Queue;

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

1. Kör följande dotnet-kommando för att skapa projektet från kommandoraden i projektkatalogen.

   ```console
   dotnet build
   ```

2. När projektet skapas har måste du köra följande kommando för att lägga till det första meddelandet i kön.

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

3. Kör appen med inga kommandoradsargument för att ta emot och ta bort det första meddelandet i kön.

   ```console
   dotnet run
   ```

4. Fortsätta att köra appen tills alla meddelanden som tas bort. Om du kör en gång, får du ett meddelande om att kön är tom och en uppmaning om att ta bort kön.

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
3. Ta bort en Azure storage-kö

Kolla in Azure-köer Snabbstart för mer information.

> [!div class="nextstepaction"]
> [Köer-Snabbstart](storage-quickstart-queues-portal.md)
