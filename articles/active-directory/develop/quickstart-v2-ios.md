---
title: Snabb start för Microsoft Identity Platform iOS och macOS | Azure
description: Lär dig hur du loggar in användare och frågar Microsoft Graph i ett iOS-eller macOS-program.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81536139"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Snabb start: Logga in användare och anropa Microsoft Graph API från en iOS-eller macOS-app

Den här snabb starten innehåller ett kod exempel som visar hur ett inbyggt iOS-eller macOS-program kan använda Microsoft Identity Platform för att logga in på personliga konton, arbets-och skol konton, hämta en åtkomsttoken och anropa Microsoft Graph-API: et.

Den här snabb starten gäller både iOS-och macOS-appar. Vissa steg behövs bara för iOS-appar. De här stegen anropar att de endast är för iOS.

![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Förutsättningar**
> * XCode 10 +
> * iOS 10 +
> * macOS 10.12 +

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Alternativ 1: registrera och konfigurera appen automatiskt och ladda ned kod exemplet
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> För att registrera din app
> 1. Gå till fönstret ny [Azure Portal-Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) .
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://aka.ms/MobileAppReg) .
> 1. Välj **ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>      - I avsnittet **namn** anger du ett meningsfullt program namn som ska visas för användarna av appen när de loggar in eller medgivande till din app.
>      - Hoppa över andra konfigurationer på den här sidan.
>      - Välj `Register`.
> 1. I avsnittet **Hantera** `Authentication`  >  `Add Platform`  > väljer `iOS`du.
>      - Ange ***paket-ID*** : t för ditt program. Paket-ID: t är bara en unik sträng som unikt identifierar ditt program, till `com.<yourname>.identitysample.MSALMacOS`exempel. Anteckna värdet du använder.
>      - Observera att iOS-konfigurationen även gäller för macOS-program.
> 1. Välj `Configure` och spara ***konfigurations*** informationen för MSAL för senare i den här snabb starten.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Steg 1: Konfigurera programmet
> För att kod exemplet för den här snabb starten ska fungera måste du lägga till en omdirigerings-URI som är kompatibel med auth Broker.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-ios/green-check.png) appen konfigureras med de här attributen

#### <a name="step-2-download-the-sample-project"></a>Steg 2: Hämta exempel projektet

- [Ladda ned kod exemplet för iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Ladda ned kod exemplet för macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Steg 3: Installera beroenden

I ett terminalfönster navigerar du till mappen med det nedladdade kod exemplet och `pod install` kör för att installera det senaste MSAL-biblioteket.

#### <a name="step-4-configure-your-project"></a>Steg 4: Konfigurera ditt projekt

> [!div renderon="docs"]
> Om du valde alternativ 1 ovan kan du hoppa över de här stegen.

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extrahera zip-filen och öppna projektet i XCode.
> 1. Redigera **ViewController. SWIFT** och ersätt raden som börjar med "Låt kClientID" med följande kodfragment. Kom ihåg att uppdatera värdet för `kClientID` med det klient-ID som du sparade när du registrerade din app i portalen tidigare i snabb starten:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Redigera **ViewController. SWIFT** och ersätt raden som börjar med "Låt kAuthority" med följande kodfragment:
>    ```swift
>    let kAuthority = "Enter_the_Authority_Endpoint_Host_HereEnter_the_Tenant_Info_Here"
>    ```
> 1. Redigera **ViewController. SWIFT** och ersätt raden som börjar med "Låt kGraphEndpoint" med följande kodfragment:
>    ```swift
>    let kGraphEndpoint = "Enter_the_MS_Graph_Endpoint_Host_Here"
>    ```
> 1. Öppna projekt inställningarna. I avsnittet **identitet** anger du det **paket-ID** som du angav i portalen.
> 1. För endast iOS högerklickar du på **info. plist** och väljer **öppna som** > **källkod**.
> 1. Endast för iOS, under noden dict-rotnod, ersätter `CFBundleURLSchemes` du med det ***paket-ID*** som du angav i portalen.
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
> 1. Redigera **ViewController. SWIFT** och ersätt raden som börjar med "Låt kClientID" med följande kodfragment. Kom ihåg att uppdatera värdet för `kClientID` med clientID som du sparade när du registrerade din app i portalen tidigare i den här snabb starten:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Om du skapar en app för [nationella Azure AD-moln](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints)ersätter du raden som börjar med "Låt kGraphEndpoint" och "Låt kAuthority" med rätt slut punkter. Använd standardvärden för global åtkomst:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. Andra slut punkter dokumenteras [här](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints). Om du till exempel vill köra snabb starten med Azure AD Tyskland använder du följande:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Öppna projekt inställningarna. I avsnittet **identitet** anger du det **paket-ID** som du angav i portalen.
> 1. För endast iOS högerklickar du på **info. plist** och väljer **öppna som** > **källkod**.
> 1. Endast för iOS, under noden dict-rotnod, ersätter `Enter_the_bundle_Id_Here` du med det ***paket-ID*** som du använde i portalen.
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

### <a name="get-msal"></a>Hämta MSAL

MSAL ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) är det bibliotek som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Du kan lägga till MSAL i ditt program med hjälp av följande process:

```
$ vi Podfile

```
Lägg till följande i den här Podfile (med projektets mål):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Kör CocoaPods-installations kommando:

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
> | `authority` | Slut punkten för Microsoft Identity Platform. I de flesta fall är detta *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | Omdirigerings-URI för programmet. Du kan skicka Nil om du vill använda standardvärdet eller din anpassade omdirigerings-URI. |

### <a name="for-ios-only-additional-app-requirements"></a>För endast iOS, ytterligare krav för appar

Din app måste också ha följande i din `AppDelegate`. Detta låter MSAL SDK hantera token-svar från auth Broker-appen när du utför autentisering.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> I iOS 13 +, om du antar `UISceneDelegate` i stället `UIApplicationDelegate`för, placerar du koden i `scene:openURLContexts:` återanropet i stället (se [Apples dokumentation](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> Om du har stöd för både UISceneDelegate och UIApplicationDelegate för kompatibilitet med äldre iOS måste MSAL-återanropet placeras på båda platserna.

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

Slutligen måste appen ha en `LSApplicationQueriesSchemes` post i din ***info. plist*** tillsammans med. `CFBundleURLTypes` Exemplet följer med detta.

   ```xml
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Logga in användare & token för begäran

MSAL har två metoder som används för att hämta token: `acquireToken` och `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: få en token interaktivt

Vissa situationer kräver att användare interagerar med Microsoft Identity Platform. I sådana fall kan slutanvändaren behöva välja sitt konto, ange sina autentiseringsuppgifter eller godkänna appens behörigheter. Exempel:

* Första gången användaren loggar in på programmet
* Om en användare återställer sitt lösen ord måste de ange sina autentiseringsuppgifter
* När ditt program begär åtkomst till en resurs för första gången
* När MFA eller andra principer för villkorlig åtkomst krävs

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Där:||
> |---------|---------|
> | `scopes` | Innehåller de omfattningar som begärs (det `[ "user.read" ]` vill säga för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API`api://<Application ID>/access_as_user`: er () |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: Hämta en åtkomsttoken tyst

Appar bör inte kräva att användarna loggar in varje gång de begär en token. Om användaren redan har loggat in, tillåter den här metoden appar att begära token tyst.

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
> | `scopes` | Innehåller de omfattningar som begärs (det `[ "user.read" ]` vill säga för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API`api://<Application ID>/access_as_user`: er () |
> | `account` | Det konto som en token begärs för. Den här snabb starten är ungefär samma konto program. Om du vill skapa en app med flera konton måste du definiera logik för att identifiera vilket konto som ska användas för token-begäranden `accountsFromDeviceForParameters:completionBlock:` som använder och skickar korrekt`accountIdentifier` |

## <a name="next-steps"></a>Nästa steg

Testa själv studie kursen om iOS och macOS för en fullständig steg-för-steg-guide om hur du skapar program, inklusive en fullständig förklaring av den här snabb starten.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>Lär dig hur du skapar programmet som används i den här snabb starten

> [!div class="nextstepaction"]
> [Ring Graph API självstudie för iOS och macOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
