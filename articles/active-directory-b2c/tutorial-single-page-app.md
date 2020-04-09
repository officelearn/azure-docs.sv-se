---
title: 'Självstudiekurs: Aktivera autentisering i en ensidig app'
titleSuffix: Azure AD B2C
description: I den här självstudien kan du läsa om hur du använder Azure Active Directory B2C för att tillhandahålla användarinloggning för ett JavaScript-baserat ensidigt program (SPA).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: d7cd437f597fc34fe83904715fc2e459dfe4550f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875599"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Självstudiekurs: Aktivera autentisering i ett ensidigt program med Azure AD B2C

Den här självstudien visar hur du använder Azure Active Directory B2C (Azure AD B2C) för att registrera dig och logga in användare i ett ensidigt program (SPA).

I den här självstudien, den första i en serie i två delar:

> [!div class="checklist"]
> * Lägga till en svars-URL till ett program som är registrerat i din Azure AD B2C-klient
> * Hämta ett kodexempel från GitHub
> * Ändra exempelprogrammets kod så att den fungerar med din klientorganisation
> * Registrera dig med ditt användarflöde för registrering/inloggning

[Nästa självstudiekurs](tutorial-single-page-app-webapi.md) i serien aktiverar webb-API-delen av kodexemplet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Du behöver följande Azure AD B2C-resurser på plats innan du fortsätter med stegen i den här självstudien:

* [Azure AD B2C-klient](tutorial-create-tenant.md)
* [Ansökan registrerad](tutorial-register-applications.md) i din klient
* [Användarflöden som skapats](tutorial-create-user-flows.md) i din klientorganisation

Dessutom behöver du följande i din lokala utvecklingsmiljö:

* [Visual Studio-kod](https://code.visualstudio.com/) eller annan kodredigerare
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Uppdatera programmet

I den andra självstudien som du slutförde som en del av förutsättningarna registrerade du ett webbprogram i Azure AD B2C. Om du vill aktivera kommunikation med kodexemplet i den här självstudien lägger du till en svars-URL (kallas även en omdirigerings-URI) i programregistreringen.

Du kan använda den aktuella **programupplevelsen** eller vår nya enhetliga **appregistreringar (förhandsversion)** för att uppdatera programmet. [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** i det övre vänstra hörnet av Azure-portalen och sök sedan efter och välj Azure AD **B2C**.
1. Välj **Program** och därefter programmet *webapp1*.
1. Under **Svars-URL** lägger du till `http://localhost:6420`.
1. Välj **Spara**.
1. På egenskapssidan registrerar du **program-ID: et**. Du använder app-ID:t i ett senare steg när du uppdaterar koden i det ensidiga webbprogrammet.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Välj **Appregistreringar (förhandsgranskning)**, välj fliken **Ägda program** och välj sedan *webapp1-programmet.*
1. Välj **Autentisering**och välj sedan **Prova den nya upplevelsen** (om den visas).
1. Under **Webben**väljer du länken Lägg `http://localhost:6420`till **URI,** anger och väljer sedan **Spara**.
1. Välj **Översikt**.
1. Registrera **program-ID:t (klient)** för användning i ett senare steg när du uppdaterar koden i ett endasideswebbprogram.

* * *

## <a name="get-the-sample-code"></a>Hämta exempelkoden

I den här självstudien konfigurerar du ett kodexempel som du hämtar från GitHub för att fungera med din B2C-klientorganisation. Exemplet visar hur ett ensidigt program kan använda Azure AD B2C för användaranmälan och inloggning, och för att anropa ett skyddat webb-API (du aktiverar webb-API:et i nästa självstudiekurs i serien).

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) eller klona exemplet från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Uppdatera exemplet

Nu när du har fått exemplet uppdaterar du koden med ditt Azure AD B2C-klientnamn och det program-ID som du spelade in i ett tidigare steg.

1. Öppna *filen authConfig.js* i *JavaScriptSPA-mappen.*
1. Uppdatera `msalConfig` i objektet:
    * `clientId`med värde med **programmets (klient)-ID** som du spelade in i ett tidigare steg
    * `authority`URI med ditt Azure AD B2C-klientnamn och namnet på det användarflöde för registrering/inloggning som du skapade som en del av förutsättningarna (till exempel *B2C_1_signupsignin1)*

    ```javascript
    const msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", // Replace this value with your Application (client) ID
            authority: "https://your-b2c-tenant.b2clogin.com/your-b2c-tenant.onmicrosoft.com/B2C_1_signupsignin1", // Update with your tenant and user flow names
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

## <a name="run-the-sample"></a>Kör exemplet

1. Öppna ett konsolfönster och ändra till katalogen som innehåller exemplet. Ett exempel:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Kör följande kommandon:

    ```console
    npm install && npm update
    npm start
    ```

    Konsolfönstret visar portnumret för den lokalt gående Node.js-servern:

    ```console
    Listening on port 6420...
    ```
1. Bläddra `http://localhost:6420` till för att visa webbprogrammet som körs på din lokala dator.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Webbläsare som visar ensidig program som körs lokalt":::

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

Det här exempelprogrammet stöder registrering, inloggning och återställning av lösenord. I den här självstudien registrerar du dig med en e-postadress.

1. Välj **Logga in** om du vill initiera det *B2C_1_signupsignin1* användarflöde som du angav i ett tidigare steg.
1. Azure AD B2C presenterar en inloggningssida som innehåller en registreringslänk. Eftersom du ännu inte har ett konto väljer du länken **Registrera dig nu.**
1. Arbetsflödet för registrering presenterar en sida för att samla in och verifiera användarens identitet med hjälp av en e-postadress. Arbetsflödet för registrering samlar också in användarens lösenord och de begärda attributen som definierats i användarflödet.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Registrera sida som visas av Azure AD B2C användarflöde":::

1. Välj **Skapa** om du vill skapa ett lokalt konto i Azure AD B2C-katalogen.

När du väljer **Skapa**visas namnet på den inloggade användaren.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Webbläsare som visar ensidig applikation med inloggad användare":::

Om du vill testa inloggning väljer du knappen **Logga ut** och väljer sedan **Logga in** och logga in med den e-postadress och det lösenord du angav när du registrerade dig.

### <a name="what-about-calling-the-api"></a>Vad sägs om att anropa API?

Om du väljer knappen **Ring API** efter inloggning visas sidan för användarflöde för registrering/inloggning i stället för resultatet av API-anropet. Detta förväntas eftersom du ännu inte har konfigurerat API-delen av programmet för att kommunicera med ett webb-API-program som är registrerat i *din* Azure AD B2C-klientorganisation.

Nu försöker programmet fortfarande kommunicera med API:et som är registrerat i demoklienten (fabrikamb2c.onmicrosoft.com) och eftersom du inte är autentiserad med den klienten visas inloggnings-/inloggningssidan.

Gå vidare till nästa självstudiekurs i serien för att aktivera det skyddade API:et (se avsnittet [Nästa steg).](#next-steps)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du konfigurerat ett ensidigt program för att arbeta med ett användarflöde i din Azure AD B2C-klient för att tillhandahålla registrerings- och inloggningsfunktioner. Du har slutfört följande steg:

> [!div class="checklist"]
> * Lade till en svars-URL i ett program som är registrerat i din Azure AD B2C-klient
> * Hämtade ett kodexempel från GitHub
> * Ändrade exempelprogrammets kod för att fungera med din klient
> * Registrera dig med ditt användarflöde för registrering/inloggning

Gå nu vidare till nästa handledning i serien för att bevilja åtkomst till ett skyddat webb-API från SPA:

> [!div class="nextstepaction"]
> [Självstudiekurs: Skydda och bevilja åtkomst till webb-API från ett ensidigt program >](tutorial-single-page-app-webapi.md)
