---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c919cfce3d868a81f28eb8172314e9639387e933
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112045"
---
### <a name="validate-package-name-and-permissions"></a>Verifiera paket namn och behörigheter

1. I **PushDemo. Android**öppnar du **projekt alternativen** sedan **Android-appen** från **build** -avsnittet.

1. Kontrol lera att **paket namnet** stämmer överens med det värde som du använde i [Firebase-konsolens](https://console.firebase.google.com) **PushDemo** -projekt. **Paket namnet** har formatet ``com.<organization>.pushdemo`` .

1. Ange den **lägsta Android-versionen** till **Android 8,0 (API-nivå 26)** och **mål-Android-versionen** till den senaste API- **nivån**.

    > [!NOTE]
    > Endast de enheter som kör **API-nivå 26 och senare** stöds för den här själv studie kursen, men du kan utöka den till att stödja enheter som kör äldre versioner.

1. Se till att **Internet** -och **READ_PHONE_STATE** behörigheter är aktiverade under **nödvändig behörighet**.

1. Klicka på **OK**

### <a name="add-the-xamarin-google-play-services-base-and-xamarinfirebasemessaging-packages"></a>Lägg till Xamarin Google Play Services Base och Xamarin. Firebase. Messaging-paket

1. I **PushDemo. Android**, **kontroll**  +  **klickar** du på mappen **paket** och väljer sedan **Hantera NuGet-paket...**.

1. Sök efter **Xamarin. GooglePlayServices. Base** (inte **Basement**) och kontrol lera att den är markerad.

1. Sök efter **Xamarin. Firebase. Messaging** och kontrol lera att det är markerat.

1. Klicka på **Lägg till paket**och klicka sedan på **acceptera** när du uppmanas att godkänna **licens villkoren**.

### <a name="add-the-google-services-json-file"></a>Lägg till Google Services JSON-filen

1. **Kontroll**  +  **Klicka** på `PushDemo.Android` projektet och välj sedan **befintlig fil...** från menyn **Lägg till** .

1. Välj den *google-services.jspå* filen som du laddade ned tidigare när du konfigurerade **PushDemo** -projektet i [Firebase-konsolen](https://console.firebase.google.com) och klicka sedan på **Öppna**.

1. När du uppmanas väljer du att **Kopiera filen till katalogen**.

1. **Kontroll**  +  **Klicka** på *google-services.jspå* filen inifrån `PushDemo.Android` projektet och kontrol lera att **GoogleServicesJson** har angetts som build- **åtgärd**.

### <a name="handle-push-notifications-for-android"></a>Hantera push-meddelanden för Android

1. **Kontroll**  +  **Klicka** på `PushDemo.Android` projektet, Välj **ny mapp** på menyn **Lägg till** och klicka sedan på **Lägg till** med *tjänster* som **mappnamn**.

1. **Kontroll**  +  **Klicka** på mappen **tjänster** och välj sedan **ny fil...** från menyn **Lägg till** .

1. Välj **allmän**  >  **Tom klass**, ange *DeviceInstallationService.cs* som **namn**och klicka sedan på **ny** Lägg till följande implementering.

    ```csharp
    using System;
    using Android.App;
    using Android.Gms.Common;
    using PushDemo.Models;
    using PushDemo.Services;
    using static Android.Provider.Settings;

    namespace PushDemo.Droid.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            public string Token { get; set; }

            public bool NotificationsSupported
                => GoogleApiAvailability.Instance
                    .IsGooglePlayServicesAvailable(Application.Context) == ConnectionResult.Success;

            public string GetDeviceId()
                => Secure.GetString(Application.Context.ContentResolver, Secure.AndroidId);

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetPlayServicesError());

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "fcm",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetPlayServicesError()
            {
                int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(Application.Context);

                if (resultCode != ConnectionResult.Success)
                    return GoogleApiAvailability.Instance.IsUserResolvableError(resultCode) ?
                               GoogleApiAvailability.Instance.GetErrorString(resultCode) :
                               "This device is not supported";

                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Den här klassen ger ett unikt ID (med hjälp av [Secure. AndroidId](https://docs.microsoft.com/dotnet/api/android.provider.settings.secure.androidid?view=xamarin-android-sdk-9)) som en del av nytto lasten Notification Hub-registrering.

1. Lägg till en annan **Tom klass** till mappen **tjänster** med namnet *PushNotificationFirebaseMessagingService.cs*och Lägg sedan till följande implementering.

    ```csharp
    using Android.App;
    using Android.Content;
    using Firebase.Messaging;
    using PushDemo.Services;

    namespace PushDemo.Droid.Services
    {
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class PushNotificationFirebaseMessagingService : FirebaseMessagingService
        {
            IPushDemoNotificationActionService _notificationActionService;
            INotificationRegistrationService _notificationRegistrationService;
            IDeviceInstallationService _deviceInstallationService;

            IPushDemoNotificationActionService NotificationActionService
                => _notificationActionService ??
                    (_notificationActionService =
                    ServiceContainer.Resolve<IPushDemoNotificationActionService>());

            INotificationRegistrationService NotificationRegistrationService
                => _notificationRegistrationService ??
                    (_notificationRegistrationService =
                    ServiceContainer.Resolve<INotificationRegistrationService>());

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService =
                    ServiceContainer.Resolve<IDeviceInstallationService>());

            public override void OnNewToken(string token)
            {
                DeviceInstallationService.Token = token;

                NotificationRegistrationService.RefreshRegistrationAsync()
                    .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; });
            }

            public override void OnMessageReceived(RemoteMessage message)
            {
                if(message.Data.TryGetValue("action", out var messageAction))
                    NotificationActionService.TriggerAction(messageAction);
            }
        }
    }
    ```

1. I **MainActivity.cs**kontrollerar du att följande namn rymder har lagts till överst i filen.

    ```csharp
    using System;
    using Android.App;
    using Android.Content;
    using Android.Content.PM;
    using Android.OS;
    using Android.Runtime;
    using Firebase.Iid;
    using PushDemo.Droid.Services;
    using PushDemo.Services;
    ```

1. I **MainActivity.cs**anger du **LaunchMode** till **SingleTop** så **MainActivity** skapas inte igen när den öppnas.

    ```csharp
    [Activity(
        Label = "PushDemo",
        LaunchMode = LaunchMode.SingleTop,
        Icon = "@mipmap/icon",
        Theme = "@style/MainTheme",
        MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    ```

1. Lägg till privata egenskaper och motsvarande säkerhets kopierings fält för att lagra en referens till **IPushNotificationActionService** -och **IDeviceInstallationService** -implementeringarna.

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. Implementera **IOnSuccessListener** -gränssnittet för att hämta och lagra **Firebase** -token.

    ```csharp
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity, Android.Gms.Tasks.IOnSuccessListener
    {
        ...

        public void OnSuccess(Java.Lang.Object result)
            => DeviceInstallationService.Token =
                result.Class.GetMethod("getToken").Invoke(result).ToString();
    }
    ```

1. Lägg till en ny metod med namnet **ProcessNotificationActions** som kontrollerar om en angiven **avsikt** har ett extra värde med namnet *Action*. Utlöser villkorligt den åtgärden med hjälp av **IPushDemoNotificationActionService** -implementeringen.

    ```csharp
    void ProcessNotificationActions(Intent intent)
    {
        try
        {
            if (intent?.HasExtra("action") == true)
            {
                var action = intent.GetStringExtra("action");

                if (!string.IsNullOrEmpty(action))
                    NotificationActionService.TriggerAction(action);
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Åsidosätt metoden **OnNewIntent** för att anropa **CheckIntentForNotificationActions** -metoden.

    ```csharp
    protected override void OnNewIntent(Intent intent)
    {
        base.OnNewIntent(intent);
        ProcessNotificationActions(intent);
    }
    ```

    > [!NOTE]
    > Eftersom **LaunchMode** för **aktiviteten** är inställt på **SingleTop**skickas en **avsikt** till den befintliga **aktivitets** instansen via metoden **OnNewIntent** i stället för **OnCreate** -metoden, så du måste hantera en inkommande avsikt i både **OnCreate** -och **OnNewIntent** -metoderna.

1. Uppdatera **OnCreate** -metoden så att den anropas `Bootstrap.Begin` direkt efter att anropet `base.OnCreate` överförts i den plattformsspecifika implementeringen av **IDeviceInstallationService**.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. I samma metod måste du villkorligt anropa **GetInstanceId** på **FirebaseApp** -instansen, direkt efter anropet till `Bootstrap.Begin` , lägga till **MainActivity** som **IOnSuccessListener**.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        FirebaseInstanceId.GetInstance(Firebase.FirebaseApp.Instance)
            .GetInstanceId()
            .AddOnSuccessListener(this);
    }
    ```

1. Anropa **ProcessNotificationActions** omedelbart efter att anropet till **OnCreate** `LoadApplication` överförts i den aktuella **avsikten**i OnCreate.

    ```cs
    ...

    LoadApplication(new App());

    ProcessNotificationActions(Intent);
    ```

> [!NOTE]
> Du måste registrera om appen varje gång du kör den och stoppa den från en felsökningssession för att fortsätta att ta emot push-meddelanden.
