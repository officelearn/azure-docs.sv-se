---
title: Push-meddelanden till Swift iOS-appar med Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du push-meddelanden till Swift iOS-appar med Azure Notification Hubs.
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
ms.openlocfilehash: 64a05c6cbd412953ae10b31efc1d141c9cefd062
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65994773"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-using-the-notification-hubs-rest-api"></a>Självstudier: Push-meddelanden till Swift iOS-appar med hjälp av Notification Hubs REST API

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

I de här självstudierna använder Azure Notification Hubs för push-meddelanden till en Swift-baserade iOS-program med den [REST API](/rest/api/notificationhubs/). Du skapar en tom iOS-app som tar emot push-meddelanden med hjälp av [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Generera filen för begäran om certifikatsignering
> * Registrera din app för push-meddelanden
> * Skapa en etableringsprofil för appen
> * Skapa en meddelandehubb
> * Konfigurera meddelandehubben med APNS-information
> * Anslut iOS-appen till meddelandehubbar
> * Testa lösningen

## <a name="prerequisites"></a>Nödvändiga komponenter
Om du vill följa med, behöver du:

- Gå igenom [översikt över Azure Notification Hubs](notification-hubs-push-notification-overview.md) om du inte är bekant med tjänsten. 
- Läs mer om registreringar och installationen: [Registreringshantering](notification-hubs-push-notification-registration-management.md)
- En aktiv [Apple Developer-konto](https://developer.apple.com) 
- En Mac med Xcode tillsammans med ett giltigt developer-certifikat som är installerat i din nyckelring
- En fysisk iPhone-enhet som du kan köra och felsöka med (det inte går att testa push-meddelanden med simulatorn)
- Fysiska iPhone-enheten registrerad i den [Apples Portal](https://developer.apple.com) och som är associerade med ditt certifikat
- En [Azure-prenumeration](https://portal.azure.com) där du kan skapa och hantera resurser

Det bör vara möjligt att följa instruktionerna för att skapa det här första principerna exemplet utan tidigare erfarenhet. Dock är bekant med följande begrepp användbar:

- Att skapa iOS-appar med Xcode och Swift
- Konfigurera en [Azure Notification Hub](notification-hubs-ios-apple-push-notification-apns-get-started.md) för iOS
- Bekant med den [Apple Developer-portalen](https://developer.apple.com) och [Azure-portalen](https://portal.azure.com)

> [!NOTE]
> Meddelandehubben ska konfigureras för att använda den *Sandbox* endast autentiseringsläge. Du bör inte använda det här autentiseringsläget för produktionsarbetsbelastningar.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Anslut iOS-appen till Notification Hubs
I det här avsnittet ska du skapa iOS-app som ska ansluta till notification hub.  

### <a name="create-an-ios-project"></a>Skapa en iOS-projekt
1. I **Xcode**skapar du ett nytt iOS-projekt och väljer den **program enkel vy** mall.
2. När du anger alternativ för det nya projektet så här:
    1. Använda samma **produktnamn** (det vill säga **PushDemo**) och **Organisationsidentifierare** (det vill säga `com.<organization>`) du använde när den **paket Identifieraren** har angetts i den **Apple Developer-portalen**. 
    2. Välj den **Team** som den **App-ID** har ställt in för.
    3. Ange den **språk** till **Swift**.
    4. Klicka på **Nästa**
3. Skapa en ny mapp med namnet **SupportingFiles**.
4. Skapa en ny **plist** fil med namnet **devsettings.plist** under den **SupportingFiles** mapp. Se till att lägga till mappen i din **gitignore** -fil så att den inte är allokerade när du arbetar med en **git-lagringsplats**. I ett produktionsprogram skulle du förmodligen villkorligt ställa dessa hemligheter som en del av en automatisk build-process. Den omfattas dock inte som en del av den här genomgången.
5. Uppdatera **devsettings.plist** att inkludera följande konfigurationsposter (med hjälp av dina egna värden från den **Notification Hub** du etablerat):

   | Nyckel                            | Type                     | Värde                     |               
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey\>                |
   | notificationHubKeyName         | String                   | \<hubKeyName\>            |
   | notificationHubName            | String                   | \<hubName\>               |
   | notificationHubNamespace       | String                   | \<hubNamespace\>          |
    
   Du kan hitta de nödvändiga värdena genom att navigera till den **Meddelandehubb** resurs i den **Azure-portalen**. Du hittar den **notificationHubName** och **notificationHubNamespace** värdena i det övre högra hörnet av den **Essentials** sammanfattning inom den  **Översikt över** sidan.

   ![Notification Hubs Essentials Summary](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Du hittar den **notificationHubKeyName** och **notificationHubKey** värden genom att gå till **åtkomstprinciper**, klicka på respektive **åtkomst Principen**. Till exempel `DefaultFullSharedAccessSignature`. Kopiera från den **primär anslutningssträng** värdet prefixet `SharedAccessKeyName=` för `notificationHubKeyName` och värdet prefixet `SharedAccessKey=` för den `notificationHubKey`. Anslutningssträngen ska vara i följande format:
    
   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Du ska använda för att enkelt *DefaultFullSharedAccessSignature* så att du kan använda token för att skicka meddelanden samt. Men i praktiken den `DefaultListenSharedAccessSignature` skulle vara ett bättre alternativ för situationer där du bara vill ta emot meddelanden.       
6. Under **Projektnavigeringen**, klickar du på den **projektnamn**, klicka sedan på den **Allmänt** fliken
7. Hitta **identitet**, ange den **paket-ID** värde så att den matchar den som används för den **App-ID** (från det tidigare steget) dvs. `'com.<organization>.PushDemo'`
8. Hitta **signering**, måste du välja lämplig **Team** för din **Apple Developer-konto** (det som du har skapat ditt certifikat och -profiler tidigare).  **Xcode** bör automatiskt hämta lämplig **Etableringsprofil** baserat på den **paketidentifieraren**. Om du inte ser den nya **Etableringsprofil**, försöka uppdatera profilerna för den **identitet för kodsignering** (*Xcode > Inställningar > konto > Visa information om*). När du klickar på den **identitet för kodsignering**, klicka på den **uppdatera** knappen längst ned till höger ska hämtas profilerna.
9. Välj den **funktioner** fliken och se till att **push-meddelanden** är aktiverade.
10. Öppna din **AppDelegate.swift** fil att implementera den *UNUserNotificationCenterDelegate* -protokollet och Lägg till följande kod högst upp i klassen:
    
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

    Senare ska du använda dessa medlemmar. Den *taggar* och *genericTemplate* används som en del av registreringen. Mer information om taggar finns i [taggar för registreringar](notification-hubs-tags-segment-push-message.md) och [mallregistreringar](notification-hubs-templates-cross-platform-push-messages.md).
 
11. I samma fil, lägger du till följande kod i den *didFinishLaunchingWithOptions* funktionen:

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

    Den här koden hämtar inställningsvärden från **devsettings.plist**, anger den **AppDelegate** klassen som den *UNUserNotificationCenter* delegera begäranden auktorisering för push-meddelanden och samtal *registerForRemoteNotifications*.
    
    För att enkelt koden stöder **iOS 10 och senare endast**. Du kan lägga till stöd för äldre OS-versioner med villkorligt respektive API: er och metoder som du skulle göra.

12. Lägg till följande funktioner i samma fil:

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

    Koden använder den *installationId* och *pushChannel* värden att registrera med den **Notification Hub**. I detta fall använder du använder *UIDevice.current.identifierForVendor* tillhandahåller ett unikt värde som identifierar enheten och sedan formatera den *deviceToken* att ge oss den önskade *pushChannel* värde. Den *showAlert* funktionen är bara att visa vissa meddelandetexten i exempelsyfte.

13. Fortfarande är i **AppDelegate.swift**, lägga till den *willPresent* och *didReceive* **UNUserNotificationCenterDelegate** fungerar till Visa en avisering när meddelandet anlänt när appen körs i förgrunden och bakgrunden respektive
    
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

14. Lägg till Skriv ut uttryck längst ned på den *didRegisterForRemoteNotificationsWithDeviceToken* funktionen för att kontrollera att *installationId* och *pushChannel* som ska tilldelade värden
15. Skapa mappar (**modeller**, **Services**, och **verktyg**) för de grundläggande komponenter som du ska lägga till i projektet senare
16. Kontrollera att projektet skapas och körs på en fysisk enhet (push-meddelanden inte går att testa med hjälp av simulatorn)

### <a name="create-models"></a>Skapa modeller
I det här steget ska du skapa en uppsättning modeller för att representera den [Notification Hubs – REST API](/rest/api/notificationhubs/) nyttolaster och för att lagra de nödvändiga **SAS-token** data.


1.  Lägg till en ny swift fil och den **modeller** kallas **PushTemplate.swift**. Den här modellen ger en struct som representerar den **BRÖDTEXT** för en enskild mall som en del av den **DeviceInstallation** nyttolast:
    
    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String
    
        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Lägg till en ny swift fil och den **modeller** mapp med namnet **DeviceInstallation.swift**. Den här filen definierar en struct som representerar nyttolasten för att skapa eller uppdatera en **enhetsinstallation**. Lägg till följande kod i filen:
    
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

3.  Lägg till en ny swift fil under **modeller** kallas **TokenData.swift**. Den här modellen som används för att lagra en **SAS-token** tillsammans med dess förfallodatum

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
**Meddelandehubbar** använder samma säkerhetsinfrastruktur som **Azure Service Bus**. För att anropa REST-API kan du behöva [programmatiskt Generera en SAS-token](/rest/api/eventhub/generate-sas-token) som kan användas i den **auktorisering** huvudet i begäran.  

Den resulterande token är i följande format: 

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Själva processen omfattar samma sex viktiga steg:  

1.  Databehandling utgång vid [UNIX epoktid](https://en.wikipedia.org/wiki/Unix_time) format (sekunder har förflutit sedan 00:00:00 UTC 1 januari 1970)
2.  Formatera den **ResourceUrl** (som representerar den resurs som du försöker komma åt, det vill säga `'https://\<namespace\>.servicebus.windows.net/\<hubName\>'`) så att den är procentkodad och gemener
3.  Förbereda den **StringToSign**, som består av `'\<**UrlEncodedResourceUrl**\>\n\<**ExpiryEpoch**\>'`
4.  Databehandling (och base 64-kodning) den **signatur** med hjälp av den **HMAC-SHA256** av den **StringToSign** värde med den **nyckel** en del av den **Connection String** (för respektive **auktoriseringsregel**)
5.  Formatering av base 64-kodad **signatur** så att det är kodat i procent
6.  Konstruera den **token** i rätt format med hjälp av den **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**, och **UrlEncodedResourceUrl** värden

Se den [dokumentation för Azure Service Bus](../service-bus-messaging/service-bus-sas.md) för en mer omfattande översikt över **signatur för delad åtkomst** och hur den används av **Azure Service Bus** och  **Meddelandehubbar**.

För exemplet Swift, ska du använda Apples öppen källkod **CommonCrypto** -biblioteket för att underlätta hashing av signaturen. Eftersom det är ett C-bibliotek, det är inte tillgänglig i Swift out-of the box. Men kan du göra detta med en datacenterbryggning rubrik. Lägga till och konfigurera datacenterbryggning rubriken:

1. I **Xcode**går du till **filen**, sedan **New**, sedan **filen** och välj **huvudfil** namnge den *'BridgingHeader.h'*
2. Redigera filen för att importera **CommonHMAC**

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

3. Uppdatera målets **Versionsinställningar** att referera till rubriken datacenterbryggning. Öppna den **inställningar för att bygga** fliken och rulla ned till den **Swift Compiler** avsnittet. Se till att den **installera Objective-C kompatibilitet rubrik** alternativet inställt på **Ja** och ange filepath till vår datacenterbryggning rubrik i den **Objective-C bryggning rubrik**   alternativ som är `'\<ProjectName\>/BridgingHeader.h'`. Om du inte hittar de här alternativen kan du kontrollera att du har den **alla** vy som valts (snarare än **grundläggande** eller **anpassad**).
    
   Det finns många från tredje part open source-omslutning bibliotek tillgänglig, vilket kan göra med hjälp av **CommonCrypto** lite enklare. Det är utanför omfattningen för den här artikeln.

4. Lägg till en ny Swift fil under den **verktyg** mapp med namnet **TokenUtility.swift** och Lägg till följande kod:

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
    
    Det här verktyget innehåller logiken som ansvarar för att generera den **SAS-token**. Den *getSasToken* funktionen samordnar de övergripande stegen som krävs för att förbereda token som beskrivs tidigare och kommer att anropas av installationstjänsten i stegen senare i den här självstudien. De två funktionerna anropas av den *getSasToken funktionen*; *sha256HMac* för databehandling signaturen och *urlEncodedString* för encoding respektive Url-strängen. Den *urlEncodedString* funktionen krävdes eftersom det inte möjligt att uppnå nödvändiga utdata med inbyggt *addingPercentEncoding* funktion. Den [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) hanteras som ett utmärkt exempel på hur de här åtgärderna i Objective-C. Ytterligare information på **Azure Service Bus SAS-token** finns i den [dokumentation för Azure Service Bus](../service-bus-messaging/service-bus-sas.md). 

### <a name="verify-the-sas-token"></a>Kontrollera SAS-token
Innan du implementerar installationstjänsten i klienten, du kan kontrollera att vår app korrekt genererar den **SAS-token** med http-verktyget val. För det här inlägget, våra verktyg blir **Postman**.

Anteckna den *installationId* och *token* värden som skapas av programmet med en korrekt placerad skriva ut instruktionen eller brytpunkt. 

Följ dessa steg för att anropa den *installationer* API:

1. I **Postman**, öppna en ny flik
2. Anger **hämta** och följande adress:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

3. Konfigurera begärandehuvuden enligt följande:
    
   | Nyckel           | Värde            |
   | ------------- | ---------------- |
   | Innehållstyp  | application/json |
   | Auktorisering | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. Klicka på den **kod** knappen (upp till höger under den **spara** knappen). Begäran bör likna exemplet nedan:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

5. Klicka på den **skicka** knappen

Det finns ingen registrering för den angivna *installationId* nu, men det bör resultera i en **404 hittades inte** svar snarare än **401 Ej behörig**. Resultatet bör kontrollera att den **SAS-token** har accepterats.

### <a name="implement-the-installation-service-class"></a>Implementera tjänstklass installation
Bredvid implementerar du våra grundläggande omslutning runt den [installationer REST API](/rest/api/notificationhubs/create-overwrite-installation).  

Lägg till en ny Swift fil under den **Services** mapp med namnet **NotificationRegistrationService.swift**, Lägg sedan till följande kod till den här filen:

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
 
De nödvändiga informationen tillhandahålls som en del av initieringen. Taggar och mallar som du kan också skickas till den *registrera* funktionen att ingå i den **enhetsinstallation** JSON-nyttolast.  

Den *registrera* funktion som anropar till de privata funktionerna som du förbereder begäran. När ett svar tas emot, kallas slutförs som anger om registreringen har lyckats eller inte.  

Begäran-slutpunkt skapas genom att den *getBaseAddress* med hjälp av den **Meddelandehubb** parametrar **namnområde** och **namn**tillhandahålls under initieringen.  

Den *getSasToken* funktionen kommer att kontrollera om lagrade token är giltig, annars anropar till den **TokenUtility** att generera en ny och lagra den innan den returneras ett värde. 

Slutligen den *encodeToJson* konverterar respektive modell-objekt till JSON för användning som en del av frågans brödtext.

### <a name="invoke-the-notification-hubs-rest-api"></a>Anropa REST-API för Notification Hubs
Det sista steget är att uppdatera **AppDelegate** att använda den **NotifiationRegistrationService** att registrera med vår **NotificationHub**. 

1. Öppna **AppDelegate.swift** och Lägg till en klassnivåvariabler för att lagra en referens till den **NoficiationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

2. I samma fil, uppdaterar den *didRegisterForRemoteNotificationsWithDeviceToken* funktionen för att initiera den **NotificationRegistrationService** med de obligatoriska parametrarna sedan anropa *registrera* funktion.

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
    
        // Call register passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    För att göra det enkelt, ska du använda ett par av skriva ut rapporter för att uppdatera utdatafönstret med resultatet från den *registrera* igen. 

3. Nu skapa och köra appen (på en fysisk enhet). Du bör se **”Registered”** i utdatafönstret.

## <a name="test-the-solution"></a>Testa lösningen
I det här skedet vår app är registrerad med **NotificationHub** och det kan ta emot push-meddelanden. I **Xcode**, stoppar felsökningsverktyget och stänger appen (om den körs för närvarande). Därefter ska du kontrollera som den **enhetsinstallation** information är som förväntat och att appen verkligen nu kan ta emot push-meddelanden.  

### <a name="verify-the-device-installation"></a>Verifiera installationen
Nu kan du göra samma begäran som du gjorde tidigare med **Postman** för [verifierar SAS-token](#verify-the-sas-token). Om vi antar att det **SAS-token** har inte upphört att gälla, svaret ska nu innehålla installationsinformationen, till exempel mallar och taggar.  

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
Det snabbaste sättet att testa att kan du nu ta emot meddelanden är att navigera till den **Meddelandehubb** i den **Azure-portalen**.

1. I den **Azure-portalen**, navigera till den **översikt** fliken på din **Notification Hub**
2. Klicka på **skicka Test** (övre vänstra) ovanför den **Essentials** sammanfattning

    ![Notification Hubs Essentials skicka sammanfattning av Test-knappen](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)
3. Välj **anpassad mall** från listan över **plattformar**
4. Ange **12345** för den **skicka till Tagguttryck** (du hade angett den här taggen i vår installation)
5. Du kan också redigera den **meddelande** i JSON-nyttolast
    
    ![Notification Hubs prova att skicka](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)
6. Klicka på **skicka** och portalen ska indikera om meddelandet har skickats till enheten

    ![Skicka resultat för Notification Hubs Test](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Du bör även se ett meddelande i den **Meddelandecentret** på enheten (förutsatt att appen inte körs i förgrunden). Att trycka på meddelandet ska öppna appen och visa aviseringen.

    ![Meddelandet emot exempel](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-postman"></a>Skicka ett testmeddelande (Postman)
Du kan skicka meddelanden via till respektive [REST API](/rest/api/notificationhubs/) via **Postman** som källa och det kan vara det enklaste sättet att testa. 

1. I **Postman**, öppna en ny flik
2. Anger **POST** och ange följande adress:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

3. Konfigurera begärandehuvuden enligt följande:
    
   | Nyckel                            | Värde                          |
   | ------------------------------ | ------------------------------ |
   | Innehållstyp                   | application/json;charset=utf-8 |
   | Auktorisering                  | \<sasToken\>                   |
   | ServiceBusNotification-Format  | mall                       |
   | Tags                           | "12345"                        |

4. Konfigurera begäran **BRÖDTEXT** att använda **RAW - JSON (application.json)** med följande JSON-nyttolasten:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

5. Klicka på den **kod** knappen (upp till höger under den **spara** knappen). Begäran bör likna exemplet nedan:

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

5. Klicka på den **skicka** knappen

Du bör få en lyckad statuskod och ta emot meddelandet på klientenheten.

## <a name="next-steps"></a>Nästa steg
Nu har du en snabb grundläggande iOS-app ansluten till en **Meddelandehubb** via den [REST API](/rest/api/notificationhubs/) och kan skicka och ta emot meddelanden. Mer information finns i följande artiklar: 

- [Översikt över Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Notification Hub REST API: er](/rest/api/notificationhubs/)
- [Notification Hubs SDK för backend-åtgärder](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK på GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrera med serverdelen för programmet](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Registreringshantering](notification-hubs-push-notification-registration-management.md)
- [Arbeta med taggar](notification-hubs-tags-segment-push-message.md) 
- [Arbeta med anpassade mallar](notification-hubs-templates-cross-platform-push-messages.md)
- [Service Bus åtkomstkontroll med signaturer för delad åtkomst](../service-bus-messaging/service-bus-sas.md)
- [Programmatiskt generera SAS-token](/rest/api/eventhub/generate-sas-token)
- [Apple-säkerhet: vanliga krypto](https://developer.apple.com/security/)
- [UNIX-epoktid](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
