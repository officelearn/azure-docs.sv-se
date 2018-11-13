---
title: 'Snabbstart: Azure AD v2 Windows-skrivbord | Microsoft Docs'
description: Lär dig hur ett Windows-skrivbordsprogram med .NET (XAML) kan hämta en åtkomsttoken och anropa ett API som skyddas av en Azure Active Directory v2.0-slutpunkt.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/01/2018
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7e7e5e16b6f7de1cee8312fd31801c202c3e16ef
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962919"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>Snabbstart: Hämta en token och anropa Microsoft Graph API från en Windows-skrivbordsapp

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

I den här snabbstarten får du lära dig att skriva ett Windows-skrivbordsprogram med .NET (WPF) som kan logga in personliga konton och arbets- och skolkonton, hämta en åtkomsttoken samt anropa Microsoft Graph API.

![Så fungerar den exempelapp som genereras med den här snabbstarten](media/quickstart-v2-windows-desktop/windesktop-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> [!div renderon="docs" class="sxs-lookup"]
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel
>
> 1. Gå till [Azure-portalen – Programregistrering](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps)
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet manuellt
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
> 1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten och sedan **Appregistreringar (förhandsversion)** > **Ny registrering**.
> 1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
>      - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `Win-App-calling-MsGraph`.
>      - I avsnittet **Kontotyper som stöds** väljer du **Konton alla organisationskataloger och personliga Microsoft-konton (till exempel Skype, Xbox och Outlook.com)**.
>      - Välj **Registrera** för att skapa programmet.
> 1. I listan över sidor för appen väljer du **Autentisering**.
> 1. I avsnittet **Omdirigerings-URI:er** letar du upp avsnittet **Föreslagna omdirigerings-URI:er för offentliga klienter (mobil, skrivbord)** och väljer **"urn:ietf:wg:oauth:2.0:oob**.
> 1. Välj **Spara**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Steg 1: Konfigurera programmet
> För att kodexemplet för den här snabbstarten ska fungera behöver du lägga till en svars-URL som **urn:ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-windows-desktop/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-your-visual-studio-project"></a>Steg 2: Ladda ned ditt Visual Studio-projekt

[Ladda ned Visual Studio 2017-projektet](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Steg 3: Konfigurera ditt Visual Studio-projekt

1. Extrahera zip-filen i en lokal mapp nära diskens rot, till exempel **C:\Azure-Samples**.
1. Öppna projektet i Visual Studio.
1. Redigera **App.Xaml.cs** och ersätt värdena för fälten `ClientId` och `Tenant` med följande kod:

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    private static string Tenant = "Enter_the_Tenant_Info_Here";
    ```

> [!div renderon="docs"]
> Där:
> - `Enter_the_Application_Id_here` – är **program-ID (klient)** för programmet som du har registrerat.
> - `Enter_the_Tenant_Info_Here` – är inställt på något av följande alternativ:
>   - Om ditt program stöder **Endast konton i den här organisationskatalogen** ska du ersätta värdet med **klient-ID** eller **klientnamn** (till exempel contoso.microsoft.com)
>   - Om ditt program stöder **Konton i valfri organisationskatalog** ersätter du värdet med `organizations`
>   - Om ditt program stöder **Konton i en valfri organisationskatalog och personliga Microsoft-konton** ersätter du värdet med `common`
>
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och **Kontotyper som stöds** går du till appens **översiktssida** i Azure-portalen.

## <a name="more-information"></a>Mer information

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) är det bibliotek som används för att logga in användare och begära token som används för åtkomst till ett API som skyddas av Microsoft Azure Active Directory (Azure AD). Du kan installera MSAL genom att köra följande kommando i **Package Manager-konsolen** i Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

### <a name="msal-initialization"></a>MSAL-initiering

Du kan lägga till referensen för MSAL genom att lägga till följande kod:

```csharp
using Microsoft.Identity.Client;
```

Initiera sedan MSAL med hjälp av följande kod:

```csharp
public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
```

> |Där: ||
> |---------|---------|
> | `ClientId` | Är **Program-ID (klient)** för det program som registrerats på Azure-portalen. Du hittar det här värdet på appens **översiktssida** på Azure-portalen. |

### <a name="requesting-tokens"></a>Begära token

MSAL har två metoder för hämtning av token: `AcquireTokenAsync` och `AcquireTokenSilentAsync`.

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Vissa situationer kräver att användare tvingas interagera med Azure AD v2.0-slutpunkten via ett popup-fönster för att antingen verifiera sina autentiseringsuppgifter eller ge sitt medgivande. Några exempel är:

- Första gången användaren loggar in på programmet
- När användarna kan behöva ange sina autentiseringsuppgifter igen eftersom lösenordet har upphört att gälla
- När programmet begär åtkomst till en resurs som användaren behöver ge sitt medgivande för
- När tvåfaktorsautentisering krävs

```csharp
authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
```

> |Där:||
> |---------|---------|
> | `_scopes` | Innehåller de omfång som begärs, till exempel `{ "user.read" }` för Microsoft Graph eller `{ "api://<Application ID>/access_as_user" }` för anpassade webb-API:er. |

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Du vill inte kräva att användarna verifierar sina autentiseringsuppgifter varje gång de behöver komma åt en resurs. I de flesta fall vill du ha hämtning och förnyelse av token utan någon användarinteraktion. Du kan använda metoden `AcquireTokenSilentAsync` för att hämta token för att komma åt skyddade resurser efter den inledande metoden `AcquireTokenAsync`:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());
```

> |Där: ||
> |---------|---------|
> | `scopes` | Innehåller de omfång som begärs, till exempel `{ "user.read" }` för Microsoft Graph eller `{ "api://<Application ID>/access_as_user" }` för anpassade webb-API:er. |
> | `accounts.FirstOrDefault()` | Anger den första användaren i cachen (MSAL stöder flera användare i en enda app). |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Prova att använda självstudien om Windows-skrivbordet för en komplett stegvis guide till att skapa appar och nya funktioner, däribland en fullständig förklaring av den här snabbstarten.

> [!div class="nextstepaction"]
> [Självstudie om att anropa Graph API](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-windesktop)

