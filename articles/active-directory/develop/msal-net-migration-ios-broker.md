---
title: Migrera Xamarin iOS-program med Microsoft Authenticator från ADAL.NET till MSAL.NET | Azure
description: Lär dig hur du migrerar Xamarin iOS-program med Microsoft Authenticator från Azure AD Authentication Library för .NET (ADAL.NET) till Microsoft Authentication Library för .NET (MSAL.NET)
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
ms.openlocfilehash: 609aeeada78985466cb7c254cab32864e5d84b0e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875658"
---
# <a name="migrating-ios-applications-using-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrera iOS-program med Microsoft Authenticator från ADAL.NET till MSAL.NET

Du har använt ADAL.NET och iOS-Broker och det är dags att migrera till MSAL.NET [Microsoft Authentication Library](msal-overview.md), som stöder Service Broker på iOS från version 4,3 och senare. 

Var ska vi börja? Den här artikeln hjälper dig att migrera din Xamarin iOS-app från ADAL till MSAL.

## <a name="prerequisites"></a>Förutsättningar
Det här dokumentet förutsätter att du redan har en Xamarin iOS-app som är integrerad med iOS-Broker. Om du inte gör det skulle det vara bäst att gå över till MSAL.NET och påbörja Service Broker-implementering där. I [den här dokumentationen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications) finns information om hur du anropar iOS-broker i MSAL.net med ett nytt program.

## <a name="background"></a>Bakgrund

### <a name="what-are-brokers"></a>Vad är utjämnare?

Mäklare är program, som tillhandahålls av Microsoft, på Android och iOS ([Microsoft Authenticator](https://www.microsoft.com/account/authenticator) på iOS och android, Intune-företagsportal på Android). 

De aktiverar:

- Enkel inloggning,
- Enhets identifiering, vilket krävs av vissa [principer för villkorlig åtkomst](../conditional-access/overview.md) (se [enhets hantering](../conditional-access/conditions.md#device-platforms))
- Verifiering av program identifiering, krävs även i vissa företags scenarier (se för [Intunes hantering av mobil program eller Mam](https://docs.microsoft.com/intune/mam-faq))

## <a name="migrate-from-adal-to-msal"></a>Migrera från ADAL till MSAL

### <a name="step-1-enable-the-broker"></a>Steg 1: Aktivera Service Broker

<table>
<tr><td>Aktuell ADAL-kod:</td><td>MSAL motsvarighet:</td></tr>
<tr><td>
I ADAL.NET har Broker-stödet Aktiver ATS per autentisering, inaktiverat som standard. Du måste ange en `useBroker` flagga som sant `PlatformParameters` i konstruktorn för att anropa Broker:

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
I det här exemplet i den plattformsspecifik koden, i det här exemplet, ställer du in följande i sid åter givningen för iOS:`useBroker` 
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
I MSAL.NET aktive ras Broker-stöd för per offentlig klient program. Det är inaktiverat som standard. Använd följande för att aktivera det: 

`WithBroker()`parameter (anges till sant som standard) för att anropa Broker:

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
I ADAL.NET skickade du UIViewController som en del av PlatformParameters (se exemplet i steg 1). Men i MSAL.NET, för att ge utvecklaren mer flexibilitet, används ett objekt fönster, men krävs inte i normal iOS-användning. För att kunna använda Broker måste du dock ange objekt fönstret för att kunna skicka och ta emot svar från Broker. 
<table>
<tr><td>Aktuell ADAL-kod:</td><td>MSAL motsvarighet:</td></tr>
<tr><td>
UIViewController skickas till PlatformParamters i den iOS-speciella plattformen.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
I MSAL.NET måste du göra två saker för att ange objekt fönstret för iOS:

1) I `AppDelegate.cs`, `UIViewController()`anger du till en ny. `App.RootViewController` Den här tilldelningen ser till att det finns en UIViewController med anropet till Broker. Om den inte anges korrekt kan du få följande fel meddelande:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) På AcquireTokenInteractive-anropet använder du`.WithParentActivityOrWindow(App.RootViewController)`
och skicka i referensen till objekt fönstret you'will use.

**Till exempel:**

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

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Steg 3: Uppdatera AppDelegate för att hantera återanropet
Både ADAL och MSAL kommer att anropa Service Broker, och Broker kommer i sin tur att gå tillbaka till ditt program via `OpenUrl` -metoden `AppDelegate` i-klassen. Mer information finns [här](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS/_edit#step-two-update-appdelegate-to-handle-the-callback)

: heavy_check_mark:**det finns inga ändringar här mellan ADAL.net och MSAL.net**

### <a name="step-4-register-a-url-scheme"></a>Steg 4: Registrera ett URL-schema
ADAL.NET och MSAL.NET använder URL: er för att anropa Broker och returnera Service Broker-svaret tillbaka till appen. Registrera URL-schemat i `Info.plist` filen för din app enligt följande:

<table>
<tr><td>Aktuell ADAL-kod:</td><td>MSAL motsvarighet:</td></tr>
<tr><td>
URL-schemat är unikt för din app.
</td><td>
Filtypen 

`CFBundleURLSchemes`namnet måste innehålla 

`msauth.`

som ett prefix följt av din`CFBundleURLName`

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
>  Detta URL-schema kommer att bli en del av RedirectUri som används för att identifiera appen unikt när du tar emot svaret från Service Broker

</table>

### <a name="step-5-lsapplicationqueriesschemes"></a>Steg 5: LSApplicationQueriesSchemes

ADAL.net och MSAL.net använder `-canOpenURL:` båda för att kontrol lera om Service Broker är installerat på enheten. Lägg till rätt ID för iOS-Broker i avsnittet LSApplicationQueriesSchemes i filen info. plist enligt följande: 
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

### <a name="step-6-register-you-redirecturi-in-the-portal"></a>Steg 6: Registrera dig RedirectUri i portalen

ADAL.NET och MSAL.NET lägger både till ett extra krav på redirectUri vid mål koordinatorn. Registrera omdirigerings-URI: n med ditt program i portalen.
<table>
<tr><td>Aktuell ADAL-kod:</td><td>MSAL motsvarighet:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`exempel`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

exempel:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Mer information om hur du registrerar redirectUri i portalen finns i använda [Service Broker i Xamarin. iOS-program](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app) för mer information.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Xamarin iOS-/regionsspecifika överväganden med MSAL.net](msal-net-xamarin-ios-considerations.md). 
