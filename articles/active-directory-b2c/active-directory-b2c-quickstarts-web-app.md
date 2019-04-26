---
title: Snabbstart – Konfigurera inloggning för ett ASP.NET-program med Azure Active Directory B2C | Microsoft Docs
description: Kör en ASP.NET-exempelwebbapp som använder Azure Active Directory B2C för användarinloggningen.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: f8fc57d0a01845aeea2cb423237b7e27fe769b6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317809"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Snabbstart: Konfigurera inloggning för ett ASP.NET-program med hjälp av Azure Active Directory B2C

Azure Active Directory (AD Azure) B2C tillhandahåller identitetshantering i molnet för att skydda dina program, ditt företag och dina kunder. Med Azure AD B2C kan program autentisera med konton på sociala medier och företagskonton med öppna protokoll. I den här snabbstarten använder du ett ASP.NET-program för att logga in med en social identitetsprovider och anropa en Azure AD B2C-skyddad webb-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Visual Studio 2017](https://www.visualstudio.com/downloads/) med arbetsbelastningen **ASP.NET och webbutveckling**. 
- Ett konto från ett socialt medium, till exempel Facebook, Google, Microsoft eller Twitter.
- [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) eller klona exempelwebbprogrammet från GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    De här två projekten finns i exempellösningen:

    - **TaskWebApp** – Ett webbprogram som skapar och redigerar en uppgiftslista. Webbprogrammet använder användarflödet för **registrering eller inloggning** för att registrera eller logga in användare.
    - **TaskService** – Ett webb-API med stöd för att skapa, läsa, uppdatera och ta bort en uppgiftslista. Webb-API:n skyddas av Azure AD B2C och anropas av webbprogrammet.

## <a name="run-the-application-in-visual-studio"></a>Kör programmet i Visual Studio

1. I exempelprogrammets projektmapp öppnar du lösningen **B2C-WebAPI-DotNet.sln** i Visual Studio.
2. För den här snabbstarten kör du både **TaskWebApp**- och **TaskService**-projektet samtidigt. Högerklicka på lösningen **B2C-WebAPI-DotNet** i Solution Explorer och välj sedan **Ange startprojekt**. 
3. Välj **Flera startprojekt** och ändra **Åtgärd** för båda projekten till **Start**. 
4. Klicka på **OK**.
5. Tryck på **F5** för att felsöka båda programmen. Varje program öppnas i ett eget webbläsarfönster:

    - `https://localhost:44316/` – ASP.NET-webbprogrammet. Du kan interagera direkt med det här programmet i snabbstarten.
    - `https://localhost:44332/` – Webb-API som anropas av ASP.NET-webbprogrammet.

## <a name="sign-in-using-your-account"></a>Logga in på ditt konto

1. Klicka på **Registrera dig/Logga in** i ASP.NET-webbprogrammet för att starta arbetsflödet.

    ![Testa en ASP.NET-webbapp](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

    Exemplet stöder flera registreringsalternativ, till exempel att använda en social identitetsprovider eller att skapa ett lokalt konto med en e-postadress. För den här snabbstarten använder du ett konto från ett socialt medium, till exempel Facebook, Google, Microsoft eller Twitter.

2. Azure AD B2C visar en anpassad inloggningssida för ett fiktivt varumärke som kallas Wingtip Toys för exempelwebbprogrammet. Klicka på knappen för den identitetsprovider som du vill använda för att registrera dig med en social identitetsprovider.

    ![Inloggnings- eller registreringsprovider](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

    Du autentiserar dig (loggar in) med autentiseringsuppgifterna för ditt sociala konto och ger behörighet till programmet att läsa information från det sociala kontot. När du beviljar åtkomst kan programmet hämta profilinformation från det sociala kontot, till exempel ditt namn och din ort. 

3. Avsluta inloggningsprocessen för identitetsprovidern.

## <a name="edit-your-profile"></a>Redigera din profil

Azure Active Directory B2C tillhandahåller funktioner som gör det möjligt för användare att uppdatera sina profiler. Exempelwebbappen använder ett Azure AD B2C-användarflöde för att redigera profiler för arbetsflödet. 

1. Klicka på ditt profilnamn på programmets menyrad och välj **Redigera profil** för att redigera profilen som du har skapat.

    ![Redigera profil](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

2. Ändra **Visningsnamn** eller **Ort** och klicka sedan på **Fortsätt** för att uppdatera din profil. 

    Den ändrade visas uppe till höger på webbprogrammets startsida.

## <a name="access-a-protected-api-resource"></a>Få åtkomst till en skyddad API-resurs

1. Klicka på **att göra-listan** för att redigera objekten på att göra-listan. 

2. Ange text i textrutan **Nytt objekt**. Klicka på **Lägg till** för att anropa det Azure AD B2C-skyddade webb-API:et som lägger till ett objekt i att göra-listan.

    ![Lägg till ett objekt i en att göra-lista](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

    ASP.NET-webbtillämpningsprogrammet innehåller en åtkomsttoken för Azure AD i begäran till den skyddade web API-resursen som utför åtgärder på användarnas objekt för att göra-listor.

Du har använt ditt Azure AD B2C-användarkonto för att utföra ett auktoriserat anrop till ett Azure AD B2C-skyddat webb-API.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda Azure AD B2C-klientorganisationen om du vill prova andra snabbstarter eller självstudier för Azure AD B2C. När den inte längre behövs kan du ta bort [Azure AD B2C-klientorganisationen](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du ett ASP.NET-exempelprogram för att logga in med en anpassad inloggningssida, logga in med en social identitetsprovider, skapa ett Azure AD B2C-konto och anropa ett Azure AD B2C-skyddat webb-API. 

Kom igång med att skapa en egen Azure AD B2C-klientorganisation.

> [!div class="nextstepaction"]
> [Skapa en Azure Active Directory B2C-klientorganisation i Azure-portalen](tutorial-create-tenant.md)
