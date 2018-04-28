---
title: Självstudie – Bevilja åtkomst till ett ASP.NET webb-API från en webbapp med Azure Active Directory B2C | Microsoft Docs
description: Självstudiekurs som visar hur du använder Active Directory B2C för att skydda ett ASP.NET webb-API och anropa det från en ASP.NET-webbapp.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.author: davidmu
ms.date: 01/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: f61a3b103d8738e1b86fb64aff99dab9c6986fdf
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-from-a-web-app-using-azure-active-directory-b2c"></a>Självstudier: Bevilja åtkomst till ett ASP.NET webb-API från en webbapp med Azure Active Directory B2C

Den här självstudien lär dig anropa en webb-API-resurs som skyddas av Azure Active Directory (Azure AD) B2C från en ASP.NET-webbapp.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Registrera ett webb-API i en Azure AD B2C-klientorganisation
> * Definiera och konfigurera omfång för ett webb-API
> * Tilldela behörighet till webb-API
> * Uppdatera exempelkod och använder Azure AD B2C för att skydda ett webb-API

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* Genomför självstudiekursen [Använda Azure Active Directory B2C för användarautentisering i en ASP.NET webbapp](active-directory-b2c-tutorials-web-app.md).
* Installera [Visual Studio 2017](https://www.visualstudio.com/downloads/) med arbetsbelastningen **ASP.NET och webbutveckling**.

## <a name="register-web-api"></a>Registrera webb-API

Webb-API-resurser måste vara registrerade i klientorganisationen innan de kan godkänna och svara på en [begäran från en skyddad resurs](../active-directory/develop/active-directory-dev-glossary.md#resource-server) från [klientprogram](../active-directory/develop/active-directory-dev-glossary.md#client-application) som använder en [åtkomsttoken](../active-directory/develop/active-directory-dev-glossary.md#access-token) från Azure Active Directory. Registrering skapar [programmet och tjänstens huvudnamnsobjekt](../active-directory/develop/active-directory-dev-glossary.md#application-object) i klientorganisationen. 

Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för Azure AD B2C-klientorganisationen.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Välj **Azure AD B2C** i listan över tjänster i Azure Portal.

2. I B2C-inställningarna klickar du på **Program** och sedan på **Lägg till**.

    Registrera webb-API-exemplet i klientorganisationen med följande inställningar.
    
    ![Lägga till ett nytt API](media/active-directory-b2c-tutorials-web-api/web-api-registration.png)
    
    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Namn** | Mitt webb-API-exempel | Ange ett **Namn** som beskriver ditt webb-API för utvecklare. |
    | **Ta med webbapp/webb-API** | Ja | Välj **Ja** om det är ett webb-API. |
    | **Tillåt implicit flöde** | Ja | Välj **Ja** eftersom API:et använder [OpenID Connect-inloggning](active-directory-b2c-reference-oidc.md). |
    | **Svarswebbadress** | `https://localhost:44332` | Svarswebbadresser är slutpunkter där Azure AD B2C returnerar de token som API:et begär. I den här självstudien körs webb-API-exemplet lokalt (lokal värd) och lyssnar på port 44332. |
    | **URI för app-id** | myAPISample | URI:n identifierar API:et i klientorganisationen. Det gör att flera API:er kan registreras per klientorganisation. [Omfång](../active-directory/develop/active-directory-dev-glossary.md#scopes) styr åtkomsten till den skyddade API-resursen och definieras med URI:n för app-ID. |
    | **Inbyggd klient** | Nej | Eftersom det här är ett webb-API och inte en intern klient väljer du Nej. |
    
3. Klicka på **Skapa** för att registrera API:et.

Registrerade API visas i programlistan för Azure AD B2C-klientorganisationen. Välj webb-API i listan. Webb-API:ets egenskapsruta visas.

![Egenskaper för webb-API](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

Anteckna det **Programklients-id** som visas. Detta ID identifierar API:et och behövs när API:et konfigureras senare under självstudierna.

När webb-API:et registreras i Azure AD B2C skapas ett förtroende. Eftersom API:et är registrerat med B2C kan API:et nu lita på de B2C-åtkomsttoken det får från andra program.

## <a name="define-and-configure-scopes"></a>Definiera och konfigurera omfång

[Omfång](../active-directory/develop/active-directory-dev-glossary.md#scopes) är ett sätt att styra åtkomst till skyddade resurser. Omfång används av webb-API för att implementera omfångsbaserad åtkomststyrning. Vissa användare kan till exempel ha både läs- och skrivåtkomst medan andra bara har skrivskyddad åtkomst. I den här självstudiekursen definierar du läs- och skrivrättigheter för webb-API.

### <a name="define-scopes-for-the-web-api"></a>Definiera omfång för webb-API

Registrerade API visas i programlistan för Azure AD B2C-klientorganisationen. Välj webb-API i listan. Webb-API:ets egenskapsruta visas.

Klicka på **Publicerade områden (förhandsgranskning)**.

Konfigurera omfång för API:t genom att lägga till följande poster. 

![omfång definierade i webb-api](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| Inställning      | Föreslaget värde  | Beskrivning                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Omfång** | Hello.Read | Läsåtkomst till hello |
| **Omfång** | Hello.Write | Skrivåtkomst till hello |

Klicka på **Spara**.

De publicerade omfången kan användas för att tilldela behörighet för webb-API till ett klientprogram.

### <a name="grant-app-permissions-to-web-api"></a>Tilldela appbehörighet till webb-API

Om du vill anropa ett skyddat webb-API från en app måste du ge appen åtkomst till API:t. I den här självstudiekursen använder du webbappen i [Använda Azure Active Directory B2C för användarautentisering i en ASP.NET webbapp](active-directory-b2c-tutorials-web-app.md). 

1. I Azure Portal väljer du **Azure AD B2C** från listan med tjänster och klickar på **Program** för att visa den registrerade applistan.

2. Välj **My Sample Web App** från applistan och klicka på **API-åtkomst (förhandsgranskning)** och sedan på **Lägg till**.

3. I listrutan **Välj API** väljer du det registrerade web-API:et **My Sample Web API**.

4. I listrutan **Välj områden** väljer du de omfång du definierade vid registreringen av webb-API:t.

    ![välja omfång för app](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Klicka på **OK**.

**My Sample Web App** är registrerad för att anropa den skyddade **My Sample Web API**. En användare [autentiserar](../active-directory/develop/active-directory-dev-glossary.md#authentication) med Azure AD B2C för att använda appen. Webbappen får ett [auktoriseringsbeviljande](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant) från Azure AD B2C som ger tillgång till det skyddade webb-API:et.

## <a name="update-code"></a>Uppdatera kod

När webb-API:et är registrerat och har ett definierat omfång måste du konfigurera webb-API-koden så den använder din Azure AD B2C-klientorganisation. I den här självstudiekursen konfigurerar du ett webb-API-exempel. 

Webb-API-exemplet ingår i projektet du laddade ned i den obligatoriska föregående självstudiekursen: [Använda Azure Active Directory B2C för användarautentisering i en ASP.NET webbapp](active-directory-b2c-tutorials-web-app.md). Om du inte genomfört den obligatoriska självstudiekursen gör du det innan du går vidare.

Exempellösningen innehåller två projekt:

**Exempelwebbapp (TaskWebApp):** webbapp som skapar och redigerar en uppgiftslista. Webbappen använder **registrerings- eller inloggningsprincipen** för att registrera eller logga in användare med en e-postadress.

**Webb-API-exempelapp (TaskService):** Webb-API med stöd för att skapa, läsa, uppdatera och ta bort en uppgiftslista. Webb-API:et skyddas av Azure AD B2C och anropas av webbappen.

Exempelwebbappen och webb-API:et definierar konfigurationsvärdena som appinställningar i varje projekts Web.config-fil.

Öppna **B2C-WebAPI-DotNet**-lösningen i Visual Studio.

### <a name="configure-the-web-app"></a>Konfigurera webbappen

1. Öppna **Web.config** i projektet **TaskWebApp**.

2. Kör API:et lokalt genom att använda inställningen för lokala värden för **api:TaskServiceUrl**. Ändra Web.config enligt följande: 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Konfigurera en URI för API:et. Det här är den URI som webbappen använder för API-anrop. Ställ även in de behörigheter som krävs.

    ```C#
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/myAPISample/" />
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
    <add key="ida:ClientId" value="<The Application ID for your web API obtained from the Azure portal>"/>
    ```

4. Uppdatera principinställningarna med det namn som skapades när du skapade principerna för registrering och inloggning.

    ```C#
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_SiUpIn" />
    ```

5. Konfigurera inställningen för omfång så den överensstämmer med det du skapade i portalen.

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample"></a>Kör exemplet

Du måste köra både **TaskWebApp**- och **TaskService**-projektet. 

1. Högerklicka på lösningen i Solution Explorer och välj **Ange startprojekt...**. 
2. Du kan också markera radioknappen **Multiple startup projects** (Starta flera projekt).
3. Ändra **Åtgärd** för båda projekten till **Start**.
4. Spara ändringarna genom att klicka på OK.
5. Tryck på **F5** för att köra båda programmen. Varje program öppnas i sitt eget webbläsarfönster. `https://localhost:44316/` är webbappen.
    `https://localhost:44332/` är webb-API:t.

6. Klicka på länken registrera dig eller logga in i menyn och registrera dig för webbprogrammet. Använd kontot du skapade i [Självstudier om webbappen](active-directory-b2c-tutorials-web-app.md). 
7. När du loggat in klickar du på länken för **att göra-listan** och skapar en post i listan.

När du skapar en post i listan skickar webbappen en förfrågan till webb-API:et om att skapa listposten. Den skyddade webbappen anropar det skyddade webb-api:et i Azure AD B2C-klientorganisationen.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda Azure AD B2C-klientorganisationen om du vill prova andra självstudier för Azure AD B2C. När den inte längre behövs kan du ta bort [Azure AD B2C-klientorganisationen](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du kan skydda ett ASP.NET webb-API genom att registrera och definiera omfång i Azure AD B2C. Om du vill veta mer om detta scenario och även gå igenom koden går du vidare till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Skapa en ASP.NET webbapp med Azure Active Directory B2C och registrering, inloggning, profilredigering och återställning av lösenord](active-directory-b2c-devquickstarts-web-dotnet-susi.md)