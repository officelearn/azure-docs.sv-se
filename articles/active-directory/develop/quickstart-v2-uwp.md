---
title: Microsoft identity-plattformen Windows UWP Snabbstart | Azure
description: Lär dig hur en Universal Windows Platform (XAML)-program kan få en åtkomsttoken och anropa ett API som skyddas av Microsoft identity-plattformen slutpunkt.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7ed2830b704d379e2ecc5a5e548f831800af56d
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526392"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Snabbstart: Anropa Microsoft Graph API från en UWP-app (universell Windows-plattform)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Den här snabbstarten innehåller ett kodexempel som visar hur en UWP-app (Universell Windows-plattform) kan logga in användare med ett personligt konto eller arbets- och skolkonto, hämta en åtkomsttoken samt anropa Microsoft Graph API.

![Visar hur exempelapp som genererats av den här snabbstarten fungerar](media/quickstart-v2-uwp/uwp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> [!div renderon="docs" class="sxs-lookup"]
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till den nya [Azure portal – appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs) fönstret.
> 1. Ange ett namn för programmet och klicka på **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen med hjälp av följande steg:
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. Gå till Microsoft identity-plattformen för utvecklare [appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan.
> 1. Välj **ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>      - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `UWP-App-calling-MsGraph`.
>      - I avsnittet **Kontotyper som stöds** väljer du **Konton alla organisationskataloger och personliga Microsoft-konton (till exempel Skype, Xbox och Outlook.com)**.
>      - Välj **Registrera** för att skapa programmet.
> 1. I listan över sidor för appen väljer du **Autentisering**.
> 1. I avsnittet **Omdirigerings-URL:er** letar du upp avsnittet **Föreslagna omdirigerings-URI:er för offentliga klienter (mobil, skrivbord)** och väljer **"urn:ietf:wg:oauth:2.0:oob**.
> 1. Välj **Spara**.

> [!div renderon="portal" class="sxs-lookup alert alert-info"]
> #### <a name="step-1-configure-your-application"></a>Steg 1: Konfigurera ditt program
> För kodexemplet för den här snabbstarten måste du lägga till en omdirigerings-URI som **urn:ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-uwp/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-your-visual-studio-project"></a>Steg 2: Ladda ned ditt Visual Studio-projekt

 - [Ladda ned Visual Studio 2017-projektet](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Steg 3: Konfigurera ditt Visual Studio-projekt

1. Extrahera zip-filen i en lokal mapp nära diskens rot, till exempel **C:\Azure-Samples**.
1. Öppna projektet i Visual Studio. Du kan uppmanas att installera en UWP-SDK. I så fall kan godkänna.
1. Redigera **MainPage.Xaml.cs** och Ersätt värdena för den `ClientId` fält:

    ```csharp
    private const string ClientId = "Enter_the_Application_Id_here";
    ```

> [!div renderon="docs"]
> Där:
> - `Enter_the_Application_Id_here` – är program-Id för programmet som du har registrerat.
>
> > [!TIP]
> > Att hitta värdena för *program-ID*går du till den **översikt** sidan

#### <a name="step-4-run-your-application"></a>Steg 4: Köra ditt program

Om du vill prova snabbstarten på din Windows-dator:

1. I Visual Studio-verktygsfältet väljer du den rätta plattformen (förmodligen **x64** eller **x86**, inte ARM).
   > Observera att målenheten ändras från *enhet* till *lokal dator*
1. Välj Felsök | **Starta utan felsökning**

## <a name="more-information"></a>Mer information

Det här avsnittet innehåller mer information om snabbstarten.

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) är i biblioteket som används för att logga in användare och begär säkerhetstoken. Säkerhetstoken används för att få åtkomst till ett API som skyddas av Microsoft Identity-plattformen för utvecklare. Du kan installera MSAL genom att köra följande kommando i *Package Manager-konsolen* i Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>MSAL-initiering

Du kan lägga till referensen för MSAL genom att lägga till följande kod:

```csharp
using Microsoft.Identity.Client;
```

Initiera sedan MSAL med hjälp av följande kod:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = new PublicClientApplicationBuilder.Create(ClientId)
                                                    .Build();
```

> |Där: ||
> |---------|---------|
> | `ClientId` | Är **Program-ID (klient)** för det program som registrerats på Azure-portalen. Du hittar det här värdet på appens **översiktssida** på Azure-portalen. |

### <a name="requesting-tokens"></a>Begära token

MSAL har två metoder som används för att hämta token interaktivt: `AcquireTokenInteractive` och `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Vissa situationer kräver framtvingar användare interagera med Microsoft identity-plattformen slutpunkten via ett popup-fönster för att antingen verifiera autentiseringsuppgifterna eller för att ge ditt medgivande. Några exempel är:

- De första gången en användarna logga in till programmet
- När användarna kan behöva ange sina autentiseringsuppgifter igen eftersom lösenordet har upphört att gälla
- När ditt program begär åtkomst till en resurs som användaren behöver för att godkänna
- När tvåfaktorsautentisering krävs

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

> |Där:||
> |---------|---------|
> | `scopes` | Innehåller de omfång som begärs, till exempel `{ "user.read" }` för Microsoft Graph eller `{ "api://<Application ID>/access_as_user" }` för anpassade webb-API:er. |

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Du vill inte kräva att användarna verifierar sina autentiseringsuppgifter varje gång de behöver komma åt en resurs. I de flesta fall vill du ha hämtning och förnyelse av token utan någon användarinteraktion. Du kan använda metoden `AcquireTokenSilent` för att hämta token för att komma åt skyddade resurser efter den inledande metoden `AcquireTokenAsync`:

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
