---
title: Push-arkitektur för meddelandehubbar
description: Lär dig mer om hur du använder Azure Notification Hubs i en företagsmiljö
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
ms.openlocfilehash: 0104547a432f7f78d74731e11926bcd82088cef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264041"
---
# <a name="enterprise-push-architectural-guidance"></a>Push-arkitekturvägledning för företag

Företag idag är gradvis på väg mot att skapa mobila applikationer för antingen sina slutanvändare (externa) eller för de anställda (interna). De har befintliga backend-system på plats oavsett om det finns stordatorer eller vissa LoB-applikationer, som måste integreras i den mobila applikationsarkitekturen. Den här guiden beskriver hur du bäst gör den här integrationen och rekommenderar en möjlig lösning på vanliga scenarier.

Ett vanligt krav är att skicka push-meddelanden till användarna via deras mobilapplikation när en händelse av intresse inträffar i backend-systemen. En bankkund som har bankens bankapp på en iPhone vill till exempel bli meddelad när en debet görs över ett visst belopp från kontot eller ett intranätscenario där en anställd från ekonomiavdelningen som har en budgetgodkännandeapp på en Windows Phone vill ha meddelas när begäran om godkännande tas emot.

Bankkontot eller godkännande bearbetning kommer sannolikt att göras i vissa backend system, som måste initiera en push till användaren. Det kan finnas flera sådana serverdsystem, som alla måste bygga samma typ av logik för att driva när en händelse utlöser ett meddelande. Komplexiteten här ligger i att integrera flera backend-system tillsammans med ett enda push-system där slutanvändarna kan ha prenumererat på olika anmälningar och det kan även finnas flera mobila applikationer. Till exempel intranät mobilappar där en mobil applikation kanske vill ta emot meddelanden från flera sådana backend-system. Backend-systemen vet inte eller behöver känna till push semantik / teknik så en gemensam lösning här traditionellt har varit att införa en komponent, som opinionsundersökningar backend system för alla händelser av intresse och är ansvarig för att skicka push-meddelanden till kunden.

En bättre lösning är att använda Azure Service Bus - Ämne/Prenumerationsmodell, vilket minskar komplexiteten samtidigt som lösningen blir skalbar.

Här är den allmänna arkitekturen i lösningen (generaliserad med flera mobilappar men lika tillämplig när det bara finns en mobilapp)

## <a name="architecture"></a>Arkitektur

![][1]

Den viktigaste delen i det här arkitektoniska diagrammet är Azure Service Bus, som innehåller en programmeringsmodell för ämnen/prenumerationer (mer om den på [Service Bus Pub/Sub-programmering).] Mottagaren, som i det här fallet är mobile-serverdelen (vanligtvis [Azure Mobile Service], som initierar en push till mobilapparna) tar inte emot meddelanden direkt från serverdelssystemen utan i stället ett mellanliggande abstraktionslager som tillhandahålls av Azure Service [Bus], vilket gör att mobil serverdel kan ta emot meddelanden från ett eller flera backend-system. Ett servicebussämne måste skapas för var och en av serverdelssystemen, till exempel Konto, HR, Ekonomi, som i grunden är "ämnen" av intresse, som initierar meddelanden som ska skickas som push-meddelande. Serveringssystemen skickar meddelanden till dessa ämnen. En Mobile Backend kan prenumerera på ett eller flera sådana ämnen genom att skapa en Service Bus-prenumeration. Det ger den mobila backend att få ett meddelande från motsvarande backend-system. Mobile backend fortsätter att lyssna efter meddelanden på sina prenumerationer och så fort ett meddelande anländer, vänder tillbaka och skickar den som ett meddelande till sin anmälan nav. Meddelandehubbar levererar sedan meddelandet så småningom till mobilappen. Här är listan över viktiga komponenter:

1. Backend-system (LoB/Legacy-system)
   * Skapar servicebussavsnitt
   * Skickar meddelande
1. Mobil backend
   * Skapar tjänstprenumeration
   * Tar emot meddelande (från Serverd-system)
   * Skickar meddelande till klienter (via Azure Notification Hub)
1. Mobilprogram
   * Ta emot och visa meddelande

### <a name="benefits"></a>Fördelar

1. Frikopplingen mellan mottagaren (mobilapp/tjänst via Notification Hub) och avsändare (backend-system) gör att ytterligare backend-system kan integreras med minimal förändring.
1. Det gör också scenariot med flera mobilappar att kunna ta emot händelser från en eller flera backend-system.  

## <a name="sample"></a>Exempel

### <a name="prerequisites"></a>Krav

Slutför följande självstudier för att bekanta dig med begreppen samt vanliga & konfigurationssteg:

1. [Service Bus Pub/Sub programmering] - Den här självstudien förklarar information om hur du arbetar med Service Bus Ämnen / Prenumerationer, hur man skapar ett namnområde för att innehålla ämnen / prenumerationer, hur man skickar & ta emot meddelanden från dem.
2. [Notification Hubs - Windows Universal tutorial] - Den här självstudien förklarar hur du konfigurerar en Windows Store-app och använder Meddelandehubbar för att registrera och sedan ta emot meddelanden.

### <a name="sample-code"></a>Exempelkod

Den fullständiga exempelkoden finns på [Notification Hub Samples]. Den är uppdelad i tre komponenter:

1. **FöretagPushBackendSystem**

    a. Det här projektet använder **WindowsAzure.ServiceBus** NuGet-paketet och baseras på [Service Bus Pub/Sub-programmering].

    b. Detta program är en enkel C# konsol app för att simulera ett LoB-system, som initierar meddelandet som ska levereras till mobilappen.

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

    c. `CreateTopic`används för att skapa servicebussämnet.

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

    d. `SendMessage`används för att skicka meddelandena till det här servicebussavsnittet. Den här koden skickar helt enkelt en uppsättning slumpmässiga meddelanden till ämnet med jämna mellanrum för exemplet. Normalt finns det ett serverdsystem som skickar meddelanden när en händelse inträffar.

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
2. **Ta emotOchSendNotification**

    a. Det här projektet använder *WindowsAzure.ServiceBus* och **Microsoft.Web.WebJobs.Publish** NuGet-paket och baseras på [Service Bus Pub/Sub-programmering].

    b. Följande konsolapp körs som ett [Azure WebJob] eftersom det måste köras kontinuerligt för att lyssna efter meddelanden från LoB/backend-systemen. Denna applikation är en del av din Mobile backend.

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

    c. `CreateSubscription`används för att skapa en Service Bus-prenumeration för det ämne där serverdelssystemet skickar meddelanden. Beroende på affärsscenariot skapar den här komponenten en eller flera prenumerationer på motsvarande ämnen (till exempel kan vissa ta emot meddelanden från HR-systemet, vissa från Finance-systemet och så vidare)

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

    d. `ReceiveMessageAndSendNotification`används för att läsa meddelandet från ämnet med hjälp av dess prenumeration och om läsningen lyckas sedan skapa ett meddelande (i exempelscenariot ett Windows native toast-meddelande) som ska skickas till det mobila programmet med Azure Notification Hubs.

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

    e. För att publicera den här appen som ett **WebJob**högerklickar du på lösningen i Visual Studio och väljer **Publicera som WebJob**

    ![][2]

    f. Välj din publiceringsprofil och skapa en ny Azure WebSite om den inte redan finns, som är värd för det här webbjobbet och när du har website sedan **Publicera**.

    ![][3]

    g. Konfigurera jobbet så att det blir "Kör kontinuerligt" så att du bör se något liknande följande när du loggar in på [Azure-portalen:]

    ![][4]

3. **FöretagPushMobileApp**

    a. Det här programmet är ett Windows Store-program som tar emot popup-meddelanden från WebJob som körs som en del av din Mobile-backend och visar den. Den här koden är baserad på [Notification Hubs - Windows Universal tutorial].  

    b. Kontrollera att ditt program är aktiverat för att ta emot popup-meddelanden.

    c. Kontrollera att följande registreringskod för Meddelandehubbar anropas vid `HubName` appstarten (efter att du har ersatt värdena och: `DefaultListenSharedAccessSignature`

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

1. Kontrollera att WebJob körs och att den ska köras kontinuerligt.
2. Kör **EnterprisePushMobileApp**, som startar Windows Store-appen.
3. Kör **Programmet EnterprisePushBackendSystem,** som simulerar LoB-serverdelen och börjar skicka meddelanden och du bör se popup-meddelanden som visas som följande bild:

    ![][5]

4. Meddelandena skickades ursprungligen till Service Bus-ämnen, som övervakades av Service Bus-prenumerationer i webbjobbet. När ett meddelande togs emot skapades ett meddelande och skickades till mobilappen. Du kan titta igenom WebJob-loggarna för att bekräfta bearbetningen när du går till länken Loggar i [Azure-portalen] för ditt webbjobb:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Exempel på meddelandehubb]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile-tjänst]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: https://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Service Bus Pub/Sub programmering]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: ../app-service/webjobs-create.md
[Meddelandehubbar - Självstudiekurs för Windows Universal]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure-portal]: https://portal.azure.com/
