---
title: Självstudie – arbeta med Azure Storage-köer – Azure Storage
description: En själv studie kurs om hur du använder Azure-Kötjänst för att skapa köer och infoga, hämta och ta bort meddelanden.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: 9cbdc5231fdc9f836f300b1a3a81a237a9efc123
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75968205"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Självstudie: arbeta med Azure Storage-köer

Azure Queue Storage implementerar molnbaserade köer för att möjliggöra kommunikation mellan komponenter i ett distribuerat program. Varje kö underhåller en lista över meddelanden som kan läggas till av en avsändarens komponent och bearbetas av en mottagar komponent. Med en kö kan ditt program skalas direkt för att möta efter frågan. Den här artikeln visar de grundläggande stegen för att arbeta med en Azure Storage-kö.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
>
> - Skapa ett Azure-lagringskonto
> - Skapa appen
> - Lägg till stöd för asynkron kod
> - Skapa en kö
> - Infoga meddelanden i en kö
> - Ta bort meddelanden från kön
> - Ta bort en tom kö
> - Sök efter kommando rads argument
> - Skapa och kör appen

## <a name="prerequisites"></a>Krav

- Få en kostnads fri kopia av den plattforms oberoende kod redigeraren i [Visual Studio](https://code.visualstudio.com/download) .
- Ladda ned och installera [.net Core SDK](https://dotnet.microsoft.com/download).
- Om du inte har en aktuell Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

Börja med att skapa ett Azure Storage-konto. En steg-för-steg-guide om hur du skapar ett lagrings konto finns i [skapa ett lagrings konto](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) snabb start.

## <a name="create-the-app"></a>Skapa appen

Skapa ett .NET Core-program med namnet **QueueApp**. För enkelhetens skull kommer den här appen både att skicka och ta emot meddelanden via kön.

1. I ett konsol fönster (till exempel CMD, PowerShell eller Azure CLI) använder du kommandot `dotnet new` för att skapa en ny konsol-app med namnet **QueueApp**. Det här kommandot skapar ett enkelt "Hello World C# "-projekt med en enda käll fil: **program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Växla till den nyligen skapade **QueueApp** -mappen och skapa appen för att kontrol lera att alla är väl.

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

Eftersom appen använder moln resurser körs koden asynkront. Men C#är **async** och **väntar** på att inte vara giltiga nyckelord i **huvud** metoderna fram till C# 7,1. Du kan enkelt växla till den kompilatorn via en flagga i **CSPROJ** -filen.

1. Från kommando raden i projekt katalogen skriver du `code .` för att öppna Visual Studio Code i den aktuella katalogen. Låt kommando rads fönstret vara öppet. Det kommer att finnas fler kommandon att köra senare. Om du uppmanas att lägga C# till till gångar som krävs för att skapa och felsöka klickar du på knappen **Ja** .

2. Öppna filen **QueueApp.csproj** i redigeraren.

3. Lägg till `<LangVersion>7.1</LangVersion>` i den första **PropertyGroup** i build-filen. Se till att du bara lägger till **LangVersion** -taggen eftersom din **TargetFramework** kan vara olika beroende på vilken version av .net som du har installerat.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Spara filen **QueueApp. CSPROJ** .

5. Öppna käll filen **program.cs** och uppdatera **main** -metoden så att den körs asynkront. Ersätt **void** med ett retur värde för **asynkron uppgift** .

   ```csharp
   static async Task Main(string[] args)
   ```

6. Spara **Program.cs**-filen.

## <a name="create-a-queue"></a>Skapa en kö

1. Installera **Microsoft. Azure. Storage. common** och **Microsoft. Azure. Storage. Queue** -paketen i projektet med kommandot `dotnet add package`. Kör följande dotNet-kommandon från mappen Project i konsol fönstret.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. Längst upp i **program.cs** -filen lägger du till följande namn rymder direkt efter `using System;`-instruktionen. Den här appen använder typer från dessa namn områden för att ansluta till Azure Storage och arbeta med köer.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Spara **Program.cs**-filen.

### <a name="get-your-connection-string"></a>Hämta anslutningssträngen

Klient biblioteket använder en anslutnings sträng för att upprätta anslutningen. Anslutnings strängen är tillgänglig i avsnittet **Inställningar** på ditt lagrings konto i Azure Portal.

1. Logga in på [Azure Portal](https://portal.azure.com/)i webbläsaren.

2. Navigera till ditt lagringskonto i Azure-portalen.

3. Välj **åtkomst nycklar**.

4. Klicka på knappen **Kopiera** till höger om fältet **anslutnings sträng** .

![Anslutningssträng](media/storage-tutorial-queues/get-connection-string.png)

Anslutningssträngen har det här formatet:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Lägg till anslutnings strängen i appen

Lägg till anslutnings strängen i appen så att den kan komma åt lagrings kontot.

1. Växla tillbaka till Visual Studio Code.

2. I **program** -klassen lägger du till en `private const string connectionString =`-medlem som ska innehålla anslutnings strängen.

3. Efter likhets tecknet klistrar du in det sträng värde som du kopierade tidigare i Azure Portal. **ConnectionString** -värdet är unikt för ditt konto.

4. Ta bort koden "Hello World" från **main**. Koden bör se ut ungefär så här, men med ditt unika anslutnings sträng värde.

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

5. Uppdatera **huvud** för att skapa ett **CloudQueue** -objekt, som senare skickas till metoderna för att skicka och ta emot.

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

Skapa en ny metod för att skicka ett meddelande till kön. Lägg till följande metod i **program** klassen. Den här metoden hämtar en Queue-referens och skapar sedan en ny kö om den inte redan finns genom att anropa [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Sedan lägger den till meddelandet i kön genom att anropa [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

1. Lägg till följande **SendMessageAsync** -Metod i **program** klassen.

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

Ett meddelande måste har ett format som stöds av en XML-begäran med UTF-8-kodning, och kan vara upp till 64 kB stort. Om ett meddelande innehåller binära data, rekommenderar vi att du base64-kodar meddelandet.

Som standard är den högsta livslängden för ett meddelande inställt på 7 dagar. Du kan ange ett positivt tal för Time-to-Live-meddelandet. Om du vill lägga till ett meddelande som inte upphör att gälla använder du `Timespan.FromSeconds(-1)` i anropet till **AddMessageAsync**.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Ta bort meddelanden från kön

Skapa en ny metod som heter **ReceiveMessageAsync**. Den här metoden tar emot ett meddelande från kön genom att anropa [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). När meddelandet har tagits emot är det viktigt att ta bort det från kön så att det inte bearbetas mer än en gång. När meddelandet har tagits emot tar du bort det från kön genom att anropa [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

1. Lägg till följande **ReceiveMessageAsync** -Metod i **program** klassen.

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

Det är en bra idé i slutet av ett projekt att identifiera om du fortfarande behöver de resurser som du har skapat. Resurser som fortsätter att köras kan medföra kostnader. Om kön finns men är tom ber du användaren om han vill ta bort den.

1. Expandera **ReceiveMessageAsync** -metoden för att ta med en uppvarning för att ta bort den tomma kön.

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

## <a name="check-for-command-line-arguments"></a>Sök efter kommando rads argument

Om det finns kommando rads argument som skickas till appen, antar du att de är ett meddelande som ska läggas till i kön. Koppla ihop argumenten för att skapa en sträng. Lägg till den här strängen i meddelande kön genom att anropa **SendMessageAsync** -metoden som vi lade till tidigare.

Om det inte finns några kommando rads argument kör du en Hämta-åtgärd. Anropa **ReceiveMessageAsync** -metoden för att hämta det första meddelandet i kön.

Vänta sedan på användarindata innan du avslutar genom att anropa **Console. readline**.

1. Expandera **huvud** metoden för att kontrol lera kommando rads argument och vänta på användarindata.

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

Här är den fullständiga kod listan för det här projektet.

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

1. Från kommando raden i projekt katalogen kör du följande dotNet-kommando för att skapa projektet.

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

3. Kör appen utan kommando rads argument för att ta emot och ta bort det första meddelandet i kön.

   ```console
   dotnet run
   ```

4. Fortsätt att köra appen tills alla meddelanden har tagits bort. Om du kör den en gång till får du ett meddelande om att kön är tom och att du uppmanas att ta bort kön.

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
3. Ta bort en Azure Storage-kö

Mer information finns i snabb start för Azure-köer.

> [!div class="nextstepaction"]
> [Snabb start för köer](storage-quickstart-queues-portal.md)
