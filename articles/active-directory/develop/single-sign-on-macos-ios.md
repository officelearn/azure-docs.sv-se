---
title: Konfigurera SSO på macOS och iOS
titleSuffix: Microsoft identity platform
description: Lär dig hur du konfigurerar enkel inloggning (SSO) på macOS och iOS.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 91a55520b37c549c8f1d94ba6cf08ecd24db85b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262456"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Så här konfigurerar du SSO på macOS och iOS

Microsoft Authentication Library (MSAL) för macOS och iOS stöder Enkel inloggning (SSO) mellan macOS/iOS-appar och webbläsare. Den här artikeln innehåller följande SSO-scenarier:

- [Tyst SSO mellan flera appar](#silent-sso-between-apps)

Den här typen av SSO fungerar mellan flera appar som distribueras av samma Apple-utvecklare. Det ger tyst SSO (det vill, användaren inte uppmanas för autentiseringsuppgifter) genom att läsa uppdatera token skrivna av andra appar från nyckelringen och utbyta dem för åtkomsttoken tyst.  

- [SSO via autentiseringsmäklare](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Det här flödet är inte tillgängligt på macOS.

Microsoft tillhandahåller appar, så kallade mäklare, som aktiverar SSO mellan program från olika leverantörer så länge den mobila enheten är registrerad med Azure Active Directory (AAD). Den här typen av SSO kräver att ett mäklarprogram installeras på användarens enhet.

- **SSO mellan MSAL och Safari**

SSO uppnås genom [klassen ASWebAuthenticationSession.](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) Den använder befintligt inloggningstillstånd från andra appar och webbläsaren Safari. Det är inte begränsat till appar som distribueras av samma Apple-utvecklare, men det kräver viss användarinteraktion.

Om du använder standardwebbvyn i appen för att logga in användare får du automatisk SSO mellan MSAL-baserade program och Safari. Mer information om webbvyer som MSAL stöder finns i [Anpassa webbläsare och WebViews](customize-webviews.md).

> [!IMPORTANT]
> Den här typen av SSO är för närvarande inte tillgänglig på macOS. MSAL på macOS stöder endast WKWebView som inte har SSO-stöd med Safari. 

- **Tyst SSO mellan ADAL- och MSAL-macOS/iOS-appar**

MSAL Objective-C stöder migrering och SSO med ADAL Objective-C-baserade appar. Apparna måste distribueras av samma Apple-utvecklare.

Se [SSO mellan ADAL- och MSAL-appar på macOS och iOS](sso-between-adal-msal-apps-macos-ios.md) för instruktioner för SSO mellan flera appar mellan appar med flera appar.

## <a name="silent-sso-between-apps"></a>Tyst SSO mellan appar

MSAL stöder SSO-delning via iOS-nyckelringsåtkomstgrupper.

Om du vill aktivera SSO i dina program måste du göra följande steg, som förklaras mer i detalj nedan:

1. Kontrollera att alla dina program använder samma klient-ID eller program-ID.
1. Se till att alla dina program delar samma signeringscertifikat från Apple så att du kan dela nyckelringar.
1. Begär samma nyckelringsrätt för var och en av dina ansökningar.
1. Berätta för MSAL SDK:er om den delade nyckelringen som du vill att vi ska använda om den skiljer sig från standardringen.

### <a name="use-the-same-client-id-and-application-id"></a>Använda samma klient-ID och program-ID

För att Microsofts identitetsplattform ska veta vilka program som kan dela token måste dessa program dela samma klient-ID eller program-ID. Detta är den unika identifierare som angavs till dig när du registrerade ditt första program i portalen.

Det sätt som Microsoft identity-plattformen berättar för appar som använder samma application-ID isär är av deras **Redirect URI:er**. Varje program kan ha flera omdirigera URI:er registrerade i introduktionsportalen. Varje app i din svit kommer att ha en annan omdirigera URI. Ett exempel:

App1 Omdirigera URI:`msauth.com.contoso.mytestapp1://auth`  
App2 Omdirigera URI:`msauth.com.contoso.mytestapp2://auth`  
App3 Omdirigera URI:`msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> Formatet för omdirigeringsuris måste vara kompatibelt med formatet MSAL-stöd, som dokumenteras i [kraven för MSAL Redirect URI-format](redirect-uris-ios.md#msal-redirect-uri-format-requirements).

### <a name="setup-keychain-sharing-between-applications"></a>Konfigurera nyckelringsdelning mellan program

Se Apples artikel [Om tilläggsfunktioner](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) för att aktivera nyckelringsdelning. Vad som är viktigt är att du bestämmer vad du vill att din nyckelring ska kallas och lägga till den funktionen till alla dina program som kommer att delta i SSO.

När du har konfigurerat berättigandena korrekt visas `entitlements.plist` en fil i projektkatalogen som innehåller ungefär så här:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>Lägga till en ny nyckelringsgrupp

Lägg till en ny nyckelringsgrupp i **projektfunktionerna**. Nyckelringsgruppen bör vara:
* `com.microsoft.adalcache`på iOS 
* `com.microsoft.identity.universalstorage`på macOS.

![exempel på nyckelring](media/single-sign-on-macos-ios/keychain-example.png)

Mer information finns i [nyckelringsgrupper](howto-v2-keychain-objc.md).

## <a name="configure-the-application-object"></a>Konfigurera programobjektet

När du har aktiverat nyckelringsbehörigheten i vart och ett av `MSALPublicClientApplication` dina program och du är redo att använda SSO konfigurerar du med nyckelringsåtkomstgruppen som i följande exempel:

Mål C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> När du delar en nyckelring i dina program kan alla program ta bort användare eller till och med alla token i ditt program.
> Detta är särskilt effektfullt om du har program som förlitar sig på token för att göra bakgrundsarbete.
> Att dela en nyckelring innebär att du måste vara mycket försiktig när appen använder Microsoft identity SDK-åtgärder.

Klart! Microsoft identity SDK delar nu autentiseringsuppgifter för alla dina program. Kontolistan delas också mellan programinstanser.

## <a name="sso-through-authentication-broker-on-ios"></a>SSO via autentiseringsmäklare på iOS

MSAL ger stöd för förmedlad autentisering med Microsoft Authenticator. Microsoft Authenticator tillhandahåller SSO för AAD-registrerade enheter och hjälper även ditt program att följa principer för villkorlig åtkomst.

Följande steg är hur du aktiverar SSO med hjälp av en autentiseringsmäklare för din app:

1. Registrera ett kompatibelt Redirect URI-format för programmet i appens Info.plist. Mäklaren kompatibel Redirect URI-format är `msauth.<app.bundle.id>://auth`. Ersätt "<app.bundle.id>'' med programmets bunt-ID. Ett exempel:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Lägg till följande scheman i appens `LSApplicationQueriesSchemes`Info.plist under :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Lägg till följande `AppDelegate.m` i filen för att hantera motringningar:

    Mål C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Om du använder Xcode 11**bör du placera `SceneDelegate` MSAL-motringning i filen i stället.
Om du stöder både UISceneDelegate och UIApplicationDelegate för kompatibilitet med äldre iOS, måste MSAL-motringning placeras i båda filerna.

Mål C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

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

## <a name="next-steps"></a>Nästa steg

Läs mer om [autentiseringsflöden och programscenarier](authentication-flows-app-scenarios.md)