
I det här avsnittet kan skicka du de senaste nyheterna som formaterad mallen meddelanden från en .NET-konsolapp.

Om du använder funktionen Mobilappar i Microsoft Azure App Service finns i [Lägg till push-meddelanden för Mobile Apps] kursen, och välj din plattform längst upp.

Om du vill använda Java eller PHP finns [hur du använder Notification Hubs från Java eller PHP]. Du kan skicka meddelanden från en serverdel med hjälp av den [Notification Hub REST-gränssnittet].

Om du har skapat konsolprogram för att skicka meddelanden när du slutfört [Kom igång med Notification Hubs], hoppa över steg 1-3.

1. Skapa en ny Visual C#-konsolapp i Visual Studio:
   
      ![Länken konsolprogram][13]

2. På huvudmenyn Visual Studio, markera **verktyg** > **Library Package Manager** > **Pakethanterarkonsolen** och sedan, i konsolen fönstret, anger du följande sträng:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
3. Välj **ange**.  
    Den här åtgärden lägger till en referens i Azure Notification Hubs SDK med hjälp av [Microsoft.Azure.Notification Hubs-NuGet-paketet].

4. Öppna filen Program.cs och Lägg till följande `using` instruktionen:
   
        using Microsoft.Azure.NotificationHubs;

5. I den `Program` klassen, lägger du till följande metod eller ersätta den om den redan finns:
   
        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub =
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");
   
            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business",
                                            "Technology", "Science", "Sports"};
   
            // Send the notification as a template notification. All template registrations that contain
            // "messageParam" and the proper tags will receive the notifications.
            // This includes APNS, GCM, WNS, and MPNS template registrations.
   
            Dictionary<string, string> templateParams = new Dictionary<string, string>();
   
            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
         }
   
    Den här koden skickar ett meddelande om mallen för var och en av de sex taggarna i Strängmatrisen. Användning av taggar garanterar att enheter får aviseringar endast för registrerade kategorier.

5. I föregående kod, ersätter den `<hub name>` och `<connection string with full access>` platshållarna med namnet på din meddelandehubb och anslutningssträngen för *DefaultFullSharedAccessSignature* från instrumentpanelen i din meddelandehubb.

6. Lägg till följande rader i **Main**-metoden:
   
         SendTemplateNotificationAsync();
         Console.ReadLine();

7. Bygga appen konsolen.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Kom igång med Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hub REST-gränssnittet]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Lägg till push-meddelanden för Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[hur du använder Notification Hubs från Java eller PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.Notification Hubs-NuGet-paketet]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
