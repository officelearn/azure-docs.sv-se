---
title: Självstudie – Aktivera autentisering i en ensidesapplikation – Azure Active Directory B2C | Microsoft Docs
description: Självstudie som lär dig använda Azure Active Directory B2C för att tillhandahålla en användarinloggning till en ensidesapplikation (JavaScript).
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 813c7131ff0a56e843e728cd78fff969b1d90fcc
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756333"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Självstudier: Aktivera autentisering i en ensidesapplikation med hjälp av Azure Active Directory B2C

Den här självstudien lär dig använda Azure Active Directory (Azure AD) B2C för att logga in och registrera användare i en ensidesapplikation (SPA). Med Azure AD B2C kan program autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med hjälp av öppna standardprotokoll.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Skapa användarflöden](tutorial-create-user-flows.md) för att möjliggöra användarupplevelser i programmet. 
* Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med arbetsbelastningen **ASP.NET och webbutveckling**.
* Installera [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) eller senare
* Installera [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Uppdatera programmet

I den självstudien som du slutförde som en del av förutsättningarna lade du till en webbapp i Azure AD B2C. För att möjliggöra kommunikation med exemplet i självstudien behöver du lägga till en omdirigerings-URI i programmet i Azure AD B2C.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** i den översta menyn och välja den katalog som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **Program** och därefter programmet *webapp1*.
5. Under **Svars-URL** lägger du till `http://localhost:6420`.
6. Välj **Spara**.
7. På egenskapssidan antecknar du det program-ID som du kommer att använda när du konfigurerar webbappen.
8. Välj **Nycklar** följt av **Skapa nyckel** och sedan **Spara**. Anteckna den nyckel som du kommer att använda när du konfigurerar webbappen.

## <a name="configure-the-sample"></a>Konfigurera exemplet

I den här självstudien konfigurerar du ett exempel som du kan ladda ned från GitHub. Exemplet visar hur en ensidesapplikation kan använda Azure AD B2C för användarens registrering och inloggning samt anropa ett skyddat webb-API.

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) eller klona exemplet från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

Så här ändrar du inställningarna:

1. Öppna filen `index.html` i exemplet.
2. Konfigurera exemplet med det program-ID och den nyckel som du antecknade tidigare. Ändra följande kodrader genom att ersätta värdena med namnen på din katalog och dina API:er:

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID>',
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
    };
    ```

    Namnet på det användarflöde som används i den här självstudien är **B2C_1_signupsignin1**. Om du använder ett annat namn på användarflöde ska du använda namnet på användarflöde i värdet `authority`.

## <a name="run-the-sample"></a>Kör exemplet

1. Starta en kommandotolk för Node.js.
2. Gå till den katalog som innehåller Node.js-exemplet. Till exempel `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Kör följande kommandon:

    ```
    npm install && npm update
    node server.js
    ```

    Konsolfönstret visar portnumret där appen finns.
    
    ```
    Listening on port 6420...
    ```

4. Använd en webbläsare för att gå till adressen `http://localhost:6420` och visa programmet.

Exempelappen har stöd för registrering, inloggning, redigering av profil och återställning av lösenord. Den här självstudien visar hur en användare registrerar sig med hjälp av en e-postadress.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

1. Klicka på **Logga in** för att logga in som användare av programmet. Då används användarflödet **B2C_1_signupsignin1**, som du definierade i ett tidigare steg.
2. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du inte har något konto klickar du på länken **Registrera dig**. 
3. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som definierats i användarflödet.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen. 

    ![Arbetsflöde för registrering](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Klicka på **Skapa** och skapa ett lokalt konto i Azure AD B2C-katalogen.

Användaren kan nu använda en e-postadress för att logga in och använda SPA-appen.

> [!NOTE]
> Efter inloggningen visar appen ett fel som handlar om ”otillräckliga behörigheter”. Du får felmeddelandet eftersom du försöker komma åt en resurs från demokatalogen. Eftersom ditt åtkomst-token endast är giltigt för din Azure AD-katalog är API-anropet obehörigt. Fortsätt med nästa självstudiekurs och skapa ett skyddat webb-API för din katalog.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

> [!div class="nextstepaction"]
> [Självstudier: Bevilja åtkomst till ett ASP.NET Core webb-API från en ensidesapp med hjälp av Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
