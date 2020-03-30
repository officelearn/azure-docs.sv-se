---
title: 'Snabbstart: Konfigurera inloggning för en ASP.NET webbapp'
titleSuffix: Azure AD B2C
description: I den här snabbstarten kör du ett exempel ASP.NET webbapp som använder Azure Active Directory B2C för att tillhandahålla kontosignering.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 197155650bf7b8d113f96dbcfd6d9e2b58924b7f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78187210"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Snabbstart: Konfigurera inloggning för ett ASP.NET program med Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) tillhandahåller molnidentitetshantering för att skydda ditt program, företag och kunder. Med Azure AD B2C kan program autentisera med konton på sociala medier och företagskonton med öppna protokoll. I den här snabbstarten använder du ett ASP.NET-program för att logga in med en social identitetsprovider och anropa en Azure AD B2C-skyddad webb-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) med **arbetsbelastningen ASP.NET och webbutveckling.**
- Ett socialt konto från Facebook, Google eller Microsoft.
- [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) eller klona exempelwebbprogrammet från GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    Det finns två projekt i urvalslösningen:

    - **TaskWebApp** – Ett webbprogram som skapar och redigerar en uppgiftslista. Webbprogrammet använder användarflödet **för registrering eller inloggning** för att registrera eller logga in användare.
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

    ![Exempel ASP.NET webbapp i webbläsaren med registrerings-/anmälningslänken markerad](./media/quickstart-web-app-dotnet/web-app-sign-in.png)

    Exemplet stöder flera registreringsalternativ, till exempel att använda en social identitetsprovider eller att skapa ett lokalt konto med en e-postadress. För den här snabbstarten använder du ett konto för en social identitetsleverantör från antingen Facebook, Google eller Microsoft.

2. Azure AD B2C visar en inloggningssida för ett fiktivt företag som heter Fabrikam för exempelwebbprogrammet. Klicka på knappen för den identitetsprovider som du vill använda för att registrera dig med en social identitetsprovider.

    ![Inloggnings- eller registreringssida med knappar för identitetsprovider](./media/quickstart-web-app-dotnet/sign-in-or-sign-up-web.png)

    Du autentiserar (loggar in) med dina autentiseringsuppgifter för sociala konton och godkänner att programmet läser information från ditt sociala konto. När du beviljar åtkomst kan programmet hämta profilinformation från det sociala kontot, till exempel ditt namn och din ort.

3. Avsluta inloggningsprocessen för identitetsprovidern.

## <a name="edit-your-profile"></a>Redigera din profil

Azure Active Directory B2C tillhandahåller funktioner som gör det möjligt för användare att uppdatera sina profiler. Exempelwebbappen använder ett Azure AD B2C-användarflöde för att redigera profiler för arbetsflödet.

1. Klicka på ditt profilnamn på programmets menyrad och välj **Redigera profil** för att redigera profilen som du har skapat.

    ![Exempel på webbapp i webbläsaren med länken Redigera profil markerad](./media/quickstart-web-app-dotnet/edit-profile-web.png)

2. Ändra **Visningsnamn** eller **Ort** och klicka sedan på **Fortsätt** för att uppdatera din profil.

    Den ändrade visas uppe till höger på webbprogrammets startsida.

## <a name="access-a-protected-api-resource"></a>Få åtkomst till en skyddad API-resurs

1. Klicka på **att göra-listan** för att redigera objekten på att göra-listan.

2. Ange text i textrutan **Nytt objekt**. Klicka på **Lägg till** för att anropa det Azure AD B2C-skyddade webb-API:et som lägger till ett objekt i att göra-listan.

    ![Exempel på webbapp i webbläsaren med Lägg till ett att göra-listobjekt](./media/quickstart-web-app-dotnet/add-todo-item-web.png)

    ASP.NET-webbtillämpningsprogrammet innehåller en åtkomsttoken för Azure AD i begäran till den skyddade web API-resursen som utför åtgärder på användarnas objekt för att göra-listor.

Du har använt ditt Azure AD B2C-användarkonto för att utföra ett auktoriserat anrop till ett Azure AD B2C-skyddat webb-API.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda Azure AD B2C-klientorganisationen om du vill prova andra snabbstarter eller självstudier för Azure AD B2C. När den inte längre behövs kan du ta bort [Azure AD B2C-klientorganisationen](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du ett exempel ASP.NET program för att:

* Logga in med en anpassad inloggningssida
* Logga in med en leverantör av social identitet
* Skapa ett Azure AD B2C-konto
* Anropa ett webb-API som skyddas av Azure AD B2C

Kom igång med att skapa en egen Azure AD B2C-klientorganisation.

> [!div class="nextstepaction"]
> [Skapa en Azure Active Directory B2C-klientorganisation i Azure-portalen](tutorial-create-tenant.md)
