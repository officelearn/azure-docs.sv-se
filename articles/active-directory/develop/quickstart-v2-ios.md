---
title: Microsoft identity-plattformen iOS och macOS snabbstart | Azure
description: Lär dig hur du loggar in användare och frågar Microsoft Graph i ett iOS- eller macOS-program.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: 47485d8d9007a6cf6432b7bf401c7c1c34a9863a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536139"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Snabbstart: Logga in användare och anropa Microsoft Graph API från en iOS- eller macOS-app

Den här snabbstarten innehåller ett kodexempel som visar hur ett inbyggt iOS- eller macOS-program kan använda Microsofts identitetsplattform för att logga in på personliga konton, arbets- och skolkonton, hämta en åtkomsttoken och anropa Microsoft Graph API.

Den här snabbstarten gäller både iOS- och macOS-appar. Vissa steg behövs bara för iOS-appar. Dessa steg ropar att de bara är för iOS.

![Visar hur exempelappen som genereras av den här snabbstarten fungerar](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Krav**
> * XCode 10+
> * iOS 10+
> * macOS 10.12+

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och hämta sedan kodexemplet
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Om du vill registrera din app
> 1. Gå till den nya [Azure-portalen - fönstret Appregistreringar.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs)
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Navigera till sidan Microsoft identity platform för utvecklare [Appregistreringar.](https://aka.ms/MobileAppReg)
> 1. Välj **Ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>      - I avsnittet **Namn** anger du ett meningsfullt programnamn som visas för användare av appen när de loggar in eller samtycker till din app.
>      - Hoppa över andra konfigurationer på den här sidan.
>      - Välj `Register`.
> 1. Välj `iOS` **Manage** `Authentication`  > i `Add Platform`avsnittet Hantera .  > 
>      - Ange ***paketidentifieraren*** för ditt program. Buntidentifieraren är bara en unik sträng som unikt `com.<yourname>.identitysample.MSALMacOS`identifierar ditt program, till exempel . Anteckna det värde du använder.
>      - Observera att iOS-konfigurationen även gäller för macOS-program.
> 1. Välj `Configure` och spara ***MSAL-konfigurationsinformationen*** för senare i den här snabbstarten.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Steg 1: Konfigurera programmet
> För att kodexemplet för den här snabbstarten ska fungera måste du lägga till en omdirigerings-URI som är kompatibel med Auth-mäklaren.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-ios/green-check.png) appen konfigureras med de här attributen

#### <a name="step-2-download-the-sample-project"></a>Steg 2: Ladda ner exempelprojektet

- [Ladda ner kodexemplet för iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Hämta kodexemplet för macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Steg 3: Installera beroenden

I ett terminalfönster navigerar du till mappen med `pod install` det hämtade kodexemplet och kör för att installera det senaste MSAL-biblioteket.

#### <a name="step-4-configure-your-project"></a>Steg 4: Konfigurera projektet

> [!div renderon="docs"]
> Om du har valt Alternativ 1 ovan kan du hoppa över dessa steg.

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extrahera zip-filen och öppna projektet i XCode.
> 1. Redigera **ViewController.swift** och ersätt raden som börjar med 'let kClientID' med följande kodavsnitt. Kom ihåg att `kClientID` uppdatera värdet för med klient-ID som du sparade när du registrerade din app i portalen tidigare i snabbstarten:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Redigera **ViewController.swift** och ersätt raden som börjar med "let kAuthority" med följande kodavsnitt:
>    ```swift
>    let kAuthority = "Enter_the_Authority_Endpoint_Host_HereEnter_the_Tenant_Info_Here"
>    ```
> 1. Redigera **ViewController.swift** och ersätt raden som börjar med 'let kGraphEndpoint' med följande kodavsnitt:
>    ```swift
>    let kGraphEndpoint = "Enter_the_MS_Graph_Endpoint_Host_Here"
>    ```
> 1. Öppna projektinställningarna. I avsnittet **Identitet** anger du den **buntidentifierare** som du angav i portalen.
> 1. Endast för iOS högerklickar du på **Info.plist** och väljer **Öppna som** > **källkod**.
> 1. Endast för iOS ersätts `CFBundleURLSchemes` med det ***paket-ID*** som du angav i portalen under den dict-rotnoden.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>    ```
> 1. Bygg & kör appen!
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
>
> 1. Extrahera zip-filen och öppna projektet i XCode.
> 1. Redigera **ViewController.swift** och ersätt raden som börjar med 'let kClientID' med följande kodavsnitt. Kom ihåg att `kClientID` uppdatera värdet för med klient-ID som du sparade när du registrerade din app i portalen tidigare i den här snabbstarten:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Om du skapar en app för [nationella Azure AD-moln](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints)ersätter du raden som börjar med "let kGraphEndpoint" och "let kAuthority" med korrekta slutpunkter. För global åtkomst använder du standardvärden:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. Andra slutpunkter dokumenteras [här](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints). Om du till exempel vill köra snabbstarten med Azure AD Tyskland använder du följande:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Öppna projektinställningarna. I avsnittet **Identitet** anger du den **buntidentifierare** som du angav i portalen.
> 1. Endast för iOS högerklickar du på **Info.plist** och väljer **Öppna som** > **källkod**.
> 1. Endast för iOS ersätts `Enter_the_bundle_Id_Here` med det ***paket-ID*** som du använde i portalen under den dict-rotnod som du använde i portalen.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Bygg & kör appen!

## <a name="more-information"></a>Mer information

Läs dessa avsnitten om du vill lära dig mer om den här snabbstarten.

### <a name="get-msal"></a>Skaffa MSAL

MSAL[(MSAL.framework)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)är det bibliotek som används för att logga in användare och begära token som används för att komma åt ett API som skyddas av Microsofts identitetsplattform. Du kan lägga till MSAL i ditt program med hjälp av följande process:

```
$ vi Podfile

```
Lägg till följande i den här podfilen (med projektets mål):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Kör monteringskommandot CocoaPods:

```pod install```

### <a name="initialize-msal"></a>Initiera MSAL

Du kan lägga till referensen för MSAL genom att lägga till följande kod:

```swift
import MSAL
```

Initiera sedan MSAL med hjälp av följande kod:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)

let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |Där: ||
> |---------|---------|
> | `clientId` | Program-ID från den app som registrerats i *portal.azure.com* |
> | `authority` | Slutpunkten för Microsoft-identitetsplattformen. I de flesta fall är detta *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | Omdirigerings-URI för programmet. Du kan passera "noll" för att använda standardvärdet, eller din anpassade omdirigering URI. |

### <a name="for-ios-only-additional-app-requirements"></a>Endast för iOS kan ytterligare appkrav

Appen måste också ha följande `AppDelegate`i din . På så sätt kan MSAL SDK hantera tokensvar från Auth broker-appen när du gör autentisering.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> På iOS 13+, `UISceneDelegate` om `UIApplicationDelegate`du antar i `scene:openURLContexts:` stället för , placera denna kod i motringningen istället (Se [Apples dokumentation](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> Om du stöder både UISceneDelegate och UIApplicationDelegate för kompatibilitet med äldre iOS, måste MSAL-motringning placeras på båda ställena.

 ```swift
 func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {

        guard let urlContext = URLContexts.first else {
            return
        }

        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication

        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
 ```

Slutligen måste din app `LSApplicationQueriesSchemes` ha en post i din `CFBundleURLTypes` ***Info.plist*** tillsammans med . Provet levereras med detta ingår.

   ```xml
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Logga in användare & begärantoken

MSAL har två metoder som används för att hämta token: `acquireToken` och `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: Få en token interaktivt

Vissa situationer kräver att användarna interagerar med Microsofts identitetsplattform. I dessa fall kan slutanvändaren vara skyldig att välja sitt konto, ange sina autentiseringsuppgifter eller godkänna appens behörigheter. Exempel:

* Första gången användaren loggar in på programmet
* Om en användare återställer sitt lösenord måste han eller hon ange sina autentiseringsuppgifter
* När ditt program begär åtkomst till en resurs för första gången
* När MFA eller andra principer för villkorlig åtkomst krävs

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Där:||
> |---------|---------|
> | `scopes` | Innehåller de scope som begärs `[ "user.read" ]` (det vill `[ "<Application ID URL>/scope" ]` än Microsoft Graph`api://<Application ID>/access_as_user`eller för anpassade webb-API:er ( ) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: Få en åtkomsttoken tyst

Appar bör inte kräva att användarna loggar in varje gång de begär en token. Om användaren redan har loggat in tillåter den här metoden appar att begära token tyst.

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in

   guard let account = currentAccount else {
      return
   }

   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |Där: ||
> |---------|---------|
> | `scopes` | Innehåller de scope som begärs `[ "user.read" ]` (det vill `[ "<Application ID URL>/scope" ]` än Microsoft Graph`api://<Application ID>/access_as_user`eller för anpassade webb-API:er ( ) |
> | `account` | Kontot som en token begärs för. Den här snabbstarten handlar om ett enda kontoprogram. Om du vill skapa en app med flera konton måste du definiera logik för `accountsFromDeviceForParameters:completionBlock:` att identifiera vilket konto som ska användas för tokenbegäranden med hjälp av och skicka korrekt`accountIdentifier` |

## <a name="next-steps"></a>Nästa steg

Prova självstudien för iOS och macOS för en komplett steg-för-steg-guide om att skapa program, inklusive en fullständig förklaring av den här snabbstarten.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>Lär dig hur du skapar programmet som används i den här snabbstarten

> [!div class="nextstepaction"]
> [Call Graph API självstudiekurs för iOS och macOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
