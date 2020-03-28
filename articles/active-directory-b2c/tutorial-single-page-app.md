---
title: 'Självstudiekurs: Aktivera autentisering i en ensidig app'
titleSuffix: Azure AD B2C
description: I den här självstudien kan du läsa om hur du använder Azure Active Directory B2C för att tillhandahålla användarinloggning för ett JavaScript-baserat ensidigt program (SPA).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 435800d9c6bfd9131d50681a9808f9836104fac0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183354"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c-azure-ad-b2c"></a>Självstudiekurs: Aktivera autentisering i ett ensidigt program med Azure Active Directory B2C (Azure AD B2C)

Den här självstudien visar hur du använder Azure Active Directory B2C (Azure AD B2C) för att logga in och registrera användare i ett ensidigt program (SPA). Med Azure AD B2C kan program autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med hjälp av öppna standardprotokoll.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

Du behöver följande Azure AD B2C-resurser på plats innan du fortsätter med stegen i den här självstudien:

* [Azure AD B2C-klient](tutorial-create-tenant.md)
* [Ansökan registrerad](tutorial-register-applications.md) i din klient
* [Användarflöden som skapats](tutorial-create-user-flows.md) i din klientorganisation

Dessutom behöver du följande i din lokala utvecklingsmiljö:

* Kodredigeraren, till exempel [Visual Studio Code](https://code.visualstudio.com/) eller Visual Studio [2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download) eller senare
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Uppdatera programmet

I den andra självstudien som du slutförde som en del av förutsättningarna registrerade du ett webbprogram i Azure AD B2C. För att möjliggöra kommunikation med exemplet i självstudien behöver du lägga till en omdirigerings-URI i programmet i Azure AD B2C.

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

I den här självstudien konfigurerar du ett kodexempel som du hämtar från GitHub. Exemplet visar hur ett ensidigt program kan använda Azure AD B2C för användaranmälan och inloggning och för att anropa ett skyddat webb-API.

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) eller klona exemplet från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Uppdatera exemplet

Nu när du har fått exemplet uppdaterar du koden med ditt Azure AD B2C-klientnamn och det program-ID som du spelade in i ett tidigare steg.

1. Öppna `index.html` filen i roten på exempelkatalogen.
1. Ändra `msalConfig` värdet **clientId** med det program-ID som du spelade in i ett tidigare steg i definitionen. Uppdatera sedan **URI-värdet för auktoriteten** med ditt Azure AD B2C-klientnamn. Uppdatera även URI med namnet på det användarflöde för registrering/inloggning som du skapade i en av förutsättningarna (till exempel *B2C_1_signupsignin1*).

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

    Namnet på det användarflöde som används i den här självstudien är **B2C_1_signupsignin1**. Om du använder ett annat användarnamn anger du `authority` namnet i värdet.

## <a name="run-the-sample"></a>Kör exemplet

1. Öppna ett konsolfönster och ändra till katalogen som innehåller exemplet. Ett exempel:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Kör följande kommandon:

    ```
    npm install && npm update
    node server.js
    ```

    Konsolfönstret visar portnumret för den lokalt gående Node.js-servern:

    ```
    Listening on port 6420...
    ```

1. Gå `http://localhost:6420` till i webbläsaren för att visa programmet.

Exempelappen har stöd för registrering, inloggning, redigering av profil och återställning av lösenord. Den här självstudien visar hur en användare registrerar sig med hjälp av en e-postadress.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

> [!WARNING]
> När du har registrerat dig eller loggat in kan du se ett [otillräckligt behörighetsfel](#error-insufficient-permissions). På grund av kodexemplets aktuella implementering förväntas det här felet. Det här problemet kommer att lösas i en framtida version av kodexemplet, då den här varningen tas bort.

1. Välj **Logga in** om du vill initiera det *B2C_1_signupsignin1* användarflöde som du angav i ett tidigare steg.
1. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du ännu inte har ett konto väljer du länken **Registrera dig nu.**
1. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som definierats i användarflödet.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen.

    ![Registreringssida som visas av användarflödet för inloggning/registrering](./media/tutorial-single-page-app/azure-ad-b2c-sign-up-workflow.png)

1. Välj **Skapa** om du vill skapa ett lokalt konto i Azure AD B2C-katalogen.

När du väljer **Skapa**stängs registreringssidan och inloggningssidan visas igen.

Du kan nu använda din e-postadress och ditt lösenord för att logga in på programmet.

### <a name="error-insufficient-permissions"></a>Fel: otillräcklig behörighet

När du har loggat in kan programmet returnera ett otillräckligt behörighetsfel:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Det här felet visas eftersom webbprogrammet försöker komma åt ett webb-API som skyddas av demokatalogen, *fabrikamb2c*. Eftersom åtkomsttoken endast är giltig för din Azure AD-katalog är API-anropet obehörigt.

Lös det här felet genom att fortsätta till nästa självstudiekurs i serien (se [Nästa steg](#next-steps)) för att skapa ett skyddat webb-API för katalogen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

Gå nu vidare till nästa handledning i serien för att bevilja åtkomst till ett skyddat webb-API från SPA:

> [!div class="nextstepaction"]
> [Självstudiekurs: Bevilja åtkomst till ett ASP.NET Core-webb-API från ett SPA med Azure AD B2C->](tutorial-single-page-app-webapi.md)
