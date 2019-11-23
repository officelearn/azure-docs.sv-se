---
title: Quickstart - Set up sign-in for a desktop app using Azure Active Directory B2C
description: In this Quickstart, run a sample WPF desktop application that uses Azure Active Directory B2C to provide account sign-in.
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
ms.openlocfilehash: ed92605c7fb74186ddde6ff193a1365146494594
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420222"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Snabbstart: Konfigurera inloggning för en skrivbordsapp med Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) provides cloud identity management to keep your application, business, and customers protected. Med Azure AD B2C kan program autentisera med konton på sociala medier och företagskonton med öppna protokoll. I den här snabbstarten använder du ett Windows Presentation Foundation-skrivbordsprogram (WPF) för att logga in med en social identitetsprovider och anropa ett Azure AD B2C-skyddat webb-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) with the **ASP.NET and web development** workload.
- A social account from either Facebook, Google, or Microsoft.
- [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip) eller klona exempelwebbappen från GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Köra programmet i Visual Studio

1. I exempelprogrammets projektmapp öppnar du lösningen **active-directory-b2c-wpf.sln** i Visual Studio.
2. Tryck på **F5** för att felsöka programmet.

## <a name="sign-in-using-your-account"></a>Logga in med ditt konto

1. Klicka på **Logga in** för att starta arbetsflödet **Registrera sig eller logga in**.

    ![Screenshot of the sample WPF application](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

    The sample supports several sign-up options. These options include using a social identity provider or creating a local account using an email address. For this quickstart, use a social identity provider account from either Facebook, Google, or Microsoft.


2. Azure AD B2C presents a sign-in page for a fictitious company called Fabrikam for the sample web application. Klicka på knappen för den identitetsprovider som du vill använda för att registrera dig med en social identitetsprovider.

    ![Sign In or Sign Up page showing identity providers](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

    You authenticate (sign in) using your social account credentials and authorize the application to read information from your social account. När du beviljar åtkomst kan programmet hämta profilinformation från det sociala kontot, till exempel ditt namn och din ort.

2. Avsluta inloggningsprocessen för identitetsprovidern.

    Dina profiluppgifter för det nya kontot fylls i automatiskt med information från det sociala kontot.

## <a name="edit-your-profile"></a>Redigera din profil

Azure AD B2C tillhandahåller funktioner som gör det möjligt för användare att uppdatera sina profiler. Exempelwebbappen använder ett Azure AD B2C-användarflöde för att redigera profiler för arbetsflödet.

1. På programmets menyrad klickar du på **Redigera profil** för att redigera den profil som du skapade.

    ![Edit profile button highlighted in WPF sample app](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

2. Välj den identitetsprovider som är associerad med kontot som du skapade. For example, if you used Facebook as the identity provider when you created your account, choose Facebook to modify the associated profile details.

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

In this quickstart, you used a sample desktop application to:

* Sign in with a custom login page
* Sign in with a social identity provider
* Create an Azure AD B2C account
* Call a web API protected by Azure AD B2C

Kom igång med att skapa en egen Azure AD B2C-klient.

> [!div class="nextstepaction"]
> [Skapa en Azure Active Directory B2C-klientorganisation i Azure-portalen](tutorial-create-tenant.md)
