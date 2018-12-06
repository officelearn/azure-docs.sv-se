---
title: Så här används iOS SDK för Azure Mobile Apps
description: Så här används iOS SDK för Azure Mobile Apps
services: app-service\mobile
documentationcenter: ios
author: conceptdev
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 8eef75a6e1f4f05aa6d7ce8f9e6fdda52162d0bc
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960727"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Så här används iOS-klientbiblioteket för Azure Mobile Apps

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Den här guiden lär du dig att utföra vanliga scenarier med senast [Azure Mobile Apps iOS SDK][1]. Om du är nybörjare på Azure Mobile Apps slutföra [Azure mobila appar Snabbstart] för att skapa en serverdel, skapa en tabell och ladda ned ett färdiga iOS Xcode-projekt. I den här guiden fokuserar vi på klientsidan iOS SDK. Mer information om SDK för serversidan för serverdelen finns Server SDK HOWTOs.

## <a name="reference-documentation"></a>Referensdokumentation

Referensdokumentation för SDK för iOS-klient finns här: [Azure Mobile Apps iOS referens för klienthantering][2].

## <a name="supported-platforms"></a>Plattformar som stöds

IOS SDK stöder projekt Objective-C, Swift 2.2 projekt och Swift 2.3 projekt för iOS version 8.0 eller senare.

”Server-flöde” autentiseringen använder en webbvy för uppvisade Användargränssnittet.  Om enheten inte är kunna presentera en WebView UI och sedan en annan metod för autentisering måste anges som inte omfattas av produkten.  
Denna SDK är därför inte lämpligt för Watch-type eller begränsade enheter på samma sätt.

## <a name="Setup"></a>Installation och krav

Den här guiden förutsätter att du har skapat en serverdel med en tabell. Den här handboken förutsätts att tabellen har samma schema som tabellerna i dessa självstudier. Den här guiden förutsätter också att i din kod du referera till `MicrosoftAzureMobile.framework` och importera `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>Så här: skapa klient

Om du vill få åtkomst till en Azure Mobile Apps-serverdel i projektet måste skapa en `MSClient`. Ersätt `AppUrl` med app-URL. Du kan lämna `gatewayURLString` och `applicationKey` tom. Om du har konfigurerat en gateway för autentisering kan du fylla i `gatewayURLString` med gateway-URL.

**Objective-C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**SWIFT**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="table-reference"></a>Så här: skapa tabellreferens

För att få åtkomst till eller uppdatera data skapar du en referens till serverdelstabellen. Ersätt `TodoItem` med namnet på tabellen

**Objective-C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**SWIFT**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="querying"></a>Anvisning: fråga efter Data

Om du vill skapa en databasfråga fråga den `MSTable` objekt. Följande fråga hämtar alla objekt i `TodoItem` och loggar texten för varje objekt.

**Objective-C**:

```objc
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**SWIFT**:

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

## <a name="filtering"></a>Så här: Filter returnerade Data

Det finns många tillgängliga alternativ för att filtrera resultaten.

Om du vill filtrera med hjälp av ett predikat, använda en `NSPredicate` och `readWithPredicate`. Följande filter returnerade data för att hitta endast ofullständig Todo-objekt.

**Objective-C**:

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

**SWIFT**:

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

## <a name="query-object"></a>Så här: använda MSQuery

Om du vill utföra en komplex fråga (inklusive sortering och växling) skapa en `MSQuery` objekt, direkt eller genom att använda ett predikat:

**Objective-C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**SWIFT**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery` kan du styra flera fråga beteenden.

* Ange ordning av resultat
* Begränsa vilka fält som ska returneras
* Begränsa hur många poster som ska returneras
* Ange totalt antal svar
* Ange anpassade parametrar för frågesträngen i begäran
* Använda ytterligare funktioner

Köra en `MSQuery` frågan genom att anropa `readWithCompletion` på objektet.

## <a name="sorting"></a>Så här: sortera Data med MSQuery

Om du vill sortera resultaten, låt oss titta på ett exempel. Om du vill sortera i stigande fältet ”text” och sedan efter ”klar” fallande, anropa `MSQuery` t.ex:

**Objective-C**:

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

**SWIFT**:

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

## <a name="selecting"></a><a name="parameters"></a>Så här: begränsa fält och expandera frågesträngparametrar med MSQuery

För att begränsa fält som ska returneras i en fråga, ange namnen på fälten i den **selectFields** egenskapen. Det här exemplet returnerar endast text och slutförda fält:

**Objective-C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**SWIFT**:

```swift
query.selectFields = ["text", "complete"]
```

Om du vill inkludera ytterligare parametrar för frågesträngen i serverbegäran (till exempel eftersom ett anpassat skript på serversidan använder dem) fylla i `query.parameters` t.ex:

**Objective-C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**SWIFT**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Så här: Konfigurera sidstorlek

Med Azure Mobile Apps styr sidstorleken antalet poster som hämtas i taget från backend-tabeller. Ett anrop till `pull` data skulle sedan batch upp data, baserat på den här sidstorlek, tills det finns inga fler poster att hämta.

Det är möjligt att konfigurera en sida storlek med hjälp av **MSPullSettings** enligt nedan. Standardsidstorleken 50 och ändrar i exemplet nedan till 3.

Du kan konfigurera en annan storlek av prestandaskäl. Om du har ett stort antal små dataposter minskar antalet turer server i en hög storlek.

Den här inställningen styr endast sidstorleken på klientsidan. Om klienten begär en större storlek än vad som har stöd för Mobile Apps-serverdel, är sidstorleken begränsat till det högsta serverdelen är konfigurerad för att stödja.

Den här inställningen är också den *nummer* av dataposter, inte den *bytestorlek*.

Om du ökar storleken på klienten, bör du även öka sidstorleken på servern. Se [”så här: justera sidindelning tabellstorleken”](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) anvisningar om hur du gör detta.

**Objective-C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**SWIFT**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Anvisning: Infoga Data

Om du vill infoga en ny tabellrad, skapa en `NSDictionary` och anropa `table insert`. Om [dynamiskt Schema] är aktiverad, Azure App Service-mobilserverdel genererar automatiskt nya kolumner baserat på den `NSDictionary`.

Om `id` har inte angetts serverdelen genererar automatiskt ett nytt unikt ID. Ange din egen `id` att använda e-post-adresser, användarnamn, eller dina egna anpassade värden som ID. Att tillhandahålla en egen ID kan underlätta kopplingar och affärsorienterade databasen logik.

Den `result` innehåller det nya objektet som har infogats. Det kan ha ytterligare eller ändrade data jämfört med vad som har skickats till servern beroende på din serverlogik.

**Objective-C**:

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

**SWIFT**:

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

## <a name="modifying"></a>Så här: ändra Data

Om du vill uppdatera en befintlig rad, ändra ett objekt och anropa `update`:

**Objective-C**:

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

**SWIFT**:

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

Du kan också ange rad-ID och det uppdaterade fältet:

**Objective-C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**SWIFT**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Åtminstone den `id` attributet måste anges när du gör uppdateringar.

## <a name="deleting"></a>Så här: ta bort Data

Ta bort ett objekt genom att anropa `delete` med objektet:

**Objective-C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**SWIFT**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Du kan också ta bort genom att tillhandahålla ett rad-ID:

**Objective-C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**SWIFT**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Åtminstone den `id` attributet måste anges när att göra tar bort.

## <a name="customapi"></a>Så här: anropa anpassade API

Du kan använda ett anpassat API för att lägga upp heller serverdel. Det behöver inte mappas till en Tabellåtgärd. Inte bara får du större kontroll över meddelanden, du kan även läsa/set rubriker och ändra brödtext svarsformat. Läs hur du skapar ett anpassat API på serverdelen [anpassade API: er](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

För att anropa en anpassad API kan anropa `MSClient.invokeAPI`. Begäran och svaret innehåll behandlas som JSON. Att använda andra medietyper [Använd överlagringen för `invokeAPI` ] [ 5].  Att göra en `GET` begär i stället för en `POST` begära en parameter för `HTTPMethod` till `"GET"` och parametern `body` till `nil` (eftersom GET-begäranden inte har meddelandekroppen.) Om ditt anpassade API stöder andra HTTP-verb, ändra `HTTPMethod` på rätt sätt.

**Objective-C**:

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

**SWIFT**:

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

## <a name="templates"></a>Så här: registrera push-mallar för att skicka plattformsoberoende meddelanden

Registrera mallar genom att skicka mallar med din **client.push registerDeviceToken** -metod i klientappen.

**Objective-C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**SWIFT**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

Mallarna är av typen NSDictionary och kan innehålla flera mallar i följande format:

**Objective-C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**SWIFT**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Alla taggar tas bort från begäran för säkerhet.  Om du vill lägga till taggar till installationer eller mallar i installationer, se [arbeta med SDK för .NET-serverdelen för Azure Mobile Apps][4].  Om du vill skicka meddelanden med hjälp av dessa registrerade mallar, arbeta med [Notification Hubs API: er][3].

## <a name="errors"></a>Så här: hantera fel

När du anropar en mobil serverdel i Azure App Service, slutförande blocket innehåller en `NSError` parametern. När ett fel uppstår, är den här parametern inte är noll. I din kod, bör du kontrollera den här parametern och hantera felet efter behov, som visas i föregående kodfragment.

Filen [ `<WindowsAzureMobileServices/MSError.h>` ] [ 6] definierar konstanterna `MSErrorResponseKey`, `MSErrorRequestKey`, och `MSErrorServerItemKey`. Att få mer information om felet:

**Objective-C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**SWIFT**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Dessutom kan definierar filen konstanter för respektive felkod:

**Objective-C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**SWIFT**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Så här: autentisera användare med Active Directory Authentication Library

Du kan använda Active Directory Authentication Library (ADAL) för att registrera användare i ditt program med Azure Active Directory. Flow klientautentisering med hjälp av en identitetsprovider SDK är bättre än att använda den `loginWithProvider:completion:` metoden.  Flow klientautentisering ger en mer interna UX-design och tillåter för ytterligare anpassning.

1. Konfigurera mobilappsserverdelen för AAD-inloggningen genom att följa den [så här konfigurerar du App Service för Active Directory-inloggning] [ 7] självstudien. Se till att slutföra det valfria steget med att registrera ett internt klientprogram. För iOS-, rekommenderar vi att omdirigerings-URI: N är i formatet `<app-scheme>://<bundle-id>`. Mer information finns i den [ADAL iOS Snabbstart][8].
2. Installera ADAL med Cocoapods. Redigera din Podfile för att inkludera följande definition ersätta **YOUR-projekt** med namnet på ditt Xcode-projekt:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   och en Pod:

        pod 'ADALiOS'

3. Använda terminalen kör `pod install` från katalogen som innehåller ditt projekt och öppna den genererade Xcode-arbetsytan (inte projektet).
4. Lägg till följande kod i ditt program, beroende på vilket språk som du använder. Se dessa ersättningar i varje:

   * Ersätt **INSERT-UTFÄRDARE-HERE** med namnet på den klient som du har etablerat för ditt program. Formatet ska vara https://login.microsoftonline.com/contoso.onmicrosoft.com. Det här värdet kan kopieras från fliken domän i Azure Active Directory i den [Azure Portal].
   * Ersätt **INSERT-resurs-ID-HERE** med klient-ID för din mobilappsserverdel. Du kan hämta klient-ID från den **Avancerat** fliken **Azure Active Directory-inställningar** i portalen.
   * Ersätt **INSERT-klient-ID-HERE** med klient-ID som du kopierade från native client-program.
   * Ersätt **INSERT-OMDIRIGERINGS-URI-HERE** med webbplatsens */.auth/login/done* slutpunkten, med hjälp av HTTPS-schema. Det här värdet ska vara liknar *https://contoso.azurewebsites.net/.auth/login/done*.

**Objective-C**:

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

**SWIFT**:

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

## <a name="facebook-sdk"></a>Så här: autentisera användare med Facebook-SDK för iOS

Du kan använda med Facebook SDK för iOS för att registrera användare i ditt program med Facebook.  Med hjälp av ett flöde klientautentisering är bättre än att använda den `loginWithProvider:completion:` metoden.  Flow klientautentisering ger en mer interna UX-design och tillåter för ytterligare anpassning.

1. Konfigurera mobilappsserverdelen för Facebook-inloggning genom att följa den [så här konfigurerar du App Service för Facebook-inloggning] [ 9] självstudien.
2. Installera Facebook-SDK för iOS genom att följa den [Facebook SDK för iOS – komma igång] [ 10] dokumentation. Istället för att skapa en app kan du lägga till iOS-plattformen i din befintliga registrering.
3. Facebooks dokumentationen innehåller vissa Objective-C-koden i App-delegaten. Om du använder **Swift**, du kan använda följande översättningar för AppDelegate.swift:

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
4. Förutom att lägga till `FBSDKCoreKit.framework` till ditt projekt du också lägga till en referens till `FBSDKLoginKit.framework` på samma sätt.
5. Lägg till följande kod i ditt program, beroende på vilket språk som du använder.

    **Objective-C**:

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

    **SWIFT**:

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

## <a name="twitter-fabric"></a>Så här: autentisera användare med Twitter Fabric för iOS

Du kan använda infrastruktur för iOS för att registrera användare i ditt program med Twitter. Flow klientautentisering är bättre än att använda den `loginWithProvider:completion:` metod, eftersom den innehåller en mer interna UX-design och gör att för ytterligare anpassning.

1. Konfigurera mobilappsserverdelen för Twitter-inloggningen genom att följa den [så här konfigurerar du App Service för Twitter-inloggning](../app-service/app-service-mobile-how-to-configure-twitter-authentication.md) självstudien.
2. Lägg till Fabric i projektet genom att följa den [Infrastruktur för iOS – komma igång] dokumentation och hur du konfigurerar TwitterKit.

   > [!NOTE]
   > Som standard skapar Fabric ett Twitter-program. Du kan undvika att skapa ett program genom att registrera använda nyckeln och Konsumenthemligheten som du skapat tidigare med följande kodavsnitt.    Alternativt kan du ersätta de använda nyckeln och Konsumenthemligheten värdena som du anger till App Service med de värden som du ser i den [Instrumentpanelen för infrastrukturresurser]. Om du väljer det här alternativet måste du ange Webbadressen för återanrop ett platshållarvärde som `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Om du väljer att använda hemligheter som du skapade tidigare, lägger du till följande kod till ditt ombud för appen:

    **Objective-C**:

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

    **SWIFT**:

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

3. Lägg till följande kod i ditt program, beroende på vilket språk som du använder.

    **Objective-C**:

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

    **SWIFT**:

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

## <a name="google-sdk"></a>Så här: autentisera användare med Google-inloggning SDK för iOS

Du kan använda med Google-inloggning SDK för iOS för att registrera användare i ditt program med ett Google-konto.  Google meddelade nyligen ändringar i sina OAuth-säkerhetsprinciper.  Dessa principändringar kräver användning av Google-SDK i framtiden.

1. Konfigurera mobilappsserverdelen för Google-inloggningen genom att följa den [så här konfigurerar du App Service för Google-inloggning](../app-service/app-service-mobile-how-to-configure-google-authentication.md) självstudien.
2. Installera Google-SDK för iOS genom att följa den [Google Sign-In för iOS - börja integrera](https://developers.google.com/identity/sign-in/ios/start-integrating) dokumentation. Du kan hoppa över avsnittet ”autentisera med ett Backend-servern”.
3. Lägg till följande till ditt ombud `signIn:didSignInForUser:withError:` metod, beroende på vilket språk som du använder.

    **Objective-C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **SWIFT**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Kontrollera att du också lägga till följande för att `application:didFinishLaunchingWithOptions:` i din app ombud, Ersätt ”SERVER_CLIENT_ID” med samma ID som användes för att konfigurera App Service i steg 1.

    **Objective-C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **SWIFT**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Lägg till följande kod i ditt program i en UIViewController som implementerar den `GIDSignInUIDelegate` -protokollet, beroende på vilket språk som du använder.  Du har loggats ut innan som signeras igen och även om du inte behöver ange dina autentiseringsuppgifter igen kan du se en dialogruta för medgivande.  Bara anropa den här metoden när sessionstoken har upphört att gälla.

   **Objective-C**:

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

   **SWIFT**:

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
[Azure mobila appar Snabbstart]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure Portal]: https://portal.azure.com/
[Handling Expired Tokens]: https://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: https://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: https://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Dynamiskt Schema]: https://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Instrumentpanelen för infrastrukturresurser]: https://www.fabric.io/home
[Infrastruktur för iOS – komma igång]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[8]:../active-directory/develop/quickstart-v1-ios.md
[9]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
