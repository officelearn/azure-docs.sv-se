---
title: Självstudie – Ge åtkomst till ett Node.js-webb-API från en skrivbordsapp med Azure Active Directory B2C | Microsoft Docs
description: Självstudiekurs som visar hur du använder Active Directory B2C för att skydda ett Node.js-webb-API och anropar det från en .NET-skrivbordsapp.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 90a6a88ff0dc5aab1163e471b24cd1d00e548a1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60315940"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Självstudie: Bevilja åtkomst till ett Node.js-webb-API från en skrivbordsapp med hjälp av Azure Active Directory B2C

I den här självstudiekursen ser du hur du kan anropa en Node.js-webb-API-resurs som skyddas av Azure Active Directory (Azure AD) B2C från en WPF-skrivbordsapp (Windows Presentation Foundation).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Ge behörigheter till webb-API:t
> * Uppdatera exemplet så att programmet används

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför stegen och förutsättningarna i [Självstudie: Aktivera autentisering av konton i ett skrivbordsprogram med hjälp av Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Lägga till ett program för webb-API

Webb-API-resurser måste vara registrerade i klientorganisationen innan de kan godkänna och svara på en begäran från en skyddad resurs från klientprogram som använder en åtkomsttoken. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **Program** och därefter **Lägg till**.
5. Ange ett namn på programmet. Till exempel *webapi1*.
6. För **Inkludera webbapp/webb-API** och **Tillåt implicit flöde** väljer du **Ja**.
7. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. I den här självstudien körs exemplet lokalt och lyssnar på `https://localhost:5000`.
8. För **URI för app-ID** anger du den identifierare som används för webb-API:t. Den fullständiga URI-identifieraren inklusive domänen skapas åt dig. Till exempel `https://contosotenant.onmicrosoft.com/api`.
9. Klicka på **Skapa**.
10. På egenskapssidan antecknar du program-ID:t du kommer att använda när du konfigurerar webbappen.

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Vissa användare kan till exempel ha både läs- och skrivåtkomst medan andra bara har skrivskyddad åtkomst. I den här självstudien definierar du läs- och skrivrättigheter för webb-API:et.

1. Välj **Program** och sedan *webapi1*.
2. Välj **Publicerade omfång**.
3. Som **omfång** anger du `Hello.Read` och som beskrivning anger du `Read access to hello`.
4. Som **omfång** anger du `Hello.Write` och som beskrivning anger du `Write access to hello`.
5. Klicka på **Spara**.

De publicerade omfången kan användas för att tilldela behörighet för webb-API till ett klientprogram.

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett program måste du ge programmet åtkomst till API:t. I den obligatoriska föregående självstudien skapade du en webbapp i Azure AD B2C med namnet *app1*. Du använder det här programmet till att anropa webb-API:t.

1. Välj **Program** och sedan *nativeapp1*.
2. Välj **API-åtkomst** och sedan **Lägg till**.
3. I listrutan **Välj API** väljer du *webapi1*.
4. I listrutan **Välj reservationsomfång** väljer du omfången **Hello.Read** och **Hello.Write** som du definierade tidigare.
5. Klicka på **OK**.

En användare autentiserar med Azure AD B2C för att använda WPF-skrivbordsappen. Skrivbordsappen får ett auktoriseringsbeviljande från Azure AD B2C som ger tillgång till det skyddade webb-API:t.

## <a name="configure-the-sample"></a>Konfigurera exemplet

När webb-API:t är registrerat och har ett definierat omfång måste du konfigurera webb-API-koden så att den använder din Azure AD B2C-klientorganisation. I den här självstudien konfigurerar du en Node.js-webbapp som du kan ladda ned från GitHub. 

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) eller klona exempelwebbappen från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
Exempelwebb-API:et för Node.js använder Passport.js-biblioteket för att aktivera Azure AD B2C att skydda anrop till API: et. 

1. Öppna filen `index.js`.
2. Konfigurera exemplet med registreringsinformation för Azure AD B2C-klientorganisationen. Ändra följande rader med kod:

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Kör exemplet

1. Starta en kommandotolk för Node.js.
2. Gå till den katalog som innehåller Node.js-exemplet. Till exempel `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Kör följande kommandon:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Kör skrivbordsappen

1. Öppna lösningen **active-directory-b2c-wpf** i Visual Studio.
2. Tryck på **F5** för att köra skrivbordsappen.
3. Logga in med e-postadressen och lösenordet som skapades i [självstudien för autentisering av användare med Azure Active Directory B2C i en skrivbordsapp](active-directory-b2c-tutorials-desktop-app.md).
4. Klicka på knappen **Anropa API**. 

Skrivbordsappen gör skickar en förfrågan till webb-API:t och får ett svar med den inloggade användarens visningsnamn. Den skyddade skrivbordsappen anropar det skyddade webb-API:t i Azure AD B2C-klientorganisationen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Ge behörigheter till webb-API:t
> * Uppdatera exemplet så att programmet används

> [!div class="nextstepaction"]
> [Självstudie: Lägga till identitetsproviders i program i Azure Active Directory B2C](tutorial-add-identity-providers.md)
