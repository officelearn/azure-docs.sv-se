---
title: Snabb start – konfigurera inloggning för en app med en enda sida med hjälp av Azure Active Directory B2C
description: Kör ett ensidesprogram som använder Azure Active Directory B2C för användarinloggningen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 18a2063c2ee2ae734e481cf663573ab5543e5edf
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065801"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Snabbstart: Konfigurera inloggning för en ensidesapp med hjälp av Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) tillhandahåller moln identitets hantering för att hålla ditt program, din verksamhet och dina kunder skyddade. Med Azure AD B2C kan program autentisera med konton på sociala medier och företagskonton med öppna protokoll. I den här snabbstarten använder du ett enkelsidigt program för ASP.NET till att logga in med en social identitetsprovider och anropa en Azure AD B2C-skyddad webb-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) med arbets belastningen **ASP.net och webb utveckling** .
- Installera [Node.js](https://nodejs.org/en/download/)
- Ett socialt konto från Facebook, Google eller Microsoft.
- [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) eller klona exempelwebbappen från GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Köra programmet

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

    ![Exempel program för en enda sida som visas i webbläsare](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

    Exemplet stöder flera registreringsalternativ, till exempel att använda en social identitetsprovider eller att skapa ett lokalt konto med en e-postadress. I den här snabb starten ska du använda ett konto för sociala identitets leverantörer från Facebook, Google eller Microsoft.

2. Azure AD B2C visar en inloggnings sida för ett fiktivt företag som kallas Fabrikam för exempel webb programmet. Klicka på knappen för den identitetsprovider som du vill använda för att registrera dig med en social identitetsprovider.

    ![Sidan logga in eller registrera dig med identitets leverantörs knappar](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    Du autentiserar (loggar in) med dina autentiseringsuppgifter för ditt sociala konto och godkänner att programmet läser information från ditt sociala konto. När du beviljar åtkomst kan programmet hämta profilinformation från det sociala kontot, till exempel ditt namn och din ort.

3. Avsluta inloggningsprocessen för identitetsprovidern.

## <a name="access-a-protected-api-resource"></a>Få åtkomst till en skyddad API-resurs

Klicka på **Anropa webb-API** för att returnera ditt visningsnamn från Web API-anropet som ett JSON-objekt.

![Exempel program i webbläsare som visar svaret på webb-API](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

Det enkelsidiga exempelprogrammet innehåller en åtkomsttoken i begäran till den skyddade webb-API-resursen.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda Azure AD B2C-klientorganisationen om du vill prova andra snabbstarter eller självstudier för Azure AD B2C. När den inte längre behövs kan du ta bort [Azure AD B2C-klientorganisationen](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du ett exempel på en Enkels Ides applikation för att:

* Logga in med en anpassad inloggnings sida
* Logga in med en social identitetsprovider
* Skapa ett Azure AD B2C konto
* Anropa ett webb-API som skyddas av Azure AD B2C

Kom igång med att skapa en egen Azure AD B2C-klientorganisation.

> [!div class="nextstepaction"]
> [Skapa en Azure Active Directory B2C-klientorganisation i Azure-portalen](tutorial-create-tenant.md)
