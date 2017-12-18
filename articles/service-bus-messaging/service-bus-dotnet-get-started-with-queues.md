---
title: "Komma igång med Azure Service Bus-köer | Microsoft Docs"
description: "Skriv ett C#-konsolprogram som använder meddelandeköer i Service Bus."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/7/2017
ms.author: sethm
ms.openlocfilehash: 6af7e4d238c10c0fed3443db58644e3557525993
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-service-bus-queues"></a>Komma igång med Service Bus-köer

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Den här självstudien omfattar följande steg:

1. Skapa ett Service Bus-namnområde med Azure Portal.
2. Skapa en Service Bus-kö med Azure Portal.
3. Skriva ett .NET Core-konsolprogram för att skicka en uppsättning meddelanden till kön.
4. Skriva ett .NET Core-konsolprogram för att ta emot meddelandena från kön.

## <a name="prerequisites"></a>Krav

1. [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](http://www.visualstudio.com/vs) eller senare.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows), version 2.0 eller senare.
2. En Azure-prenumeration.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Skapa ett namnområde med Azure Portal

> [!NOTE] 
> Du kan också skapa ett Service Bus-namnområde och meddelandeentiteter med [PowerShell](/powershell/azure/get-started-azureps). Mer information finns i [Använda PowerShell för att hantera Service Bus-resurser](service-bus-manage-with-ps.md).

Om du redan har skapat ett namnområde för Service Bus-meddelanden går du vidare till avsnittet [Skapa en kö med hjälp av Azure Portal](#2-create-a-queue-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Skapa en kö med hjälp av Azure Portal

Om du redan har skapat en Service Bus-kö går du vidare till avsnittet [Skicka meddelanden till kön](#3-send-messages-to-the-queue).

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Skicka meddelanden till kön

Skriv ett C#-konsolprogram med Visual Studio för att skicka meddelanden till kön.

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

Starta Visual Studio och skapa ett nytt projekt: **Konsolprogram (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Lägga till Service Bus-NuGet-paketet

1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.
2. Klicka på fliken **Bläddra**, sök efter **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** och markera posten **Microsoft.Azure.ServiceBus**. Klicka på **Installera** för att slutföra installationen och stäng sedan den här dialogrutan.
   
    ![Välj ett NuGet-paket][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Skriva kod för att skicka meddelanden till kön

1. I Program.cs lägger du till följande `using`-instruktioner överst i definitionen för namnområdet, före klassdeklarationen:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. I `Program`-klassen deklarerar du följande variabler. Ställ in variabeln `ServiceBusConnectionString` på den anslutningssträng du fick när du skapade namnområdet, och ställ in `QueueName` på namnet du använde när du skapade kön:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ``` 

3. Ersätt standardinnehållet i `Main()` med följande rad med kod:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Direkt efter `Main()` lägger du till följande asynkrona `MainAsync()`-metod som anropar metoden för att skicka meddelanden:

    ```csharp
    static async Task MainAsync()
    {
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

5. Direkt efter `MainAsync()`-metoden lägger du till följande `SendMessagesAsync()`-metod som utför uppgiften att skicka det antal meddelanden som anges av `numberOfMessagesToSend` (för närvarande inställd på 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
   
6. Så här bör filen Program.cs se ut:
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            // Connection String for the namespace can be obtained from the Azure portal under the 
            // 'Shared Access policies' section.
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string QueueName = "<your_queue_name>";
            static IQueueClient queueClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Send Messages
                await SendMessagesAsync(numberOfMessages);
                        
                Console.ReadKey();

                await queueClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the queue
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the queue
                        await queueClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

7. Kör programmet och kontrollera Azure Portal: klicka på köns namn på fönstret **Översikt** för namnområdet. Skärmen **Grundläggande** för kön visas. Observera att **Antal aktiva meddelanden** för kön nu är **10**. Varje gång du kör sändningsprogrammet utan att hämta meddelanden (beskrivs i nästa avsnitt) ökar detta värde med 10. Observera också att köns aktuella storlek ökar värdet **Aktuell** i fönstret **Grundläggande** varje gång programmet lägger till meddelanden i kön.
   
      ![Meddelandestorlek][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Ta emot meddelanden från kön

För att ta emot de meddelanden som du just skickade skapar du ytterligare ett .NET Core-konsolprogram och installerar **Microsoft.Azure.ServiceBus** NuGet-paketet, på ungefär samma sätt som i det tidigare sändningsprogrammet.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Skriva kod för att ta emot meddelanden från kön

1. I Program.cs lägger du till följande `using`-instruktioner överst i definitionen för namnområdet, före klassdeklarationen:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. I `Program`-klassen deklarerar du följande variabler. Ställ in variabeln `ServiceBusConnectionString` på den anslutningssträng du fick när du skapade namnområdet, och ställ in `QueueName` på namnet du använde när du skapade kön:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

3. Ersätt standardinnehållet i `Main()` med följande rad med kod:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Direkt efter `Main()` lägger du till följande asynkrona `MainAsync()`-metod som anropar metoden `RegisterOnMessageHandlerAndReceiveMessages()`:

    ```csharp
    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register the queue message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

5. Direkt efter `MainAsync()`-metoden lägger du till följande metod som registrerar meddelandehanteraren och tar emot meddelanden som skickas av sändningsprogrammet:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

6. Direkt efter den föregående metoden lägger du till följande `ProcessMessagesAsync()`-metod för att bearbeta de mottagna meddelandena:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. Slutligen lägger du till följande metod för att hantera alla undantag som kan uppstå:
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    
   
8. Så här bör din Program.cs-fil se ut:
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            // Connection String for the namespace can be obtained from the Azure portal under the 
            // 'Shared Access policies' section.
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string QueueName = "<your_queue_name>";
            static IQueueClient queueClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register QueueClient's MessageHandler and receive messages in a loop
                RegisterOnMessageHandlerAndReceiveMessages();
                    
                Console.ReadKey();

                await queueClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that will process messages
                queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
                await queueClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
                // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
               // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
4. Kör programmet och kontrollera portalen igen. Observera att värdena för **Antal aktiva meddelanden** och **Aktuell** nu är **0**.
   
    ![Kölängd][queue-message-receive]

Grattis! Nu har du skapat en kö, skickat en uppsättning meddelanden till kön och tagit emot meddelandena från samma kö.

## <a name="next-steps"></a>Nästa steg

Kolla in våra [GitHub-databaser med exempel](https://github.com/Azure/azure-service-bus/tree/master/samples) som visar några av de mer avancerade funktionerna i meddelandetjänsten i Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png

