---
title: 'Självstudie: aktivera autentisering i en app med en enda sida'
titleSuffix: Azure AD B2C
description: I den här självstudien får du lära dig hur du använder Azure Active Directory B2C för att tillhandahålla användar inloggning för ett JavaScript-baserat program med en sida (SPA).
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183354"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c-azure-ad-b2c"></a>Självstudie: aktivera autentisering i ett program med en enda sida med hjälp av Azure Active Directory B2C (Azure AD B2C)

Den här självstudien visar hur du använder Azure Active Directory B2C (Azure AD B2C) för att logga in och registrera användare i ett enda webb program (SPA). Med Azure AD B2C kan program autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med hjälp av öppna standardprotokoll.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande Azure AD B2C resurser på plats innan du fortsätter med stegen i den här självstudien:

* [Azure AD B2C klient](tutorial-create-tenant.md)
* [Programmet är registrerat](tutorial-register-applications.md) i din klient
* [Användar flöden som skapats](tutorial-create-user-flows.md) i din klient organisation

Dessutom behöver du följande i din lokala utvecklings miljö:

* Kod redigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/) eller [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2,2](https://dotnet.microsoft.com/download) eller senare
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Uppdatera programmet

I den andra själv studie kursen som du avslutade som en del av förutsättningarna registrerade du ett webb program i Azure AD B2C. För att möjliggöra kommunikation med exemplet i självstudien behöver du lägga till en omdirigerings-URI i programmet i Azure AD B2C.

Du kan använda den aktuella **program** upplevelsen eller vår nya enhetliga **Appregistreringar (för hands version)** för att uppdatera programmet. [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter programmet *webapp1*.
1. Under **Svars-URL** lägger du till `http://localhost:6420`.
1. Välj **Spara**.
1. På sidan Egenskaper registrerar du **program-ID: t**. Du använder app-ID i ett senare steg när du uppdaterar koden i webb programmet med en sida.

#### <a name="app-registrations-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.
1. Välj **Appregistreringar (för hands version)** , Välj fliken **ägda program** och välj sedan *webapp1* -programmet.
1. Välj **autentisering**och välj sedan **testa den nya upplevelsen** (om den visas).
1. Under **webb**väljer du länken **Lägg till URI** , anger `http://localhost:6420`och väljer sedan **Spara**.
1. Välj **Översikt**.
1. Registrera **program-ID: t (klient)** för användning i ett senare steg när du uppdaterar koden i webb programmet med en sida.

* * *

## <a name="get-the-sample-code"></a>Hämta exempelkoden

I den här självstudien konfigurerar du ett kod exempel som du hämtar från GitHub. Exemplet visar hur ett program med en enda sida kan använda Azure AD B2C för användarens registrering och inloggning och för att anropa ett skyddat webb-API.

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) eller klona exemplet från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Uppdatera exemplet

Nu när du har hämtat exemplet uppdaterar du koden med ditt Azure AD B2C klient namn och det program-ID som du registrerade i ett tidigare steg.

1. Öppna `index.html`-filen i roten i exempel katalogen.
1. I `msalConfig` definition ändrar du värdet för **clientId** med det program-ID som du registrerade i ett tidigare steg. Uppdatera sedan URI-värdet för **utfärdaren** med ditt Azure AD B2C klient namn. Uppdatera även URI: n med namnet på det användar flöde för registrering/inloggning som du skapade i någon av kraven (till exempel *B2C_1_signupsignin1*).

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

    Namnet på det användarflöde som används i den här självstudien är **B2C_1_signupsignin1**. Om du använder ett annat användar flödes namn anger du det namnet i `authority`-värdet.

## <a name="run-the-sample"></a>Kör exemplet

1. Öppna ett konsol fönster och ändra till den katalog som innehåller exemplet. Några exempel:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Kör följande kommandon:

    ```
    npm install && npm update
    node server.js
    ```

    Konsol fönstret visar port numret för det lokalt använda Node. js-servern:

    ```
    Listening on port 6420...
    ```

1. Gå till `http://localhost:6420` i webbläsaren om du vill visa programmet.

Exempelappen har stöd för registrering, inloggning, redigering av profil och återställning av lösenord. Den här självstudien visar hur en användare registrerar sig med hjälp av en e-postadress.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

> [!WARNING]
> Efter registreringen eller inloggningen kan ett [fel meddelande visas om otillräcklig behörighet](#error-insufficient-permissions). På grund av den aktuella implementeringen av kod exemplet förväntas det här felet. Det här problemet kommer att lösas i en framtida version av kod exemplet, då varningen tas bort.

1. Välj **Logga in** för att starta det *B2C_1_signupsignin1* användar flöde du angav i ett tidigare steg.
1. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du ännu inte har ett konto väljer du länken **Registrera dig nu** .
1. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som definierats i användarflödet.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen.

    ![Registrerings sidan som visas i användar flödet för inloggning/registrering](./media/tutorial-single-page-app/azure-ad-b2c-sign-up-workflow.png)

1. Välj **skapa** för att skapa ett lokalt konto i katalogen Azure AD B2C.

När du väljer **skapa**stängs sidan Registrera dig och inloggnings sidan visas igen.

Du kan nu använda din e-postadress och ditt lösen ord för att logga in i programmet.

### <a name="error-insufficient-permissions"></a>Fel: otillräcklig behörighet

När du har loggat in kan programmet returnera ett fel för otillräcklig behörighet:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Du får det här felet eftersom webb programmet försöker få åtkomst till ett webb-API som skyddas av demo katalogen, *fabrikamb2c*. Eftersom din åtkomsttoken bara är giltig för din Azure AD-katalog, är API-anropet inte auktoriserat.

Du kan åtgärda det här felet genom att fortsätta till nästa självstudie i serien (se [Nästa steg](#next-steps)) för att skapa ett skyddat webb-API för din katalog.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

Gå vidare till nästa självstudie i serien för att bevilja åtkomst till ett skyddat webb-API från SPA:

> [!div class="nextstepaction"]
> [Självstudie: bevilja åtkomst till ett ASP.NET Core webb-API från ett SPA med Azure AD B2C >](tutorial-single-page-app-webapi.md)
