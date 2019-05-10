---
title: Självstudie – Aktivera autentisering i ett internt klientprogram – Azure Active Directory B2C | Microsoft Docs
description: Självstudiekurs som lär dig hur du använder Azure Active Directory B2C för att tillhandahålla användarinloggning i ett .NET-program.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 7ca7a670e8d660efb43861312b2670efbaac6638
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190259"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>Självstudier: Aktivera autentisering i ett internt klientprogram med hjälp av Azure Active Directory B2C

I den här självstudien lär du dig använda Azure Active Directory (Azure AD) B2C för att logga in och registrera användare i en WFP-skrivbordsapp (Windows Presentation Foundation). Med Azure AD B2C kan program autentisera med konton på sociala medier, företagskonton och Azure Active Directory-konton med hjälp av öppna standardprotokoll.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till det interna klientprogrammet
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Skapa användarflöden](tutorial-create-user-flows.md) för att möjliggöra användarupplevelser i programmet. 
- Installera [Visual Studio 2019](https://www.visualstudio.com/downloads/) med **.NET-skrivbordsutveckling** och **ASP.NET och webbutveckling** arbetsbelastningar.

## <a name="add-the-native-client-application"></a>Lägga till det interna klientprogrammet

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **Program** och därefter **Lägg till**.
5. Ange ett namn på programmet. Till exempel *nativeapp1*.
6. För **Ta med webbapp/webb-API** väljer du **Nej**.
7. För **Ta med intern klient** väljer du **Ja**.
8. För **Omdirigerings-URI** anger du en giltig omdirigerings-URI med ett anpassat schema. Det finns två viktiga saker att tänka på när du väljer omdirigerings-URI:

    - **Unicitet** – schemat för omdirigerings-URI:n måste vara unikt för varje program. I det här exemplet är `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` schemat. Du måste följa det här mönstret. Om två program delar samma schema får användaren välja program. Inloggningen misslyckas om användaren väljer fel.
    - **Fullständighet** – omdirigerings-URI:n måste ha ett schema och en sökväg. Sökvägen måste innehålla minst ett snedstreck efter domänen. Exempelvis så fungerar `//contoso/` medan `//contoso` misslyckas. Se till att omdirigerings-URI:n inte innehåller några specialtecken, som understreck.

9. Klicka på **Skapa**.
10. På egenskapssidan antecknar du det program-ID som du kommer att använda när du konfigurerar exemplet.

## <a name="configure-the-sample"></a>Konfigurera exemplet

I den här självstudien konfigurerar du ett exempel som du kan ladda ned från GitHub. I WPF-exempelprogrammet visas registrering, inloggning och anrop till ett skyddat webb-API i Azure AD B2C. [Ladda ned en zip-fil](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [bläddra på lagringsplatsen](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) eller klona exemplet från GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Du ändrar appinställningarna genom att byta ut `<your-tenant-name>` mot namnet på klientorganisationen och `<application-ID`> mot det program-ID du antecknade.

1. Öppna därefter `active-directory-b2c-wpf`-lösningen i Visual Studio.
2. I projektet `active-directory-b2c-wpf` öppnar du filen **App.xaml.cs** filen och gör följande uppdateringar:

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. Uppdatera variabeln **PolicySignUpSignIn** med namnet på användarflödet du skapade.

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Kör exemplet

Tryck på **F5** för att skapa och köra exemplet.

### <a name="sign-up-using-an-email-address"></a>Registrera sig med en e-postadress

1. Klicka på **Logga In** för att registrera dig som användare. Då används användarflödet **B2C_1_signupsignin1**.
2. Azure AD B2C visar en inloggningssida med en registreringslänk. Eftersom du inte har något konto klickar du på länken **Registrera dig**. 
3. Arbetsflödet för registrering visar en sida för att samla in och verifiera användarens identitet med en e-postadress. Arbetsflödet för registrering samlar även in användarens lösenord och de attribut som definierats i användarflödet.

    Använd en giltig e-postadress och verifiera med verifieringskoden. Ange ett lösenord. Ange värden för de begärda attributen. 

    ![Arbetsflöde för registrering](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Klicka på **Skapa** och skapa ett lokalt konto i Azure AD B2C-klientorganisationen.

Användaren kan nu använda e-postadressen för att logga in och använda skrivbordsappen.

> [!NOTE]
> Om du klickar på knappen **Anropa API** får du felmeddelandet ”Obehörig”. Du får felmeddelandet eftersom du försöker komma åt en resurs från demoklientorganisationen. Eftersom ditt åtkomst-token endast är giltigt för din Azure AD-klientorganisation är API-anropet obehörigt. Fortsätt med nästa självstudiekurs och skapa ett skyddat webb-API för din klientorganisation.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Lägga till det interna klientprogrammet
> * Konfigurera exemplet för att använda programmet
> * Registrera dig via användarflödet

> [!div class="nextstepaction"]
> [Självstudie: Ge åtkomst till ett Node.js-webb-API från en skrivbordsapp med hjälp av Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
