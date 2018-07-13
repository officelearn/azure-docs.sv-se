---
title: Kom igång med ämnen och prenumerationer i Azure Service Bus | Microsoft Docs
description: Skriv ett C# .NET Core-konsolprogram som använder meddelandeämnen och prenumerationer i Service Bus.
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 07/03/2018
ms.author: sethm
ms.openlocfilehash: 8916ccb430da6bea66de3894201a11a70bb45df9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38298970"
---
# <a name="get-started-with-service-bus-topics"></a>Kom igång med Service Bus-ämnen

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Den här självstudien omfattar följande steg:

1. Skapa ett Service Bus-namnområde med Azure Portal.
2. Skapa ett Service Bus-ämne med Azure Portal.
3. Skapa en Service Bus-prenumeration på ämnet med Azure Portal.
4. Skriva ett .NET Core-konsolprogram för att skicka en uppsättning meddelanden till ämnet.
5. Skriva ett .NET Core-konsolprogram för att ta emot meddelanden från prenumerationen.

## <a name="prerequisites"></a>Nödvändiga komponenter

1. [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](http://www.visualstudio.com/vs) eller senare.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows), version 2.0 eller senare.
2. En Azure-prenumeration.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Skapa ett namnområde med Azure Portal

> [!NOTE] 
> Du kan också skapa ett Service Bus-namnområde och meddelandeentiteter med [PowerShell](/powershell/azure/get-started-azureps). Mer information finns i [Använda PowerShell för att hantera Service Bus-resurser](service-bus-manage-with-ps.md).

Om du redan har skapat ett namnområde för Service Bus-meddelanden går du vidare till avsnittet [Skapa ett ämne med Azure Portal](#2-create-a-topic-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Skapa ett ämne med Azure Portal

1. Logga in på [Azure Portal][azure-portal].
2. I det vänstra navigeringsfönstret i portalen klickar du på **Service Bus** (om du inte ser **Service Bus** klickar du på **Alla tjänster** eller på **Alla resurser**). Klicka på det namnområde där du vill skapa ämnet. 
3. Översiktsfönstret för namnområdet öppnas. Klicka på **Ämnen**:
   
    ![Skapa ett ämne][createtopic1]
4. Klicka på **+ ämne**.
   
    ![Välja ämnen][createtopic2]
5. Ange ett namn för ämnet. Lämna standardvärdena för de andra alternativen.
   
    ![Välj ny][createtopic3]
6. Klicka på **Skapa** längst ned i dialogrutan.

## <a name="3-create-a-subscription-to-the-topic"></a>3. Skapa en prenumeration på ämnet

1. I fönstret med portalresurser klickar du på det namnområde du skapade i steg 1. Klicka sedan på **Ämnen** och klicka på namnet på det ämne du skapade i steg 2.
2. Klicka på **+ Prenumeration** längst upp i översiktsfönstret för att lägga till en prenumeration på det här ämnet.

    ![Skapa en prenumeration][createtopic4]

3. Ange ett namn för prenumerationen. Lämna standardvärdena för de andra alternativen.

## <a name="4-send-messages-to-the-topic"></a>4. Skicka meddelanden till ämnet

För att kunna skicka meddelanden till ämnet skriver du ett C#-konsolprogram med Visual Studio.

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

Starta Visual Studio och skapa ett nytt projekt: **Konsolprogram (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Lägga till Service Bus-NuGet-paketet

1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.
2. Klicka på fliken **Bläddra**, sök efter **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** och markera posten **Microsoft.Azure.ServiceBus**. Klicka på **Installera** för att slutföra installationen och stäng sedan den här dialogrutan.
   
    ![Välj ett NuGet-paket][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>Skriva kod för att skicka meddelanden till ämnet

1. I Program.cs lägger du till följande `using`-instruktioner överst i definitionen för namnområdet, före klassdeklarationen:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. I `Program`-klassen deklarerar du följande variabler. Ställ in variabeln `ServiceBusConnectionString` på den anslutningssträng du fick när du skapade namnområdet, och ställ in `TopicName` på namnet du använde när du skapade ämnet:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
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
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
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
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. Så här bör din Program.cs-sändningsfil se ut.
   
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
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send messages.
                await SendMessagesAsync(numberOfMessages);

                Console.ReadKey();

                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
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

3. Kör programmet och kontrollera Azure Portal: klicka på ämnets namn i fönstret **Översikt** för namnområdet. Skärmen **Grundläggande** för ämnet visas. Titta på prenumerationen som visas längst ned i fönstret, och lägg märke till att värdet **Antal meddelanden** för prenumerationen nu är **10**. Varje gång du kör sändningsprogrammet utan att hämta meddelanden (beskrivs i nästa avsnitt) ökar detta värde med 10. Observera också att ämnets aktuella storlek ökar värdet **Aktuell** i fönstret **Grundläggande** varje gång programmet lägger till meddelanden i ämnet.
   
      ![Meddelandestorlek][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. Ta emot meddelanden från prenumerationen

För att ta emot de meddelanden som du just skickade skapar du ytterligare ett .NET Core-konsolprogram och installerar **Microsoft.Azure.ServiceBus** NuGet-paketet, på ungefär samma sätt som i det tidigare sändningsprogrammet.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>Skriva kod för att ta emot meddelanden från prenumerationen

1. I Program.cs lägger du till följande `using`-instruktioner överst i definitionen för namnområdet, före klassdeklarationen:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. I `Program`-klassen deklarerar du följande variabler. Ange variabeln `ServiceBusConnectionString` till den anslutningssträng som du fick när du skapade namnområdet, ange `TopicName` till det namn som du använde när du skapade ämnet och ange `SubscriptionName` till det namn som du använde när du skapade prenumerationen för ämnet:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. Ersätt standardinnehållet i `Main()` med följande rad med kod:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Direkt efter `Main()` lägger du till följande asynkrona `MainAsync()`-metod som anropar metoden `RegisterOnMessageHandlerAndReceiveMessages()`:

    ```csharp
    static async Task MainAsync()
    {
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();
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
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. Direkt efter den föregående metoden lägger du till följande `ProcessMessagesAsync()`-metod för att bearbeta de mottagna meddelandena:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
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

8. Så här bör din Program.cs-mottagningsfil se ut:
   
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
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register subscription message handler and receive messages in a loop.
                RegisterOnMessageHandlerAndReceiveMessages();

                Console.ReadKey();

                await subscriptionClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
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
9. Kör programmet och kontrollera portalen igen. Observera att värdena för **Antal meddelanden** och **Aktuell** nu är **0**.
   
    ![Ämnets längd][topic-message-receive]

Grattis! Med .NET Standard-biblioteket har du nu skapat ett ämne och en prenumeration, skickat tio meddelanden och tagit emot dessa meddelanden.

## <a name="next-steps"></a>Nästa steg

Kolla in Service Bus [GitHub-databaser med exempel](https://github.com/Azure/azure-service-bus/tree/master/samples) som visar några av de mer avancerade funktionerna i meddelandetjänsten i Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
