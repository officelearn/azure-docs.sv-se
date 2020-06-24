---
title: 'Skicka push-meddelanden till SWIFT iOS-appar med hjälp av Azure Notification Hubs och REST-API: er'
description: 'I den här självstudien får du lära dig hur du använder Azure Notification Hubs och REST-API: er för att skicka push-meddelanden till iOS-enheter.'
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/03/2020
ms.openlocfilehash: 861011874c63f4c5d18ce33b107bbe9a80359045
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126533"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-apis"></a>Självstudie: skicka push-meddelanden till SWIFT iOS-appar med hjälp av Notification Hubs REST API: er

Den här självstudien beskriver hur du använder Azure Notification Hubs för att skicka push-meddelanden till ett iOS-program med hjälp av REST API: er

Den här självstudien omfattar följande steg:

- Skapa en exempel-iOS-app.
- Anslut din iOS-app till Azure Notification Hubs.
- Skicka test-push-meddelanden.
- Verifiera att appen tar emot meddelanden.

Den fullständiga koden för den här själv studie kursen kan hämtas från [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples).

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien, finns följande förhandskrav:

- En Mac som kör [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), tillsammans med ett giltigt certifikat för utvecklare som installerats i din nyckel Ring.

- En iPhone eller iPad som kör iOS version 10 eller senare.

- Din fysiska enhet som är registrerad i [Apples Portal](https://developer.apple.com/)   och kopplad till ditt certifikat.

Innan du fortsätter bör du gå igenom den föregående kursen om att komma igång med [Azure Notification Hubs för iOS-appar](https://go.microsoft.com/fwlink/?linkid=2129801) för att konfigurera och konfigurera push-autentiseringsuppgifter i Notification Hub. Även om du inte har någon tidigare erfarenhet av iOS-utveckling bör du kunna följa dessa steg.

> [!NOTE]
> På grund av konfigurations krav för push-meddelanden måste du distribuera och testa push-meddelanden på en fysisk iOS-enhet (iPhone eller iPad) i stället för iOS-emulatorn.

I följande avsnitt skapar du en iOS-app som ansluter till Notification Hub.

## <a name="create-an-ios-project"></a>Skapa ett iOS-projekt

1. Skapa ett nytt iOS-projekt i Xcode och välj mallen för **enkel vy**   .

2. När du anger alternativ för det nya projektet:
   - Ange det **produkt namn**   (PushDemo) och **organisations-ID**   ( `com.<organization>` ) som du använde när du angav **paket-ID**   i Apple Developer-portalen.
   - Välj det **team**   som **app-ID:** t   har kon figurer ATS för.
   - Ange **språket**   till **Swift**.
   - Välj **Nästa**.

3. Skapa en ny mapp med namnet **SupportingFiles**.

4. Skapa en ny p-list-fil med namnet **devsettings. plist**   i mappen **SupportingFiles**   . Se till att lägga till den här mappen till din **gitignore**-   fil så att den inte har allokerats när du arbetar med en git-lagrings platsen. I en app för produktion kan du villkorligt ställa in dessa hemligheter som en del i en automatiserad versions process. Dessa inställningar beskrivs inte i den här självstudien.

5. Uppdatera **devsettings. plist**   för att inkludera följande konfigurations poster genom att använda dina egna värden från den meddelande hubb som du har allokerat:

   | **Nyckel**                  | **Typ** | **Värde**        |
   | ------------------------ | -------- | ---------------- |
   | notificationHubKey       | Sträng   | `<hubKey>`       |
   | notificationHubKeyName   | Sträng   | `<hubKeyName>`   |
   | notificationHubName      | Sträng   | `<hubName>`      |
   | notificationHubNamespace | Sträng   | `<hubNamespace>` |

   Du kan hitta de värden som krävs genom att gå till resursen Notification Hub i Azure Portal. I synnerhet visas värdena **notificationHubName**   och **notificationHubNamespace**   i det övre högra hörnet av **Essentials**   sammanfattnings sammanfattningen på sidan **Översikt**   .

   :::image type="content" source="media/ios-sdk-swift-rest/image1.png" alt-text="Värden som krävs":::

   Du kan också hitta värdena **notificationHubKeyName**   och **notificationHubKey**   genom att gå till **åtkomst principer**   och välja respektive **åtkomst princip**, till exempel **DefaultFullSharedAccessSignature**. Efter det kopierar du från den **primära anslutnings strängen**   värdet som föregås av  `SharedAccessKeyName=`   för **notificationHubKeyName**och värdet som är prefixet  `SharedAccessKey=`   för **notificationHubKey**. Anslutnings strängen måste ha följande format:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   För enkelhetens skull anger du **DefaultFullSharedAccessSignature**, så att du kan använda token för att skicka meddelanden. I praktiken är **DefaultListenSharedAccessSignature**   ett bättre alternativ för situationer där du bara vill ta emot meddelanden.

6. Under **projekt navigering**väljer du **projekt namnet**   och väljer sedan fliken **Allmänt**   .

7. Hitta **identitet**   och ange sedan **Bundle Identifier**   värdet för paket-ID: t så att det matchar  `com.<organization>.PushDemo` , vilket är det värde som används för **app-ID: t**   från föregående steg.

8. Hitta **signerings & funktioner**och välj sedan lämpligt **team**   för ditt **Apple Developer-konto**.  **Teamets**   värde ska matcha det som du skapade med dina certifikat och profiler.

9. Xcode bör automatiskt hämta rätt **etablerings profil**   värde baserat på **paket-ID**. Om du inte ser det nya **etablerings profil**   svärdet kan du försöka att uppdatera profilerna för **signerings identiteten**   genom att välja **Xcode**, sedan **Inställningar**, **konto**och sedan välja knappen **Hämta manuella profiler**   för att ladda ned profilerna.

10. Klicka på knappen + funktion fortfarande på fliken **signerings & funktioner**    **+ Capability**   och dubbelknacka på push- **meddelanden**i   listan för att säkerställa att **push-meddelanden**   är aktiverade.

11. Öppna filen **AppDelegate. SWIFT**   för att implementera **UNUserNotificationCenterDelegate**-   protokollet och Lägg till följande kod överst i klassen:

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

    Du kommer att använda dessa medlemmar senare. Mer specifikt använder du **taggarna**   medlem som en del av registreringen med hjälp av en **anpassad mall**. Mer information om taggar finns i [taggar för registreringar](notification-hubs-tags-segment-push-message.md)   och [mall registreringar](notification-hubs-templates-cross-platform-push-messages.md).

12. I samma fil lägger du till följande kod i **didFinishLaunchingWithOptions** -funktionen:

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

    Den här koden hämtar inställningarna från **devsettings. plist**, ställer in **AppDelegate**   -klassen som **UNUserNotificationCenter**   -delegaten, begär auktorisering för push-meddelanden och anropar sedan **registerForRemoteNotifications**.

    För enkelhetens skull stöder koden bara iOS 10 och senare. Du kan lägga till stöd för tidigare iOS-versioner genom att villkorligt använda respektive API och metoder, precis som vanligt.

13. I samma fil lägger du till följande kod:

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

    I den här koden används **installationId**-   och **pushChannel**-   värden för att registrera med Notification Hub. I det här fallet använder du **UIDevice. Current. identifierForVendor**   för att ange ett unikt värde för att identifiera enheten och sedan formatera **deviceToken**   för att tillhandahålla det önskade **pushChannel**-   värdet. Funktionen **showAlert**   finns bara för att visa viss meddelande text i demonstrations syfte.

14. Lägg fortfarande till **AppDelegate.swift**    **willPresent**-   och **DidReceive**-   funktionerna till **UNUserNotificationCenterDelegate**i filen AppDelegate. Swift. Dessa funktioner visar en avisering när de får ett meddelande om att en app körs antingen i förgrunden eller i bakgrunden.

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

15. Lägg till `print` instruktioner längst ned i **didRegisterForRemoteNotificationsWithDeviceToken**-   funktionen för att kontrol lera att **installationId**   och **pushChannel**   tilldelas värden:

    ```swift
    print(installationId)
    print(pushChannel)
    ```

16. Skapa mapparna **modeller**, **tjänster**och **verktyg**   för de grundläggande komponenterna som du kommer att lägga till i projektet senare.

17. Kontrol lera att projektet bygger och körs på en fysisk enhet. Det går inte att testa push-meddelanden med simulatorn.

## <a name="create-models"></a>Skapa modeller

I det här steget skapar du en uppsättning modeller som representerar [Notification Hubs REST API](/rest/api/notificationhubs/)   nytto laster och för att lagra nödvändiga data för signatur för delad åtkomst (SAS).

1. Lägg till en ny Swift-fil med namnet **PushTemplate. SWIFT**   i mappen **modeller**   . Den här modellen definierar en struktur som representerar **bröd texten**   i en enskild mall som en del av **DeviceInstallation**-   nyttolasten.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Lägg till en ny Swift-fil med namnet **DeviceInstallation. SWIFT**   i mappen **modeller**   . Den här filen definierar en struktur som representerar nytto lasten för att skapa eller uppdatera en **enhets installation**. Lägg till följande kod i filen:

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

3. Lägg till en ny Swift-fil med namnet **TokenData. SWIFT**   i mappen **modeller**   . Den här modellen används för att lagra en SAS-token tillsammans med dess förfallo datum. Lägg till följande kod i filen:

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

## <a name="generate-a-sas-token"></a>Generera en SAS-token

Notification Hubs använder samma säkerhets infrastruktur som Azure Service Bus. För att anropa REST API, [genererar program mässigt en SAS-token](/rest/api/eventhub/generate-sas-token)   som kan användas i **Authorization**   begärans Authorization-huvud.

Den resulterande token kommer att ha följande format:

```xml
SharedAccessSignature sig=\<UrlEncodedSignature\>\&se=\<ExpiryEpoch\>\&skn=\<KeyName\>\&sr=\<UrlEncodedResourceUri\> |
```

Själva processen omfattar samma sex steg:

1. Beräkna förfallo [tiden i UNIX](https://en.wikipedia.org/wiki/Unix_time)   -värdes format, vilket innebär hur många sekunder som har förflutit sedan midnatt Universal Coordinated Time, 1 januari 1970.

2. Formatera **ResourceUrl**   som representerar resursen som du försöker få åtkomst till, så att den är i procent kodad och gemen.  **ResourceUrl**   har formatet `https://<namespace>.servicebus.windows.net/<hubName>` .

3. Förbered **StringToSign**, som är formaterad som `<UrlEncodedResourceUrl>\n<ExpiryEpoch>` .

4. Beräkna och base64-koda **signaturen**   med hjälp av HMAC-SHA256 hash för **StringToSign**-   värdet. Hash-värdet används med **nyckel**   delen av **anslutnings strängen**   för respektive **auktoriseringsregel**.

5. Formatera den base64-kodade **signaturen**   så att den är procent kodad.

6. Konstruera token i förväntat format med hjälp av värdena **UrlEncodedSignature**, **ExpiryEpoch**, **nyckel namn**och **UrlEncodedResourceUrl**   .

I [Azure Service Bus-dokumentationen](../service-bus-messaging/service-bus-sas.md)finns   en mer fullständig översikt över signaturer för delad åtkomst och hur Azure Service Bus och Notification Hubs använder den.

I det här Swift-exemplet använder du Apples CommonCrypto-bibliotek med öppen källkod **CommonCrypto**   för att hjälpa till med hashing av signaturen. Som det är ett C-bibliotek går det inte att komma åt i snabbt med rutan. Du kan göra biblioteket tillgängligt genom att använda ett bryggnings huvud.

Lägga till och konfigurera bryggnings huvudet:

1. I Xcode väljer du **Arkiv**, **nytt**, sedan **fil**och sedan **rubrik fil**. Namnge rubrik filen **BridgingHeader. h**.

2. Redigera filen för att importera **CommonHMAC. h**:

   ```swift
   #import <CommonCrypto/CommonHMAC.h>

   #ifndef BridgingHeader_h
   #define BridgingHeader_h

   #endif /* BridgingHeader_h */
   ```

3. Uppdatera mål **versions inställningarna**   så att de refererar till interimskontot för överbryggning:

   1. Välj **PushDemo**-   projektet och rulla ned till avsnittet **Swift-kompilator**   .

   2. Se till att alternativet **Installera mål-C-kompatibilitetsläge**   är inställt på **Ja**.

   3. Ange fil Sök vägen  `<ProjectName>/BridgingHeader.h`   i alternativet **mål-C-bryggning**   . Detta är fil Sök vägen till bryggnings huvudet.

   Om du inte kan hitta de här alternativen måste du kontrol lera **All**att du har   valt vyn alla, snarare än **grundläggande**   eller **anpassad**.

   Det finns många tredjeparts omslutnings bibliotek med öppen källkod som kan göra att det blir enklare att använda **CommonCrypto**   . Diskussion av dessa bibliotek ligger dock utanför den här artikelns omfattning.

4. Lägg till en ny Swift-fil med namnet **TokenUtility. SWIFT**   i mappen **verktyg**   och Lägg till följande kod:

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

   Som beskrivs ovan **getSasToken**   dirigerar funktionen getSasToken de avancerade steg som krävs för att förbereda token. Funktionen kommer att anropas av installations tjänsten senare i den här självstudien.

   De andra två funktionerna anropas av funktionen **getSasToken**   : **sha256HMac**   för att beräkna signaturen och **urlEncodedString**   för kodning av den associerade URL-strängen. Funktionen **urlEncodedString**   krävs, eftersom det inte går att uppnå de utdata som krävs med hjälp av den inbyggda **addingPercentEncoding**-   funktionen.

    [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m)   är ett utmärkt exempel på hur du kan närma dig dessa åtgärder i mål-C. Mer information om Azure Service Bus SAS-token finns i Azure Service Bus- [dokumentationen](../service-bus-messaging/service-bus-sas.md).

5. I **AppDelegate. SWIFT**lägger du till följande kod i  `didRegisterForRemoteNotificationsWithDeviceToken`   funktionen för att kontrol lera att **TokenUtility. getSasToken**   genererar en giltig token

   ```swift
   let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

   let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                            withKeyName: self.notificationHubKeyName!,
                                            andKey: self.notificationHubKey!)

   print(tokenData.token)
   ```

   Se till att ersätta plats hållarna i **baseAddress**   strängaren sträng med dina egna värden.

## <a name="verify-the-sas-token"></a>Verifiera SAS-token

Innan du implementerar installations tjänsten i klienten måste du kontrol lera att din app genererar SAS-token korrekt genom att använda ett HTTP-verktyg. I den här självstudien är vårt verktyg att välja **Postman**.

Anteckna värdena för **installationId**   och **token**   som genereras av appen.

Följ de här stegen för att **installations**anropa   API: erna för installation:

1. Öppna en ny flik i **Postman**.
2. Ange begäran för att **Hämta**   och ange följande adress:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
   ```

3. Konfigurera begärandehuvuden enligt följande:

   | **Nyckel**       | **Värde**        |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Auktorisering | \<sasToken\>     |
   | x-MS-version  | 2015-01          |

4. Välj den **kod**   knapp som visas längst upp till höger under knappen **Spara**   . Begäran bör se ut ungefär som i följande exempel:

   ```xml
   GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json
   Authorization: <sasToken>
   x-ms-version: 2015-01
   Cache-Control: no-cache
   Postman-Token: <postmanToken>
   ```

5. Välj knappen **Skicka**   .

Det finns ingen registrering för den angivna **installationId**   just nu. Verifieringen bör resultera i att ett "404" inte hittas "-svar i stället för ett" 401 oauktoriserat "-svar. Det här resultatet bör bekräfta att SAS-token har accepterats.

## <a name="implement-the-installation-service-class"></a>Implementera installations tjänst klassen

Implementera sedan en grundläggande omslutning runt [Installations REST API](/rest/api/notificationhubs/create-overwrite-installation).

Lägg till en ny Swift-fil med namnet **NotificationRegistrationService. SWIFT**   under mappen **tjänster**   och Lägg sedan till följande kod i filen:

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

Den information som krävs tillhandahålls som en del av initieringen. Taggar och mallar skickas alternativt till **register**-   funktionen för att utgöra en del av JSON-nyttolasten för **enhets installation**   .

Funktionen **register**   anropar de andra privata funktionerna för att förbereda begäran. När ett svar har tagits emot anropas slut för ande och indikerar om registreringen lyckades.

Begär ande slut punkten konstrueras av **getBaseAddress**-   funktionen. Bygget använder Notification Hub-parametrarna *namespace*   och *Name*   som angavs under initieringen.

Funktionen **getSasToken**   kontrollerar om den aktuella lagrade token är giltig. Om token inte är giltig anropar funktionen **TokenUtility**   för att generera en ny token och lagrar den innan ett värde returneras.

Slutligen konverterar **encodeToJson**   respektive modell objekt till JSON för användning som en del av begär ande texten.

## <a name="invoke-the-notification-hubs-rest-api"></a>Anropa Notification Hubs REST API

Det sista steget är att uppdatera **AppDelegate**   för att använda **NotificationRegistrationService**   för att registrera dig hos **NotificationHub**.

1. Öppna **AppDelegate. SWIFT**   och Lägg till variabler på klass nivå för att lagra en referens till **NoficiationRegistrationService**   och den allmänna **PushTemplate**:

   ```swift
   var registrationService : NotificationRegistrationService?
   let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
   ```

2. I samma fil uppdaterar du DidRegisterForRemoteNotificationsWithDeviceToken- **didRegisterForRemoteNotificationsWithDeviceToken**   funktionen för att initiera **NotificationRegistrationService**   med nödvändiga parametrar och anropa sedan funktionen **Registrera**   .

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

   För enkelhetens skull använder koden `print` instruktioner för att uppdatera fönstret utdata med resultatet av **register**   åtgärden.

3. Skapa och kör appen på en fysisk enhet. Du bör se **registrerade** i fönstret utdata.

## <a name="test-the-solution"></a>Testa lösningen

I det här skedet registreras appen med **NotificationHub**   och kan ta emot push-meddelanden. Stoppa fel söknings programmet i Xcode och Stäng appen om den körs för närvarande. Kontrol lera sedan att **enhets installations**   informationen visas som förväntat och att appen nu kan ta emot push-meddelanden.

### <a name="verify-the-device-installation"></a>Verifiera enhets installationen

Du kan nu göra samma begäran som du tidigare, genom att använda **Postman**   för [att verifiera SAS-token](notification-hubs-ios-push-notifications-swift-apps-get-started.md#verify-the-sas-token). Om du antar att SAS-token inte har gått ut bör du nu ta med den installations information som du har angett, till exempel mallarna och taggarna.

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

Om din tidigare SAS-token har upphört att gälla kan du lägga till en Bryt punkt på rad 24 i **TokenUtility**-   klassen för att hämta en ny SAS-token och uppdatera **Authorization**-   huvudet med det nya värdet.

### <a name="send-a-test-notification-azure-portal"></a>Skicka ett test meddelande (Azure Portal)

Det snabbaste sättet att testa att du nu kan ta emot meddelanden är att bläddra till Notification Hub i Azure Portal:

1. I Azure Portal bläddrar du till fliken **Översikt**   i Notification Hub.

2. Välj **testa att skicka**, som är ovanför **Essentials**   sammanfattnings sammanfattningen längst upp till vänster i Portal fönstret:

   :::image type="content" source="media/ios-sdk-swift-rest/image2.png" alt-text="Skicka test av sammanfattnings test för Notification Hubs Essentials":::

3. Välj **anpassad mall**   i listan **plattformar**   .

4. Ange **12345**   som **uttryck för skicka till-tagg**. Du har angett den här taggen tidigare i installationen.

5. Du kan också redigera **meddelandet**   i JSON-nyttolasten:

   :::image type="content" source="media/ios-sdk-swift-rest/image3.png" alt-text="Notification Hubs skicka test":::

6. Välj **Skicka**. Portalen ska indikera om meddelandet har skickats till enheten:

   :::image type="content" source="media/ios-sdk-swift-rest/image4.png" alt-text="Testa sändnings resultat":::

   Förutsatt att appen inte körs i förgrunden bör du även se ett meddelande i **meddelande centret**   på din enhet. Om du trycker på meddelandet bör du öppna appen och visa aviseringen.

   :::image type="content" source="media/ios-sdk-swift-rest/image5.png" alt-text="Test meddelande":::

### <a name="send-a-test-notification-mail-carrier"></a>Skicka ett test meddelande (e-postbärvåg)

Du kan skicka meddelanden via [REST API](/rest/api/notificationhubs/)   med **Postman**, vilket kan vara ett bekvämare sätt att testa.

1. Öppna en ny flik i **Postman**.

2. Ange begäran att **publicera**och ange följande adress:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
   ```

3. Konfigurera begärandehuvuden enligt följande:

   | Tangent                           | Värde                          |
   | ----------------------------- | ------------------------------ |
   | Content-Type                  | Application/JSON; charset = utf-8 |
   | Auktorisering                 | \<sasToken\>                   |
   | ServiceBusNotification-format | mall                       |
   | Taggar                          | "12345"                        |

4. Konfigurera begär ande **texten**   att använda **RAW-JSON (application.jspå)**   med följande JSON-nytto last:

   ```json
   {
   "message" : "Hello from Postman!"
   }
   ```

5. Välj knappen **kod**   , som finns under knappen **Spara**   längst upp till höger i fönstret. Begäran bör se ut ungefär som i följande exempel:

   ```xml
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

6. Välj knappen **Skicka**   .

Du bör få en **201 skapad**   status kod för lyckad avisering och ta emot meddelandet på klient enheten.

## <a name="next-steps"></a>Nästa steg

Nu har du en grundläggande iOS Swift-app som är ansluten till en Notification Hub via [Notification Hubs REST API](/rest/api/notificationhubs/)och kan skicka och ta emot meddelanden. Om du vill veta mer om hur du skickar meddelanden till vissa enheter går du vidare till följande självstudie:

- [Självstudie: push-meddelanden till vissa enheter](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Mer information finns i följande artiklar:

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
