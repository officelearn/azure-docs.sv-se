## <a name="create-the-webapi-project"></a>Skapa WebAPI-projekt
I nästa avsnitt beskrivs skapandet av en ny ASP.NET-WebAPI-serverdel. Den här processen har tre huvudsakliga syften:

* **Autentisera klienter**: du lägger till en meddelandehanteraren senare för att autentisera klientbegäranden och associera användaren med begäran.

* **Registrera för meddelanden med hjälp av WebAPI-serverdel**: du lägger till en domänkontrollant för att hantera nya registreringar för en klientenhet tar emot meddelanden. Autentiserade användarnamnet läggs automatiskt till registreringen som en [taggen](https://msdn.microsoft.com/library/azure/dn530749.aspx).

* **Skicka meddelanden till klienterna**: du också lägga till en domänkontrollant för att ge användare möjlighet att utlösa en säker push till enheter och klienter som är associerade med taggen. 

Skapa ny ASP.NET-WebAPI-serverdel genom att göra följande: 

> [!IMPORTANT]
> Om du använder Visual Studio 2015 eller tidigare, innan du påbörjar den här självstudiekursen, se till att har du installerat den senaste versionen av NuGet Package Manager för Visual Studio. 
>
>Börja med att starta Visual Studio. På den **verktyg** väljer du **tillägg och uppdateringar**. Sök efter **NuGet Package Manager** i din version av Visual Studio och kontrollera att du har den senaste versionen. Om din version inte är den senaste versionen, avinstallera den och sedan installera om NuGet Package Manager.
 
![][B4]

> [!NOTE]
> Kontrollera att du har installerat [Azure SDK](https://azure.microsoft.com/downloads/) för Visual Studio för webbplatsdistribution.
> 
> 

1. Starta Visual Studio eller Visual Studio Express. 

2. Välj **Server Explorer**, och logga in på ditt Azure-konto. För att skapa webbplatsresurser på ditt konto, måste du vara inloggad.

3. I Visual Studio väljer **filen** > **ny** > **projekt**, expandera **mallar**, expandera **Visual C#**, och välj sedan **Web** och **ASP.NET-webbprogram**.

4. I den **namn** skriver **AppBackend**, och välj sedan **OK**. 
   
    ![Fönstret nytt projekt][B1]

5. I den **nytt ASP.NET-projekt** väljer den **Web API** kryssrutan och välj sedan **OK**.
   
    ![Fönstret Nytt ASP.NET-projekt][B2]

6. I den **konfigurera Microsoft Azure Web App** väljer en prenumeration och sedan, i den **programtjänstplanen** gör något av följande:

    * Välj en app service-plan som du redan har skapat. 
    * Välj **skapar en ny app service-plan**, och sedan skapa en. 
    
  Du behöver ingen databas för den här självstudiekursen. När du har valt din apptjänstplan, Välj **OK** att skapa projektet.
   
    ![Konfigurera Microsoft Azure Web App-fönstret][B5]

## <a name="authenticate-clients-to-the-webapi-back-end"></a>Autentisera klienter till WebAPI-serverdel
I det här avsnittet skapar du en ny meddelandehanteraren klass som heter **AuthenticationTestHandler** för nya serverdelen. Den här klassen härleds från [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) och läggas till som en meddelandehanteraren så att den kan bearbeta alla begäranden som kommer till serverdelen. 

1. I Solution Explorer högerklickar du på den **AppBackend** projektet, Välj **Lägg till**, och välj sedan **klassen**. 
 
2. Kalla den nya klassen **AuthenticationTestHandler.cs**, och välj sedan **Lägg till** att skapa klassen. Den här klassen autentiserar användare med hjälp av *grundläggande autentisering* för enkelhetens skull. Din app kan använda alla autentiseringsschema.

3. Lägg till följande `using`-instruktioner i AuthenticationTestHandler.cs:
   
        using System.Net.Http;
        using System.Threading;
        using System.Security.Principal;
        using System.Net;
        using System.Text;
        using System.Threading.Tasks;

4. I AuthenticationTestHandler.cs, ersätter den `AuthenticationTestHandler` klassen med följande kod: 
   
    Hanteraren godkänner begäran om följande tre villkor är uppfyllda:
   
   * Begäran innehåller en *auktorisering* huvud. 
   * Begäran använder *grundläggande* autentisering. 
   * Strängen för användarnamn och lösenordssträngen är samma sträng.
     
  Annars avvisas begäran. Det här är inte en riktig autentiserings- och auktoriseringsmetod. Det är bara en väldigt enkelt exempel för den här självstudiekursen.
     
  Om meddelandet med begäran autentiseras och auktoriseras av `AuthenticationTestHandler`, grundläggande autentisering användaren är ansluten till den aktuella begäranden på [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx). Användarinformation i HttpContext kommer att användas av en annan domänkontrollant (RegisterController) senare lägga till en [taggen](https://msdn.microsoft.com/library/azure/dn530749.aspx) till meddelandebegäran för registrering.
     
       public class AuthenticationTestHandler : DelegatingHandler
       {
           protected override Task<HttpResponseMessage> SendAsync(
           HttpRequestMessage request, CancellationToken cancellationToken)
           {
               var authorizationHeader = request.Headers.GetValues("Authorization").First();
     
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
     > Säkerhetsmeddelande: den `AuthenticationTestHandler` klassen inte ange SANT autentisering. Den används endast för att efterlikna grundläggande autentisering och är inte säker. Du måste implementera en säker autentiseringsmekanism i dina tjänster och program i produktionsmiljön.                
     > 
     > 
5. För att registrera meddelande-hanterare, lägger du till följande kod i slutet av den `Register` metod i den **App_Start/WebApiConfig.cs** klass:
   
        config.MessageHandlers.Add(new AuthenticationTestHandler());

6. Spara ändringarna.

## <a name="register-for-notifications-by-using-the-webapi-back-end"></a>Registrera dig för meddelanden med hjälp av WebAPI-serverdel
I det här avsnittet, du lägger till en ny domänkontrollant WebAPI-serverdel att hantera begäranden om att registrera en användare och en enhet för meddelanden med hjälp av klientbiblioteket för meddelandehubbar. Kontrollanten lägger till en användartagg för den användare som har autentiserats och bifogas till HttpContext av `AuthenticationTestHandler`. Taggen har strängformatet `"username:<actual username>"`.

1. I Solution Explorer högerklickar du på den **AppBackend** projektet och välj sedan **hantera NuGet-paket**.

2. I den vänstra rutan, Välj **Online** och sedan, i den **Sök** skriver **Microsoft.Azure.NotificationHubs**.

3. Välj i resultatlistan **Microsoft Azure Notification Hub**, och välj sedan **installera**. Slutför installationen och stäng sedan NuGet Package Manager-fönstret.
   
    Den här åtgärden lägger till en referens i Azure Notification Hubs SDK med hjälp av <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs-NuGet-paketet</a>.

4. Skapa en ny klassfil som representerar anslutningen med den meddelandehubb som används för att skicka meddelanden. I Solution Explorer högerklickar du på den **modeller** mapp, Välj **Lägg till**, och välj sedan **klassen**. Kalla den nya klassen **Notifications.cs**, och välj sedan **Lägg till** att skapa klassen. 
   
    ![Fönstret Lägg till nytt objekt][B6]

5. Lägg till följande `using`-instruktion överst i Notifications.cs-filen:
   
        using Microsoft.Azure.NotificationHubs;

6. Ersätt den `Notifications` klassen med följande kod och Ersätt två platshållarna med anslutningssträngen (med fullständig åtkomst) för meddelandehubben och hubbnamnet (finns på [klassiska Azure-portalen](http://manage.windowsazure.com)):
   
        public class Notifications
        {
            public static Notifications Instance = new Notifications();
   
            public NotificationHubClient Hub { get; set; }
   
            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>", 
                                                                             "<hub name>");
            }
        }
7. Skapa sedan en ny domänkontrollant med namnet **RegisterController**. I Solution Explorer högerklickar du på den **domänkontrollanter** mapp, Välj **Lägg till**, och välj sedan **domänkontrollant**. 

8. Välj **Web API 2 styrenhet – tom**, och välj sedan **Lägg till**.
   
    ![Fönstret Lägg till Kodskelett][B7]
   
9. I den **Kontrollnamn** skriver **RegisterController** som namn på den nya klassen och välj sedan **Lägg till**.

    ![Fönstret Lägg till styrenhet][B8]

10. Lägg till följande `using`-instruktioner i RegisterController.cs:
   
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.NotificationHubs.Messaging;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

11. Lägg till följande kod i `RegisterController`-klassdefinitionen. Observera att i den här koden vi lägga till en användartaggen för den användare som är kopplad till HttpContext. Användaren har autentiserats och bifogas till HttpContext av meddelandefiltret som vi har lagt till `AuthenticationTestHandler`. Du kan också lägga till valfria kontroller som kontrollerar att användaren har behörighet att registrera sig för de begärda taggarna.
   
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
12. Spara ändringarna.

## <a name="send-notifications-from-the-webapi-back-end"></a>Skicka meddelanden från WebAPI-serverdel
Lägg till en ny domänkontrollant som Exponerar en metod för klientenheter att skicka ett meddelande i det här avsnittet. Meddelandet baseras på taggen användarnamn som använder Azure Notification Hubs Service Management Library i ASP.NET-WebAPI-serverdel.

1. Skapa en ny domänkontrollant med namnet **NotificationsController** på samma sätt som du skapade **RegisterController** i föregående avsnitt.

2. Lägg till följande `using`-instruktioner i NotificationsController.cs:
   
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

3. Lägg till följande metod för att den **NotificationsController** klass:
   
    Den här koden skickar en meddelandetyp som baseras på Platform Notification Service (PNS) `pns` parameter. Värdet för `to_tag` används för att ställa in *usernamne*-taggen för meddelandet. Den här taggen måste matcha en username-tagg för en aktiv meddelandehubbsregistrering. Meddelandet hämtas från innehållsdelen i POST-begäran och formateras för PNS-måltjänsten. 
   
    Beroende på pns-systemet som dina enheter som stöds använder för att ta emot meddelanden, stöds meddelanden i olika format. Till exempel på Windows-enheter, du kan använda en [popup-meddelanden med WNS](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) som direkt stöds inte av en annan PNS. I sådana fall måste din serverdel formateras meddelandet i ett meddelande som stöds för pns-systemet för enheter som du planerar att stödja. Och sedan använda en skicka API på den [NotificationHubClient klassen](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx).
   
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

4. Om du vill köra programmet och säkerställa ditt arbete hittills, Välj den **F5** nyckel. Öppnar en webbläsare för appen och den visas på startsidan för ASP.NET. 

## <a name="publish-the-new-webapi-back-end"></a>Publicera nya WebAPI-serverdel
Därefter måste distribuera du appen till en Azure-webbplats så att den är tillgänglig från alla enheter. 

1. Högerklicka på den **AppBackend** projektet och välj sedan **publicera**.

2. Välj **Microsoft Azure App Service** som publiceringsmål och välj sedan **publicera**.  
    Skapa App Service-fönstret öppnas. Här kan du skapa alla nödvändiga Azure-resurser för att köra ASP.NET-webbapp i Azure.

    ![Panelen Microsoft Azure App Service][B15]

3. I den **skapa App Service** fönster, Välj ditt Azure-konto. Välj **ändra typen** > **Webbapp**. Behåller du standardvärdet **Webbprogramnamnet**, och välj sedan den **prenumeration**, **resursgruppen**, och **Apptjänstplan**. 

4. Välj **Skapa**.

5. Notera egenskapen **Plats-URL** i avsnittet **Sammanfattning**. Den här URL: en är din *backend-slutpunkt* senare under kursen. 

6. Välj **publicera**.

När du har slutfört guiden publicerar ASP.NET-webbapp till Azure och sedan öppnar appen i standardwebbläsaren.  Programmet kan visas i Azure App Service.

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
