---
title: Migrera Xamarin-appar med hjälp av asynkrona meddelandeköer till MSAL.NET
titleSuffix: Microsoft identity platform
description: Läs om hur du migrerar Xamarin iOS-appar som använder Microsoft Authenticator från ADAL.NET till MSAL.NET.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: de259daa7fd27cc4f138c294a7f347502ca482a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185833"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrera iOS-program som använder Microsoft Authenticator från ADAL.NET till MSAL.NET

Du har använt Azure Active Directory Authentication Library för .NET (ADAL.NET) och iOS-mäklaren. Nu är det dags att migrera till [Microsoft Authentication Library](msal-overview.md) for .NET (MSAL.NET), som stöder mäklaren på iOS från version 4.3 och framåt. 

Var ska du börja? Den här artikeln hjälper dig att migrera din Xamarin iOS-app från ADAL till MSAL.

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du redan har en Xamarin iOS-app som är integrerad med iOS-mäklaren. Om du inte gör det, flytta direkt till MSAL.NET och börja mäklaren genomförandet där. Information om hur du anropar iOS-mäklaren i MSAL.NET med ett nytt program finns i [den här dokumentationen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Bakgrund

### <a name="what-are-brokers"></a>Vad är mäklare?

Mäklare är program som tillhandahålls av Microsoft på Android och iOS. (Se [Appen Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) på iOS och Android och Intune Company Portal-appen på Android.) 

De gör det möjligt att:

- Enkel inloggning.
- Enhetsidentifiering, vilket krävs av vissa [principer för villkorlig åtkomst](../conditional-access/overview.md). Mer information finns i [Enhetshantering](../conditional-access/concept-conditional-access-conditions.md#device-platforms).
- Programidentifieringsverifiering, vilket också krävs i vissa företagsscenarier. Mer information finns i [Intune-hantering av mobilappar (MAM).](https://docs.microsoft.com/intune/mam-faq)

## <a name="migrate-from-adal-to-msal"></a>Migrera från ADAL till MSAL

### <a name="step-1-enable-the-broker"></a>Steg 1: Aktivera mäklaren

<table>
<tr><td>Nuvarande ADAL-kod:</td><td>MSAL-motsvarighet:</td></tr>
<tr><td>
I ADAL.NET aktiverades mäklarstöd per autentisering. Den är inaktiverad som standard. Du var tvungen att ställa in en 

`useBroker`flagga till sant `PlatformParameters` i konstruktorn att ringa mäklaren:

```csharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
I den plattformsspecifika koden, i det här exemplet, i sidrendaren för iOS, ställer du också in`useBroker` 
flagga till sant:
```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Inkludera sedan parametrarna i det inhämtningstoken-anropet:
```csharp
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
I MSAL.NET aktiveras mäklarstöd per offentligclientApplication.In MSAL.NET, broker support is enabled on a per-PublicClientApplication basis. Den är inaktiverad som standard. Om du vill aktivera den använder du 

`WithBroker()`parameter (inställd på true som standard) för att ringa mäklaren:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
I det inhämtningstoken anrop:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Steg 2: Ange en UIViewController()
I ADAL.NET passerade du i en UIViewController `PlatformParameters`som en del av . (Se exemplet i steg 1.) I MSAL.NET, för att ge utvecklare mer flexibilitet, används ett objektfönster, men det krävs inte i vanlig iOS-användning. Om du vill använda mäklaren ställer du in objektfönstret för att skicka och ta emot svar från mäklaren. 
<table>
<tr><td>Nuvarande ADAL-kod:</td><td>MSAL-motsvarighet:</td></tr>
<tr><td>
En UIViewController skickas in i 

`PlatformParameters`i den iOS-specifika plattformen.

```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
I MSAL.NET gör du två saker för att ställa in objektfönstret för iOS:

1. I `AppDelegate.cs`, `App.RootViewController` inställd `UIViewController()`på en ny . Den här tilldelningen säkerställer att det finns en UIViewController med samtalet till mäklaren. Om den inte är korrekt inställd kan det här felet visas:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Använd och skicka referensen till `.WithParentActivityOrWindow(App.RootViewController)`det objektfönster du ska använda i anropet AcquireTokenInteractive.

**Till exempel:**

Följande gäller i `App.cs`:
```csharp
   public static object RootViewController { get; set; }
```
Följande gäller i `AppDelegate.cs`:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
I det inhämtningstoken anrop:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Steg 3: Uppdatera AppDelegate för att hantera motringningen
Både ADAL och MSAL ringa mäklaren, och mäklaren i `OpenUrl` sin `AppDelegate` tur ringer tillbaka till din ansökan genom metoden för klassen. Mer information finns i [den här dokumentationen](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback).

Det finns inga förändringar här mellan ADAL.NET och MSAL.NET.

### <a name="step-4-register-a-url-scheme"></a>Steg 4: Registrera ett URL-schema
ADAL.NET och MSAL.NET använda webbadresser för att anropa mäklaren och returnera mäklarsvaret tillbaka till appen. Registrera URL-schemat `Info.plist` i filen för din app enligt följande:

<table>
<tr><td>Nuvarande ADAL-kod:</td><td>MSAL-motsvarighet:</td></tr>
<tr><td>
URL-schemat är unikt för din app.
</td><td>
Filtypen 

`CFBundleURLSchemes`namn måste innehålla 

`msauth.`

som prefix, följt av din`CFBundleURLName`

Exempel: `$"msauth.(BundleId")`

```csharp
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
> Det här URL-schemat blir en del av den omdirigera URI som används för att unikt identifiera appen när den tar emot svaret från mäklaren.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Steg 5: Lägg till mäklaridentifieraren i avsnittet LSApplicationQueriesSchemes

ADAL.NET och MSAL.NET båda använder `-canOpenURL:` för att kontrollera om mäklaren är installerad på enheten. Lägg till rätt identifierare för iOS-mäklaren i avsnittet LSApplicationQueriesSchemes i filen info.plist enligt följande:

<table>
<tr><td>Nuvarande ADAL-kod:</td><td>MSAL-motsvarighet:</td></tr>
<tr><td>
Användningar 

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Användningar 

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>Steg 6: Registrera din omdirigera URI i portalen

ADAL.NET och MSAL.NET lägger båda till ett extra krav på omdirigerings-URI när den riktar sig till mäklaren. Registrera omdirigerings-URI:n med ditt program i portalen.
<table>
<tr><td>Nuvarande ADAL-kod:</td><td>MSAL-motsvarighet:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Exempel: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Exempel:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Mer information om hur du registrerar omdirigera URI i portalen finns [i Utnyttja mäklaren i Xamarin.iOS-program](msal-net-use-brokers-with-xamarin-apps.md#step-8-make-sure-the-redirect-uri-is-registered-with-your-app).

## <a name="next-steps"></a>Nästa steg

Läs mer om [Xamarin iOS-specifika överväganden med MSAL.NET](msal-net-xamarin-ios-considerations.md). 
