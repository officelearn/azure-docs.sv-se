---
title: 'Självstudiekurs: Bevilja åtkomst till ett Node.js-webb-API från ett skrivbordsprogram'
description: Självstudiekurs om hur du använder Active Directory B2C för att skydda ett nod.js-webb-API och anropa det från en .NET-skrivbordsapp.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 59670cda68f54e4c0b20b361f0688e6766acba61
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183402"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Självstudier: Bevilja åtkomst till ett Node.js-webb-API från en skrivbordsapp med Azure Active Directory B2C

Den här självstudien visar hur du anropar ett Node.js-webb-API som skyddas av Azure Active Directory B2C (Azure AD B2C) från en WPF-skrivbordsapp (Windows Presentation Foundation), som också skyddas av Azure AD B2C.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Ge behörigheter till webb-API:t
> * Uppdatera exemplet så att programmet används

## <a name="prerequisites"></a>Krav

Slutför stegen och förutsättningarna i [självstudiekurs: Autentisera användare i en inbyggd skrivbordsklient](tutorial-desktop-app.md).

## <a name="add-a-web-api-application"></a>Lägga till ett program för webb-API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Vissa användare kan till exempel ha både läs- och skrivåtkomst medan andra bara har skrivskyddad åtkomst. I den här självstudiekursen definierar du läs- och skrivrättigheter för webb-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Registrera värdet under **SCOPES** som `demo.read` scopet ska använda i ett senare steg när du konfigurerar skrivbordsprogrammet. Det fullständiga scopevärdet `https://contosob2c.onmicrosoft.com/api/demo.read`liknar .

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett inbyggt klientprogram måste du bevilja de registrerade ursprungliga klientprogrambehörigheterna till webb-API:et som du registrerade i Azure AD B2C.

I den nödvändiga självstudien registrerade du ett inbyggt klientprogram med namnet *nativeapp1*. Följande steg konfigurerar den inbyggda programregistreringen med de API-scope som du har exponerat för *webapi1* i föregående avsnitt. Detta gör det möjligt för skrivbordsprogrammet att hämta en åtkomsttoken från Azure AD B2C som webb-API:et kan använda för att verifiera och ge begränsad åtkomst till sina resurser. Du konfigurerar och kör både skrivbordsprogrammet och webb-API-kodexemplen senare i självstudien.

#### <a name="applications"></a>[Program](#tab/applications/)

1. Välj **Program** och sedan *nativeapp1*.
1. Välj **API-åtkomst** och därefter **Lägg till**.
1. I listrutan **Välj API** väljer du *webapi1*.
1. I listrutan **Välj scope** väljer du de scope som du definierade tidigare. Till exempel *demo.read* och *demo.write*.
1. Välj **OK**.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Välj **Appregistreringar (förhandsversion)** och välj sedan det inbyggda klientprogrammet som ska ha åtkomst till API:et. Till exempel *nativeapp1*.
1. Under **Hantera**väljer du **API-behörigheter**.
1. Under **Konfigurerade behörigheter**väljer du **Lägg till en behörighet**.
1. Välj fliken **Mina API:er.**
1. Välj det API som det inbyggda klientprogrammet ska beviljas åtkomst till. Till exempel *webapi1*.
1. Expandera **demo**under **Behörighet**och välj sedan de scope som du definierade tidigare. Till exempel *demo.read* och *demo.write*.
1. Välj **Lägg till behörigheter**. Enligt anvisningarna väntar du några minuter innan du fortsätter till nästa steg.
1. Välj **Bevilja administratörsmedgivande för (ditt klientnamn)**.
1. Välj ditt inloggade administratörskonto eller logga in med ett konto i din Azure AD B2C-klient som har tilldelats åtminstone *rollen cloud application administrator.*
1. Välj **Acceptera**.
1. Välj **Uppdatera**och kontrollera sedan att "Beviljad för ..." visas under **Status** för båda scopea. Det kan ta några minuter innan behörigheterna sprids.

* * *

En användare autentiserar med Azure AD B2C för att använda WPF-skrivbordsappen. Skrivbordsappen får ett auktoriseringsbeviljande från Azure AD B2C som ger tillgång till det skyddade webb-API:t.

## <a name="configure-the-samples"></a>Konfigurera exemplen

Nu när webb-API:et är registrerat och du har konfigurerat scope och behörigheter konfigurerar du skrivbordsprogrammet och webb-API-exemplen för att använda din Azure AD B2C-klient.

### <a name="update-the-desktop-application"></a>Uppdatera skrivbordsprogrammet

I en förutsättning för den här artikeln har du ändrat ett [WPF-skrivbordsprogram](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) för att aktivera inloggning med ett användarflöde i din Azure AD B2C-klientorganisation. I det här avsnittet uppdaterar du samma program för att referera till webb-API:et som du registrerade tidigare, *webapi1*.

1. Öppna **active-directory-b2c-wpf-lösningen** (`active-directory-b2c-wpf.sln`) i Visual Studio.
1. Öppna **App.xaml.cs-filen i active-directory-b2c-wpf-projektet** och hitta följande variabla definitioner. *App.xaml.cs*
    1. Ersätt variabelns `ApiScopes` värde med det värde som du spelade in tidigare när du definierade **scopet demo.read.**
    1. Ersätt värdet för `ApiEndpoint` variabeln med den **Redirect URI** som du spelade in tidigare när du registrerade webb-API:et (till exempel *webapi1)* i din klientorganisation.

    Här är ett exempel:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Hämta och uppdatera API-exemplet node.js

Hämta sedan exemplet node.js webb-API-kod från GitHub och konfigurera det för att använda webb-API:et som du registrerade i din Azure AD B2C-klient.

[Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) eller klona exempelwebbappen från GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Exempelwebb-API:et för Node.js använder Passport.js-biblioteket för att aktivera Azure AD B2C att skydda anrop till API: et.

1. Öppna filen `index.js`.
1. Uppdatera dessa variabla definitioner med följande värden. Ändra `<web-API-application-ID>` till **program-ID :t för** webb-API:et som du registrerade tidigare (*webapi1*). Ändra `<your-b2c-tenant>` till namnet på din Azure AD B2C-klientorganisation.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Eftersom du kör API:et lokalt uppdaterar du sökvägen i `/` flödet för GET-metoden `/hello`till i stället för demoappens plats för:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Kör exemplen

### <a name="run-the-nodejs-web-api"></a>Köra webb-API:et nod.js

1. Starta en kommandotolk för Node.js.
2. Gå till den katalog som innehåller Node.js-exemplet. Till exempel `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Kör följande kommandon:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Kör skrivbordsappen

1. Öppna lösningen **active-directory-b2c-wpf** i Visual Studio.
2. Tryck på **F5** för att köra skrivbordsappen.
3. Logga in med e-postadressen och lösenordet som skapades i [självstudien för autentisering av användare med Azure Active Directory B2C i en skrivbordsapp](tutorial-desktop-app.md).
4. Välj knappen **Anropa API.**

Skrivbordsprogrammet gör en begäran till det lokalt körande webb-API:et och visar den inloggade användarens visningsnamn när du har verifierat en giltig åtkomsttoken.

![Visningsnamn som visas i det övre fönstret i WPF-skrivbordsprogrammet](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

Ditt skrivbordsprogram, skyddat av Azure AD B2C, anropar det lokalt körande webb-API:et som också skyddas av Azure AD B2C.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Ge behörigheter till webb-API:t
> * Uppdatera exemplet så att programmet används

> [!div class="nextstepaction"]
> [Självstudiekurs: Lägga till identitetsleverantörer i dina program i Azure Active Directory B2C](tutorial-add-identity-providers.md)
