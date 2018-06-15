---
title: Notification Hubs – Enterprise Push-arkitektur
description: Anvisningar om hur du använder Azure Notification Hubs i en företagsmiljö
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: d7066b58330d35e5dba66cfe6ed5cfaddff4b68a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778070"
---
# <a name="enterprise-push-architectural-guidance"></a>Push-arkitekturvägledning för företag
Företag befordras idag gradvis mot skapa mobila program för slutanvändarna (externa) eller för anställda (intern). De har befintliga serverdelssystem på plats vara den stordatorer eller vissa LoB-program som integreras i arkitektur för mobila program. Den här guiden handlar om hur bäst att göra den här integreringen rekommenderar möjlig lösning till vanliga scenarier.

En ofta krävs för att skicka push-meddelanden till användare via sina mobila program när en händelse av intresse inträffar i backend-system. till exempel vill en bankkund som har den bank bank app på en iPhone meddelas när en debitering görs över en viss mängd från kontot eller ett intranätscenario där en medarbetare från ekonomiavdelningen som har en budget godkännande app på en Windows Phone vill  ska meddelas när begäran om godkännande tas emot.

Konto eller godkännande bearbetning kommer troligen att göras i vissa backend-systemet, vilket måste initiera en sändning till användaren. Det kan finnas flera sådana backend-system, skapa samma typ av logik för att push-när en händelse utlöser ett meddelande. Komplexitet här ligger i integrera flera serverdelssystem tillsammans med ett enda push-system där slutanvändarna kan prenumererar på olika meddelanden och kan även vara flera mobila program. Exempelvis intranät mobilappar när en mobilprogram kan vilja ta emot meddelanden från flera sådana backend-system. Backend-system inte känner till eller behöver veta för push-semantik/teknik så att en vanlig lösning har traditionellt att införa en komponent som genomsöker serverdelssystem för alla händelser och ansvarar för att skicka push-meddelanden till den klienten.

En bättre lösning är att använda Azure Service Bus - ämne /-prenumeration modellen, vilket minskar komplexiteten när lösningen skalbara.

Här är lösningen allmänna arkitektur (generaliserad med flera mobila appar men lika tillämpliga när det finns endast en mobil app)

## <a name="architecture"></a>Arkitektur
![][1]

Den nyckel i den här Arkitekturdiagram är Azure Service Bus, vilket ger en prenumerationer eller det avsnitt programmeringsmodellen (mer om den på [Service Bus Pub/Sub programmering]). Mottagaren som i det här fallet är mobila serverdel (vanligtvis [Azure-Mobiltjänst], som initierar en push för mobila appar) inte tar emot meddelanden direkt från backend-system utan istället en mellanliggande abstraktion lagret som tillhandahålls av [Azure Service Bus, vilket gör att mobilserverdel ta emot meddelanden från en eller flera serverdelssystem. Ett Service Bus-ämne måste skapas för varje backend-system, till exempel kontot, HR, ekonomi som är i grunden ”intresseområden”, som startar meddelanden ska skickas som push-meddelande. Backend-system skicka meddelanden till det här avsnittet. En mobila serverdel kan prenumerera på en eller flera ämnen genom att skapa en Service Bus-prenumeration. Den ger rätt mobilserverdel tar emot ett meddelande från motsvarande backend-systemet. Mobilserverdel fortsätter att lyssnar efter meddelanden på sina prenumerationer och när ett meddelande tas emot den stängs tillbaka och skickar det som meddelande till dess meddelandehubben. Meddelandehubbar skickar slutligen meddelandet till mobilappen. Här är listan med viktiga komponenter:

1. Serverdelssystem (LoB/äldre system)
   * Skapar Service Bus-ämne
   * Skickar meddelandet
2. Mobil serverdel
   * Skapar en prenumeration på tjänsten
   * Tar emot meddelandet (från Backend-system)
   * Skickar meddelandet till klienter (via Azure Notification Hub)
3. Mobila program
   * Tar emot och visa meddelandet

### <a name="benefits"></a>Fördelar:
1. Frikoppling mellan mottagaren (app/Mobiltjänst via Notification Hub) och avsändaren (serverdelssystem) gör att ytterligare serverdelssystem integreras med minimal förändring.
2. Gör det också scenariot för att kunna ta emot händelser från en eller flera serverdelssystem för flera mobila appar.  

## <a name="sample"></a>Exempel:
### <a name="prerequisites"></a>Förutsättningar
Slutför följande kurser att bekanta med koncept samt vanliga åtgärder för skapande och konfiguration:

1. [Service Bus Pub/Sub programmering] -den här självstudiekursen beskriver hur du arbetar med Service Bus avsnitt/prenumerationer, hur du skapar ett namnområde för att innehålla avsnitt/prenumerationer, så skicka och ta emot meddelanden från dem.
2. [Notification Hubs – självstudiekurs för Windows Universal] -den här självstudiekursen beskrivs hur du ställer in en app för Windows Store och använda Notification Hubs för att registrera och ta emot meddelanden.

### <a name="sample-code"></a>Exempelkod
Fullständig exempelkod finns på [Notification Hub exempel]. Den är uppdelad i tre komponenter:

1. **EnterprisePushBackendSystem**
   
    a. Det här projektet använder den *WindowsAzure.ServiceBus* NuGet-paketet och baseras på [Service Bus Pub/Sub programmering].
   
    b. Det här programmet är en enkel C#-konsolapp att simulera en LoB-systemet som startar meddelandet som ska levereras till mobilappen.
   
        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the topic
            CreateTopic(connectionString);
   
            // Send message
            SendMessage(connectionString);
        }
   
    c. `CreateTopic` används för att skapa Service Bus-ämne.
   
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
   
    d. `SendMessage` används för att skicka meddelanden till den här Service Bus-ämne. Den här koden skickar bara slumpmässiga meddelanden till avsnittet regelbundet i exemplet. Det är normalt en backend-system som skickar meddelanden när en händelse inträffar.
   
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
2. **ReceiveAndSendNotification**
   
    a. Det här projektet använder den *WindowsAzure.ServiceBus* och *Microsoft.Web.WebJobs.Publish* NuGet-paket och baseras på [Service Bus Pub/Sub programmering].
   
    b. Följande konsolprogram som körs som en [Azure Webjobs] eftersom den har ska köras för att lyssna efter meddelanden från LoB-/ serverdelssystem. Det här programmet är en del av din mobila serverdel.
   
        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the subscription that receives messages
            CreateSubscription(connectionString);
   
            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }
   
    c. `CreateSubscription` används för att skapa en Service Bus-prenumeration för avsnittet där backend-systemet skickar meddelanden. Beroende på affärsscenario, skapar den här komponenten en eller flera prenumerationer till motsvarande avsnitt (till exempel vissa kan få meddelanden från HR-system, vissa från ekonomi system och så vidare)
   
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
   
    d. ReceiveMessageAndSendNotification används för att läsa meddelandet från avsnittet med hjälp av prenumerationen och om Läs lyckas skapa en avisering (i Exempelscenario ett Windows interna popup-meddelande) för att skickas till den mobila program med hjälp av Azure Notification Hubs.
   
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
   
    e. För att publicera den här appen som en **Webbjobb**, högerklicka på lösningen i Visual Studio och välj **Publicera som Webbjobbet**
   
    ![][2]
   
    f. Välj din publiceringsprofil och skapa en ny Azure-webbplats om den inte redan finns, som är värd för den här Webbjobb och när du har webbplatsen sedan **publicera**.
   
    ![][3]
   
    g. Konfigurera jobbet för att vara ”kör kontinuerligt” så att när du loggar in på den [Azure-portalen] bör du se något som liknar följande:
   
    ![][4]
3. **EnterprisePushMobileApp**
   
    a. Det här programmet är ett Windows Store-program som tar emot popup-meddelanden från Webbjobb som körs som en del av din mobila serverdel och visa den. Den här koden är baserad på [Notification Hubs – självstudiekurs för Windows Universal].  
   
    b. Kontrollera att programmet är aktiverat för att ta emot popup-meddelanden.
   
    c. Se till att följande Notification Hubs registration kod kallas vid appen startar (när du ersätter den *HubName* och *DefaultListenSharedAccessSignature*:
   
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

### <a name="running-sample"></a>Köra exemplet:
1. Kontrollera att din Webbjobbet har körts och schemalagd för att köras.
2. Kör den ** EnterprisePushMobileApp som startar Windows Store-app.
3. Kör den **EnterprisePushBackendSystem** konsolprogram som simulerar LoB-serverdelen och börjar skicka meddelanden och du bör se popup-meddelanden som visas som i följande bild:
   
    ![][5]
4. Meddelandena som har ursprungligen skickats till Service Bus-ämnen som övervakades med Service Bus prenumerationer i ditt webb-jobb. När ett meddelande togs emot ett meddelande skapades och skickas till mobilappen. Du kan titta igenom Webbjobb loggfilerna för att bekräfta bearbetningen när du går till länken loggar i [Azure-portalen] för Web-jobb:
   
    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Notification Hub exempel]: https://github.com/Azure/azure-notificationhubs-samples
[Azure-Mobiltjänst]: http://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Service Bus Pub/Sub programmering]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure Webjobs]: ../app-service/web-sites-create-web-jobs.md
[Notification Hubs – självstudiekurs för Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure-portalen]: https://portal.azure.com/
