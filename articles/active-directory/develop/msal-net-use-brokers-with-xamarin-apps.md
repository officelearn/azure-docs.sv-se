---
title: Använda mäklare med Xamarin, iOS, & Android | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du migrerar Xamarin iOS-program som kan använda Microsoft Authenticator från Azure AD Authentication Library för .NET (ADAL.NET) till Microsoft Authentication Library för .NET (MSAL.NET)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49198909da103debd77fcf0d630e0fa16c1e4448
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424228"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Använda Microsoft Authenticator eller Microsoft Intune Företagsportal på Xamarin-program

På Android och iOS är-utjämnare som Microsoft Authenticator eller Microsoft Intune Företagsportal aktivera (endast Android):

- Enkel inloggning (SSO). Användarna behöver inte logga in på varje program.
- Enhets identifiering. Service Broker har åtkomst till enhets certifikatet som skapades på enheten när den var ansluten till arbets platsen.
- Verifiering av program identifiering. När ett program anropar Broker, skickar den sin omdirigerings-URL och Service Broker verifierar den.

Programutvecklare måste använda parametern `WithBroker()` när de anropar `PublicClientApplicationBuilder.CreateApplication`-metoden för att aktivera någon av dessa funktioner. `.WithBroker()` har värdet true som standard. Utvecklare måste också följa stegen här för [iOS](#brokered-authentication-for-ios) -eller [Android](#brokered-authentication-for-android) -program.

## <a name="brokered-authentication-for-ios"></a>Brokered Authentication för iOS

Följ dessa steg för att aktivera din Xamarin. iOS-app för att kommunicera med [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) -appen.

### <a name="step-1-enable-broker-support"></a>Steg 1: Aktivera stöd för Broker
Stöd för Service Broker är aktiverat per PublicClientApplication. Den är inaktive rad som standard. Använd `WithBroker()` parameter (anges till sant som standard) när du skapar PublicClientApplication via PublicClientApplicationBuilder.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Steg 2: aktivera nyckel rings åtkomst

Om du vill aktivera nyckel rings åtkomst måste ditt program ha en åtkomst grupp för nyckel ringar. Du kan använda `WithIosKeychainSecurityGroup()`-API: et för att ange åtkomst gruppen för nyckel ringar när du skapar ditt program:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Mer information finns i [aktivera nyckel rings åtkomst](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Steg 3: uppdatera AppDelegate för att hantera återanropet
När Microsoft Authentication Library för .NET (MSAL.NET) anropar Broker, anropar Broker i tur och tillbaka till ditt program via `OpenUrl`-metoden i `AppDelegate`-klassen. Eftersom MSAL väntar på svar från Service Broker måste ditt program samar beta för att anropa MSAL.NET tillbaka. Om du vill aktivera detta samarbete uppdaterar du `AppDelegate.cs`-filen så att den åsidosätter följande metod.

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

Den här metoden anropas varje gång programmet startas. Den används som en möjlighet att bearbeta svaret från Service Broker och slutföra autentiseringsprocessen som initierats av MSAL.NET.

### <a name="step-4-set-a-uiviewcontroller"></a>Steg 4: Ange en UIViewController ()
Fortfarande i `AppDelegate.cs`måste du ange ett objekt fönster. Normalt, med Xamarin iOS, behöver du inte ange objekt fönstret. Du behöver ett objekt fönster för att kunna skicka och ta emot svar från Service Broker. 

Det gör du genom att göra två saker. 
1. I `AppDelegate.cs`anger du `App.RootViewController` till en ny `UIViewController()`. Den här tilldelningen ser till att det finns en UIViewController med anropet till Broker. Om den inte anges korrekt kan du få följande fel meddelande: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. På AcquireTokenInteractive-anropet använder du `.WithParentActivityOrWindow(App.RootViewController)` och skickar i referensen till objekt fönstret som du kommer att använda.

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
I Hämta token-anrop:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

### <a name="step-5-register-a-url-scheme"></a>Steg 5: registrera ett URL-schema
MSAL.NET använder URL: er för att anropa Service Broker och returnerar sedan Service Broker-svaret tillbaka till din app. Om du vill slutföra den runda resan registrerar du ett URL-schema för din app i `Info.plist`-filen.

`CFBundleURLSchemes` namnet måste innehålla `msauth.` som prefix, följt av din `CFBundleURLName`.

`$"msauth.(BundleId)"`

**Till exempel:**

`msauth.com.yourcompany.xforms`

> [!NOTE]
> Detta URL-schema blir en del av omdirigerings-URI: n som används för att unikt identifiera din app när den tar emot svaret från Service Broker.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Steg 6: Lägg till Service Broker-identifieraren i avsnittet LSApplicationQueriesSchemes
MSAL använder `–canOpenURL:` för att kontrol lera om Broker är installerad på enheten. I iOS 9 är Apple låst för vilka scheman ett program kan fråga efter. 

Lägg till `msauthv2` i avsnittet `LSApplicationQueriesSchemes` i `Info.plist`s filen.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Steg 7: registrera din omdirigerings-URI i program portalen
Om du använder Service Broker läggs ett extra krav på omdirigerings-URI: n. Omdirigerings-URI: n _måste_ ha följande format:
```csharp
$"msauth.{BundleId}://auth"
```
**Till exempel:**
```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Observera att omdirigerings-URI: n matchar `CFBundleURLSchemes` namn som du inkluderade i `Info.plist`-filen.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Steg 8: kontrol lera att omdirigerings-URI: n är registrerad i appen

Den här omdirigerings-URI: n måste registreras på App Registration-portalen (https://portal.azure.com) som en giltig omdirigerings-URI för programmet. 

Portalen har en ny Experience app Registration-portal som hjälper dig att beräkna den asynkrona svars-URI: n från paket-ID: t.

1. Välj **autentisering** i appens registrering och välj **testa den nya upplevelsen**.

   ![Prova den nya appens registrerings upplevelse](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Välj **Lägg till en plattform**.

   ![Lägg till en plattform](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. När listan över plattformar stöds väljer du **iOS**.

   ![Konfigurera iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Ange ditt paket-ID enligt begäran och välj sedan **Konfigurera**.

   ![Ange paket-ID](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

1. Omdirigerings-URI: n beräknas för dig.

   ![Kopiera omdirigerings-URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Brokered Authentication för Android

MSAL.NET stöder bara Xamarin. iOS-plattformen för tillfället. Den har ännu inte stöd för utjämnare för Xamarin. Android-plattformen.

Det inbyggda Android-biblioteket i MSAL har redan stöd för det. Mer information finns [i utjämnad autentisering i Android](brokered-auth.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [universell Windows-plattform-/regionsspecifika överväganden med MSAL.net](msal-net-uwp-considerations.md).
