---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: b8cf4217ca6c80be998b92e71c3ba29c4f68bce2
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52272374"
---
## <a name="webapi-project"></a>WebAPI-projekt
1. Visual Studio, öppna den **AppBackend** projektet som du skapade i den **meddela användare** självstudien.
2. Ersätt hela i Notifications.cs **meddelanden** klassen med följande kod. Var noga med att ersätta platshållarna med anslutningssträngen (med fullständig åtkomst) för din meddelandehubb samt hubbnamnet. Du kan hämta dessa värden från den [Azure-portalen](http://portal.azure.com). Den här modulen representerar nu olika säkra meddelanden som skickas. En fullständig implementering kommer meddelanden att sparas i en databas. för enkelhetens skull lagra i det här fallet vi dem i minnet.
   
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

1. Ersätt Koden i i NotificationsController.cs den **NotificationsController** klassdefinitionen med följande kod. Den här komponenten implementerar ett sätt för enheten hämtar meddelandet på ett säkert sätt och innehåller också ett sätt (för den här självstudien) för att utlösa en säker push till dina enheter. Observera att när du skickar meddelandet till notification hub, vi bara skicka ett raw-meddelande med ID meddelandet (och inga faktiska meddelanden):
   
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


Observera att den `Post` metoden tar nu inte skicka ett popup-meddelande. Skickar den ett raw-meddelande som innehåller endast meddelande-ID och inte alla känsligt innehåll. Kontrollera också att kommentera Sändningsåtgärden för de plattformar som du inte har autentiseringsuppgifter som konfigurerats på din meddelandehubb, eftersom de kommer att orsaka fel.

1. Nu ska vi nytt distribuera den här appen till en Azure-webbplats för att kunna göra det tillgängligt från alla enheter. Högerklicka på **AppBackend**-projektet och välj **Publicera**.
2. Välj Azure-webbplats som din publiceringsmål. Logga in med ditt Azure-konto och välj en befintlig eller ny webbplats och anteckna den **mål-URL** -egenskapen i den **anslutning** fliken. Vi ska referera till den här URL:en som *serverdelens slutpunkt* senare i den här självstudiekursen. Klicka på **Publicera**.

