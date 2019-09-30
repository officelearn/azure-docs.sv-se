---
title: Självstudie – Ge åtkomst till ett Node.js-webb-API från en skrivbordsapp med Azure Active Directory B2C | Microsoft Docs
description: Självstudiekurs som visar hur du använder Active Directory B2C för att skydda ett Node.js-webb-API och anropar det från en .NET-skrivbordsapp.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8c4b2d818549da07faf9ecd28f61b4ed5315f745
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679326"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Självstudie: Bevilja åtkomst till ett Node.js-webb-API från en skrivbordsapp med hjälp av Azure Active Directory B2C

Den här självstudien visar hur du anropar en Azure Active Directory B2C (Azure AD B2C) skyddad Node. js-webb-API-resurs från en Windows Presentation Foundation (WPF) Desktop-app.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Ge behörigheter till webb-API:t
> * Uppdatera exemplet så att programmet används

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen och förutsättningarna i [Självstudie: Aktivera autentisering av konton i ett skrivbordsprogram med hjälp av Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Lägga till ett program för webb-API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Vissa användare kan till exempel ha både läs- och skrivåtkomst medan andra bara har skrivskyddad åtkomst. I den här självstudien definierar du läs- och skrivrättigheter för webb-API:et.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett program måste du ge programmet åtkomst till API:t. I den obligatoriska föregående självstudien skapade du en webbapp i Azure AD B2C med namnet *app1*. Du använder det här programmet till att anropa webb-API:t.

1. Välj **Program** och sedan *nativeapp1*.
1. Välj **API-åtkomst** och sedan **Lägg till**.
1. I listrutan **Välj API** väljer du *webapi1*.
1. I list rutan **Välj omfång** väljer du de omfattningar som du definierade tidigare. Till exempel *demo. Read* och *demo. Write*.
1. Välj **OK**.

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
> [Självstudier: Lägga till identitetsproviders i program i Azure Active Directory B2C](tutorial-add-identity-providers.md)
