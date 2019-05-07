---
title: Webbläsare i Microsoft Authentication Library för .NET | Azure
description: Läs mer om specifika överväganden när du använder Xamarin Android med Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6e13ec3d822ba8a8cd2484f42ea81e615bae268
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190991"
---
# <a name="using-web-browsers-in-msalnet"></a>Med webbläsare i MSAL.NET
Webbläsare krävs för interaktiv autentisering. Som standard MSAL.NET stöder den [system webbläsare](#system-web-browser-on-xamarinios-and-xamarinandroid) på Xamarin.iOS och [Xamarin.Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/system-browser). Men [du kan också aktivera Embedded webbläsarens](#enable-embedded-webviews) beroende på dina behov (UX behovet av enkel inloggning (SSO), säkerhet) i [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) och [Xamarin.Android](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) appar. Och du kan även [väljer dynamiskt](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) vilken webbläsare du använder baserat på förekomsten av Chrome eller en webbläsare som stöder Chrome anpassade flikar i Android.

## <a name="web-browsers-in-msalnet"></a>Webbläsare i MSAL.NET

Det är viktigt att förstå att när du hämtar en token interaktivt innehållet i dialogrutan inte tillhandahålls av biblioteket utan av STS (Security Token Service). Autentiseringsslutpunkten skickar tillbaka vissa HTML och JavaScript som styr interaktion som återges i en webbläsare eller webbkontrollen för. Att tillåta STS att hantera interaktionen HTML har många fördelar:

- Lösenordet (om någon har skrivits) lagras aldrig av programmet, och inte heller autentiseringsbiblioteket.
- Aktiverar omdirigeringar till andra identitetsleverantörer (till exempel inloggning in med ett arbetskonto för skolan eller ett personligt konto MSAL eller med ett socialt konto med Azure AD B2C).
- Kan Säkerhetstokentjänsten styra villkorlig åtkomst, till exempel genom att låta användare gör flera factor authentication (MFA) vid autentisering (att ange en Windows Hello PIN-kod eller som anropas på sin telefon eller på en authentication-appen på sin telefon). I fall där krävs Multi-Factor authentication har konfigurerats det ännu kan kan användaren konfigurera den just-in-time i samma dialogrutan.  Användaren anger sitt mobiltelefonnummer och leds för att installera ett program för autentisering och skanna en QR-tagg för att lägga till sitt konto. Den här servern driven interaktion är en bra upplevelse!
- Användaren kan ändra sina lösenord i den här dialogrutan för samma när lösenordet har upphört att gälla (som tillhandahåller ytterligare fält för det gamla lösenordet och det nya lösenordet).
- Möjliggör anpassning av klienten, eller ett program (avbildningar) kontrolleras av Azure AD-administratör / ägare för programmet.
- Gör att användarna samtycker till att låta programmet åtkomst till resurser / scope i sina namn efter autentiseringen.

## <a name="system-web-browser-on-xamarinios-and-xamarinandroid"></a>System-webbläsaren på Xamarin.iOS och Xamarin.Android

Som standard stöder MSAL.NET system webbläsarens på Xamarin.iOS och Xamarin.Android. För alla plattformar som tillhandahåller Användargränssnittet (det vill säga inte .NET Core), kommer en dialogruta från biblioteket bädda in en webbläsarkontroll. MSAL.NET använder även en inbäddad webbvy för .NET Desktop- och Windows Adressbok för UWP-plattformen. Men den använder som standard den **system webbläsare** för Xamarin iOS- och Xamarin Android-program. På iOS-, väljer den även webbvyn att använda beroende på vilken version av operativsystemet (iOS12, iOS11, och tidigare).

Webbläsaren system har stor fördel med att dela SSO-tillstånd med andra program och webbprogram utan att behöva en koordinator (Företagsportalen / Authenticator). System webbläsaren användes, som standard i MSAL.NET för Xamarin iOS- och Xamarin Android-plattformar eftersom, på följande plattformar system webbläsarens tar upp hela skärmen och användarupplevelsen är bättre. Webbvy system är inte kan särskiljas från en dialogruta. På iOS-, men kanske användaren ge ditt medgivande för webbläsaren om du vill anropa programmet, vilket kan vara störande.

### <a name="uwp-does-not-use-the-system-webview"></a>UWP använder inte systemets webbvy

För program, men leder startar en-systemets webbvy till en subpar användarupplevelse som användaren ser webbläsaren, där de kanske redan har andra flikar som öppnas. Och när autentisering har skett användarna får en sida som ber dem att Stäng det här fönstret. Om du inte betalar åtgärdas, kan de stänga hela processen (inklusive andra flikar som är inte relaterat till autentisering). Att utnyttja system webbläsaren på skrivbordet även kräver att öppna lokala portar och lyssna på dem, vilket kan kräva avancerade behörigheter för programmet. Du som utvecklare, användaren eller administratören, kan vara ovilliga om det här kravet.

## <a name="enable-embedded-webviews"></a>Aktivera sig av inbäddade Webbvyer 
Du kan också aktivera sig av inbäddade Webbvyer i Xamarin.iOS och Xamarin.Android-appar. Från och med MSAL.NET 2.0.0-preview, MSAL.NET har också stöd för den **embedded** webview-alternativet. För ADAL.NET är inbäddade webview det enda alternativet som stöds.

Som utvecklare med hjälp av MSAL.NET som riktar in sig på Xamarin, kan du använda sig av inbäddade Webbvyer eller system webbläsare. Det här är valfritt beroende på användarnas upplevelse och säkerhet behov som du vill använda.

För närvarande stöder MSAL.NET ännu inte mäklare för Android och iOS. Om du behöver att tillhandahålla enkel inloggning (SSO), kan system-webbläsaren fortfarande därför ett bättre alternativ. Stöd för asynkrona Meddelandeköer med inbäddade webbläsaren finns på MSAL.NET eftersläpningen.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Skillnader mellan embedded webbvy och system webbläsare 
Det finns några visual skillnader mellan embedded webbvy och system webbläsare i MSAL.NET.

**Interaktiv inloggning med MSAL.NET med inbäddade webbvyn:**

![Inbäddad](media/msal-net-web-browsers/embedded-webview.png)

**Interaktiv inloggning med MSAL.NET webbläsaren System:**

![System-webbläsare](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Utvecklaralternativ

Som utvecklare med hjälp av MSAL.NET har du flera alternativ för att visa dialogrutan interaktiva från STS:

- **System-webbläsaren.** System-webbläsaren är som standard i biblioteket. Om du använder Android läsa [system webbläsare](msal-net-system-browser-android-considerations.md) för specifik information om vilka webbläsare som stöds för autentisering. När webbläsaren system i Android, rekommenderar vi att enheten har en webbläsare som stöder Chrome anpassade flikar.  I annat fall misslyckas autentiseringen.
- **Inbäddade webbvy.** Du använder endast inbäddade webview i MSAL.NET, den `AcquireTokenInteractively` parametrar builder innehåller en `WithUseEmbeddedWebView()` metod.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Välja mellan embedded webbläsare eller system webbläsaren på Xamarin.iOS

I din iOS-app i `AppDelegate.cs` kan du kan initiera den `ParentWindow` till `null`. Den används inte i iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Välja mellan embedded webbläsare eller system webbläsaren på Xamarin.Android

I din Android-app i `MainActivity.cs` du kan ange den överordnade aktiviteten så att resultat för autentisering hämtar tillbaka till den:

```csharp
 App.ParentWindow = this;
```

I den `MainPage.xaml.cs`:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Identifiera förekomsten av anpassade flikar på Xamarin.Android

Om du vill använda webbläsaren system för att aktivera enkel inloggning med appar som körs i webbläsaren, men de är oroliga om användarupplevelsen för Android-enheter inte har en webbläsare med stöd för anpassad flik, har möjlighet att bestämma genom att anropa den `IsSystemWebViewAvailable()` -metod i < c 2 > `IPublicClientApplication` . Den här metoden returnerar `true` om PackageManager identifierar anpassade flikar och `false` om de inte identifieras på enheten.

Baserat på värdet som returneras av den här metoden och dina krav kan fatta du ett beslut:

- Du kan returnera ett anpassat felmeddelande för användaren. Exempel: ”Installera Chrome för att fortsätta med autentisering” - OR-
- Du kan återgår till att alternativet embedded webbvy och starta Användargränssnittet för som en inbäddad webbvy.

Koden nedan visar embedded webview-alternativet:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

## <a name="net-core-does-not-support-interactive-authentication-out-of-the-box"></a>.NET core stöder inte interaktiv autentisering direkt

För .NET Core är inköp av token interaktivt inte tillgänglig. Verkligen, .NET Core ger inte Användargränssnittet ännu. Om du vill ange interaktiv inloggning för ett program med .NET Core kan du låta programmet presentera för användaren en kod och en URL att gå för att logga in interaktivt (se [enheten kod Flow](msal-authentication-flows.md#device-code)).

Du kan också implementera den [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) gränssnitt och ange din egen webbläsare