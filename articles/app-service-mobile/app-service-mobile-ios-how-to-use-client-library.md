---
title: "Så här används iOS SDK för Azure Mobile Apps"
description: "Så här används iOS SDK för Azure Mobile Apps"
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: 
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
ms.openlocfilehash: 63dd283605553297a7dc8feab90c8bcbd716d5de
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Så här används iOS-klientbiblioteket för Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Den här guiden lär du dig att utföra vanliga scenarier med senast [Azure Mobile Apps iOS SDK][1]. Om du har använt Azure Mobile Apps först slutföra [Azure Mobile Apps Snabbstart] för att skapa en serverdel, skapa en tabell och hämta en förskapad iOS Xcode-projekt. I den här guiden fokuserar vi på klientsidan iOS SDK. Mer information om SDK för serversidan för backend finns Server SDK HOWTOs.

## <a name="reference-documentation"></a>Referensdokumentation
Referensdokumentationen för klientens iOS SDK finns här: [Azure Mobile Apps iOS klienten referens][2].

## <a name="supported-platforms"></a>Plattformar som stöds
IOS SDK stöder Objective-C-projekt, Swift 2.2 projekt och Swift 2.3 projekt för iOS version 8.0 eller senare.

”Server flöde” autentisering använder en webbvy för presenterades Användargränssnittet.  Om enheten inte kunna presentera en WebView UI, kan en annan metod för autentisering krävs som inte omfattas av produkten.  
Detta SDK lämpar sig därför inte för titta på typen eller begränsade enheter på samma sätt.

## <a name="Setup"></a>Installationen och förutsättningar
Den här handboken förutsätts att du har skapat en serverdel med en tabell. Den här guiden förutsätter att tabellen har samma schema som tabellerna i dessa självstudier. Den här guiden förutsätter också att i din kod måste du referera till `MicrosoftAzureMobile.framework` och importera `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

## <a name="create-client"></a>Så här: skapa klienten
För att komma åt en Azure Mobile Apps-serverdel i projektet, skapa en `MSClient`. Ersätt `AppUrl` med app-URL. Du kan lämna `gatewayURLString` och `applicationKey` tom. Om du ställer in en gateway för autentisering fylla `gatewayURLString` med gateway-URL.

**Objective-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**SWIFT**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


## <a name="table-reference"></a>Så här: skapa tabellreferens
För att få åtkomst till eller uppdatera data skapar du en referens till serverdelstabellen. Ersätt `TodoItem` med namnet på tabellen

**Objective-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**SWIFT**:

```
let table = client.tableWithName("TodoItem")
```


## <a name="querying"></a>Så här: fråga Data
Om du vill skapa en databasfråga fråga den `MSTable` objekt. Följande fråga hämtar alla objekt i `TodoItem` och loggar texten för varje objekt.

**Objective-C**:

```
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

```
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

## <a name="filtering"></a>Så här: filtret returnerade Data
Det finns många tillgängliga alternativ för att filtrera resultat.

Om du vill filtrera med hjälp av ett predikat, använda en `NSPredicate` och `readWithPredicate`. Följande filter returnerade data för att hitta endast ofullständiga Todo-objekt.

**Objective-C**:

```
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

```
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
Om du vill utföra en komplex fråga (inklusive sortering och sidindelning) skapar du en `MSQuery` objekt, direkt eller genom att använda ett predikat:

**Objective-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**SWIFT**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`kan du styra flera olika beteenden för frågan.

* Ange ordning av resultat
* Begränsa vilka fält som ska returneras
* Begränsa hur många poster för att returnera
* Ange totalt antal i svaret
* Ange anpassad fråga string-parametrar i begäran
* Tillämpa ytterligare funktioner

Köra en `MSQuery` frågan genom att anropa `readWithCompletion` på objektet.

## <a name="sorting"></a>Så här: sortera Data med MSQuery
För att sortera resultaten ska vi titta på ett exempel. Om du vill sortera efter fältet 'text' stigande sedan efter ”klar” fallande anropa `MSQuery` t.ex:

**Objective-C**:

```
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

```
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


## <a name="selecting"></a><a name="parameters"></a>Så här: begränsa fält och expandera frågeparametrar sträng med MSQuery
Om du vill begränsa fält som ska returneras i en fråga, ange namnen på fälten i den **selectFields** egenskapen. Det här exemplet returnerar endast text och slutförda fält:

**Objective-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**SWIFT**:

```
query.selectFields = ["text", "complete"]
```

Om du vill inkludera ytterligare fråga string-parametrar i serverbegäran (till exempel eftersom ett anpassat skript för serversidan använder dem) fylla `query.parameters` t.ex:

**Objective-C**:

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**SWIFT**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Så här: Konfigurera sidstorlek
Med Azure Mobile Apps styr sidstorleken antalet poster som hämtas i taget från backend-tabeller. Ett anrop till `pull` data skulle sedan batch in data, baserat på den här sidstorleken tills det inte finns några fler poster att dra.

Det är möjligt att konfigurera en storlek med **MSPullSettings** enligt nedan. Sidan standardstorleken är 50 och exemplet nedan ändrar till 3.

Du kan konfigurera en annan sidstorlek av prestandaskäl. Om du har ett stort antal poster för små, minskar en hög sidstorlek antalet turer server.

Den här inställningen styr endast sidstorleken på klientsidan. Om klienten begär en större storlek än Mobile Apps-serverdel stöder, är sidstorleken begränsat till den maximala serverdelen är konfigurerad för att stödja.

Den här inställningen är också den *nummer* dataposter, inte den *bytestorlek*.

Om du ökar storleken på klienten, bör du också öka sidstorleken på servern. Se [”så här: justera tabellen växlingsfilens storlek”](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) stegen för att göra detta.

**Objective-C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```


**SWIFT**:

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>Så här: Infoga Data
Om du vill infoga en ny tabellrad, skapa en `NSDictionary` och anropa `table insert`. Om [dynamiska schemat] är aktiverad kan Azure App Service mobilserverdel genererar automatiskt nya kolumner som är baserat på den `NSDictionary`.

Om `id` har inte angetts serverdelen genererar automatiskt ett nytt unikt ID. Ange en egen `id` att använda e-post-adresser, användarnamn, eller dina egna anpassade värden som ID. Tillhandahåller egna ID kan underlätta kopplingar och affärsorienterade databasen logik.

Den `result` innehåller det nya objekt som har infogats. Det kan ha ytterligare eller ändrade data jämfört med vad som har skickats till servern beroende på din server-logiken.

**Objective-C**:

```
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

```
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
Om du vill uppdatera en befintlig rad, ändra ett objekt och anropet `update`:

**Objective-C**:

```
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

```
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

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**SWIFT**:

```
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

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**SWIFT**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Du kan också ta bort genom att tillhandahålla en rad-ID:

**Objective-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**SWIFT**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Åtminstone den `id` attributet måste anges när att tas bort.

## <a name="customapi"></a>Så här: anropa anpassade API
Med en anpassad API kan du exponera backend funktioner. Den har inte mappas till en Tabellåtgärd för. Inte bara gör du få bättre kontroll över meddelanden, du kan även läsa/set sidhuvuden och ändra svar body-formatet. Om du vill veta hur du skapar en anpassad API på serverdelen läsa [anpassade API: er](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

För att anropa en anpassad API, anropa `MSClient.invokeAPI`. Förfrågan och svar innehåll behandlas som JSON. Att använda andra typer av media, [Använd överlagringen för `invokeAPI` ] [ 5].  Att göra en `GET` begäran i stället för en `POST` begära parameter för `HTTPMethod` till `"GET"` och parametern `body` till `nil` (eftersom GET-begäranden saknar meddelandetext.) Om din anpassade API: et stöder andra HTTP-verb, ändra `HTTPMethod` på lämpligt sätt.

**Objective-C**:

```
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

```
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
Om du vill registrera mallar skicka mallar med din **client.push registerDeviceToken** metod i klientappen.

**Objective-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**SWIFT**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Mallarna är av typen NSDictionary och kan innehålla flera mallar i följande format:

**Objective-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**SWIFT**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Alla taggar tas bort från begäran för säkerhet.  Om du vill lägga till taggar i installationer eller mallar inom anläggningar finns [arbeta med serverdelen .NET SDK för Azure Mobile Apps][4].  Om du vill skicka meddelanden med mallarna registrerade arbeta med [Notification Hubs API: er][3].

## <a name="errors"></a>Så här: hantera fel
När du anropar en mobila serverdel i Azure App Service, slutförande blocket innehåller en `NSError` parameter. När ett fel uppstår i den här parametern är inte är nil. I koden, bör du kontrollera den här parametern och hantera fel efter behov, som visas i föregående kodfragment.

Filen [ `<WindowsAzureMobileServices/MSError.h>` ] [ 6] definierar konstanterna `MSErrorResponseKey`, `MSErrorRequestKey`, och `MSErrorServerItemKey`. Att få mer information om felet:

**Objective-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**SWIFT**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Dessutom kan definierar filen konstanter för respektive felkod:

**Objective-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**SWIFT**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Så här: autentiserar användare med Active Directory Authentication Library
Du kan använda Active Directory Authentication Library (ADAL) för att logga in användare på ditt program med Azure Active Directory. Flödet för klientautentisering med hjälp av en identitetsleverantör SDK är bättre än att använda den `loginWithProvider:completion:` metoden.  Flöde för klientautentisering ger en mer ursprungligt UX känslan och gör det möjligt för ytterligare anpassning.

1. Konfigurera mobilappsserverdelen för AAD-inloggning genom att följa den [konfigurera App Service för Active Directory-inloggningen] [ 7] kursen. Se till att slutföra det valfria steget med att registrera en native client-program. För iOS, rekommenderar vi att omdirigerings-URI: N har formatet `<app-scheme>://<bundle-id>`. Mer information finns i [ADAL iOS quickstart][8].
2. Installera ADAL med Cocoapods. Redigera din Podfile för att inkludera följande definition ersätter **din PROJECT** med namnet på ditt Xcode-projekt:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   och baljor:

        pod 'ADALiOS'
3. Använda terminalen och kör `pod install` från katalogen som innehåller ditt projekt och sedan öppna den genererade Xcode-arbetsytan (inte projektet).
4. Lägg till följande kod i ditt program, beroende på vilket språk som du använder. I varje, se dessa ersättningar:

   * Ersätt **INSERT-UTFÄRDARE-här** med namnet på klienten som du har etablerat ditt program. Formatet som ska vara https://login.microsoftonline.com/contoso.onmicrosoft.com. Det här värdet kan kopieras från fliken domän i Azure Active Directory i den [Azure-portalen].
   * Ersätt **INSERT-resurs-ID-här** med klient-ID för din mobilappsserverdel. Du kan hämta klient-ID från den **Avancerat** fliken **inställningarna för Azure Active Directory** i portalen.
   * Ersätt **INSERT-klient-ID-här** med klient-ID som du kopierade från native client-program.
   * Ersätt **INSERT-OMDIRIGERINGS-URI-här** med webbplatsens */.auth/login/done* slutpunkten, med hjälp av HTTPS-schema. Det här värdet ska vara liknar *https://contoso.azurewebsites.net/.auth/login/done*.

**Objective-C**:

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


**SWIFT**:

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

## <a name="facebook-sdk"></a>Så här: autentiserar användare med Facebook-SDK för iOS
Du kan använda Facebook SDK för iOS för att logga in användare på ditt program med hjälp av Facebook.  Med hjälp av en flödet för klientautentisering är bättre än att använda den `loginWithProvider:completion:` metoden.  Flödet klientautentisering ger en mer ursprungligt UX känslan och gör det möjligt för ytterligare anpassning.

1. Konfigurera mobilappsserverdelen för Facebook-inloggning genom att följa den [konfigurera App Service för Facebook-inloggningen] [ 9] kursen.
2. Installera Facebook-SDK för iOS genom att följa den [Facebook SDK för iOS - komma igång] [ 10] dokumentation. Istället för att skapa en app kan du lägga till iOS-plattformen befintliga registreringen.
3. Facebooks dokumentationen innehåller vissa Objective-C-koden i appen delegaten. Om du använder **Swift**, du kan använda följande översättningar för AppDelegate.swift:

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
4. Förutom att lägga till `FBSDKCoreKit.framework` till ditt projekt du också lägga till en referens till `FBSDKLoginKit.framework` på samma sätt.
5. Lägg till följande kod i ditt program, beroende på vilket språk som du använder.

**Objective-C**:

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

**SWIFT**:

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

## <a name="twitter-fabric"></a>Så här: autentiserar användare med Twitter-infrastrukturen för iOS
Du kan använda infrastruktur för iOS för att logga in användare på ditt program med Twitter. Flöde för klientautentisering är bättre än att använda den `loginWithProvider:completion:` metoden eftersom den innehåller en mer ursprungligt UX känslan och gör det möjligt för ytterligare anpassning.

1. Konfigurera mobilappsserverdelen för inloggning Twitter genom att följa den [konfigurera App Service för Twitter inloggningen](../app-service/app-service-mobile-how-to-configure-twitter-authentication.md) kursen.
2. Lägg till Fabric i projektet genom att följa den [infrastruktur för iOS - komma igång] dokumentationen och konfigurera TwitterKit.

   > [!NOTE]
   > Som standard skapas Fabric ett Twitter-program. Du kan undvika att skapa ett program genom att registrera den konsumenten och konsumenthemlighet som du skapat tidigare med följande kodavsnitt.    Alternativt kan du ersätta konsumentnyckel och konsumenthemlighet värdena som du anger till App Service med värden som visas i den [instrumentpanelen]. Om du väljer det här alternativet måste du ange URL: en motringning till ett platshållarvärde som `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.
   >
   >

    Lägg till följande kod i ombudet App om du väljer att använda hemligheter som du skapade tidigare:

    **Objective-C**:

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

    **SWIFT**:

        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
3. Lägg till följande kod i ditt program, beroende på vilket språk som du använder.

**Objective-C**:

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

**SWIFT**:

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

## <a name="google-sdk"></a>Så här: autentiserar användare med Google inloggning SDK för iOS
Du kan använda Google inloggning SDK för iOS för att logga in användare på ditt program med hjälp av ett Google-konto.  Google presenterade nyligen ändringar av deras OAuth säkerhetsprinciper.  Dessa principändringar kräver användning av Google-SDK i framtiden.

1. Konfigurera mobilappsserverdelen för Google-inloggning genom att följa den [konfigurera App Service för Google inloggningen](../app-service/app-service-mobile-how-to-configure-google-authentication.md) kursen.
2. Installera Google-SDK för iOS genom att följa den [starta Google Sign-In för iOS - integreringen](https://developers.google.com/identity/sign-in/ios/start-integrating) dokumentation. Du kan hoppa över avsnittet ”autentisera med en Backend-servern”.
3. Lägg till följande till ditt ombud `signIn:didSignInForUser:withError:` metoden enligt det språk som du använder.

**Objective-C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };

        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**SWIFT**:

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

1. Kontrollera att du också lägga till följande `application:didFinishLaunchingWithOptions:` i din app ombud kan ersätta ”SERVER_CLIENT_ID” med samma ID som du använde för att konfigurera App Service i steg 1.

**Objective-C**:

         [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";

 **SWIFT**:

        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"


1. Lägg till följande kod i ditt program i en UIViewController som implementerar det `GIDSignInUIDelegate` protokoll, beroende på vilket språk som du använder.  Du loggas innan som signeras igen och även om du inte behöver ange dina autentiseringsuppgifter igen, visas en dialogruta för medgivande.  Endast anropa denna metod när sessionstoken har gått ut.

   **Objective-C**:

       #import <Google/SignIn.h>
       // ...
       - (void)authenticate
       {
               [GIDSignIn sharedInstance].uiDelegate = self;
               [[GIDSignIn sharedInstance] signOut];
               [[GIDSignIn sharedInstance] signIn];
        }

   **SWIFT**:

       // ...
       func authenticate() {
           GIDSignIn.sharedInstance().uiDelegate = self
           GIDSignIn.sharedInstance().signOut()
           GIDSignIn.sharedInstance().signIn()
       }

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
[Azure-portalen]: https://portal.azure.com/
[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[dynamiska schemat]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[instrumentpanelen]: https://www.fabric.io/home
[infrastruktur för iOS - komma igång]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[8]: ../active-directory/active-directory-devquickstarts-ios.md
[9]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
