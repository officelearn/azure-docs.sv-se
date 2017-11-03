---
title: "Lägga till inloggning till en iOS-App med Azure AD v2.0-slutpunkten | Microsoft Docs"
description: "Hur du skapar en iOS-app som loggar in användare med både personliga Microsoft-konto och arbets-eller skolkonton med hjälp av tredjeparts-bibliotek."
services: active-directory
documentationcenter: 
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: fd3603c0-42f7-438c-87b5-a52d20d6344b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: cf1455dc3d55ea3581195f7a315556d134c23a26
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Lägga till inloggning till en iOS-app med hjälp av en tredjeparts-bibliotek med Graph API: et med v2.0-slutpunkten
Microsofts identitetsplattform använder öppna standarder som OAuth2 och OpenID Connect. Utvecklare kan använda alla bibliotek som de vill integrera med våra tjänster. Vi har skrivit några genomgång som detta att demonstrera hur du konfigurerar tredjeparts-bibliotek för att ansluta till Microsoft identity-plattformen för att hjälpa utvecklare att använda vår plattform med andra bibliotek. De flesta bibliotek som implementerar [RFC6749 OAuth2-specifikationen](https://tools.ietf.org/html/rfc6749) kan ansluta till Microsoft identity-plattformen.

Med det program som skapar den här genomgången, användare logga in i organisationen och söka efter andra i organisationen med hjälp av Graph API.

Om du har använt OAuth2 eller OpenID Connect eventuellt mycket av det här exempelkonfiguration ingen vits till dig. Vi rekommenderar att du läser [v2.0-protokoll - OAuth 2.0 auktorisering kod flöda](active-directory-v2-protocols-oauth-code.md) för bakgrunden.

> [!NOTE]
> Vissa funktioner i vår plattform som har ett uttryck i OAuth2 eller OpenID Connect standarder, till exempel villkorlig åtkomst och hantering av Intune måste du använda våra Microsoft Azure identitet bibliotek med öppen källkod.
> 
> 

V2.0-slutpunkten har inte stöd för alla Azure Active Directory-scenarier och funktioner.

> [!NOTE]
> Läs mer om för att avgöra om du ska använda v2.0-slutpunkten [v2.0 begränsningar](active-directory-v2-limitations.md).
> 
> 

## <a name="download-code-from-github"></a>Hämta koden från GitHub
Koden för den här självstudiekursen [finns på GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2).  Om du vill följa med kan du [ladda ned appens stomme som en .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) eller klona stommen:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

Du kan också hämta exempelfilerna och komma igång nu direkt:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Registrera en app
Skapa en ny app på den [programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller följer detaljerade anvisningar på [hur du registrerar en app med v2.0-slutpunkten](active-directory-v2-app-registration.md).  Se till att:

* Kopiera den **program-Id** som har tilldelats din app eftersom du behöver den snart.
* Lägg till den **Mobile** plattform för din app.
* Kopiera den **omdirigerings-URI** från portalen. Du måste använda standardvärdet för `urn:ietf:wg:oauth:2.0:oob`.

## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Hämta från tredje part NXOAuth2 bibliotek och skapa en arbetsyta
Den här genomgången använder OAuth2Client från GitHub, vilket är en OAuth2-biblioteket för Mac OS X och iOS (Cocoa och Cocoa touch). Det här biblioteket baseras på utkast 10 av OAuth2-specifikationen. Den implementerar interna programprofilen och stöder autentiseringsslutpunkt för användaren. Detta är allt du behöver integrera med Microsoft identity-plattformen.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Lägg till biblioteket i projektet med CocoaPods
CocoaPods är en beroendehanterare för Xcode-projekt. Den hanterar tidigare installationsstegen automatiskt.

```
$ vi Podfile
```
1. Lägg till följande i Podfile:
   
    ```
     platform :ios, '8.0'
   
     target 'QuickStart' do
   
     pod 'NXOAuth2Client'
   
     end
    ```
2. Läsa in podfile med CocoaPods. Då skapas en ny Xcode-arbetsyta som ska läsas in.
   
    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Utforska strukturen för projektet
Följande struktur har ställts in för projektet i stommen:

* En bakgrundsläge med en UPN-sökning
* En detaljerad vy för data om den markerade användaren
* Vyn inloggning där en användare kan logga in på appen för att fråga diagrammet

Vi flyttas till olika filer i stommen för att lägga till autentisering. Andra delar av kod, exempelvis visual koden rör inte identitet, men du.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Konfigurera settings.plst filen i biblioteket
* I snabbstartsprojektet öppnar den `settings.plist` filen. Ersätt värdena för elementen i avsnittet för att återspegla de värden som du använde i Azure-portalen. Koden ska referera till dessa värden när den används av Active Directory Authentication Library.
  * Den `clientId` är klient-ID för programmet som du kopierade från portalen.
  * Den `redirectUri` är omdirigerings-URL som angetts i portalen.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Ställ in NXOAuth2Client biblioteket i din LoginViewController
Biblioteket NXOAuth2Client kräver vissa värden för att konfigurera. Du kan använda anskaffats token för att anropa Graph API när du har slutfört uppgiften. Eftersom `LoginView` kommer att anropas när vi behöver verifiera det praktiskt att placera konfigurationsvärden i filen.

* Ska vi lägga till vissa värden till den `LoginViewController.m` fil att ange kontext för autentisering och auktorisering. Information om värden följer koden.
  
    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Nu ska vi titta på information om koden.

Den första strängen är för `scopes`.  Den `User.Read` värde kan du läsa grundläggande profilen för den inloggade användaren.

Du kan lära dig mer om alla tillgängliga scope på [behörighetsomfattningen för Microsoft Graph](https://graph.microsoft.io/docs/authorization/permission_scopes).

För `authURL`, `loginURL`, `bhh`, och `tokenURL`, bör du använda de värden som anges tidigare. Om du använder öppen källkod bibliotek för Microsoft Azure identitet, hämtar vi informationen du med hjälp av vår metadataslutpunkten. Du slipper extrahera värdena själv. Vi gör det åt dig.

`keychain`-värdet är den behållare som NXOAuth2Client-biblioteket använder för att skapa en nyckelring som lagrar dina token. Om du vill hämta enkel inloggning (SSO) över flera appar kan du ange samma nyckelringen i var och en av dina program och begäran användningen av den nyckelringen i Xcode-rättigheter. Detta förklaras i Apples dokumentation.

Resten av värdena krävs för att använda biblioteket och skapa platser för dig att utföra värden i kontexten.

### <a name="create-a-url-cache"></a>Skapa en URL-cache
I `(void)viewDidLoad()`, vilket kallas alltid efter vyn har lästs in, följande kod primes ett cacheminne för våra användning.

Lägg till följande kod:

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Skapa en webbvy för inloggning
En webbvy kan fråga användaren om faktorer som SMS textmeddelande (om konfigurerad) eller returnera felmeddelanden för användaren. Här definierar du upp webbvyn och sedan skriva kod för att hantera återanrop som sker i webbvy från tjänsterna identitet.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Åsidosätt WebView-metoderna för att hantera autentiseringen
Om du vill ge webbvyn vad som händer när en användare måste logga in som beskrivits tidigare, kan du klistra in följande kod.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Skriv koden för att hantera resultatet från OAuth2-begäran
Följande kod kommer att hantera den RedirectUrl anges som returneras från webbvyn. Om autentisering inte lyckas försöker koden igen. Under tiden ger biblioteket felet som du kan visa i konsolen eller hantera asynkront.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Konfigurera OAuth-kontexten (kallas kontoarkiv)
Här kan du anropa `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` på delade kontoarkiv för varje tjänst som du vill att programmet ska kunna få åtkomst till. Typen är en sträng som används som en identifierare för en viss tjänst. Eftersom du kommer åt Graph API koden refererar till den som `"myGraphService"`. Du ställa in en person som anger när något ändras med token. När du har fått token som du kommer tillbaka användaren tillbaka till den `masterView`.

```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Ställ in bakgrundsläge att söka efter och visa användare från Graph-API
Självstudier för många online förklarar hur du skapar en en app i Master-View-Controller (MVC) som visar returnerade data i rutnätet är utanför omfattningen för den här genomgången. Den här koden är i stommen-filen. Men behöver du hantera några saker i den här MVC-program:

* Fånga upp när användaren skriver något i sökfältet
* Ange ett objekt av data tillbaka till MasterView så att den kan visa resultaten i rutnätet

Vi ska göra de nedan.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Lägg till en kontroll för att se om du är inloggad
Programmet har liten eller om användaren inte är inloggad, så det är att kontrollera om det finns redan en token i cacheminnet. Om inte du omdirigerar till kontollen för användaren att logga in. Om du kommer ihåg det bästa sättet att utföra åtgärder när vyn läses in är att använda den `viewDidLoad()` metod som Apple ger oss.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Uppdatera tabellvyn när data tas emot
När Graph API returnerar data, måste du visa data. För enkelhetens skull är här all kod att uppdatera tabellen. Du kan bara klistra in rätt värden i MVC formaterad koden.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Ger dig ett sätt att anropa Graph API när någon skriver i sökfältet
När en användare skriver en sökning i rutan, behöver du shove som för Graph API. Den `GraphAPICaller` -klassen, som du skapar i följande kod, separerar lookup-funktioner från presentationen. Nu är det dags att skriva koden som flöden Sök tecken för Graph API. Vi kan göra detta genom att tillhandahålla en metod som kallas `lookupInGraph`, som tar den sträng som vi vill söka efter.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Skriv en hjälparklass för att komma åt Graph-API
Detta är kärnan i vårt program. Medan resten Infoga kod i MVC Standardmönster från Apple, skriva här du kod för att fråga diagrammet som användartyper och returnera dessa data. En detaljerad förklaring följer det här är koden.

### <a name="create-a-new-objective-c-header-file"></a>Skapa en ny rubrikfil i Objective C
Namn på filen `GraphAPICaller.h`, och Lägg till följande kod.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Här ser du att en angivna metoden tar en sträng och returnerar ett completionBlock. Den här completionBlock uppdateras som du kan ha gissa tabellen genom att tillhandahålla ett objekt med ifyllda data i realtid som användaren sökningar.

### <a name="create-a-new-objective-c-file"></a>Skapa en ny fil i Objective C
Namn på filen `GraphAPICaller.m`, och Lägg till följande metod.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

Vi går igenom den här metoden i detalj.

Kärnan i den här koden finns i den `NXOAuth2Request`, metod som använder parametrar som du redan har definierat i filen settings.plist.

Det första steget är att skapa rätt Graph API-anropet. Eftersom du anropar `/users`, du anger att genom att lägga till den resursen Graph API tillsammans med versionen. Det är praktiskt att placera dem i en extern inställningsfil eftersom de kan ändra som utvecklas för API: et.

```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Du måste sedan ange parametrar som du ger även Graph API-anropet. Det är *viktigt* att du inte anger parametrarna i resurs-slutpunkten eftersom som befordras för alla icke-URI förväntad tecken vid körning. All kod som frågan måste anges i parametrarna.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Du kan se detta anropar en `convertParamsToDictionary` metod som du ännu inte har skrivits. Låt oss göra nu i slutet av filen:

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
Nu ska vi använda den `NXOAuth2Request` metod för att hämta data tillbaka från API: et i JSON-format.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Slutligen kan du nu ska vi titta på hur du returnerar data till MasterViewController. Data returnerar som serialiseras och behöver avserialiseras och läsas in i ett objekt som MainViewController kan använda. För detta ändamål stommen har en `User.m/h` -fil som skapar ett användarobjekt. Du kan fylla det användarobjektet med information från diagrammet.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Köra exemplet
Om du har använt stommen eller följt tillsammans med den här genomgången ska nu ditt program att köras. Starta simulatorn och på **inloggning** att använda programmet.

## <a name="get-security-updates-for-our-product"></a>Hämta säkerhetsuppdateringar för vår produkt
Vi rekommenderar att du aktiverar aviseringar om säkerhetsincidenter genom att besöka den [säkerhet TechCenter](https://technet.microsoft.com/security/dd252948) och prenumerera på Security Advisory-aviseringar.

