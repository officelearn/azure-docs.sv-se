---
title: Notification Hubs – Enterprise Push-arkitektur
description: Vägledning om hur du använder Azure Notification Hubs i en företagsmiljö
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 938801148b175456553865b54d59271021811401
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372428"
---
# <a name="enterprise-push-architectural-guidance"></a>Push-arkitekturvägledning för företag

Företag befordras idag gradvis mot skapar mobila applikationer för antingen slutanvändarna (extern) eller för anställda (internt). De har befintliga serverdelssystem på plats stordatorer och vissa LoB-program som integreras i arkitekturen för mobila program. Den här guiden pratar om hur bäst att göra den här integreringen rekommenderar möjlig lösning till vanliga scenarier.

En ofta krävs för att skicka push-meddelande till användare via deras mobila program när en händelse av intresse som inträffar i backend-system. till exempel vill en bankkund som har bankens bank-appen på en iPhone meddelas när en debitering görs över en viss mängd från kontot eller en intranätscenario där en medarbetare från ekonomiavdelningen som har en budget godkännande-app på en Windows Phone-vill  att bli meddelad när en begäran om godkännande tas emot.

Bank-konto eller godkännande bearbetning troligtvis som ska utföras i vissa backend-system som måste initiera en push till användaren. Det kan finnas flera sådana backend-system, vilket skapar samma typ av logik för att push-när en händelse utlöser en avisering. Komplexiteten här ligger i att integrera flera backend-system tillsammans med ett enda push-system där slutanvändarna kan prenumererar på olika meddelanden och det kan även finnas flera mobila program. Exempelvis intranät mobilappar när en mobilprogram kan vilja ta emot meddelanden från flera sådana backend-system. Backend-system inte känner till eller behöver veta om push-semantik/teknik så att en vanlig lösning har traditionellt att införa en komponent som ska avsöka backend-system för händelser av intresse och ansvarar för att skicka push-meddelanden till den klient.

En bättre lösning är att använda Azure Service Bus - ämne/prenumeration modellen, vilket minskar komplexiteten när du gjorde lösningen skalbara.

Här är lösningen allmän arkitektur (generaliserad med flera mobilappar men lika tillämpliga när det är bara en mobilapp)

## <a name="architecture"></a>Arkitektur

![][1]

Viktig i den här Arkitekturdiagram är Azure Service Bus, vilket ger en ämnen/prenumerationer programmeringsmodellen (mer om den på [Service Bus Pub/Sub-programmering]). Mottagare, som i det här fallet är mobil serverdel (vanligtvis [Azure Mobile Service], som initierar en push för mobila appar) tar inte emot meddelanden direkt från backend-system utan istället en mellanliggande abstraktion lagret som tillhandahålls av [Azure Service Bus], vilket gör att mobilserverdel ta emot meddelanden från en eller flera backend-system. En Service Bus-ämne måste skapas för var och en av backend-system, till exempel konto, HR, ekonomi, som i praktiken är ”intresseområden”, som startar meddelanden skickas som push-meddelande. Backend-system skicka meddelanden till det här avsnittet. En mobil serverdel kan prenumerera på att en eller flera ämnen genom att skapa en Service Bus-prenumeration. Den ger rätt mobilserverdel tar emot ett meddelande från motsvarande backend-system. Mobil serverdel fortsätter att lyssna efter meddelanden i sina prenumerationer och när ett meddelande anländer, den sätts och skickar den som meddelandet till dess meddelandehubben. Meddelandehubbar sedan leverera så småningom meddelande till mobilappen. Här är listan över viktiga komponenter:

1. Backend-system (LoB/äldre system)
   * Skapar Service Bus-ämne
   * Skickar meddelande
1. Mobil serverdel
   * Skapar prenumeration på tjänsten
   * Tar emot meddelande (från Backend-system)
   * Skickar meddelande till klienter (via Azure Notification Hub)
1. Mobilprogram
   * Tar emot och aviseringar

### <a name="benefits"></a>Fördelar

1. Frikoppling mellan mottagare (mobila appen/tjänsten via Notification Hub) och avsändare (backend-system) kan ytterligare backend-system som integreras med minimal ändringen.
1. Gör det också scenario där flera mobila appar att kunna ta emot händelser från en eller flera backend-system.  

## <a name="sample"></a>Exempel

### <a name="prerequisites"></a>Förutsättningar

Slutför följande självstudiekurser och bekanta med koncept samt vanliga steg för skapande och konfiguration:

1. [Service Bus Pub/Sub-programmering] – den här självstudiekursen beskriver hur du arbetar med Service Bus-ämnen/prenumerationer, hur du skapar ett namnområde för att innehålla ämnen/prenumerationer, så skicka och ta emot meddelanden från dem.
2. [Notification Hubs – självstudiekurs för Windows Universal] – den här självstudien beskrivs hur du konfigurerar en Windows Store-app och använder Notification Hubs för att registrera och ta emot meddelanden.

### <a name="sample-code"></a>Exempelkod

Fullständig exempelkoden finns på [Notification Hub-exempel]. Det är uppdelat i tre komponenter:

1. **EnterprisePushBackendSystem**

    a. Det här projektet använder den **WindowsAzure.ServiceBus** NuGet-paketet och baseras på [Service Bus Pub/Sub-programmering].

    b. Det här programmet är en enkel C#-konsolapp att simulera en LoB-system, som startar meddelandet som ska levereras till mobilappen.

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

    c. `CreateTopic` används för att skapa Service Bus-ämne.

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

    d. `SendMessage` används för att skicka meddelanden till den här Service Bus-ämne. Den här koden skickar helt enkelt en uppsättning slumpmässiga meddelanden till ämnet med jämna mellanrum i exemplet. Det är normalt en backend-system som skickar meddelanden när en händelse inträffar.

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

    a. Det här projektet använder den *WindowsAzure.ServiceBus* och **Microsoft.Web.WebJobs.Publish** NuGet-paket och baseras på [Service Bus Pub/Sub-programmering].

    b. Följande konsolappen körs som en [Azure WebJob] eftersom den måste köras kontinuerligt för att lyssna efter meddelanden från LoB-/ serverdelssystem. Det här programmet är en del av din mobila serverdelstjänst.

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

    c. `CreateSubscription` används för att skapa en Service Bus-prenumerationen för ämnet där backend-systemet skickar meddelanden. Beroende på scenario företag skapar en eller flera prenumerationer till motsvarande avsnitt i den här komponenten (till exempel vissa kan få meddelanden från HR-systemet, vissa från finans-system och så vidare)

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

    d. `ReceiveMessageAndSendNotification` används för att läsa meddelandet från ämnet med hjälp av dess prenumeration och Läs genomförs sedan skapa ett meddelande (i Exempelscenario ett Windows interna popup-meddelande) för att skickas till den mobila program med Azure Notification Hubs.

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

    e. För att publicera den här appen som en **WebJob**, högerklicka på lösningen i Visual Studio och välj **Publicera som WebJob**

    ![][2]

    f. Välj din publiceringsprofil och skapa en ny Azure-webbplats om den inte redan finns, som är värd för den här WebJob och när du har webbplatsen du **publicera**.

    ![][3]

    g. Konfigurera jobbet för att vara ”kör kontinuerligt” så att när du loggar in till den [Azure Portal] du bör se ut ungefär så här:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Det här programmet är ett Windows Store-program som tar emot popup-meddelanden från Webbjobbet körs som en del av din mobila serverdelstjänst och visa den. Den här koden är baserad på [Notification Hubs – självstudiekurs för Windows Universal].  

    b. Kontrollera att ditt program är aktiverat för att ta emot popup-meddelanden.

    c. Se till att följande Notification Hubs Registreringskod anropas vid appen startar (när du har ersatt den `HubName` och `DefaultListenSharedAccessSignature` värden:

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

1. Kontrollera att ditt WebJob har körts och schemalagd för att köras kontinuerligt.
2. Kör den **EnterprisePushMobileApp**, vilket startar Windows Store-app.
3. Kör den **EnterprisePushBackendSystem** konsolappen, som simulerar LoB-serverdelen och börjar skicka meddelanden och du bör se popup-meddelanden som visas som på följande bild:

    ![][5]

4. Meddelandena som har ursprungligen skickats till Service Bus-ämnen som har som övervakas av Service Bus-prenumerationer i ditt webb-jobb. När ett meddelande togs emot ett meddelande skapades och skickas till den mobila appen. Du kan söka igenom WebJob-loggar att bekräfta bearbetningen när du går till länken loggar i [Azure Portal] för ditt webb-jobb:

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
[Service Bus Pub/Sub-programmering]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: ../app-service/webjobs-create.md
[Notification Hubs – självstudiekurs för Windows Universal]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure Portal]: https://portal.azure.com/
