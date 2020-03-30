---
title: Anropa ett ASP.NET webb-API som skyddas av Microsofts identitetsplattform
description: I den här snabbstarten kan du läsa om hur du anropar ett ASP.NET webb-API som skyddas av Microsoft identity platform från ett WPF-program (Windows Desktop). WPF-klienten autentiserar en användare, begär en åtkomsttoken och anropar webb-API:et.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 582afef8929da2ba75aab70c1ed0fa9e57fd3f19
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76703481"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>Snabbstart: Anropa ett ASP.NET webb-API som skyddas av Microsofts identitetsplattform

I den här snabbstarten exponerar du ett webb-API och skyddar det så att endast autentiserade användare kan komma åt det. Det här exemplet visar hur du exponerar ett ASP.NET webb-API så att det kan acceptera token som utfärdats av personliga konton (inklusive outlook.com, live.com och andra) samt arbets- och skolkonton från alla företag eller organisationer som har integrerats med Microsoft-identitet Plattform.

Exemplet innehåller också en WPF-klient (Windows Desktop Application) som visar hur du kan begära en åtkomsttoken för att komma åt ett webb-API.

## <a name="prerequisites"></a>Krav

För att kunna köra det här exemplet behöver du följande:

* Visual Studio 2017 eller 2019.  Ladda ner [Visual Studio gratis](https://www.visualstudio.com/downloads/).

* Antingen ett [Microsoft-konto](https://www.outlook.com) eller [ett Office 365-utvecklarprogram](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Hämta eller klona det här exemplet

Du kan klona det här exemplet från skalet eller kommandoraden:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

Du kan också [hämta exemplet som en ZIP-fil](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api-in-the-application-registration-portal"></a>Registrera webb-API:et i programregistreringsportalen

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Välj den Azure AD-klient där du vill skapa dina program

Om du vill registrera dina appar manuellt måste du som ett första steg:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto finns i mer än en Azure AD-klient väljer du din profil längst upp till höger i menyn högst upp på sidan och byter sedan **katalog**.
   Ändra din portalsession till önskad Azure AD-klientorganisation.

### <a name="register-the-service-app-todolistservice"></a>Registrera tjänstappen (TodoListService)

1. Navigera till sidan Microsoft identity platform för utvecklare [Appregistreringar.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Välj **Ny registrering**.
1. När sidan **Registrera en ansökan** visas anger du registreringsinformationen för din ansökan:
   - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `AppModelv2-NativeClient-DotNet-TodoListService`.
   - Ändra **kontotyper som stöds** till konton i en **organisationskatalog**.
   - Välj **Registrera** för att skapa programmet.

1. Leta reda på värdet **program (klient) och** registrera det för senare på sidan App **översikt.** Du behöver den för att konfigurera Visual Studio-konfigurationsfilen för det här projektet (`ClientId` i `TodoListService\Web.config`).
1. Markera avsnittet **Exponera ett API** och:
   - Välj **Lägg till ett scope**
   - acceptera den föreslagna application-ID-URI:n (api://{clientId}) genom att välja **Spara och fortsätt**
   - Ange följande parametrar:
     - för **Användning av scopenamn**`access_as_user`
     - Se till att alternativet **Administratörer och användare** är valt för Vem kan **medgivande**
     - i **Namntyp för administratörsmedgivande**`Access TodoListService as a user`
     - i **Beskrivningstyp för administratörsmedgivande**`Accesses the TodoListService Web API as a user`
     - i **Namntyp för visning av användarmedgivande**`Access TodoListService as a user`
     - i **Beskrivningstyp för användarsamgivande**`Accesses the TodoListService Web API as a user`
     - Behåll **tillstånd** som **aktiverat**
     - Välj **Lägg till omfattning**

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>Konfigurera serviceprojektet så att det matchar det registrerade webb-API:et 

1. Öppna lösningen i Visual Studio och öppna sedan filen **Web.config** under roten **till TodoListService-projektet.**
1. Ersätt parametervärdet `ida:ClientId` med **klient-ID (Application ID)** från det program som du just registrerade i programregistreringsportalen.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Lägg till det nya scopet i *TodoListClient's*app.config

1. Öppna **filen app.config** som finns i **TodoListClient-projektets** rotmapp och klistra sedan in **program-ID** från det `{Enter the Application ID of your TodoListService from the app registration portal}`program som du just registrerat för *TodoListService* under `TodoListServiceScope` parametern och ersätt strängen .

   > Kontrollera att följande format används:
   >
   > `api://{TodoListService-Application-ID}/access_as_user` 
   >
   >(där {TodoListService-Application-ID} är DET GUID som representerar program-ID:t för TodoListService).

## <a name="register-the-client-app-todolistclient"></a>Registrera klientappen (TodoListClient)

I det här steget konfigurerar du *TodoListClient-projektet* genom att registrera ett nytt program i programregistreringsportalen. I de fall där klienten och servern betraktas som *samma program* kan du också bara återanvända samma program som registrerats i "Steg 2.". Det behövs samma program om du vill att användarna ska logga in med Microsofts personliga konton

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>Registrera *TodoListClient-programmet* i *programregistreringsportalen*

1. Navigera till sidan Microsoft identity platform för utvecklare [Appregistreringar.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Välj **Ny registrering**.
1. När sidan **Registrera en ansökan** visas anger du registreringsinformationen för din ansökan:
   - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `NativeClient-DotNet-TodoListClient`.
   - Ändra **kontotyper som stöds** till konton i en **organisationskatalog**.
   - Välj **Registrera** för att skapa programmet.
1. På appens översiktssida väljer du avsnittet **Autentisering**.
   - I avsnittet **Omdirigera URI:er** | **Föreslagen omdirigering för offentliga klienter (mobila, stationära)** kontrollerar du**https://login.microsoftonline.com/common/oauth2/nativeclient**
   - Välj **Spara**.
1. Välj avsnittet **API-behörigheter**
   - Klicka på knappen **Lägg till en behörighet** och sedan
   - Välj fliken **Mina API:er.**
   - I listan över API:er väljer du `AppModelv2-NativeClient-DotNet-TodoListService API`det eller namnet som du angav för webb-API:et.
   - Kontrollera **behörigheten access_as_user** om den inte redan är markerad. Använd sökrutan om det behövs.
   - Välj knappen **Lägg till behörigheter**

### <a name="configure-your-todolistclient-project"></a>Konfigurera *todolistclient-projektet*

1. I *programregistreringsportalen*kopierar du värdet för **programmets (klient)-ID** i sidan **Översikt**
1. Öppna **filen app.config** som finns i **TodoListClient-projektets** rotmapp `ida:ClientId` och klistra sedan in värdet i parametervärdet

## <a name="run-your-project"></a>Köra projektet

1. Tryck `<F5>` för att köra projektet. Din *TodoListClient* ska öppnas.
1. Välj **Logga in längst** upp till höger och logga in med samma användare som du har använt för att registrera ditt program, eller en användare i samma katalog.
1. Om du loggar in för första gången kan du uppmanas att godkänna *TodoListService* Web Api.
1. Inloggningen begär också åtkomsttoken till *access_as_user* omfånget för att komma åt *TodoListService* Web Api och manipulera *Att göra-listan.*

## <a name="pre-authorize-your-client-application"></a>Förauktorisera ditt klientprogram

Ett sätt att tillåta användare från andra kataloger att komma åt ditt webb-API är att *föra auktoriserade* klientprogrammen åt ditt webb-API genom att lägga till program-ID:n från klientprogram i listan över *förauktoriserade* program för webb-API:et. Genom att lägga till en förauktoriserat klient behöver du inte användarens samtycke till att använda ditt webb-API. Följ stegen nedan för att förauktorisera webbprogrammet::

1. Gå tillbaka till *programregistreringsportalen* och öppna egenskaperna **för din TodoListService**.
1. I avsnittet **Exponera ett API** klickar du på Lägg till ett **klientprogram** under avsnittet *Auktoriserade klientprogram.*
1. Klistra in programmets program-ID i `TodoListClient` fältet *Klient-ID.*
1. I avsnittet *Auktoriserade scope* väljer du omfånget för det här webb-API:et `api://<Application ID>/access_as_user`.
1. Tryck på knappen **Lägg till program** längst ned på sidan.

## <a name="run-your-project"></a>Köra projektet

1. Tryck `<F5>` för att köra projektet. Din *TodoListClient* ska öppnas.
1. Välj **Logga in längst** upp till höger (eller Rensa cache/inloggning) och logga sedan in antingen med ett personligt Microsoft-konto (live.com eller hotmail.com) eller arbets- eller skolkonto.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Valfritt: Begränsa inloggningsåtkomsten till ditt program

Som standard, när du hämtar det här kodexemplet och konfigurerar programmet så att det använder Azure Active Directory v2-slutpunkten enligt föregående steg, både personliga konton – som outlook.com, live.com och andra – samt arbets- eller skolkonton från alla organisationer som är integrerade med Azure AD kan begära token och komma åt ditt webb-API. 

Om du vill begränsa vem som kan logga in på ditt program använder du något av alternativen:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Alternativ 1: Begränsa åtkomsten till en enda organisation (en enda klient)

Du kan begränsa inloggningsåtkomst för ditt program till endast användarkonton som finns i en enda Azure AD-klientorganisation – inklusive *gästkonton* för den klienten. Det här scenariot är vanligt för *affärsprogram:*

1. Öppna filen **App_Start\Startup.Auth** och ändra värdet för den metadataslutpunkt som `OpenIdConnectSecurityTokenProvider` skickas `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` till (du kan också använda `contoso.onmicrosoft.com`klientnamnet, till exempel ).
2. I samma fil anger `ValidIssuer` du `TokenValidationParameters` egenskapen `ValidateIssuer` på `true`till `"https://sts.windows.net/{Tenant ID}/"` och argumentet till .

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Alternativ 2: Använd en anpassad metod för att validera emittenter

Du kan implementera en anpassad metod för att validera utfärdare med parametern **IssuerValidator.** Mer information om hur du använder den här parametern finns i [klassen TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg
Läs mer om det skyddade webb-API-scenariot som Microsoft identity-plattformen stöder:
> [!div class="nextstepaction"]
> [Scenario för skyddat webb-API](scenario-protected-web-api-overview.md)
