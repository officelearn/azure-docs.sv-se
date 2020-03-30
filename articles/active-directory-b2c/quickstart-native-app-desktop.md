---
title: 'Snabbstart: Konfigurera inloggning för en skrivbordsapp'
titleSuffix: Azure AD B2C
description: I den här snabbstarten kör du ett exempel på WPF-skrivbordsprogram som använder Azure Active Directory B2C för att tillhandahålla kontosignering.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebed2f5e8664bd4336219f9387b8d27c8f3a1c59
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78187346"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Snabbstart: Konfigurera inloggning för en skrivbordsapp med Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) tillhandahåller molnidentitetshantering för att skydda ditt program, företag och kunder. Med Azure AD B2C kan program autentisera med konton på sociala medier och företagskonton med öppna protokoll. I den här snabbstarten använder du ett Windows Presentation Foundation-skrivbordsprogram (WPF) för att logga in med en social identitetsprovider och anropa ett Azure AD B2C-skyddat webb-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) med **arbetsbelastningen ASP.NET och webbutveckling.**
- Ett socialt konto från antingen Facebook, Google eller Microsoft.
- [Hämta en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip) eller klona [Azure-Samples/active-directory-b2c-dotnet-desktop-databasen](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) från GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Kör programmet i Visual Studio

1. I exempelprogrammets projektmapp öppnar du lösningen **active-directory-b2c-wpf.sln** i Visual Studio.
2. Tryck på **F5** för att felsöka programmet.

## <a name="sign-in-using-your-account"></a>Logga in på ditt konto

1. Klicka på **Logga in** för att starta arbetsflödet **Registrera sig eller logga in**.

    ![Skärmbild av exempelprogrammet WPF](./media/quickstart-native-app-desktop/wpf-sample-application.png)

    Exemplet stöder flera registreringsalternativ. Dessa alternativ inkluderar att använda en leverantör av social identitet eller skapa ett lokalt konto med hjälp av en e-postadress. För den här snabbstarten använder du ett konto för en social identitetsleverantör från antingen Facebook, Google eller Microsoft.


2. Azure AD B2C visar en inloggningssida för ett fiktivt företag som heter Fabrikam för exempelwebbprogrammet. Klicka på knappen för den identitetsprovider som du vill använda för att registrera dig med en social identitetsprovider.

    ![Logga in eller registrera dig sida som visar identitetsleverantörer](./media/quickstart-native-app-desktop/sign-in-or-sign-up-wpf.png)

    Du autentiserar (loggar in) med dina autentiseringsuppgifter för sociala konton och godkänner att programmet läser information från ditt sociala konto. När du beviljar åtkomst kan programmet hämta profilinformation från det sociala kontot, till exempel ditt namn och din ort.

2. Avsluta inloggningsprocessen för identitetsprovidern.

    Dina profiluppgifter för det nya kontot fylls i automatiskt med information från det sociala kontot.

## <a name="edit-your-profile"></a>Redigera din profil

Azure AD B2C tillhandahåller funktioner som gör det möjligt för användare att uppdatera sina profiler. Exempelwebbappen använder ett Azure AD B2C-användarflöde för att redigera profiler för arbetsflödet.

1. På programmets menyrad klickar du på **Redigera profil** för att redigera den profil som du skapade.

    ![Redigera profilknapp markerad i WPF-exempelapp](./media/quickstart-native-app-desktop/edit-profile-wpf.png)

2. Välj den identitetsprovider som är associerad med kontot som du skapade. Om du till exempel använde Facebook som identitetsleverantör när du skapade ditt konto väljer du Facebook för att ändra den associerade profilinformationen.

3. Ändra ditt **Visningsnamn** eller **Stad** och klicka sedan på **Fortsätt**.

    En ny åtkomsttoken visas i textrutan *Tokeninformation*. Om du vill kontrollera ändringarna i din profil, kan du kopiera och klistra in åtkomsttoken i tokenavkodaren https://jwt.ms.

## <a name="access-a-protected-api-resource"></a>Få åtkomst till en skyddad API-resurs

Klicka på **Anropa API** för att göra en begäran om den skyddade resursen.

![Anropa API](./media/quickstart-native-app-desktop/call-api-wpf.png)

Programmet innehåller Azure AD-åtkomsttoken i begäran till den skyddade web-API-resursen. Webb-API:n skickar tillbaka det visningsnamn som ingår i åtkomsttoken.

Du har använt ditt Azure AD B2C-användarkonto för att utföra ett auktoriserat anrop till ett Azure AD B2C-skyddat webb-API.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda Azure AD B2C-klientorganisationen om du vill prova andra snabbstarter eller självstudier för Azure AD B2C. När den inte längre behövs kan du ta bort [Azure AD B2C-klientorganisationen](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du ett exempelskrivbordsprogram för att:

* Logga in med en anpassad inloggningssida
* Logga in med en leverantör av social identitet
* Skapa ett Azure AD B2C-konto
* Anropa ett webb-API som skyddas av Azure AD B2C

Kom igång med att skapa en egen Azure AD B2C-klientorganisation.

> [!div class="nextstepaction"]
> [Skapa en Azure Active Directory B2C-klientorganisation i Azure-portalen](tutorial-create-tenant.md)
