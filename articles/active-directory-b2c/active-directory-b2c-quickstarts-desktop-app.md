---
title: Snabbstart – Konfigurera inloggning för en skrivbordsapp med Azure Active Directory B2C | Microsoft Docs
description: Kör en ASP.NET-exempelskrivbordsapp som använder Azure Active Directory B2C för användarinloggningen.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/24/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b018872eb1bd8575004fc50124c8ab8b77564b15
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50247580"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Snabbstart: Konfigurera inloggning för en skrivbordsapp med Azure Active Directory B2C 

Azure Active Directory (AD Azure) B2C tillhandahåller identitetshantering i molnet för att skydda dina program, ditt företag och dina kunder. Med Azure AD B2C kan program autentisera med konton på sociala medier och företagskonton med öppna protokoll. I den här snabbstarten använder du ett Windows Presentation Foundation-skrivbordsprogram (WPF) för att logga in med en social identitetsprovider och anropa ett Azure AD B2C-skyddat webb-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Visual Studio 2017](https://www.visualstudio.com/downloads/) med arbetsbelastningen **ASP.NET och webbutveckling**. 
- Ett konto från ett socialt medium, till exempel Facebook, Google, Microsoft eller Twitter.
- [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip) eller klona exempelwebbappen från GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Köra programmet i Visual Studio

1. I exempelprogrammets projektmapp öppnar du lösningen **active-directory-b2c-wpf.sln** i Visual Studio.
2. Tryck på **F5** för att felsöka programmet.

## <a name="sign-in-using-your-account"></a>Logga in med ditt konto

1. Klicka på **Logga in** för att starta arbetsflödet **Registrera sig eller logga in**.

    ![Exempelprogram](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

    Exemplet stöder flera registreringsalternativ, till exempel att använda en social identitetsprovider eller att skapa ett lokalt konto med en e-postadress. För den här snabbstarten använder du ett konto från ett socialt medium, till exempel Facebook, Google, Microsoft eller Twitter. 


2. Azure AD B2C visar en anpassad inloggningssida för ett fiktivt varumärke som kallas Wingtip Toys för exempelwebbappen. Klicka på knappen för den identitetsprovider som du vill använda för att registrera dig med en social identitetsprovider. 

    ![Inloggnings- eller registreringsprovider](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

    Du autentiserar dig (loggar in) med autentiseringsuppgifterna för ditt sociala konto och ger behörighet till programmet att läsa information från det sociala kontot. När du beviljar åtkomst kan programmet hämta profilinformation från det sociala kontot, till exempel ditt namn och din ort. 

2. Avsluta inloggningsprocessen för identitetsprovidern.

    Dina profiluppgifter för det nya kontot fylls i automatiskt med information från det sociala kontot.

## <a name="edit-your-profile"></a>Redigera din profil

Azure AD B2C tillhandahåller funktioner som gör det möjligt för användare att uppdatera sina profiler. Exempelwebbappen använder en Azure AD B2C-princip för att redigera profiler för arbetsflödet. 

1. På programmets menyrad klickar du på **Redigera profil** för att redigera den profil som du skapade.

    ![Redigera profil](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

2. Välj den identitetsprovider som är associerad med kontot som du skapade. Om du exempelvis använde Twitter som identitetsprovider när du skapade ditt konto, väljer du Twitter om du vill ändra den tillhörande profilinformationen.

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

I den här snabbstarten använde du ett exempelskrivbordsprogram för att logga in med en anpassad inloggningssida, logga in med en social identitetsprovider, skapa ett Azure AD B2C-konto och anropa ett Azure AD B2C-skyddat webb-API. 

Kom igång med att skapa en egen Azure AD B2C-klientorganisation. 

> [!div class="nextstepaction"]
> [Skapa en Azure Active Directory B2C-klientorganisation i Azure-portalen](tutorial-create-tenant.md)
