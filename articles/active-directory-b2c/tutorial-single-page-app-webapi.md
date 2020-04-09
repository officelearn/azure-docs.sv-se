---
title: 'Självstudiekurs: Skydda ett nod.js-webb-API med Azure AD B2C och bevilja åtkomst till ett ensidigt program (SPA)'
titleSuffix: Azure AD B2C
description: I den här självstudien kan du lära dig hur du använder Active Directory B2C för att skydda ett nod.js-webb-API och anropa det från ett ensidigt program.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 50524159186987b7a30015c878fa3fac949afc79
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875711"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Självstudiekurs: Skydda och bevilja åtkomst till ett Node.js-webb-API från ett ensidigt program med Azure AD B2C

Den här självstudien visar hur du anropar ett Azure Active Directory B2C (Azure AD B2C)-skyddat webb-API för nod.js från ett ensidigt program.

I den här självstudien, den andra i en serie i två delar:

> [!div class="checklist"]
> * Skapa en webb-API-programregistrering i din Azure AD B2C-klient
> * Konfigurera scope för webb-API:et
> * Ge behörigheter till webb-API:t
> * Ändra ett webb-API-kodexempel för att arbeta med din klientorganisation

I den [första självstudien](tutorial-single-page-app.md) i den här serien hämtade du kodexemplet och ändrade det för att logga in användare med ett användarflöde i din Azure AD B2C-klient.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

* Slutför stegen och förutsättningarna i [självstudiekursen: Aktivera autentisering i ett ensidigt program med Azure AD B2C](tutorial-single-page-app.md)
* [Visual Studio-kod](https://code.visualstudio.com/) eller annan kodredigerare
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Lägga till ett program för webb-API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Vissa användare kan till exempel ha både läs- och skrivåtkomst medan andra bara har skrivskyddad åtkomst. I den här självstudien definierar du både läs- och skrivbehörighet för webb-API:et.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Registrera värdet under **Scopes** för scope som `demo.read` ska användas i ett senare steg när du konfigurerar ensidiga programmet. Det fullständiga scopevärdet `https://contosob2c.onmicrosoft.com/api/demo.read`liknar .

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett annat program måste du bevilja programbehörigheterna till webb-API:et.

I den nödvändiga självstudien skapade du ett webbprogram med namnet *webapp1*. I den här självstudien konfigurerar du programmet så att det anropar webb-API:et som du skapade i ett tidigare avsnitt, *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Ditt ensidiga webbprogram har nu beviljats behörighet till det skyddade webb-API:et för de angivna scopena. En användare autentiserar med Azure AD B2C för att använda ensidiga program. Den ensidiga appen använder auktoriseringsbidragsflödet för att komma åt det skyddade webb-API:et med en åtkomsttoken som returneras av Azure AD B2C.

## <a name="configure-the-sample"></a>Konfigurera exemplet

Nu när webb-API:et är registrerat och du har definierat scope konfigurerar du webb-API-koden så att den fungerar med din Azure AD B2C-klient. I den här självstudien konfigurerar du ett exempel på Node.js-webb-API som du hämtar från GitHub.

[Hämta \*ett ZIP-arkiv](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) eller klona exempelweet-projektet från GitHub. Du kan också bläddra direkt till [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi-projektet](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) på GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurera webb-API

1. Öppna *filen config.js* i kodredigeraren.
1. Ändra variabelvärdena så att de återspeglar de programregistrering som du skapade tidigare. Uppdatera också `policyName` med användarflödet som du skapade som en del av förutsättningarna. Till exempel *B2C_1_signupsignin1*.

    ```javascript
    const clientID = "<your-webapi-application-ID>"; // Application (client) ID
    const b2cDomainHost = "<your-tenant-name>.b2clogin.com";
    const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (a GUID)
    const policyName = "B2C_1_signupsignin1";
    ```

#### <a name="enable-cors"></a>Aktivera CORS

Om du vill att ditt ensidiga program ska kunna anropa webb-API:et Node.js måste du aktivera [CORS](https://expressjs.com/en/resources/middleware/cors.html) i webb-API:et. I ett produktionsprogram bör du vara försiktig med vilken domän som gör begäran, men för den här självstudien, tillåta begäranden från alla domäner.

Om du vill aktivera CORS använder du följande mellanprogram. I exemplet med Node.js-webb-API-kod i den här självstudien har den redan lagts till i *filen index.js.*

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>Konfigurera ett ensidigt program

Det ensidiga programmet (SPA) från föregående [självstudie i](tutorial-single-page-app.md) serien använder Azure AD B2C för användares registrering och inloggning, och anropar som standard nod.js-webb-API:et som skyddas av *den fabrikamb2c-demoklienten.*

I det här avsnittet uppdaterar du det ensidiga webbprogrammet för att anropa nod.js-webb-API:et som skyddas av *din* Azure AD B2C-klient (och som du kör på den lokala datorn).

Så här ändrar du inställningarna i SPA:et:

1. Öppna filen *apiConfig.js* i *JavaScriptSPA-mappen* i [active-directory-b2c-javascript-msal-singlepageapp-projektet][github-js-spa] som du hämtade eller klonade i föregående självstudiekurs.
1. Konfigurera exemplet med URI för *det demo.read-scope* som du skapade tidigare och url:en för webb-API:et.
    1. I `apiConfig` definitionen ersätter `b2cScopes` du värdet med hela URI för *scopet demo.read* **(scopevärdet** som du spelade in tidigare).
    1. Ändra domänen `webApi` i värdet till den omdirigera URI som du lade till när du registrerade webb-API-programmet i ett tidigare steg.

    Eftersom API:et `/hello` är tillgängligt vid slutpunkten lämnar du */hello* i URI:n.

    Definitionen `apiConfig` ska se ut ungefär som följande kodblock, men med din B2C-klientnamn i stället `<your-tenant-name>`för:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Kör SPA- och webb-API:et

Du är nu redo att testa det ensidiga programmets begränsade åtkomst till API:et. Kör både nod.js-webb-API:et och exempelprogrammet JavaScript på den lokala datorn. Logga sedan in på ensidiga programmet och välj knappen **Anropa API** för att initiera en begäran till det skyddade API:et.

Även om båda programmen körs lokalt när du följer den här självstudien har du konfigurerat dem för att använda Azure AD B2C för säker registrering/inloggning och för att bevilja åtkomst till det skyddade webb-API:et.

### <a name="run-the-nodejs-web-api"></a>Köra webb-API:et nod.js

1. Öppna ett konsolfönster och ändra till katalogen som innehåller webb-API-exemplet Node.js. Ett exempel:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Kör följande kommandon:

    ```console
    npm install && npm update
    node index.js
    ```

    Konsolfönstret visar portnumret där programmet finns.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Köra ensidig app

1. Öppna ett annat konsolfönster och ändra till katalogen som innehåller JavaScript SPA-exemplet. Ett exempel:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```

1. Kör följande kommandon:

    ```console
    npm install && npm update
    npm start
    ```

    Konsolfönstret visar portnumret där programmet finns.

    ```console
    Listening on port 6420...
    ```

1. Navigera `http://localhost:6420` till i webbläsaren för att visa programmet.

    ![Exempelapp för en sida visas i webbläsaren](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Logga in med den e-postadress och det lösenord som du använde i [föregående självstudie .](tutorial-single-page-app.md) Vid lyckad inloggning bör `User 'Your Username' logged-in` du se meddelandet.
1. Välj knappen **Anropa API.** SPA erhåller ett auktoriseringsbidrag från Azure AD B2C och kommer sedan åt det skyddade webb-API:et för att visa namnet på den inloggade användaren:

    ![Ensidig applikation i webbläsaren som visar JSON-användarnamnet JSON-resultat som returneras av API](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Skapade en registrering av webb-API-program i din Azure AD B2C-klient
> * Konfigurerade scope för webb-API:et
> * Beviljade behörigheter till webb-API:et
> * Ändrat ett webb-API-kodexempel för att arbeta med din klientorganisation

Nu när du har sett en SPA-begäran om en resurs från ett skyddat webb-API får du en djupare förståelse för hur dessa programtyper interagerar med varandra och med Azure AD B2C.

> [!div class="nextstepaction"]
> [Programtyper som kan användas i Active Directory B2C->](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
