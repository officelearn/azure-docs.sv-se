---
title: Använda webbläsare (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Läs mer om specifika överväganden när du använder Xamarin Android med Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ed1f47ae99f6346a932d0fe94be7586dc25a672f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262742"
---
# <a name="using-web-browsers-msalnet"></a>Använda webbläsare (MSAL.NET)

Webbläsare krävs för interaktiv autentisering. Som standard stöder MSAL.NET [systemwebbläsaren](#system-web-browser-on-xamarinios-xamarinandroid) på Xamarin.iOS och Xamarin.Android. Men [du kan också aktivera den inbäddade webbläsaren](#enable-embedded-webviews-on-ios-and-android) beroende på dina krav (UX, behov av enkel inloggning (SSO), säkerhet) i [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) och [Xamarin.Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) apps. Och du kan även [välja dynamiskt](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) vilken webbläsare du ska använda baserat på förekomsten av Chrome eller en webbläsare som stöder Chrome anpassade flikar i Android. MSAL.NET stöder bara systemwebbläsaren i .NET Core-skrivbordsprogram.

## <a name="web-browsers-in-msalnet"></a>Webbläsare i MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Interaktion sker i en webbläsare

Det är viktigt att förstå att när du skaffar en token interaktivt, innehållet i dialogrutan inte tillhandahålls av biblioteket utan av STS (Security Token Service). Autentiseringsslutpunkten skickar tillbaka en del HTML och JavaScript som styr interaktionen, som återges i en webbläsare eller webbkontroll. Att låta STS hantera HTML-interaktionen har många fördelar:

- Lösenordet (om ett har skrivits) lagras aldrig av programmet eller autentiseringsbiblioteket.
- Aktiverar omdirigeringar till andra identitetsleverantörer (till exempel inloggning med ett arbetsskolekonto eller ett personligt konto hos MSAL, eller med ett socialt konto hos Azure AD B2C).
- Låter STS styra villkorlig åtkomst, till exempel genom att låta användaren göra MFA (Multiple Factor Authentication) under autentiseringsfasen (ange en Windows Hello-stift eller anropas på sin telefon eller på en autentiseringsapp på sin telefon). I de fall där den nödvändiga multifaktorautentiseringen inte har konfigurerats ännu kan användaren ställa in den precis i tid i samma dialogruta.  Användaren anger sitt mobiltelefonnummer och får hjälp att installera ett autentiseringsprogram och skanna en QR-tagg för att lägga till sitt konto. Denna server driven interaktion är en stor upplevelse!
- Låter användaren ändra sitt lösenord i samma dialogruta när lösenordet har gått ut (med ytterligare fält för det gamla lösenordet och det nya lösenordet).
- Aktiverar branding av klienten eller programmet (avbildningarna) som kontrolleras av Azure AD-klientadministratören/programägaren.
- Gör det möjligt för användarna att samtycka till att låta programmet komma åt resurser/scope i sitt namn strax efter autentiseringen.

### <a name="embedded-vs-system-web-ui"></a>Inbyggt mot systemwebbgränssnitt

MSAL.NET är ett multi-framework-bibliotek och har ram-specifik kod för att vara värd för en webbläsare i en gränssnittskontroll (till exempel på .Net Classic den använder WinForms, på Xamarin den använder inbyggda mobila kontroller etc.). Den här `embedded` kontrollen kallas webbgränssnitt. Alternativt kan MSAL.NET också sparka igång webbläsaren systemet OS.

I allmänhet rekommenderas att du använder plattformens standard, och detta är vanligtvis systemwebbläsaren. Systemet webbläsaren är bättre på att komma ihåg de användare som har loggat in tidigare. Om du behöver ändra detta beteende använder du`WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>I korthet

| Ramverk        | Inbäddade | System | Default |
| ------------- |-------------| -----| ----- |
| .NET Classic     | Ja | Ja^ | Inbäddade |
| .NET Core     | Inga | Ja^ | System |
| .NET Standard | Inga | Ja^ | System |
| UWP | Ja | Inga | Inbäddade |
| Xamarin.Android | Ja | Ja  | System |
| Xamarin.iOS | Ja | Ja  | System |
| Xamarin.Mac (på andra)| Ja | Inga | Inbäddade |

^ Kräverhttp://localhost" " omdirigera URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Systemwebbläsare på Xamarin.iOS, Xamarin.Android

Som standard stöder MSAL.NET systemwebbläsaren på Xamarin.iOS, Xamarin.Android och .NET Core. För alla plattformar som tillhandahåller användargränssnittet (det vill ha inte .NET Core) tillhandahålls en dialogruta i biblioteket som bäddar in en webbläsarkontroll. MSAL.NET använder också en inbäddad webbvy för .NET Desktop och WAB för UWP-plattformen. Det utnyttjar dock som standard **systemets webbläsare** för Xamarin iOS och Xamarin Android-program. På iOS väljer den till och med webbvyn att använda beroende på vilken version av operativsystemet (iOS12, iOS11 och tidigare).

Använda systemet webbläsaren har den betydande fördelen av att dela SSO staten med andra program och med webbapplikationer utan att behöva en mäklare (Företagsportal / Authenticator). Systemet webbläsaren användes, som standard, i MSAL.NET för Xamarin iOS och Xamarin Android plattformar eftersom, på dessa plattformar, systemet webbläsaren upptar hela skärmen, och användarupplevelsen är bättre. Systemwebbvyn kan inte skiljas från en dialogruta. På iOS kan dock användaren behöva ge sitt samtycke till att webbläsaren ringer upp programmet, vilket kan vara irriterande.

## <a name="system-browser-experience-on-net-core"></a>Systembläddrare på .NET Core

På .NET Core startar MSAL.NET systembläddraren som en separat process. MSAL.NET inte har kontroll över den här webbläsaren, men när användaren är klar med autentiseringen omdirigeras webbsidan på ett sådant sätt att MSAL.NET kan avlyssna Uri.

Du kan också konfigurera appar som skrivits för .NET Classic för att använda den här webbläsaren genom att ange

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET kan inte upptäcka om användaren navigerar bort eller helt enkelt stänger webbläsaren. Appar som använder den här tekniken uppmuntras att definiera en timeout (via `CancellationToken`). Vi rekommenderar en timeout på minst några minuter för att ta hänsyn till fall där användaren uppmanas att ändra lösenord eller utföra multifaktorautentisering.

### <a name="how-to-use-the-default-os-browser"></a>Så här använder du standardwebbläsaren

MSAL.NET måste lyssna på `http://localhost:port` och avlyssna koden som AAD skickar när användaren är klar med autentiseringen (Se [Auktoriseringskod](v2-oauth2-auth-code-flow.md) för mer information)

Så här aktiverar du systembläddraren:

1. Under appregistrering `http://localhost` konfigurerar du som en omdirigeringsuri (stöds för närvarande inte av B2C)
2. När du skapar din PublicClientApplication anger du den här omdirigeringsuri:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Om du `http://localhost`konfigurerar kommer internt MSAL.NET att hitta en slumpmässig öppen port och använda den.

### <a name="linux-and-mac"></a>Linux och MAC

På Linux öppnar MSAL.NET standardwebbläsaren med xdg-open-verktyget. Om du vill felsöka kör du verktyget från en terminal till exempel`xdg-open "https://www.bing.com"`  
På Mac öppnas webbläsaren genom att anropa`open <url>`

### <a name="customizing-the-experience"></a>Anpassa upplevelsen

> [!NOTE]
> Anpassning är tillgänglig i MSAL.NET 4.1.0 eller senare.

MSAL.NET kan svara med ett HTTP-meddelande när en token tas emot eller vid fel. Du kan visa ett HTML-meddelande eller omdirigera till valfri url:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Öppna en viss webbläsare (Experimentell)

Du kan anpassa hur MSAL.NET öppnar webbläsaren. Till exempel istället för att använda vilken webbläsare som är standard, kan du tvinga öppna en viss webbläsare:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP använder inte System Webview

För skrivbordsprogram leder dock lanseringen av en System Webview till en underparanvändarupplevelse, eftersom användaren ser webbläsaren, där de kanske redan har andra flikar öppna. Och när autentisering har hänt, får användarna en sida som ber dem att stänga det här fönstret. Om användaren inte är uppmärksam kan de stänga hela processen (inklusive andra flikar som inte har något samband med autentiseringen). Utnyttja systemets webbläsare på skrivbordet skulle också kräva att öppna lokala portar och lyssna på dem, vilket kan kräva avancerade behörigheter för programmet. Du som utvecklare, användare eller administratör kan vara tveksam till det här kravet.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Aktivera inbäddade webbvisningar på iOS och Android

Du kan också aktivera inbäddade webbvisningar i Xamarin.iOS- och Xamarin.Android-appar. Från och med MSAL.NET 2.0.0-förhandsversionen stöder MSAL.NET också att använda det **inbäddade** webview-alternativet. För ADAL.NET är inbäddad webview det enda alternativet som stöds.

Som utvecklare som använder MSAL.NET inriktning Xamarin, kan du välja att använda antingen inbäddade webbvisningar eller webbläsare system. Detta är ditt val beroende på användarupplevelsen och säkerhetsfrågor du vill rikta.

För närvarande stöder MSAL.NET ännu inte Android- och iOS-mäklare. Därför om du behöver ge enkel inloggning (SSO), systemet webbläsaren kan fortfarande vara ett bättre alternativ. Stödja mäklare med den inbäddade webbläsaren är på MSAL.NET eftersläpning.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Skillnader mellan inbyggd webbvy och systemwebbläsare
Det finns vissa visuella skillnader mellan inbäddade webview och systemwebbläsare i MSAL.NET.

**Interaktiv inloggning med MSAL.NET med hjälp av den inbäddade webbvyn:**

![inbäddad](media/msal-net-web-browsers/embedded-webview.png)

**Interaktiv inloggning med MSAL.NET med hjälp av systembläddraren:**

![System webbläsare](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Alternativ för utvecklare

Som utvecklare som använder MSAL.NET har du flera alternativ för att visa den interaktiva dialogrutan från STS:

- **Systembläddraren.** Systembläddraren anges som standard i biblioteket. Om du använder Android läser du [systemwebbläsare](msal-net-system-browser-android-considerations.md) för specifik information om vilka webbläsare som stöds för autentisering. När du använder systembläddraren i Android rekommenderar vi att enheten har en webbläsare som stöder anpassade flikar i Chrome.  Annars kan autentisering misslyckas.
- **Inbäddad webbvy.** Om du bara vill använda inbäddad webbvy i MSAL.NET innehåller parameterbyggaren `AcquireTokenInteractively` en `WithUseEmbeddedWebView()` metod.

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Välja mellan inbäddad webbläsare eller systemwebbläsare på Xamarin.iOS

I din iOS-app kan du i `AppDelegate.cs` du initiera `ParentWindow` till `null`. Det används inte i iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Välja mellan inbäddad webbläsare eller systemwebbläsare på Xamarin.Android

I din Android-app kan du i `MainActivity.cs` du ställa in den överordnade aktiviteten så att autentiseringsresultatet återgår till den:

```csharp
 App.ParentWindow = this;
```

Sedan `MainPage.xaml.cs`i:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Upptäcka förekomsten av anpassade flikar på Xamarin.Android

Om du vill använda systemets webbläsare för att aktivera SSO med de appar som körs i webbläsaren, men är orolig för användarupplevelsen för `IsSystemWebViewAvailable()` Android-enheter som inte har en webbläsare med anpassat flikstöd, har du möjlighet att bestämma genom att anropa metoden i `IPublicClientApplication`. Den här `true` metoden returnerar om PackageManager `false` identifierar anpassade flikar och om de inte identifieras på enheten.

Baserat på det värde som returneras med den här metoden och dina krav kan du fatta ett beslut:

- Du kan returnera ett anpassat felmeddelande till användaren. Till exempel: "Installera Chrome för att fortsätta med autentisering" -OR-
- Du kan återgå till det inbäddade webview-alternativet och starta användargränssnittet som en inbäddad webbvy.

Koden nedan visar det inbäddade webview-alternativet:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core stöder inte interaktiv autentisering med en inbäddad webbläsare

För .NET Core är förvärv av token interaktivt endast tillgängligt via systemets webbläsare, inte med inbäddade webbvyer. Faktum är att .NET Core inte tillhandahåller användargränssnitt ännu.
Om du vill anpassa webbupplevelsen med systemets webbläsare kan du implementera [IWithCustomUI-gränssnittet](scenario-desktop-acquire-token.md#withcustomwebui) och även tillhandahålla din egen webbläsare.
