---
title: Microsoft Identity Platform Windows-UWP snabb start | Azure
description: Lär dig hur ett Universell Windows-plattform (XAML)-program kan få en åtkomsttoken och anropa ett API som skyddas av Microsoft Identity Platform-slutpunkten.
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b89bd3992bd6249e0acd576fee7e1556968ad65
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334176"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Snabbstart: Anropa Microsoft Graph API från en UWP-app (universell Windows-plattform)

Den här snabb starten innehåller ett kod exempel som visar hur ett Universell Windows-plattform-program (UWP) kan logga in användare med personliga konton eller arbets-och skol konton, hämta en åtkomsttoken och anropa Microsoft Graph API.

![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-uwp/uwp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> [!div renderon="docs" class="sxs-lookup"]
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till fönstret ny [Azure Portal-Appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs) .
> 1. Ange ett namn för programmet och klicka på **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen med hjälp av följande steg:
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://aka.ms/MobileAppReg) .
> 1. Välj **ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>      - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `UWP-App-calling-MsGraph`.
>      - I avsnittet **Kontotyper som stöds** väljer du **Konton alla organisationskataloger och personliga Microsoft-konton (till exempel Skype, Xbox och Outlook.com)** .
>      - Välj **Registrera** för att skapa programmet.
> 1. I listan över sidor för appen väljer du **Autentisering**.
> 1. Expandera avsnittet **Desktop +** Devices.  (Om **Station ära + enheter** inte visas klickar du först på översta banderollen för att Visa förhands gransknings upplevelsen)
> 1. Under avsnittet omdirigerings- **URI** väljer du **Lägg till URI**.  Typ **urn: IETF: WG: OAuth: 2.0: OOB**.
> 1. Välj **Spara**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Steg 1: Konfigurera ditt program
> För kodexemplet för den här snabbstarten måste du lägga till en omdirigerings-URI som **urn:ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-uwp/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-your-visual-studio-project"></a>Steg 2: Ladda ned ditt Visual Studio-projekt

 - [Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Steg 3: Konfigurera ditt Visual Studio-projekt

1. Extrahera zip-filen i en lokal mapp nära diskens rot, till exempel **C:\Azure-Samples**.
1. Öppna projektet i Visual Studio. Du kan uppmanas att installera en UWP SDK. I så fall accepterar du.
1. Redigera **mainpage.XAML.cs** och ersätt värdena för `ClientId` fältet:

    ```csharp
    private const string ClientId = "Enter_the_Application_Id_here";
    ```
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Den här snabb starten stöder Enter_the_Supported_Account_Info_Here.    

> [!div renderon="docs"]
> Där:
> - `Enter_the_Application_Id_here` – är program-Id för programmet som du har registrerat.
>
> > [!TIP]
> > Du hittar värdet för *program-ID*genom att gå till **översikts** avsnittet i portalen

#### <a name="step-4-run-your-application"></a>Steg 4: Kör programmet

Om du vill prova snabb starten på din Windows-dator:

1. I verktygsfältet Visual Studio väljer du rätt plattform (antagligen **x64** eller **x86**, inte arm).
   > Observera att mål enheten ändras från *enheten* till den *lokala datorn*
1. Välj Felsök | **Starta utan fel sökning**

## <a name="more-information"></a>Mer information

Det här avsnittet innehåller mer information om snabbstarten.

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client)) är det bibliotek som används för att logga in användare och begära säkerhetstoken. Säkerhetstoken används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform för utvecklare. Du kan installera MSAL genom att köra följande kommando i *Package Manager-konsolen* i Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>MSAL-initiering

Du kan lägga till referensen för MSAL genom att lägga till följande kod:

```csharp
using Microsoft.Identity.Client;
```

Sedan initieras MSAL med hjälp av följande kod:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = new PublicClientApplicationBuilder.Create(ClientId)
                                                    .Build();
```

> |Där: ||
> |---------|---------|
> | `ClientId` | Är **Program-ID (klient)** för det program som registrerats på Azure-portalen. Du hittar det här värdet på appens **översiktssida** på Azure-portalen. |

### <a name="requesting-tokens"></a>Begära token

MSAL har två metoder för att förvärva tokens i en UWP-app: `AcquireTokenInteractive` och `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Vissa situationer kräver tvingande av användare att interagera med Microsoft Identity Platform-slutpunkten via ett popup-fönster för att antingen verifiera sina autentiseringsuppgifter eller ge medgivande. Några exempel är:

- Användare som loggar in på programmet första gången
- När användarna kan behöva ange sina autentiseringsuppgifter igen eftersom lösenordet har upphört att gälla
- När ditt program begär åtkomst till en resurs, så att användaren måste samtycka till
- När tvåfaktorsautentisering krävs

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

> |Där:||
> |---------|---------|
> | `scopes` | Innehåller de omfång som begärs, till exempel `{ "user.read" }` för Microsoft Graph eller `{ "api://<Application ID>/access_as_user" }` för anpassade webb-API:er. |

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Använd metoden för att hämta token för att få åtkomst till skyddade resurser `AcquireTokenInteractive` efter den första metoden. `AcquireTokenSilent` Du vill inte kräva att användaren validerar sina autentiseringsuppgifter varje gång de behöver åtkomst till en resurs. Det mesta av den tid som du vill ha token-förvärv och förnyelse utan användar interaktion

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Där: ||
> |---------|---------|
> | `scopes` | Innehåller de omfång som begärs, till exempel `{ "user.read" }` för Microsoft Graph eller `{ "api://<Application ID>/access_as_user" }` för anpassade webb-API:er |
> | `firstAccount` | Anger det första användar kontot i cachen (MSAL stöder flera användare i en enda app) |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Prova att använda självstudien om Windows-skrivbordet för en komplett stegvis guide till att skapa appar och nya funktioner, däribland en fullständig förklaring av den här snabbstarten.

> [!div class="nextstepaction"]
> [UWP – Självstudie om att anropa Graph API](tutorial-v2-windows-uwp.md)

Hjälp oss att förbättra Microsoft Identity Platform. Berätta för oss vad du tycker genom att slutföra en kort enkät med två frågor.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform-undersökning](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)