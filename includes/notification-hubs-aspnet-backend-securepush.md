---
author: sethmanheim
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: sethm
ms.openlocfilehash: 7fd161c90234d45a6751f173ba3685ee8c392c87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74260894"
---
## <a name="webapi-project"></a>WebAPI-projekt

1. Öppna **AppBackend-projektet** som du skapade i självstudien **Meddela användare** i Visual Studio.
2. I Notifications.cs ersätter du hela klassen **Notifications** med följande kod. Var noga med att ersätta platshållarna med anslutningssträngen (med fullständig åtkomst) för meddelandehubben och hubbnamnet. Du kan hämta dessa värden från [Azure-portalen](https://portal.azure.com). Den här modulen representerar nu de olika säkra meddelanden som ska skickas. I ett fullständigt genomförande kommer anmälningarna att lagras i en databas. för enkelhetens skull, i det här fallet lagrar vi dem i minnet.
   
   ```csharp
    public class Notification
    {
        public int Id { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",     "{hub name}");
        }

        public Notification CreateNotification(string payload)
        {
            var notification = new Notification() {
            Id = notifications.Count,
            Payload = payload,
            Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Notification ReadNotification(int id)
        {
            return notifications.ElementAt(id);
        }
    }
    ```

1. I NotificationsController.cs ersätter du koden i klassen **NotificationsController** med följande kod. Den här komponenten implementerar ett sätt för enheten att hämta meddelandet på ett säkert sätt och ger också ett sätt (i den här självstudien) att utlösa en säker push till dina enheter. Observera att när du skickar meddelandet till meddelandehubben skickar vi bara ett råmeddelande med id:t för meddelandet (och inget verkligt meddelande):
   
   ```csharp
    public NotificationsController()
    {
        Notifications.Instance.CreateNotification("This is a secure notification!");
    }

    // GET api/notifications/id
    public Notification Get(int id)
    {
        return Notifications.Instance.ReadNotification(id);
    }

    public async Task<HttpResponseMessage> Post()
    {
        var secureNotificationInTheBackend = Notifications.Instance.CreateNotification("Secure confirmation.");
        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // windows
        var rawNotificationToBeSent = new Microsoft.Azure.NotificationHubs.WindowsNotification(secureNotificationInTheBackend.Id.ToString(),
                        new Dictionary<string, string> {
                            {"X-WNS-Type", "wns/raw"}
                        });
        await Notifications.Instance.Hub.SendNotificationAsync(rawNotificationToBeSent, usernameTag);

        // apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync("{\"aps\": {\"content-available\": 1}, \"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}", usernameTag);

        // gcm
        await Notifications.Instance.Hub.SendGcmNativeNotificationAsync("{\"data\": {\"secureId\": \"" + secureNotificationInTheBackend.Id.ToString() + "\"}}", usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

Observera att `Post` metoden nu inte skickar ett popup-meddelande. Den skickar ett råmeddelande som bara innehåller meddelande-ID: n och inte något känsligt innehåll. Se också till att kommentera skicka åtgärden för de plattformar som du inte har autentiseringsuppgifter konfigurerade på din anmälan nav, eftersom de kommer att resultera i fel.

1. Nu kommer vi att distribuera om den här appen till en Azure-webbplats för att göra den tillgänglig från alla enheter. Högerklicka på **AppBackend**-projektet och välj **Publicera**.
2. Välj Azure-webbplats som publiceringsmål. Logga in med ditt Azure-konto och välj en befintlig eller ny webbplats och anteckna egenskapen **mål-URL** på fliken **Anslutning.** Vi kommer att referera till den här webbadressen som din *backend-slutpunkt* senare i den här självstudien. Klicka på **Publicera**.
