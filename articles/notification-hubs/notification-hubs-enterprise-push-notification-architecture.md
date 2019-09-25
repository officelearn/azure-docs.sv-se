---
title: Notification Hubs-Enterprise push-arkitektur
description: Vägledning om hur du använder Azure Notification Hubs i en företags miljö
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 5b65fe6acb1fdf7ba79b106c876527c9b6736c5f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211900"
---
# <a name="enterprise-push-architectural-guidance"></a>Push-arkitekturvägledning för företag

Företag idag flyttar gradvis till att skapa mobil program för slutanvändare (externa) eller för de anställda (interna). De har befintliga backend-system på plats, de är stordatorer eller vissa LoB-program som måste integreras i arkitekturen för mobil program. Den här guiden beskriver hur du bäst använder den här integrationen för att rekommendera vanliga scenarier.

Ett frekvent krav är att skicka push-meddelanden till användarna via deras mobil program när en händelse av intresse inträffar i backend-systemen. Till exempel vill en bank kund som har bankens bank app på en iPhone meddelas när ett debetbelopp görs över en viss mängd från kontot eller ett intranäts scenario där en anställd från ekonomi avdelningen som har en app för godkännande av budget på en Windows Phone vill  att meddelas när begäran om godkännande tas emot.

Bank kontot eller godkännande bearbetningen kan förmodligen utföras i vissa Server dels system, vilket måste initiera en push-överföring till användaren. Det kan finnas flera sådana backend-system, vilket måste alla bygga samma typ av logik för att pusha när en händelse utlöser ett meddelande. Komplexiteten här är att integrera flera backend-system tillsammans med ett enda push-system där slutanvändarna kan ha prenumererat på olika meddelanden och det kan även finnas flera mobil program. Till exempel kan du använda mobila mobil program där ett mobilt program vill ta emot meddelanden från flera sådana Server dels system. Backend-systemen känner inte igen eller behöver känna till push-meddelanden/-teknik, så en gemensam lösning här traditionellt har varit att införa en komponent, som avsöker backend-systemen efter händelser av intresse och ansvarar för att skicka push-meddelanden till klientsession.

En bättre lösning är att använda Azure Service Bus-ämne/prenumerations modell, vilket minskar komplexiteten samtidigt som lösningen blir skalbar.

Här är den allmänna arkitekturen i lösningen (generaliseras med flera mobilappar, men gäller även när det bara finns en mobilapp)

## <a name="architecture"></a>Arkitektur

![][1]

Nyckel delen i det här arkitektur diagrammet är Azure Service Bus, vilket ger en programmerings modell för ämnen/prenumerationer (mer på den med [Service Bus pub/sub-programmering]). Mottagaren, som i det här fallet är mobil Server del (vanligt vis [Azure Mobile Service], som initierar en push-överföring till Mobile Apps) tar inte emot meddelanden direkt från backend-systemen utan i stället ett mellanliggande abstraktions lager [Azure Service Bus], som gör det möjligt för mobila Server delar att ta emot meddelanden från ett eller flera backend-system. Ett Service Bus ämne måste skapas för var och en av backend-systemen, till exempel konto, HR, ekonomi, som i princip är ämnen som är intressanta, som initierar meddelanden som ska skickas som push-meddelanden. Backend-systemen skickar meddelanden till dessa ämnen. En mobil Server del kan prenumerera på ett eller flera sådana ämnen genom att skapa en Service Bus prenumeration. Den ger den mobila Server delen för att ta emot ett meddelande från motsvarande Server dels system. Mobil Server del fortsätter att lyssna efter meddelanden på sina prenumerationer och så snart ett meddelande anländer, så blir det tillbaka och skickar det som ett meddelande till sin Notification Hub. Notification Hub skickar sedan meddelandet till mobilappen. Här är listan över viktiga komponenter:

1. Backend-system (LoB/äldre system)
   * Skapar Service Bus ämne
   * Skickar meddelande
1. Mobil serverdel
   * Skapar tjänst prenumeration
   * Tar emot meddelande (från backend-systemet)
   * Skickar meddelande till klienter (via Azure Notification Hub)
1. Mobil program
   * Ta emot och Visa meddelande

### <a name="benefits"></a>Fördelar

1. Kopplingen mellan mottagaren (mobilappen/tjänsten via Notification Hub) och avsändaren (backend-system) gör att ytterligare Server dels system integreras med minimal ändring.
1. Det gör också scenariot för flera mobilappar att kunna ta emot händelser från ett eller flera backend-system.  

## <a name="sample"></a>Exempel

### <a name="prerequisites"></a>Förutsättningar

Följ de här självstudierna för att bekanta dig med begreppen och vanliga & konfigurations steg för att skapa:

1. [Service Bus pub/sub-programmering] – i den här självstudien beskrivs information om hur du arbetar med Service Bus ämnen/prenumerationer, hur du skapar ett namn område som innehåller ämnen/prenumerationer, hur du skickar & ta emot meddelanden från dem.
2. [Notification Hubs Windows Universal-självstudie] – i den här självstudien beskrivs hur du konfigurerar en Windows Store-app och använder Notification Hubs för att registrera och ta emot meddelanden.

### <a name="sample-code"></a>Exempelkod

Den fullständiga exempel koden är tillgänglig i [Notification Hub-exempel]. Den delas upp i tre komponenter:

1. **EnterprisePushBackendSystem**

    a. I det här projektet används NuGet-paketet **windowsazure. Service Bus** och baseras på [Service Bus pub/sub-programmering].

    b. Det här programmet är en C# enkel konsol app för att simulera ett LOB-system, som initierar meddelandet som ska levereras till mobilappen.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
            CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the topic
        CreateTopic(connectionString);

        // Send message
        SendMessage(connectionString);
    }
    ```

    c. `CreateTopic`används för att skapa Service Bus avsnittet.

    ```csharp
    public static void CreateTopic(string connectionString)
    {
        // Create the topic if it does not exist already

        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.TopicExists(sampleTopic))
        {
            namespaceManager.CreateTopic(sampleTopic);
        }
    }
    ```

    d. `SendMessage`används för att skicka meddelanden till den här Service Bus avsnittet. Den här koden skickar bara en uppsättning slumpmässiga meddelanden till ämnet med jämna mellanrum för exemplet. Normalt finns det ett Server dels system som skickar meddelanden när en händelse inträffar.

    ```csharp
    public static void SendMessage(string connectionString)
    {
        TopicClient client =
            TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

        // Sends random messages every 10 seconds to the topic
        string[] messages =
        {
            "Employee Id '{0}' has joined.",
            "Employee Id '{0}' has left.",
            "Employee Id '{0}' has switched to a different team."
        };

        while (true)
        {
            Random rnd = new Random();
            string employeeId = rnd.Next(10000, 99999).ToString();
            string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

            // Send Notification
            BrokeredMessage message = new BrokeredMessage(notification);
            client.Send(message);

            Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

            System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
        }
    }
    ```
2. **ReceiveAndSendNotification**

    a. I det här projektet används *windowsazure. Service Bus* och **Microsoft. Web. WebJobs. publicera** NuGet-paket och baseras på [Service Bus pub/sub-programmering].

    b. Följande konsol program körs som ett [Azure-webbjobb] eftersom det måste köras kontinuerligt för att lyssna efter meddelanden från LOB/backend-systemen. Det här programmet är en del av din mobila Server del.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the subscription that receives messages
        CreateSubscription(connectionString);

        // Receive message
        ReceiveMessageAndSendNotification(connectionString);
    }
    ```

    c. `CreateSubscription`används för att skapa en Service Bus-prenumeration för det avsnitt där backend-systemet skickar meddelanden. Beroende på affärs scenariot skapar den här komponenten en eller flera prenumerationer på motsvarande ämnen (till exempel vissa kan ta emot meddelanden från HR-system, vissa från ekonomi system och så vidare)

    ```csharp
    static void CreateSubscription(string connectionString)
    {
        // Create the subscription if it does not exist already
        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
        {
            namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
        }
    }
    ```

    d. `ReceiveMessageAndSendNotification`används för att läsa meddelandet från avsnittet med hjälp av prenumerationen och om läsningen lyckas kan du skicka ett meddelande (i exempel scenariot ett Windows Native popup-meddelande) till det mobila programmet med Azure Notification Hubs.

    ```csharp
    static void ReceiveMessageAndSendNotification(string connectionString)
    {
        // Initialize the Notification Hub
        string hubConnectionString = CloudConfigurationManager.GetSetting
                ("Microsoft.NotificationHub.ConnectionString");
        hub = NotificationHubClient.CreateClientFromConnectionString
                (hubConnectionString, "enterprisepushservicehub");

        SubscriptionClient Client =
            SubscriptionClient.CreateFromConnectionString
                    (connectionString, sampleTopic, sampleSubscription);

        Client.Receive();

        // Continuously process messages received from the subscription
        while (true)
        {
            BrokeredMessage message = Client.Receive();
            var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

            if (message != null)
            {
                try
                {
                    Console.WriteLine(message.MessageId);
                    Console.WriteLine(message.SequenceNumber);
                    string messageBody = message.GetBody<string>();
                    Console.WriteLine("Body: " + messageBody + "\n");

                    toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                    SendNotificationAsync(toastMessage);

                    // Remove message from subscription
                    message.Complete();
                }
                catch (Exception)
                {
                    // Indicate a problem, unlock message in subscription
                    message.Abandon();
                }
            }
        }
    }
    static async void SendNotificationAsync(string message)
    {
        await hub.SendWindowsNativeNotificationAsync(message);
    }
    ```

    e. För att publicera den här appen som ett **webb jobb**, högerklicka på lösningen i Visual Studio och välj **Publicera som webb jobb**

    ![][2]

    f. Välj din publicerings profil och skapa en ny Azure-webbplats om den inte redan finns, som är värd för det här webb jobbet och när du har **publicerat**webbplatsen.

    ![][3]

    g. Konfigurera jobbet så att det körs kontinuerligt, så när du loggar in på [Azure Portal] bör du se något som liknar följande:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Det här programmet är ett Windows Store-program som tar emot popup-meddelanden från webb jobbet som körs som en del av din mobila Server del och visar det. Den här koden baseras på [Notification Hubs Windows Universal-självstudie].  

    b. Se till att ditt program är aktiverat för att ta emot popup-meddelanden.

    c. Se till att följande Notification Hubs registrerings kod anropas vid appens start (när du `HubName` har ersatt värdena och: `DefaultListenSharedAccessSignature`

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

### <a name="running-the-sample"></a>Köra exemplet

1. Kontrol lera att ditt webb jobb körs utan problem och att det är schemalagt att köras kontinuerligt.
2. Kör **EnterprisePushMobileApp**, som startar Windows Store-appen.
3. Kör **EnterprisePushBackendSystem** -konsol programmet, som simulerar LOB-Server delen och börjar skicka meddelanden och du bör se popup-meddelanden som visas som följande bild:

    ![][5]

4. Meddelandena skickades ursprungligen till Service Bus ämnen som övervakades av Service Bus prenumerationer i ditt webb jobb. När ett meddelande har tagits emot har ett meddelande skapats och skickats till mobilappen. Du kan titta igenom webb jobbs loggarna för att bekräfta bearbetningen när du går till länken loggar i [Azure Portal] för ditt webb jobb:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Notification Hub-exempel]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Service]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: https://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Service Bus pub/sub-programmering]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure-webbjobb]: ../app-service/webjobs-create.md
[Notification Hubs Windows Universal-självstudie]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure Portal]: https://portal.azure.com/
