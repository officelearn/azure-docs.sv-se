---
title: Självstudie – Bevilja åtkomst till ett ASP.NET webb-API – Azure Active Directory B2C | Microsoft Docs
description: Självstudie om hur du använder Active Directory B2C för att skydda ett ASP.NET webb-API och anropa det från en ASP.NET-webbapp.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 77e3eaeffba862c727e021427e5f27967fcf35bd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687986"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Självstudier: Bevilja åtkomst till ett ASP.NET webb-API med hjälp av Azure Active Directory B2C

Den här självstudien lär dig anropa en skyddad webb-API-resurs i Azure Active Directory (Azure AD) B2C från en ASP.NET-webbapp.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Tilldela behörigheter till webb-API:et
> * Konfigurera exemplet för att använda programmet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Slutför stegen och kraven i [Självstudie: Aktivera autentisering i en webbapp med hjälp av Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md).

## <a name="add-a-web-api-application"></a>Lägga till ett program för webb-API

Webb-API-resurser måste vara registrerade i klientorganisationen innan de kan godkänna och svara på en begäran från en skyddad resurs från klientprogram som använder en åtkomsttoken.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **Program** och därefter **Lägg till**.
5. Ange ett namn på programmet. Till exempel *webapi1*.
6. För **Inkludera webbapp/webb-API** och **Tillåt implicit flöde** väljer du **Ja**.
7. För **Svars-URL** anger du en slutpunkt dit Azure AD B2C ska returnera de token som programmet begär. I den här självstudien körs exemplet lokalt och lyssnar på `https://localhost:44332`.
8. För **URI för app-ID** anger du den identifierare som används för webb-API:t. Den fullständiga URI-identifieraren inklusive domänen skapas åt dig. Till exempel `https://contosotenant.onmicrosoft.com/api`.
9. Klicka på **Skapa**.
10. På egenskapssidan antecknar du program-ID:t du kommer att använda när du konfigurerar webbappen.

## <a name="configure-scopes"></a>Konfigurera omfång

Omfång är ett sätt att styra åtkomsten till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Till exempel kan användare av webb-API:et ha både läs- och skrivbehörighet, eller så har användare av webb-API:et kanske bara läsbehörighet. I den här självstudiekursen använder du omfång för att definiera läs- och skrivrättigheter för webb-API.

1. Välj **Program** och därefter *webapi1*.
2. Välj **Publicerade omfång**.
3. Som **omfång** anger du `Hello.Read` och som beskrivning anger du `Read access to hello`.
4. Som **omfång** anger du `Hello.Write` och som beskrivning anger du `Write access to hello`.
5. Klicka på **Spara**.

De publicerade omfången kan användas för att ge ett klientprogram behörighet till webb-API:et.

## <a name="grant-permissions"></a>Bevilja behörigheter

Om du vill anropa ett skyddat webb-API från ett program måste du ge programmet åtkomst till API:et. I den obligatoriska föregående självstudien skapade du en webbapp i Azure AD B2C med namnet *webapp1*. Du använder det programmet för att anropa webb-API:et.

1. Välj **Program** och därefter *webapp1*.
2. Välj **API-åtkomst** och därefter **Lägg till**.
3. I listrutan **Välj API** väljer du *webapi1*.
4. I listrutan **Välj reservationsomfång** väljer du omfången **Hello.Read** och **Hello.Write** som du definierade tidigare.
5. Klicka på **OK**.

Programmet har registrerats för att anropa det skyddade webb-API:et. En användare autentiserar med Azure AD B2C för att använda programmet. Programmet får ett auktoriseringsbeviljande från Azure AD B2C som ger tillgång till det skyddade webb-API:et.

## <a name="configure-the-sample"></a>Konfigurera exemplet

När webb-API:et är registrerat och har ett definierat omfång konfigurerar du webb-API:et så det använder din Azure AD B2C-klientorganisation. I den här självstudiekursen konfigurerar du ett webb-API-exempel. Exempelwebb-API:et ingår i det projekt som du laddade ned i den obligatoriska föregående självstudien.

Exempellösningen innehåller två projekt:

Följande två projekt finns i exempellösningen:

- **TaskWebApp** – Skapa och redigera en uppgiftslista. Exemplet använder användarflödet för **registrering eller inloggning** för att registrera eller logga in användare.
- **TaskService** – Har stöd för att skapa, läsa, uppdatera och ta bort funktionen för uppgiftslistor. API:et skyddas av Azure AD B2C och anropas av TaskWebApp.

### <a name="configure-the-web-application"></a>Konfigurera webbappen

1. Öppna **B2C-WebAPI-DotNet**-lösningen i Visual Studio.
2. Öppna **Web.config** i projektet **TaskWebApp**.
3. Kör API:et lokalt genom att använda inställningen för lokala värden för **api:TaskServiceUrl**. Ändra Web.config enligt följande: 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Konfigurera en URI för API:et. Det här är den URI som webbappen använder för att göra API-begäran. Ställ även in de behörigheter som krävs.

    ```C#
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

### <a name="configure-the-web-api"></a>Konfigurera webb-API

1. Öppna **Web.config** i projektet **TaskService**.
2. Ställ in API:t så det använder din klientorganisation.

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Ställ in klient-ID till det registrerade program-ID:t för ditt API.

    ```C#
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

4. Uppdatera inställningarna för användarflöde namnet för användarflödet för registrering och inloggning.

    ```C#
    <add key="ida:SignUpSignInUserFlowId" value="B2C_1_signupsignin1" />
    ```

5. Konfigurera inställningen för omfång så den överensstämmer med det du skapade i portalen.

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>Kör exemplet

Du måste köra både **TaskWebApp**- och **TaskService**-projektet. 

1. Högerklicka på lösningen i Solution Explorer och välj **Ange startprojekt...**. 
2. Välj **Flera startprojekt**.
3. Ändra **Åtgärd** för båda projekten till **Start**.
4. Spara ändringarna genom att klicka på **OK**.
5. Tryck på **F5** för att köra båda programmen. Varje program öppnas i sitt eget webbläsarfönster. `https://localhost:44316/` är webbappen.
    `https://localhost:44332/` är webb-API:t.

6. I webbappen klickar du på **registrera/logga in** för att logga in i webbappen. Använd det konto som du skapade tidigare. 
7. När du loggat in klickar du på **att göra-listan** och skapar en post i listan.

När du skapar en post i listan skickar webbappen en begäran till webb-API:et om att skapa listposten. Den skyddade webbappen anropar det skyddade webb-API:et i din Azure AD B2C-klientorganisation.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till ett program för webb-API
> * Konfigurera omfånget för ett webb-API
> * Tilldela behörigheter till webb-API:et
> * Konfigurera exemplet för att använda programmet

> [!div class="nextstepaction"]
> [Självstudie: Lägga till identitetsproviders i program i Azure Active Directory B2C](tutorial-add-identity-providers.md)
