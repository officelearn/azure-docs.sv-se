---
title: 'Självstudiekurs: Autentisera användare i ett inbyggt klientprogram'
titleSuffix: Azure AD B2C
description: Självstudiekurs som lär dig hur du använder Azure Active Directory B2C för att tillhandahålla användarinloggning i ett .NET-program.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06d27c3a3daa4702653a2063d0ac70fd094e2d74
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78186207"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Självstudiekurs: Autentisera användare i en inbyggd skrivbordsklient med Azure Active Directory B2C

Den här självstudien visar hur du använder Azure Active Directory B2C (Azure AD B2C) för att logga in och registrera användare i ett WPF-skrivbordsprogram (Windows Presentation Foundation). Med Azure AD B2C kan program autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med hjälp av öppna standardprotokoll.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Lägga till det interna klientprogrammet
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

- [Skapa användarflöden](tutorial-create-user-flows.md) för att möjliggöra användarupplevelser i programmet.
- Installera [Visual Studio 2019](https://www.visualstudio.com/downloads/) med **.NET-skrivbordsutveckling** och **ASP.NET- och webbutvecklingsarbetsbelastningar.**

## <a name="add-the-native-client-application"></a>Lägga till det interna klientprogrammet

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Registrera **program-ID:t (klient)** för användning i ett senare steg.

## <a name="configure-the-sample"></a>Konfigurera exemplet

I den här självstudien konfigurerar du ett exempel som du kan ladda ned från GitHub. Exempelet på WPF-skrivbordsprogram demonstrerar registrering, inloggning och kan anropa ett skyddat webb-API i Azure AD B2C. [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), [bläddra på lagringsplatsen](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) eller klona exemplet från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Så här uppdaterar du programmet så att det fungerar med din Azure AD B2C-klient och anropar dess användarflöden i stället för de i standardversionen av klienten:

1. Öppna **active-directory-b2c-wpf-lösningen** (`active-directory-b2c-wpf.sln`) i Visual Studio.
2. Öppna **App.xaml.cs-filen i active-directory-b2c-wpf-projektet** och hitta följande variabla definitioner. *App.xaml.cs* Ersätt `{your-tenant-name}` med ditt Azure AD B2C-klientnamn och `{application-ID}` med det program-ID som du spelade in tidigare.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Uppdatera principnamnsvariablerna med namnen på de användarflöden som du skapade som en del av förutsättningarna. Ett exempel:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>Kör exemplet

Tryck på **F5** för att skapa och köra exemplet.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

1. Välj **Logga in** för att registrera dig som användare. Då används användarflödet **B2C_1_signupsignin1**.
2. Azure AD B2C presenterar en inloggningssida med länken **Registrera dig nu.** Eftersom du ännu inte har ett konto väljer du länken **Registrera dig nu.**
3. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som definierats i användarflödet.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen.

    ![Registreringssida som visas som en del av arbetsflödet för inloggning/registrering](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Välj **Skapa** för att skapa ett lokalt konto i Azure AD B2C-klienten.

Användaren kan nu använda sin e-postadress för att logga in och använda skrivbordsprogrammet. Efter en lyckad registrering eller inloggning visas tokeninformationen i den nedre rutan i WPF-appen.

![Tokeninformation som visas i det nedre fönstret i WPF-skrivbordsprogrammet](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

Om du väljer knappen **Anropa API** visas ett **felmeddelande.** Du stöter på felet eftersom programmet i sitt aktuella tillstånd försöker komma `fabrikamb2c.onmicrosoft.com`åt ett API som skyddas av demoklienten. Eftersom din åtkomsttoken endast är giltig för din Azure AD B2C-klient är API-anropet därför obehörigt.

Fortsätt till nästa självstudiekurs för att registrera ett skyddat webb-API i din egen klientorganisation och aktivera **funktionen Anropa API.**

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till det interna klientprogrammet
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

Om du vill aktivera call **API-knappfunktionen** ger du WPF-skrivbordsprogrammet åtkomst till ett webb-API som är registrerat i din egen Azure AD B2C-klient:

> [!div class="nextstepaction"]
> [Självstudiekurs: Bevilja åtkomst till ett Node.js-webb-API från en skrivbordsapp >](tutorial-desktop-app-webapi.md)
