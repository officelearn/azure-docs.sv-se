---
title: Migrera Xamarin iOS-program som använder Microsoft Authenticator från ADAL.NET till MSAL.NET
titleSuffix: Microsoft identity platform
description: Lär dig hur du migrerar Xamarin iOS-program som använder Microsoft Authenticator från Azure AD Authentication Library för .NET (ADAL.NET) till Microsoft Authentication Library för .NET (MSAL.NET).
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c64f9f371424eddc7295b6ec40bda1ebdaaafd5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175584"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrera iOS-program som använder Microsoft Authenticator från ADAL.NET till MSAL.NET

Du har använt Azure Active Directory Authentication Library för .NET (ADAL.NET) och iOS-Broker. Nu är det dags att migrera till [Microsoft Authentication Library](msal-overview.md) för .net (MSAL.net), som stöder Service Broker på iOS från version 4,3 och senare. 

Var ska du börja? Den här artikeln hjälper dig att migrera din Xamarin iOS-app från ADAL till MSAL.

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan har en Xamarin iOS-app som är integrerad med iOS-Broker. Om du inte gör det går du direkt till MSAL.NET och påbörjar Broker-implementering där. Information om hur du anropar iOS-Broker i MSAL.NET med ett nytt program finns i [den här dokumentationen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Bakgrund

### <a name="what-are-brokers"></a>Vad är utjämnare?

Mäklare är program som tillhandahålls av Microsoft på Android och iOS. (Se [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) -appen på iOS och android och Intune-företagsportal-appen på Android.) 

De aktiverar:

- Enkel inloggning.
- Enhets identifiering, vilket krävs av vissa [principer för villkorlig åtkomst](../conditional-access/overview.md). Mer information finns i [enhets hantering](../conditional-access/conditions.md#device-platforms).
- Verifiering av program identifiering, som också krävs i vissa företags scenarier. Mer information finns i [Intunes hantering av mobil program (MAM)](https://docs.microsoft.com/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>Migrera från ADAL till MSAL

### <a name="step-1-enable-the-broker"></a>Steg 1: aktivera Service Broker

<table>
<tr><td>Aktuell ADAL-kod:</td><td>MSAL motsvarighet:</td></tr>
<tr><td>
I ADAL.NET har Broker-stödet Aktiver ATS per autentisering. Den är inaktive rad som standard. Du var tvungen att ange en 

`useBroker` flagga till true i `PlatformParameters`-konstruktorn för att anropa Service Broker:

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
I det här exemplet i den plattformsspecifik koden i det här exemplet anger du `useBroker` 
flagga till sant:
```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Ta sedan med parametrarna i anropet för inhämta token:
```CSharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
I MSAL.NET aktive ras stöd för service nivå per PublicClientApplication. Den är inaktive rad som standard. Om du vill aktivera det använder du 

`WithBroker()` parameter (anges till sant som standard) för att anropa Service Broker:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
I Hämta token-anrop:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Steg 2: Ange en UIViewController ()
I ADAL.NET angav du en UIViewController som en del av `PlatformParameters`. (Se exemplet i steg 1.) I MSAL.NET används ett objekt fönster, men det krävs inte i normal iOS-användning för att ge utvecklare mer flexibilitet. Om du vill använda Service Broker ställer du in fönstret objekt för att skicka och ta emot svar från Service Broker. 
<table>
<tr><td>Aktuell ADAL-kod:</td><td>MSAL motsvarighet:</td></tr>
<tr><td>
En UIViewController skickas till 

`PlatformParameters` på iOS-plattformen.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
I MSAL.NET gör du två saker för att ange objekt fönstret för iOS:

1. I `AppDelegate.cs`anger `App.RootViewController` till en ny `UIViewController()`. Den här tilldelningen säkerställer att det finns en UIViewController med anropet till Broker. Om den inte anges korrekt kan du få följande fel meddelande: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Använd `.WithParentActivityOrWindow(App.RootViewController)`på AcquireTokenInteractive-anropet och skicka i referensen till objekt fönstret som du använder.

**Exempel:**

Följande gäller i `App.cs`:
```CSharp
   public static object RootViewController { get; set; }
```
Följande gäller i `AppDelegate.cs`:
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
I Hämta token-anrop:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Steg 3: uppdatera AppDelegate för att hantera återanropet
Både ADAL och MSAL anropar Service Broker och Broker i tur anrop till ditt program via `OpenUrl`-metoden i klassen `AppDelegate`. Mer information finns i [den här dokumentationen](msal-net-use-brokers-with-xamarin-apps.md#step-2-update-appdelegate-to-handle-the-callback).

Det finns inga ändringar här mellan ADAL.NET och MSAL.NET.

### <a name="step-4-register-a-url-scheme"></a>Steg 4: registrera ett URL-schema
ADAL.NET och MSAL.NET använder URL: er för att anropa Broker och returnera Service Broker-svaret tillbaka till appen. Registrera URL-schemat i `Info.plist`-filen för din app enligt följande:

<table>
<tr><td>Aktuell ADAL-kod:</td><td>MSAL motsvarighet:</td></tr>
<tr><td>
URL-schemat är unikt för din app.
</td><td>
Filtypen 

`CFBundleURLSchemes` namn måste innehålla 

`msauth.`

som ett prefix följt av din `CFBundleURLName`

Exempel: `$"msauth.(BundleId")`

```CSharp
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

> [!NOTE]
> Detta URL-schema blir en del av omdirigerings-URI: n som används för att unikt identifiera appen när den tar emot svaret från Broker.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Steg 5: Lägg till Service Broker-identifieraren i avsnittet LSApplicationQueriesSchemes

ADAL.NET och MSAL.NET använder båda `-canOpenURL:` för att kontrol lera om Service Broker är installerat på enheten. Lägg till rätt ID för iOS-Broker i avsnittet LSApplicationQueriesSchemes i filen info. plist enligt följande:

<table>
<tr><td>Aktuell ADAL-kod:</td><td>MSAL motsvarighet:</td></tr>
<tr><td>
Användningsområden 

`msauth`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Användningsområden 

`msauthv2`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>Steg 6: registrera din omdirigerings-URI i portalen

ADAL.NET och MSAL.NET lägger både till ett extra krav på omdirigerings-URI: n när den är riktad mot Broker. Registrera omdirigerings-URI: n med ditt program i portalen.
<table>
<tr><td>Aktuell ADAL-kod:</td><td>MSAL motsvarighet:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Exempel: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Exempel:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Mer information om hur du registrerar omdirigerings-URI: n i portalen finns i [utnyttja Service Broker i Xamarin. iOS-program](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Xamarin iOS-/regionsspecifika överväganden med MSAL.net](msal-net-xamarin-ios-considerations.md). 
