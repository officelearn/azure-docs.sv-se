---
title: 'Mobilapp som anropar webb-API: er (kod konfiguration) – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du skapar en mobilapp som anropar webb-API: er (appens kod konfiguration)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bdf9210eb88b2057cf861b208f19d3e6f562e9a
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414855"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>Mobilapp som anropar webb-API: er – kod konfiguration

När du har skapat ditt program får du lära dig hur du konfigurerar koden från programmets parametrar som du fick när du registrerade appen. Mobila program har också vissa komplexa specifika lösningar, som måste göra med montering i ramverket som används för att skapa dessa appar

## <a name="msal-libraries-supporting-mobile-apps"></a>MSAL-bibliotek med stöd för mobila appar

Microsoft-biblioteken som stöder Mobile Apps är:

  MSAL-bibliotek | Beskrivning
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | För att utveckla bärbara program. MSAL.NET-plattformar som stöds för att bygga ett mobilt program är UWP, Xamarin. iOS och Xamarin. Android.
  ![MSAL. iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL. iOS | Utveckla inbyggda iOS-program med mål C eller Swift
  ![MSAL. Android](media/sample-v2-code/logo_android.png) <br/> MSAL. Android | Utveckla inbyggda Android-program i Java för Android

## <a name="configuring-the-application"></a>Konfigurera programmet

Mobil program använder MSAL `PublicClientApplication` -klassen. Så här skapar du en instans av det:

### <a name="android"></a>Android

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

```swift
// Initialize the app.
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
}
```

### <a name="xamarin-or-uwp"></a>Xamarin eller UWP

I följande stycke förklaras hur du konfigurerar koden för programmet för Xamarin. iOS-, Xamarin. Android-och UWP-appar. Det första steget är att instansiera programmet. Ett valfritt steg är att konfigurera Broker.

#### <a name="instantiating-the-application"></a>Instansiera programmet

I Xamarin eller UWP är det enklaste sättet att instansiera programmet enligt följande, där `ClientId` är GUID för ditt program som registrerat.

```CSharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Det finns ytterligare med*parameter* metoder som anger det överordnade användar gränssnittet, Åsidosätt standard utfärdaren, ange ett klient namn och-version (för telemetri), ange en omdirigerings-URI, ange den http-fabrik som ska användas (för att till exempel hantera proxyservrar anger du telemetri och loggning). Detta är ämnet i följande stycken.

##### <a name="specifying-the-parent-uiwindowactivity"></a>Ange ett överordnat gränssnitt/fönster/aktivitet

På Android måste du skicka den överordnade aktiviteten innan du utför den interaktiva autentiseringen. När du använder en Service Broker i iOS måste du skicka in ViewController. På samma sätt på UWP kanske du vill skicka det överordnade fönstret. Detta är möjligt när du hämtar token, men det är också möjligt att ange ett återanrop när appen skapas, ett ombud som returnerar UIParent.

```CSharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

På Android rekommenderar vi att du gör `CurrentActivityPlugin` det [här](https://github.com/jamesmontemagno/CurrentActivityPlugin).  Sedan skulle `PublicClientApplication` din Builder-kod se ut så här:

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>Fler parametrar för att skapa appar

- Listan över alla modifierare som är tillgängliga på `PublicClientApplicationBuilder`finns i referens dokumentationen [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Beskrivning av alla alternativ som visas i `PublicClientApplicationOptions` se [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)i referens dokumentationen

#### <a name="xamarin-ios-specific-considerations"></a>Xamarin iOS-/regionsspecifika överväganden

På Xamarin iOS finns det flera saker som du måste ta hänsyn till när du använder MSAL.NET:

1. [Åsidosätt och implementera `OpenUrl` funktionen i`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [Aktivera nyckel rings grupper](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [Aktivera delning av token cache](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [Aktivera nyckel rings åtkomst](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Information finns i [Xamarin iOS](msal-net-xamarin-ios-considerations.md) -överväganden

#### <a name="other-xamarin-android-specific-considerations"></a>Andra Xamarin Android-/regionsspecifika faktorer

Här är Xamarin Android-information:

- [Se till att kontrollen går tillbaka till MSAL när den interaktiva delen av autentiserings flödet slutar](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Uppdatera Android-manifestet](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Använda den inbäddade webb visningen (valfritt)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Felsökning](msal-net-xamarin-android-considerations.md#troubleshooting)

Mer information finns i [Xamarin Android](msal-net-xamarin-android-considerations.md) -överväganden

Slutligen finns det vissa speciella kunskaper om webbläsare på Android. De beskrivs i [Xamarin Android-/regionsspecifika överväganden med MSAL.net](msal-net-system-browser-android-considerations.md)

#### <a name="uwp-specific-considerations"></a>UWP-information

På UWP kan du använda företags nätverk. Information om UWP-information finns i [universell Windows-plattform-Specific överväganden med MSAL.net](msal-net-uwp-considerations.md).

## <a name="configuring-the-application-to-use-the-broker"></a>Konfigurera programmet så att det använder Service Broker

### <a name="why-use-brokers-on-xamarinios-and-xamarinandroid-applications"></a>Varför ska du använda utjämnare i Xamarin. iOS och Xamarin. Android-program?

I Android och iOS aktiverar mäklaren:

- Enkel inloggning (SSO). Användarna behöver inte logga in på varje program.
- Enhets identifiering. Aktiverar Azure AD Device-relaterade principer för villkorlig åtkomst genom att komma åt enhets certifikatet som skapades på enheten när den var ansluten till arbets platsen.
- Verifiering av program identifiering. När ett program anropar Broker, skickar den sin omdirigerings-URL och Service Broker verifierar den.

### <a name="enable-the-brokers-on-xamarin"></a>Aktivera utjämnare på Xamarin

Om du vill aktivera någon av dessa funktioner använder `WithBroker()` du parametern när du `PublicClientApplicationBuilder.CreateApplication` anropar-metoden. `.WithBroker()`anges till sant som standard. Följ stegen nedan för [iOS](#brokered-authentication-for-xamarinios).

### <a name="brokered-authentication-for-xamarinios"></a>Brokered Authentication för Xamarin. iOS

Följ stegen nedan för att aktivera din Xamarin. iOS-app för att kommunicera med [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) -appen.

#### <a name="step-1-enable-broker-support"></a>Steg 1: Aktivera stöd för Broker

Stöd för Broker aktive ras per`PublicClientApplication` beräkning. Den är inaktive rad som standard. Du måste använda `WithBroker()` parametern (anges till sant som standard) när du `PublicClientApplication` skapar genom `PublicClientApplicationBuilder`.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Steg 2: Uppdatera AppDelegate för att hantera återanropet

När MSAL.net anropar Service Broker, kommer Broker i sin tur att gå tillbaka till ditt program via `AppDelegate.OpenUrl` -metoden. Eftersom MSAL väntar på svar från Service Broker måste programmet samar beta för att anropa MSAL.NET tillbaka. Du gör detta genom att uppdatera `AppDelegate.cs` filen för att åsidosätta metoden nedan.

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

#### <a name="step-3-set-a-uiviewcontroller"></a>Steg 3: Ange en UIViewController ()

Med Xamarin iOS behöver du vanligt vis inte ange ett objekt fönster, men i det här fallet ska du skicka och ta emot svar från en Broker. Ange ett `AppDelegate.cs`ViewController fortfarande i.

Gör följande för att ange objekt fönstret:

1) I `AppDelegate.cs`, `UIViewController()`anger du till en ny. `App.RootViewController` Detta ser till att det finns en `UIViewController` med anropet till Broker. Om den inte anges korrekt kan du få följande fel meddelande:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
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

#### <a name="step-4-register-a-url-scheme"></a>Steg 4: Registrera ett URL-schema

MSAL.NET använder URL: er för att anropa Service Broker och returnerar sedan Service Broker-svaret tillbaka till din app. Du måste registrera ett URL-schema för din app i `Info.plist` filen för att kunna slutföra den här fördröjningen.

Prefixet `msauth`med. `CFBundleURLSchemes` Lägg `CFBundleURLName` sedan till i slutet.

`$"msauth.(BundleId)"`

**Exempel:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Detta URL-schema kommer att bli en del av RedirectUri som används för unik identifiering av appen när du tar emot svaret från Service Broker.

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

#### <a name="step-5-lsapplicationqueriesschemes"></a>Steg 5: LSApplicationQueriesSchemes

MSAL använder `–canOpenURL:` för att kontrol lera om Broker är installerad på enheten. I iOS 9 är Apple låst för vilka scheman ett program kan fråga efter.

**Lägg till** till avsnittet i`Info.plist` filen. `LSApplicationQueriesSchemes` **`msauthv2`**

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Brokered Authentication för Xamarin. Android

MSAL.NET har ännu inte stöd för hanterare för Android.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämtar en token](scenario-mobile-acquire-token.md)
