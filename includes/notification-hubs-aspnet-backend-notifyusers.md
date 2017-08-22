## <a name="create-the-webapi-project"></a>Skapa ett WebAPI-projekt
I avsnitten som följer ska vi skapa en ny ASP.NET WebAPI-serverdel som kommer att ha tre huvudsyften:

1. **Klientautentisering**: Senare ska vi lägga till en meddelandehanterare för att autentisera klientbegäranden och associera användaren med begäran.
2. **Registreringar för klientmeddelanden**: Därefter ska vi lägga till en kontrollant som hanterar nya registreringar så att en klientenhet kan ta emot meddelanden. Det autentiserade användarnamnet läggs automatiskt till i registreringen som en [tagg](https://msdn.microsoft.com/library/azure/dn530749.aspx).
3. **Skicka meddelanden till klienter**: Vi ska även lägga till en kontrollant som gör att en användare kan utlösa en säker push-överföring till enheter och klienter som är associerade med taggen. 

Följande steg beskriver hur du skapar den nya ASP.NET-WebAPI-serverdelen: 

> [!IMPORTANT]
> Om du använder Visual Studio 2015 eller tidigare ska du kontrollera att du har installerat den senaste versionen av NuGet Package Manager innan du påbörjar den här självstudiekursen. Börja med att starta Visual Studio. Klicka på **Extensions and Updates** (Tillägg och uppdateringar) på **Verktyg**-menyn. Sök efter **NuGet Package Manager** för din version av Visual Studio och kontrollera att du har den senaste versionen. Om inte avinstallerar du och installerar om NuGet Package Manager.
> 
> ![][B4]
> 
> [!NOTE]
> Kontrollera att du har installerat [Azure SDK](https://azure.microsoft.com/downloads/) för Visual Studio för webbplatsdistribution.
> 
> 

1. Starta Visual Studio eller Visual Studio Express. Klicka på **Server Explorer** och logga in på ditt Azure-konto. Du måste vara inloggad för att Visual Studio ska kunna skapa webbplatsresurserna på ditt konto.
2. Klicka på **Arkiv** i Visual Studio, klicka på **Nytt** och sedan på **Projekt**. Expandera **Mallar**, **Visual C#** och klicka sedan på **Webb** och **ASP.NET-webbprogram**. Skriv namnet **AppBackend** och klicka sedan på **OK**. 
   
    ![][B1]
3. Klicka på **Webb-API** i dialogrutan **Nytt ASP.NET-projekt** och klicka sedan på **OK**.
   
    ![][B2]
4. I dialogrutan **Configure Microsoft Azure Web App** (Konfigurera Microsoft Azure-webbapp) väljer du en prenumeration och en **App Service-plan** som du redan har skapat. Du kan också välja **Create a new app service plan** (Skapa en ny apptjänstplan) och skapa en från dialogrutan. Du behöver ingen databas för den här självstudiekursen. När du har valt apptjänstplan klickar du på **OK** för att skapa projektet.
   
    ![][B5]

## <a name="authenticating-clients-to-the-webapi-backend"></a>Autentisera klienter mot WebAPI-serverdelen
I det här avsnittet ska du skapa en ny meddelandehanterarklass med namnet **AuthenticationTestHandler** för den nya serverdelen. Den här klassen härleds från [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) och läggs till som en meddelandehanterare så att den kan bearbeta alla begäranden som kommer till serverdelen. 

1. Högerklicka på **AppBackend**-projektet i Solution Explorer, klicka på **Lägg till** och sedan på **Klass**. Ge den nya klassen namnet **AuthenticationTestHandler.cs** och klicka på **Lägg till**  för att generera klassen. För enkelhetens skull ska den här klassen användas för att autentisera användare med *grundläggande autentisering*. Observera att din app kan använda alla autentiseringsscheman.
2. Lägg till följande `using`-instruktioner i AuthenticationTestHandler.cs:
   
        using System.Net.Http;
        using System.Threading;
        using System.Security.Principal;
        using System.Net;
        using System.Text;
        using System.Threading.Tasks;

3. Ersätt `AuthenticationTestHandler`-klassdefinitionen i AuthenticationTestHandler.cs med följande kod. 
   
    Den här hanteraren auktoriserar begäran om följande tre villkor är uppfyllda:
   
   * Begäran innehåller ett *auktoriseringsrubrik*. 
   * Begäran använder *grundläggande* autentisering. 
   * Strängen för användarnamn och lösenordssträngen är samma sträng.
     
     Annars avvisas begäran. Det här är inte en riktig autentiserings- och auktoriseringsmetod. Det är bara ett väldigt enkelt exempel för den här självstudiekursen.
     
     Om meddelandet med begäran autentiseras och auktoriseras av `AuthenticationTestHandler` kopplas användaren med den grundläggande autentiseringen till den aktuella begäran i [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx). Senare används användarinformationen i HttpContext av en annan kontrollant (RegisterController) för att lägga till en [tagg](https://msdn.microsoft.com/library/azure/dn530749.aspx) till meddelanderegistreringsbegäran.
     
       public class AuthenticationTestHandler : DelegatingHandler   {       protected override Task<HttpResponseMessage> SendAsync(       HttpRequestMessage request, CancellationToken cancellationToken)       {           var authorizationHeader = request.Headers.GetValues("Authorization").First();
     
               if (authorizationHeader != null && authorizationHeader
                   .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
               {
                   string authorizationUserAndPwdBase64 =
                       authorizationHeader.Substring("Basic ".Length);
                   string authorizationUserAndPwd = Encoding.Default
                       .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                   string user = authorizationUserAndPwd.Split(':')[0];
                   string password = authorizationUserAndPwd.Split(':')[1];
     
                   if (verifyUserAndPwd(user, password))
                   {
                       // Attach the new principal object to the current HttpContext object
                       HttpContext.Current.User =
                           new GenericPrincipal(new GenericIdentity(user), new string[0]);
                       System.Threading.Thread.CurrentPrincipal =
                           System.Web.HttpContext.Current.User;
                   }
                   else return Unauthorized();
               }
               else return Unauthorized();
     
               return base.SendAsync(request, cancellationToken);
           }
     
           private bool verifyUserAndPwd(string user, string password)
           {
               // This is not a real authentication scheme.
               return user == password;
           }
     
           private Task<HttpResponseMessage> Unauthorized()
           {
               var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
               var tsc = new TaskCompletionSource<HttpResponseMessage>();
               tsc.SetResult(response);
               return tsc.Task;
           }
       }
     
     > [!NOTE]
     > **Säkerhetsmeddelande**: Klassen `AuthenticationTestHandler` tillhandahåller inte riktig autentisering. Den används endast för att efterlikna grundläggande autentisering och är inte säker. Du måste implementera en säker autentiseringsmekanism i dina tjänster och program i produktionsmiljön.                
     > 
     > 
4. Registrera meddelandehanteraren genom att lägga till följande kod i slutet av `Register`-metoden i klassen **App_Start/WebApiConfig.cs**:
   
        config.MessageHandlers.Add(new AuthenticationTestHandler());
5. Spara ändringarna.

## <a name="registering-for-notifications-using-the-webapi-backend"></a>Registrering för meddelanden med hjälp av WebAPI-serverdelen
I det här avsnittet ska vi lägga till en ny kontrollant till WebAPI-serverdelen som ska hantera begäranden för att registrera en användare och enhet för meddelanden med hjälp av klientbiblioteket för meddelandehubbar. Kontrollanten lägger till en användartagg för den användare som autentiserades och kopplades till HttpContext av `AuthenticationTestHandler`. Taggen har strängformatet `"username:<actual username>"`.

1. Högerklicka på **AppBackend**-projektet i Solution Explorer och klicka sedan på **Hantera NuGet-paket**.
2. Klicka på **Online** till vänster och sök efter **Microsoft.Azure.NotificationHubs** i rutan **Sök**.
3. Klicka på **Microsoft Azure Notification Hubs** i resultatlistan och sedan på **Installera**. Slutför installationen och stäng fönstret för NuGet-pakethanteraren.
   
    Då läggs en referens till i Azure Notification Hubs SDK med hjälp av <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs-NuGet-paketet</a>.
4. Nu ska vi skapa en ny klassfil som representerar anslutningen med meddelandehubben som används för att skicka meddelanden. Högerklicka på mappen **Modeller**, klicka på **Lägg till** och klicka sedan på **Klass**. Ge den nya klassen namnet **Notifications.cs** och generera sedan klassen genom att klicka på **Lägg till**. 
   
    ![][B6]
5. Lägg till följande `using`-instruktion överst i Notifications.cs-filen:
   
        using Microsoft.Azure.NotificationHubs;
6. Ersätt `Notifications`-klassdefinitionen med följande och glöm inte att ersätta de två platshållarna med anslutningssträngen (med fullständig åtkomst) för din meddelandehubb samt hubbnamnet (tillgängligt på [den klassiska Azure-portalen](http://manage.windowsazure.com)):
   
        public class Notifications
        {
            public static Notifications Instance = new Notifications();
   
            public NotificationHubClient Hub { get; set; }
   
            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>", 
                                                                             "<hub name>");
            }
        }
7. Nu ska vi skapa en ny kontrollant med namnet **RegisterController**. Högerklicka på mappen **Kontrollanter** i Solution Explorer, klicka på **Lägg till** och sedan på **Kontrollant**. Klicka på objektet **Web API 2 Controller -- Empty** (Web API 2-kontrollant – tom) och klicka sedan på **Lägg till**. Ge den nya klassen namnet **RegisterController** och klicka sedan på **Lägg till** igen för att generera kontrollanten.
   
    ![][B7]
   
    ![][B8]
8. Lägg till följande `using`-instruktioner i RegisterController.cs:
   
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.NotificationHubs.Messaging;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;
9. Lägg till följande kod i `RegisterController`-klassdefinitionen. Observera att vi i den här koden lägger till en användartagg för den användare som är kopplad till HttpContext. Användaren autentiserades och kopplades till HttpContext med hjälp av meddelandefiltret som vi lade till, `AuthenticationTestHandler`. Du kan också lägga till valfria kontroller som kontrollerar att användaren har behörighet att registrera sig för de begärda taggarna.
   
        private NotificationHubClient hub;
   
        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }
   
        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }
   
        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            string newRegistrationId = null;
   
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);
   
                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }
   
            if (newRegistrationId == null) 
                newRegistrationId = await hub.CreateRegistrationIdAsync();
   
            return newRegistrationId;
        }
   
        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }
   
            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;
   
            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);
   
            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }
10. Spara ändringarna.

## <a name="sending-notifications-from-the-webapi-backend"></a>Skicka meddelanden från WebAPI-serverdelen
I det här avsnittet ska du lägga till en ny kontrollant som gör att klientenheter kan skicka ett meddelande baserat på username-taggen med hjälp av Azure Notification Hubs Service Management Library i ASP.NET WebAPI-serverdelen.

1. Skapa en till kontrollant med namnet **NotificationsController**. Skapa den på samma sätt som du skapade **RegisterController** i det föregående avsnittet.
2. Lägg till följande `using`-instruktioner i NotificationsController.cs:
   
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;
3. Lägg till följande metod i **NotificationsController**-klassen.
   
    Den här koden skickar en meddelandetyp baserat på PNS-parametern (Platform Notification Service) `pns`. Värdet för `to_tag` används för att ställa in *usernamne*-taggen för meddelandet. Den här taggen måste matcha en username-tagg för en aktiv meddelandehubbsregistrering. Meddelandet hämtas från innehållsdelen i POST-begäran och formateras för PNS-måltjänsten. 
   
    Beroende på vilken PNS (Platform Notification Service) som dina kompatibla enheter använder för att ta emot meddelanden, stöds olika meddelanden i olika format. På Windows-enheter kan du till exempel använda ett [popup-meddelande med WNS](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) som inte stöds direkt av en annan PNS. Serverdelen måste alltså formatera meddelandet till ett meddelande som stöds för PNS-tjänsten för enheter som du vill ha stöd för. Därefter använder du lämpligt överförings-API i [NotificationHubClient-klassen](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx)
   
        public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
        {
            var user = HttpContext.Current.User.Identity.Name;
            string[] userTag = new string[2];
            userTag[0] = "username:" + to_tag;
            userTag[1] = "from:" + user;
   
            Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
            HttpStatusCode ret = HttpStatusCode.InternalServerError;
   
            switch (pns.ToLower())
            {
                case "wns":
                    // Windows 8.1 / Windows Phone 8.1
                    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                                "From " + user + ": " + message + "</text></binding></visual></toast>";
                    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                    break;
                case "apns":
                    // iOS
                    var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                    outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                    break;
                case "gcm":
                    // Android
                    var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                    outcome = await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);
                    break;
            }
   
            if (outcome != null)
            {
                if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                    (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
                {
                    ret = HttpStatusCode.OK;
                }
            }
   
            return Request.CreateResponse(ret);
        }
4. Tryck på **F5** för att köra programmet och för att kontrollera att allt fungerar som det ska hittills. Appen ska starta en webbläsare och visa ASP.NET-startsidan. 

## <a name="publish-the-new-webapi-backend"></a>Publicera den nya WebAPI-serverdelen
1. Nu ska vi distribuera den här appen till en Azure-webbplats så att den kan nås från alla enheter. Högerklicka på **AppBackend**-projektet och välj **Publicera**.
2. Välj **Microsoft Azure App Service** som publiceringsmål och klicka på **Publicera**. Dialogrutan Create App Service (Skapa apptjänst) öppnas, där du kan skapa alla Azure-resurser som du behöver för att köra ASP.NET-webbappen i Azure.

    ![][B15]
3. Välj ditt Azure-konto i dialogrutan **Create App Service** (Skapa apptjänst). Klicka på **Ändra typ** och välj **Webbapp**. Behåll **namnet på webbappen** som visas och välj **prenumeration**, **resursgrupp** och **apptjänstplan**.  Klicka på **Skapa**.

4. Notera egenskapen **Plats-URL** i avsnittet **Sammanfattning**. Vi ska referera till den här URL:en som *serverdelens slutpunkt* senare i den här självstudiekursen. Klicka på **Publicera**.

5. När guiden slutförs publiceras ASP.NET-webbappen till Azure och sedan öppnas appen i standardwebbläsaren.  Programmet kan visas i Azure App Service.

URL:en använder webbappens namn som du angav tidigare, i formatet http://<appnamn>.azurewebsites.net.

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
