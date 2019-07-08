---
title: Självstudiekurs – aktivera autentisering i ett enkelsidigt program – Azure Active Directory B2C
description: Lär dig hur du använder Azure Active Directory B2C för att tillhandahålla en användarinloggning för en ensidesapp (JavaScript).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/08/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6824cc84c24b41fd82afd39ead3029a212173948
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67624785"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Självstudier: Aktivera autentisering i en ensidesapplikation med hjälp av Azure Active Directory B2C

Den här självstudien lär dig använda Azure Active Directory (Azure AD) B2C för att logga in och registrera användare i en ensidesapplikation (SPA). Med Azure AD B2C kan program autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med hjälp av öppna standardprotokoll.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande Azure AD B2C-resurser på plats innan du fortsätter med stegen i den här självstudien:

* [Azure AD B2C-klient](tutorial-create-tenant.md)
* [Registrerat program](tutorial-register-applications.md) i din klient
* [Användarflöden skapade](tutorial-create-user-flows.md) i din klient

Dessutom behöver du följande i din lokala utvecklingsmiljö:

* Code redigeringsprogram, till exempel [Visual Studio Code](https://code.visualstudio.com/) eller [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [.NET core SDK 2.0.0](https://www.microsoft.com/net/core) eller senare
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Uppdatera programmet

I den andra självstudien som du har slutfört som en del av förutsättningarna, registrerade du ett webbprogram i Azure AD B2C. För att möjliggöra kommunikation med exemplet i självstudien behöver du lägga till en omdirigerings-URI i programmet i Azure AD B2C.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter programmet *webapp1*.
1. Under **Svars-URL** lägger du till `http://localhost:6420`.
1. Välj **Spara**.
1. På egenskapssidan registrera den **program-ID**. Du kan använda app-ID i ett senare steg när du uppdaterar koden i enkelsidiga webbprogrammet.

## <a name="get-the-sample-code"></a>Hämta exempelkoden

I den här självstudien konfigurerar du ett kodexempel som du laddar ned från GitHub. Exemplet visar hur ett enkelsidigt program kan använda Azure AD B2C för registrering och inloggning, och till ett skyddat webb-API-anropet.

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) eller klona exemplet från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Uppdatera exemplet

Nu när du har köpt exemplet kan du uppdatera koden med din Azure AD B2C-klientnamn och det program-ID som du antecknade i ett tidigare steg.

1. Öppna den `index.html` filen i roten av den exempel-katalogen.
1. I den `msalConfig` definition, ändra den **clientId** värdet med det program-ID som du antecknade i ett tidigare steg. Därefter uppdaterar den **utfärdare** URI-värdet med namnet på din Azure AD B2C-klientorganisation. Dessutom uppdatera URI: N med namnet på registrerings-registreringen/logga in användarflödet som du skapade i något av kraven (till exempel *B2C_1_signupsignin1*).

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Namnet på det användarflöde som används i den här självstudien är **B2C_1_signupsignin1**. Om du använder ett annat användarnamn för flödet, anger du det namnet i den `authority` värde.

## <a name="run-the-sample"></a>Kör exemplet

1. Öppna ett konsolfönster och ändra till den katalog som innehåller exemplet. Exempel:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Kör följande kommandon:

    ```
    npm install && npm update
    node server.js
    ```

    Konsolfönstret visar portnumret för lokalt köra Node.js-server:

    ```
    Listening on port 6420...
    ```

1. Gå till `http://localhost:6420` i webbläsaren för att visa programmet.

Exempelappen har stöd för registrering, inloggning, redigering av profil och återställning av lösenord. Den här självstudien visar hur en användare registrerar sig med hjälp av en e-postadress.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

1. Klicka på **Logga in** för att logga in som användare av programmet. Här används den **B2C_1_signupsignin1** användarflödet som du angav i föregående steg.
1. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du inte har något konto klickar du på länken **Registrera dig**.
1. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som definierats i användarflödet.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen.

    ![Arbetsflöde för registrering](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

1. Klicka på **Skapa** och skapa ett lokalt konto i Azure AD B2C-katalogen.

När du klickar på **skapa**, sidan registrera stängs och inloggningssidan visas igen.

Du kan nu använda din e-postadress och lösenord för att logga in till programmet.

### <a name="error-insufficient-permissions"></a>Fel: otillräcklig behörighet

När du loggar in, appen visar ett fel med otillräckliga behörigheter – det här är **förväntat**:

`ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.`

Du får detta felmeddelande eftersom du försöker komma åt en resurs från den demo-katalogen, men ditt åtkomst-token är endast giltig för Azure AD-katalogen. API-anrop kan därför inte användas.

Fortsätt med nästa självstudie i serien (se [nästa steg](#next-steps)) att skapa ett skyddat webb-API för din katalog.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

Nu gå vidare till nästa självstudie i serien att bevilja åtkomst till ett skyddat webb-API från att SPA: N:

> [!div class="nextstepaction"]
> [Självstudie: Bevilja åtkomst till ett ASP.NET Core webb-API från en ensidesapp med hjälp av Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
