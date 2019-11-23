---
title: Quickstart - Set up sign in for an ASP.NET application using Azure Active Directory B2C
description: In this Quickstart, run a sample ASP.NET web app that uses Azure Active Directory B2C to provide account sign-in.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 114e82147b03fb500197a2129b7960101f85a455
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420191"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Quickstart: Set up sign in for an ASP.NET application using Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) provides cloud identity management to keep your application, business, and customers protected. Med Azure AD B2C kan program autentisera med konton på sociala medier och företagskonton med öppna protokoll. I den här snabbstarten använder du ett ASP.NET-program för att logga in med en social identitetsprovider och anropa en Azure AD B2C-skyddad webb-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) with the **ASP.NET and web development** workload.
- A social account from Facebook, Google, or Microsoft.
- [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) eller klona exempelwebbprogrammet från GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    There are two projects are in the sample solution:

    - **TaskWebApp** – Ett webbprogram som skapar och redigerar en uppgiftslista. The web application uses the **sign-up or sign-in** user flow to sign up or sign in users.
    - **TaskService** – Ett webb-API med stöd för att skapa, läsa, uppdatera och ta bort en uppgiftslista. Webb-API:n skyddas av Azure AD B2C och anropas av webbprogrammet.

## <a name="run-the-application-in-visual-studio"></a>Köra programmet i Visual Studio

1. I exempelprogrammets projektmapp öppnar du lösningen **B2C-WebAPI-DotNet.sln** i Visual Studio.
2. För den här snabbstarten kör du både **TaskWebApp**- och **TaskService**-projektet samtidigt. Högerklicka på lösningen **B2C-WebAPI-DotNet** i Solution Explorer och välj sedan **Ange startprojekt**.
3. Välj **Flera startprojekt** och ändra **Åtgärd** för båda projekten till **Start**.
4. Klicka på **OK**
5. Tryck på **F5** för att felsöka båda programmen. Varje program öppnas i ett eget webbläsarfönster:

    - `https://localhost:44316/` – ASP.NET-webbprogrammet. Du kan interagera direkt med det här programmet i snabbstarten.
    - `https://localhost:44332/` – Webb-API som anropas av ASP.NET-webbprogrammet.

## <a name="sign-in-using-your-account"></a>Logga in med ditt konto

1. Klicka på **Registrera dig/Logga in** i ASP.NET-webbprogrammet för att starta arbetsflödet.

    ![Sample ASP.NET web app in browser with sign up/sign link highlighted](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

    Exemplet stöder flera registreringsalternativ, till exempel att använda en social identitetsprovider eller att skapa ett lokalt konto med en e-postadress. For this quickstart, use a social identity provider account from either Facebook, Google, or Microsoft.

2. Azure AD B2C presents a sign-in page for a fictitious company called Fabrikam for the sample web application. Klicka på knappen för den identitetsprovider som du vill använda för att registrera dig med en social identitetsprovider.

    ![Sign In or Sign Up page showing identity provider buttons](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

    You authenticate (sign in) using your social account credentials and authorize the application to read information from your social account. När du beviljar åtkomst kan programmet hämta profilinformation från det sociala kontot, till exempel ditt namn och din ort.

3. Avsluta inloggningsprocessen för identitetsprovidern.

## <a name="edit-your-profile"></a>Redigera din profil

Azure Active Directory B2C tillhandahåller funktioner som gör det möjligt för användare att uppdatera sina profiler. Exempelwebbappen använder ett Azure AD B2C-användarflöde för att redigera profiler för arbetsflödet.

1. Klicka på ditt profilnamn på programmets menyrad och välj **Redigera profil** för att redigera profilen som du har skapat.

    ![Sample web app in browser with Edit profile link highlighted](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

2. Ändra **Visningsnamn** eller **Ort** och klicka sedan på **Fortsätt** för att uppdatera din profil.

    Den ändrade visas uppe till höger på webbprogrammets startsida.

## <a name="access-a-protected-api-resource"></a>Få åtkomst till en skyddad API-resurs

1. Klicka på **att göra-listan** för att redigera objekten på att göra-listan.

2. Ange text i textrutan **Nytt objekt**. Klicka på **Lägg till** för att anropa det Azure AD B2C-skyddade webb-API:et som lägger till ett objekt i att göra-listan.

    ![Sample web app in browser with Add a to-do list item](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

    ASP.NET-webbtillämpningsprogrammet innehåller en åtkomsttoken för Azure AD i begäran till den skyddade web API-resursen som utför åtgärder på användarnas objekt för att göra-listor.

Du har använt ditt Azure AD B2C-användarkonto för att utföra ett auktoriserat anrop till ett Azure AD B2C-skyddat webb-API.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda Azure AD B2C-klientorganisationen om du vill prova andra snabbstarter eller självstudier för Azure AD B2C. När den inte längre behövs kan du ta bort [Azure AD B2C-klientorganisationen](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nästa steg

In this quickstart, you used a sample ASP.NET application to:

* Sign in with a custom login page
* Sign in with a social identity provider
* Create an Azure AD B2C account
* Call a web API protected by Azure AD B2C

Kom igång med att skapa en egen Azure AD B2C-klient.

> [!div class="nextstepaction"]
> [Skapa en Azure Active Directory B2C-klientorganisation i Azure-portalen](tutorial-create-tenant.md)
