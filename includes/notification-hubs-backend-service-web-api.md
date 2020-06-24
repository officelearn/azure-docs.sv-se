---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: f0e83548d3ba3b353b471e2e3429a23421aec7b2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081544"
---
### <a name="create-a-web-project"></a>Skapa ett webb projekt

1. I **Visual Studio**väljer du **fil**  >  **ny lösning**.

1. Välj **.net Core**  >  **app**  >  **ASP.net Core**  >  **API**  >  **härnäst**.
  
1. I dialog rutan **Konfigurera din nya ASP.net Core webb-API väljer du** **mål ramverk** för **.net Core 3,1**.

1. Ange *PushDemoApi* som **projekt namn** och välj sedan **skapa**.

1. Starta fel sökning (**kommando**  +  **RETUR**) för att testa den mallbaserade appen.

    > [!NOTE]
    > Den mallbaserade appen är konfigurerad att använda **WeatherForecastController** som *launchUrl*. Detta anges i **Egenskaper**  >  **launchSettings.jspå**.  
    >
    > Om du får ett meddelande om att ett **ogiltigt utvecklings certifikat hittades** :
    >
    > 1. Klicka på **Ja** om du vill köra verktyget ' dotNet dev-certifikats-https ' för att åtgärda detta. Med verktyget ' dotNet dev-certificates https ' uppmanas du att ange ett lösen ord för certifikatet och lösen ordet för din nyckel Ring.
    >
    > 1. Klicka på **Ja** när du uppmanas att **Installera och lita på det nya certifikatet**och ange sedan lösen ordet för din nyckel Ring.

1. Expandera mappen **controllers** och ta sedan bort **WeatherForecastController.cs**.

1. Ta bort **WeatherForecast.cs**.

1. **Kontroll**  +  **Klicka** på **PushDemoApi** -projektet och välj sedan **ny fil...** från menyn **Lägg till** .

1. Konfigurera lokala konfigurations värden med [verktyget Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=linux#secret-manager). Genom att koppla från hemligheterna från lösningen ser du till att de inte slutar i käll kontrollen. Öppna **terminalen** och gå sedan till katalogen för projekt filen och kör följande kommandon:

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set "NotificationHub:Name" <value>
    dotnet user-secrets set "NotificationHub:ConnectionString" <value>
    ```

    Ersätt plats hållarna med dina egna namn och anslutnings sträng värden för Notification Hub. Du har gjort en anteckning om dem i avsnittet [skapa en Notification Hub](#create-a-notification-hub) . Annars kan du se dem i [Azure](https://portal.azure.com).

    **NotificationsHub: namn**:  
    Se *namn* **i sammanfattnings sammanfattningen** längst upp i **översikten**.  

    **NotificationHub: ConnectionString**:  
    Se *DefaultFullSharedAccessSignature* i **åtkomst principer**

    > [!NOTE]
    > I produktions scenarier kan du titta på alternativ som Azure- [valv](https://azure.microsoft.com/services/key-vault) för att lagra anslutnings strängen på ett säkert sätt. För enkelhetens skull kommer hemligheterna att läggas till i program inställningarna för [Azure App Service](https://azure.microsoft.com/services/app-service/) .

### <a name="authenticate-clients-using-an-api-key-optional"></a>Autentisera klienter med en API-nyckel (valfritt)

API-nycklar är inte lika säkra som tokens, men är tillräckliga för de här självstudierna. En API-nyckel kan konfigureras enkelt via [ASP.net mellanprogram](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1).

1. Lägg till **API-nyckeln** i de lokala konfigurations värdena.

    ```bash
    dotnet user-secrets set "Authentication:ApiKey" <value>
    ```

    > [!NOTE]
    > Du bör ersätta plats hållarnas värde med ditt eget och anteckna det.

1. **Kontroll**  +  **Klicka** på **PushDemoApi** -projektet, Välj **ny mapp** på menyn **Lägg till** och klicka sedan på **Lägg till** med *autentisering* som **mappnamn**.

1. **Kontroll**  +  **Klicka** på mappen **autentisering** och välj sedan **ny fil...** från menyn **Lägg till** .

1. Välj **allmän**  >  **Tom klass**, ange *ApiKeyAuthOptions.cs* som **namn**och klicka sedan på **ny** Lägg till följande implementering.

    ```csharp
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthOptions : AuthenticationSchemeOptions
        {
            public const string DefaultScheme = "ApiKey";
            public string Scheme => DefaultScheme;
            public string ApiKey { get; set; }
        }
    }
    ```

1. Lägg till en annan **Tom klass** i **autentiseringscookien** med namnet *ApiKeyAuthHandler.cs*och Lägg sedan till följande implementering.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Security.Claims;
    using System.Text.Encodings.Web;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthHandler : AuthenticationHandler<ApiKeyAuthOptions>
        {
            const string ApiKeyIdentifier = "apikey";

            public ApiKeyAuthHandler(
                IOptionsMonitor<ApiKeyAuthOptions> options,
                ILoggerFactory logger,
                UrlEncoder encoder,
                ISystemClock clock)
                : base(options, logger, encoder, clock) {}

            protected override Task<AuthenticateResult> HandleAuthenticateAsync()
            {
                string key = string.Empty;

                if (Request.Headers[ApiKeyIdentifier].Any())
                {
                    key = Request.Headers[ApiKeyIdentifier].FirstOrDefault();
                }
                else if (Request.Query.ContainsKey(ApiKeyIdentifier))
                {
                    if (Request.Query.TryGetValue(ApiKeyIdentifier, out var queryKey))
                        key = queryKey;
                }

                if (string.IsNullOrWhiteSpace(key))
                    return Task.FromResult(AuthenticateResult.Fail("No api key provided"));

                if (!string.Equals(key, Options.ApiKey, StringComparison.Ordinal))
                    return Task.FromResult(AuthenticateResult.Fail("Invalid api key."));

                var identities = new List<ClaimsIdentity> {
                    new ClaimsIdentity("ApiKeyIdentity")
                };

                var ticket = new AuthenticationTicket(
                    new ClaimsPrincipal(identities), Options.Scheme);

                return Task.FromResult(AuthenticateResult.Success(ticket));
            }
        }
    }
    ```

    > [!NOTE]
    > En [autentiseringsnyckel](https://docs.microsoft.com/aspnet/core/security/authentication/?view=aspnetcore-3.1#authentication-handler) är en typ som implementerar ett schemas beteende, i det här fallet ett anpassat API-nyckelpar.

1. Lägg till en annan **Tom klass** i **autentiseringscookien** med namnet *ApiKeyAuthenticationBuilderExtensions.cs*och Lägg sedan till följande implementering.

    ```csharp
    using System;
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public static class AuthenticationBuilderExtensions
        {
            public static AuthenticationBuilder AddApiKeyAuth(
                this AuthenticationBuilder builder,
                Action<ApiKeyAuthOptions> configureOptions)
            {
                return builder
                    .AddScheme<ApiKeyAuthOptions, ApiKeyAuthHandler>(
                        ApiKeyAuthOptions.DefaultScheme,
                        configureOptions);
            }
        }
    }
    ```

    > [!NOTE]
    > Med den här tilläggs metoden kan du förenkla konfigurations koden för mellanprogram i **startup.cs** så att den blir lättare att läsa och enklare att följa.

1. I **startup.cs**uppdaterar du **ConfigureServices** -metoden för att konfigurera API-nyckel-autentiseringen under anropet till **tjänsterna. AddControllers** -metod.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = ApiKeyAuthOptions.DefaultScheme;
            options.DefaultChallengeScheme = ApiKeyAuthOptions.DefaultScheme;
        }).AddApiKeyAuth(Configuration.GetSection("Authentication").Bind);
    }
    ```

1. Uppdatera **startup.cs** **-metoden för att anropa** metoderna **UseAuthentication** och **UseAuthorization** på appens **IApplicationBuilder**. Se till att dessa metoder anropas efter **UseRouting** och innan **appen. UseEndpoints**.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseHttpsRedirection();

        app.UseRouting();

        app.UseAuthentication();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
    ```

    > [!NOTE]
    > När du anropar **UseAuthentication** registreras mellanprogram som använder tidigare registrerade autentiseringsscheman (från **ConfigureServices**). Detta måste anropas före alla mellanprogram som är beroende av användare som autentiseras.

### <a name="add-dependencies-and-configure-services"></a>Lägg till beroenden och konfigurera tjänster

ASP.NET Core stöder design mönstret för program [beroende insprutning (di)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1) , vilket är en teknik för att uppnå [inversion av kontrollen (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mellan klasser och deras beroenden.  

Att använda Notification Hub och [Notification HUBS SDK för Server dels åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) är inkapslat i en tjänst. Tjänsten är registrerad och görs tillgänglig via en lämplig abstraktion.

1. **Kontroll**  +  **Klicka** på mappen **beroenden** och välj sedan **Hantera NuGet-paket...**.

1. Sök efter **Microsoft. Azure. NotificationHubs** och kontrol lera att det är markerat.

1. Klicka på **Lägg till paket**och klicka sedan på **acceptera** när du uppmanas att godkänna licens villkoren.

1. **Kontroll**  +  **Klicka** på **PushDemoApi** -projektet, Välj **ny mapp** på menyn **Lägg till** och klicka sedan på **Lägg till** med *modeller* som **mappnamn**.

1. **Kontroll**  +  **Klicka** på mappen **modeller** och välj sedan **ny fil...** från menyn **Lägg till** .

1. Välj **allmän**  >  **Tom klass**, ange *PushTemplates.cs* som **namn**och klicka sedan på **ny** Lägg till följande implementering.

    ```csharp
    namespace PushDemoApi.Models
    {
        public class PushTemplates
        {
            public class Generic
            {
                public const string Android = "{ \"notification\": { \"title\" : \"PushDemo\", \"body\" : \"$(alertMessage)\"}, \"data\" : { \"action\" : \"$(alertAction)\" } }";
                public const string iOS = "{ \"aps\" : {\"alert\" : \"$(alertMessage)\"}, \"action\" : \"$(alertAction)\" }";
            }

            public class Silent
            {
                public const string Android = "{ \"data\" : {\"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\"} }";
                public const string iOS = "{ \"aps\" : {\"content-available\" : 1, \"apns-priority\": 5, \"sound\" : \"\", \"badge\" : 0}, \"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\" }";
            }
        }
    }
    ```

    > [!NOTE]
    > Den här klassen innehåller de token meddelande nytto lasterna för allmänna och tysta meddelanden som krävs i det här scenariot. Nytto lasterna definieras utanför- [installationen](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation?view=azure-dotnet) för att tillåta experimentering utan att behöva uppdatera befintliga installationer via tjänsten. Hantering av ändringar av installationer på det här sättet ligger utanför omfånget för den här självstudien. För produktion bör du överväga [anpassade mallar](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

1. Välj **allmän**  >  **Tom klass**, ange *DeviceInstallation.cs* som **namn**och klicka sedan på **ny** Lägg till följande implementering.

    ```csharp
    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class DeviceInstallation
        {
            [Required]
            public string InstallationId { get; set; }

            [Required]
            public string Platform { get; set; }

            [Required]
            public string PushChannel { get; set; }

            public IList<string> Tags { get; set; } = Array.Empty<string>();
        }
    }
    ```

1. Lägg till en annan **Tom klass** i mappen **modeller** med namnet *NotificationRequest.cs*och Lägg sedan till följande implementering.

    ```csharp
    using System;

    namespace PushDemoApi.Models
    {
        public class NotificationRequest
        {
            public string Text { get; set; }
            public string Action { get; set; }
            public string[] Tags { get; set; } = Array.Empty<string>();
            public bool Silent { get; set; }
        }
    }
    ```

1. Lägg till en annan **Tom klass** i mappen **modeller** med namnet *NotificationHubOptions.cs*och Lägg sedan till följande implementering.

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class NotificationHubOptions
        {
            [Required]
            public string Name { get; set; }

            [Required]
            public string ConnectionString { get; set; }
        }
    }
    ```

1. Lägg till en ny mapp i **PushDemoApi** -projektet med namnet *tjänster*.

1. Lägg till ett **tomt gränssnitt** i mappen **tjänster** med namnet *INotificationService.cs*och Lägg sedan till följande implementering.

    ```csharp
    using System.Threading.Tasks;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public interface INotificationService
        {
            Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token);
            Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token);
            Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token);
        }
    }
    ```

1. Lägg till en **Tom klass** i mappen **tjänster** med namnet *NotificationHubsService.cs*och Lägg sedan till följande kod för att implementera **INotificationService** -gränssnittet:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public class NotificationHubService : INotificationService
        {
            readonly NotificationHubClient _hub;
            readonly Dictionary<string, NotificationPlatform> _installationPlatform;
            readonly ILogger<NotificationHubService> _logger;

            public NotificationHubService(IOptions<NotificationHubOptions> options, ILogger<NotificationHubService> logger)
            {
                _logger = logger;
                _hub = NotificationHubClient.CreateClientFromConnectionString(
                    options.Value.ConnectionString,
                    options.Value.Name);

                _installationPlatform = new Dictionary<string, NotificationPlatform>
                {
                    { nameof(NotificationPlatform.Apns).ToLower(), NotificationPlatform.Apns },
                    { nameof(NotificationPlatform.Fcm).ToLower(), NotificationPlatform.Fcm }
                };
            }

            public async Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(deviceInstallation?.InstallationId) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.Platform) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.PushChannel))
                    return false;

                var installation = new Installation()
                {
                    InstallationId = deviceInstallation.InstallationId,
                    PushChannel = deviceInstallation.PushChannel,
                    Tags = deviceInstallation.Tags
                };

                if (_installationPlatform.TryGetValue(deviceInstallation.Platform, out var platform))
                    installation.Platform = platform;
                else
                    return false;

                try
                {
                    await _hub.CreateOrUpdateInstallationAsync(installation, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(installationId))
                    return false;

                try
                {
                    await _hub.DeleteInstallationAsync(installationId, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token)
            {
                if ((notificationRequest.Silent &&
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
                    (!notificationRequest.Silent &&
                    (string.IsNullOrWhiteSpace(notificationRequest?.Text)) ||
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)))
                    return false;

                var androidPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.Android :
                    PushTemplates.Generic.Android;

                var iOSPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.iOS :
                    PushTemplates.Generic.iOS;

                var androidPayload = PrepareNotificationPayload(
                    androidPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                var iOSPayload = PrepareNotificationPayload(
                    iOSPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                try
                {
                    if (notificationRequest.Tags.Length == 0)
                    {
                        // This will broadcast to all users registered in the notification hub
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, token);
                    }
                    else if (notificationRequest.Tags.Length <= 20)
                    {
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, notificationRequest.Tags, token);
                    }
                    else
                    {
                        var notificationTasks = notificationRequest.Tags
                            .Select((value, index) => (value, index))
                            .GroupBy(g => g.index / 20, i => i.value)
                            .Select(tags => SendPlatformNotificationsAsync(androidPayload, iOSPayload, tags, token));

                        await Task.WhenAll(notificationTasks);
                    }

                    return true;
                }
                catch (Exception e)
                {
                    _logger.LogError(e, "Unexpected error sending notification");
                    return false;
                }
            }

            string PrepareNotificationPayload(string template, string text, string action) => template
                .Replace("$(alertMessage)", text, StringComparison.InvariantCulture)
                .Replace("$(alertAction)", action, StringComparison.InvariantCulture);

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, token)
                };

                return Task.WhenAll(sendTasks);
            }

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, IEnumerable<string> tags, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, tags, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, tags, token)
                };

                return Task.WhenAll(sendTasks);
            }
        }
    }
    ```

    > [!NOTE]
    > Etikett uttrycket som angetts för **SendTemplateNotificationAsync** är begränsat till 20 taggar. Den är begränsad till 6 för de flesta operatorer, men uttrycket innehåller bara ORs (| |) i det här fallet. Om det finns fler än 20 Taggar i begäran måste de delas upp i flera begär Anden. Mer information finns i dokumentationen om [Routning och tagg-uttryck](https://msdn.microsoft.com/library/azure/Dn530749.aspx?f=255&MSPPError=-2147217396) .

1. I **startup.cs**uppdaterar du **ConfigureServices** -metoden för att lägga till **NotificationHubsService** som en singleton-implementering av **INotificationService**.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ...

        services.AddSingleton<INotificationService, NotificationHubService>();

        services.AddOptions<NotificationHubOptions>()
            .Configure(Configuration.GetSection("NotificationHub").Bind)
            .ValidateDataAnnotations();
    }
    ```

### <a name="create-the-notifications-api"></a>Skapa API: er för meddelanden

1. **Kontroll**  +  **Klicka** på mappen **kontrollanter** och välj sedan **ny fil...** från menyn **Lägg till** .

1. Välj **ASP.net Core**  >  **Web API Controller-klassen**, ange *NotificationsController* som **namn**och klicka sedan på **ny**.

1. Lägg till följande namn rymder överst i filen.

    ```csharp
    using System.ComponentModel.DataAnnotations;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using PushDemoApi.Models;
    using PushDemoApi.Services;
    ```

1. Uppdatera den mallbaserade styrenheten så att den härleds från **ControllerBase** och dekoreras med **ApiController** -attributet.

    ```cs
    [ApiController]
    [Route("api/[controller]")]
    public class NotificationsController : ControllerBase
    {
        // Templated methods here
    }
    ```

    > [!NOTE]
    > **Controller** -Bask Lassen ger stöd för vyer, men det behövs inte i det här fallet och därför kan **ControllerBase** användas i stället.

1. Om du väljer att slutföra kommandot [autentisera klienter med en API-nyckel](#authenticate-clients-using-an-api-key-optional) bör du dekorera **NotificationsController** med attributet **auktorisera** .

    ```cs
    [Authorize]
    ```

1. Uppdatera konstruktorn för att acceptera den registrerade instansen av **INotificationService** som ett argument och tilldela den till en skrivskyddad medlem.

    ```cs
    readonly INotificationService _notificationService;

    public NotificationsController(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }
    ```

1. I **launchSettings.jspå** (i mappen **Egenskaper** ) ändrar du **launchUrl** från `weatherforecast` till *API/aviseringar* så att de matchar den URL som anges i attributet **RegistrationsController** **Route** .

1. Starta fel sökning (**kommando**  +  **RETUR**) om du vill verifiera att appen fungerar med den nya **NotificationsController** och returnerar en **401-oauktoriserad** status.

    > [!NOTE]
    > Visual Studio kan inte starta appen automatiskt i webbläsaren. Du kommer att använda [Postman](https://www.postman.com/downloads) för att testa API: et från den här punkten.

1. På fliken ny **[Postman](https://www.postman.com/downloads)** ställer du in begäran för att **Hämta** och ange adressen nedan.

    ```bash
    https://localhost:5001/api/notifications
    ```

    > [!NOTE]
    > Localhost-adressen ska matcha **applicationUrl** -värdet som påträffades i **egenskaperna**  >  **launchSettings.jspå**. Standardvärdet bör vara `https://localhost:5001;http://localhost:5000` något för att kontrol lera om du får ett 404-svar.

1. Om du väljer att slutföra kommandot [autentisera klienter med en API-nyckel](#authenticate-clients-using-an-api-key-optional) måste du konfigurera begärandehuvuden så att de inkluderar ditt **apiKey** -värde.

   | Tangent                            | Värde                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Klicka på knappen **Skicka** .

    > [!NOTE]
    > Du bör få en status på **200 OK** med ett **JSON** -innehåll.
    >
    > Om du får en **verifierings** varning för ett SSL-certifikat, kan du byta begäran om verifiering av SSL-certifikat **[Postman](https://www.postman.com/downloads)** i **inställningarna**.

1. Ersätt de mallbaserade klass metoderna med följande kod.

    ```csharp
    [HttpPut]
    [Route("installations")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> UpdateInstallation(
        [Required]DeviceInstallation deviceInstallation)
    {
        var success = await _notificationService
            .CreateOrUpdateInstallationAsync(deviceInstallation, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpDelete()]
    [Route("installations/{installationId}")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<ActionResult> DeleteInstallation(
        [Required][FromRoute]string installationId)
    {
        var success = await _notificationService
            .DeleteInstallationByIdAsync(installationId, CancellationToken.None);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpPost]
    [Route("requests")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> RequestPush(
        [Required]NotificationRequest notificationRequest)
    {
        if ((notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
            (!notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Text)))
            return new BadRequestResult();

        var success = await _notificationService
            .RequestNotificationAsync(notificationRequest, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }
    ```

### <a name="create-the-api-app"></a>Skapa API-appen

Nu skapar du en [API-app](https://azure.microsoft.com/services/app-service/api/) i [Azure App Service](https://docs.microsoft.com/azure/app-service/) för att vara värd för backend-tjänsten.  

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Klicka på **skapa en resurs**, Sök sedan efter och välj **API-app**och klicka sedan på **skapa**.

1. Uppdatera följande fält och klicka sedan på **skapa**.

    **App-namn:**  
    Ange ett globalt unikt namn för **API-appen**

    **Prenumerera**  
    Välj samma mål **prenumeration** som du skapade Notification Hub i.

    **Resurs grupp:**  
    Välj samma **resurs grupp** som du skapade Notification Hub i.

    **App Service plan/plats:**  
    Skapa en ny **App Service plan**  

    > [!NOTE]
    > Ändra från standard alternativet till en plan som innehåller **SSL** -stöd. Annars måste du vidta lämpliga åtgärder när du arbetar med mobilappen för att förhindra att **http-** begäranden blockeras.

    **Application Insights:**  
    Behåll det rekommenderade alternativet (en ny resurs kommer att skapas med det namnet) eller Välj en befintlig resurs.

1. När du har etablerat **API-appen** navigerar du till den resursen.

1. Anteckna **URL** -egenskapen **i sammanfattnings** sammanfattningen längst upp i **översikten**. Den här URL: en är din *backend-slutpunkt* som kommer att användas senare i den här självstudien.

    > [!NOTE]
    > URL: en använder API-appens namn som du angav tidigare, med formatet `https://<app_name>.azurewebsites.net` .

1. Välj **konfiguration** i listan (under **Inställningar**).  

1. För var och en av inställningarna nedan, klickar du på **ny program inställning** för att ange **namn** och **värde**och klickar sedan på **OK**.

   | Name                               | Värde                          |
   | ---------------------------------- | ------------------------------ |
   | `Authentication:ApiKey`            | <api_key_value>                |
   | `NotificationHub:Name`             | <hub_name_value>               |
   | `NotificationHub:ConnectionString` | <hub_connection_string_value>  |

   > [!NOTE]
   > Detta är samma inställningar som du definierade tidigare i användar inställningarna. Du bör kunna kopiera dessa. Inställningen **autentisering: ApiKey** krävs bara om du väljer att slutföra kommandot [autentisera klienter med en API-nyckel](#authenticate-clients-using-an-api-key-optional) . För produktions scenarier kan du titta på alternativ som Azure- [valv](https://azure.microsoft.com/services/key-vault). Dessa har lagts till som program inställningar för enkelhetens skull.

1. När alla program inställningar har lagts till klickar du på **Spara**och sedan på **Fortsätt**.

### <a name="publish-the-backend-service"></a>Publicera backend-tjänsten

Sedan distribuerar du appen till API-appen för att göra den tillgänglig från alla enheter.  

1. Ändra konfigurationen från **fel sökning** till **version** om du inte redan gjort det.

1. **Kontroll**  +  **Klicka på** projektet **PushDemoApi** och välj sedan **Publicera på Azure...** från menyn **publicera** .

1. Följ auth-flödet om du uppmanas att göra det. Använd det konto som du använde i föregående avsnitt [om att skapa API-appen](#create-the-api-app) .

1. Välj den **Azure App Service-API-app** som du skapade tidigare från listan som publicerings mål och klicka sedan på **publicera**.

När du har slutfört guiden publicerar den appen till Azure och öppnar sedan appen. Anteckna **URL:** en om du inte redan har gjort det. Den här URL: en är din *backend-slutpunkt* som används senare i den här självstudien.

### <a name="validating-the-published-api"></a>Verifierar det publicerade API: et

1. I **[Postman](https://www.postman.com/downloads)** öppnar du en ny flik, anger begäran att **publicera** och anger adressen nedan. Ersätt plats hållaren med bas adressen som du antecknade i föregående [publicera tjänsten Server](#publish-the-backend-service) del.

    ```csharp
    https://<app_name>.azurewebsites.net/api/notifications/installations
    ```

    > [!NOTE]
    > Bas adressen ska vara i formatet``https://<app_name>.azurewebsites.net/``

1. Om du väljer att slutföra kommandot [autentisera klienter med en API-nyckel](#authenticate-clients-using-an-api-key-optional) måste du konfigurera begärandehuvuden så att de inkluderar ditt **apiKey** -värde.

   | Tangent                            | Värde                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Välj alternativet **RAW** för **bröd texten**, välj sedan **JSON** i listan med format alternativ och ta med en plats hållare **JSON** -innehåll:

    ```json
    {}
    ```

1. Klicka på **Skicka**.

    > [!NOTE]
    > Du bör få en **400 felaktig** status för begäran från tjänsten.

1. Gör steg 1-4 igen, men den här gången anger slut punkten för begär Anden för att verifiera att du får samma 400-svar på begäran som är **felaktig** .

    ```bash
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

> [!NOTE]
> Det går inte att testa API: et med giltiga begär ande data eftersom detta kräver plattformsspecifik information från klientens mobilapp.
