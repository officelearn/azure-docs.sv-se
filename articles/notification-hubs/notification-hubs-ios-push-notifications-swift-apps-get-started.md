---
title: Skicka push-meddelanden till Swift iOS-appar som använder Azure Notification Hubs | Microsoft-dokument
description: Läs om hur du skickar meddelanden till Swift iOS-appar som använder Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: a721c519c7a836e20455c6f1887bcfa7b52951f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336644"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>Självstudiekurs: Skicka push-meddelanden till Swift iOS-appar med REST API FÖR Meddelandehubbar

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

I den här självstudien använder du Azure Notification Hubs för att skicka meddelanden till ett Swift-baserat iOS-program med hjälp av [REST API](/rest/api/notificationhubs/). Du skapar också en tom iOS-app som tar emot push-meddelanden med hjälp av [Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Den här självstudien tar dig igenom följande steg:

> [!div class="checklist"]
> * Generera begäran om certifikatsigneringsbegäran.
> * Begär din app för push-meddelanden.
> * Skapa en etableringsprofil för appen.
> * Skapa en meddelandehubb.
> * Konfigurera meddelandehubben med APNs-information.
> * Anslut din iOS-app till en meddelandehubb.
> * Testa lösningen.

Den fullständiga koden för den här självstudien finns [på GitHub](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest).

## <a name="prerequisites"></a>Krav

För att följa med behöver du:

- Så här går du igenom [översikten över Azure Notification Hubs](notification-hubs-push-notification-overview.md) om du inte är bekant med tjänsten.
- Om du vill veta mer om [registreringar och installation](notification-hubs-push-notification-registration-management.md).
- Ett aktivt [Apple-utvecklarkonto](https://developer.apple.com).
- En Mac som kör Xcode tillsammans med ett giltigt utvecklarcertifikat installerat i nyckelringen.
- En fysisk iPhone-enhet som du kan köra och felsöka med, eftersom du inte kan testa push-meddelanden med simulatorn.
- Din fysiska iPhone-enhet som är registrerad på [Apple-portalen](https://developer.apple.com) och som är kopplad till ditt certifikat.
- En [Azure-prenumeration](https://portal.azure.com) där du kan skapa och hantera resurser.

Även om du inte har någon tidigare erfarenhet av iOS-utveckling bör du kunna följa stegen för att skapa det här exemplet med första principer. Du kan dock dra nytta av att du känner till följande begrepp:

- Bygga iOS-appar med Xcode och Swift.
- Konfigurera en [Azure Notification-hubb](notification-hubs-ios-apple-push-notification-apns-get-started.md) för iOS.
- [Apple Developer Portal](https://developer.apple.com) och [Azure-portalen](https://portal.azure.com).

> [!NOTE]
> Meddelandehubben konfigureras endast för att använda **begränsat** autentiseringsläge. Du bör inte använda det här autentiseringsläget för produktionsarbetsbelastningar.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Ansluta din iOS-app till en meddelandehubb

I det här avsnittet ska du skapa iOS-appen som ansluter till meddelandehubben.  

### <a name="create-an-ios-project"></a>Skapa ett iOS-projekt

1. Skapa ett nytt iOS-projekt i Xcode och välj mallen **Program enkel vy**.

1. När du ställer in alternativen för det nya projektet:

   1. Ange **produktnamn** (PushDemo) och **organisationsidentifierare** (`com.<organization>`) som du använde när du angav **Paketidentifierare** i Apple Developer Portal.

   1. Välj det **team** som **app-ID:et** har konfigurerats för.

   1. Ange **språket** till **Swift**.

   1. Välj **Nästa**.

1. Skapa en ny mapp med namnet **SupportingFiles**.

1. Skapa en ny p-listfil med namnet **devsettings.plist** i mappen **SupportingFiles.** Var noga med att lägga till den här mappen i **din gitignore-fil** så att den inte är engagerad när du arbetar med en git-repo. I en produktionsapp anger du förmodligen villkorligt dessa hemligheter som en del av en automatiserad byggprocess. Sådana inställningar omfattas inte av den här genomgången.

1. Uppdatera **devsettings.plist** så att de innehåller följande konfigurationsposter med hjälp av dina egna värden från meddelandehubben som du har etablerat:

   | Nyckel                            | Typ                     | Värde                     |
   |--------------------------------| -------------------------| --------------------------|
   | meddelandeHubKey             | String                   | \<hubKey>                  |
   | meddelandeHubKeyName         | String                   | \<hubKeyName>              |
   | meddelandeHubName            | String                   | \<hubName>                 |
   | meddelandeHubNamespace       | String                   | \<hubNamespace>            |

   Du kan hitta de värden som krävs genom att navigera till meddelandehubbresursen i Azure-portalen. I synnerhet **är värdena notificationHubName** och **notificationHubNamespace** i det övre högra hörnet i **Essentials-sammanfattningen** på **översiktssidan.**

   ![Sammanfattning av Sammanfattning av Notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Du kan också hitta **värdena notificationHubKeyName** och **notificationHubKey** genom att navigera till `DefaultFullSharedAccessSignature` **åtkomstprinciper** och välja respektive **åtkomstprincip**, till exempel . Därefter kopierar du **Primary Connection String** värdet som föregås av `SharedAccessKeyName=` `notificationHubKeyName` för och värdet som `SharedAccessKey=` föregås av för `notificationHubKey`.

   Anslutningssträngen ska vara i följande format:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Om du vill `DefaultFullSharedAccessSignature` hålla det enkelt anger du så att du kan använda token för att skicka meddelanden. I praktiken `DefaultListenSharedAccessSignature` skulle vara ett bättre val för situationer där du bara vill ta emot meddelanden.

1. Markera **projektnamnet under** **Project Navigator**och välj sedan fliken **Allmänt.**

1. Hitta **identitet** och ange sedan värdet **Buntidentifierare** så att det matchar `com.<organization>.PushDemo`, vilket är det värde som används för **app-ID** från ett tidigare steg.

1. Hitta **signering &-funktioner**och välj sedan lämpligt **team** för ditt **Apple-utvecklarkonto**. **Gruppvärdet** ska matcha det under vilket du skapade dina certifikat och profiler.

1. Xcode ska automatiskt dra ned lämpligt **etableringsprofilvärde** baserat på **buntidentifieraren**. Om du inte ser det nya **etableringsprofilvärdet** kan du prova att uppdatera profilerna för **signeringsidentiteten** genom att välja **Xcode** > **Preferences-konto** > **Account** och välj sedan knappen **Hämta manuella profiler** för att hämta profilerna.

1. Klicka på knappen **+ Kapacitet** på fliken **Signering &** och dubbeltryck på **push-meddelanden** från listan för att se till att **push-meddelanden** är aktiverade.

1. Öppna filen **AppDelegate.swift** för att implementera **UNUserNotificationCenterDelegate-protokollet** och lägga till följande kod högst upp i klassen:

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
        
        ...
    }
    ```

    Du kommer att använda dessa medlemmar senare. Specifikt ska du använda **taggarna** medlem som en del av registreringen med hjälp av en **anpassad mall**. Mer information om taggar finns i [Taggar för registreringar](notification-hubs-tags-segment-push-message.md) och [mallregistreringar](notification-hubs-templates-cross-platform-push-messages.md).

1. I samma fil lägger du till följande kod i funktionen **didFinishLaunchingWithOptions:**

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

    Den här koden hämtar inställningsvärdena från **devsettings.plist**, anger klassen **AppDelegate** som **UNUserNotificationCenter-delegat,** begär auktorisering för push-meddelanden och anropar sedan **registerForRemoteNotifications**.

    För att hålla det enkelt stöder koden *iOS 10 och senare bara*. Du kan lägga till stöd för tidigare OS-versioner genom att villkorligt använda respektive API:er och metoder som vanligt.

1. Lägg till följande funktioner i samma fil:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Koden använder **värdena installationId** och **pushChannel** för att registrera sig med meddelandehubben. I det här fallet använder du **UIDevice.current.identifierForVendor** för att ange ett unikt värde för att identifiera enheten och sedan formatera **enhetenToken** för att ange önskat **pushChannel-värde.** Funktionen **showAlert** finns helt enkelt för att visa vissa meddelandetexter för demonstrationsändamål.

1. Fortfarande i **Filen AppDelegate.swift,** lägg till **willPresent** och **didReceive** funktioner till **UNUserNotificationCenterDelegate**. Dessa funktioner visar en avisering när de får ett meddelande om att en app körs i antingen förgrunden eller bakgrunden.

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

1. Lägg till utskriftssatser längst ned i funktionen **didRegisterForRemoteNotificationsMedDeviceToken** för att kontrollera att **installationsId** och **pushChannel** tilldelas värden.

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. Skapa mapparna **Modeller,** **Tjänster**och **Verktyg** för de grundläggande komponenter som du lägger till i projektet senare.

1. Kontrollera att projektet bygger och körs på en fysisk enhet. Push-meddelanden kan inte testas med hjälp av simulatorn.

### <a name="create-models"></a>Skapa modeller

I det här steget ska du skapa en uppsättning modeller som representerar API-nyttolaster för [meddelandehubben](/rest/api/notificationhubs/) rest och för att lagra nödvändiga SAS-tokendata (Shared Access Signature).

1. Lägg till en ny Swift-fil som heter **PushTemplate.swift** i **mappen Modeller.** Den här modellen innehåller en struktur som representerar **BODY för** en enskild mall som en del av nyttolasten **för enhetsinstallation.**

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Lägg till en ny Swift-fil som heter **DeviceInstallation.swift** i **mappen Modeller.** Den här filen definierar en struktur som representerar nyttolasten för att skapa eller uppdatera en **enhetsinstallation**. Lägg till följande kod i filen:

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

1. Lägg till en ny Swift-fil som heter **TokenData.swift** i **mappen Modeller.** Den här modellen används för att lagra en SAS-token tillsammans med dess förfallodatum.

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

Meddelandehubbar använder samma säkerhetsinfrastruktur som Azure Service Bus. Om du vill anropa REST API måste du [programmässigt generera en SAS-token](/rest/api/eventhub/generate-sas-token) som kan användas i **auktoriseringshuvudet** för begäran.  

Den resulterande token kommer att vara i följande format:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Själva processen omfattar samma sex huvudsteg:  

1. Beräkna utgången i [UNIX Epoch](https://en.wikipedia.org/wiki/Unix_time) tidsformat, vilket innebär att antalet sekunder förflutit sedan midnatt Coordinated Universal Time, 1 januari 1970.
1. Formatera **ResourceUrl** som representerar den resurs du försöker komma åt så att den är procentkodad och gemen. **ResourceUrl** har formuläret `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Förbereda **StringToSign**, som är `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`formaterad som .
1. Datoranvändning och Base64-kodning av **signaturen** med hMAC-SHA256-hashen i **StringToSign-värdet.** Hash-värdet används med **nyckeldelen** av **anslutningssträngen** för respektive **auktoriseringsregel**.
1. Formatera Base64-kodad **signatur** så att den är procentkodad.
1. Konstruera token i det förväntade formatet med hjälp av värdena **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**och **UrlEncodedResourceUrl.**

Mer information om signature för delad åtkomst i Azure Service Bus finns i [dokumentationen](../service-bus-messaging/service-bus-sas.md) till Azure Service Bus för en mer ingående översikt över signaturen för delad åtkomst och hur Azure Service Bus- och Meddelandehubbar använder den.

I detta Swift-exempel kommer du att använda Apples **CommonCrypto-bibliotek** med öppen källkod för att hjälpa till med hashing av signaturen. Eftersom det är ett C-bibliotek, är det inte tillgängligt i Swift ur lådan. Du kan göra biblioteket tillgängligt med hjälp av ett överbryggningshuvud.

Så här lägger du till och konfigurerar övergångshuvudet:

1. Välj **Fil** > **ny** > **filfil** > **Header File**i Xcode . Namnge rubrikfilen **BridgingHeader.h**.

1. Redigera filen för att importera **CommonHMAC.h:**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Uppdatera målets **bygginställningar** för att referera till brygghuvudet:

   1. Tryck på **PushDemo-projektet** och bläddra ned till avsnittet **Snabb kompilator.**

   1. Kontrollera att alternativet **Installera mål-C-kompatibilitetshuvud** är inställt på **Ja**.

   1. Ange filsökvägen `'<ProjectName>/BridgingHeader.h'` till alternativet **Mål-C-brygghuvud.** Det här är filsökvägen till vårt övergångshuvud.

   Om du inte hittar dessa alternativ kontrollerar du att vyn **Alla** är markerad i stället för **Basic** eller **Anpassad**.

   Det finns många bibliotek med öppen källkod med öppen källkod som kan göra det lite enklare att använda **CommonCrypto.** Diskussionen om sådana bibliotek ligger dock utanför den här artikelns räckvidd.

1. Lägg till en ny Swift-fil med namnet **TokenUtility.swift** i mappen **Verktyg** och lägg till följande kod:

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

   Det här verktyget kapslar in logiken som ansvarar för att generera SAS-token.

   Som beskrivits tidigare, **den getSasToken** funktionen orkestrerar hög nivå steg som krävs för att förbereda token. Funktionen anropas av installationstjänsten senare i den här självstudien.

   De andra två funktionerna anropas av **funktionen getSasToken:** **sha256HMac** för beräkning av signaturen och **urlEncodedString** för kodning av den associerade URL-strängen. **Funktionen urlEncodedString** krävs eftersom det inte är möjligt att uppnå den nödvändiga utdata med hjälp av den inbyggda **addPercentEncoding-funktionen.**

   [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) är ett utmärkt exempel på hur du närmar dig dessa åtgärder i Objective-C. Mer information om Azure Service Bus SAS-token finns i [Azure Service Bus-dokumentationen](../service-bus-messaging/service-bus-sas.md).

1. I **AppDelegate.swift**lägger du till följande kod i funktionen *didRegisterForRemoteNotificationsWithDeviceToken* för att verifiera att **tokenUtility.getSasToken** genererar en giltig token
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    Var noga med att ersätta platshållarvärdena i **basadresssträngen** med din egen

### <a name="verify-the-sas-token"></a>Verifiera SAS-token

Innan du implementerar installationstjänsten i klienten kontrollerar du att vår app genererar SAS-token korrekt med hjälp av ditt HTTP-val. I den här guiden kommer vårt verktyg val vara **Postman**.

Anteckna de **installationsid-** och **tokenvärden** som genereras av appen.

Följ dessa steg **installations** för att anropa installations-API:

1. Öppna en ny flik i **Postman.**

1. Ange begäran till **GET** och ange följande adress:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Konfigurera förfråningsrubrikerna enligt följande:

   | Nyckel           | Värde            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Auktorisering | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. Välj knappen **Kod** som visas längst upp till höger under knappen **Spara.** Begäran ska se ut ungefär som i följande exempel:

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

Det finns ingen registrering för den angivna **installationenId** vid denna tidpunkt. Verifieringen bör resultera i ett "404 Hittades inte"-svar i stället för ett "401 Obehörigt" svar. Detta resultat bör bekräfta att SAS-token har accepterats.

### <a name="implement-the-installation-service-class"></a>Implementera installationstjänstklassen

Därefter implementerar du vårt grundläggande omslag runt [INSTALLATIONS REST API.](/rest/api/notificationhubs/create-overwrite-installation)  

Lägg till en ny Swift-fil med namnet **NotificationRegistrationService.swift** under mappen **Tjänster** och lägg sedan till följande kod i den här filen:

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
    private var tokenExpiryDate : Date? = nil
    
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
                        completion(err == nil && 
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {
            
            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)
            
            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
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

De nödvändiga detaljerna tillhandahålls som en del av initieringen. Taggar och mallar skickas eventuellt till **registerfunktionen** för att ingå i **JSON-nyttolasten för enhetsinstallation.**  

**Registerfunktionen** anropar de andra privata funktionerna för att förbereda begäran. När ett svar har tagits emot anropas slutförandet och anger om registreringen lyckades.  

Slutpunkten för begäran är konstruerad av funktionen **getBaseAddress.** Konstruktionen använder *namnområdet* för meddelandehubbens parametrar och *namn* som angavs under initieringen.  

Funktionen **getSasToken** kontrollerar om den lagrade token är giltig. Om token inte är giltig anropar funktionen **TokenUtility** för att generera en ny token och lagrar den sedan innan du returnerar ett värde.

Slutligen konverterar **kodtojson** respektive modellobjekt till JSON för användning som en del av begärandetexten.

### <a name="invoke-the-notification-hubs-rest-api"></a>Anropa REST API FÖR meddelandehubbar

Det sista steget är att uppdatera **AppDelegate** för att använda **NotificationRegistrationService** för att registrera dig hos vår **NotificationHub**.

1. Öppna **AppDelegate.swift** och lägg till variabler på klassnivå för att lagra en referens till **NoficiationRegistrationService** och den generiska **PushTemplate:**

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. I samma fil uppdaterar du funktionen **didRegisterForRemoteNotificationsMedDeviceToken** för att initiera **NotificationRegistrationService** med de nödvändiga parametrarna och anropa sedan **registerfunktionen.**

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

    För att hålla det enkelt, du kommer att använda ett par utskriftssatser **register** för att uppdatera utdatafönstret med resultatet av registeråtgärden.

1. Nu bygga och köra appen på en fysisk enhet. Du bör se "Registrerad" i utdatafönstret.

## <a name="test-the-solution"></a>Testa lösningen

Vår app i detta skede är registrerad hos **NotificationHub** och kan ta emot push-meddelanden. I Xcode stoppar du felsökningsprogrammet och stänger appen om den körs för tillfället. Kontrollera sedan att informationen om **enhetsinstallation** är som förväntat och att vår app nu kan ta emot push-meddelanden.  

### <a name="verify-the-device-installation"></a>Verifiera enhetsinstallationen

Du kan nu göra samma begäran som du gjorde tidigare genom att använda **Postman** för [att verifiera SAS-token](#verify-the-sas-token). Förutsatt att SAS-token inte har upphört att gälla bör svaret nu innehålla de installationsdetaljer som du angav, till exempel mallar och taggar.

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

Om din tidigare **SAS-token** har upphört att gälla kan du lägga till en **brytpunkt** på **rad 24** i klassen **TokenUtility** för att hämta en ny **SAS-token** och uppdatera **auktoriseringshuvudet** med det nya värdet.

### <a name="send-a-test-notification-azure-portal"></a>Skicka ett testmeddelande (Azure-portal)

Det snabbaste sättet att testa att du nu kan få meddelanden är att bläddra till meddelandehubben i Azure-portalen:

1. I Azure-portalen bläddrar du till fliken **Översikt** på meddelandehubben.

1. Välj **Testa skicka**, som ligger ovanför **Essentials-sammanfattningen** längst upp till vänster i portalfönstret:

    ![Knappen Sammanfattningstest för sammanfattningstest för meddelandehubbar](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Välj **Anpassad mall** i listan **Plattformar.**

1. Ange **12345** för **uttrycket Skicka till tagg**. Du hade tidigare angett den här taggen i vår installation.

1. Du kan också redigera **meddelandet** i JSON-nyttolasten:

    ![Test skicka testsändning av meddelandehubbar](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Välj **Skicka**. Portalen bör ange om meddelandet har skickats till enheten:

    ![Testresultat för meddelandehubbar](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Om du antar att appen inte körs i förgrunden bör du också se ett meddelande i **Meddelandecentret** på enheten. Om du trycker på meddelandet ska appen öppnas och aviseringen visas.

    ![Exempel på mottaget meddelande](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Skicka ett testmeddelande (E-postoperatör)

Du kan skicka meddelanden via [REST API](/rest/api/notificationhubs/) med hjälp av **Postman**, vilket kan vara ett bekvämare sätt att testa.

1. Öppna en ny flik i **Postman**.

1. Ange begäran till **POST**och ange följande adress:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Konfigurera förfråningsrubrikerna enligt följande:

   | Nyckel                            | Värde                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | ansökan/json;charset=utf-8 |
   | Auktorisering                  | \<sasToken>                     |
   | ServiceBusNotification-Format  | mall                       |
   | Taggar                           | "12345"                        |

1. Konfigurera begäran **BODY** för att använda **RAW - JSON (application.json)** med följande JSON nyttolast:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Välj **knappen Kod,** som finns under knappen **Spara** längst upp till höger i fönstret. Begäran ska se ut ungefär som i följande exempel:

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

Du bör få en **201 Skapad** statuskod för lyckade uppgifter och få meddelandet på klientenheten..

## <a name="next-steps"></a>Nästa steg
Du har nu en grundläggande iOS Swift-app ansluten till en meddelandehubb via [REST API](/rest/api/notificationhubs/) och kan skicka och ta emot meddelanden. Mer information finns i följande artiklar:

- [Översikt över Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [REST-API:er för meddelandehubbar](/rest/api/notificationhubs/)
- [Meddelandehubbar SDK för backend-åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Meddelandehubbar SDK på GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrera dig med programback end](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Registreringshantering](notification-hubs-push-notification-registration-management.md)
- [Arbeta med taggar](notification-hubs-tags-segment-push-message.md) 
- [Arbeta med anpassade mallar](notification-hubs-templates-cross-platform-push-messages.md)
- [Service Bus-åtkomstkontroll med signaturer för delad åtkomst](../service-bus-messaging/service-bus-sas.md)
- [Skapa SAS-token program som programmässigt](/rest/api/eventhub/generate-sas-token)
- [Apples säkerhet: gemensam krypto](https://developer.apple.com/security/)
- [UNIX-epoktid](https://en.wikipedia.org/wiki/Unix_time)
- [Hmac](https://en.wikipedia.org/wiki/HMAC)
