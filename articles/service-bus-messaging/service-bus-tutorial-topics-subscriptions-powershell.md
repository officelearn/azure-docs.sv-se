---
title: Självstudie – Uppdatera butikssortimentet med publicera/avpublicera kanaler och ämnesfilter med Azure PowerShell | Microsoft Docs
description: I den här självstudien får du lära dig hur du skickar och tar emot meddelanden från ett ämne och en prenumeration och hur du lägger till och använder filterregler med Azure PowerShell
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 09/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 845fc32d527158258304a92c6855017c9d8c0492
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049565"
---
# <a name="tutorial-update-inventory-using-powershell-and-topicssubscriptions"></a>Självstudier: Uppdatera lager med hjälp av PowerShell och ämnen/prenumerationer

Azure Service Bus är en meddelandetjänst i molnet för flera klienter som skickar information mellan program och tjänst. Asynkrona åtgärder ger dig en flexibel, asynkron meddelandetjänst med funktioner för strukturerade meddelanden enligt FIFO-metoden (först-in-först-ut) och funktioner för publicering/prenumeration. 

Den här kursen visar hur du skickar och tar emot meddelanden till och från en Service Bus-kö med PowerShell för att skapa ett namnområde för meddelanden och en kö inom det namnområdet och hur du får autentiseringsuppgifterna för det namnområdet. Proceduren visar därefter hur du skickar och tar emot meddelanden från den här kön med [.NET standardbiblioteket](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett Service Bus-ämne och en eller flera prenumerationer på det ämnet med Azure PowerShell
> * Lägg till ämnesfilter med PowerShell
> * Skapa två meddelanden med olika innehåll
> * Skicka meddelandena och verifiera att de anlände i de förväntade prenumerationerna
> * Ta emot meddelanden från prenumerationerna

Ett exempel på det här scenariot är en uppdatering av lagersortimentet för flera butiker. I det här scenariot, får varje butik eller uppsättning butiker meddelanden för att uppdatera sina sortiment. Den här självstudien visar hur du implementerar det här scenariot med prenumerationer och filter. Först skapar du ett ämne med 3 prenumerationer, lägger till några regler och filter och skickar och tar sedan emot meddelanden från ämnet och prenumerationerna.

![ämne](./media/service-bus-tutorial-topics-subscriptions-powershell/about-service-bus-topic.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][] innan du börjar.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa de här självstudierna ska du kontrollera att du har installerat:

1. [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](https://www.visualstudio.com/vs) eller senare.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows), version 2.0 eller senare.

Den här självstudien kräver att du kör den senaste versionen av Azure PowerShell. Om du behöver installera eller uppgradera, kan du läsa [Installera och konfigurera Azure PowerShell][].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Kör följande kommandon för att logga in på Azure. De här stegen behövs inte om du kör PowerShell-kommandon i Cloud Shell: 

1. Installera Service Bus PowerShell-modulen:

   ```azurepowershell-interactive
   Install-Module Az.ServiceBus
   ```

2. Kör följande kommandon för att logga in på Azure:

   ```azurepowershell-interactive
   Login-AzAccount
   ```

4. Ange den aktuella prenumerationskontexten eller se den aktiva prenumerationen:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzContext
   ```

## <a name="provision-resources"></a>Etablera resurser

När du har loggat in på Azure kör du följande kommandon för att etablera Service Bus-resurser. Tänk på att ersätta alla platshållare med lämpliga värden:

```azurepowershell-interactive
# Create a resource group 
New-AzResourceGroup –Name my-resourcegroup –Location westus2

# Create a Messaging namespace
New-AzServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location westus2

# Create a queue 
New-AzServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

Efter att `Get-AzServiceBusKey`-cmdleten kör, kopierar och klistrar du in anslutningssträngen och det könamn du valt till en tillfällig plats som Anteckningar. Du behöver den i nästa steg.

## <a name="send-and-receive-messages"></a>Skicka och ta emot meddelanden

När namnområdet och kön skapas och du har de behörigheter som krävs, är du redo att skicka och ta emot meddelanden. Du kan granska koden i [den här GitHub-exempelmappen](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart).

Om du vill köra koden gör du följande:

1. Klona [Service Bus GitHub-lagringsplatsen](https://github.com/Azure/azure-service-bus/) genom att utfärda följande kommando:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Öppna en PowerShell-prompt.

3. Navigera till exempelmappen `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Om du inte redan har gjort det, kan du hämta anslutningssträngen med hjälp av följande PowerShell-cmdlet. Se till att ersätta `my-resourcegroup` och `namespace-name` med dina specifika värden: 

   ```azurepowershell-interactive
   Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```
5. Vid PowerShell-prompten, skriver du följande kommando:

   ```shell
   dotnet build
   ```
6. Gå till mappen `\bin\Debug\netcoreapp2.0`.
7. Skriv följande kommando för att köra programmet. Se till att ersätta `myConnectionString` med det värde du tidigare fick och `myQueueName` med namnet på den kö som du skapade:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 
8. Observera hur 10 meddelanden skickas till kön och därefter tas emot från kön:

   ![programmets utdata](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Rensa resurser

Kör följande kommando för att ta bort resursgruppen, namnområdet och alla relaterade resurser:

```powershell-interactive
Remove-AzResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Det här avsnittet innehåller mer information om vad exempelkoden gör. 

### <a name="get-connection-string-and-queue"></a>Hämta anslutningssträngen och kön

Anslutningssträngen och könamnet skickas till `Main()`-metoden som kommandoradsargument. `Main()` deklarerar två strängvariabler för dessa värden:

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

`MainAsync()`-metoden skapar en köklient med kommandoradsargumenten, anropar en mottagande meddelandehanteraren med namnet `RegisterOnMessageHandlerAndReceiveMessages()` och skickar meddelandeuppsättningen:

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

Den `RegisterOnMessageHandlerAndReceiveMessages()` metoden anger alternativ för hanteraren för några meddelanden och sedan anropar klienten kön `RegisterMessageHandler()` metod som startar mottagandet:

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

I den här självstudien, etablerade du resurser med hjälp av Azure PowerShell och därefter skickade du och tog emot meddelanden från ett Service Bus-ämne och dess prenumerationer. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Service Bus-ämne och en eller flera prenumerationer på det ämnet med Azure-portalen
> * Lägg till ämnesfilter med .NET-kod
> * Skapa två meddelanden med olika innehåll
> * Skicka meddelandena och verifiera att de anlände i de förväntade prenumerationerna
> * Ta emot meddelanden från prenumerationerna

Om du vill ha fler exempel på att skicka och ta emot meddelanden, kan du komma igång med [Service Bus-exempel på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Gå vidare till nästa självstudie för att läsa mer om att använda Service Bus-funktionerna publicera/prenumerera.

> [!div class="nextstepaction"]
> [Uppdatera lager med hjälp av PowerShell och ämnen/prenumerationer](service-bus-tutorial-topics-subscriptions-cli.md)

[Azure-konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Installera och konfigurera Azure PowerShell]: /powershell/azure/install-Az-ps
