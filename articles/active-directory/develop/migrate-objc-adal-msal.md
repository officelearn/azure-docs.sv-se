---
title: ADAL till MSAL-migreringsguide (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig skillnaderna mellan MSAL för iOS/macOS och Azure AD Authentication Library for ObjectiveC (ADAL. ObjC) och hur du migrerar till MSAL för iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 6050bdc8c2600998b9804b04b62102e74612719f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085174"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Migrera program till MSAL för iOS och macOS

Azure Active Directory Authentication Library ([ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)) skapades för att arbeta med Azure Active Directory-konton via v1.0-slutpunkten.

Microsoft Authentication Library för iOS och macOS (MSAL) är byggt för att fungera med alla Microsoft-identiteter som Azure Active Directory -konton (Azure AD), personliga Microsoft-konton och Azure AD B2C-konton via Microsofts identitetsplattform (formellt slutpunkten för Azure AD v2.0).

Microsoft-identitetsplattformen har några viktiga skillnader med Azure Active Directory v1.0. I den här artikeln belyser du dessa skillnader och ger vägledning om hur du migrerar en app från ADAL till MSAL.

## <a name="adal-and-msal-app-capability-differences"></a>Skillnader i ADAL- och MSAL-appfunktioner

### <a name="who-can-sign-in"></a>Vem kan logga in

* ADAL stöder bara arbets- och skolkonton – även kallade Azure AD-konton.
* MSAL stöder msa-konton (Personal Microsoft-konton) som Hotmail.com, Outlook.com och Live.com.
* MSAL stöder arbets- och skolkonton och Azure AD B2C-konton.

### <a name="standards-compliance"></a>Efterlevnad av standarder

* Slutpunkten för Microsoft identity Platform följer OAuth 2.0- och OpenId Connect-standarderna.

### <a name="incremental-and-dynamic-consent"></a>Inkrementellt och dynamiskt samtycke

* Azure Active Directory v1.0-slutpunkten kräver att alla behörigheter deklareras i förväg under programregistreringen. Det innebär att dessa behörigheter är statiska.
* Microsofts identitetsplattform gör att du kan begära behörigheter dynamiskt. Appar kan bara be om behörigheter efter behov och begära mer när appen behöver dem.

Mer information om skillnader mellan Azure Active Directory v1.0 och Microsoft identity platform finns i [Varför uppdatera till Microsoft identity platform (v2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="adal-and-msal-library-differences"></a>ADAL och MSAL bibliotek skillnader

MSAL:s offentliga API återspeglar några viktiga skillnader mellan Azure AD v1.0 och Microsoft identity-plattformen.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication istället för ADAuthenticationContext

`ADAuthenticationContext`är det första objektet som en ADAL-app skapar. Det representerar en instansiering av ADAL. Appar skapar en `ADAuthenticationContext` ny instans för varje Azure Active Directory-moln- och klientkombination (auktoritet). Detsamma `ADAuthenticationContext` kan användas för att hämta token för flera offentliga klientprogram.

I MSAL är huvudinteraktionen genom ett `MSALPublicClientApplication` objekt som modelleras efter [OAuth 2.0 Public Client](https://tools.ietf.org/html/rfc6749#section-2.1). En instans `MSALPublicClientApplication` av kan användas för att interagera med flera AAD-moln och klienter, utan att behöva skapa en ny instans för varje myndighet. För de flesta `MSALPublicClientApplication` appar räcker det med en instans.

### <a name="scopes-instead-of-resources"></a>Scope i stället för resurser

I ADAL var en app tvungen att `https://graph.microsoft.com` tillhandahålla en *resursidentifierare* som att hämta token från Azure Active Directory v1.0-slutpunkten. En resurs kan definiera ett antal scope, eller oAuth2Permissions i appmanifestet, som den förstår. Detta gjorde det möjligt för klientappar att begära token från den resursen för en viss uppsättning scope som fördefinieras under appregistrering.

I MSAL, i stället för en enda resursidentifierare, innehåller appar en uppsättning scope per begäran. Ett scope är en resursidentifierare följt av ett behörighetsnamn i formulärresursen/behörigheten. Till exempel, `https://graph.microsoft.com/user.read`

Det finns två sätt att tillhandahålla scope i MSAL:

* Ange en lista över alla behörigheter som dina appar behöver. Ett exempel: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    I det här fallet `directory.read` begär `directory.write` appen behörigheterna och. Användaren uppmanas att godkänna dessa behörigheter om de inte har samtyckt till dem tidigare för den här appen. Programmet kan också få ytterligare behörigheter som användaren redan har samtyckt till för programmet. Användaren uppmanas bara att godkänna nya behörigheter eller behörigheter som inte har beviljats.

* Omfattningen. `/.default`

Detta är det inbyggda omfånget för alla program. Den refererar till den statiska listan över behörigheter som konfigurerades när programmet registrerades. Dess beteende liknar det `resource`i . Detta kan vara användbart när du migrerar för att säkerställa att en liknande uppsättning scope och användarupplevelse upprätthålls.

Om du `/.default` vill använda `/.default` scopet lägger du till resursidentifieraren. Till exempel: `https://graph.microsoft.com/.default`. Om resursen slutar med`/`ett snedstreck `/.default`( ) bör du fortfarande lägga till , inklusive`//`det inledande snedstrecket framåt, vilket resulterar i ett scope som har ett dubbelt snedstreck ( ) i den.

Du kan läsa mer information om hur du använder "/.default"-scopet [här](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope)

### <a name="supporting-different-webview-types--browsers"></a>Stöd för olika WebView-typer & webbläsare

ADAL stöder endast UIWebView/WKWebView för iOS och WebView för macOS. MSAL för iOS stöder fler alternativ för att visa webbinnehåll när `UIWebView`du begär en auktoriseringskod och stöder inte längre; som kan förbättra användarupplevelsen och säkerheten.

Som standard använder MSAL på iOS [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), som är den webbkomponent som Apple rekommenderar för autentisering på iOS 12+ enheter. Det ger SSO -förmåner (Single Sign-On) genom cookie-delning mellan appar och webbläsaren Safari.

Du kan välja att använda en annan webbkomponent beroende på appkrav och vilken slutanvändarupplevelse du vill ha. Se [webbvytyper som stöds](customize-webviews.md) för fler alternativ.

När du migrerar från ADAL `WKWebView` till MSAL ger den användarupplevelse som är mest lik ADAL på iOS och macOS. Vi uppmuntrar dig `ASWebAuthenticationSession` att migrera till på iOS, om möjligt. För macOS uppmuntrar vi `WKWebView`dig att använda .

### <a name="account-management-api-differences"></a>API-skillnader för kontohantering

När du anropar `acquireToken()` `acquireTokenSilent()`ADAL-metoderna `ADUserInformation` eller får du ett `id_token` objekt som innehåller en lista med anspråk från som representerar kontot som autentiseras. Dessutom `ADUserInformation` returnerar `userId` en baserat `upn` på anspråket. Efter första interaktiva token förvärv, ADAL förväntar utvecklare att tillhandahålla `userId` i alla tysta samtal.

ADAL tillhandahåller inte ett API för att hämta kända användaridentiteter. Den är beroende av appen för att spara och hantera dessa konton.

MSAL innehåller en uppsättning API:er för att lista alla konton som är kända för MSAL utan att behöva hämta en token.

Precis som ADAL returnerar MSAL kontoinformation som `id_token`innehåller en lista över anspråk från . Det är en `MSALAccount` del av `MSALResult` objektet inuti objektet.

MSAL innehåller en uppsättning API:er för att ta bort konton, vilket gör de borttagna kontona otillgängliga för appen. När kontot har tagits bort uppmanas användaren att göra interaktiva tokeninsamlingar senare. Kontoborttagning gäller endast klientprogrammet som startade det och tar inte bort kontot från de andra apparna som körs på enheten eller från systembläddraren. Detta säkerställer att användaren fortsätter att ha en SSO-upplevelse på enheten även efter att ha loggat ut från en enskild app.

Dessutom returnerar MSAL också en kontoidentifierare som kan användas för att begära en token tyst senare. Kontoidentifieraren (som är `identifier` tillgänglig `MSALAccount` via egenskapen i objektet) kan dock inte visas och du kan inte anta vilket format den har i och du bör inte heller försöka tolka eller tolka den.

### <a name="migrating-the-account-cache"></a>Migrera kontocachen

När du migrerar från ADAL lagrar appar `userId`normalt ADAL:s `identifier` , som inte har det som krävs av MSAL. Som ett engångsmigreringssteg kan en app fråga ett MSAL-konto med ADAL:s userId med följande API:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Det här API:et läser både MSAL:s och ADAL:s cacheminne för att hitta kontot av ADAL userId (UPN).

Om kontot hittas bör utvecklaren använda kontot för att göra tyst tokeninsamling. Den första tyst token anskaffning kommer effektivt att uppgradera kontot, och utvecklaren kommer`identifier`att få en MSAL kompatibel kontoidentifierare i MSAL resultatet ( ). Därefter ska `identifier` endast användas för kontosökningar med hjälp av följande API:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Även om det är möjligt att `userId` fortsätta använda ADAL:s `userId` för alla åtgärder i MSAL, eftersom det är baserat på UPN, är det föremål för flera begränsningar som resulterar i en dålig användarupplevelse. Om UPN till exempel ändras måste användaren logga in igen. Vi rekommenderar att alla appar använder `identifier` kontot som inte kan visas för alla åtgärder.

Läs mer om [migrering av cachetillstånd](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Ändringar av tokenförvärv

MSAL introducerar några ändringar av tokeninsamlingsanrop:

* Liksom ADAL, `acquireTokenSilent` alltid resulterar i en tyst begäran.
* Till skillnad `acquireToken` från ADAL resulterar alltid i användargränssnittet, antingen via webbvyn eller i Microsoft Authenticator-appen. Beroende på SSO-tillståndet i webview/Microsoft Authenticator kan användaren uppmanas att ange sina autentiseringsuppgifter.
* I `acquireToken` ADAL, `AD_PROMPT_AUTO` med första försöken tyst token förvärv, och visar bara UI om den tysta begäran misslyckas. I MSAL kan den här logiken uppnås genom att först anropa `acquireTokenSilent` och endast anropa `acquireToken` om tyst förvärv misslyckas. Detta gör det möjligt för utvecklare att anpassa användarupplevelsen innan du startar interaktiv tokeninsamling.

### <a name="error-handling-differences"></a>Skillnader i felhantering

MSAL ger mer klarhet mellan fel som kan hanteras av din app och de som kräver åtgärder av användaren. Det finns ett begränsat antal fel utvecklare måste hantera:

* `MSALErrorInteractionRequired`: Användaren måste göra en interaktiv begäran. Detta kan orsakas av olika orsaker, till exempel en förfallen autentiseringssession, principen villkorlig åtkomst har ändrats, en uppdateringstoken har upphört att gälla eller återkallades, det finns inga giltiga token i cacheminnet och så vidare.
* `MSALErrorServerDeclinedScopes`: Begäran slutfördes inte helt och vissa scope har inte beviljats åtkomst. Detta kan orsakas av en användare som avböjer samtycke till ett eller flera scope.

Det är valfritt att hantera alla andra fel i [ `MSALError` listan.](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) Du kan använda informationen i dessa fel för att förbättra användarupplevelsen.

Se [Hantera undantag och fel med MSAL](msal-handling-exceptions.md) för mer information om MSAL-felhantering.

### <a name="broker-support"></a>Support för mäklare

MSAL, som börjar med version 0.3.0, ger stöd för förmedlad autentisering med hjälp av Microsoft Authenticator-appen. Microsoft Authenticator aktiverar också stöd för scenarier för villkorlig åtkomst. Exempel på scenarier för villkorlig åtkomst är principer för enhetsefterlevnad som kräver att användaren registrerar enheten via Intune eller registrerar sig hos AAD för att få en token. Och MAM-principer (Mobile Application Management), som kräver bevis på efterlevnad innan din app kan få en token.

Så här aktiverar du mäklare för ditt program:

1. Registrera ett kompatibla uri-format för en mäklare som är kompatibel för programmet. Mäklaren kompatibel omdirigera URI-format är `msauth.<app.bundle.id>://auth`. Ersätt `<app.bundle.id>` med programmets bunt-ID. Om du migrerar från ADAL och din ansökan redan var mäklare kan, det finns inget extra du behöver göra. Din tidigare omdirigera URI är helt kompatibel med MSAL, så du kan hoppa till steg 3.

2. Lägg till programmets omdirigerings-URI-schema i filen info.plist. För standard-MSAL-omdirigerings-URI är `msauth.<app.bundle.id>`formatet . Ett exempel:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Lägg till följande scheman i appens Info.plist under LSApplicationQueriesSchemes:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Lägg till följande i filen AppDelegate.m för att hantera motringningar: Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
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

### <a name="business-to-business-b2b"></a>Företag till företag (B2B)

I ADAL skapar du separata `ADAuthenticationContext` instanser för varje klient som appen begär token för. Detta är inte längre ett krav i MSAL. I MSAL kan du skapa `MSALPublicClientApplication` en enda instans av och använda den för alla AAD-moln och organisationer genom att ange en annan behörighet för acquireToken och acquireTokenSilent-anrop.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO i samarbete med andra SDK:er

MSAL för iOS kan uppnå SSO via en enhetlig cache med följande SDK:er:

- ADAL Mål-C 2.7.x+
- MSAL.NET för Xamarin 2.4.x+
- ADAL.NET för Xamarin 4.4.x+

SSO uppnås via iOS-nyckelringsdelning och är endast tillgängligt mellan appar som publiceras från samma Apple Developer-konto.

SSO genom iOS nyckelring delning är den enda tysta SSO typ.

På macOS kan MSAL uppnå SSO med andra MSAL för iOS- och macOS-baserade program och ADAL Objective-C-baserade program.

MSAL på iOS stöder också två andra typer av SSO:

* SSO via webbläsaren. MSAL för iOS stöder `ASWebAuthenticationSession`, som tillhandahåller SSO via cookies som delas mellan andra appar på enheten och särskilt webbläsaren Safari.
* SSO via en autentiseringsmäklare. På en iOS-enhet fungerar Microsoft Authenticator som autentiseringsmäklare. Den kan följa principer för villkorlig åtkomst, till exempel kräver en kompatibel enhet, och tillhandahåller SSO för registrerade enheter. MSAL SDK:er som börjar med version 0.3.0 stöder som standard en mäklare.

## <a name="intune-mam-sdk"></a>Intune MAM SDK

[Intune MAM SDK](https://docs.microsoft.com/intune/app-sdk-get-started) stöder MSAL för iOS som börjar med version [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL och ADAL i samma app

ADAL version 2.7.0, och högre, kan inte samexistera med MSAL i samma program. Den främsta orsaken är på grund av den gemensamma koden för den delade undermodulen. Eftersom Objective-C inte stöder namnområden, om du lägger till både ADAL- och MSAL-ramverk i ditt program, kommer det att finnas två instanser av samma klass. Det finns ingen garanti för vilken man blir vald vid körning. Om båda SDK:erna använder samma version av klassen i konflikt kan appen fortfarande fungera. Men om det är en annan version kan din app uppleva oväntade krascher som är svåra att diagnostisera.

Köra ADAL och MSAL i samma produktionsprogram stöds inte. Men om du bara testar och migrerar dina användare från ADAL Objective-C till MSAL för iOS och macOS kan du fortsätta använda [ADAL Objective-C 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10). Det är den enda versionen som fungerar med MSAL i samma program. Det kommer inte att finnas några nya funktionsuppdateringar för den här ADAL-versionen, så den bör endast användas för migrerings- och testningsändamål. Din app bör inte förlita sig på ADAL och MSAL samexistens på lång sikt.

ADAL och MSAL samexistens i samma program stöds inte.
ADAL och MSAL samexistens mellan flera program stöds fullt ut.

## <a name="practical-migration-steps"></a>Praktiska migreringssteg

### <a name="app-registration-migration"></a>Migrering av appregistrering

Du behöver inte ändra ditt befintliga AAD-program för att växla till MSAL och aktivera AAD-konton. Men om ditt ADAL-baserade program inte stöder förmedlad autentisering måste du registrera en ny omdirigera URI för programmet innan du kan växla till MSAL.

Omdirigera URI bör vara i `msauth.<app.bundle.id>://auth`detta format: . Ersätt `<app.bundle.id>` med programmets bunt-ID. Ange omdirigerings-URI i [Azure-portalen](https://aka.ms/MobileAppReg).

För att endast iOS ska kunna stödja certbaserad autentisering måste ytterligare en uri för omdirigering registreras i ditt program och i Azure-portalen i följande format: `msauth://code/<broker-redirect-uri-in-url-encoded-form>`. Till exempel, `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

Vi rekommenderar alla appar att registrera både omdirigerings-URI:er.

Om du vill lägga till stöd för inkrementellt medgivande väljer du de API:er och behörigheter som appen är konfigurerad för att begära åtkomst till i din appregistrering under fliken **API-behörigheter.**

Om du migrerar från ADAL och vill stödja både AAD- och MSA-konton måste din befintliga programregistrering uppdateras för att stödja båda. Vi rekommenderar inte att du uppdaterar din befintliga produktionsapp för att stödja både AAD och MSA direkt. Skapa i stället ett annat klient-ID som stöder både AAD och MSA för testning, och när du har verifierat att alla scenarier fungerar uppdaterar du den befintliga appen.

### <a name="add-msal-to-your-app"></a>Lägga till MSAL i appen

Du kan lägga till MSAL SDK i appen med ditt önskade pakethanteringsverktyg. Se [detaljerade instruktioner här](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Uppdatera appens infolista

Endast för iOS lägger du till programmets omdirigerings-URI-schema i din info.plist-fil. För ADAL mäklare kompatibla appar, bör det vara där redan. Standardsystemet FÖR MSAL-omdirigering `msauth.<app.bundle.id>`kommer att vara i formatet: .  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Lägg till följande scheman i appens `LSApplicationQueriesSchemes`Info.plist under .

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Uppdatera din AppDelegate-kod

För endast iOS lägger du till följande i filen AppDelegate.m:

Mål C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
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

Detta gör det möjligt för MSAL att hantera svar från mäklaren och webbkomponenten.
Detta var inte nödvändigt i ADAL eftersom det "swizzled" app delegera metoder automatiskt. Lägga till den manuellt är mindre felbenägna och ger programmet mer kontroll.

### <a name="enable-token-caching"></a>Aktivera cachelagring av token

Som standard cachelagrar MSAL appens token i iOS- eller macOS-nyckelringen. 

Så här aktiverar du tokencachelagring:
1. Se till att din ansökan är korrekt signerad
2. Gå till fliken > Xcode Project Settings > **Capabilities**Aktivera**nyckelringsdelning**
3. Klicka **+** och ange följande post **för nyckelringsgrupper:** 3.a För iOS anger du `com.microsoft.adalcache` 3.b för macOS-inmatning`com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Skapa MSALPublicClientApplication och byt till dess acquireToken och förvärvaTokeSilent-samtal

Du kan `MSALPublicClientApplication` skapa med följande kod:

Mål C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Anropa sedan API:et för kontohantering för att se om det finns några konton i cachen:

Mål C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift:

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



eller läsa alla konton:

Mål C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift:

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Om ett konto hittas anropar du MSAL-API:et: `acquireTokenSilent`

Mål C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Nästa steg

Läs mer om [autentiseringsflöden och programscenarier](authentication-flows-app-scenarios.md)
