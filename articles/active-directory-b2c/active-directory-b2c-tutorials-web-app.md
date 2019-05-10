---
title: Självstudie – Aktivera autentisering i en webbapp – Azure Active Directory B2C | Microsoft Docs
description: Självstudie som lär dig använda Azure Active Directory B2C för att tillhandahålla en användarinloggning till en ASP.NET-webbapp.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: fc7f198299460faaf5e5df62fe4e167a3d595486
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190350"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Självstudier: Aktivera autentisering i en webbapp med hjälp av Azure Active Directory B2C

Den här självstudien lär dig att använda Azure Active Directory (Azure AD) B2C för att logga in och registrera användare i en ASP.NET-webbapp. Med Azure AD B2C kan program autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med hjälp av öppna standardprotokoll.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Skapa användarflöden](tutorial-create-user-flows.md) för att möjliggöra användarupplevelser i programmet. 
- Installera [Visual Studio 2019](https://www.visualstudio.com/downloads/) med den **ASP.NET och webbutveckling** arbetsbelastning.

## <a name="update-the-application"></a>Uppdatera programmet

I den självstudien som du slutförde som en del av förutsättningarna lade du till en webbapp i Azure AD B2C. För att möjliggöra kommunikation med exemplet i den här självstudien behöver du lägga till en omdirigerings-URI i programmet i Azure AD B2C.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **Program** och därefter programmet *webapp1*.
5. Under **Svars-URL** lägger du till `https://localhost:44316`.
6. Välj **Spara**.
7. På egenskapssidan antecknar du det program-ID som du kommer att använda när du konfigurerar webbappen.
8. Välj **Nycklar** följt av **Skapa nyckel** och sedan **Spara**. Anteckna den nyckel som du kommer att använda när du konfigurerar webbappen.

## <a name="configure-the-sample"></a>Konfigurera exemplet

I den här självstudien konfigurerar du ett exempel som du kan ladda ned från GitHub. Exemplet använder ASP.NET för att tillhandahålla en enkel att göra-lista. I exemplet används [Microsoft OWIN-mellanprogramkomponenter](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) eller klona exemplet från GitHub. Se till att extrahera exempelfilen i en mapp där sökvägens totala teckenlängd är mindre än 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Följande två projekt finns i exempellösningen:

- **TaskWebApp** – Skapa och redigera en uppgiftslista. Exemplet använder användarflödet för **registrering eller inloggning** för att registrera eller logga in användare.
- **TaskService** – Har stöd för att skapa, läsa, uppdatera och ta bort funktionen för uppgiftslistor. API:et skyddas av Azure AD B2C och anropas av TaskWebApp.

Du ändrar exemplet till att använda det program som är registrerat i din klientorganisation, vilket innefattar det program-ID och den nyckel som du tidigare registrerade. Du konfigurerar även de användarflöden som du skapade. Exemplet definierar konfigurationsvärdena som inställningar i Web.config-filen. Så här ändrar du inställningarna:

1. Öppna **B2C-WebAPI-DotNet**-lösningen i Visual Studio.
2. I projektet **TaskWebApp** öppnar du **Web.config**-filen. Ersätt värdet för `ida:Tenant` med namnet på den klientorganisation som du skapade. Ersätt värdet för `ida:ClientId` med det program-ID som du registrerade. Ersätt värdet för `ida:ClientSecret` med den nyckel som du registrerade.
3. I filen **Web.config** ersätter du värdet för `ida:SignUpSignInPolicyId` med `b2c_1_signupsignin1`. Ersätt värdet för `ida:EditProfilePolicyId` med `b2c_1_profileediting1`. Ersätt värdet för `ida:ResetPasswordPolicyId` med `b2c_1_passwordreset1`.

## <a name="run-the-sample"></a>Kör exemplet

1. I Solution Explorer högerklickar du på projektet **TaskWebApp** och klickar sedan på **Set as StartUp Project** (Konfigurera som StartUp-projekt).
2. Tryck på **F5**. Standardwebbläsaren öppnas med den lokala webbplatsadressen `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

1. Klicka på **Registrera/Logga in** för att registrera dig som användare av programmet. Användarflödet **b2c_1_signupsignin1** används.
2. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du inte har något konto än väljer du **Registrera dig nu**. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som definierats i användarflödet.
3. Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen. 

    ![Arbetsflöde för registrering](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Klicka på **Skapa** och skapa ett lokalt konto i Azure AD B2C-klientorganisationen.

Användaren kan nu använda e-postadressen för att logga in och använda webbappen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Uppdatera programmet i Azure AD B2C
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

> [!div class="nextstepaction"]
> [Självstudie: Använda Azure Active Directory B2C för att skydda ett ASP.NET webb-API](active-directory-b2c-tutorials-web-api.md)
