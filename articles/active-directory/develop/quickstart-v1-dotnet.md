---
title: Azure AD .NET Desktop (WPF) komma igång | Microsoft Docs
description: 'Hur du skapar ett Windows Desktop .NET-program som integreras med Azure AD för inloggning och anropar Azure AD skyddade API: er med OAuth.'
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
ms.date: 11/30/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e7a662afd78ff053c60aeb5de3af9f0070a7c458
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506530"
---
# <a name="azure-ad-net-desktop-wpf-getting-started"></a>Azure AD .NET Desktop (WPF) komma igång
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Om du utvecklar ett skrivbordsprogram Azure AD att gör det enkelt och enkelt för dig att autentisera dina användare med deras Active Directory-konton. Dessutom kan ditt program på ett säkert sätt använda alla webb-API som skyddas av Azure AD, som API: er för Office 365 eller Azure-API.

För .NET interna klienter som behöver åtkomst till skyddade resurser, tillhandahåller Azure AD Active Directory Authentication Library eller ADAL. ADALS syfte i vardagen är att göra det enkelt för din app för att få åtkomst-token. För att demonstrera hur enkelt det är ska här vi skapa ett att göra-lista för .NET-WPF-program som:

* Hämtar åtkomsttoken för att anropa en Azure AD Graph API med hjälp av den [OAuth 2.0-autentiseringsprotokollet](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Söker i en katalog för användare med en viss alias.
* Användare loggar ut.

Om du vill skapa det fullständiga fungerande programmet, måste du:

1. Registrera ditt program med Azure AD.
2. Installera och konfigurera ADAL.
3. Använd ADAL för att hämta token från Azure AD.

Du kommer igång [ladda ned stommen app](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) eller [hämta det färdiga exemplet](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Du måste också en Azure AD-klient som du kan skapa användare och registrera ett program. Om du inte redan har en klient, [Lär dig hur du skaffa ett](quickstart-create-new-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>1. Registrera DirectorySearcher-program
För att aktivera din app för att hämta token måste måste du först du registrera den i Azure AD-klienten och bevilja behörighet att komma åt Azure AD Graph-API:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I det översta fältet, klicka på ditt konto och under den **Directory** väljer Active Directory-klient som du vill registrera ditt program.
3. Klicka på **alla tjänster** i det vänstra navigeringsfältet och välj **Azure Active Directory**.
4. Klicka på **appregistreringar** och välj **Lägg till**.
5. Följ anvisningarna och skapa en ny **internt klientprogram**.
  * Den **namn** av programmet innehåller en beskrivning av ditt program för slutanvändare
  * Den **omdirigerings-Uri** är en kombination av schema och strängen som Azure AD ska använda för att returnera tokensvar. Ange ett värde som är specifika för ditt program, t.ex. `http://DirectorySearcher`.
6. När du har slutfört registreringen, tilldelar AAD en app i ett unikt program-ID. Du behöver det här värdet i nästa avsnitt, så kopiera det från programsidan.
7. Från den **inställningar** väljer **nödvändiga behörigheter** och välj **Lägg till**. Välj den **Microsoft Graph** som API och lägger till den **läsa kataloginformation** behörighet under **delegerade behörigheter**. Detta aktiverar ditt program att fråga Graph API för användare.

## <a name="2-install--configure-adal"></a>2. Installera och konfigurera ADAL
Nu när du har ett program i Azure AD kan du skriva koden identitetsrelaterade installera ADAL. Du behöver tillhandahålla viss information om appregistrering av din för ADAL för att kunna kommunicera med Azure AD.

* Börja genom att lägga till ADAL DirectorySearcher projektet med Package Manager-konsolen.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* Öppna i projektet DirectorySearcher `app.config`. Ersätt värdena för elementen i den `<appSettings>` avsnitt för att återspegla de värden som du anger i Azure Portal. Koden kommer att använda dessa värden när den använder ADAL.
  * Den `ida:Tenant` är domänen för Azure AD-klienten, t.ex. contoso.onmicrosoft.com
  * Den `ida:ClientId` är clientId av ditt program som du kopierade från portalen.
  * Den `ida:RedirectUri` är omdirigeringen url som du registrerade i portalen.

## <a name="3-use-adal-to-get-tokens-from-aad"></a>3. Använd ADAL för att hämta token från AAD
Den grundläggande principen bakom ADAL är att varje gång som din app behöver en åtkomsttoken, bara anropas `authContext.AcquireTokenAsync(...)`, så ADAL resten. 

* I den `DirectorySearcher` projektet öppnar `MainWindow.xaml.cs` och leta upp den `MainWindow()` metoden. Det första steget är att initiera appens `AuthenticationContext` -ADAL användarens primära klassen. Det är där du skickar ADAL koordinaterna som behövs för att kommunicera med Azure AD och hur den att cachelagra token.

```csharp
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

* Nu letar du upp den `Search(...)` metod som kommer att anropas när användaren klickar på knappen ”Sök” i appens användargränssnitt. Den här metoden gör en GET-begäran för Azure AD Graph-API för att fråga för användare vars UPN börjar med den angivna söktermen. Men för att fråga Graph API, måste du inkludera ett access_token i den `Authorization` rubrik på begäran – det här är här ADAL kommer in.

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
* När appen begär en token genom att anropa `AcquireTokenAsync(...)`, ADAL försöker att returnera en token utan att be användaren om autentiseringsuppgifter. Om ADAL avgör att användaren måste logga in och hämta en token, visas en dialogruta för inloggning, samla in användarens autentiseringsuppgifter och returnerar en token efter lyckad autentisering. Om ADAL inte kan returnera en token av någon anledning, genereras ett `AdalException`.
* Observera att den `AuthenticationResult` objektet innehåller en `UserInfo` objekt som kan användas för att samla in information som din app kan behöva. I DirectorySearcher `UserInfo` används för att anpassa appens användargränssnitt med det användar-id.
* När användaren klickar på knappen ”Logga ut” vi vill se till att nästa anrop till `AcquireTokenAsync(...)` uppmanas användaren att logga in. Med ADAL är det lika enkelt som att rensa cacheminnet token:

```csharp
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

* Men om användaren inte klickar på knappen ”Logga ut”, kommer du att behålla användarens session för nästa gång de kör DirectorySearcher. När appen startar kan du kontrollera ADAL-tokencache för en befintlig token och uppdatera Användargränssnittet i enlighet med detta. I den `CheckForCachedToken()` metod, se ett annat anrop till `AcquireTokenAsync(...)`, nu skicka den `PromptBehavior.Never` parametern. `PromptBehavior.Never` talar om ADAL att användaren inte att ange för inloggning och ADAL i stället utlösa ett undantag om det inte går att returnera en token.

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

Grattis! Nu har en fungerande .NET WPF-program som har möjlighet att autentisera användare, på ett säkert sätt anropa webb-API: er med hjälp av OAuth 2.0 och få grundläggande information om användaren. Om du inte redan gjort nu är det hög tid att fylla i din klient med vissa användare. Kör DirectorySearcher appen och logga in med något av dessa användare. Sök efter andra användare baserat på deras UPN. Stäng appen och kör den igen. Observera hur användarens session förblir intakta. Logga ut och logga in igen som en annan användare.

ADAL gör det enkelt att införliva alla dessa vanliga identitetsfunktioner i ditt program. Det hand tar om ändrad arbetet åt dig – hantering av cache, stöd för OAuth-protokoll, presentera användaren med ett användargränssnitt, uppdatera utgångna token och mycket mer. Allt du behöver veta är ett enda API-anrop `authContext.AcquireTokenAsync(...)`.

För referens, färdiga exemplet (utan dina konfigurationsvärden) tillhandahåller [här](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). Du kan nu gå vidare till fler scenarier. Du kan du testa:

[Skydda ett .NET webb-API med Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

