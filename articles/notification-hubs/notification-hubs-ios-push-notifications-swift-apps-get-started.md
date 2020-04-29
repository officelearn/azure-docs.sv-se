---
title: Skicka push-meddelanden till SWIFT iOS-appar som använder Azure Notification Hubs | Microsoft Docs
description: Lär dig att skicka meddelanden till SWIFT iOS-appar som använder Azure Notification Hubs.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336644"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>Självstudie: skicka push-meddelanden till SWIFT iOS-appar med hjälp av Notification Hubs REST API

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

I den här självstudien använder du Azure Notification Hubs för att skicka meddelanden till ett SWIFT-baserat iOS-program med hjälp av [REST API](/rest/api/notificationhubs/). Du skapar också en tom iOS-app som tar emot push-meddelanden med hjälp av [Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Den här självstudien tar dig igenom följande steg:

> [!div class="checklist"]
> * Generera filen för certifikat signerings förfrågan.
> * Begär din app för push-meddelanden.
> * Skapa en etablerings profil för appen.
> * Skapa en meddelandehubb.
> * Konfigurera Notification Hub med APN-information.
> * Anslut din iOS-app till en Notification Hub.
> * Testa lösningen.

Du hittar den fullständiga koden för den här självstudien [på GitHub](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest).

## <a name="prerequisites"></a>Krav

För att följa med måste du:

- För att gå igenom [Azure Notification Hubs-översikt](notification-hubs-push-notification-overview.md) om du inte är bekant med tjänsten.
- Läs mer om [registreringar och installation](notification-hubs-push-notification-registration-management.md).
- Ett aktivt [Apple Developer-konto](https://developer.apple.com).
- En Mac som kör Xcode tillsammans med ett giltigt utvecklarresurser installerat i din nyckel Ring.
- En fysisk iPhone-enhet som du kan köra och felsöka med, eftersom du inte kan testa push-meddelanden med simulatorn.
- Din fysiska iPhone-enhet som registrerats i [Apples Portal](https://developer.apple.com) och som är kopplad till ditt certifikat.
- En [Azure-prenumeration](https://portal.azure.com) där du kan skapa och hantera resurser.

Även om du inte har någon tidigare erfarenhet av iOS-utveckling bör du följa stegen för att skapa det här exemplet för första principer. Du kommer dock att ha nytta av att du är bekant med följande begrepp:

- Skapa iOS-appar med Xcode och Swift.
- Konfigurera en [Azure Notification Hub](notification-hubs-ios-apple-push-notification-apns-get-started.md) för iOS.
- [Apple Developer-portalen](https://developer.apple.com) och [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Notification Hub konfigureras för att endast använda **begränsat** läge för autentisering. Du bör inte använda detta autentiseringsläge för produktions arbets belastningar.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Ansluta din iOS-app till en Notification Hub

I det här avsnittet ska du bygga den iOS-app som ska ansluta till Notification Hub.  

### <a name="create-an-ios-project"></a>Skapa ett iOS-projekt

1. Skapa ett nytt iOS-projekt i Xcode och välj mallen **Program enkel vy**.

1. När du anger alternativ för det nya projektet:

   1. Ange det **produkt namn** (PushDemo) och **organisations** -`com.<organization>`ID () som du använde när du angav **samlings-ID** i Apple Developer-portalen.

   1. Välj det **team** som **app-ID: t** har kon figurer ATS för.

   1. Ange **språket** till **Swift**.

   1. Välj **Nästa**.

1. Skapa en ny mapp med namnet **SupportingFiles**.

1. Skapa en ny p-list-fil med namnet **devsettings. plist** i mappen **SupportingFiles** . Se till att lägga till den här mappen till din **gitignore** -fil så att den inte har allokerats när du arbetar med en git-lagrings platsen. I en app för produktion skulle du förmodligen villkorligt ange dessa hemligheter som en del i en automatiserad versions process. Sådana inställningar beskrivs inte i den här genom gången.

1. Uppdatera **devsettings. plist** för att inkludera följande konfigurations poster genom att använda dina egna värden från den meddelande hubb som du har allokerat:

   | Nyckel                            | Typ                     | Värde                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | Sträng                   | \<hubKey>                  |
   | notificationHubKeyName         | Sträng                   | \<hubKeyName>              |
   | notificationHubName            | Sträng                   | \<hubName>                 |
   | notificationHubNamespace       | Sträng                   | \<hubNamespace>            |

   Du kan hitta de värden som krävs genom att gå till resursen Notification Hub i Azure Portal. I synnerhet visas värdena **notificationHubName** och **notificationHubNamespace** i det övre högra hörnet **av sammanfattnings** sammanfattningen på sidan **Översikt** .

   ![Sammanfattning av Notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Du kan också hitta **notificationHubKeyName** -och **notificationHubKey** -värden genom att gå till **principer** och välja respektive **åtkomst princip**, till exempel. `DefaultFullSharedAccessSignature` Efter det kopierar du från den **primära anslutnings strängen** värdet `SharedAccessKeyName=` som föregås `notificationHubKeyName` av och värdet som är prefixet `SharedAccessKey=` för. `notificationHubKey`

   Anslutnings strängen måste ha följande format:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   För att det ska vara enkelt `DefaultFullSharedAccessSignature` , anger du så att du kan använda token för att skicka meddelanden. I praktiken är `DefaultListenSharedAccessSignature` det ett bättre alternativ för situationer där du bara vill ta emot meddelanden.

1. Under **projekt navigering**väljer du **projekt namnet** och väljer sedan fliken **Allmänt** .

1. Hitta **identitet** och ange sedan värdet för **paket-ID** : t så `com.<organization>.PushDemo`att det matchar, vilket är det värde som används för **app-ID: t** från föregående steg.

1. Hitta **signerings & funktioner**och välj sedan lämpligt **team** för ditt **Apple Developer-konto**. **Teamets** värde ska matcha det som du skapade med dina certifikat och profiler.

1. Xcode bör automatiskt hämta rätt **etablerings profil** värde baserat på **paket-ID**. Om du inte ser det nya **etablerings profil** svärdet kan du försöka med att uppdatera profilerna för **signerings identiteten** genom att välja**kontot** för **Xcode** > **Inställningar** > och sedan hämta profilerna genom att välja knappen **Hämta manuella profiler** .

1. Klicka på knappen **+ funktion** fortfarande på fliken **signerings & funktioner** och dubbelknacka på push- **meddelanden** i listan för att säkerställa att **push-meddelanden** är aktiverade.

1. Öppna filen **AppDelegate. SWIFT** för att implementera **UNUserNotificationCenterDelegate** -protokollet och Lägg till följande kod överst i klassen:

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

    Du kommer att använda dessa medlemmar senare. Mer specifikt använder du **taggarna** medlem som en del av registreringen med hjälp av en **anpassad mall**. Mer information om taggar finns i [taggar för registreringar](notification-hubs-tags-segment-push-message.md) och [mall registreringar](notification-hubs-templates-cross-platform-push-messages.md).

1. I samma fil lägger du till följande kod i **didFinishLaunchingWithOptions** -funktionen:

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

    Den här koden hämtar inställnings värden från **devsettings. plist**, ställer in **AppDelegate** -klassen som **UNUserNotificationCenter** -delegaten, begär auktorisering för push-meddelanden och anropar sedan **registerForRemoteNotifications**.

    För att det ska vara enkelt, stöder koden *endast iOS 10 och senare*. Du kan lägga till stöd för tidigare OS-versioner genom att villkorligt använda respektive API och metoder som vanligt.

1. I samma fil lägger du till följande funktioner:

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

    I koden används **installationId** -och **pushChannel** -värden för att registrera med Notification Hub. I det här fallet använder du **UIDevice. Current. identifierForVendor** för att ange ett unikt värde för att identifiera enheten och sedan formatera **deviceToken** för att tillhandahålla det önskade **pushChannel** -värdet. Funktionen **showAlert** finns bara för att visa viss meddelande text i demonstrations syfte.

1. Lägg fortfarande till **willPresent** -och **didReceive** -funktionerna till **UNUserNotificationCenterDelegate**i filen **AppDelegate. SWIFT** . Dessa funktioner visar en avisering när de får ett meddelande om att en app körs antingen i förgrunden eller i bakgrunden.

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

1. Lägg till Print-instruktioner längst ned i **didRegisterForRemoteNotificationsWithDeviceToken** -funktionen för att kontrol lera att **installationId** och **pushChannel** tilldelas värden.

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. Skapa mapparna **modeller**, **tjänster**och **verktyg** för de grundläggande komponenterna som du kommer att lägga till i projektet senare.

1. Kontrol lera att projektet bygger och körs på en fysisk enhet. Det går inte att testa push-meddelanden med simulatorn.

### <a name="create-models"></a>Skapa modeller

I det här steget skapar du en uppsättning modeller som representerar [Notification Hubs REST API](/rest/api/notificationhubs/) nytto laster och för att lagra nödvändiga data för signatur för delad åtkomst (SAS).

1. Lägg till en ny Swift-fil med namnet **PushTemplate. SWIFT** i mappen **modeller** . Den här modellen innehåller en struktur som representerar **bröd texten** i en enskild mall som en del av **DeviceInstallation** -nyttolasten.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Lägg till en ny Swift-fil med namnet **DeviceInstallation. SWIFT** i mappen **modeller** . Den här filen definierar en STRUCT som representerar nytto lasten för att skapa eller uppdatera en **enhets installation**. Lägg till följande kod i filen:

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

1. Lägg till en ny Swift-fil med namnet **TokenData. SWIFT** i mappen **modeller** . Den här modellen kommer att användas för att lagra en SAS-token tillsammans med dess förfallo datum.

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

Notification Hubs använda samma säkerhets infrastruktur som Azure Service Bus. Om du vill anropa REST API måste du [skapa en SAS-token via programmering](/rest/api/eventhub/generate-sas-token) som kan användas i begärans **Authorization** -huvud.  

Den resulterande token kommer att ha följande format:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Själva processen omfattar samma sex viktiga steg:  

1. Beräknar förfallo [tiden i UNIX](https://en.wikipedia.org/wiki/Unix_time) -värdes format, vilket innebär hur många sekunder som har förflutit sedan midnatt Coordinated Universal Time, 1 januari 1970.
1. Formatera **ResourceUrl** som representerar resursen som du försöker få åtkomst till, så att den är i procent kodad och gemen. **ResourceUrl** har formuläret `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Förbereder **StringToSign**, som är formaterad som `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Data bearbetning och base64-kodning av **signaturen** med hjälp av HMAC-SHA256 hash för **StringToSign** -värdet. Hash-värdet används med **nyckel** delen av **anslutnings strängen** för respektive **auktoriseringsregel**.
1. Formaterar den base64-kodade **signaturen** så att den är procent kodad.
1. Konstruera token i förväntat format med hjälp av värdena **UrlEncodedSignature**, **ExpiryEpoch**, **nyckel namn**och **UrlEncodedResourceUrl** .

Se [Azure Service Bus-dokumentationen](../service-bus-messaging/service-bus-sas.md) för en mer grundlig översikt över signaturen för delad åtkomst och hur Azure Service Bus och Notification Hubs använda den.

I det här Swift-exemplet ska du använda Apples **CommonCrypto** -bibliotek med öppen källkod för att hjälpa till med hashing av signaturen. Som det är ett C-bibliotek går det inte att komma åt i Swift-rutan. Du kan göra biblioteket tillgängligt genom att använda ett bryggnings huvud.

Lägga till och konfigurera bryggnings huvudet:

1. I Xcode väljer du **Arkiv** > **ny** > **File** > fil**huvud fil**. Namnge rubrik filen **BridgingHeader. h**.

1. Redigera filen för att importera **CommonHMAC. h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Uppdatera målets **build-inställningar** så att de refererar till interimskontot för överbryggning:

   1. Tryck på **PushDemo** -projektet och rulla ned till avsnittet **Swift-kompilator** .

   1. Se till att alternativet **Installera mål-C-kompatibilitetsläge** är inställt på **Ja**.

   1. Ange fil Sök vägen `'<ProjectName>/BridgingHeader.h'` till alternativet **mål-C-bryggning-rubrik** . Detta är fil Sök vägen till vårt bro huvud.

   Om du inte kan hitta de här alternativen måste du kontrol lera att du har valt vyn **alla** och inte **grundläggande** eller **anpassad**.

   Det finns många tredjeparts omslutnings bibliotek med öppen källkod som kan göra att det blir enklare att använda **CommonCrypto** . Diskussion av sådana bibliotek ligger dock utanför den här artikelns omfattning.

1. Lägg till en ny Swift-fil med namnet **TokenUtility. SWIFT** i mappen **verktyg** och Lägg till följande kod:

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

   Det här verktyget kapslar in logiken som ansvarar för att skapa SAS-token.

   Som beskrivs ovan dirigerar funktionen **getSasToken** de avancerade steg som krävs för att förbereda token. Funktionen kommer att anropas av installations tjänsten senare i den här självstudien.

   De andra två funktionerna anropas av funktionen **getSasToken** : **sha256HMac** för att beräkna signaturen och **urlEncodedString** för kodning av den associerade URL-strängen. Funktionen **urlEncodedString** krävs eftersom det inte går att uppnå de utdata som krävs med hjälp av den inbyggda **addingPercentEncoding** -funktionen.

   [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) är ett utmärkt exempel på hur du kan närma dig dessa åtgärder i mål-C. Mer information om Azure Service Bus SAS-token finns i Azure Service Bus- [dokumentationen](../service-bus-messaging/service-bus-sas.md).

1. I **AppDelegate. SWIFT**lägger du till följande kod i *didRegisterForRemoteNotificationsWithDeviceToken* -funktionen för att kontrol lera att **TokenUtility. getSasToken** genererar en giltig token
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    Se till att ersätta plats hållarnas värden i en **strängare** sträng med egna

### <a name="verify-the-sas-token"></a>Verifiera SAS-token

Innan du implementerar installations tjänsten i klienten kontrollerar du att den här appen genererar SAS-token korrekt genom att använda önskat HTTP-verktyg. I den här självstudien får du det verktyg som du väljer **Postman**.

Anteckna de **installationId** -och **token** -värden som genereras av appen.

Följ de här stegen för att anropa API: erna för **installation** :

1. Öppna en ny flik i **Postman**.

1. Ange begäran för att **Hämta** och ange följande adress:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Konfigurera begärandehuvuden enligt följande:

   | Nyckel           | Värde            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Auktorisering | \<sasToken>       |
   | x-MS-version  | 2015-01          |

1. Välj den **kod** knapp som visas längst upp till höger under knappen **Spara** . Begäran bör se ut ungefär som i följande exempel:

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

Det finns ingen registrering för den angivna **installationId** just nu. Verifieringen bör resultera i att ett "404" inte hittas "-svar i stället för ett" 401 oauktoriserat "-svar. Det här resultatet bör bekräfta att SAS-token har accepterats.

### <a name="implement-the-installation-service-class"></a>Implementera installations tjänst klassen

Härnäst ska du implementera den grundläggande omslutningen kring [installationerna REST API](/rest/api/notificationhubs/create-overwrite-installation).  

Lägg till en ny Swift-fil med namnet **NotificationRegistrationService. SWIFT** under mappen **tjänster** och Lägg sedan till följande kod i filen:

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

Den information som krävs tillhandahålls som en del av initieringen. Taggar och mallar skickas alternativt till **register** -funktionen för att utgöra en del av JSON-nyttolasten för **enhets installation** .  

Funktionen **register** anropar de andra privata funktionerna för att förbereda begäran. När ett svar har tagits emot anropas slut för ande och indikerar om registreringen lyckades.  

Begär ande slut punkten konstrueras av **getBaseAddress** -funktionen. Bygget använder Notification Hub-parametrarna *namespace* och *Name* som angavs under initieringen.  

Funktionen **getSasToken** kontrollerar om den aktuella lagrade token är giltig. Om token inte är giltig anropar funktionen **TokenUtility** för att generera en ny token och lagrar den innan ett värde returneras.

Slutligen konverterar **encodeToJson** respektive modell objekt till JSON för användning som en del av begär ande texten.

### <a name="invoke-the-notification-hubs-rest-api"></a>Anropa Notification Hubs REST API

Det sista steget är att uppdatera **AppDelegate** till att använda **NotificationRegistrationService** för att registrera dig hos vår **NotificationHub**.

1. Öppna **AppDelegate. SWIFT** och Lägg till variabler på klass nivå för att lagra en referens till **NoficiationRegistrationService** och den allmänna **PushTemplate**:

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. I samma fil uppdaterar du **didRegisterForRemoteNotificationsWithDeviceToken** -funktionen för att initiera **NotificationRegistrationService** med nödvändiga parametrar och anropa sedan funktionen **Registrera** .

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

    För att det ska vara enkelt, kommer du att använda ett par skrivar instruktioner för att uppdatera fönstret utdata med resultatet av **register** åtgärden.

1. Nu ska du skapa och köra appen på en fysisk enhet. Du bör se "registrerad" i fönstret utdata.

## <a name="test-the-solution"></a>Testa lösningen

Vår app i det här skedet registreras med **NotificationHub** och kan ta emot push-meddelanden. Stoppa fel söknings programmet i Xcode och Stäng appen om den körs för närvarande. Kontrol lera sedan att **enhets installations** informationen är som förväntat och att vår app nu kan ta emot push-meddelanden.  

### <a name="verify-the-device-installation"></a>Verifiera enhets installationen

Du kan nu göra samma begäran som du tidigare har använt **Postman** för [att verifiera SAS-token](#verify-the-sas-token). Om du antar att SAS-token inte har gått ut bör du nu ta med den installations information som du har angett, till exempel mallarna och taggarna.

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

Om din tidigare **SAS-token** har upphört att gälla kan du lägga till en **Bryt punkt** på **rad 24** i **TokenUtility** -klassen för att hämta en ny **SAS-token** och uppdatera **Authorization** -huvudet med det nya värdet.

### <a name="send-a-test-notification-azure-portal"></a>Skicka ett test meddelande (Azure Portal)

Det snabbaste sättet att testa att du nu kan ta emot meddelanden är att bläddra till Notification Hub i Azure Portal:

1. I Azure Portal bläddrar du till fliken **Översikt** i Notification Hub.

1. Välj **testa att skicka**, som är ovanför **Essentials** sammanfattnings sammanfattningen längst upp till vänster i Portal fönstret:

    ![Sammanfattnings test för Notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Välj **anpassad mall** i listan **plattformar** .

1. Ange **12345** som **uttryck för skicka till-tagg**. Du har angett den här taggen tidigare i vår installation.

1. Du kan också redigera **meddelandet** i JSON-nyttolasten:

    ![Notification Hubs skicka test](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Välj **Skicka**. Portalen ska indikera om meddelandet har skickats till enheten:

    ![Notification Hubs skicka test resultat](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Förutsatt att appen inte körs i förgrunden bör du även se ett meddelande i **meddelande centret** på din enhet. Om du trycker på meddelandet bör du öppna appen och visa aviseringen.

    ![Exempel på mottaget meddelande](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Skicka ett test meddelande (e-postbärvåg)

Du kan skicka meddelanden via [REST API](/rest/api/notificationhubs/) med **Postman**, vilket kan vara ett bekvämare sätt att testa.

1. Öppna en ny flik i **Postman**.

1. Ange begäran att **publicera**och ange följande adress:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Konfigurera begärandehuvuden enligt följande:

   | Nyckel                            | Värde                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | Application/JSON; charset = utf-8 |
   | Auktorisering                  | \<sasToken>                     |
   | ServiceBusNotification-format  | mall                       |
   | Taggar                           | "12345"                        |

1. Konfigurera begär ande **texten** så att den använder **RAW-JSON (Application. JSON)** med följande JSON-nytto last:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Välj knappen **kod** , som finns under knappen **Spara** längst upp till höger i fönstret. Begäran bör se ut ungefär som i följande exempel:

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

Du bör få en **201 skapad** status kod och ta emot meddelandet på klient enheten.

## <a name="next-steps"></a>Nästa steg
Nu har du en grundläggande app för iOS-Swift ansluten till en Notification Hub via [REST API](/rest/api/notificationhubs/) och kan skicka och ta emot meddelanden. Mer information finns i följande artiklar:

- [Översikt över Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST-API: er](/rest/api/notificationhubs/)
- [Notification Hubs SDK för Server dels åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK på GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrera dig med programmets Server del](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Registreringshantering](notification-hubs-push-notification-registration-management.md)
- [Arbeta med Taggar](notification-hubs-tags-segment-push-message.md) 
- [Arbeta med anpassade mallar](notification-hubs-templates-cross-platform-push-messages.md)
- [Service Bus åtkomst kontroll med signaturer för delad åtkomst](../service-bus-messaging/service-bus-sas.md)
- [Generera SAS-token via programmering](/rest/api/eventhub/generate-sas-token)
- [Apple-säkerhet: gemensam kryptering](https://developer.apple.com/security/)
- [Tid för UNIX-tid](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
