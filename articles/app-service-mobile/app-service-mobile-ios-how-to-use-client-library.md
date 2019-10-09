---
title: Så här använder du iOS SDK för Azure Mobile Apps
description: Så här använder du iOS SDK för Azure Mobile Apps
services: app-service\mobile
documentationcenter: ios
author: elamalani
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 44b3259745877ddb43e643f1fa8307e100ca8b38
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025238"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Så här använder du klient biblioteket för iOS för Azure Mobile Apps

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center stöder utveckling av mobila appar från slut punkt till slut punkt och integrerade tjänster. Utvecklare kan använda **bygge**-, **test** -och **distributions** tjänster för att konfigurera kontinuerlig integrering och leverans pipeliner. När appen har distribuerats kan utvecklare övervaka status och användning av appen med hjälp av **analys** -och **diagnos** tjänster och engagera med användare med **push** -tjänsten. Utvecklare kan också utnyttja **auth** för att autentisera sina användare och **data** tjänster för att spara och synkronisera AppData i molnet.
> Om du vill integrera moln tjänster i ditt mobil program kan du registrera dig med App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) idag.

## <a name="overview"></a>Översikt
Den här guiden lär dig att utföra vanliga scenarier med hjälp av den senaste [Azure Mobile Apps iOS SDK][1]. Om du är nybörjare på Azure Mobile Apps måste du först slutföra [Azure Mobile Apps Snabbstart] för att skapa en server del, skapa en tabell och ladda ned ett fördefinierat iOS Xcode-projekt. I den här hand boken fokuserar vi på klient sidans iOS SDK. Mer information om Server sidans SDK för Server delen finns i Server SDK-HOWTOs.

## <a name="reference-documentation"></a>Referensdokumentation

Referens dokumentationen för iOS client SDK finns här: [Klient referens för Azure Mobile Apps iOS][2].

## <a name="supported-platforms"></a>Plattformar som stöds

IOS SDK stöder mål-C-projekt, Swift 2,2-projekt och SWIFT 2,3-projekt för iOS version 8,0 eller senare.

Autentiseringen "Server-Flow" använder en webbvy för det visade användar gränssnittet.  Om enheten inte kan visa användar gränssnittet för WebView krävs en annan autentiseringsmetod som ligger utanför produktens omfattning.  
Detta SDK är därför inte lämpligt för bevakade eller liknande enheter.

## <a name="Setup"></a>Konfiguration och krav

Den här guiden förutsätter att du har skapat en server del med en tabell. Den här guiden förutsätter att tabellen har samma schema som tabellerna i de här självstudierna. Den här guiden förutsätter också att du refererar `MicrosoftAzureMobile.framework` och importerar `MicrosoftAzureMobile/MicrosoftAzureMobile.h` i din kod.

## <a name="create-client"></a>Hur: Skapa klient

Skapa en `MSClient` för att få åtkomst till en Azure Mobile Apps Server del i projektet. Ersätt `AppUrl` med appens URL. Du kan lämna `gatewayURLString` och `applicationKey` tomt. Om du konfigurerar en gateway för autentisering fyller du i `gatewayURLString` med Gateway-URL: en.

**Mål-C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="table-reference"></a>Hur: Skapa tabell referens

För att få åtkomst till eller uppdatera data skapar du en referens till serverdelstabellen. Ersätt `TodoItem` med namnet på tabellen

**Mål-C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="querying"></a>Hur: Fråga efter data

Fråga `MSTable`-objektet om du vill skapa en databas fråga. Följande fråga hämtar alla objekt i `TodoItem` och loggar texten för varje objekt.

**Mål-C**:

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

**Swift**:

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

## <a name="filtering"></a>Hur: Filtrera returnerade data

Det finns många tillgängliga alternativ för att filtrera resultat.

Om du vill filtrera med ett predikat använder du en `NSPredicate` och `readWithPredicate`. Följande filter returnerade data för att hitta endast ofullständiga att göra-objekt.

**Mål-C**:

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

**Swift**:

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

## <a name="query-object"></a>Hur: Använd MSQuery

Om du vill utföra en komplex fråga (inklusive sortering och växling) skapar du ett `MSQuery`-objekt, direkt eller genom att använda ett predikat:

**Mål-C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

med `MSQuery` kan du styra flera fråge beteenden.

* Ange resultat ordning
* Begränsa vilka fält som ska returneras
* Begränsa hur många poster som ska returneras
* Ange totalt antal i svar
* Ange anpassade parametrar för frågesträngen i begäran
* Använd ytterligare funktioner

Kör en `MSQuery`-fråga genom att anropa `readWithCompletion` på objektet.

## <a name="sorting"></a>Hur: Sortera data med MSQuery

För att sortera resultaten ska vi titta på ett exempel. Om du vill sortera efter fält "text", sedan efter "Complete", anropar du `MSQuery` så här:

**Mål-C**:

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

**Swift**:

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

## <a name="selecting"></a><a name="parameters"></a>Hur: Begränsa fält och expandera Frågesträngs parametrar med MSQuery

Om du vill begränsa vilka fält som ska returneras i en fråga anger du namnen på fälten i egenskapen **selectFields** . I det här exemplet returneras endast fälten text och slutförda:

**Mål-C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```swift
query.selectFields = ["text", "complete"]
```

Om du vill inkludera ytterligare parametrar för frågesträngar i serverbegäran (till exempel på grund av ett anpassat skript på Server sidan använder dem) fyller du i `query.parameters` så här:

**Mål-C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Hur: Konfigurera sid storlek

Med Azure Mobile Apps kontrollerar sid storleken antalet poster som hämtas i taget från Server dels tabellerna. Ett anrop till `pull`-data skulle sedan skapa data, baserat på den här sid storleken, tills det inte finns några fler poster att hämta.

Det är möjligt att konfigurera en sid storlek med **MSPullSettings** som visas nedan. Standard sid storleken är 50 och exemplet nedan ändrar den till 3.

Du kan konfigurera en annan sid storlek av prestanda skäl. Om du har ett stort antal små data poster minskar en hög sid storlek antalet servers tur och-svar.

Den här inställningen styr bara sid storleken på klient sidan. Om klienten frågar efter en större sid storlek än den Mobile Apps backend-servern har stöd för, är sid storleken ett tak på den maximala Server delen som är konfigurerad för att stödja.

Den här inställningen är också *antalet* data poster, inte byte- *storlek*.

Om du ökar klient sidans storlek bör du också öka sid storleken på servern. Se [ "How to: Justera tabellens sid storlek "](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) för att göra detta.

**Mål-C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Swift**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Hur: Infoga data

Om du vill infoga en ny tabell rad skapar du en `NSDictionary` och anropar `table insert`. Om [dynamiskt schema] är aktiverat genererar Azure App Service mobil Server del automatiskt nya kolumner baserat på `NSDictionary`.

Om `id` inte anges genererar Server delen automatiskt ett nytt unikt ID. Ange din egen `id` om du vill använda e-postadresser, användar namn eller dina egna anpassade värden som-ID. Att tillhandahålla ditt eget ID kan under lätta anslutningarna och affärs orienterad databas logik.

@No__t-0 innehåller det nya objektet som infogades. Beroende på din server logik kan det finnas ytterligare eller ändrade data jämfört med vad som skickats till servern.

**Mål-C**:

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

**Swift**:

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

## <a name="modifying"></a>Hur: Ändra data

Om du vill uppdatera en befintlig rad ändrar du ett objekt och anropar `update`:

**Mål-C**:

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

**Swift**:

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

Alternativt kan du ange rad-ID och uppdaterat fält:

**Mål-C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Som minimum måste attributet `id` anges när uppdateringar görs.

## <a name="deleting"></a>Hur: Ta bort data

Om du vill ta bort ett objekt, anropa `delete` med objektet:

**Mål-C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Du kan också ta bort genom att ange ett rad-ID:

**Mål-C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Som minimum måste attributet `id` anges när du gör borttagningar.

## <a name="customapi"></a>Hur: Anropa anpassat API

Med ett anpassat API kan du exponera Server dels funktioner. Den behöver inte mappas till en tabell åtgärd. Om du inte bara får mer kontroll över meddelanden kan du till och med läsa/ange rubriker och ändra text formatet för svar.

Anropa `MSClient.invokeAPI` om du vill anropa en anpassad API. Innehållet i begäran och svaret behandlas som JSON. Om du vill använda andra medie typer [använder du den andra överlagringen för `invokeAPI`][5].  Om du vill göra en `GET`-begäran i stället för en `POST`-begäran anger du parametern `HTTPMethod` till `"GET"` och parametern `body` till `nil` (eftersom GET-begäranden inte har meddelande texter). Om ditt anpassade API har stöd för andra HTTP-verb, ändra `HTTPMethod` på lämpligt sätt.

**Mål-C**:

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

**Swift**:

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

## <a name="templates"></a>Hur: Registrera push-mallar för att skicka meddelanden mellan plattformar

För att registrera mallar, passa mallar med din **klient. push-registerDeviceToken** -Metod i klient programmet.

**Mål-C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

Mallarna är av typen NSDictionary och kan innehålla flera mallar i följande format:

**Mål-C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Alla Taggar tas bort från begäran om säkerhet.  Information om hur du lägger till taggar till installationer eller mallar i installationer finns i [arbeta med .NET-Server del Server SDK för Azure Mobile Apps][4].  Om du vill skicka meddelanden med hjälp av dessa registrerade mallar kan du arbeta med [Notification Hubs API: er][3].

## <a name="errors"></a>Hur: Hantera fel

När du anropar en Azure App Service mobil Server del innehåller slut för ande blocket en `NSError`-parameter. När ett fel inträffar är den här parametern icke-Nil. I din kod bör du kontrol lera den här parametern och hantera felet efter behov, som visas i föregående kodfragment.

Filen [`<WindowsAzureMobileServices/MSError.h>`][6] definierar konstanterna `MSErrorResponseKey`, `MSErrorRequestKey` och `MSErrorServerItemKey`. Hämta mer information om felet:

**Mål-C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Dessutom definierar filen konstanter för varje felkod:

**Mål-C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Hur: Autentisera användare med Active Directory-autentiseringsbibliotek

Du kan använda Active Directory-autentiseringsbibliotek (ADAL) för att logga användare i ditt program med Azure Active Directory. Autentisering med klient flöde med hjälp av en Identity Provider SDK är ett bättre sätt att använda metoden `loginWithProvider:completion:`.  Autentisering med klient flöde ger en mer enhetlig känsla och möjliggör ytterligare anpassning.

1. Konfigurera din server del för mobilappen för AAD-inloggning genom att följa själv studie kursen [konfigurera App Service för Active Directory inloggning][7] . Se till att slutföra det valfria steget när du registrerar ett internt klient program. För iOS rekommenderar vi att omdirigerings-URI: n har formen `<app-scheme>://<bundle-id>`. Mer information finns i snabb starten för [ADAL iOS][8].
2. Installera ADAL med Cocoapods. Redigera din Podfile för att inkludera följande definition, och ersätt projektet med namnet på ditt Xcode **-** projekt:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   och pod:

        pod 'ADALiOS'

3. Använd terminalen och kör `pod install` från katalogen som innehåller ditt projekt och öppna sedan den genererade Xcode-arbetsytan (inte projektet).
4. Lägg till följande kod i programmet, enligt det språk som du använder. I vart och ett, gör du följande ersättningar:

   * Ersätt **insert-Authority – här** visas namnet på den klient där du etablerade ditt program. Formatet ska vara https://login.microsoftonline.com/contoso.onmicrosoft.com. Det här värdet kan kopieras från fliken domän i Azure Active Directory i [Azure Portal].
   * Ersätt **insert-Resource-ID – här** med klient-ID: t för Server delen för mobilappen. Du kan hämta klient-ID: t från fliken **Avancerat** under **Azure Active Directory inställningar** i portalen.
   * Ersätt **insert-Client-ID – här** med det klient-ID som du kopierade från det interna klient programmet.
   * Ersätt **insert-Redirect-URI – här** med platsens */.auth/login/Done* -slutpunkt, med hjälp av https-schemat. Värdet bör likna *https://contoso.azurewebsites.net/.auth/login/done* .

**Mål-C**:

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

**Swift**:

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

## <a name="facebook-sdk"></a>Hur: Autentisera användare med Facebook SDK för iOS

Du kan använda Facebook SDK för iOS för att logga användare i ditt program med Facebook.  Användning av en klient flödes autentisering är att föredra att använda metoden `loginWithProvider:completion:`.  Autentiseringen av klient flödet ger en mer enhetlig känsla och möjliggör ytterligare anpassning.

1. Konfigurera din server del för mobilappen för Facebook-inloggning genom att följa själv studie kursen [konfigurera App Service för Facebook-inloggning][9] .
2. Installera Facebook SDK för iOS genom att följa [Facebook SDK för iOS-komma igång-][10] dokumentationen. I stället för att skapa en app kan du lägga till iOS-plattformen till din befintliga registrering.
3. Facebook-dokumentationen innehåller viss mål-C-kod i appens ombud. Om du använder **Swift**kan du använda följande översättningar för AppDelegate. SWIFT:

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
4. Förutom att lägga till `FBSDKCoreKit.framework` i projektet, kan du också lägga till en referens till `FBSDKLoginKit.framework` på samma sätt.
5. Lägg till följande kod i programmet, enligt det språk som du använder.

    **Mål-C**:

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

    **Swift**:

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

## <a name="twitter-fabric"></a>Hur: Autentisera användare med Twitter Fabric för iOS

Du kan använda Fabric för iOS för att logga användare i ditt program med hjälp av Twitter. Autentisering med klient flöde är att föredra att använda metoden `loginWithProvider:completion:`, eftersom den ger en mer enhetlig känsla och möjliggör ytterligare anpassning.

1. Konfigurera Server delen för mobilappen för Twitter-inloggning genom att följa själv studie kursen [konfigurera App Service för Twitter-inloggning](../app-service/configure-authentication-provider-twitter.md) .
2. Lägg till infrastruktur resurser i projektet genom att följa [Infrastruktur resurs för iOS – Komma igång] dokumentation och konfigurera TwitterKit.

   > [!NOTE]
   > Som standard skapar Fabric ett Twitter-program åt dig. Du kan undvika att skapa ett program genom att registrera konsument nyckeln och den konsument hemlighet som du skapade tidigare med hjälp av följande kodfragment.    Alternativt kan du ersätta konsument nyckeln och de konsument hemliga värden som du anger för att App Service med de värden som visas i [Instrument panel för infrastruktur resurser]. Om du väljer det här alternativet måste du ange en URL för återanrop till ett plats hållare, till exempel `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Om du väljer att använda de hemligheter som du skapade tidigare, lägger du till följande kod i appens ombud:

    **Mål-C**:

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

    **Swift**:

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

3. Lägg till följande kod i programmet, enligt det språk som du använder.

    **Mål-C**:

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

    **Swift**:

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

## <a name="google-sdk"></a>Hur: Autentisera användare med Google-inloggning SDK för iOS

Du kan använda Google-inloggning SDK för iOS för att logga in användare i ditt program med ett Google-konto.  Google har nyligen lanserat ändringar i sina OAuth-säkerhetsprinciper.  Dessa princip ändringar kräver att Google SDK används i framtiden.

1. Konfigurera din server del för mobilappen för Google-inloggning genom att följa själv studie kursen [konfigurera App Service för Google-inloggning](../app-service/configure-authentication-provider-google.md) .
2. Installera Google SDK för iOS genom att följa den [Google-inloggningen för iOS-börja integrera](https://developers.google.com/identity/sign-in/ios/start-integrating) dokumentation. Du kan hoppa över avsnittet "autentisera med en backend-server".
3. Lägg till följande i ditt ombuds `signIn:didSignInForUser:withError:`-metod enligt det språk som du använder.

    **Mål-C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Swift**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. Se till att du även lägger till följande i `application:didFinishLaunchingWithOptions:` i appens ombud, och Ersätt "SERVER_CLIENT_ID" med samma ID som du använde för att konfigurera App Service i steg 1.

    **Mål-C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Swift**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. Lägg till följande kod i programmet i en UIViewController som implementerar `GIDSignInUIDelegate`-protokollet, enligt det språk som du använder.  Du är utloggad innan du loggar in igen och även om du inte behöver ange dina autentiseringsuppgifter igen visas en dialog ruta för medgivande.  Anropa bara den här metoden när sessionstoken har upphört att gälla.

   **Mål-C**:

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

   **Swift**:

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
[Azure Mobile Apps Snabbstart]: app-service-mobile-ios-get-started.md

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
[Dynamiskt schema]: https://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Instrument panel för infrastruktur resurser]: https://www.fabric.io/home
[Infrastruktur resurs för iOS – Komma igång]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v1-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
