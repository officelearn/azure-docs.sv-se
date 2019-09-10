---
title: Använda Microsoft Authenticator eller Microsoft Intune företags Portal på Xamarin iOS-och Android-program | Azure
description: Lär dig hur du migrerar Xamarin iOS-program kan använda Microsoft Authenticator från Azure AD Authentication Library för .NET (ADAL.NET) till Microsoft Authentication Library för .NET (MSAL.NET)
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
ms.openlocfilehash: e16ad801b3b691d942c2ba0dc723ba72e24cf4c0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875645"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Använda Microsoft Authenticator eller Microsoft Intune företags Portal på Xamarin-program

På Android och iOS, som utjämnare som Microsoft Authenticator eller Microsoft Intune företags portalen aktivera (endast Android):

- Enkel inloggning (SSO). Användarna behöver inte logga in på varje program.
- Enhets identifiering. Genom att få åtkomst till enhets certifikatet som skapades på enheten när den var ansluten till arbets platsen.
- Verifiering av program identifiering. När ett program anropar Broker, skickar den sin omdirigerings-URL och Service Broker verifierar den.

För att aktivera någon av dessa funktioner måste programutvecklare använda `WithBroker()` -parametern vid anrop till `PublicClientApplicationBuilder.CreateApplication` -metoden. `.WithBroker()`anges till sant som standard. Utvecklare måste också följa stegen nedan för [iOS](#brokered-authentication-for-ios) -eller [Android](#brokered-authentication-for-android) -program.

## <a name="brokered-authentication-for-ios"></a>Brokered Authentication för iOS

Följ stegen nedan för att aktivera din Xamarin. iOS-app för att kommunicera med [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) -appen.

### <a name="step-1-enable-broker-support"></a>Steg 1: Aktivera stöd för Broker
Stöd för Service Broker är aktiverat per PublicClientApplication. Den är inaktive rad som standard. `WithBroker()` Använd parametern (anges till sant som standard) när du skapar PublicClientApplication via PublicClientApplicationBuilder.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Steg 2: Uppdatera AppDelegate för att hantera återanropet
När MSAL.net anropar Service Broker, kommer Broker i sin tur att gå tillbaka till ditt program via `OpenUrl` -metoden `AppDelegate` i-klassen. Eftersom MSAL väntar på svar från Service Broker måste programmet samar beta för att anropa MSAL.NET tillbaka. Om du vill aktivera detta samarbete uppdaterar `AppDelegate.cs` du filen för att åsidosätta metoden nedan.

```CSharp
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

Den här metoden anropas varje gång programmet startas och används som en möjlighet att bearbeta svaret från Broker och slutföra autentiseringsprocessen som initierats av MSAL.NET.

### <a name="step-3-set-a-uiviewcontroller"></a>Steg 3: Ange en UIViewController ()
Fortfarande i `AppDelegate.cs`måste du ange ett objekt fönster. Normalt, med Xamarin iOS, behöver du inte ange objekt fönstret, men för att skicka och ta emot svar från Service Broker behöver du ett objekt fönster. 

För att göra detta måste du göra två saker. 
1) I `AppDelegate.cs`, `UIViewController()`anger du till en ny. `App.RootViewController` Den här tilldelningen ser till att det finns en UIViewController med anropet till Broker. Om den inte anges korrekt kan du få följande fel meddelande:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) På AcquireTokenInteractive-anropet använder `.WithParentActivityOrWindow(App.RootViewController)` du och skickar i referensen till objekt fönstret som du kommer att använda.

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

### <a name="step-4-register-a-url-scheme"></a>Steg 4: Registrera ett URL-schema
MSAL.NET använder URL: er för att anropa Service Broker och returnerar sedan Service Broker-svaret tillbaka till din app. Du måste registrera ett URL-schema för din app i `Info.plist` filen för att kunna slutföra den här fördröjningen.

Namnet måste innehålla `msauth.` ett prefix, följt av ditt `CFBundleURLName`. `CFBundleURLSchemes`

`$"msauth.(BundleId)"`

**Exempel:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Detta kommer att bli en del av RedirectUri som används för unik identifiering av appen när du tar emot svaret från Service Broker.

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

### <a name="step-5-lsapplicationqueriesschemes"></a>Steg 5: LSApplicationQueriesSchemes
MSAL använder `–canOpenURL:` för att kontrol lera om Broker är installerad på enheten. I iOS 9 är Apple låst för vilka scheman ett program kan fråga efter. 

**Lägg till** till avsnittet i`Info.plist` filen. `LSApplicationQueriesSchemes` **`msauthv2`**

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirecturi-in-the-application-portal"></a>Steg 6: Registrera din RedirectUri i program portalen
Genom att använda Service Broker lägger du till ett extra krav på din redirectUri. RedirectUri _**måste**_ ha följande format:
```CSharp
$"msauth.{BundleId}://auth"
```
**Till exempel:**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
**Observera att RedirectUri matchar det `CFBundleURLSchemes` namn som du inkluderade `Info.plist` i filen.**

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Steg 7: kontrol lera att omdirigerings-URI: n är registrerad i appen

Den här omdirigerings-URI: n måste registreras på appens registrerings portal (https://portal.azure.com) som en giltig omdirigerings-URI för programmet. 

Portalen har en ny Experience app Registration-portal som hjälper dig att beräkna den asynkrona svars-URI: n från paket-ID:

1. Välj **autentisering** i appens registrering och välj **testa den nya upplevelsen**.
   ![Prova den nya appens registrerings upplevelse](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Välj **Lägg till plattform**.
   ![Lägg till en plattform](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. När listan över plattformar stöds väljer du **iOS**.
   ![Konfigurera iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Ange ditt paket-ID enligt begäran och tryck sedan på **Registrera**.
   ![Ange paket-ID](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. Omdirigerings-URI: n beräknas för dig.
   ![Kopiera omdirigerings-URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Brokered Authentication för Android

Service Broker-stödet är inte tillgängligt för Android

## <a name="next-steps"></a>Nästa steg

[Universell Windows-plattform-/regionsspecifika överväganden med MSAL.NET](msal-net-uwp-considerations.md)