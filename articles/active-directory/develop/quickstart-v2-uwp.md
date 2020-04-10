---
title: Microsoft identity-plattformen Windows UWP snabbstart | Azure
description: Lär dig hur ett XAML-program (Universal Windows Platform) kan hämta en åtkomsttoken och anropa ett API som skyddas av slutpunkten för Microsoft identity platform.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: feb1fa82653a0db7b3041a4f745d0563c220bd31
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991100"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Snabbstart: Anropa Microsoft Graph API från en UWP-app (Universell Windows-plattform)

Den här snabbstarten innehåller ett kodexempel som visar hur ett UWP-program (Universal Windows Platform) kan logga in användare med personliga konton eller arbets- och skolkonton, få en åtkomsttoken och anropa Microsoft Graph API. (Se [Hur exemplet fungerar](#how-the-sample-works) för en illustration.)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> [!div renderon="docs" class="sxs-lookup"]
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till den nya [Azure-portalen - fönstret Appregistreringar.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs)
> 1. Ange ett namn för programmet och klicka på **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen med hjälp av följande steg:
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. Navigera till sidan Microsoft identity platform för utvecklare [Appregistreringar.](https://aka.ms/MobileAppReg)
> 1. Välj **Ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>      - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `UWP-App-calling-MsGraph`.
>      - I avsnittet **Kontotyper som stöds** väljer du **Konton alla organisationskataloger och personliga Microsoft-konton (till exempel Skype, Xbox och Outlook.com)**.
>      - Välj **Registrera** för att skapa programmet.
> 1. I listan över sidor för appen väljer du **Autentisering**.
> 1. Kontrollera i avsnittet **Omdirigera URI:er** | **föreslagna omdirigerings-URI:er för offentliga klienter (mobil, stationär dator)** **https://login.microsoftonline.com/common/oauth2/nativeclient**.
> 1. Välj **Spara**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Steg 1: Konfigurera programmet
> För att kodexemplet för snabbstart ska fungera måste **https://login.microsoftonline.com/common/oauth2/nativeclient**du lägga till en omdirigerings-URI som .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-uwp/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-your-visual-studio-project"></a>Steg 2: Ladda ned ditt Visual Studio-projekt

> [!div renderon="docs"]
> [Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Kör projektet med Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Ladda ner kodexemplet](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Steg 3: Appen är konfigurerad och redo att köras
> Vi har konfigurerat ditt projekt med värden för appens egenskaper och det är redo att köras. 

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Steg 3: Konfigurera ditt Visual Studio-projekt
> 
> 1. Extrahera zip-filen i en lokal mapp nära diskens rot, till exempel **C:\Azure-Samples**.
> 1. Öppna projektet i Visual Studio. Du kan bli ombedd att installera en UWP SDK. I så fall, acceptera.
> 1. Redigera **MainPage.Xaml.cs** och ersätt fältets `ClientId` värden:
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
> Där:
> - `Enter_the_Application_Id_here` – är program-Id för programmet som du har registrerat.
>
> > [!TIP]
> > Om du vill hitta värdet för *program-ID*går du till avsnittet **Översikt** i portalen

#### <a name="step-4-run-your-application"></a>Steg 4: Kör ditt program

Om du vill prova snabbstarten på din Windows-dator:

1. I verktygsfältet Visual Studio väljer du rätt plattform (förmodligen **x64** eller **x86**, inte ARM). Du kommer att observera att målenheten ändras från *enhet* till *lokal dator*
1. Välj Felsökning | **Starta utan felsökning**

## <a name="more-information"></a>Mer information

Det här avsnittet innehåller mer information om snabbstarten.

### <a name="how-the-sample-works"></a>Så här fungerar exemplet
![Visar hur exempelappen som genereras av den här snabbstarten fungerar](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) är det bibliotek som används för att logga in användare och begära säkerhetstoken. Säkerhetstoken används för att komma åt ett API som skyddas av Microsoft Identity-plattformen för utvecklare. Du kan installera MSAL genom att köra följande kommando i *Package Manager-konsolen* i Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL-initiering

Du kan lägga till referensen för MSAL genom att lägga till följande kod:

```csharp
using Microsoft.Identity.Client;
```

Sedan initieras MSAL med hjälp av följande kod:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

> |Där: ||
> |---------|---------|
> | `ClientId` | Är **Program-ID (klient)** för det program som registrerats på Azure-portalen. Du hittar det här värdet på appens **översiktssida** på Azure-portalen. |

### <a name="requesting-tokens"></a>Begära token

MSAL har två metoder för att hämta token `AcquireTokenInteractive` `AcquireTokenSilent`i en UWP-app: och .

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Vissa situationer kräver att användarna tvingas interagera med slutpunkten för Microsoft-identitetsplattformen via ett popup-fönster för att antingen validera sina autentiseringsuppgifter eller ge sitt samtycke. Några exempel är:

- Förstagångsanvändare loggar in på programmet
- När användarna kan behöva ange sina autentiseringsuppgifter igen eftersom lösenordet har upphört att gälla
- När ditt program begär åtkomst till en resurs måste användaren samtycka till
- När tvåfaktorsautentisering krävs

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

> |Där:||
> |---------|---------|
> | `scopes` | Innehåller de omfång som begärs, till exempel `{ "user.read" }` för Microsoft Graph eller `{ "api://<Application ID>/access_as_user" }` för anpassade webb-API:er. |

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Använd `AcquireTokenSilent` metoden för att hämta token för att `AcquireTokenInteractive` komma åt skyddade resurser efter den första metoden. Du vill inte kräva att användaren validerar sina autentiseringsuppgifter varje gång de behöver komma åt en resurs. För det mesta vill du ha tokenförvärv och förnyelse utan någon användarinteraktion

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Där: ||
> |---------|---------|
> | `scopes` | Innehåller de omfång som begärs, till exempel `{ "user.read" }` för Microsoft Graph eller `{ "api://<Application ID>/access_as_user" }` för anpassade webb-API:er |
> | `firstAccount` | Anger det första användarkontot i cacheminnet (MSAL stöder flera användare i en enda app) |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Prova att använda självstudien om Windows-skrivbordet för en komplett stegvis guide till att skapa appar och nya funktioner, däribland en fullständig förklaring av den här snabbstarten.

> [!div class="nextstepaction"]
> [UWP – Självstudie om att anropa Graph API](tutorial-v2-windows-uwp.md)
