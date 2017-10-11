



När du skickar meddelanden om mallar du behöver bara ange en uppsättning egenskaper, i vårt fall skickar vi en uppsättning egenskaper som innehåller den lokaliserade versionen av den aktuella nyheten, till exempel:

    {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin": "World News in Mandarin!"
    }


Det här avsnittet visas hur du skickar meddelanden med hjälp av en konsolapp

Inkluderade koden skickar till Windows Store- och iOS-enheter, eftersom serverdelen kan skicka till någon av enheterna som stöds.

### <a name="to-send-notifications-using-a-c-console-app"></a>Att skicka meddelanden med en C#-konsolapp
Ändra den `SendTemplateNotificationAsync` metoden i konsolen appen som du skapade tidigare med följande kod. Observera hur i det här fallet finns inget behov av att skicka flera meddelanden för olika språk och plattformar.

        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Sending the notification as a template notification. All template registrations that contain 
            // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
            // This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
            var locales = new string[] { "English", "French", "Mandarin" };

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";

                // Sending localized News for each tag too...
                foreach( var locale in locales)
                {
                    string key = "News_" + locale;

                    // Your real localized news content would go here.
                    templateParams[key] = "Breaking " + category + " News in " + locale + "!";
                }

                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
        }


Observera att det här enkla anropet levererar lokaliserade typ av nyheterna till **alla** dina enheter, oavsett plattformen som din Meddelandehubb bygger och ger korrekt interna nyttolasten för alla enheter som prenumererar på en specifik taggen.

### <a name="sending-the-notification-with-mobile-services"></a>Skicka meddelanden med Mobile Services
Du kan använda följande skript i din Mobiltjänst Schemaläggaren:

    var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
            "News_English": "World News in English!",
            "News_French": "World News in French!",
            "News_Mandarin", "World News in Mandarin!"
    }
    notificationHubService.send('World', notification, function(error) {
        if (!error) {
            console.warn("Notification successful");
        }
    });


