---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 1dc491084f65bc90397b0897de6b6cfe4f2fd410
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448756"
---
### <a name="configure-infoplist-and-entitlementsplist"></a>Konfigurera info. plist och rättigheter. plist

1. Se till att du har loggat in på ditt **Apple Developer-konto** i **Visual Studio**-  >  **Inställningar...**  >  **Publicera**  >  **Apple Developer-konton** och lämpligt *certifikat* och *etablerings profil* har hämtats. Du bör ha skapat dessa till gångar som en del av föregående steg.

1. I **PushDemo. iOS**öppnar du **info. plist** och kontrollerar att **BundleIdentifier** matchar värdet som användes för respektive etablerings profil i [Apple Developer-portalen](https://developer.apple.com). **BundleIdentifier** har formatet ``com.<organization>.PushDemo`` .

1. I samma fil anger du **lägsta System version** till **13,0**.

    > [!NOTE]
    > Endast de enheter som kör **iOS 13,0 och senare** stöds för den här själv studie kursen, men du kan utöka den till att stödja enheter som kör äldre versioner.

1. Öppna **projekt alternativen** för **PushDemo. iOS** (dubbelklicka på projektet).

1. I **projekt alternativ**, under **build > iOS-paket signering**, kontrollerar du att ditt Developer-konto är valt under **team**. Kontrol lera sedan att "hantera signering" automatiskt är markerat och att ditt signerings certifikat och etablerings profil väljs automatiskt.

    > [!NOTE]
    > Om ditt *signerings certifikat* och *etablerings profilen* inte har marker ATS automatiskt väljer du **manuell etablering**och klickar sedan på **paket signerings alternativ**. Se till att ditt *team* har marker ATS för **signering av identitet** och att din *PushDemo* -angivna etablerings profil har valts för **etablerings profil** för både **fel söknings** -och **versions** konfigurationer som säkerställer att **iPhone** har valts för **plattformen** i båda fallen.

1. I **PushDemo. iOS**öppnar du **rättigheter. plist** och kontrollerar att **Aktivera push-meddelanden** är markerat när det visas på fliken **rättigheter** . Kontrol lera sedan att inställningen för **APS-miljön** är inställd på **utveckling** när den visas på fliken **källa** .

### <a name="handle-push-notifications-for-ios"></a>Hantera push-meddelanden för iOS

1. **Kontroll**  +  **Klicka** på **PushDemo. iOS** -projektet, Välj **ny mapp** på menyn **Lägg till** och klicka sedan **på Lägg till** med *tjänster* som **mappnamn**.

1. **Kontroll**  +  **Klicka** på mappen **tjänster** och välj sedan **ny fil...** från menyn **Lägg till** .

1. Välj **allmän**  >  **Tom klass**, ange *DeviceInstallationService.cs* som **namn**och klicka sedan på **ny** Lägg till följande implementering.

    ```csharp
    using System;
    using PushDemo.Models;
    using PushDemo.Services;
    using UIKit;

    namespace PushDemo.iOS.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            const int SupportedVersionMajor = 13;
            const int SupportedVersionMinor = 0;

            public string Token { get; set; }

            public bool NotificationsSupported
                => UIDevice.CurrentDevice.CheckSystemVersion(SupportedVersionMajor, SupportedVersionMinor);

            public string GetDeviceId()
                => UIDevice.CurrentDevice.IdentifierForVendor.ToString();

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetNotificationsSupportError());

                if (string.isNullOrWhitespace(Token))
                    throw new Exception("Unable to resolve token for APNS");

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "apns",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetNotificationsSupportError()
            {
                if (!NotificationsSupported)
                    return $"This app only supports notifications on iOS {SupportedVersionMajor}.{SupportedVersionMinor} and above. You are running {UIDevice.CurrentDevice.SystemVersion}.";

                if (Token == null)
                    return $"This app can support notifications but you must enable this in your settings.";


                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Den här klassen ger ett unikt ID (med värdet [UIDevice. IdentifierForVendor](https://docs.microsoft.com/dotnet/api/uikit.uidevice.identifierforvendor?view=xamarin-ios-sdk-12) ) och nytto lasten för Notification Hub-registrering.

1. Lägg till en ny mapp i **PushDemo. iOS** -projektet med namnet *tillägg* och Lägg sedan till en **Tom klass** i mappen med namnet *NSDataExtensions.cs* med följande implementering.

    ```csharp
    using System.Text;
    using Foundation;

    namespace PushDemo.iOS.Extensions
    {
        internal static class NSDataExtensions
        {
            internal static string ToHexString(this NSData data)
            {
                var bytes = data.ToArray();

                if (bytes == null)
                    return null;

                StringBuilder sb = new StringBuilder(bytes.Length * 2);

                foreach (byte b in bytes)
                    sb.AppendFormat("{0:x2}", b);

                return sb.ToString().ToUpperInvariant();
            }
        }
    }
    ```

1. I **AppDelegate.cs**kontrollerar du att följande namn rymder har lagts till överst i filen.

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Foundation;
    using PushDemo.iOS.Extensions;
    using PushDemo.iOS.Services;
    using PushDemo.Services;
    using UIKit;
    using UserNotifications;
    using Xamarin.Essentials;
    ```

1. Lägg till privata egenskaper och deras respektive säkerhets kopierings fält för att lagra en referens till **IPushDemoNotificationActionService**-, **INotificationRegistrationService**-och **IDeviceInstallationService** -implementeringarna.

    ```csharp
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
    ```

1. Lägg till **RegisterForRemoteNotifications** -metoden för att registrera inställningar för användar meddelanden och sedan för fjärraviseringar med **APN**.

    ```csharp
    void RegisterForRemoteNotifications()
    {
        MainThread.BeginInvokeOnMainThread(() =>
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert |
                UIUserNotificationType.Badge |
                UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        });
    }
    ```

1. Lägg till **CompleteRegistrationAsync** -metoden för att ange `IDeviceInstallationService.Token` egenskap svärdet. Uppdatera registreringen och cachelagra enhetens token om den har uppdaterats sedan den senast lagrades.

    ```csharp
    Task CompleteRegistrationAsync(NSData deviceToken)
    {
        DeviceInstallationService.Token = deviceToken.ToHexString();
        return NotificationRegistrationService.RefreshRegistrationAsync();
    }
    ```

1. Lägg till **ProcessNotificationActions** -metoden för att bearbeta **NSDictionary** -aviserings data och villkorligt anropa **NotificationActionService. TriggerAction**.

    ```csharp
    void ProcessNotificationActions(NSDictionary userInfo)
    {
        if (userInfo == null)
            return;

        try
        {
            var actionValue = userInfo.ObjectForKey(new NSString("action")) as NSString;

            if (!string.IsNullOrWhiteSpace(actionValue?.Description))
                NotificationActionService.TriggerAction(actionValue.Description);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Åsidosätt metoden **RegisteredForRemoteNotifications** genom att skicka **deviceToken** -argumentet till **CompleteRegistrationAsync** -metoden.

    ```csharp
    public override void RegisteredForRemoteNotifications(
        UIApplication application,
        NSData deviceToken)
        => CompleteRegistrationAsync(deviceToken).ContinueWith((task)
            => { if (task.IsFaulted) throw task.Exception; });
    ```

1. Åsidosätt metoden **ReceivedRemoteNotification** genom att skicka **userInfo** -argumentet till **ProcessNotificationActions** -metoden.

    ```csharp
    public override void ReceivedRemoteNotification(
        UIApplication application,
        NSDictionary userInfo)
        => ProcessNotificationActions(userInfo);
    ```

1. Åsidosätt **FailedToRegisterForRemoteNotifications** -metoden för att logga felet.

    ```csharp
    public override void FailedToRegisterForRemoteNotifications(
        UIApplication application,
        NSError error)
        => Debug.WriteLine(error.Description);
    ```

    > [!NOTE]
    > Det här är mycket en plats hållare. Du bör implementera korrekt loggning och fel hantering för produktions scenarier.

1. Uppdatera **FinishedLaunching** -metoden så att den anropas `Bootstrap.Begin` direkt efter att anropet `Forms.Init` överförts i den plattformsspecifika implementeringen av **IDeviceInstallationService**.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. I samma metod kan villkorligt begära auktorisering och registrera sig för fjärraviseringar omedelbart efter `Bootstrap.Begin` .

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        UNUserNotificationCenter.Current.RequestAuthorization(
                UNAuthorizationOptions.Alert |
                UNAuthorizationOptions.Badge |
                UNAuthorizationOptions.Sound,
                (approvalGranted, error) =>
                {
                    if (approvalGranted && error == null)
                        RegisterForRemoteNotifications();
                });
    }
    ```

1. Anropa **ProcessNotificationActions** omedelbart efter anropet till **FinishedLaunching** `LoadApplication` om argumentet **alternativ** innehåller **UIApplication. LaunchOptionsRemoteNotificationKey** som skickas i det resulterande **userInfo** -objektet i FinishedLaunching.

    ```csharp
    using (var userInfo = options?.ObjectForKey(
        UIApplication.LaunchOptionsRemoteNotificationKey) as NSDictionary)
            ProcessNotificationActions(userInfo);
    ```
