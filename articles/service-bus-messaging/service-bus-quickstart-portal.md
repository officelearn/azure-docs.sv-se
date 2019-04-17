---
title: Snabbstart – Skapa en Service Bus-kö i Azure-portalen | Microsoft Docs
description: I den här snabbstarten lär du dig hur du skapar en Service Bus-kö med hjälp av Azure-portalen. Sedan använder du ett exempelklientprogram för att skicka meddelanden till och ta emot meddelanden från kön.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: spelluru
ms.openlocfilehash: 05c84f91c960bbcf7383cd2164289c8398f8dc91
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607763"
---
# <a name="quickstart-use-azure-portal-to-create-a-service-bus-queue"></a>Snabbstart: Skapa en Service Bus-kö i Azure-portalen
Den här snabbstarten beskriver hur man skickar och tar emot meddelanden till och från en Service Bus-kö med [Azure-portalen][Azure portal] för att skapa ett namnområde för meddelanden och en kö inom det namnområdet och för att få auktoriseringsuppgifter på det namnområdet. Proceduren visar därefter hur du skickar och tar emot meddelanden från den här kön med [.NET standardbiblioteket](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa de här självstudierna ska du kontrollera att du har installerat:

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][] innan du börjar.
- [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](https://www.visualstudio.com/vs) eller senare. Du kan använda Visual Studio för att skapa ett exempel som skickar meddelanden till och tar emot meddelanden från en kö. I exemplet är att testa kön som du skapade med hjälp av PowerShell. 
- [NET Core SDK](https://www.microsoft.com/net/download/windows), version 2.0 eller senare.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="send-and-receive-messages"></a>Skicka och ta emot meddelanden

Efter att namnområdet och kön har etablerats och du har de behörigheter som krävs, är du redo att skicka och ta emot meddelanden. Du kan granska koden i [den här GitHub-exempelmappen](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters).

Om du vill köra koden gör du följande:

1. Klona [Service Bus GitHub-lagringsplatsen](https://github.com/Azure/azure-service-bus/) genom att utfärda följande kommando:

   ```
   git clone https://github.com/Azure/azure-service-bus.git
   ```
3. Navigera till exempelmappen `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.
4. Kopiera den anslutningssträng och det könamn som du hämtade i avsnittet Hämta autentiseringsuppgifter.
5.  Skriv följande kommando i kommandotolken:

    ```
    dotnet build
    ```
6.  Gå till mappen `bin\Debug\netcoreapp2.0`.
7.  Skriv följande kommando för att köra programmet. Se till att ersätta `myConnectionString` med det värde du tidigare fick och `myQueueName` med namnet på den kö som du skapade:

    ```shell
    dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
    ``` 
8. Observera hur 10 meddelanden skickas till kön och därefter tas emot från kön:

   ![programmets utdata](./media/service-bus-quickstart-portal/dotnet.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda portalen för att ta bort resursgruppen, namnområdet och kön.

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Det här avsnittet innehåller mer information om vad exempelkoden gör. 

### <a name="get-connection-string-and-queue"></a>Hämta anslutningssträngen och kön

Anslutningsnamn för sträng- och kö har skickats till den `Main()` metod som kommandoradsargument. `Main()` deklarerar två strängvariabler för att hålla dessa värden:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
`Main()`-metoden startar därefter den asynkrona meddelandeloopen `MainAsync()`.

### <a name="message-loop"></a>Meddelandeloop

Metoden MainAsync() skapar en kö-klient med kommandoradsargument, anropar en mottagande meddelandehanterare som heter `RegisterOnMessageHandlerAndReceiveMessages()`, och skickar uppsättningen av meddelanden:

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

`RegisterOnMessageHandlerAndReceiveMessages()`-metoden anger ett antal alternativ för meddelandehanteraren och anropar sedan köklientens `RegisterMessageHandler()`-metod som startar mottagandet:

```csharp
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
```

### <a name="send-messages"></a>Skicka meddelanden

Åtgärderna skapa och skicka meddelanden uppstår i `SendMessagesAsync()`-metoden:

```csharp
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
```

### <a name="process-messages"></a>Bearbeta meddelanden

`ProcessMessagesAsync()`-metoden bekräftar, bearbetar och är slutför mottagandet av meddelanden:

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du ett Service Bus-namnområde och andra resurser som krävs för att skicka och ta emot meddelanden från en kö. Mer information om hur du skriver kod för att skicka och ta emot meddelanden, gå vidare till självstudiekurserna i den **skicka och ta emot meddelanden** avsnittet. 

> [!div class="nextstepaction"]
> [Skicka och ta emot meddelanden](service-bus-dotnet-get-started-with-queues.md)


[kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
