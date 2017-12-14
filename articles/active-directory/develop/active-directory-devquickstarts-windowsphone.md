---
title: "Azure AD-Windows Phone komma igång | Microsoft Docs"
description: "Hur du skapar en Windows Phone-program som kan integreras med Azure AD för inloggning och Azure AD-anrop skyddade API: er som använder sig av OAuth."
services: active-directory
documentationcenter: windows
author: jmprieur
manager: mtillman
editor: 
ms.assetid: 66f5ac20-5e1f-4b9d-bb99-9b3305e26416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5edaee6a4adedc440b16e936462df5e224fe8609
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-azure-ad-with-a-windows-phone-app"></a>Integrera Azure AD med en Windows Phone-App
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

> [!NOTE]
> Windows Phone 8.1 och projekt i tidigare versioner stöds inte i Visual Studio 2017.  Mer information finns i [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Visual Studio 2017 – målplattform och plattformskompatibilitet).

Om du utvecklar en app för Windows Phone 8.1, Azure AD att gör det lätt att autentisera användarna med deras Active Directory-konton.  Det gör också ditt program att använda alla webb-API som skyddas av Azure AD, till exempel API: er för Office 365 eller Azure API på ett säkert sätt.

> [!NOTE]
> Det här kodexemplet använder ADAL version 2.0.  För den senaste tekniken, kanske du vill prova i stället vår [Windows Universal självstudier med ADAL v3.0](active-directory-devquickstarts-windowsstore.md).  Om du verkligen skapar en app för Windows Phone 8.1 är på rätt plats.  ADAL v2.0 fortfarande stöds och det rekommenderade sättet att utveckla appar agianst Windows Phone 8.1 använder Azure AD.
> 
> 

Azure AD innehåller Active Directory Authentication Library eller ADAL för .NET interna klienter som behöver åtkomst till skyddade resurser.  ADALS uteslutande i livslängd är att göra det lättare för din app för att få åtkomst-token.  För att demonstrera hur lätt det är ska här vi skapa en ”Directory användaren” Windows Phone 8.1-app som:

* Hämtar åtkomst till token för att anropa en Azure AD Graph API med hjälp av den [OAuth 2.0-autentiseringsprotokollet](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Söker en katalog för användare med ett angivet UPN.
* Användare loggar ut.

För att skapa fullständiga fungerande program måste du:

1. Registrera ditt program med Azure AD.
2. Installera och konfigurera ADAL.
3. Använd ADAL för att hämta token från Azure AD.

Du kommer igång [ladda ned stommen av ett projekt](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/skeleton.zip) eller [hämta det slutförda exemplet](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Varje är en lösning för Visual Studio 2013.  Du måste också en Azure AD-klient som du kan skapa användare och registrera ett program.  Om du inte redan har en klient [Lär dig hur du skaffa en](active-directory-howto-tenant.md).

## <a name="1-register-the-directory-searcher-application"></a>1. Registrera Directory användaren program
Om du vill aktivera din app att hämta token måste du måste först registrera det i Azure AD-klienten och bevilja behörighet att komma åt Azure AD Graph-API:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på den översta raden på ditt konto och under den **Directory** Välj Active Directory-klient som du vill registrera ditt program.
3. Klicka på **fler tjänster** i den vänstra nav och välj **Azure Active Directory**.
4. Klicka på **App registreringar** och välj **Lägg till**.
5. Följ anvisningarna och skapa en ny **internt klientprogram**.
  * Den **namn** av programmet beskriva programmet till slutanvändare
  * Den **omdirigerings-Uri** är en kombination av schemat och strängen som Azure AD för att returnera token svar.  Ange en platshållarvärde för nu, t.ex. `http://DirectorySearcher`.  Vi kommer att ersätta det här värdet senare.
6. När du har slutfört registreringen tilldelas AAD appen ett unikt program-ID.  Du behöver det här värdet i nästa avsnitt så kopiera den från programfliken.
7. Från den **inställningar** väljer **nödvändiga behörigheter** och välj **Lägg till**. Välj den **Microsoft Graph** som API och Lägg till den **läsa katalogdata** behörighet under **delegerade behörigheter**.  Detta gör att programmet att fråga Graph API för användare.

## <a name="2-install--configure-adal"></a>2. Installera och konfigurera ADAL
Nu när du har ett program i Azure AD kan du installera ADAL och Skriv koden identitetsrelaterade.  Du måste tillhandahålla information om din appregistrering för ADAL för att kunna kommunicera med Azure AD.

* Börja genom att lägga till ADAL DirectorySearcher projektet med Package Manager-konsolen.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* Öppna i projektet DirectorySearcher `MainPage.xaml.cs`.  Ersätter värdena i den `Config Values` region för att återspegla de värden du matar in i Azure Portal.  Koden ska referera till dessa värden när den använder ADAL.
  * Den `tenant` är domänen för din Azure AD-klient, t.ex. contoso.onmicrosoft.com
  * Den `clientId` är clientId för ditt program som du kopierade från portalen.
* Nu måste du identifiera återanrop URI: n för Windows Phone-app.  Ange en brytpunkt på den här raden i den `MainPage` metoden:

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
* Kör appen och kopiera reserverats värdet för `redirectUri` när brytpunkten träffar.  Det bör se ut ungefär så

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

* Tillbaka på den **konfigurera** fliken i ditt program i Azure-hanteringsportalen, Ersätt värdet för den **RedirectUri** med det här värdet.  

## <a name="3-use-adal-to-get-tokens-from-aad"></a>3. Använd ADAL för att hämta token från AAD
Den grundläggande principen bakom ADAL är att när din app behöver en åtkomst-token kan bara anropas `authContext.AcquireToken(…)`, och ADAL gör resten.  

* Det första steget är att initiera appens `AuthenticationContext` -ADAL vars primära klassen.  Det är där du skickar ADAL koordinaterna behöver kommunicera med Azure AD och hur det kan cachelagra token.

```C#
public MainPage()
{
    ...

    // ADAL for Windows Phone 8.1 builds AuthenticationContext instances through a factory
    authContext = AuthenticationContext.CreateAsync(authority).GetResults();
}
```

* Leta reda på `Search(...)` metod som ska anropas när användaren cliks ”Sök” knappen i appens användargränssnitt.  Den här metoden gör en GET-begäran till Azure AD Graph API frågan för användare vars UPN-namnet börjar med den angivna söktermen.  Men om du vill fråga Graph API, måste du inkludera ett access_token i den `Authorization` huvud för begäran - detta är där ADAL kommer in.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...

    // Try to get a token without triggering any user prompt.
    // ADAL will check whether the requested token is in ADAL's token cache or can otherwise be obtained without user interaction.
    AuthenticationResult result = await authContext.AcquireTokenSilentAsync(graphResourceId, clientId);
    if (result != null && result.Status == AuthenticationStatus.Success)
    {
        // A token was successfully retrieved.
        QueryGraph(result);
    }
    else
    {
        // Acquiring a token without user interaction was not possible.
        // Trigger an authentication experience and specify that once a token has been obtained the QueryGraph method should be called
        authContext.AcquireTokenAndContinue(graphResourceId, clientId, redirectURI, QueryGraph);
    }
}
```
* Om interaktiv autentisering krävs ADAL använder Windows Phone Web Authentication Service Broker (Windows Adressbok) och [fortsättning modellen](http://www.cloudidentity.com/blog/2014/06/16/adal-for-windows-phone-8-1-deep-dive/) att visa Azure AD-inloggning på sidan.  När användaren loggar in, din app behöver skicka ADAL resultaten av Windows Adressbok interaktion.  Detta är så enkelt som att implementera den `ContinueWebAuthentication` gränssnitt:

```C#
// This method is automatically invoked when the application
// is reactivated after an authentication interaction through WebAuthenticationBroker.
public async void ContinueWebAuthentication(WebAuthenticationBrokerContinuationEventArgs args)
{
    // pass the authentication interaction results to ADAL, which will
    // conclude the token acquisition operation and invoke the callback specified in AcquireTokenAndContinue.
    await authContext.ContinueAcquireTokenAsync(args);
}
```

* Nu är det dags att använda den `AuthenticationResult` som ADAL tillbaka till din app.  I den `QueryGraph(...)` motringning, bifoga access_token som du har köpt till GET-begäran i Authorization-huvud:

```C#
private async void QueryGraph(AuthenticationResult result)
{
    if (result.Status != AuthenticationStatus.Success)
    {
        MessageDialog dialog = new MessageDialog(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", result.Error, result.ErrorDescription), "Sorry, an error occurred while signing you in.");
        await dialog.ShowAsync();
    }

    // Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

    ...
}
```
* Du kan också använda den `AuthenticationResult` objekt att visa information om användaren i din app. I den `QueryGraph(...)` -metoden använder resultatet för att visa användarens id på sidan:

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
* Slutligen kan du använda ADAL för att logga användaren utanför programmet samt.  När användaren klickar på knappen ”Logga ut” vi vill se till att nästa anrop till `AcquireTokenSilentAsync(...)` misslyckas.  Det är lika enkelt som att rensa cacheminnet token med ADAL:

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Grattis! Nu har en aktiv Windows Phone-app som har möjlighet att autentisera användare, på ett säkert sätt anropa webb-API: er med hjälp av OAuth 2.0 och få grundläggande information om användaren.  Om du inte redan gjort nu är det dags att fylla i din klient med vissa användare.  Kör appen DirectorySearcher och logga in med något av dessa användare.  Sök efter andra användare baserat på deras UPN.  Stäng appen och kör den igen.  Observera hur användarens session förblir intakt.  Logga ut och logga in igen som en annan användare.

ADAL gör det enkelt att införliva alla dessa vanliga identity-funktioner i programmet.  Det hand tar om ändrad arbetet åt dig - hantering av cache, OAuth protokollstöd presentera användaren med en inloggning UI, uppdatera token har upphört att gälla och mycket mer.  Allt du behöver veta är ett enda API-anrop `authContext.AcquireToken*(…)`.

Referens tillhandahålls det slutförda exemplet (utan dina konfigurationsvärden) [här](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Du kan nu gå vidare till ytterligare identitet scenarier.  Du kanske vill prova:

[Skydda ett .NET-webb-API med Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

