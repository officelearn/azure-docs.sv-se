---
title: 'Självstudiekurs: Aktivera autentisering i ett webbprogram'
titleSuffix: Azure AD B2C
description: Självstudie som lär dig använda Azure Active Directory B2C för att tillhandahålla en användarinloggning till en ASP.NET-webbapp.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e4b56f18bf8a2ed1c22b00b8a57efdbf06eb7fa2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183342"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Självstudiekurs: Aktivera autentisering i ett webbprogram med Azure Active Directory B2C

Den här självstudien visar hur du använder Azure Active Directory B2C (Azure AD B2C) för att logga in och registrera användare i ett ASP.NET webbprogram. Med Azure AD B2C kan program autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med hjälp av öppna standardprotokoll.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

* [Skapa användarflöden](tutorial-create-user-flows.md) för att möjliggöra användarupplevelser i programmet.
* Installera [Visual Studio 2019](https://www.visualstudio.com/downloads/) med **arbetsbelastningen ASP.NET och webbutveckling.**

## <a name="update-the-application-registration"></a>Uppdatera programregistreringen

I självstudien som du slutförde som en del av förutsättningarna registrerade du ett webbprogram i Azure AD B2C. Om du vill aktivera kommunikation med exemplet i den här självstudien måste du lägga till en omdirigerings-URI och skapa en klienthemlighet (nyckel) för det registrerade programmet.

### <a name="add-a-redirect-uri-reply-url"></a>Lägga till en omdirigerings-URI (svars-URL)

Du kan använda den aktuella **programupplevelsen** eller vår nya enhetliga **appregistreringar (förhandsversion)** för att uppdatera programmet. [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter programmet *webapp1*.
1. Under **Svars-URL** lägger du till `https://localhost:44316`.
1. Välj **Spara**.
1. På egenskapssidan registrerar du program-ID:n för användning i ett senare steg när du konfigurerar webbprogrammet.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Välj **Appregistreringar (förhandsgranskning)**, välj fliken **Ägda program** och välj sedan *webapp1-programmet.*
1. Välj **Autentisering**och välj sedan **Prova den nya upplevelsen** (om den visas).
1. Under **Webben**väljer du länken Lägg `https://localhost:44316`till **URI,** anger och väljer sedan **Spara**.
1. Välj **Översikt**.
1. Registrera **program-ID:t (klient)** för användning i ett senare steg när du konfigurerar webbprogrammet.

* * *

### <a name="create-a-client-secret"></a>Skapa en klienthemlighet

Skapa sedan en klienthemlighet för det registrerade webbprogrammet. Webbprogramkoden använder detta för att bevisa sin identitet när token begärs.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>Konfigurera exemplet

I den här självstudien konfigurerar du ett exempel som du kan ladda ned från GitHub. Exemplet använder ASP.NET för att tillhandahålla en enkel att göra-lista. I exemplet används [Microsoft OWIN-mellanprogramkomponenter](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) eller klona exemplet från GitHub. Se till att extrahera exempelfilen i en mapp där sökvägens totala teckenlängd är mindre än 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Följande två projekt finns i exempellösningen:

* **TaskWebApp** – Skapa och redigera en uppgiftslista. I exemplet används användarflödet **för registrering eller inloggning** för att registrera och logga in användare.
* **TaskService** – Har stöd för att skapa, läsa, uppdatera och ta bort funktionen för uppgiftslistor. API:et skyddas av Azure AD B2C och anropas av TaskWebApp.

Du ändrar exemplet till att använda det program som är registrerat i din klientorganisation, vilket innefattar det program-ID och den nyckel som du tidigare registrerade. Du konfigurerar även de användarflöden som du skapade. Exemplet definierar konfigurationsvärdena som inställningar i *filen Web.config.*

Uppdatera inställningarna i filen Web.config så att de fungerar med ditt användarflöde:

1. Öppna **B2C-WebAPI-DotNet**-lösningen i Visual Studio.
1. I projektet **TaskWebApp** öppnar du **Web.config**-filen.
    1. Uppdatera värdet `ida:Tenant` för `ida:AadInstance` och med namnet på Azure AD B2C-klienten som du skapade. Ersätt till `fabrikamb2c` exempel `contoso`med .
    1. Ersätt värdet `ida:ClientId` för med det program-ID som du spelade in.
    1. Ersätt värdet för `ida:ClientSecret` med den nyckel som du registrerade. Om klienthemligheten innehåller en fördefinierad XML-entitet, till exempel mindre än`<`( ), större än (`>`), et-tecken (`&`) eller dubbel offert (`"`) måste du undvika dessa tecken genom att XML-kodning av klienthemligheten innan du lägger till den på din Web.config.
    1. Ersätt värdet `ida:SignUpSignInPolicyId` för `b2c_1_signupsignin1`med .
    1. Ersätt värdet `ida:EditProfilePolicyId` för `b2c_1_profileediting1`med .
    1. Ersätt värdet `ida:ResetPasswordPolicyId` för `b2c_1_passwordreset1`med .

## <a name="run-the-sample"></a>Kör exemplet

1. I Solution Explorer högerklickar du på projektet **TaskWebApp** och klickar sedan på **Set as StartUp Project** (Konfigurera som StartUp-projekt).
1. Tryck på **F5**. Standardwebbläsaren öppnas med den lokala webbplatsadressen `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

1. Välj **Registrera dig / Logga in** för att registrera dig som användare av programmet. Användarflödet **b2c_1_signupsignin1** används.
1. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du inte har något konto än väljer du **Registrera dig nu**. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som definierats i användarflödet.
1. Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen.

    ![Registreringssida som visas som en del av arbetsflödet för inloggning/registrering](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. Välj **Skapa** för att skapa ett lokalt konto i Azure AD B2C-klienten.

Programanvändaren kan nu använda sin e-postadress för att logga in och använda webbprogrammet.

**Att göra-listan** fungerar dock inte förrän du har slutfört nästa självstudiekurs i serien, [Självstudiekurs: Använd Azure AD B2C för att skydda ett ASP.NET webb-API](tutorial-web-api-dotnet.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

Gå nu vidare till nästa handledning för att aktivera **att göra-listan** funktionen i webbprogrammet. I den registrerar du ett webb-API-program i din egen Azure AD B2C-klient och ändrar sedan kodexemplet för att använda din klient för API-autentisering.

> [!div class="nextstepaction"]
> [Självstudiekurs: Använda Azure Active Directory B2C för att skydda ett ASP.NET webb-API >](tutorial-web-api-dotnet.md)
