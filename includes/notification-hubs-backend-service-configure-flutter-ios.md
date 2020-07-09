---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9a8b76c0887cff498edd90aedf5cc6eb674fc7e5
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147821"
---
### <a name="configure-the-runner-target-and-infoplist"></a>Konfigurera utlöpare-målet och info. plist

1. I **Visual Studio Code**, **kontroll**  +  **klickar** du på mappen **iOS** och väljer sedan **Öppna i Xcode**.

1. I **Xcode**klickar du på **löpare** ( **XCODEPROJ** högst upp, inte i mappen) och **sedan på att** sedan **signera &-funktioner**. När du har valt **alla** build-konfiguration väljer du ditt Developer-konto för **teamet**. Se till att alternativet "hantera signering automatiskt" är markerat och att ditt signerings certifikat och etablerings profil väljs automatiskt.

    > [!NOTE]
    > Om du inte ser det nya etablerings profil svärdet kan du försöka med att uppdatera profilerna för signerings identiteten genom att välja kontot för **Xcode**  >  **Inställningar**och  >  **Account** sedan hämta profilerna genom att välja knappen **Hämta manuella profiler** .

1. Klicka på **+ funktion**och Sök sedan efter **push-meddelanden**. **Dubbelklicka** på **push-meddelanden** för att lägga till den här funktionen.

1. Öppna **info. plist** och ange den **lägsta system versionen** till **13,0**.

    > [!NOTE]
    > Endast de enheter som kör **iOS 13,0 och senare** stöds för den här själv studie kursen, men du kan utöka den till att stödja enheter som kör äldre versioner.

1. Öppna **löpare. rättigheter** och se till att inställningen för **APS-miljön** är inställd på **utveckling**.

### <a name="handle-push-notifications-for-ios"></a>Hantera push-meddelanden för iOS

1. **Kontroll**  +  **Klicka** på mappen **löpare** (i projektet löpare) och välj sedan **ny grupp** med **tjänster** som namn.

1. **Kontroll**  +  **Klicka** på mappen **tjänster** och välj sedan **ny fil...**. Välj sedan **Swift-fil** och klicka på **Nästa**. Ange **DeviceInstallationService** som namn och klicka sedan på **skapa**.

1. Implementera **DeviceInstallationService. SWIFT** med hjälp av följande kod.

    ```swift
    import Foundation

    class DeviceInstallationService {

        enum DeviceRegistrationError: Error {
            case notificationSupport(message: String)
        }

        var token : Data? = nil

        let DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
        let GET_DEVICE_ID = "getDeviceId"
        let GET_DEVICE_TOKEN = "getDeviceToken"
        let GET_DEVICE_PLATFORM = "getDevicePlatform"

        private let deviceInstallationChannel : FlutterMethodChannel

        var notificationsSupported : Bool {
            get {
                if #available(iOS 13.0, *) {
                    return true
                }
                else {
                    return false
                }
            }
        }

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
            deviceInstallationChannel = FlutterMethodChannel(name: DEVICE_INSTALLATION_CHANNEL, binaryMessenger: binaryMessenger)
            deviceInstallationChannel.setMethodCallHandler(handleDeviceInstallationCall)
        }

        func getDeviceId() -> String {
            return UIDevice.current.identifierForVendor!.description
        }

        func getDeviceToken() throws -> String {
            if(!notificationsSupported) {
                let notificationSupportError = getNotificationsSupportError()
                throw DeviceRegistrationError.notificationSupport(message: notificationSupportError)
            }

            if (token == nil) {
                throw DeviceRegistrationError.notificationSupport(message: "Unable to resolve token for APNS.")
            }

            return token!.reduce("", {$0 + String(format: "%02X", $1)})
        }

        func getDevicePlatform() -> String {
            return "apns"
        }

        private func handleDeviceInstallationCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_DEVICE_ID:
                result(getDeviceId())
            case GET_DEVICE_TOKEN:
                getDeviceToken(result: result)
            case GET_DEVICE_PLATFORM:
                result(getDevicePlatform())
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        private func getDeviceToken(result: @escaping FlutterResult) {
            do {
                let token = try getDeviceToken()
                result(token)
            }
            catch let error {
                result(FlutterError(code: "UNAVAILABLE", message: error.localizedDescription, details: nil))
            }
        }

        private func getNotificationsSupportError() -> String {

            if (!notificationsSupported) {
                return "This app only supports notifications on iOS 13.0 and above. You are running \(UIDevice.current.systemVersion)"
            }

            return "An error occurred preventing the use of push notifications."
        }
    }
    ```

    > [!NOTE]
    > Den här klassen implementerar den plattformsspecifik motparten för `com.<your_organization>.pushdemo/deviceinstallation` kanalen. Detta definierades i Flutter-delen av appen i **DeviceInstallationService. Dart**. I detta fall görs anropen från den gemensamma koden till den interna värden. Se till att ersätta **<your_organization>** med din egen organisation oavsett var den används.
    >
    > Den här klassen ger ett unikt ID (med hjälp av värdet [UIDevice. identifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor) ) som en del av nytto lasten för Notification Hub-registrering.

1. Lägg till en annan **Swift-fil** till mappen **tjänster** med namnet *NotificationRegistrationService*och Lägg sedan till följande kod.

    ```swift
    import Foundation

    class NotificationRegistrationService {

        let NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
        let REFRESH_REGISTRATION = "refreshRegistration"

        private let notificationRegistrationChannel : FlutterMethodChannel

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
           notificationRegistrationChannel = FlutterMethodChannel(name: NOTIFICATION_REGISTRATION_CHANNEL, binaryMessenger: binaryMessenger)
        }

        func refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, arguments: nil)
        }
    }
    ```

    > [!NOTE]
    > Den här klassen implementerar den plattformsspecifik motparten för `com.<your_organization>.pushdemo/notificationregistration` kanalen. Detta definierades i Flutter-delen av appen i **NotificationRegistrationService. Dart**. I detta fall görs anropen från den interna värden till den gemensamma koden. Börja med att ersätta **<your_organization>** med din egen organisation oavsett var den används.

1. Lägg till en annan **Swift-fil** till mappen **tjänster** med namnet *NotificationActionService*och Lägg sedan till följande kod.

    ```swift
    import Foundation

    class NotificationActionService {

        let NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
        let TRIGGER_ACTION = "triggerAction"
        let GET_LAUNCH_ACTION = "getLaunchAction"

        private let notificationActionChannel: FlutterMethodChannel

        var launchAction: String? = nil

        init(withBinaryMessenger binaryMessenger: FlutterBinaryMessenger) {
            notificationActionChannel = FlutterMethodChannel(name: NOTIFICATION_ACTION_CHANNEL, binaryMessenger: binaryMessenger)
            notificationActionChannel.setMethodCallHandler(handleNotificationActionCall)
        }

        func triggerAction(action: String) {
           notificationActionChannel.invokeMethod(TRIGGER_ACTION, arguments: action)
        }

        private func handleNotificationActionCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_LAUNCH_ACTION:
                result(launchAction)
            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
    ```

    > [!NOTE]
    > Den här klassen implementerar den plattformsspecifik motparten för `com.<your_organization>.pushdemo/notificationaction` kanalen. Som definierades i Flutter-delen av appen i **NotificationActionService. Dart**. Anrop kan göras i båda riktningarna i det här fallet. Se till att ersätta **<your_organization>** med din egen organisation oavsett var den används.

1. I **AppDelegate. SWIFT**lägger du till variabler för att lagra en referens till de tjänster som du skapade tidigare.

    ```swift
    var deviceInstallationService : DeviceInstallationService?
    var notificationRegistrationService : NotificationRegistrationService?
    var notificationActionService : NotificationActionService?
    ```

1. Lägg till en funktion som kallas **processNotificationActions** för bearbetning av aviserings data. Utlöser villkorligt åtgärden eller lagrar den för användning senare om åtgärden bearbetas när appen startas.

    ```swift
    func processNotificationActions(userInfo: [AnyHashable : Any], launchAction: Bool = false) {
        if let action = userInfo["action"] as? String {
            if (launchAction) {
                notificationActionService?.launchAction = action
            }
            else {
                notificationActionService?.triggerAction(action: action)
            }
        }
    }
    ```

1. Åsidosätt funktionen **didRegisterForRemoteNotificationsWithDeviceToken** som anger värdet för **token** för **DeviceInstallationService**. Anropa sedan **refreshRegistration** på **NotificationRegistrationService**.

    ```swift
    override func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
      deviceInstallationService?.token = deviceToken
      notificationRegistrationService?.refreshRegistration()
    }
    ```

1. Åsidosätt funktionen **didReceiveRemoteNotification** genom att skicka **userInfo** -argumentet till **processNotificationActions** -funktionen.

    ```swift
    override func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
        processNotificationActions(userInfo: userInfo)
    }
    ```

1. Åsidosätt funktionen **didFailToRegisterForRemoteNotificationsWithError** för att logga felet.

    ```swift
    override func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(error);
    }
    ```

    > [!NOTE]
    > Det här är mycket en plats hållare. Du bör implementera korrekt loggning och fel hantering för produktions scenarier.

1. I **didFinishLaunchingWithOptions**instansierar du variablerna **deviceInstallationService**, **notificationRegistrationService**och **notificationActionService** .

    ```swift
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

    deviceInstallationService = DeviceInstallationService(withBinaryMessenger: controller.binaryMessenger)
    notificationRegistrationService = NotificationRegistrationService(withBinaryMessenger: controller.binaryMessenger)
    notificationActionService = NotificationActionService(withBinaryMessenger: controller.binaryMessenger)
    ```

1. I samma funktion kräver villkorlig auktorisering och registrera dig för fjärraviseringar.

    ```swift
    if #available(iOS 13.0, *) {
      UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
          (granted, error) in

          if (granted)
          {
              DispatchQueue.main.async {
                  let pushSettings = UIUserNotificationSettings(types: [.alert, .sound, .badge], categories: nil)
                  application.registerUserNotificationSettings(pushSettings)
                  application.registerForRemoteNotifications()
              }
          }
      }
    }
    ```

1. Om **launchOptions** innehåller **remoteNotification** -nyckeln anropar du **processNotificationActions** i slutet av **didFinishLaunchingWithOptions** -funktionen. Skicka in det resulterande **userInfo** -objektet och Använd *True* för argumentet *launchAction* . Ett *Sant* värde anger att åtgärden bearbetas när appen startas.

    ```swift
    if let userInfo = launchOptions?[.remoteNotification] as? [AnyHashable : Any] {
        processNotificationActions(userInfo: userInfo, launchAction: true)
    }
    ```
