---
title: 'Snabbstart: Konfigurera inloggning för en ensidig app (SPA)'
titleSuffix: Azure AD B2C
description: I den här snabbstarten kör du ett exempel på ensidigt program som använder Azure Active Directory B2C för att tillhandahålla kontosignering.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a5d4319f47530a91bcceb9b2dba94c6aa8e4c388
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78183915"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Snabbstart – Konfigurera inloggning för en ensidesapp med Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) tillhandahåller molnidentitetshantering för att skydda ditt program, företag och kunder. Med Azure AD B2C kan program autentisera med konton på sociala medier och företagskonton med öppna protokoll. I den här snabbstarten använder du ett enkelsidigt program för ASP.NET till att logga in med en social identitetsprovider och anropa en Azure AD B2C-skyddad webb-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) med **arbetsbelastningen ASP.NET och webbutveckling**
- [Node.js](https://nodejs.org/en/download/)
- Socialt konto från Facebook, Google eller Microsoft
- Kodexempel från GitHub: [active-directory-b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    Du kan [ladda ner zip-arkivet](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) eller klona databasen:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Köra appen

1. Starta servern genom att köra följande kommandon från Node.js-kommandotolken:

    ```
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    node server.js
    ```

    Server.js visar det portnummer som lyssnar på localhost.

    ```
    Listening on port 6420...
    ```

2. Bläddra till programmets URL. Till exempel `http://localhost:6420`.

## <a name="sign-in-using-your-account"></a>Logga in på ditt konto

1. Klicka på **Inloggning** för att starta arbetsflödet.

    ![Exempelapp för en sida visas i webbläsaren](./media/quickstart-single-page-app/sample-app-spa.png)

    Exemplet stöder flera registreringsalternativ, till exempel att använda en social identitetsprovider eller att skapa ett lokalt konto med en e-postadress. För den här snabbstarten använder du ett konto för en social identitetsleverantör från antingen Facebook, Google eller Microsoft.

2. Azure AD B2C visar en inloggningssida för ett fiktivt företag som heter Fabrikam för exempelwebbprogrammet. Klicka på knappen för den identitetsprovider som du vill använda för att registrera dig med en social identitetsprovider.

    ![Inloggnings- eller registreringssida med knappar för identitetsprovider](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    Du autentiserar (loggar in) med dina autentiseringsuppgifter för sociala konton och godkänner att programmet läser information från ditt sociala konto. När du beviljar åtkomst kan programmet hämta profilinformation från det sociala kontot, till exempel ditt namn och din ort.

3. Avsluta inloggningsprocessen för identitetsprovidern.

## <a name="access-a-protected-api-resource"></a>Få åtkomst till en skyddad API-resurs

Klicka på **Anropa webb-API** för att returnera ditt visningsnamn från Web API-anropet som ett JSON-objekt.

![Exempelprogram i webbläsaren som visar webb-API-svaret](./media/quickstart-single-page-app/call-api-spa.png)

Det enkelsidiga exempelprogrammet innehåller en åtkomsttoken i begäran till den skyddade webb-API-resursen.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda Azure AD B2C-klientorganisationen om du vill prova andra snabbstarter eller självstudier för Azure AD B2C. När den inte längre behövs kan du ta bort [Azure AD B2C-klientorganisationen](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du ett exempel på ensidig program för att:

* Logga in med en anpassad inloggningssida
* Logga in med en leverantör av social identitet
* Skapa ett Azure AD B2C-konto
* Anropa ett webb-API som skyddas av Azure AD B2C

Kom igång med att skapa en egen Azure AD B2C-klientorganisation.

> [!div class="nextstepaction"]
> [Skapa en Azure Active Directory B2C-klientorganisation i Azure-portalen](tutorial-create-tenant.md)
