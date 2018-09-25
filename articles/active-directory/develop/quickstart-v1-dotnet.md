---
title: Logga in användare och anropa Microsoft Graph API från en app för .NET Desktop (WPF) | Microsoft Docs
description: 'Lär dig hur du skapar ett Windows Desktop .NET-program som integreras med Azure AD för inloggning och anrop Azure AD skyddade API: er med OAuth 2.0.'
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f9389a7c0e80f075c01f2236fa1bdf9dc9544ac6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987449"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Snabbstart: Logga in användare och anropa Microsoft Graph API från en app för .NET Desktop (WPF)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

För .NET interna klienter som behöver åtkomst till skyddade resurser, ger Azure Active Directory (Azure AD) Active Directory Authentication Library (ADAL). ADAL gör det enkelt för din app för att få åtkomst-token. 

I den här snabbstarten får du lära dig hur du skapar ett att göra-lista för .NET-WPF-program som:

* Hämtar åtkomsttoken för att anropa Azure AD Graph-API med OAuth 2.0-autentiseringsprotokoll.
* Söker i en katalog för användare med en viss alias.
* Användare loggar ut.

Om du vill skapa klar fungerande-program, måste du:

1. Registrera ditt program med Azure AD.
2. Installera och konfigurera ADAL.
3. Använd ADAL för att hämta token från Azure AD.

## <a name="prerequisites"></a>Förutsättningar

Kom igång genom att slutföra dessa krav:

* [Ladda ned stommen app](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) eller [hämta det färdiga exemplet](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)
* Har en Azure AD-klient där du kan skapa användare och registrera ett program. Om du inte redan har en klient, [Lär dig hur du skaffa ett](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Steg 1: Registrera DirectorySearcher-program

Aktivera din app för att hämta token, registrera din app i Azure AD-klienten och bevilja behörighet att komma åt Azure AD Graph-API:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I det översta fältet, Välj ditt konto och under den **Directory** väljer Active Directory-klient som du vill registrera ditt program.
3. Välj på **alla tjänster** i det vänstra navigeringsfältet och välj **Azure Active Directory**.
4. På **appregistreringar**, Välj **Lägg till**.
5. Följ anvisningarna och skapa en ny **interna** klientprogram.
    * Den **namn** av programmet innehåller en beskrivning av ditt program för slutanvändare
    * Den **omdirigerings-Uri** är en kombination av schema och strängen som Azure AD ska använda för att returnera tokensvar. Ange ett specifikt värde till ditt program, till exempel `http://DirectorySearcher`.

6. När du har slutfört registreringen, tilldelar AAD en app i ett unikt program-ID. Du behöver det här värdet i nästa avsnitt, så kopiera det från programsidan.
7. Från den **inställningar** väljer **nödvändiga behörigheter** och välj **Lägg till**. Välj **Microsoft Graph** som API: et och under **delegerade behörigheter** lägga till den **läsa katalogdata** behörighet. Anger den här behörigheten kan ditt program att fråga Graph API för användare.

## <a name="step-2-install-and-configure-adal"></a>Steg 2: Installera och konfigurera ADAL

Nu när du har ett program i Azure AD kan du skriva koden identitetsrelaterade installera ADAL. Du måste tillhandahålla viss information om din appregistrering för ADAL att kommunicera med Azure AD.

1. Börja genom att lägga till ADAL för att den `DirectorySearcher` projekt med Package Manager-konsolen.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. I den `DirectorySearcher` projektet öppnar `app.config`.
1. Ersätt värdena för elementen i den `<appSettings>` avsnitt för att återspegla de värden som du anger i Azure-portalen. Koden kommer att använda dessa värden när den använder ADAL.
  * Den `ida:Tenant` är domänen för din Azure AD-klient, till exempel contoso.onmicrosoft.com
  * Den `ida:ClientId` är klient-ID för ditt program som du kopierade från portalen.
  * Den `ida:RedirectUri` är omdirigerings-URL som du registrerade i portalen.

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Steg 3: Använda ADAL för att hämta token från Azure AD

Den grundläggande principen bakom ADAL är att varje gång som din app behöver en åtkomsttoken, appen anropar bara `authContext.AcquireTokenAsync(...)`, så ADAL resten.

1. I den `DirectorySearcher` projektet öppnar `MainWindow.xaml.cs`.
1. Leta upp den `MainWindow()` metoden. 
1. Initiera din app `AuthenticationContext` -ADAL användarens primära klassen. `AuthenticationContext` är där du skickar ADAL koordinaterna som behövs för att kommunicera med Azure AD och hur den att cachelagra token.

    ```csharp
    public MainWindow()
    {
        InitializeComponent();

        authContext = new AuthenticationContext(authority, new FileCache());

        CheckForCachedToken();
    }
    ```

1. Leta upp den `Search(...)` metod som anropas när användaren väljer den **Search** knappen i appens användargränssnitt. Den här metoden gör en GET-begäran för Azure AD Graph-API för att fråga för användare vars UPN börjar med den angivna söktermen.
1. Om du vill fråga Graph API är en access_token i den `Authorization` huvudet i begäran, vilket är här ADAL kommer in.

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

    När appen begär en token genom att anropa `AcquireTokenAsync(...)`, ADAL försöker att returnera en token utan att be användaren om autentiseringsuppgifter.
    * Om ADAL avgör att användaren måste logga in och hämta en token, visas en dialogruta för inloggning, samla in användarens autentiseringsuppgifter och returnerar en token efter lyckad autentisering. 
    * Om ADAL inte kan returnera en token av någon anledning, genereras ett `AdalException`.

1. Observera att den `AuthenticationResult` objektet innehåller en `UserInfo` objekt som kan användas för att samla in information som din app kan behöva. I DirectorySearcher `UserInfo` används för att anpassa appens användargränssnitt med det användar-ID.
1. När användaren väljer den **logga ut** knapp, se till att nästa anrop till `AcquireTokenAsync(...)` uppmanas användaren att logga in. Du kan enkelt göra detta med ADAL genom att avmarkera token-cache:

    ```csharp
    private void SignOut(object sender = null, RoutedEventArgs args = null)
    {
        // Clear the token cache
        authContext.TokenCache.Clear();

        ...
    }
    ```

    Om du inte klickar på den **logga ut** knapp, måste vara användarens session för nästa gång de kör DirectorySearcher. När appen startar kan du kontrollera ADAL-tokencache för en befintlig token och uppdatera Användargränssnittet i enlighet med detta.

1. I den `CheckForCachedToken()` metod, se ett annat anrop till `AcquireTokenAsync(...)`, nu skicka den `PromptBehavior.Never` parametern. `PromptBehavior.Never` talar om ADAL att användaren inte att ange för inloggning och ADAL i stället utlösa ett undantag om det inte går att returnera en token.

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

Grattis! Nu har du en fungerande .NET WPF-program som kan autentisera användare, på ett säkert sätt anropa webb-API: er med hjälp av OAuth 2.0 och få grundläggande information om användaren. Om du inte redan gjort nu är det hög tid att fylla i din klient med vissa användare. Kör DirectorySearcher appen och logga in med något av dessa användare. Sök efter andra användare baserat på deras UPN. Stäng appen och köra den igen. Observera hur användarens session förblir intakta. Logga ut och logga in igen som en annan användare.

ADAL gör det enkelt att införliva de här vanliga identitetsfunktioner i ditt program. Det hand tar om ändrad arbetet åt dig, inklusive hantering av cache, stöd för OAuth-protokoll, presentera användaren med ett användargränssnitt, uppdatera utgångna token och mycket mer. Allt du behöver veta är ett enda API-anrop `authContext.AcquireTokenAsync(...)`.

Referenser finns i det färdiga exemplet (utan dina konfigurationsvärden) [på GitHub](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Nästa steg

Lär dig att skydda ett webb-API med OAuth 2.0-ägartoken åtkomst.
> [!div class="nextstepaction"]
> [Skydda ett .NET webb-API med Azure AD >>](quickstart-v1-dotnet-webapi.md)
