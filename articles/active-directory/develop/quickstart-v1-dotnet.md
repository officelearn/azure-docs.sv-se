---
title: Logga in användare och anropa Microsoft Graph API från en .NET Desktop (WPF)-app | Microsoft Docs
description: Lär dig hur du skapar ett .NET Windows Desktop-program som integreras med Azure AD för inloggning och anropar Azure AD-skyddade API:er med hjälp av OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b06ceb86e8406657766be1375889fcfe1b4673d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204550"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Snabbstart: Logga in användare och anropa Microsoft Graph API från en .NET Desktop (WPF)-app

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

För inbyggda .NET-klienter som behöver komma åt skyddade resurser tillhandahåller Azure Active Directory (Azure AD) Active Directory Authentication Library (ADAL). ADAL gör det enkelt för din app för att hämta åtkomsttoken. 

I den här snabbstarten lär du dig hur du skapar ett att göra-program med .NET WPF som:

* Hämtar åtkomsttoken för att anropa Azure AD Graph API med hjälp av OAuth 2.0-autentiseringsprotokollet.
* Söker efter en katalog för användare med ett visst alias.
* Loggar ut användare.

Om du vill skapa ett komplett, fungerande program måste du:

1. Registrera appen med Azure AD.
2. Installera och konfigurera ADAL.
3. Använda ADAL för att hämta token från Azure AD.

## <a name="prerequisites"></a>Nödvändiga komponenter

Kom igång genom att slutföra följande krav:

* [Ladda ned appens stomme](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) eller [ladda ned det färdiga exemplet](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)
* Ha en Azure AD-klientorganisation där du kan skapa användare och registrera en app. Om du inte redan har en klientorganisation kan du [läsa om hur du skaffar en](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Steg 1: Registrera DirectorySearcher-programmet

Om du vill göra det möjligt för appen att hämta apptoken måste du registrera appen i din Azure AD-klientorganisation och bevilja den behörighet för åtkomst till Azure AD Graph API:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I det översta fältet väljer du ditt konto och under **kataloglistan** väljer du den Active Directory-klientorganisation där du vill registrera appen.
3. Välj **Alla tjänster** i det vänstra navigeringsfältet och välj **Azure Active Directory**.
4. För **Appregistreringar** väljer du **Lägg till**.
5. Följ anvisningarna för att skapa ett nytt **internt** klientprogram.
    * **Namnet** på appen beskriver den för slutanvändarna
    * **Omdirigerings-URI** är ett schema och en strängkombination som Azure AD använder till att returnera tokensvar. Ange ett värde som är specifikt för ditt program, till exempel `http://DirectorySearcher`.

6. När du har slutfört registreringen tilldelar AAD din app ett unikt program-ID. Du behöver det här värdet i nästa avsnitt, så kopiera det från programsidan.
7. På sidan **Inställningar** väljer du **Nödvändiga behörigheter** och sedan **Lägg till**. Välj **Microsoft Graph** som API:et, och under **Delegerade behörigheter** lägger du till behörigheten **Läsa katalogdata**. När du konfigurerar den här behörigheten kana appen fråga Graph API efter användare.

## <a name="step-2-install-and-configure-adal"></a>Steg 2: Installera och konfigurera ADAL

Nu när du har en app i Azure AD kan du installera ADAL och skriva din identitetsrelaterade kod. För att ADAL ska kunna kommunicera med Azure AD måste du ange viss information om appregistreringen.

1. Börja genom att lägga till ADAL till `DirectorySearcher`-projektet med hjälp av Package Manager-konsolen.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. I projektet `DirectorySearcher` öppnar du `app.config`.
1. Ersätt värdena för elementen i avsnittet `<appSettings>` så att de speglar de värden som du matade in i Azure-portalen. Koden kommer att referera till dessa värden när den använder ADAL.
  * `ida:Tenant` är domänen för din Azure AD-klientorganisation, till exempel contoso.onmicrosoft.com
  * `ida:ClientId` är det klient-ID för din app som du kopierade från portalen.
  * `ida:RedirectUri` är den omdirigerings-URL som du registrerade i portalen.

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Steg 3: Använda ADAL för att hämta token från Azure AD

Grundprincipen bakom ADAL är att när appen behöver en åtkomsttoken anropar den helt enkelt `authContext.AcquireTokenAsync(...)`, så gör ADAL resten.

1. I projektet `DirectorySearcher` öppnar du `MainWindow.xaml.cs`.
1. Leta upp metoden `MainWindow()`. 
1. Initiera appens `AuthenticationContext` – ADAL:s primära klass. `AuthenticationContext` är dit du skickar koordinaterna till ADAL som behövs för att kommunicera med Azure AD, och ange hur den ska cachelagra token.

    ```csharp
    public MainWindow()
    {
        InitializeComponent();

        authContext = new AuthenticationContext(authority, new FileCache());

        CheckForCachedToken();
    }
    ```

1. Leta upp metoden `Search(...)`, som anropas när användaren väljer **sökknappen** i appens användargränssnitt. Den här metoden gör en GET-begäran till Azure AD Graph API för att fråga efter användare vars UPN börjar med den angivna söktermen.
1. Om du vill fråga Graph API måste du inkludera en access_token i `Authorization`-huvudet för begäran, och det är här ADAL kommer in i bilden.

    ```csharp
    private async void Search(object sender, RoutedEventArgs e)
    {
        // Validate the Input String
        if (string.IsNullOrEmpty(SearchText.Text))
        {
            MessageBox.Show("Please enter a value for the To Do item name");
            return;
        }

        // Get an Access Token for the Graph API
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
            UserNameLabel.Content = result.UserInfo.DisplayableId;
            SignOutButton.Visibility = Visibility.Visible;
        }
        catch (AdalException ex)
        {
            // An unexpected error occurred, or user canceled the sign in.
            if (ex.ErrorCode != "access_denied")
                MessageBox.Show(ex.Message);

            return;
        }

        ...
    }
    ```

    När appen begär en token genom att anropa `AcquireTokenAsync(...)` försöker ADAL returnera en token utan att fråga efter autentiseringsuppgifter.
    * Om ADAL avgör att användaren måste logga in för att få en token visas en dialogruta för inloggning. Användarens autentiseringsuppgifter samlas in och en token returneras efter utförd autentisering. 
    * Om ADAL inte kan returnera en token av någon anledning genererar det ett `AdalException`.

1. Observera att objektet `AuthenticationResult` innehåller ett `UserInfo`-objekt som du kan använda till att samla in information som appen kan behöva. I DirectorySearcher används `UserInfo` för att anpassa appens användargränssnitt med användar-ID:t.
1. När användaren väljer knappen **Logga ut** ska du se till att nästa anrop till `AcquireTokenAsync(...)` uppmanar användaren att logga in. Du kan enkelt göra detta med ADAL genom att avmarkera token-cache:

    ```csharp
    private void SignOut(object sender = null, RoutedEventArgs args = null)
    {
        // Clear the token cache
        authContext.TokenCache.Clear();

        ...
    }
    ```

    Om du inte klickar på **utloggningsknappen** måste du underhålla användarens session tills nästa gång de kör DirectorySearcher. När appen startar kan du kontrollera ADAL-tokencache för en befintlig token och uppdatera användargränssnittet i enlighet med detta.

1. I metoden `CheckForCachedToken()` gör du ytterligare ett anrop till `AcquireTokenAsync(...)`, och den här gången skickar du `PromptBehavior.Never`-parametern. `PromptBehavior.Never` talar om för ADAL att användaren inte bör uppmanas att logga in, och att ADAL nu i stället ska generera ett undantag om det inte går att returnera en token.

    ```csharp
    public async void CheckForCachedToken() 
    {
        // As the application starts, try to get an access token without prompting the user. If one exists, show the user as signed in.
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "user_interaction_required")
            {
                // An unexpected error occurred.
                MessageBox.Show(ex.Message);
            }

            // If user interaction is required, proceed to main page without singing the user in.
            return;
        }

        // A valid token is in the cache
        SignOutButton.Visibility = Visibility.Visible;
        UserNameLabel.Content = result.UserInfo.DisplayableId;
    }
    ```

Grattis! Nu har du en fungerande .NET WPF-app som kan autentisera användare, på ett säkert sätt anropa webb-API:er med hjälp av OAuth 2.0 och få grundläggande information om användaren. Om du inte redan har gjort det är det dags att fylla i klientorganisationen med några användare. Kör din DirectorySearcher-app och logga in med någon av dessa användare. Sök efter andra användare utifrån deras UPN. Stäng appen och kör den igen. Observera att användarens session förblir intakt. Logga ut och logga in igen som en annan användare.

ADAL gör det enkelt att inkorporera dessa vanliga identitetsfunktioner i appen. Det tar hand om grovjobbet åt dig, som cachehantering, stöd för OAuth-protokoll, visa användaren ett användargränssnitt för inloggning, uppdatera token som gått ut och mer. Allt du verkligen behöver veta är ett enda API-anrop, `authContext.AcquireTokenAsync(...)`.

Som referens kan du titta på det slutförda exemplet (utan dina konfigurationsvärden) på [GitHub](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Nästa steg

Lär dig att skydda ett webb-API med OAuth 2.0-ägartokenåtkomst.
> [!div class="nextstepaction"]
> [Skydda ett .NET-webb-API med Azure AD >>](quickstart-v1-dotnet-webapi.md)
