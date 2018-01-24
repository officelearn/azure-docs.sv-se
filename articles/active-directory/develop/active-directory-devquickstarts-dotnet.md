---
title: "Azure AD .NET skrivbordet (WPF) komma igång | Microsoft Docs"
description: "Hur du skapar ett program i .NET Windows-skrivbordet som kan integreras med Azure AD för inloggning och Azure AD-anrop skyddade API: er som använder sig av OAuth."
services: active-directory
documentationcenter: .net
author: jmprieur
manager: mtillman
editor: 
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/30/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9b1118b0159437e179b09b179571ed1460c3daf6
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="azure-ad-net-desktop-wpf-getting-started"></a>Azure AD .NET skrivbordet (WPF) komma igång
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Om du utvecklar ett skrivbordsprogram Azure AD att gör det lätt att autentisera användarna med deras Active Directory-konton.  Det gör också ditt program att använda alla webb-API som skyddas av Azure AD, till exempel API: er för Office 365 eller Azure API på ett säkert sätt.

Azure AD innehåller Active Directory Authentication Library eller ADAL för .NET interna klienter som behöver åtkomst till skyddade resurser.  ADALS uteslutande i livslängd är att göra det lättare för din app för att få åtkomst-token.  För att demonstrera hur lätt det är ska här vi skapa ett .NET uppgiftslista för WPF-program som:

* Hämtar åtkomst till token för att anropa en Azure AD Graph API med hjälp av den [OAuth 2.0-autentiseringsprotokollet](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Söker en katalog för användare med ett visst alias.
* Användare loggar ut.

För att skapa fullständiga fungerande program måste du:

1. Registrera ditt program med Azure AD.
2. Installera och konfigurera ADAL.
3. Använd ADAL för att hämta token från Azure AD.

Du kommer igång [ladda ned stommen app](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) eller [hämta det slutförda exemplet](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Du måste också en Azure AD-klient som du kan skapa användare och registrera ett program.  Om du inte redan har en klient [Lär dig hur du skaffa en](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>1. Registrera DirectorySearcher-program
Om du vill aktivera din app att hämta token måste du måste först registrera det i Azure AD-klienten och bevilja behörighet att komma åt Azure AD Graph-API:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på den översta raden på ditt konto och under den **Directory** Välj Active Directory-klient som du vill registrera ditt program.
3. Klicka på **fler tjänster** i den vänstra nav och välj **Azure Active Directory**.
4. Klicka på **App registreringar** och välj **Lägg till**.
5. Följ anvisningarna och skapa en ny **internt klientprogram**.
  * Den **namn** av programmet beskriva programmet till slutanvändare
  * Den **omdirigerings-Uri** är en kombination av schemat och strängen som Azure AD för att returnera token svar.  Ange ett värde som är specifik för ditt program, t.ex. `http://DirectorySearcher`.
6. När du har slutfört registreringen tilldelas AAD appen ett unikt program-ID.  Du behöver det här värdet i nästa avsnitt så kopiera den från appen på sidan.
7. Från den **inställningar** väljer **nödvändiga behörigheter** och välj **Lägg till**. Välj den **Microsoft Graph** som API och Lägg till den **läsa katalogdata** behörighet under **delegerade behörigheter**.  Detta gör att programmet att fråga Graph API för användare.

## <a name="2-install--configure-adal"></a>2. Installera och konfigurera ADAL
Nu när du har ett program i Azure AD kan du installera ADAL och Skriv koden identitetsrelaterade.  Du måste tillhandahålla information om din appregistrering för ADAL för att kunna kommunicera med Azure AD.

* Börja genom att lägga till ADAL DirectorySearcher projektet med Package Manager-konsolen.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* Öppna i projektet DirectorySearcher `app.config`.  Ersätt värdena för elementen i den `<appSettings>` avsnittet för att återspegla de värden du matar in i Azure Portal.  Koden ska referera till dessa värden när den använder ADAL.
  * Den `ida:Tenant` är domänen för din Azure AD-klient, t.ex. contoso.onmicrosoft.com
  * Den `ida:ClientId` är clientId för ditt program som du kopierade från portalen.
  * Den `ida:RedirectUri` är omdirigeringen url som du har registrerat i portalen.

## <a name="3----use-adal-to-get-tokens-from-aad"></a>3.    Använd ADAL för att hämta token från AAD
Den grundläggande principen bakom ADAL är att när din app behöver en åtkomst-token kan bara anropas `authContext.AcquireTokenAsync(...)`, och ADAL gör resten.  

* I den `DirectorySearcher` projektet öppnar `MainWindow.xaml.cs` och leta upp den `MainWindow()` metoden.  Det första steget är att initiera appens `AuthenticationContext` -ADAL vars primära klassen.  Det är där du skickar ADAL koordinaterna behöver kommunicera med Azure AD och hur det kan cachelagra token.

```csharp
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

* Leta reda på `Search(...)` metod som ska anropas när användaren klickar på knappen ”Sök” i appens användargränssnitt.  Den här metoden gör en GET-begäran till Azure AD Graph API frågan för användare vars UPN-namnet börjar med den angivna söktermen.  Men om du vill fråga Graph API, måste du inkludera ett access_token i den `Authorization` huvud för begäran - detta är där ADAL kommer in.

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
* När din app begär en token genom att anropa `AcquireTokenAsync(...)`, ADAL görs ett försök att returnera en token utan att fråga användaren om autentiseringsuppgifter.  Om ADAL avgör att användaren måste logga in att hämta en token, visas en dialogruta med en inloggning, samla in användarens autentiseringsuppgifter och returnera en token vid autentiseringen.  Om ADAL inte kan returnera en token av någon anledning, genereras ett `AdalException`.
* Observera att den `AuthenticationResult` objektet innehåller en `UserInfo` objekt som kan användas för att samla in information som din app kan behöva.  I DirectorySearcher `UserInfo` används för att anpassa appens användargränssnitt med det användar-id.
* När användaren klickar på knappen ”Logga ut” vi vill se till att nästa anrop till `AcquireTokenAsync(...)` uppmanas användaren att logga in.  Det är lika enkelt som att rensa cacheminnet token med ADAL:

```csharp
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

* Om användaren inte klicka på knappen ”Logga ut”, kommer du vill behålla användarens session för nästa gång de kör DirectorySearcher.  Du kan kontrollera ADAL'S token-cache för en befintlig token och uppdateras Användargränssnittet när appen startar.  I den `CheckForCachedToken()` metod, gör ett annat anrop till `AcquireTokenAsync(...)`, nu skicka i den `PromptBehavior.Never` parameter.  `PromptBehavior.Never`ADAL anger att användaren inte ska uppmanas att logga in och ADAL i stället utlösa ett undantag om det inte går att returnera en token.

```csharp
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
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

Grattis! Nu har en aktiv .NET WPF-program som har möjlighet att autentisera användare, på ett säkert sätt anropa webb-API: er med hjälp av OAuth 2.0 och få grundläggande information om användaren.  Om du inte redan gjort nu är det dags att fylla i din klient med vissa användare.  Kör appen DirectorySearcher och logga in med något av dessa användare.  Sök efter andra användare baserat på deras UPN.  Stäng appen och kör den igen.  Observera hur användarens session förblir intakt.  Logga ut och logga in igen som en annan användare.

ADAL gör det enkelt att införliva alla dessa vanliga identity-funktioner i programmet.  Det hand tar om ändrad arbetet åt dig - hantering av cache, OAuth protokollstöd presentera användaren med en inloggning UI, uppdatera token har upphört att gälla och mycket mer.  Allt du behöver veta är ett enda API-anrop `authContext.AcquireTokenAsync(...)`.

Referens tillhandahålls det slutförda exemplet (utan dina konfigurationsvärden) [här](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Du kan nu gå vidare till fler scenarier.  Du kanske vill prova:

[Skydda ett .NET-webb-API med Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

