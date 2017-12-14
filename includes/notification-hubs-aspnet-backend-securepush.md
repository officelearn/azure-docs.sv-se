## <a name="webapi-project"></a>WebAPI-projekt
1. Öppna i Visual Studio den **AppBackend** projekt som du skapade i den **meddela användare** kursen.
2. Ersätt hela i Notifications.cs, **meddelanden** klassen med följande kod. Se till att ersätta platshållarna med anslutningssträngen (med fullständig åtkomst) för meddelandehubben och hubbnamnet. Du kan hämta dessa värden från den [Azure-portalen](http://portal.azure.com). Den här modulen representerar nu olika säkra meddelanden som skickas. En fullständig implementering ska meddelanden sparas i en databas. för enkelhetens skull lagrar i det här fallet vi dem i minnet.
   
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

1. I NotificationsController.cs, Ersätt Koden i den **NotificationsController** klassen med följande kod. Den här komponenten implementerar ett sätt för enheten för att hämta meddelandet på ett säkert sätt och ger också ett sätt (för den här självstudiekursen) för att utlösa en säker push till dina enheter. Observera att när du skickar meddelandet till meddelandehubben vi bara skicka en rå med ID meddelandet (och inga faktiska meddelanden):
   
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


Observera att den `Post` metoden nu skickar ett popup-meddelande. Skickar den ett raw meddelande som innehåller endast meddelande-ID och inte något känsligt innehåll. Kontrollera också att kommentera send-åtgärden för plattformar som du inte har autentiseringsuppgifter som har konfigurerats på din meddelandehubb som de orsakar fel.

1. Vi ska nu omdistribuera den här appen till en Azure-webbplats för att göra den tillgänglig från alla enheter. Högerklicka på **AppBackend**-projektet och välj **Publicera**.
2. Välj Azure-webbplatsen som publicera-mål. Logga in med ditt Azure-konto och markera en befintlig eller ny webbplats och anteckna den **Måladress** egenskap i den **anslutning** fliken. Vi ska referera till den här URL:en som *serverdelens slutpunkt* senare i den här självstudiekursen. Klicka på **Publicera**.

