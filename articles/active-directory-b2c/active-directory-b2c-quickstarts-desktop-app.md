---
title: 'Snabb start: Konfigurera inloggning för en Skriv bords app'
titleSuffix: Azure AD B2C
description: I den här snabb starten kör du ett exempel på ett WPF Desktop-program som använder Azure Active Directory B2C för att tillhandahålla konto inloggning.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8265bad5b756ee2ac23bc41c9c58452251ce0717
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950655"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Snabbstart: Konfigurera inloggning för en skrivbordsapp med Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) tillhandahåller moln identitets hantering för att hålla ditt program, din verksamhet och dina kunder skyddade. Med Azure AD B2C kan program autentisera med konton på sociala medier och företagskonton med öppna protokoll. I den här snabbstarten använder du ett Windows Presentation Foundation-skrivbordsprogram (WPF) för att logga in med en social identitetsprovider och anropa ett Azure AD B2C-skyddat webb-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) med arbets belastningen **ASP.net och webb utveckling** .
- Ett socialt konto från Facebook, Google eller Microsoft.
- [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip) eller klona exempelwebbappen från GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Köra programmet i Visual Studio

1. I exempelprogrammets projektmapp öppnar du lösningen **active-directory-b2c-wpf.sln** i Visual Studio.
2. Tryck på **F5** för att felsöka programmet.

## <a name="sign-in-using-your-account"></a>Logga in med ditt konto

1. Klicka på **Logga in** för att starta arbetsflödet **Registrera sig eller logga in**.

    ![Skärm bild av exempel-WPF-programmet](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

    Exemplet stöder flera registrerings alternativ. Dessa alternativ omfattar att använda en social identitetsprovider eller att skapa ett lokalt konto med en e-postadress. I den här snabb starten ska du använda ett konto för sociala identitets leverantörer från Facebook, Google eller Microsoft.


2. Azure AD B2C visar en inloggnings sida för ett fiktivt företag som kallas Fabrikam för exempel webb programmet. Klicka på knappen för den identitetsprovider som du vill använda för att registrera dig med en social identitetsprovider.

    ![Sidan logga in eller registrera med identitets leverantörer](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

    Du autentiserar (loggar in) med dina autentiseringsuppgifter för ditt sociala konto och godkänner att programmet läser information från ditt sociala konto. När du beviljar åtkomst kan programmet hämta profilinformation från det sociala kontot, till exempel ditt namn och din ort.

2. Avsluta inloggningsprocessen för identitetsprovidern.

    Dina profiluppgifter för det nya kontot fylls i automatiskt med information från det sociala kontot.

## <a name="edit-your-profile"></a>Redigera din profil

Azure AD B2C tillhandahåller funktioner som gör det möjligt för användare att uppdatera sina profiler. Exempelwebbappen använder ett Azure AD B2C-användarflöde för att redigera profiler för arbetsflödet.

1. På programmets menyrad klickar du på **Redigera profil** för att redigera den profil som du skapade.

    ![Knappen Redigera profil som marker ATS i WPF-exempelprogrammet](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

2. Välj den identitetsprovider som är associerad med kontot som du skapade. Om du till exempel använde Facebook som identitetsprovider när du skapade ditt konto väljer du Facebook för att ändra den tillhör ande profil informationen.

3. Ändra ditt **Visningsnamn** eller **Stad** och klicka sedan på **Fortsätt**.

    En ny åtkomsttoken visas i textrutan *Tokeninformation*. Om du vill kontrollera ändringarna i din profil, kan du kopiera och klistra in åtkomsttoken i tokenavkodaren https://jwt.ms.

## <a name="access-a-protected-api-resource"></a>Få åtkomst till en skyddad API-resurs

Klicka på **Anropa API** för att göra en begäran om den skyddade resursen.

    ![Call API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

    The application includes the Azure AD access token in the request to the protected web API resource. The web API sends back the display name contained in the access token.

Du har använt ditt Azure AD B2C-användarkonto för att utföra ett auktoriserat anrop till ett Azure AD B2C-skyddat webb-API.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda Azure AD B2C-klientorganisationen om du vill prova andra snabbstarter eller självstudier för Azure AD B2C. När den inte längre behövs kan du ta bort [Azure AD B2C-klientorganisationen](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du ett exempel på ett Skriv bords program för att:

* Logga in med en anpassad inloggnings sida
* Logga in med en social identitetsprovider
* Skapa ett Azure AD B2C konto
* Anropa ett webb-API som skyddas av Azure AD B2C

Kom igång med att skapa en egen Azure AD B2C-klient.

> [!div class="nextstepaction"]
> [Skapa en Azure Active Directory B2C-klientorganisation i Azure-portalen](tutorial-create-tenant.md)
