---
title: "Azure Active Directory B2C: Anropa ett webb-API från ett iOS-program med hjälp av ett bibliotek från tredje part | Microsoft Docs"
description: "Den här artikeln beskriver hur du skapar en iOS-app för en ”att göra”-lista som anropar ett Node.js-webb-API med OAuth 2.0-ägartoken med hjälp av ett bibliotek från tredje part"
services: active-directory-b2c
documentationcenter: ios
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d818a634-42c2-4cbd-bf73-32fa0c8c69d3
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: objectivec
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 0175f4e83aace12d8e4607f2ad924893093c6734
ms.openlocfilehash: cc5e199816668a5a0f936019ab8096e93a7a2f5a


---
# <a name="azure-ad-b2c--call-a-web-api-from-an-ios-application-using-a-third-party-library"></a>Azure AD B2C: Anropa ett webb-API från en iOS-app med hjälp av ett bibliotek från en tredje part
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Microsofts identitetsplattform använder öppna standarder som OAuth2 och OpenID Connect. Det innebär att utvecklare kan utnyttja alla bibliotek som de vill integrera med våra tjänster. Vi har skrivit några genomgångar som den här för att hjälpa utvecklare att använda vår plattform och demonstrera hur de kan konfigurera bibliotek från tredje part för att ansluta till Microsoft Identity-plattformen. De flesta bibliotek som implementerar [RFC6749 OAuth2-specifikationen](https://tools.ietf.org/html/rfc6749) kan ansluta till Microsoft Identity-plattformen.

Om du inte har erfarenhet av OAuth2 eller OpenID Connect kanske du inte får ut så mycket av den här exempelkonfigurationen. Vi rekommenderar att du läser en kort [översikt över protokollet som vi har dokumenterat här](active-directory-b2c-reference-protocols.md).

> [!NOTE]
> Vissa funktioner för vår plattform som använder dessa standarder, till exempel Villkorlig åtkomst och Intune-principhantering, kräver att du använder våra Microsoft Azure-identitetsbibliotek med öppen källkod.
>
>

Alla Azure Active Directory-scenarier och Azure AD-funktioner stöds inte av B2C-plattformen.  Information som hjälper dig att avgöra om du ska använda B2C-plattformen finns i [B2C-begränsningar](active-directory-b2c-limitations.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Skaffa en Azure AD B2C-katalog
Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient. En katalog är en behållare för alla användare, appar, grupper och mer. Om du inte redan har en [skapar du en B2C-katalog](active-directory-b2c-get-started.md) innan du fortsätter.

## <a name="create-an-application"></a>Skapa ett program
Därefter måste du skapa en app i B2C-katalogen. Det ger Azure AD den information som det behöver för att kommunicera säkert med din app. I det här fallet representeras både appen och webb-API:et av ett enda **program-ID** eftersom de omfattar en logisk app. Du skapar en app genom att följa [dessa anvisningar](active-directory-b2c-app-registration.md). Se till att:

* Ta med en **mobil enhet** i programmet.
* Kopiera **program-ID:t** som har tilldelats din app. Du behöver även detta senare.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Skapa principer
I Azure AD B2C definieras varje användarupplevelse av en [princip](active-directory-b2c-reference-policies.md). Den här appen innehåller en identitetslösning: en kombinerad inloggning och registrering. Du måste skapa den här principen för varje typ. Mer information finns i [referensartikeln om principer](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Tänk på följande när du skapar principen:

* Välj **Visningsnamn** och registreringsattribut i principen.
* Välj det **visningsnamn** och **objekt-ID** som programmet gör anspråk på i varje princip. Du kan också välja andra anspråk.
* Kopiera **namnet** på varje princip när du har skapat den. Det bör ha prefixet `b2c_1_`.  Du behöver principnamnet senare.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

När du har skapat dina principer är det dags att bygga appen.

## <a name="download-the-code"></a>Ladda ned koden
Koden för den här självstudiekursen [finns på GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c).  Om du vill följa med i koden kan du [ladda ned appen som en .zip](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)/archive/master.zip) eller klona den:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

Eller så laddar du bara ned den färdiga koden och sätter igång direkt:

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## <a name="download-the-third-party-library-nxoauth2-and-launch-a-workspace"></a>Ladda ned tredjepartsbiblioteket nxoauth2 och starta en arbetsyta
I den här genomgången ska vi använda OAuth2Client från GitHub, ett OAuth2-bibliotek för Mac OS X och iOS (Cocoa & Cocoa touch). Det här biblioteket baseras på utkast 10 av OAuth2-specifikationen. Det implementerar den interna programprofilen och stöder slutpunkten för auktorisering av slutanvändaren. Det här är allt vi behöver för att integrera med Microsofts identitetsplattform.

### <a name="adding-the-library-to-your-project-using-cocoapods"></a>Lägga till i biblioteket i projektet med hjälp av CocoaPods
CocoaPods är en beroendehanterare för Xcode-projekt. Den hanterar installationsstegen ovan automatiskt.

```
$ vi Podfile
```
Lägg till följande i Podfile:

```
 platform :ios, '8.0'

 target 'SampleforB2C' do

 pod 'NXOAuth2Client'

 end
```

Fortsätt och läs in Podfile med CocoaPods. Nu skapas en ny Xcode-arbetsyta som du ska läsa in.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## <a name="the-structure-of-the-project"></a>Projektets struktur
Vi har konfigurerat följande grundstruktur för vårt projekt:

* En **bakgrundsvy** med ett åtgärdsfönster.
* En vy av typen **Lägg till uppgift** för information om den valda uppgiften.
* En **inloggningsvy** som gör att användaren kan logga in i appen.

Vi ska använda olika filer i projektet för att lägga till autentisering. Andra delar av koden, till exempel den visuella koden, är inte relevant för identitetshanteringen och tillhandahålls åt dig.

## <a name="create-the-settingsplist-file-for-your-application"></a>Skapa `settings.plist`-filen för ditt program
Det är enklare att konfigurera programmet om vi har en central plats där vi kan samla våra konfigurationsvärden. Det gör det också lättare att förstå vad varje inställning gör i programmet. Vi ska använda *egenskapslistan* för att tillhandahålla dessa värden till programmet.

* Skapa/öppna `settings.plist`-filen under `Supporting Files` på programarbetsytan
* Ange följande värden (vi ska gå igenom dem i detalj snart)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Nu ska vi titta närmare på dem.

Som du ser måste du fylla i ditt klientnamn för `authURL`, `loginURL`, `bhh` och `tokenURL`. Det här är klientnamnet för B2C-klienten som du har tilldelats. Till exempel `kidventusb2c.onmicrosoft.com`. Om du använder våra Microsoft Azure Identity-bibliotek för öppen källkod hämtar vi dessa data åt dig mig hjälp av vår metadataslutpunkt. Du slipper extrahera värdena själv. Vi gör det åt dig.

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

`keychain`-värdet är den behållare som NXOAuth2Client-biblioteket använder för att skapa en nyckelring som lagrar dina token. Om du vill använda enkel inloggning (SSO) i flera appar kan du ange samma nyckelring i alla program och begära att nyckelringen ska användas i dina XCode-berättiganden. Detta beskrivs närmare i Apples dokumentation.

`<policy name>` i slutet av varje URL är de platser där du placerar principen som du skapade ovan. Appen anropar dessa principer beroende på flödet.

`taskAPI` är den REST-slutpunkt som vi anropar med din B2C-token för att lägga till aktiviteter eller för att hämta data om befintliga aktiviteter. Detta har konfigurerats specifikt för det här exemplet. Du behöver inte ändra det för att exemplet ska fungera.

Resten av dessa värden krävs för att använda biblioteket och skapar bara platser så att du kan överföra värden till kontexten.

Nu när vi har skapat `settings.plist`-filen behöver vi kod för att läsa den.

## <a name="set-up-a-appdata-class-to-read-our-settings"></a>Konfigurera en AppData-klass för att läsa inställningarna
Nu ska vi skapa en enkel fil som bara parsar `settngs.plist`-filen som vi skapade ovan och göra dessa inställningar tillgängliga för alla klasser. Eftersom vi inte vill skapa en ny kopia av data varje gång en klass begär dem ska vi använda ett Singleton-mönster och bara returnera samma instans varje gång en begäran skickas för inställningarna.

* Skapa en `AppData.h`-fil:

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Skapa en `AppData.m`-fil:

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

Nu kan vi enkelt komma åt data genom att bara anropa `  AppData *data = [AppData getInstance];` i någon av våra klasser som du ser nedan.

## <a name="set-up-the-nxoauth2client-library-in-your-appdelegate"></a>Konfigurera NXOAuth2Client-biblioteket i AppDelegate
NXOAuthClient-biblioteket kräver att vissa värden konfigureras. När det är klart kan du använda den token du får för att anropa REST-API:et. Eftersom vi vet att `AppDelegate` anropas när vi läser in programmet är det en bra idé att placera konfigurationsvärdena i den filen.

* Öppna `AppDelegate.m`-filen
* Importera några huvudfiler som vi ska använda senare.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Lägg till `setupOAuth2AccountStore`-metoden i AppDelegate

Vi måste skapa en AccountStore och sedan skicka informationen som vi just har lästs in från `settings.plist`-filen till den.

Det finns några saker som du bör känna till om B2C-tjänsten som gör koden lättare att förstå:

1. Azure AD B2C använder *principen* på det sätt som anges av frågeparametrarna för att utföra din begäran. Detta gör att Azure Active Directory kan fungera som en oberoende tjänst för ditt program. För att tillhandahålla dessa extra frågeparametrar måste vi ange `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:`-metoden med våra anpassade principparametrar.
2. Azure AD-B2C använder omfång på ungefär på samma sätt som andra OAuth2-servrar. Men eftersom användningen av B2C handlar lika mycket om att autentisera en användare som att komma åt resurser är vissa resurser absolut nödvändiga för att flödet ska fungera korrekt. Det här är `openid`-omfånget. Våra Microsoft Identity-SDK:er tillhandahåller `openid`-omfånget automatiskt, så du ser inte detta i vår SDK-konfiguration. Eftersom vi använder ett bibliotek från tredje part måste vi dock ange det här omfånget.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
Försäkra dig om att du anropar det i AppDelegate under `didFinishLaunchingWithOptions:`-metoden.

```
[self setupOAuth2AccountStore];
```


## <a name="create-a-loginviewcontroller-class-that-we-will-use-to-handle-authentication-requests"></a>Skapa en `LoginViewController`-klass som ska användas för att hantera autentiseringsförfrågningar
Vi använder en webbvy för kontoinloggning. På så sätt kan vi be användaren att uppfylla ytterligare faktorer, t.ex. SMS-textmeddelanden (om det har konfigurerats), eller visa felmeddelanden för användaren. Här ska vi konfigurera webbvyn och sedan skriva koden som hanterar återanropen som görs i webbvyn från tjänsten Microsoft Identity.

* Skapa en `LoginViewController.h`-klass

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

Vi skapar var och en av dessa metoder nedan.

> [!NOTE]
> Se till att du binder `loginView` till själva webbvyn inuti din storyboard. Annars kommer inte webbvyn att dyka upp när det är dags att autentisera.
>
>

* Skapa en `LoginViewController.m`-klass
* Lägg till några tillståndsvariabler för autentiseringen

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE;
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Åsidosätt WebView-metoderna för att hantera autentiseringen

Vi måste berätta för webbvyn vilket beteende vi vill ha när en användare loggar in. Klipp bara ut och klistra in koden nedan.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Skriv koden för att hantera resultatet från OAuth2-begäran

Vi behöver kod som hanterar RedirectUrl:en som skickas tillbaka från webbvyn. Om den inte lyckas försöker vi igen. Under tiden visar biblioteket felet som du kan se i konsolen eller hantera asynkront.

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Konfigurera meddelandefabrikerna.

Vi skapar samma metod som i `AppDelegate` ovan, men den här gången lägger vi till några `NSNotification` som meddelar vad som händer i tjänsten. Vi konfigurerar en granskare som meddelar oss om eventuella tokenändringar. När vi har fått en token skickar vi tillbaka användaren till `masterView`.

```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Lägg till kod som hanterar användaren när en inloggningsbegäran initieras

Nu ska vi skapa en metod som anropas när vi får en autentiseringsförfrågan. Det här är den metod som skapar en webbvy.

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Slutligen ska vi anropa alla dessa metoder som vi har skrivit ovan varje gång `LoginViewController` läses in. Vi gör det genom att lägga till dessa metoder i `viewDidLoad`-metoden vi får från Apple.

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

Nu har du skapat den huvudsakliga metoden för att interagera med våra program för inloggning. När vi har loggat in måste vi använda de token som vi har fått. Det gör vi genom att skapa en del hjälpkod som anropar REST-API:er med hjälp av det här biblioteket.

## <a name="create-a-graphapicaller-class-to-handle-our-requests-to-a-rest-api"></a>Skapa en `GraphAPICaller`-klass för att hantera begäranden till ett REST-API
Vi har en konfiguration som läses in varje gång vi läser in vår app. Nu måste vi göra något med den när vi har fått en token.

* Skapa en `GraphAPICaller.h`-fil

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

I den här koden ser du att vi skapar två metoder: en för att hämta aktiviteterna från ett API och en annan för att lägga till aktiviteter till API:et.

Nu när vi har skapat vårt gränssnitt ska vi lägga till själva implementeringen:

* Skapa en `GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

//
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

//
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## <a name="run-the-sample-app"></a>Kör exempelappen
Slutligen skapar och kör du appen i Xcode. Registrera dig eller logga in i appen och skapa aktiviteter för en inloggad användare. Logga ut och logga in igen som en annan användare, och skapa aktiviteter för den användaren.

Tänk på att uppgifterna lagras per användare i API:et eftersom det extraherar användarens identitet från den åtkomst-token som det tar emot.

## <a name="next-steps"></a>Nästa steg
Du kan nu gå vidare till mer avancerade B2C-ämnen. Du kan prova följande:

[Anropa ett webb-API för Node.js från en Node.js-webbapp]()

[Anpassa UX för en B2C-app]()



<!--HONumber=Dec16_HO4-->


