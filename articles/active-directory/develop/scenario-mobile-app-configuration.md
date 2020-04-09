---
title: Konfigurera mobilappar som anropar webb-API:er | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar en mobilapp som anropar webb-API:er (appens kodkonfiguration)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 06475b53acf6c9161888e29723feab9cdc4336d5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882734"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Konfigurera en mobilapp som anropar webb-API:er

När du har skapat programmet får du lära dig hur du konfigurerar koden med hjälp av appregistreringsparametrarna. Mobila applikationer har vissa komplexiteter i samband med att de kan anpassas till sina ramverk för att skapa dem.

## <a name="find-msal-support-for-mobile-apps"></a>Hitta MSAL-stöd för mobilappar

Följande MSAL-typer (Microsoft Authentication Library) stöder mobilappar.

MSAL | Beskrivning
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Används för att utveckla bärbara applikationer. MSAL.NET stöder följande plattformar för att skapa ett mobilprogram: Universal Windows Platform (UWP), Xamarin.iOS och Xamarin.Android.
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Används för att utveckla inbyggda iOS-program med hjälp av Objective-C eller Swift.
![MSAL. Android](media/sample-v2-code/logo_android.png) <br/> MSAL. Android | Används för att utveckla inhemska Android-program i Java för Android.

## <a name="instantiate-the-application"></a>Instansiera programmet

### <a name="android"></a>Android

Mobila applikationer `PublicClientApplication` använder klassen. Så här instansierar du det:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Mobila applikationer på iOS måste `MSALPublicClientApplication` instansiera klassen. Använd följande kod om du vill instansiera klassen. 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[Ytterligare MSALPublicClientApplicationConfig-egenskaper](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) kan åsidosätta standardutfärdaren, ange en omdirigerings-URI eller ändra beteendet för MSAL-tokencachening. 

### <a name="xamarin-or-uwp"></a>Xamarin eller UWP

I det här avsnittet beskrivs hur du instansierar programmet för Xamarin.iOS-, Xamarin.Android- och UWP-appar.

#### <a name="instantiate-the-application"></a>Instansiera programmet

I Xamarin eller UWP är det enklaste sättet att instansiera programmet genom att använda följande kod. I den `ClientId` här koden är GUID för din registrerade app.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Ytterligare `With<Parameter>` metoder anger den överordnade användargränssnittet, åsidosätter standardutfärdaren, anger ett klientnamn och en version för telemetri, anger en omdirigerings-URI och anger den HTTP-fabrik som ska användas. HTTP-fabriken kan till exempel användas för att hantera proxyservrar och för att ange telemetri och loggning. 

Följande avsnitt innehåller mer information om instansiating av programmet.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Ange det överordnade användargränssnittet, fönstret eller aktiviteten

På Android måste du skicka den överordnade aktiviteten innan du gör den interaktiva autentiseringen. På iOS, när du använder en mäklare, `ViewController`måste du skicka in . På samma sätt på UWP kanske du vill skicka in det överordnade fönstret. Du skickar in den när du skaffar token. Men när du skapar appen kan du också ange en motringning som ett ombud som returnerar `UIParent`.

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

I Android rekommenderar vi [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)att du använder . Den `PublicClientApplication` resulterande byggarkoden ser ut så här:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Hitta fler appbyggarparametrar

En lista över alla metoder `PublicClientApplicationBuilder`som är tillgängliga i finns i [listan Metoder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).

En beskrivning av alla alternativ `PublicClientApplicationOptions`som visas i finns i [referensdokumentationen](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

## <a name="tasks-for-xamarin-ios"></a>Uppgifter för Xamarin iOS

Om du använder MSAL.NET på Xamarin iOS gör du följande uppgifter.

* [Åsidosätt och `OpenUrl` implementera funktionen i`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Aktivera nyckelringsgrupper](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Aktivera tokencachedelning](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Aktivera nyckelringsåtkomst](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Mer information finns i [Xamarin iOS-överväganden](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Uppgifter för MSAL för iOS och macOS

Dessa uppgifter är nödvändiga när du använder MSAL för iOS och macOS:

* [Implementera `openURL` motringningen](#brokered-authentication-for-msal-for-ios-and-macos)
* [Aktivera nyckelringsåtkomstgrupper](howto-v2-keychain-objc.md)
* [Anpassa webbläsare och webbvyer](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Uppgifter för Xamarin.Android

Om du använder Xamarin.Android gör du följande:

- [Se till att kontrollen går tillbaka till MSAL efter att den interaktiva delen av autentiseringsflödet har](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Uppdatera Android-manifestet](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Använda den inbäddade webbvyn (valfritt)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Felsöka vid behov](msal-net-xamarin-android-considerations.md#troubleshoot)

Mer information finns i [överväganden om Xamarin.Android](msal-net-xamarin-android-considerations.md).

För överväganden om webbläsarna på Android, se [Xamarin.Android-specifika överväganden med MSAL.NET](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Uppgifter för UWP

På UWP kan du använda företagsnätverk. I följande avsnitt beskrivs de uppgifter som du bör slutföra i företagsscenariot.

Mer information finns i [UWP-specifika överväganden med MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Konfigurera programmet så att det använder mäklaren

På Android och iOS aktiverar mäklare:

- **Enkel inloggning (SSO)**: Du kan använda SSO för enheter som är registrerade med Azure Active Directory (Azure AD). När du använder SSO behöver användarna inte logga in på varje program.
- **Enhetsidentifiering**: Den här inställningen aktiverar principer för villkorlig åtkomst som är relaterade till Azure AD-enheter. Autentiseringsprocessen använder enhetscertifikatet som skapades när enheten anslöts till arbetsplatsen.
- **Verifiering av programidentifiering**: När ett program anropar mäklaren skickar den sin omdirigerings-URL. Då mäklaren verifierar det.

### <a name="enable-the-broker-on-xamarin"></a>Aktivera mäklaren på Xamarin

Om du vill aktivera mäklaren `WithBroker()` på Xamarin `PublicClientApplicationBuilder.CreateApplication` använder du parametern när du anropar metoden. Som standard `.WithBroker()` är inställd på true. 

Om du vill aktivera förmedlad autentisering för Xamarin.iOS följer du stegen i [avsnittet Xamarin.iOS](#enable-brokered-authentication-for-xamarin-ios) i den här artikeln.

### <a name="enable-the-broker-for-msal-for-android"></a>Aktivera mäklaren för MSAL för Android

Information om hur du aktiverar en mäklare på Android finns i [Förmedlad autentisering på Android](brokered-auth.md). 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Aktivera mäklaren för MSAL för iOS och macOS

Medlad autentisering är aktiverad som standard för Azure AD-scenarier i MSAL för iOS och macOS. 

Följande avsnitt innehåller instruktioner för att konfigurera ditt program för autentiseringsstöd för medföljande autentisering för antingen MSAL för Xamarin.iOS eller MSAL för iOS och macOS. I de två uppsättningarna instruktioner skiljer sig några av stegen åt.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Aktivera förmedlad autentisering för Xamarin iOS

Följ stegen i det här avsnittet för att aktivera din Xamarin.iOS-app för att prata med [Microsoft Authenticator-appen.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

#### <a name="step-1-enable-broker-support"></a>Steg 1: Aktivera mäklarsupport

Mäklarstöd är inaktiverat som standard. Du aktiverar den `PublicClientApplication` för en enskild klass. Använd `WithBroker()` parametern när `PublicClientApplication` du `PublicClientApplicationBuilder`skapar klassen via . Parametern `WithBroker()` är inställd på true som standard.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Steg 2: Uppdatera AppDelegate för att hantera motringningen

När MSAL.NET ringer mäklaren ringer mäklaren sedan tillbaka till din ansökan. Den ringer tillbaka `AppDelegate.OpenUrl` med hjälp av metoden. Eftersom MSAL väntar på svaret från mäklaren måste programmet samarbeta för att ringa MSAL.NET tillbaka. Du ställer in det `AppDelegate.cs` här beteendet genom att uppdatera filen för att åsidosätta metoden, som följande kod visar.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

Den här metoden anropas varje gång programmet startas. Det är en möjlighet att bearbeta svaret från mäklaren och slutföra autentiseringsprocessen som MSAL.NET startat.

#### <a name="step-3-set-a-uiviewcontroller"></a>Steg 3: Ange en UIViewController()

För Xamarin iOS behöver du normalt inte ange ett objektfönster. Men i det här fallet bör du ställa in den så att du kan skicka och ta emot svar från en mäklare. Om du vill ange `AppDelegate.cs`ett objektfönster anger du ett `ViewController`.

Så här anger du objektfönstret:

1. I `AppDelegate.cs`ställer `App.RootViewController` du in `UIViewController()`en ny . Den här inställningen säkerställer att `UIViewController`samtalet till mäklaren innehåller . Om den inte är korrekt inställd kan det här felet visas:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Använd `AcquireTokenInteractive` i `.WithParentActivityOrWindow(App.RootViewController)`samtalet . Skicka referensen till det objektfönster som du ska använda. Här är ett exempel:

    Följande gäller i `App.cs`:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    Följande gäller i `AppDelegate.cs`:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    I `AcquireToken` samtalet:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>Steg 4: Registrera ett URL-schema

MSAL.NET använder webbadresser för att anropa mäklaren och sedan returnera mäklarsvaret tillbaka till din app. Slutför tur- och returresan genom att `Info.plist` registrera appens URL-schema i filen. 

Så här registrerar du appens URL-schema:

1. Prefix `CFBundleURLSchemes` `msauth`med . 
1. Lägg `CFBundleURLName` till i slutet. Följ detta mönster: 

   `$"msauth.(BundleId)"`

   Här `BundleId` identifierar du enheten unikt. Om till `BundleId` exempel `yourcompany.xforms`är , `msauth.com.yourcompany.xforms`är ditt URL-schema .
    
   > [!NOTE]
   > Det här URL-schemat blir en del av den omdirigerar-URI som unikt identifierar din app när den tar emot mäklarens svar.
    
   ```XML
    <key>CFBundleURLTypes</key>
       <array>
         <dict>
           <key>CFBundleTypeRole</key>
           <string>Editor</string>
           <key>CFBundleURLName</key>
           <string>com.yourcompany.xforms</string>
           <key>CFBundleURLSchemes</key>
           <array>
             <string>msauth.com.yourcompany.xforms</string>
           </array>
         </dict>
       </array>
   ```
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Steg 5: Lägg till i avsnittet LSApplicationQueriesSchemes

MSAL `–canOpenURL:` använder för att kontrollera om mäklaren är installerad på enheten. I iOS 9 har Apple låst de scheman som ett program kan fråga efter.

Lägg `msauthv2` till `LSApplicationQueriesSchemes` i `Info.plist` avsnittet i filen, som i följande kodexempel:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Förmedlad autentisering för MSAL för iOS och macOS

Medlad autentisering är aktiverad som standard för Azure AD-scenarier.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Steg 1: Uppdatera AppDelegate för att hantera motringningen

När MSAL för iOS och macOS anropar mäklaren ringer `openURL` mäklaren tillbaka till ditt program med hjälp av metoden. Eftersom MSAL väntar på svaret från mäklaren måste programmet samarbeta för att ringa tillbaka MSAL. Konfigurera den `AppDelegate.m` här funktionen genom att uppdatera filen för att åsidosätta metoden, vilket visas i följande kodexempel.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

> [!NOTE]
> Om du `UISceneDelegate` antog på iOS 13 eller senare, sedan `scene:openURLContexts:` `UISceneDelegate` placera MSAL motringning i i stället. MSAL `handleMSALResponse:sourceApplication:` får endast anropas en gång för varje webbadress.
>
> Mer information finns i [Apple-dokumentationen](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc).

#### <a name="step-2-register-a-url-scheme"></a>Steg 2: Registrera ett URL-schema

MSAL för iOS och macOS använder webbadresser för att anropa mäklaren och sedan returnera mäklarsvaret till din app. Om du vill slutföra tur och retur registrerar `Info.plist` du ett URL-schema för appen i filen.

Så här registrerar du ett schema för din app: 

1. Prefix ditt anpassade `msauth`URL-schema med . 

1. Lägg till paketidentifieraren i slutet av schemat. Följ detta mönster: 

   `$"msauth.(BundleId)"`

   Här `BundleId` identifierar du enheten unikt. Om till `BundleId` exempel `yourcompany.xforms`är , `msauth.com.yourcompany.xforms`är ditt URL-schema .
  
   > [!NOTE]
   > Det här URL-schemat blir en del av den omdirigerar-URI som unikt identifierar din app när den tar emot mäklarens svar. Kontrollera att omdirigerings-URI i formatet `msauth.(BundleId)://auth` är registrerad för ditt program i [Azure-portalen](https://portal.azure.com).
  
   ```XML
   <key>CFBundleURLTypes</key>
   <array>
       <dict>
           <key>CFBundleURLSchemes</key>
           <array>
               <string>msauth.[BUNDLE_ID]</string>
           </array>
       </dict>
   </array>
   ```

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Steg 3: Lägg till LSApplicationQueriesSchemes

Lägg `LSApplicationQueriesSchemes` till för att tillåta samtal till Microsoft Authenticator-appen, om den är installerad.

> [!NOTE]
> Schemat `msauthv3` behövs när appen kompileras med Xcode 11 och senare. 

Här är ett exempel på `LSApplicationQueriesSchemes`hur du lägger till:

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Förmedlad autentisering för Xamarin.Android

MSAL.NET stöder inte mäklare för Android.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skaffa en token](scenario-mobile-acquire-token.md)
