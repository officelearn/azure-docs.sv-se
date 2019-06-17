---
title: Push-meddelanden till Swift iOS-appar som använder Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du push-meddelanden till Swift iOS-appar som använder Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: a4773ddd8114659118e89cfee57e73ddb39ff6b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116682"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>Självstudier: Push-meddelanden till Swift iOS-appar som använder Notification Hubs REST API

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

I den här självstudien får du använder Azure Notification Hubs för push-meddelanden till ett Swift-baserade iOS-program med hjälp av den [REST API](/rest/api/notificationhubs/). Du också skapa en tom iOS-app som tar emot push-meddelanden med hjälp av den [Apple Push Notification service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Den här kursen går vi igenom följande steg:

> [!div class="checklist"]
> * Generera begärandefilen för certifikatsignering.
> * Begär din app för push-meddelanden.
> * Skapa en etableringsprofil för appen.
> * Skapa en meddelandehubb.
> * Konfigurera meddelandehubben med APNs-information.
> * Anslut iOS-appen till en meddelandehubb.
> * Testa lösningen.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill följa med, behöver du:

- Gå igenom [översikt över Azure Notification Hubs](notification-hubs-push-notification-overview.md) om du inte är bekant med tjänsten.
- Vill veta mer om [registreringar och installationen](notification-hubs-push-notification-registration-management.md).
- En aktiv [Apple Developer-konto](https://developer.apple.com).
- En Mac med Xcode tillsammans med ett giltigt developer-certifikat som är installerat i din nyckelring.
- En fysisk iPhone-enhet som du kan köra och felsöka med, eftersom du inte testa push-meddelanden med simulatorn.
- Fysiska iPhone-enheten registrerad i den [Apples Portal](https://developer.apple.com) och som är associerade med ditt certifikat.
- En [Azure-prenumeration](https://portal.azure.com) där du kan skapa och hantera resurser.

Även om du har några tidigare erfarenheter med iOS-utveckling, bör du kunna följa stegen för att skapa det här exemplet första-principer. Du får dock nytta bekant med följande begrepp:

- Att skapa iOS-appar med Xcode och Swift.
- Konfigurera en [Azure Notification hub](notification-hubs-ios-apple-push-notification-apns-get-started.md) för iOS.
- Den [Apple Developer-portalen](https://developer.apple.com) och [Azure-portalen](https://portal.azure.com).

> [!NOTE]
> Meddelandehubben ska konfigureras för att använda den **Sandbox** endast autentiseringsläge. Du bör inte använda det här autentiseringsläget för produktionsarbetsbelastningar.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Anslut iOS-appen till en meddelandehubb

I det här avsnittet ska du skapa iOS-app som ska ansluta till notification hub.  

### <a name="create-an-ios-project"></a>Skapa en iOS-projekt

1. Skapa ett nytt iOS-projekt i Xcode och välj mallen **Program enkel vy**.

1. När du anger alternativ för det nya projektet:

   1. Ange den **produktnamn** (PushDemo) och **Organisationsidentifierare** (`com.<organization>`) som du använde när du ställer in **paket-ID** i Apple Developer-portalen.

   1. Välj den **Team** som den **App-ID** har ställt in för.

   1. Ange den **språk** till **Swift**.

   1. Välj **Nästa**.

1. Skapa en ny mapp med namnet **SupportingFiles**.

1. Skapa en ny p-list-fil som heter **devsettings.plist** i den **SupportingFiles** mapp. Se till att lägga till mappen i din **gitignore** -fil så att den inte är allokerade när du arbetar med en git-lagringsplats. I ett produktionsprogram skulle du förmodligen villkorligt ställa dessa hemligheter som en del av en automatisk build-process. Sådana inställningar omfattas inte i den här genomgången.

1. Uppdatera **devsettings.plist** att inkludera följande konfigurationsposter med hjälp av dina egna värden från den meddelandehubb som du har etablerat:

   | Nyckel                            | Typ                     | Värde                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | <hubKey>                  |
   | notificationHubKeyName         | String                   | <hubKeyName>              |
   | notificationHubName            | String                   | <hubName>                 |
   | notificationHubNamespace       | String                   | <hubNamespace>            |

   Du hittar värdena som krävs genom att gå till notification hub-resurs i Azure-portalen. I synnerhet de **notificationHubName** och **notificationHubNamespace** värden är i det övre högra hörnet av den **Essentials** sammanfattning inom **Översikt** sidan.

   ![Notification Hubs Essentials sammanfattning](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Du kan också hitta den **notificationHubKeyName** och **notificationHubKey** värden genom att gå till **åtkomstprinciper** och välja respektive  **Princip för**, till exempel `DefaultFullSharedAccessSignature`. Efter det att kopiera från den **primär anslutningssträng** värdet prefixet `SharedAccessKeyName=` för `notificationHubKeyName` och värdet prefixet `SharedAccessKey=` för den `notificationHubKey`.

   Anslutningssträngen ska vara i följande format:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   För att enkelt ange `DefaultFullSharedAccessSignature` så att du kan använda token för att skicka meddelanden. I praktiken den `DefaultListenSharedAccessSignature` skulle vara ett bättre alternativ för situationer där du bara vill ta emot meddelanden.

1. Under **Projektnavigeringen**väljer den **projektnamn** och välj sedan den **Allmänt** fliken.

1. Hitta **identitet** och ange sedan den **paket-ID** värde så att den matchar `com.<organization>.PushDemo`, vilket är värdet som används för den **App-ID** från föregående steg.

1. Hitta **signering**, och välj sedan lämplig **Team** för din **Apple Developer-konto**. Den **Team** värde ska motsvara det konto som du skapade ditt certifikat och profiler.

1. Xcode bör automatiskt hämta ned lämpliga **Etableringsprofil** värde baserat på den **paketidentifieraren**. Om du inte ser den nya **Etableringsprofil** värde, försöka uppdatera profilerna för den **identitet för kodsignering** genom att välja **Xcode**  >  **Inställningar** > **konto** > **visa information om**. Välj **identitet för kodsignering**, och välj sedan den **uppdatera** knappen i det nedre högra hörnet att ladda ned profilerna.

1. Välj den **funktioner** fliken och se till att **push-meddelanden** är aktiverade.

1. Öppna din **AppDelegate.swift** fil att implementera den **UNUserNotificationCenterDelegate** -protokollet och Lägg till följande kod högst upp i klassen:

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")

        ...
    }
    ```

    Senare ska du använda dessa medlemmar. Mer specifikt ska du använda den **taggar** och **genericTemplate** medlemmar som en del av registreringen. Mer information om taggar finns i [taggar för registreringar](notification-hubs-tags-segment-push-message.md) och [mallregistreringar](notification-hubs-templates-cross-platform-push-messages.md).

1. Lägg till följande kod i samma fil i **didFinishLaunchingWithOptions** funktionen:

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    Den här koden hämtar inställningsvärden från **devsettings.plist**, anger den **AppDelegate** klassen som den **UNUserNotificationCenter** delegera begäranden auktorisering för push-meddelanden och samtal **registerForRemoteNotifications**.

    För att enkelt koden stöder *iOS 10 och senare*. Du kan lägga till stöd för tidigare OS-versioner med villkorligt respektive API: er och metoder som du skulle göra.

1. Lägg till följande funktioner i samma fil:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Koden använder den **installationId** och **pushChannel** värden att registrera med notification hub. I detta fall använder du använder **UIDevice.current.identifierForVendor** ange ett unikt värde för att identifiera enheten och sedan formatera den **deviceToken** ange önskad  **pushChannel** värde. Den **showAlert** funktionen finns bara om du vill visa meddelandetext i exempelsyfte.

1. Fortfarande är i den **AppDelegate.swift** Lägg till den **willPresent** och **didReceive** fungerar till **UNUserNotificationCenterDelegate**. Dessa funktioner visas en avisering när de är ett meddelande om att en app körs i förgrunden eller bakgrunden respektive.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. Lägg till Skriv ut uttryck längst ned på den **didRegisterForRemoteNotificationsWithDeviceToken** funktionen för att kontrollera att **installationId** och **pushChannel** som ska tilldelade värden.

1. Skapa den **modeller**, **Services**, och **verktyg** mappar för de grundläggande komponenterna ska du lägga till projektet senare.

1. Kontrollera att projektet skapas och körs på en fysisk enhet. Att går inte testa push-meddelanden med hjälp av simulatorn.

### <a name="create-models"></a>Skapa modeller

I det här steget ska du skapa en uppsättning modeller för att representera den [Notification Hubs – REST API](/rest/api/notificationhubs/) nyttolaster och för att lagra nödvändiga delad åtkomst token (signatur) data.

1. Lägg till en ny Swift fil med namnet **PushTemplate.swift** till den **modeller** mapp. Den här modellen ger en struct som representerar den **BRÖDTEXT** för en enskild mall som en del av den **DeviceInstallation** nyttolast.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Lägg till en ny Swift fil med namnet **DeviceInstallation.swift** till den **modeller** mapp. Den här filen definierar en struct som representerar nyttolasten för att skapa eller uppdatera en **enhetsinstallation**. Lägg till följande kod i filen:

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. Lägg till en ny Swift fil med namnet **TokenData.swift** till den **modeller** mapp. Den här modellen används för att lagra en SAS-token tillsammans med dess förfallodatum.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>Generera en SAS-token

Meddelandehubbar kan du använda samma säkerhetsinfrastruktur som Azure Service Bus. För att anropa REST-API kan du behöva [programmatiskt Generera en SAS-token](/rest/api/eventhub/generate-sas-token) som kan användas i den **auktorisering** huvudet i begäran.  

Den resulterande token är i följande format:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Själva processen omfattar samma sex viktiga steg:  

1. Databehandling utgång vid [UNIX epoktid](https://en.wikipedia.org/wiki/Unix_time) format, vilket innebär hur många sekunder har förflutit sedan midnatt UTC-tid, 1 januari 1970.
1. Formatera den **ResourceUrl** som representerar den resurs som du försöker komma åt så att den är procentkodad och gemener. Den **ResourceUrl** har formatet `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Förbereda den **StringToSign**, som är formaterade som `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Databehandling och Base64-kodning i **signatur** med hjälp av HMAC-SHA256-hash för den **StringToSign** värde. Hash-värde som ska användas med den **nyckel** en del av den **Connection String** för respektive **auktoriseringsregel**.
1. Formatering i Base64-kodad **signatur** så att det är kodat i procent.
1. Konstruera token i det förväntade formatet med hjälp av den **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**, och **UrlEncodedResourceUrl** värden.

Se den [dokumentation för Azure Service Bus](../service-bus-messaging/service-bus-sas.md) för en mer omfattande översikt över signatur för delad åtkomst och hur Azure Service Bus och Meddelandehubbar kan använda den.

För exemplet Swift, ska du använda Apples öppen källkod **CommonCrypto** -biblioteket för att underlätta hashing av signaturen. Eftersom det är ett C-bibliotek är den inte tillgänglig i Swift direkt ur lådan. Du kan göra i biblioteket med hjälp av en datacenterbryggning rubrik.

Lägga till och konfigurera datacenterbryggning rubriken:

1. I Xcode, Välj **filen** > **New** > **filen** > **huvudfil**. Namnge huvudfilen **BridgingHeader.h**.

1. Redigera filen för att importera **CommonHMAC.h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Uppdatera målets **Versionsinställningar** att referera till rubriken datacenterbryggning:

   1. Öppna den **inställningar för att bygga** fliken och rulla ned till den **Swift Compiler** avsnittet.

   1. Se till att den **installera Objective-C kompatibilitet rubrik** alternativet är inställt på **Ja**.

   1. Ange sökvägen till filen `'<ProjectName>/BridgingHeader.h'` till den **Objective-C bryggning rubrik** alternativet. Det här är sökvägen till vår datacenterbryggning rubrik.

   Om du inte hittar de här alternativen kan du kontrollera att du har den **alla** visa valda snarare än **grundläggande** eller **anpassad**.

   Det finns många från tredje part open source-omslutning bibliotek tillgängliga som kan göra med hjälp av **CommonCrypto** lite enklare. Beskrivning av sådana bibliotek är dock utanför omfattningen för den här artikeln.

1. Lägg till en ny Swift-fil med namnet **TokenUtility.swift** inom den **verktyg** mapp och Lägg till följande kod:

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   Det här verktyget innehåller logiken som ansvarar för att generera SAS-token.

   Som beskrivs tidigare i **getSasToken** funktionen samordnar de övergripande stegen som krävs för att förbereda token. Funktionen kommer att anropas av installationstjänsten senare i den här självstudien.

   De två funktionerna anropas av den **getSasToken** funktion: **sha256HMac** för databehandling signaturen och **urlEncodedString** för den associera URL-kodning sträng. Den **urlEncodedString** funktionen krävs eftersom det inte går att uppnå nödvändiga utdata med hjälp av inbyggt **addingPercentEncoding** funktion.

   Den [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) är ett utmärkt exempel på hur de här åtgärderna i Objective-C. Mer information om Azure Service Bus SAS-token kan hittas i den [dokumentation för Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

### <a name="verify-the-sas-token"></a>Kontrollera SAS-token

Innan du implementerar installationstjänsten i klienten, kan du kontrollera att vår app korrekt genererar SAS-token med hjälp av HTTP-verktyget val. För den här självstudien, våra verktyg blir **Postman**.

Använda ett korrekt placerad Skriv ut instruktionen eller brytpunkt för att Observera det **installationId** och **token** värden som genereras av appen.

Följ dessa steg för att anropa den **installationer** API:

1. I **Postman**, öppna en ny flik.

1. Anger **hämta** och ange följande adress:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Konfigurera begärandehuvuden enligt följande:

   | Nyckel           | Värde            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Auktorisering | <sasToken>       |
   | x-ms-version  | 2015-01          |

1. Välj den **kod** knapp som visas i det övre högra hörnet under den **spara** knappen. Begäran bör likna följande exempel:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Välj knappen **Skicka**.

Det finns ingen registrering för den angivna **installationId** nu. Verifieringen resulterar i svaret ”404 hittades inte” i stället för ett ”401 Ej behörig”-svar. Det här resultatet bekräftar att SAS-token har accepterats.

### <a name="implement-the-installation-service-class"></a>Implementera tjänstklass installation

Bredvid implementerar du våra grundläggande omslutning runt den [installationer REST API](/rest/api/notificationhubs/create-overwrite-installation).  

Lägg till en ny Swift fil med namnet **NotificationRegistrationService.swift** under den **Services** mapp, och Lägg sedan till följande kod till den här filen:

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil

    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }

    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {

        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)

        if let tags = tags {
            deviceInstallation.tags = tags
        }

        if let templates = templates {
            deviceInstallation.templates = templates
        }

        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"

            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"

            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }

            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)

            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
        }

        return (tokenData?.token)!
    }

    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

De nödvändiga informationen tillhandahålls som en del av initieringen. Taggar och mallar som du kan också skickas till den **registrera** funktionen att ingå i den **enhetsinstallation** JSON-nyttolast.  

Den **registrera** anropar funktionen privata funktionerna för att förbereda begäran. När ett svar tas emot slutförs kallas och om registreringen har slutförts.  

Begäran om slutpunkten skapas genom att den **getBaseAddress** funktion. Konstruktion använder parametrarna notification hub *namnområde* och *namn* som angavs under initieringen.  

Den **getSasToken** funktionen kontrollerar om den lagrade token är giltig. Om token är inte giltig funktionen anropas **TokenUtility** att generera en ny token och sedan lagrar dem innan det returneras ett värde.

Slutligen **encodeToJson** konverterar respektive modell-objekt till JSON för användning som en del av frågans brödtext.

### <a name="invoke-the-notification-hubs-rest-api"></a>Anropa REST-API för Notification Hubs

Det sista steget uppdaterar **AppDelegate** att använda **NotificationRegistrationService** att registrera med vår **NotificationHub**.

1. Öppna **AppDelegate.swift** och Lägg till en klassnivåvariabler för att lagra en referens till den **NotificationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. I samma fil, uppdaterar den **didRegisterForRemoteNotificationsWithDeviceToken** funktionen för att initiera den **NotificationRegistrationService** med nödvändiga parametrar och sedan anropa den **registrera** funktion.

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    För att göra det enkelt, ska du använda ett par av skriva ut rapporter för att uppdatera utdatafönstret med resultatet från den **registrera** igen.

1. Nu skapa och köra appen på en fysisk enhet. Du bör se ”Registered” i utdatafönstret.

## <a name="test-the-solution"></a>Testa lösningen

Vår app i det här skedet är registrerad med **NotificationHub** och kan ta emot push-meddelanden. Stoppar felsökningsverktyget och stänger appen om den körs för närvarande i Xcode. Därefter kontrollerar du att den **enhetsinstallation** information är som förväntat och att vår app kan nu få push-meddelanden.  

### <a name="verify-the-device-installation"></a>Verifiera installationen

Nu kan du göra samma begäran som du gjorde tidigare med hjälp av **Postman** för [verifierar SAS-token](#verify-the-sas-token). Om vi antar att SAS-token inte har gått ut, bör nu svaret innehålla installationsinformation som du angav, till exempel mallar och taggar.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

### <a name="send-a-test-notification-azure-portal"></a>Skicka ett testmeddelande (Azure portal)

Det snabbaste sättet att testa att kan du nu ta emot meddelanden är att bläddra till notification hub i Azure portal:

1. I Azure-portalen bläddrar du till den **översikt** fliken på meddelandehubben.

1. Välj **testsändning**, vilket är över den **Essentials** sammanfattning i det övre vänstra hörnet i portalfönstret:

    ![Notification Hubs Essentials skicka sammanfattning av Test-knappen](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Välj **anpassad mall** från den **plattformar** lista.

1. Ange **12345** för den **skicka till Tagguttryck**. Angivna taggen i vår installationen.

1. Du kan också redigera den **meddelande** i JSON-nyttolasten:

    ![Notification Hubs prova att skicka](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Välj **Skicka**. Portalen ska indikera om meddelandet har skickats till enheten:

    ![Skicka resultat för Notification Hubs Test](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Om vi antar att appen inte körs i förgrunden, bör du även se ett meddelande i den **Meddelandecentret** på din enhet. Trycker på meddelandet ska öppna appen och visa aviseringen.

    ![Meddelandet emot exempel](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Skicka ett testmeddelande (e-operatör)

Du kan skicka meddelanden via den [REST API](/rest/api/notificationhubs/) med hjälp av **Postman**, som kan vara det enklaste sättet att testa.

1. Öppna en ny flik i **Postman**.

1. Anger **POST**, och ange följande adress:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Konfigurera begärandehuvuden enligt följande:

   | Nyckel                            | Värde                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Auktorisering                  | <sasToken>                     |
   | ServiceBusNotification-Format  | mall                       |
   | Tags                           | "12345"                        |

1. Konfigurera begäran **BRÖDTEXT** att använda **RAW - JSON (application.json)** med följande JSON-nyttolasten:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Välj den **kod** knapp, vilket är under den **spara** knappen i det övre högra hörnet i fönstret. Begäran bör likna följande exempel:

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. Välj knappen **Skicka**.

Du bör få en lyckad statuskod och ta emot meddelandet på klientenheten.

## <a name="next-steps"></a>Nästa steg
Nu har du en snabb grundläggande iOS-app ansluten till en meddelandehubb via den [REST API](/rest/api/notificationhubs/) och kan skicka och ta emot meddelanden. Mer information finns i följande artiklar:

- [Översikt över Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Notification Hub REST API: er](/rest/api/notificationhubs/)
- [Notification Hubs SDK för backend-åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK på GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrera med serverdelen program](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Registreringshantering](notification-hubs-push-notification-registration-management.md)
- [Arbeta med taggar](notification-hubs-tags-segment-push-message.md) 
- [Arbeta med anpassade mallar](notification-hubs-templates-cross-platform-push-messages.md)
- [Service Bus åtkomstkontroll med signaturer för delad åtkomst](../service-bus-messaging/service-bus-sas.md)
- [Programmatiskt generera SAS-token](/rest/api/eventhub/generate-sas-token)
- [Apple-säkerhet: vanliga krypto](https://developer.apple.com/security/)
- [UNIX-epoktid](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
