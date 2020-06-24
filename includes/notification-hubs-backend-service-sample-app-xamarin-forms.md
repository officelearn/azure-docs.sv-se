---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 40bc1c8c3d578e35b6689124f60f82d8ea85f0f2
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85111985"
---
### <a name="create-the-xamarinforms-solution"></a>Skapa lösningen Xamarin. Forms

1. I **Visual Studio**skapar du en ny **Xamarin. Forms** -lösning med hjälp av **tomma formulär-appar** som mall och anger *PushDemo* som **projekt namn**.

    > [!NOTE]
    > I dialog rutan **Konfigurera din tomma formulär app** ser du till att **organisations-ID: n** matchar det värde du använt tidigare och att både **Android** -och **iOS** -målen är markerade.

1. **Kontroll**  +  **Klicka** på *PushDemo* -lösningen och välj sedan **Uppdatera NuGet-paket**.
1. **Kontroll**  +  **Klicka** på *PushDemo* -lösningen och välj sedan **Hantera NuGet-paket..**.
1. Sök efter **Newtonsoft.Jspå** och kontrol lera att den är markerad.
1. Klicka på **Lägg till paket**och klicka sedan på **acceptera** när du uppmanas att godkänna licens villkoren.
1. Skapa och kör appen på varje mål plattform (**kommando**  +  **RETUR**) för att testa att den mall appen körs på dina enheter.

### <a name="implement-the-cross-platform-components"></a>Implementera plattforms oberoende komponenter

1. **Kontroll**  +  **Klicka** på **PushDemo** -projektet, Välj **ny mapp** på menyn **Lägg till** och klicka sedan på **Lägg till** med *modeller* som **mappnamn**.

1. **Kontroll**  +  **Klicka** på mappen **modeller** och välj sedan **ny fil...** från menyn **Lägg till** .

1. Välj **allmän**  >  **Tom klass**, ange *DeviceInstallation.cs*och Lägg sedan till följande implementering.

    ```csharp
    using System.Collections.Generic;
    using Newtonsoft.Json;

    namespace PushDemo.Models
    {
        public class DeviceInstallation
        {
            [JsonProperty("installationId")]
            public string InstallationId { get; set; }

            [JsonProperty("platform")]
            public string Platform { get; set; }

            [JsonProperty("pushChannel")]
            public string PushChannel { get; set; }

            [JsonProperty("tags")]
            public List<string> Tags { get; set; } = new List<string>();
        }
    }
    ```

1. Lägg till en **Tom uppräkning** till mappen **modeller** som heter *PushDemoAction.cs* med följande implementering.

    ```csharp
    namespace PushDemo.Models
    {
        public enum PushDemoAction
        {
            ActionA,
            ActionB
        }
    }
    ```

1. Lägg till en ny mapp i **PushDemo** -projektet med namnet *tjänster* och Lägg sedan till en **Tom klass** i mappen med namnet *ServiceContainer.cs* med följande implementering.

     ```csharp
    using System;
    using System.Collections.Generic;

    namespace PushDemo.Services
    {
        public static class ServiceContainer
        {
            static readonly Dictionary<Type, Lazy<object>> services
                = new Dictionary<Type, Lazy<object>>();

            public static void Register<T>(Func<T> function)
                => services[typeof(T)] = new Lazy<object>(() => function());

            public static T Resolve<T>()
                => (T)Resolve(typeof(T));

            public static object Resolve(Type type)
            {
                {
                    if (services.TryGetValue(type, out var service))
                        return service.Value;

                    throw new KeyNotFoundException($"Service not found for type '{type}'");
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Detta är en trimmad version av klassen [service container](https://github.com/xamcat/mobcat-library/blob/master/MobCAT/ServiceContainer.cs) från [XamCAT](https://github.com/xamcat/mobcat-library) -lagringsplatsen. Den kommer att användas som en IoC (inversion av kontroll).

1. Lägg till ett **tomt gränssnitt** i mappen **tjänster** med namnet *IDeviceInstallationService.cs*och Lägg sedan till följande kod.

    ```csharp
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IDeviceInstallationService
        {
            string Token { get; set; }
            bool NotificationsSupported { get; }
            string GetDeviceId();
            DeviceInstallation GetDeviceInstallation(params string[] tags);
        }
    }
    ```

    > [!NOTE]
    > Det här gränssnittet implementeras och startats av varje mål senare för att tillhandahålla de plattformsspecifika funktioner och **DeviceInstallation** information som krävs av backend-tjänsten.

1. Lägg till ett annat **tomt gränssnitt** till mappen **tjänster** med namnet *INotificationRegistrationService.cs*och Lägg sedan till följande kod.  

    ```csharp
    using System.Threading.Tasks;

    namespace PushDemo.Services
    {
        public interface INotificationRegistrationService
        {
            Task DeregisterDeviceAsync();
            Task RegisterDeviceAsync(params string[] tags);
            Task RefreshRegistrationAsync();
        }
    }
    ```

    > [!NOTE]
    > Detta hanterar interaktionen mellan klienten och backend-tjänsten.

1. Lägg till ett annat **tomt gränssnitt** till mappen **tjänster** med namnet *INotificationActionService.cs*och Lägg sedan till följande kod.  

    ```csharp
    namespace PushDemo.Services
    {
        public interface INotificationActionService
        {
            void TriggerAction(string action);
        }
    }
    ```

    > [!NOTE]
    > Detta används som en enkel mekanism för att centralisera hanteringen av meddelande åtgärder.

1. Lägg till ett **tomt gränssnitt** till mappen **tjänster** med namnet *IPushDemoNotificationActionService.cs* som härleds från *INotificationActionService*, med följande implementering.  

    ```csharp
    using System;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IPushDemoNotificationActionService : INotificationActionService
        {
            event EventHandler<PushDemoAction> ActionTriggered;
        }
    }
    ```

    > [!NOTE]
    > Den här typen är specifik för **PushDemo** -programmet och använder **PushDemoAction** -uppräkningen för att identifiera den åtgärd som aktive ras på ett starkt skrivet sätt.

1. Lägg till en **Tom klass** till mappen **tjänster** med namnet *NotificationRegistrationService.cs* implementera *INotificationRegistrationService* med följande kod.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using PushDemo.Models;
    using Xamarin.Essentials;

    namespace PushDemo.Services
    {
        public class NotificationRegistrationService : INotificationRegistrationService
        {
            const string CachedTagsKey = "cached_tags";
            const string RequestUrl = "api/notifications/installations";

            string _baseApiUrl;
            HttpClient _client;
            IDeviceInstallationService _deviceInstallationService;

            public NotificationRegistrationService(string baseApiUri, string apiKey)
            {
                _client = new HttpClient();
                _client.DefaultRequestHeaders.Add("Accept", "application/json");
                _client.DefaultRequestHeaders.Add("apikey", apiKey);

                _baseApiUrl = baseApiUri;
            }

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService = ServiceContainer.Resolve<IDeviceInstallationService>());

            public Task DeregisterDeviceAsync()
            {
                var deviceId = DeviceInstallationService?.GetDeviceId();

                if (string.IsNullOrWhiteSpace(deviceId))
                    throw new Exception("Unable to resolve an ID for the device.");

                SecureStorage.Remove(CachedTagsKey);

                return SendAsync(HttpMethod.Delete, $"{RequestUrl}/{deviceId}");
            }

            public async Task RegisterDeviceAsync(params string[] tags)
            {
                var deviceInstallation = DeviceInstallationService?.GetDeviceInstallation(tags);

                if (deviceInstallation == null)
                    throw new Exception($"Unable to get device installation information.");

                if (string.IsNullOrWhiteSpace(deviceInstallation.InstallationId))
                    throw new Exception($"No {nameof(deviceInstallation.InstallationId)} value for {nameof(DeviceInstallation)}");

                if (string.IsNullOrWhiteSpace(deviceInstallation.Platform))
                    throw new Exception($"No {nameof(deviceInstallation.Platform)} value for {nameof(DeviceInstallation)}");

                if (string.IsNullOrWhiteSpace(deviceInstallation.PushChannel))
                    throw new Exception($"No {nameof(deviceInstallation.PushChannel)} value for {nameof(DeviceInstallation)}");

                await SendAsync<DeviceInstallation>(HttpMethod.Put, RequestUrl, deviceInstallation)
                    .ConfigureAwait(false);

                var serializedTags = JsonConvert.SerializeObject(tags);
                await SecureStorage.SetAsync(CachedTagsKey, serializedTags);
            }

            public async Task RefreshRegistrationAsync()
            {
                var serializedTags = await SecureStorage.GetAsync(CachedTagsKey)
                    .ConfigureAwait(false);

                if (string.IsNullOrWhiteSpace(serializedTags))
                    return;

                var tags = JsonConvert.DeserializeObject<string[]>(serializedTags);

                await RegisterDeviceAsync(tags);
            }

            async Task SendAsync<T>(HttpMethod requestType, string requestUri, T obj)
            {
                string serializedContent = null;

                await Task.Run(() => serializedContent = JsonConvert.SerializeObject(obj))
                    .ConfigureAwait(false);

                await SendAsync(requestType, requestUri, serializedContent);
            }

            async Task SendAsync(
                HttpMethod requestType,
                string requestUri,
                string jsonRequest = null)
            {
                var request = new HttpRequestMessage(requestType, new Uri($"{_baseApiUrl}{requestUri}"));

                if (jsonRequest != null)
                    request.Content = new StringContent(jsonRequest, Encoding.UTF8, "application/json");

                var response = await _client.SendAsync(request).ConfigureAwait(false);

                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

    > [!NOTE]
    > Argumentet **apiKey** krävs endast om du har valt att slutföra kommandot [autentisera klienter med en API-nyckel](#authenticate-clients-using-an-api-key-optional) .

1. Lägg till en **Tom klass** till mappen **tjänster** med namnet *PushDemoNotificationActionService.cs* implementera *IPushDemoNotificationActionService* med följande kod.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public class PushDemoNotificationActionService : IPushDemoNotificationActionService
        {
            readonly Dictionary<string, PushDemoAction> _actionMappings = new Dictionary<string, PushDemoAction>
            {
                { "action_a", PushDemoAction.ActionA },
                { "action_b", PushDemoAction.ActionB }
            };

            public event EventHandler<PushDemoAction> ActionTriggered = delegate { };

            public void TriggerAction(string action)
            {
                if (!_actionMappings.TryGetValue(action, out var pushDemoAction))
                    return;

                List<Exception> exceptions = new List<Exception>();

                foreach (var handler in ActionTriggered?.GetInvocationList())
                {
                    try
                    {
                        handler.DynamicInvoke(this, pushDemoAction);
                    }
                    catch (Exception ex)
                    {
                        exceptions.Add(ex);
                    }
                }

                if (exceptions.Any())
                    throw new AggregateException(exceptions);
            }
        }
    }
    ```

1. Lägg till en **Tom klass** i **PushDemo** -projektet med namnet *config.cs* med följande implementering.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            public static string ApiKey = "API_KEY";
            public static string BackendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
        }
    }
    ```

    > [!NOTE]
    > Detta används på ett enkelt sätt för att hålla hemligheterna ur käll kontroll. Du kan ersätta dessa värden som en del av en automatiserad version eller åsidosätta dem med hjälp av en lokal partiell klass. Du kommer att göra detta i nästa steg.
    >
    > Fältet **ApiKey** krävs bara om du har valt att slutföra kommandot [autentisera klienter med en API-nyckel](#authenticate-clients-using-an-api-key-optional) .

1. Lägg till en annan **Tom klass** i **PushDemo** -projektet den här gången kallas *config. local_secrets. cs* med följande implementering.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            static Config()
            {
                ApiKey = "<your_api_key>";
                BackendServiceEndpoint = "<your_api_app_url>";
            }
        }
    }
    ```

    > [!NOTE]
    > Ersätt plats hållarnas värden med dina egna. Du bör ha gjort en anteckning om dessa när du skapade backend-tjänsten. URL: en för **API-appen** ska vara ``https://<api_app_name>.azurewebsites.net/`` . Kom ihåg att lägga till ``*.local_secrets.*`` i din gitignore-fil för att undvika att spara filen.
    >
    > Fältet **ApiKey** krävs bara om du har valt att slutföra kommandot [autentisera klienter med en API-nyckel](#authenticate-clients-using-an-api-key-optional) .

1. Lägg till en **Tom klass** i **PushDemo** -projektet med namnet *bootstrap.cs* med följande implementering.  

    ```csharp
    using System;
    using PushDemo.Services;

    namespace PushDemo
    {
        public static class Bootstrap
        {
            public static void Begin(Func<IDeviceInstallationService> deviceInstallationService)
            {
                ServiceContainer.Register(deviceInstallationService);

                ServiceContainer.Register<IPushDemoNotificationActionService>(()
                    => new PushDemoNotificationActionService());

                ServiceContainer.Register<INotificationRegistrationService>(()
                    => new NotificationRegistrationService(
                        Config.BackendServiceEndpoint,
                        Config.ApiKey));
            }
        }
    }
    ```

    > [!NOTE]
    > **BEGIN** -metoden kommer att anropas av varje plattform när appen startas i en plattformsspecifik implementering av **IDeviceInstallationService**.
    >
    > **NotificationRegistrationService** **apiKey** -konstruktorn krävs bara om du har valt att slutföra kommandot [autentisera klienter med en API-nyckel](#authenticate-clients-using-an-api-key-optional) .

### <a name="implement-the-cross-platform-ui"></a>Implementera gränssnittet för plattforms oberoende

1. Öppna **mainpage. XAML** i **PushDemo** -projektet och Ersätt **StackLayout** -kontrollen med följande.

    ```xml
    <StackLayout VerticalOptions="EndAndExpand"  
                 HorizontalOptions="FillAndExpand"
                 Padding="20,40">
        <Button x:Name="RegisterButton"
                Text="Register"
                Clicked="RegisterButtonClicked" />
        <Button x:Name="DeregisterButton"
                Text="Deregister"
                Clicked="DeregisterButtonClicked" />
    </StackLayout>
    ```

1. Lägg nu till ett **skrivskyddat** säkerhets kopierings fält i **mainpage.XAML.cs**för att lagra en referens till **INotificationRegistrationService** -implementeringen.

    ```csharp
    readonly INotificationRegistrationService _notificationRegistrationService;
    ```

1. I **mainpage** -konstruktorn löser du **INotificationRegistrationService** -implementeringen med hjälp av **service container** och tilldelar den till fältet *_notificationRegistrationService_* -säkerhetskopierat.

    ```csharp
    public MainPage()
    {
        InitializeComponent();

        _notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>();
    }
    ```

1. Implementera händelse hanterare för de **RegisterButton** -och **DeregisterButton** -knappar som **klickade** på händelser som anropar motsvarande **register** / **Avregistrerings** metoder.

    ```csharp
    void RegisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.RegisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device registered"); });

    void DeregisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.DeregisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device deregistered"); });

    void ShowAlert(string message)
        => MainThread.BeginInvokeOnMainThread(()
            => DisplayAlert("PushDemo", message, "OK").ContinueWith((task)
                => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. Se till att följande namn områden är refererade i **app.XAML.cs**.

    ```csharp
    using PushDemo.Models;
    using PushDemo.Services;
    using Xamarin.Essentials;
    using Xamarin.Forms;
    ```

1. Implementera händelse hanteraren för **IPushDemoNotificationActionService** **ActionTriggered** -händelsen.

    ```csharp
    void NotificationActionTriggered(object sender, PushDemoAction e)
        => ShowActionAlert(e);

    void ShowActionAlert(PushDemoAction action)
        => MainThread.BeginInvokeOnMainThread(()
            => MainPage?.DisplayAlert("PushDemo", $"{action} action received", "OK")
                .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. I **app** -konstruktorn löser du **IPushNotificationActionService** -implementeringen med hjälp av **service container** och prenumererar på händelsen **IPushDemoNotificationActionService** **ActionTriggered** .

    ```csharp
    public App()
    {
        InitializeComponent();

        ServiceContainer.Resolve<IPushDemoNotificationActionService>()
            .ActionTriggered += NotificationActionTriggered;

        MainPage = new MainPage();
    }
    ```

    > [!NOTE]
    > Detta är bara att demonstrera mottagning och spridning av push-meddelanden. De kan vanligt vis hanteras i bakgrunden för att till exempel navigera till en viss vy eller uppdatera vissa data i stället för att visa en avisering via rot **sidan**, **mainpage** i det här fallet.
