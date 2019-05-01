---
title: Microsoft identity-plattformen iOS Snabbstart | Azure
description: Lär dig hur du loggar in användare och fråga Microsoft Graph i ett iOS-program.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32cc373421e6b04737f40dc987b10c6ace858e17
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937344"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Snabbstart: Logga in användare och anropa Microsoft Graph API från en iOS-app

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Den här snabbstarten innehåller ett kodexempel som visar hur ett internt iOS-program kan logga in personliga konton eller arbets- och skolkonton, hämta en åtkomsttoken och anropa Microsoft Graph API.

![Visar hur exempelapp som genererats av den här snabbstarten fungerar](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Förutsättningar**
> * XCode 10+
> * iOS 10+ 

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Att registrera din app
> 1. Gå till den nya [Azure portal – appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) fönstret.
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Gå till Microsoft identity-plattformen för utvecklare [appregistreringar](https://aka.ms/MobileAppReg) sidan.
> 1. Välj **ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>      - I den **namn** anger ett beskrivande programnamn som ska visas för användare av appen när de loggar in eller godkänna din app, till exempel `iOSQuickstart`.
>      - Hoppa över andra konfigurationer på den här sidan. 
>      - Tryck på den `Register` knappen.
> 1. Klicka på den nya appen > Gå till `Authentication`  >  `Add Platform`  >  `iOS`.    
>      - Ange den ***paketidentifieraren*** för ditt program. 
> 1. Välj `Configure` och spara den ***MSAL Configuration*** information till senare. 

> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Steg 1: Konfigurera ditt program
> Du måste lägga till en omdirigerings-URI som är kompatibel med Auth Service broker för kodexempel för den här snabbstarten ska fungera. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-ios/green-check.png) appen konfigureras med de här attributen

#### <a name="step-2-download-your-web-server-or-project"></a>Steg 2: Ladda ned webbservern eller projektet

- [Ladda ned kodexempel](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Steg 3: Konfigurera projektet

> [!div renderon="docs"]
> Om du har valt alternativ 1 ovan kan du hoppa över de här stegen. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extrahera zip-filen och öppna projektet i XCode.
> 1. Redigera **ViewController.swift** och ersätt den rad som börjar med ”let kClientID” med följande kodavsnitt:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_here"
>    let kAuthority = "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
>
>    ```
> 1. Högerklicka på **Info.plist** och välj **öppna som** > **källkoden**.
> 1. Under rotnoden dict Ersätt med din ***paket-Id***:
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
> 1. Skapa och köra appen! 

> [!div renderon="docs"]
>
> 1. Extrahera zip-filen och öppna projektet i XCode.
> 1. Redigera **ViewController.swift** och Ersätt den rad som börjar med ”Låt kClientID” med följande kodavsnitt:
>
>    ```swift
>    let kClientID = "<ENTER_YOUR_APPLICATION/CLIENT_ID>"
> 
>    ```
> 1. Högerklicka på **Info.plist** och välj **öppna som** > **källkoden**.
> 1. Under rotnoden dict Ersätt med din ***paket-Id***:
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.<ENTER_YOUR_BUNDLE_ID></string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Skapa och köra appen! 

## <a name="more-information"></a>Mer information

Läs dessa avsnitten om du vill lära dig mer om den här snabbstarten.

### <a name="getting-msal"></a>Hämta MSAL

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) är i biblioteket som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft identity-plattformen. Du kan lägga till MSAL i ditt program med hjälp av följande process:

```
$ vi Podfile

```
Lägg till följande i podfile (med projektmål):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL', '~> 0.4.0'
end

```

### <a name="msal-initialization"></a>MSAL-initiering

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
> | `authority` | Slutpunkt för Microsoft identity-plattformen. I de flesta fall är detta *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | Omdirigerings-URI för programmet. Du kan skicka noll om du vill använda standardvärdet eller din anpassade omdirigerings-URI. |

### <a name="additional-app-requirements"></a>Ytterligare Appkrav  

Din app måste också ha följande din `AppDelegate`. På så sätt kan MSAL SDK hantera token svar från koordinatorappen autentisering när du utför autentisering.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
         guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
             return false
         }
         
         return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
     }

```

Slutligen din app måste har en `LSApplicationQueriesSchemes` post i din ***Info.plist*** tillsammans med den `CFBundleURLTypes`. Exemplet levereras med det här ingår. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauth</string>
      <string>msauthv2</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Logga in användare och begära token

MSAL har två metoder som används för att hämta token: `acquireToken` och `acquireTokenSilent`.

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Få en token interaktivt

Vissa situationer kräver att användare interagerar med Microsoft identity-plattformen. I dessa fall kan det krävas användaren att välja sitt konto, ange sina autentiseringsuppgifter eller godkänna din app-behörigheter. Exempel: 

* Första gången användaren loggar in på programmet
* Om en användare återställer sitt lösenord, uppmanas användaren att ange sina autentiseringsuppgifter 
* När ditt program begär åtkomst till en resurs för första gången
* När MFA- eller andra principer för villkorlig åtkomst krävs

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
applicationContext.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Där:||
> |---------|---------|
> | `scopes` | Innehåller de omfång som begärts (det vill säga `[ "user.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API: er (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-getting-an-access-token-silently"></a>acquireTokenSilent: Hämta en åtkomsttoken obevakat

Appar bör inte kräver att användarna ska logga in varje gång de begär en token. Om användaren har redan loggat in, kan den här metoden appar att begära token tyst. 

```swift
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: applicationContext.allAccounts().first)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Där: ||
> |---------|---------|
> | `scopes` | Innehåller de omfång som begärts (det vill säga `[ "user.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API: er (`api://<Application ID>/access_as_user`) |
> | `account` | Kontot som en token begärs för. Den här snabbstarten är ett enda konto-program, om du vill skapa en app för flera konto måste du definiera logik för att identifiera vilket konto som ska användas för token begäranden `applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>Nästa steg

Prova att använda iOS-självstudien i en fullständig vägledning om hur du skapar program, inklusive en fullständig förklaring av den här snabbstarten.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Lär dig stegen för hur du skapar det program som används i den här snabbstarten

> [!div class="nextstepaction"]
> [Självstudie om att anropa Graph API för iOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]