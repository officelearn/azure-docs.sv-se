---
title: "Azure AD Windows Store komma igång | Microsoft Docs"
description: "Skapa Windows Store-appar som integreras med Azure AD för inloggning och anropar Azure AD skyddade API: er som använder sig av OAuth."
services: active-directory
documentationcenter: windows
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 3b96a6d1-270b-4ac1-b9b5-58070c896a68
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 09/16/2016
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6b5189dc06d7f8b0ed4426944948b904feba847e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-ad-with-windows-store-apps"></a>Integrera Azure AD med Windows Store-appar
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

> [!NOTE]
> Windows Store 8.1 och tidigare version projekt stöds inte i Visual Studio-2017.  Mer information finns i [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Visual Studio 2017 – målplattform och plattformskompatibilitet).

Om du utvecklar appar för Windows Store, Azure Active Directory (Azure AD) att gör det lätt att autentisera användarna med deras Active Directory-konton. En app kan på ett säkert sätt använda alla webb-API som skyddas av Azure AD, till exempel API: er för Office 365 eller Azure API genom att integrera med Azure AD.

För Windows Store-program som behöver åtkomst till skyddade resurser, innehåller Azure AD Active Directory Authentication Library (ADAL). Det enda syftet med ADAL är att göra det enkelt för programmet för att få åtkomst-token. För att demonstrera hur lätt det är den här artikeln visar hur du skapar en DirectorySearcher Windows Store-app som:

* Hämtar åtkomst till token för att anropa Azure AD Graph-API med hjälp av den [OAuth 2.0-autentiseringsprotokollet](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Söker en katalog för användare med en viss användarens huvudnamn (UPN).
* Användare loggar ut.

## <a name="before-you-get-started"></a>Innan du börjar
* Hämta den [stommen av ett projekt](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip), eller hämta den [färdiga exemplet](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip). Varje version är en lösning i Visual Studio 2015.
* Du måste också en Azure AD-klient att skapa användare och registrera appen. Om du inte redan har en klient [Lär dig hur du skaffa en](active-directory-howto-tenant.md).

Följ procedurerna i följande tre avsnitt när du är klar.

## <a name="step-1-register-the-directorysearcher-app"></a>Steg 1: Registrera DirectorySearcher-app
Om du vill aktivera app att hämta token, måste du först registrera det i Azure AD-klienten och bevilja behörighet att komma åt Azure AD Graph API. Så här gör du:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på ditt konto på den översta raden. Sedan, under den **Directory** väljer du Active Directory-klient som du vill registrera appen.
3. Klicka på **fler tjänster** i det vänstra fönstret och välj sedan **Azure Active Directory**.
4. Klicka på **App registreringar**, och välj sedan **Lägg till**.
5. Följ anvisningarna för att skapa en **internt klientprogram**.
  * **Namnet** beskriver app till användare.
  * **Omdirigerings-URI** är en kombination av schemat och strängen som Azure AD som används för att returnera token svar. Ange ett platshållarvärde för nu (till exempel **http://DirectorySearcher**). Du måste ersätta värdet senare.
6. När du har slutfört registreringen, tilldelar Azure AD appen ett unikt-ID. Kopiera värdet på den **programmet** fliken, eftersom du behöver senare.
7. På den **inställningar** väljer **nödvändiga behörigheter**, och välj sedan **Lägg till**.
8. För den **Azure Active Directory** app markerar **Microsoft Graph** som API.
9. Under **delegerade behörigheter**, lägga till den **åtkomst till katalogen som den inloggade användaren** behörighet. På så sätt kan programmet för att fråga Graph API för användare.

## <a name="step-2-install-and-configure-adal"></a>Steg 2: Installera och konfigurera ADAL
Nu när du har en app i Azure AD kan du installera ADAL och Skriv koden identitetsrelaterade. Om du vill aktivera ADAL att kommunicera med Azure AD, ger det viss information om appregistrering.

1. Lägg till ADAL DirectorySearcher projektet med hjälp av Package Manager-konsolen.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

2. Öppna MainPage.xaml.cs i DirectorySearcher-projektet.
3. Ersätter värdena i den **konfigurationsvärden** region med värden som du angav i Azure-portalen. Din kod refererar till dessa värden när den använder ADAL.
  * Den *klient* är domänen för din Azure AD-klient (till exempel contoso.onmicrosoft.com).
  * Den *clientId* är klient-ID för appen, som du kopierade från portalen.
4. Nu måste du identifiera motringning URI för Windows Store-app. Ange en brytpunkt på den här raden i den `MainPage` metoden:
    ```
    redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
    ```
5. Skapa lösningen, se till att alla paketet refererar till har återställts. Om några paket saknas, öppna NuGet Package Manager och återställa dem.
6. Kör appen och kopiera värdet för `redirectUri` när brytpunkten träffar. Värdet ska se ut ungefär så här:

    ```
    ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
    ```

7. Tillbaka på den **inställningar** fliken app i Azure-portalen, lägga till en **RedirectUri** med föregående värde.  

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Steg 3: Använda ADAL att hämta token från Azure AD
Den grundläggande principen bakom ADAL är att när appen behöver en åtkomst-token kan bara anropas `authContext.AcquireToken(…)`, och ADAL gör resten.  

1. Initiera appens `AuthenticationContext`, vilket är den primära klassen av ADAL. Den här åtgärden klarar ADAL koordinaterna behöver kommunicera med Azure AD och hur det kan cachelagra token.

    ```C#
    public MainPage()
    {
        ...

        authContext = new AuthenticationContext(authority);
    }
    ```

2. Leta upp den `Search(...)` metod som anropas när användaren klickar på den **Sök** knapp i appens användargränssnitt. Den här metoden gör en get-begäran till Azure AD Graph API frågan för användare vars UPN-namnet börjar med den angivna söktermen. Inkludera en åtkomst-token i en begäran om du vill fråga Graph API **auktorisering** huvud. Det är där ADAL kommer in.

    ```C#
    private async void Search(object sender, RoutedEventArgs e)
    {
        ...
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "authentication_canceled")
            {
                ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
            }
            return;
        }
        ...
    }
    ```
    När appen begär en token genom att anropa `AcquireTokenAsync(...)`, ADAL försöker denna att returnera en token utan att fråga användaren om autentiseringsuppgifter. Om ADAL avgör att användaren måste logga in att hämta en token, den visar inloggning dialogrutan samlar in användarens autentiseringsuppgifter och returnerar en token efter att autentiseringen lyckas. Om ADAL inte kan returnera en token av någon anledning den *AuthenticationResult* status är ett fel.
3. Nu är det dags att använda den åtkomst-token som du precis har köpt. Även i den `Search(...)` metod, bifoga token till get-begäran för Graph API i den **auktorisering** huvud:

    ```C#
    // Add the access token to the Authorization header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

    ```
4. Du kan använda den `AuthenticationResult` objekt att visa information om användaren i appen, till exempel det användar-ID:

    ```C#
    // Update the page UI to represent the signed-in user
    ActiveUser.Text = result.UserInfo.DisplayableId;
    ```
5. Du kan också använda ADAL för att logga in användare utanför appen. När användaren klickar på **logga ut** knappen, se till att nästa anrop till `AcquireTokenAsync(...)` visas en vy för inloggning. Den här åtgärden är lika enkelt som att rensa cacheminnet token med ADAL:

    ```C#
    private void SignOut()
    {
        // Clear session state from the token cache.
        authContext.TokenCache.Clear();

        ...
    }
    ```

## <a name="whats-next"></a>Nästa steg
Nu har du en fungerande Windows Store-app som kan autentisera användare, på ett säkert sätt anropa webb-API: er med hjälp av OAuth 2.0 och få grundläggande information om användaren.

Om du inte redan har fyllts i din klient med användare, nu är det dags att göra det.
1. Kör appen DirectorySearcher och logga sedan in med en användare.
2. Sök efter andra användare baserat på deras UPN.
3. Stäng appen och köra den igen. Observera hur användarens session förblir intakt.
4. Logga ut genom att högerklicka på att visa det nedre fältet och sedan logga in igen som en annan användare.

ADAL gör det enkelt att lägga till alla vanliga identitet funktionerna i appen. Det tar hand om ändrad arbetet, till exempel hantering av cache OAuth protokollstöd presenterar en inloggning Användargränssnittet för användaren och uppdatera gått token. Du behöver bara ett enda API-anrop, `authContext.AcquireToken*(…)`.

För referens, ladda ned den [färdiga exemplet](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip) (utan dina konfigurationsvärden).

Du kan nu gå vidare till ytterligare identitet scenarier. Till exempel försöka [skydda ett .NET-webb-API med Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
