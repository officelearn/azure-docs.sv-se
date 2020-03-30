---
title: Använda iOS SDK
description: Så här använder du iOS SDK för Azure Mobile Apps
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1bf8f8e198f6c4a4a0af308262cd830685698a80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249352"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Så här använder du iOS-klientbibliotek för Azure Mobile Apps

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Översikt
Den här guiden lär dig att utföra vanliga scenarier med hjälp av den senaste [Azure Mobile Apps iOS SDK][1]. Om du inte har tidigare azure mobile apps har du först slutfört [snabbstarten] för Azure Mobile Apps för att skapa en backend, skapa en tabell och hämta ett förbyggt iOS Xcode-projekt. I den här guiden fokuserar vi på klientsidan iOS SDK. Mer information om serversidan SDK för serversidan finns i Server SDK HOWTOs.

## <a name="reference-documentation"></a>Referensdokumentation

Referensdokumentationen för iOS-klienten SDK finns här: [Azure Mobile Apps iOS Client Reference][2].

## <a name="supported-platforms"></a>Plattformar som stöds

IOS SDK stöder Objective-C-projekt, Swift 2.2-projekt och Swift 2.3-projekt för iOS-versioner 8.0 eller senare.

Autentiseringen "serverflöde" använder en WebView för det presenterade användargränssnittet.  Om enheten inte kan presentera ett WebView-användargränssnitt krävs en annan autentiseringsmetod som ligger utanför produktens omfattning.  
Denna SDK är därför inte lämplig för Watch-type eller liknande begränsade enheter.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Inställningar och förutsättningar

Den här guiden förutsätter att du har skapat en backend med en tabell. Den här guiden förutsätter att tabellen har samma schema som tabellerna i dessa självstudier. Den här guiden förutsätter också att `MicrosoftAzureMobile.framework` du `MicrosoftAzureMobile/MicrosoftAzureMobile.h`i koden refererar och importerar .

## <a name="how-to-create-client"></a><a name="create-client"></a>Så här skapar du klient

Skapa en `MSClient`. Ersätt `AppUrl` med appens URL. Du kan `gatewayURLString` `applicationKey` gå och tömma. Om du konfigurerar en gateway `gatewayURLString` för autentisering fyller du i med gateway-URL:en.

**Mål C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift:**

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="how-to-create-table-reference"></a><a name="table-reference"></a>Så här skapar du tabellreferens

För att få åtkomst till eller uppdatera data skapar du en referens till serverdelstabellen. Ersätt `TodoItem` med namnet på tabellen

**Mål C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift:**

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="how-to-query-data"></a><a name="querying"></a>Så här: Fråga data

Om du vill skapa `MSTable` en databasfråga frågar du objektet. Följande fråga hämtar alla `TodoItem` objekt och loggar texten för varje objekt.

**Mål C**:

```objc
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occurred
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift:**

```swift
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="how-to-filter-returned-data"></a><a name="filtering"></a>Så här filtrerar du returnerade data

För att filtrera resultat finns det många tillgängliga alternativ.

Om du vill filtrera med `NSPredicate` ett `readWithPredicate`predikat använder du en och . Följande filter returnerade data för att hitta endast ofullständiga Todo-objekt.

**Mål C**:

```objc
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift:**

```swift
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="how-to-use-msquery"></a><a name="query-object"></a>Så här: Använd MSQuery

Om du vill utföra en komplex fråga (inklusive sortering och växling) skapar du ett `MSQuery` objekt, direkt eller med hjälp av ett predikat:

**Mål C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift:**

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`kan du styra flera frågebeteenden.

* Ange resultatordning
* Begränsa vilka fält som ska returneras
* Begränsa hur många poster som ska returneras
* Ange totalt antal som svar
* Ange anpassade frågesträngparametrar i begäran
* Använda ytterligare funktioner

Kör `MSQuery` en fråga `readWithCompletion` genom att anropa objektet.

## <a name="how-to-sort-data-with-msquery"></a><a name="sorting"></a>Så här: Sortera data med MSQuery

Om du vill sortera resultat ska vi titta på ett exempel. Om du vill sortera efter fältet "text" stigande, sedan `MSQuery` med "fullständig" fallande, anropa så här:

**Mål C**:

```objc
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift:**

```swift
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="how-to-limit-fields-and-expand-query-string-parameters-with-msquery"></a><a name="selecting"></a><a name="parameters"></a>Så här begränsar du fält och expanderar frågesträngparametrar med MSQuery

Om du vill begränsa fält som ska returneras i en fråga anger du namnen på fälten i egenskapen **selectFields.** I det här exemplet returneras bara texten och de ifyllda fälten:

**Mål C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Swift:**

```swift
query.selectFields = ["text", "complete"]
```

Om du vill inkludera ytterligare frågesträngparametrar i serverbegäran (till exempel `query.parameters` eftersom ett anpassat serverskript använder dem) fyller du i så här:

**Mål C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift:**

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="how-to-configure-page-size"></a><a name="paging"></a>Så här konfigurerar du sidstorlek

Med Azure Mobile Apps styr sidstorleken antalet poster som hämtas samtidigt från backend-tabellerna. Ett anrop till `pull` data skulle sedan batch upp data, baserat på denna sida storlek, tills det inte finns några fler poster att dra.

Det är möjligt att konfigurera en sidstorlek med **MSPullSettings** som visas nedan. Standardsidans storlek är 50 och exemplet nedan ändrar den till 3.

Du kan konfigurera en annan sidstorlek av prestandaskäl. Om du har ett stort antal små dataposter minskar en hög sidstorlek antalet rundturer på servern.

Den här inställningen styr bara sidstorleken på klientsidan. Om klienten frågar efter en större sidstorlek än vad backend för mobilappar stöder, begränsas sidstorleken till det maximala serverdaten är konfigurerad för att stödja.

Den här inställningen är också *antalet* dataposter, inte *bytestorleken*.

Om du ökar klientsidans storlek bör du också öka sidstorleken på servern. Se ["Så här justerar du tabellväxlingsstorleken"](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) för stegen för att göra detta.

**Mål C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Swift:**

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="how-to-insert-data"></a><a name="inserting"></a>Så här infogar du data

Om du vill infoga en `NSDictionary` ny `table insert`tabellrad skapar du en och anropar . Om [Dynamiskt schema] är aktiverat genererar Azure App Service mobile-backend `NSDictionary`automatiskt nya kolumner baserat på .

Om `id` det inte anges genererar backend automatiskt ett nytt unikt ID. Ange dina `id` egna för att använda e-postadresser, användarnamn eller dina egna anpassade värden som ID. Om du tillhandahåller ditt eget ID kan det underlätta kopplingar och affärsorienterad databaslogik.

Innehåller `result` det nya objektet som infogades. Beroende på serverlogiken kan det ha ytterligare eller ändrade data jämfört med vad som skickades till servern.

**Mål C**:

```objc
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift:**

```swift
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="how-to-modify-data"></a><a name="modifying"></a>Så här ändrar du data

Om du vill uppdatera en befintlig `update`rad ändrar du ett objekt och ett anrop:

**Mål C**:

```objc
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift:**

```swift
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Du kan också ange rad-ID:et och det uppdaterade fältet:

**Mål C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift:**

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Minst måste `id` attributet anges när uppdateringar skapas.

## <a name="how-to-delete-data"></a><a name="deleting"></a>Så här tar du bort data

Om du vill ta `delete` bort ett objekt anropar du med objektet:

**Mål C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift:**

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Alternativt kan du ta bort genom att ange ett rad-ID:

**Mål C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift:**

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Minst måste `id` attributet anges när borttagningar tas bort.

## <a name="how-to-call-custom-api"></a><a name="customapi"></a>Så här: Anropa anpassat API

Med ett anpassat API kan du exponera alla serverdfunktioner. Det behöver inte mappas till en tabelloperation. Inte bara får du mer kontroll över meddelanden, kan du även läsa / ställa in rubriker och ändra svaret kroppsformat.

Anropa ett anpassat API `MSClient.invokeAPI`om du vill anropa ett anpassat API. Begäran och svarsinnehåll behandlas som JSON. Om du vill använda andra medietyper [använder du den andra överbelastningen av `invokeAPI` ][5].  Om du `GET` vill göra `POST` en begäran `HTTPMethod` `"GET"` i `body` stället `nil` för en begäran anger du parameter till och parameter till (eftersom GET-begäranden inte har meddelandekroppar.) Om ditt anpassade API stöder andra `HTTPMethod` HTTP-verb ändrar du på lämpligt sätt.

**Mål C**:

```objc
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift:**

```swift
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

## <a name="how-to-register-push-templates-to-send-cross-platform-notifications"></a><a name="templates"></a>Så här registrerar du push-mallar för att skicka meddelanden över flera plattformar

Om du vill registrera mallar skickar du mallar med metoden **client.push registerDeviceToken** i klientappen.

**Mål C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift:**

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

Mallarna är av typen NSDictionary och kan innehålla flera mallar i följande format:

**Mål C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift:**

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Alla taggar tas bort från begäran om säkerhet.  Information om hur du lägger till taggar i installationer eller mallar i installationer finns i [Arbeta med .NET-server för server för server för server för server för server för azure mobile apps][4].  Om du vill skicka meddelanden med dessa registrerade mallar arbetar du med [API:er för Notification Hubs][3].

## <a name="how-to-handle-errors"></a><a name="errors"></a>Så här: Hantera fel

När du anropar en Azure App Service-mobile backend innehåller slutförandeblocket en `NSError` parameter. När ett fel inträffar är den här parametern inte noll. I koden bör du kontrollera den här parametern och hantera felet efter behov, vilket visas i föregående kodavsnitt.

Filen [`<WindowsAzureMobileServices/MSError.h>`][6] definierar konstanterna `MSErrorResponseKey` `MSErrorRequestKey`, `MSErrorServerItemKey`och . Så här hämtar du mer data som är relaterade till felet:

**Mål C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift:**

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Dessutom definierar filen konstanter för varje felkod:

**Mål C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Swift:**

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="how-to-authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Så här: Autentiserar användare med Active Directory-autentiseringsbiblioteket

Du kan använda Active Directory Authentication Library (ADAL) för att logga in användare i ditt program med Azure Active Directory. Klientflödesautentisering med hjälp av en identitetsprovider SDK är att föredra framför att använda `loginWithProvider:completion:` metoden.  Klientflödesautentisering ger en mer inbyggd UX-känsla och möjliggör ytterligare anpassning.

1. Konfigurera din backend för mobilappen för AAD-inloggning genom att följa [självstudien Så här konfigurerar du App Service för Active Directory-inloggningsdoktor.][7] Se till att slutföra det valfria steget för att registrera ett inbyggt klientprogram. För iOS rekommenderar vi att omdirigerings-URI:n är av formuläret `<app-scheme>://<bundle-id>`. Mer information finns i [snabbstarten för ADAL iOS][8].
2. Installera ADAL med Cocoapods. Redigera din Podfile så att den innehåller följande definition och ersätt ditt projekt med namnet på **Xcode-projektet:**

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   och Pod:

        pod 'ADALiOS'

3. Med terminalen `pod install` körs den katalog som innehåller projektet och öppna sedan den genererade Xcode-arbetsytan (inte projektet).
4. Lägg till följande kod i din ansökan, enligt det språk du använder. I varje, gör dessa ersättare:

   * Ersätt **INSERT-AUTHORITY-HERE** med namnet på klienten där du har etablerat ditt program. Formatet ska https://login.microsoftonline.com/contoso.onmicrosoft.comvara . Det här värdet kan kopieras från fliken Domän i Din Azure Active Directory i [Azure-portalen].
   * Ersätt **INSERT-RESOURCE-ID-HERE** med klient-ID för din mobilapps backend. Du kan hämta klient-ID:t från fliken **Avancerat** under **Azure Active Directory-inställningar** i portalen.
   * Ersätt **INSERT-CLIENT-ID-HERE** med klient-ID som du kopierade från det inbyggda klientprogrammet.
   * Ersätt **INSERT-REDIRECT-URI-HERE** med webbplatsens *slutpunkt /.auth/login/done* med hjälp av HTTPS-schemat. Det här värdet *https://contoso.azurewebsites.net/.auth/login/done*bör liknas vid .

**Mål C**:

```objc
#import <ADALiOS/ADAuthenticationContext.h>
#import <ADALiOS/ADAuthenticationSettings.h>
// ...
- (void) authenticate:(UIViewController*) parent
            completion:(void (^) (MSUser*, NSError*))completionBlock;
{
    NSString *authority = @"INSERT-AUTHORITY-HERE";
    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
    ADAuthenticationError *error;
    ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
    authContext.parentController = parent;
    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:resourceId
                                    clientId:clientId
                                redirectUri:redirectUri
                            completionBlock:^(ADAuthenticationResult *result) {
                                if (result.status != AD_SUCCEEDED)
                                {
                                    completionBlock(nil, result.error);;
                                }
                                else
                                {
                                    NSDictionary *payload = @{
                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
                                                            };
                                    [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                }
                            }];
}
```

**Swift:**

```swift
// add the following imports to your bridging header:
//        #import <ADALiOS/ADAuthenticationContext.h>
//        #import <ADALiOS/ADAuthenticationSettings.h>

func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
    let authority = "INSERT-AUTHORITY-HERE"
    let resourceId = "INSERT-RESOURCE-ID-HERE"
    let clientId = "INSERT-CLIENT-ID-HERE"
    let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
    var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
    let authContext = ADAuthenticationContext(authority: authority, error: error)
    authContext.parentController = parent
    ADAuthenticationSettings.sharedInstance().enableFullScreen = true
    authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
            if result.status != AD_SUCCEEDED {
                completion(nil, result.error)
            }
            else {
                let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                client.loginWithProvider("aad", token: payload, completion: completion)
            }
        }
}
```

## <a name="how-to-authenticate-users-with-the-facebook-sdk-for-ios"></a><a name="facebook-sdk"></a>Så här: Autentiserar användare med Facebook SDK för iOS

Du kan använda Facebook SDK för iOS för att logga in användare i din applikation med Facebook.  Att använda en klientflödesautentisering `loginWithProvider:completion:` är att föredra framför att använda metoden.  Klientflödesautentisering ger en mer inbyggd UX-känsla och möjliggör ytterligare anpassning.

1. Konfigurera din mobilappshandledning för Facebook-inloggning genom att följa [så här konfigurerar du App Service för Facebook-inloggningshandledning.][9]
2. Installera Facebook SDK för iOS genom att följa [Facebook SDK för iOS - Komma igång-dokumentation.][10] I stället för att skapa en app kan du lägga till iOS-plattformen i din befintliga registrering.
3. Facebooks dokumentation innehåller viss Objective-C-kod i appdelegaten. Om du använder **Swift**kan du använda följande översättningar för AppDelegate.swift:

    ```swift
    // Add the following import to your bridging header:
    //        #import <FBSDKCoreKit/FBSDKCoreKit.h>

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
        // Add any custom logic here.
        return true
    }

    func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
        let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
        // Add any custom logic here.
        return handled
    }
    ```
4. Förutom att `FBSDKCoreKit.framework` lägga till i projektet, `FBSDKLoginKit.framework` lägg också till en referens till på samma sätt.
5. Lägg till följande kod i din ansökan, enligt det språk du använder.

    **Mål C**:

    ```objc
    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
                completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
            logInWithReadPermissions: @[@"public_profile"]
            fromViewController:parent
            handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
                if (error) {
                    completionBlock(nil, error);
                } else if (result.isCancelled) {
                    completionBlock(nil, error);
                } else {
                    NSDictionary *payload = @{
                                            @"access_token":result.token.tokenString
                                            };
                    [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
                }
            }];
    }
    ```

    **Swift:**

    ```swift
    // Add the following imports to your bridging header:
    //        #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //        #import <FBSDKCoreKit/FBSDKAccessToken.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }
    ```

## <a name="how-to-authenticate-users-with-twitter-fabric-for-ios"></a><a name="twitter-fabric"></a>Så här: Autentiserar användare med Twitter Fabric för iOS

Du kan använda Fabric för iOS för att logga in användare i ditt program med Twitter. Klientflödesautentisering är att `loginWithProvider:completion:` föredra framför att använda metoden, eftersom den ger en mer inbyggd UX-känsla och möjliggör ytterligare anpassning.

1. Konfigurera din mobilappshandledning för Twitter-inloggning genom att följa [hur du konfigurerar App Service för Twitter-inloggningshandledning.](../app-service/configure-authentication-provider-twitter.md)
2. Lägg till Fabric i projektet genom att följa [Fabric for iOS - Komma] igång-dokumentationen och konfigurera TwitterKit.

   > [!NOTE]
   > Som standard skapar Fabric ett Twitter-program åt dig. Du kan undvika att skapa ett program genom att registrera konsumentnyckeln och konsumenthemligheten som du skapade tidigare med hjälp av följande kodavsnitt.    Du kan också ersätta de värden för konsumentnyckel och konsumenthemlighet som du anger till App-tjänsten med de värden som visas i [instrumentpanelen fabric]. Om du väljer det här alternativet måste du ange motringningsadressen till ett platshållarvärde, till exempel `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Om du väljer att använda hemligheterna som du skapade tidigare lägger du till följande kod i appombudet:

    **Mål C**:

    ```objc
    #import <Fabric/Fabric.h>
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
        [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
        [Fabric with:@[[Twitter class]]];
        // Add any custom logic here.
        return YES;
    }
    ```

    **Swift:**

    ```swift
    import Fabric
    import TwitterKit
    // ...
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
        Fabric.with([Twitter.self])
        // Add any custom logic here.
        return true
    }
    ```

3. Lägg till följande kod i din ansökan, enligt det språk du använder.

    **Mål C**:

    ```objc
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }
    ```

    **Swift:**

    ```swift
    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }
    ```

## <a name="how-to-authenticate-users-with-the-google-sign-in-sdk-for-ios"></a><a name="google-sdk"></a>Så här: Autentiserar användare med Googles inloggnings-SDK för iOS

Du kan använda Googles inloggning sdk för iOS för att logga in användare i ditt program med ett Google-konto.  Google tillkännagav nyligen ändringar i sina OAuth-säkerhetspolicyer.  Dessa policyändringar kräver användning av Google SDK i framtiden.

1. Konfigurera din mobilappshandledning för Google-inloggning genom att följa så [här konfigurerar du App Service för Googles inloggningshandledning.](../app-service/configure-authentication-provider-google.md)
2. Installera Google SDK för iOS genom att följa [Googles inloggning för iOS - Börja integrera](https://developers.google.com/identity/sign-in/ios/start-integrating) dokumentation. Du kan hoppa över avsnittet "Autentisera med en serverdelsserver".
3. Lägg till följande i `signIn:didSignInForUser:withError:` ombudets metod enligt det språk du använder.

    **Mål C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Swift:**

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Se till att du `application:didFinishLaunchingWithOptions:` också lägger till följande i ditt appombud och ersätter "SERVER_CLIENT_ID" med samma ID som du använde för att konfigurera App Service i steg 1.

    **Mål C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Swift:**

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Lägg till följande kod i ditt program i en `GIDSignInUIDelegate` UIViewController som implementerar protokollet, enligt det språk du använder.  Du är utloggade innan du loggas in igen, och även om du inte behöver ange dina autentiseringsuppgifter igen visas en dialogruta för medgivande.  Anropa endast den här metoden när sessionstoken har upphört att gälla.

   **Mål C**:

    ```objc
    #import <Google/SignIn.h>
    // ...
    - (void)authenticate
    {
            [GIDSignIn sharedInstance].uiDelegate = self;
            [[GIDSignIn sharedInstance] signOut];
            [[GIDSignIn sharedInstance] signIn];
    }
    ```

   **Swift:**

    ```swift
    // ...
    func authenticate() {
        GIDSignIn.sharedInstance().uiDelegate = self
        GIDSignIn.sharedInstance().signOut()
        GIDSignIn.sharedInstance().signIn()
    }
    ```

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Snabbstart för Azure Mobile Apps]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure-portal]: https://portal.azure.com/
[Handling Expired Tokens]: https://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: https://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: https://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Dynamiskt schema]: https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/dynamic-schema
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Instrumentpanel för tyg]: https://www.fabric.io/home
[Tyg för iOS - Komma igång]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v2-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
