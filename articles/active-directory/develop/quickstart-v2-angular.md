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
ms.openlocfilehash: 61a098b92db13b8422d9cfebb19610c5de7685cd
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631755"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-spa"></a>Snabbstart: Logga in användare och få en åtkomsttoken i ett vinkelsPA

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

I den här snabbstarten använder du ett kodexempel för att lära dig hur ett spa-program (Angular single page application) kan logga in användare som har personliga Microsoft-konton och arbetskonton och skolkonton. Ett vinkelspalett kan också få en åtkomsttoken för att anropa Microsoft Graph API eller något webb-API.

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio-kod](https://code.visualstudio.com/download) för att redigera projektfiler eller [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) för att köra projektet.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Starta snabbstartsappen genom att använda något av följande alternativ:
>
> ### <a name="option-1-express-register-and-auto-configure-the-app-then-download-the-code-sample"></a>Alternativ 1 (Express): Registrera och konfigurera appen automatiskt. Hämta sedan kodexemplet
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
> 1. Om ditt konto har åtkomst till mer än en klient väljer du kontot längst upp till höger och anger sedan portalsessionen till den Azure Active Directory-klient (Azure AD) som du vill använda.
> 1. Öppna fönstret nya [Azure-portal - Appregistreringar.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs)
> 1. Ange ett namn på programmet och välj sedan **Registrera**.
> 1. Navigera till snabbstartsfönstret och visa snabbstarten Kantig. Följ instruktionerna för att hämta och konfigurera ditt nya program automatiskt.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Alternativ 2 (Manuell): Registrera och konfigurera program- och kodexemplet manuellt
>
> #### <a name="step-1-register-the-application"></a>Steg 1: Registrera ansökan
>
> 1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
>
> 1. Om ditt konto har åtkomst till mer än en klient väljer du ditt konto längst upp till höger och anger din portalsession till den Azure AD-klient som du vill använda.
> 1. Följ instruktionerna för att registrera ett ensidigt program i [Azure-portalen.](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration)
> 1. Lägg till en ny plattform i **autentiseringsbladet** för din appregistrering och registrera omdirigerings-URI:`http://localhost:4200/`
> 1. Den här snabbstarten använder det [implicita bidragsflödet](v2-oauth2-implicit-grant-flow.md). Välj **inställningarna** för implicit beviljande för **ID-token** och **åtkomsttoken .** ID-token och åtkomsttoken krävs eftersom den här appen loggar in användare och anropar ett API.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Steg 1: Konfigurera programmet i Azure-portalen
> För att kodexemplet för snabbstart ska `redirectUri` `http://localhost:4200/` fungera måste du lägga till en som och aktivera **Implicit bidrag**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-javascript/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-code-sample"></a>Steg 2: Ladda ner kodexemplet
>[!div renderon="docs"]
>Om du vill köra projektet med en webbserver https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular med nod.js klonar eller [hämtar du](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip) kärnprojektfilerna. Öppna filerna med hjälp av en redigerare, till exempel Visual Studio Code.

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
> Ersätt dessa värden som sådana:
>
>|Värdenamn|Beskrivning|
>|---------|---------|
>|Enter_the_Application_Id_Here|På **översiktssidan för** din ansökan registrering, detta är din **ansökan (klient) ID** |
>|Enter_the_Cloud_Instance_Id_Here|Detta är instansen av Azure-molnet. För huvud- eller globala https://login.microsoftonline.comAzure-molnet anger du . För nationella moln (till exempel Kina), se [Nationella moln](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
>|Enter_the_Tenant_Info_Here| Ange ett av följande alternativ: 1) Om programmet stöder *konton i den här organisationskatalogen*ersätter du det här värdet med **katalog-ID:t (klient)** eller **klientnamn** (till exempel *contoso.microsoft.com*). 2) Om ditt program stöder *konton i en organisationskatalog* **ersätter**du det här värdet med organisationer . 3) Om ditt program stöder *konton i en organisationskatalog och personliga Microsoft-konton*ersätter du det här värdet med **vanliga**. 4) Om du bara vill begränsa supporten till *personliga Microsoft-konton*ersätter du det här värdet med **konsumenter**. |
>|Enter_the_Redirect_Uri_Here|Ersätt med`http://localhost:4200`|
>|cachePlats  | (Valfritt) Anger webbläsarlagring för auth-tillståndet. Standard är sessionStorage.   |
>|storeAuthStateInCookie  | (Valfritt) Biblioteket som lagrar tillståndet för autentiseringsbegäran. Det här tillståndet krävs för att validera autentiseringsflödena i webbläsarcookiesna. Den här cookien är inställd för webbläsarna Internet Explorer och Edge för att kunna hantera dessa två webbläsare. Mer information finns i [kända problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
> > [!TIP]
> > För att hitta värdena för **program-ID (klient)**, **katalog-ID (klient)** och **Kontotyper som stöds** går du till appens **översiktssida** i Azure-portalen.

Mer information om tillgängliga konfigurerbara alternativ finns i [Initiera klientprogram](msal-js-initializing-client-applications.md).

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>Steg 3: Kör projektet

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Steg 4: Kör projektet

* Om du använder Node.js:

    1. Starta servern genom att köra följande kommandon från projektkatalogen:

        ```console
        npm install
        npm start
        ```

    1. Bläddra `http://localhost:4200/`bland .
    1. Välj **Logga in**.
    1. Välj **Profil** om du vill anropa Microsoft Graph.

När webbläsaren har läser in programmet väljer du **Logga in**. Första gången du loggar in uppmanas du att ge ditt samtycke till att programmet ska kunna komma åt din profil och logga in dig. När du har loggat in väljer du **Profil**och din användarprofilinformation visas på sidan.

## <a name="how-the-sample-works"></a>Så här fungerar exemplet

![Så här fungerar exempelappen i den här snabbstarten](media/quickstart-v2-javascript/javascriptspa-intro.svg)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> Gå till den [kantiga självstudien](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular) för att lära dig hur du loggar in en användare och hämtar token.

Bläddra i [MSAL-osien](https://github.com/AzureAD/microsoft-authentication-library-for-js) för dokumentation, vanliga frågor, problem med mera.
