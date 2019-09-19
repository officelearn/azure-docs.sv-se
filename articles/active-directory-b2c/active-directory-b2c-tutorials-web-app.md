---
title: Självstudie – aktivera autentisering i ett webb program – Azure Active Directory B2C
description: Självstudie som lär dig använda Azure Active Directory B2C för att tillhandahålla en användarinloggning till en ASP.NET-webbapp.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 09/19/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b42634aa86f210382adb1ae224c847a92d89109b
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103314"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Självstudier: Aktivera autentisering i en webbapp med hjälp av Azure Active Directory B2C

Den här självstudien visar hur du använder Azure Active Directory B2C (Azure AD B2C) för att logga in och registrera användare i ett ASP.NET-webbprogram. Med Azure AD B2C kan program autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med hjälp av öppna standardprotokoll.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* [Skapa användarflöden](tutorial-create-user-flows.md) för att möjliggöra användarupplevelser i programmet.
* Installera [Visual Studio 2019](https://www.visualstudio.com/downloads/) med arbets belastningen **ASP.net och webb utveckling** .

## <a name="update-the-application"></a>Uppdatera programmet

I den självstudien som du slutförde som en del av förutsättningarna lade du till en webbapp i Azure AD B2C. För att möjliggöra kommunikation med exemplet i den här självstudien behöver du lägga till en omdirigerings-URI i programmet i Azure AD B2C.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter programmet *webapp1*.
1. Under **Svars-URL** lägger du till `https://localhost:44316`.
1. Välj **Spara**.
1. På egenskapssidan antecknar du det program-ID som du kommer att använda när du konfigurerar webbappen.
1. Välj **Nycklar** följt av **Skapa nyckel** och sedan **Spara**. Anteckna den nyckel som du kommer att använda när du konfigurerar webbappen.

## <a name="configure-the-sample"></a>Konfigurera exemplet

I den här självstudien konfigurerar du ett exempel som du kan ladda ned från GitHub. Exemplet använder ASP.NET för att tillhandahålla en enkel att göra-lista. I exemplet används [Microsoft OWIN-mellanprogramkomponenter](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) eller klona exemplet från GitHub. Se till att extrahera exempelfilen i en mapp där sökvägens totala teckenlängd är mindre än 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Följande två projekt finns i exempellösningen:

* **TaskWebApp** – Skapa och redigera en uppgiftslista. Exemplet använder användar flödet för **registrering eller inloggning** för att registrera dig och logga in användare.
* **TaskService** – Har stöd för att skapa, läsa, uppdatera och ta bort funktionen för uppgiftslistor. API:et skyddas av Azure AD B2C och anropas av TaskWebApp.

Du ändrar exemplet till att använda det program som är registrerat i din klientorganisation, vilket innefattar det program-ID och den nyckel som du tidigare registrerade. Du konfigurerar även de användarflöden som du skapade. Exemplet definierar konfigurations värden som inställningar i filen *Web. config* .

Uppdatera inställningarna i Web. config-filen så att de fungerar med ditt användar flöde:

1. Öppna **B2C-WebAPI-DotNet**-lösningen i Visual Studio.
1. I projektet **TaskWebApp** öppnar du **Web.config**-filen.
    1. Uppdatera värdet för `ida:Tenant` och `ida:AadInstance` med namnet på Azure AD B2C klienten som du skapade. Ersätt `fabrikamb2c` till exempel med `contoso`.
    1. Ersätt värdet för `ida:ClientId` med det program-ID som du har spelat in.
    1. Ersätt värdet för `ida:ClientSecret` med den nyckel som du registrerade. Du måste använda XML-koda klient hemligheten innan du lägger till den i Web. config.
    1. Ersätt värdet för `ida:SignUpSignInPolicyId` med `b2c_1_signupsignin1`.
    1. Ersätt värdet för `ida:EditProfilePolicyId` med `b2c_1_profileediting1`.
    1. Ersätt värdet för `ida:ResetPasswordPolicyId` med `b2c_1_passwordreset1`.

## <a name="run-the-sample"></a>Kör exemplet

1. I Solution Explorer högerklickar du på projektet **TaskWebApp** och klickar sedan på **Set as StartUp Project** (Konfigurera som StartUp-projekt).
1. Tryck på **F5**. Standardwebbläsaren öppnas med den lokala webbplatsadressen `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

1. Välj **Registrera dig/logga** in för att registrera dig som en användare av programmet. Användarflödet **b2c_1_signupsignin1** används.
1. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du inte har något konto än väljer du **Registrera dig nu**. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som definierats i användarflödet.
1. Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen.

    ![Registrerings sidan visas som en del av inloggnings-och registrerings arbets flödet](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.PNG)

1. Välj **skapa** för att skapa ett lokalt konto i Azure AD B2C klient organisationen.

Program användaren kan nu använda sin e-postadress för att logga in och använda webb programmet.

Men **att göra-lista-** funktionen fungerar inte förrän du har slutfört nästa självstudie i serien, [Självstudier: Använd Azure AD B2C för att skydda ett ASP.NET webb](active-directory-b2c-tutorials-web-api.md)-API.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

Gå vidare till nästa självstudie för att aktivera funktionen **att göra-listan** i webb programmet. I den registrerar du ett webb-API-program i din egen Azure AD B2C klient och ändrar sedan kod exemplet så att det använder klienten för API-autentisering.

> [!div class="nextstepaction"]
> [Självstudier: Använd Azure Active Directory B2C för att skydda ett ASP.NET-webb-API >](active-directory-b2c-tutorials-web-api.md)
