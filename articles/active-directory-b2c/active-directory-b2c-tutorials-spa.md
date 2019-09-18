---
title: 'Självstudier: Aktivera autentisering i ett program med en sida – Azure Active Directory B2C'
description: Lär dig hur du använder Azure Active Directory B2C för att tillhandahålla användar inloggning för ett enda sid program (Java Script).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06bb71b27edf97f9b7e3333aef93af7bd0e0529e
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064785"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Självstudier: Aktivera autentisering i en ensidesapplikation med hjälp av Azure Active Directory B2C

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

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter programmet *webapp1*.
1. Under **Svars-URL** lägger du till `http://localhost:6420`.
1. Välj **Spara**.
1. På sidan Egenskaper registrerar du **program-ID: t**. Du använder app-ID i ett senare steg när du uppdaterar koden i webb programmet med en sida.

## <a name="get-the-sample-code"></a>Hämta exempelkoden

I den här självstudien konfigurerar du ett kod exempel som du hämtar från GitHub. Exemplet visar hur ett program med en enda sida kan använda Azure AD B2C för användarens registrering och inloggning och för att anropa ett skyddat webb-API.

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) eller klona exemplet från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Uppdatera exemplet

Nu när du har hämtat exemplet uppdaterar du koden med ditt Azure AD B2C klient namn och det program-ID som du registrerade i ett tidigare steg.

1. `index.html` Öppna filen i roten i exempel katalogen.
1. I definitionen ändrar du clientId-värdet med det program-ID som du registrerade i ett tidigare steg. `msalConfig` Uppdatera sedan URI- värdet för utfärdaren med ditt Azure AD B2C klient namn. Uppdatera även URI: n med namnet på det användar flöde för registrering/inloggning som du skapade i en av kraven (till exempel *B2C_1_signupsignin1*).

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

    Namnet på det användarflöde som används i den här självstudien är **B2C_1_signupsignin1**. Om du använder ett annat användar flödes namn anger du det namnet i `authority` värdet.

## <a name="run-the-sample"></a>Kör exemplet

1. Öppna ett konsol fönster och ändra till den katalog som innehåller exemplet. Exempel:

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

1. Välj **Logga in** för att initiera det *B2C_1_signupsignin1* -användarkonto du angav i ett tidigare steg.
1. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du ännu inte har ett konto väljer du länken **Registrera dig nu** .
1. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som definierats i användarflödet.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen.

    ![Registrerings sidan som visas i användar flödet för inloggning/registrering](./media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

1. Välj **skapa** för att skapa ett lokalt konto i katalogen Azure AD B2C.

När du väljer **skapa**stängs sidan Registrera dig och inloggnings sidan visas igen.

Du kan nu använda din e-postadress och ditt lösen ord för att logga in i programmet.

### <a name="error-insufficient-permissions"></a>Fel: otillräcklig behörighet

När du har loggat in visas ett fel meddelande om otillräcklig behörighet. dettaär förväntat:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Du får det här felet eftersom webb programmet försöker få åtkomst till ett webb-API som skyddas av demo katalogen, *fabrikamb2c*. Eftersom din åtkomsttoken bara är giltig för din Azure AD-katalog är API-anropet därför obehörigt.

Du kan åtgärda det här felet genom att fortsätta till nästa självstudie i serien (se [Nästa steg](#next-steps)) för att skapa ett skyddat webb-API för din katalog.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

Gå vidare till nästa självstudie i serien för att bevilja åtkomst till ett skyddat webb-API från SPA:

> [!div class="nextstepaction"]
> [Självstudier: Bevilja åtkomst till ett ASP.NET Core webb-API från ett SPA med Azure AD B2C >](active-directory-b2c-tutorials-spa-webapi.md)
