---
title: Logga in användare i Vinkel-ensidiga appar - Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur en vinkelapp kan anropa ett API som kräver åtkomsttoken med hjälp av Microsofts identitetsplattform.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: 4b6a2481c18314a44470a020033ffdc4ba1d7259
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380018"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Snabbstart: Logga in användare och få en åtkomsttoken i ett kantigt ensidigt program

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av den här funktionen kan ändras före allmän tillgänglighet (GA).

I den här snabbstarten använder du ett kodexempel för att lära dig hur ett spa-program (Angular single page application) kan logga in användare som har personliga Microsoft-konton, arbetskonton eller skolkonton. Ett vinkelspalett kan också få en åtkomsttoken för att anropa Microsoft Graph API eller något webb-API.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Nod.js](https://nodejs.org/en/download/).
* [Visual Studio-kod](https://code.visualstudio.com/download) för att redigera projektfiler eller [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) för att köra projektet.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Starta snabbstartsappen genom att använda något av följande alternativ.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Alternativ 1 (express): Registrera och konfigurera appen automatiskt och hämta sedan kodexemplet
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com).
> 1. Om ditt konto har åtkomst till mer än en klient väljer du kontot längst upp till höger och anger sedan portalsessionen till Azure Active Directory (Azure AD) som du vill använda.
> 1. Öppna fönstret nya [appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) i Azure-portalen.
> 1. Ange ett namn på programmet och välj sedan **Registrera**.
> 1. Gå till snabbstartsfönstret och visa snabbstarten Kantig. Följ instruktionerna för att hämta och konfigurera ditt nya program automatiskt.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Alternativ 2 (manuell): Registrera och konfigurera program- och kodexemplet manuellt
>
> #### <a name="step-1-register-the-application"></a>Steg 1: Registrera ansökan
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com).
> 1. Om ditt konto har åtkomst till mer än en klient väljer du ditt konto längst upp till höger och anger din portalsession till den Azure AD-klient som du vill använda.
> 1. Följ instruktionerna för att registrera ett ensidigt program i [Azure-portalen.](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration)
> 1. Lägg till en ny plattform i **autentiseringsfönstret** för `http://localhost:4200/`din appregistrering och registrera omdirigerings-URI: .
> 1. Den här snabbstarten använder det [implicita bidragsflödet](v2-oauth2-implicit-grant-flow.md). Välj **implicita** **bidragsinställningar för ID-token** och **accesstoken .** ID-token och åtkomsttoken krävs eftersom den här appen loggar in användare och anropar ett API.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Steg 1: Konfigurera programmet i Azure-portalen
> För att kodexemplet för den här snabbstarten **http://localhost:4200/** ska fungera måste du lägga till en omdirigerings-URI som och aktivera **Implicit bidrag**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-javascript/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-code-sample"></a>Steg 2: Ladda ner kodexemplet
>[!div renderon="docs"]
>Om du vill köra projektet med en webbserver med nod.js [klonar du exempeldatabasen](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) eller [hämtar kärnprojektfilerna](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Öppna filerna med hjälp av en redigerare som Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Ladda ner kodexemplet](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Steg 3: Konfigurera JavaScript-appen
>
> I *src/app-mappen* *redigerar du app.module.ts* och anger `clientId` värdena och `authority` under `MsalModule.forRoot`.
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here


> [!div renderon="docs"]
>
> Ersätt dessa värden:
>
>|Värdenamn|Beskrivning|
>|---------|---------|
>|Enter_the_Application_Id_Here|På **sidan Översikt för** din ansökan registrering, detta är din **Application (klient) ID** värde. |
>|Enter_the_Cloud_Instance_Id_Here|Detta är instansen av Azure-molnet. För huvud- eller globala **https://login.microsoftonline.com**Azure-molnet anger du . För nationella moln (till exempel Kina), se [Nationella moln](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
>|Enter_the_Tenant_Info_Here| Ange ett av följande alternativ: Om programmet stöder konton i den *här organisationskatalogen*ersätter du det här värdet med katalog-ID:t (klient)-ID eller klientnamn (till exempel **contoso.microsoft.com**). Om programmet stöder *konton i en organisationskatalog* **ersätter**du det här värdet med organisationer . Om programmet stöder *konton i en organisationskatalog och personliga Microsoft-konton*ersätter du det här värdet med **vanliga**. Om du bara vill begränsa supporten till *personliga Microsoft-konton*ersätter du det här värdet med **konsumenter**. |
>|Enter_the_Redirect_Uri_Here|Ersätt **http://localhost:4200**med .|
>|cachePlats  | (Valfritt) Ange webbläsarlagring för autentiseringstillståndet. Standard är **sessionStorage**.   |
>|storeAuthStateInCookie  | (Valfritt) Identifiera biblioteket som lagrar tillståndet för autentiseringsbegäran. Det här tillståndet krävs för att validera autentiseringsflödena i webbläsarcookiesna. Den här cookien är inställd för Internet Explorer och Edge för att rymma dessa två webbläsare. Mer information finns i de [kända problemen](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och **Kontotyper som stöds** går du till appens **översiktssida** i Azure-portalen.

Mer information om tillgängliga konfigurerbara alternativ finns i [Initiera klientprogram](msal-js-initializing-client-applications.md). 

Du hittar källkoden för MSAL.js-biblioteket i [AzureAD/microsoft-authentication-library-for-js-databasen](https://github.com/AzureAD/microsoft-authentication-library-for-js) på GitHub.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>Steg 3: Kör projektet

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Steg 4: Kör projektet

Om du använder Node.js:

1. Starta servern genom att köra följande kommandon från projektkatalogen:

   ```console
   npm install
   npm start
   ```

1. Bläddra **http://localhost:4200/** till .
1. Välj **Logga in**.
1. Välj **Profil** om du vill anropa Microsoft Graph.

När webbläsaren har läser in programmet väljer du **Logga in**. Första gången du börjar logga in uppmanas du att ge ditt samtycke så att programmet kan komma åt din profil och logga in dig. När du har loggat in väljer du **Profil**och din användarprofilinformation visas på sidan.

## <a name="how-the-sample-works"></a>Så här fungerar exemplet

![Diagram som visar hur exempelappen i den här snabbstarten fungerar](media/quickstart-v2-javascript/javascriptspa-intro.svg)


## <a name="next-steps"></a>Nästa steg

Lär dig sedan hur du loggar in en användare och hämtar token i den kantiga självstudien:

> [!div class="nextstepaction"]
> [Vinkel handledning](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)

