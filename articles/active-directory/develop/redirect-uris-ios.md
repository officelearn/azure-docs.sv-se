---
title: Använd omdirigera URI:er med MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om skillnaderna mellan Microsoft Authentication Library for ObjectiveC (MSAL för iOS och macOS) och Azure AD Authentication Library for ObjectiveC (ADAL. objC) och hur man migrerar mellan dem.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: c36c6b1b1b08de6d2db9a7f7f9ebd3b162c02383
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240844"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Använda omdirigera URI:er med Microsofts autentiseringsbibliotek för iOS och macOS

När en användare autentiserar skickar Azure Active Directory (Azure AD) token till appen med hjälp av den omdirigerings-URI som registrerats med Azure AD-programmet.

Microsoft Authentication-biblioteket (MSAL) kräver att omdirigerings-URI:n registreras med Azure AD-appen i ett visst format. MSAL använder en standardomdirigerings-URI om du inte anger någon. Formatet är `msauth.[Your_Bundle_Id]://auth`.

URI-standardformatet för omdirigering fungerar för de flesta appar och scenarier, inklusive förmedlad autentisering och systemwebbläge. Använd standardformatet när det är möjligt.

Du kan dock behöva ändra omdirigera URI för avancerade scenarier, enligt beskrivningen nedan.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Scenarier som kräver en annan omdirigerings-URI

### <a name="cross-app-single-sign-on-sso"></a>Korsapp enkel inloggning (SSO)

För att Microsoft Identity-plattformen ska kunna dela token mellan appar måste varje app ha samma klient-ID eller program-ID. Detta är den unika identifierare som angavs när du registrerade din app i portalen (inte det programpaket-ID som du registrerar per app med Apple).

Omdirigerings-URI:erna måste vara olika för varje iOS-app. På så sätt kan Microsofts identitetstjänst unikt identifiera olika appar som delar ett program-ID. Varje program kan ha flera omdirigerings-URI:er registrerade i Azure-portalen. Varje app i din svit kommer att ha en annan omdirigera URI. Ett exempel:

Med följande programregistrering i Azure-portalen:

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

App1 använder `msauth.com.contoso.app1://auth` omdirigering App2 använder `msauth.com.contoso.app2://auth` App3-användningsområden`msauth.com.contoso.app1://auth`

### <a name="migrating-from-adal-to-msal"></a>Migrera från ADAL till MSAL

När du migrerar kod som använde Azure AD Authentication Library (ADAL) till MSAL kanske du redan har en omdirigerings-URI konfigurerad för din app. Du kan fortsätta använda samma omdirigera URI så länge din ADAL-app har konfigurerats för att stödja förmedlade scenarier och din omdirigera URI uppfyller kraven för MSAL-omdirigerings-URI-format.

## <a name="msal-redirect-uri-format-requirements"></a>MSAL omdirigera URI-formatkrav

* MSAL-omdirigerings-URI:n måste vara i formuläret`<scheme>://host`

    Var `<scheme>` finns en unik sträng som identifierar din app. Det är främst baserat på Bundle Identifier av ditt program för att garantera unikhet. Om appens paket-ID till `com.contoso.myapp`exempel är skulle din omdirigerings-URI vara i formuläret : `msauth.com.contoso.myapp://auth`.

    Om du migrerar från ADAL har din omdirigerings-URI troligen det här formatet: `<scheme>://[Your_Bundle_Id]`, var `scheme` är en unik sträng. Det här formatet fortsätter att fungera när du använder MSAL.

* `<scheme>`måste vara registrerad i appens Info.plist under `CFBundleURLTypes > CFBundleURLSchemes`.  I det här exemplet har Info.plist öppnats som källkod:

    ```xml
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
    

MSAL kontrollerar om din omdirigera URI registreras korrekt och returnerar ett fel om det inte är det.
    
* Om du vill använda universella länkar som `<scheme>` en `https` omdirigera URI, måste `CFBundleURLSchemes`vara och inte behöver deklareras i . Konfigurera i stället appen och domänen enligt Apples instruktioner på `handleMSALResponse:sourceApplication:` Universal `MSALPublicClientApplication` Links for Developers och anropa metoden [för](https://developer.apple.com/ios/universal-links/) när ditt program öppnas via en universell länk.

## <a name="use-a-custom-redirect-uri"></a>Använda en anpassad URI för omdirigering

Om du vill använda en `redirectUri` anpassad `MSALPublicClientApplicationConfig` omdirigerings-URI skickar du parametern till och skickar objektet till `MSALPublicClientApplication` när du initierar objektet. Om omdirigerings-URI:n `nil` är `redirectURIError`ogiltig returneras och anges med ytterligare information.  Ett exempel:

Mål C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>Hantera den url-öppnade händelsen

Programmet ska anropa MSAL när det får svar via URL-scheman eller universella länkar. Anropa `handleMSALResponse:sourceApplication:` metoden `MSALPublicClientApplication` för när programmet öppnas. Här är ett exempel för anpassade scheman:

Mål C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Nästa steg

Läs mer om [autentiseringsflöden och programscenarier](authentication-flows-app-scenarios.md)
