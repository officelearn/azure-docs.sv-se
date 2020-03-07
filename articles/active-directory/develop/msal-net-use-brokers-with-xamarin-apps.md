---
title: Använda utjämnare med Xamarin iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Lär dig att konfigurera Xamarin iOS-program som kan använda Microsoft Authenticator och Microsoft Authentication Library för .NET (MSAL.NET). Lär dig också hur du migrerar från Azure AD Authentication Library för .NET (ADAL.NET) till Microsoft Authentication Library för .NET (MSAL.NET).
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
ms.openlocfilehash: 25b8aa9b5e80720e9543dafce7970404a62b7d1f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377450"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Använda Microsoft Authenticator eller Intune-företagsportal på Xamarin-program

På Android och iOS kan-utjämnare som Microsoft Authenticator och de Android-/regionsspecifika Microsoft Intune Företagsportal aktivera:

- **Enkel inloggning (SSO)** : användarna behöver inte logga in på varje program.
- **Enhets identifiering**: koordinatorn har åtkomst till enhets certifikatet. Det här certifikatet skapas på enheten när det är anslutet till arbets platsen.
- **Verifiering av program identifiering**: när ett program anropar Service Broker skickas dess omdirigerings-URL. Service Broker verifierar URL: en.

Om du vill aktivera någon av dessa funktioner använder du parametern `WithBroker()` när du anropar `PublicClientApplicationBuilder.CreateApplication`-metoden. Parametern `.WithBroker()` anges till true som standard. 

Du kan också använda instruktionerna i följande avsnitt för att konfigurera Brokered Authentication för [iOS](#brokered-authentication-for-ios) -program eller [Android](#brokered-authentication-for-android) -program.

## <a name="brokered-authentication-for-ios"></a>Brokered Authentication för iOS

Använd följande steg för att aktivera din Xamarin. iOS-app för att kommunicera med [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) -appen.

### <a name="step-1-enable-broker-support"></a>Steg 1: Aktivera stöd för Broker
Du måste aktivera stöd för Broker för enskilda instanser av `PublicClientApplication`. Support är inaktiverat som standard. När du skapar `PublicClientApplication` via `PublicClientApplicationBuilder`använder du `WithBroker()`-parametern som i följande exempel visas. Parametern `WithBroker()` anges till true som standard.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Steg 2: aktivera nyckel rings åtkomst

Om du vill aktivera nyckel rings åtkomst måste du ha en åtkomst grupp för nyckel ringar för ditt program. Du kan använda `WithIosKeychainSecurityGroup()`-API: et för att ange åtkomst gruppen för nyckel ringar när du skapar ditt program:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Mer information finns i [aktivera nyckel rings åtkomst](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Steg 3: uppdatera AppDelegate för att hantera återanropet
När Microsoft Authentication Library för .NET (MSAL.NET) anropar Broker, anropar Broker tillbaka till ditt program via `OpenUrl`-metoden i `AppDelegate`-klassen. Eftersom MSAL väntar på svar från Service Broker måste ditt program samar beta för att anropa MSAL.NET tillbaka. Om du vill aktivera detta samarbete uppdaterar du `AppDelegate.cs`-filen så att den åsidosätter följande metod.

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

Den här metoden anropas varje gång programmet startas. Den används som en möjlighet att bearbeta svaret från Service Broker och slutföra autentiseringsprocessen som MSAL.NET startade.

### <a name="step-4-set-uiviewcontroller"></a>Steg 4: Ange UIViewController ()
Fortfarande i `AppDelegate.cs`-filen måste du ange ett objekt fönster. Normalt behöver du inte ange objekt fönstret för Xamarin iOS. Men du behöver ett objekt fönster för att skicka och ta emot svar från Service Broker. 

Så här konfigurerar du objekt fönstret: 
1. I `AppDelegate.cs`-filen anger `App.RootViewController` till en ny `UIViewController()`. Den här tilldelningen säkerställer att anropet till Service Broker innehåller `UIViewController`. Om den här inställningen tilldelas felaktigt kan du få följande fel meddelande:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Använd `.WithParentActivityOrWindow(App.RootViewController)` på `AcquireTokenInteractive`-anrop och skicka sedan i referensen till objekt fönstret som du kommer att använda.

    Följande gäller i `App.cs`:
    
    ```csharp
       public static object RootViewController { get; set; }
    ```
    
    Följande gäller i `AppDelegate.cs`:
    
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    
    I `AcquireToken`-anropet:
    
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Steg 5: registrera ett URL-schema
MSAL.NET använder URL: er för att anropa Service Broker och returnerar sedan Service Broker-svaret till din app. Om du vill slutföra den runda resan registrerar du ett URL-schema för din app i `Info.plist`-filen.

`CFBundleURLSchemes` namnet måste innehålla `msauth.` som prefix. Följ prefixet med `CFBundleURLName`. 

`BundleId` identifierar appen unikt i URL-schemat: `$"msauth.(BundleId)"`. Om `BundleId` är `com.yourcompany.xforms`är URL-schemat `msauth.com.yourcompany.xforms`.

> [!NOTE]
> Detta URL-schema blir en del av omdirigerings-URI: n som unikt identifierar din app när den tar emot svaret från Broker.

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
MSAL använder `–canOpenURL:` för att kontrol lera om Service Broker är installerat på enheten. I iOS 9 låser Apple de scheman som ett program kan fråga efter. 

Lägg till `msauthv2` i avsnittet `LSApplicationQueriesSchemes` i `Info.plist`-filen, som i följande exempel:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Steg 7: registrera din omdirigerings-URI i program portalen
När du använder Broker, har omdirigerings-URI: n ett extra krav. Omdirigerings-URI: n _måste_ ha följande format:
```csharp
$"msauth.{BundleId}://auth"
```

Här är ett exempel: 

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Observera att omdirigerings-URI: n matchar `CFBundleURLSchemes` namn som du inkluderade i `Info.plist`-filen.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Steg 8: kontrol lera att omdirigerings-URI: n är registrerad i appen

Omdirigerings-URI: n måste registreras på [app Registration-portalen](https://portal.azure.com) som en giltig omdirigerings-URI för programmet. 

Registrerings portalen för appar ger en ny upplevelse som hjälper dig att beräkna den asynkrona svars-URI: n från paket-ID: t. 

Så här beräknar du omdirigerings-URI: n:

1. Välj **autentisering** i appens registrerings Portal > **prova den nya upplevelsen**.

   ![Prova den nya appens registrerings upplevelse](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Välj **Lägg till en plattform**.

   ![Lägg till en plattform](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. När listan över plattformar stöds väljer du **iOS**.

   ![Konfigurera iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Ange ditt paket-ID enligt begäran och välj sedan **Konfigurera**.

   ![Ange paket-ID: t](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

När du har slutfört stegen beräknas omdirigerings-URI: n för dig.

![Kopiera omdirigerings-URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Brokered Authentication för Android

MSAL.NET stöder endast Xamarin. iOS-plattformen. Den har ännu inte stöd för utjämnare för Xamarin. Android-plattformen.

Det inbyggda Android-biblioteket i MSAL har redan stöd för Broker-autentisering. Mer information finns i avsnittet om [Broker-autentisering i Android](brokered-auth.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att tänka på när du använder universell Windows-plattform med MSAL.net](msal-net-uwp-considerations.md).
