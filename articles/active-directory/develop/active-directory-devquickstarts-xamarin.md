---
title: "Azure AD Xamarin komma igång | Microsoft Docs"
description: "Skapa Xamarin-program som integreras med Azure AD för inloggning och anropa Azure AD-skyddade API: er med hjälp av OAuth."
services: active-directory
documentationcenter: xamarin
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 54ee403f283bc5dc79911e2e813dd513ff595828
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="integrate-azure-ad-with-xamarin-apps"></a>Integrera Azure AD med Xamarin-appar
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Du kan skriva mobila appar med Xamarin, i C# som kan köras på iOS, Android och Windows (mobila enheter och datorer). Om du utvecklar en app med Xamarin, gör du enkelt att autentisera användare med sina Azure AD-konton med hjälp av Azure Active Directory (AD Azure). Appen kan också säkert att använda alla webb-API som skyddas av Azure AD, till exempel API: er för Office 365 eller Azure API.

För Xamarin-appar som behöver åtkomst till skyddade resurser, innehåller Azure AD Active Directory Authentication Library (ADAL). Det enda syftet med ADAL är att göra det enkelt för appar för att få åtkomst-token. För att demonstrera hur lätt det är den här artikeln visar hur man skapar DirectorySearcher appar som:

* Kör på iOS, Android, Windows-skrivbordet, Windows Phone och Windows Store.
* Använda en enda bärbar klassbiblioteket (PCL) för att autentisera användare och hämta token för Azure AD Graph API.
* Söka i en katalog för användare med ett angivet UPN.

## <a name="before-you-get-started"></a>Innan du börjar
* Hämta den [stommen av ett projekt](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip), eller hämta den [färdiga exemplet](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Varje version är en lösning i Visual Studio 2013.
* Du måste också en Azure AD-klient att skapa användare och registrera appen. Om du inte redan har en klient [Lär dig hur du skaffa en](active-directory-howto-tenant.md).

När du är klar följer du procedurerna i följande fyra avsnitt.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Steg 1: Ställ in din utvecklingsmiljö för Xamarin
Eftersom den här självstudiekursen innehåller projekt för iOS, Android och Windows, måste både Visual Studio och Xamarin. Om du vill skapa den nödvändiga miljön slutföras i [ställa upp och installera Visual Studio och Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) på MSDN. Anvisningarna omfattar material som du kan granska Läs mer om Xamarin medan du väntar för installationer ska slutföras.

När du har slutfört installationen, öppnar du lösningen i Visual Studio. Där hittar du sex projekt: fem plattformsspecifika projekt och en PCL, DirectorySearcher.cs som delas mellan alla plattformar.

## <a name="step-2-register-the-directorysearcher-app"></a>Steg 2: Registrera DirectorySearcher-app
Om du vill aktivera app att hämta token, måste du först registrera det i Azure AD-klienten och bevilja behörighet att komma åt Azure AD Graph API. Så här gör du:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på ditt konto på den översta raden. Sedan, under den **Directory** väljer du Active Directory-klient som du vill registrera appen.
3. Klicka på **fler tjänster** i det vänstra fönstret och välj sedan **Azure Active Directory**.
4. Klicka på **App registreringar**, och välj sedan **Lägg till**.
5. Att skapa en ny **internt klientprogram**, följ instruktionerna.
  * **Namnet** beskriver app till användare.
  * **Omdirigerings-URI** är en kombination av schemat och strängen som Azure AD som används för att returnera token svar. Ange ett värde (till exempel http://DirectorySearcher).
6. När du har slutfört registreringen, tilldelar Azure AD appen ett unikt-ID. Kopiera värdet från den **programmet** fliken, eftersom du behöver senare.
7. På den **inställningar** väljer **nödvändiga behörigheter**, och välj sedan **Lägg till**.
8. Välj **Microsoft Graph** som API. Under **delegerade behörigheter**, lägga till den **läsa katalogdata** behörighet.  
Den här åtgärden aktiverar appen att fråga Graph API för användare.

## <a name="step-3-install-and-configure-adal"></a>Steg 3: Installera och konfigurera ADAL
Nu när du har en app i Azure AD kan du installera ADAL och Skriv koden identitetsrelaterade. Om du vill aktivera ADAL att kommunicera med Azure AD, ger det viss information om appregistrering.

1. Lägg till ADAL DirectorySearcher projektet med hjälp av Package Manager-konsolen.

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    `

    Observera att två biblioteket referenser läggs till varje projekt: PCL-delen av ADAL och en plattformsspecifik del.
2. Öppna DirectorySearcher.cs i DirectorySearcherLib-projektet.
3. Ersätt klassen medlemsvärden med värden som du angav i Azure-portalen. Din kod refererar till dessa värden när den använder ADAL.

  * Den *klient* är domänen för din Azure AD-klient (till exempel contoso.onmicrosoft.com).
  * Den *clientId* är klient-ID för appen, som du kopierade från portalen.
  * Den *returnUri* är omdirigerings-URI som du angav på portalen (till exempel http://DirectorySearcher).

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Steg 4: Använda ADAL att hämta token från Azure AD
Nästan alla appens autentiseringslogiken ligger i `DirectorySearcher.SearchByAlias(...)`. Allt som behövs i plattformsspecifika-projekt är att skicka en kontextuella parameter till den `DirectorySearcher` PCL.

1. Öppna DirectorySearcher.cs och Lägg sedan till en ny parameter till den `SearchByAlias(...)` metoden. `IPlatformParameters`är parametern kontextuella som kapslar in plattformsspecifika objekten som ADAL behöver för att utföra autentiseringen.

    ```C#
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Initiera `AuthenticationContext`, vilket är den primära klassen av ADAL.  
Den här åtgärden klarar ADAL koordinaterna kommunicera med Azure AD.
3. Anropa `AcquireTokenAsync(...)`, som tar emot den `IPlatformParameters` objekt och anropar autentiseringsflödet som krävs för att returnera en token till appen.

    ```C#
    ...
        AuthenticationResult authResult = null;
        try
        {
            AuthenticationContext authContext = new AuthenticationContext(authority);
            authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
        }
        catch (Exception ee)
        {
            results.Add(new User { error = ee.Message });
            return results;
        }
    ...
    ```

    `AcquireTokenAsync(...)`försöker först att returnera en token för den begärda resursen (Graph API i det här fallet) utan att användarna anger sina autentiseringsuppgifter (via cachelagring eller uppdatera gamla token). Vid behov visar den användare inloggningssidan för Azure AD före hämtning av den begärda token.
4. Koppla den åtkomst-token till Graph API-begäran i den **auktorisering** huvud:

    ```C#
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

Det är allt för den `DirectorySearcher` PCL och appen datorns identitetsrelaterade kod. Allt som återstår är att anropa den `SearchByAlias(...)` metod i vyer för varje plattform och vid behov för att lägga till kod för att korrekt hantera UI-livscykeln.

### <a name="android"></a>Android
1. Lägga till ett anrop till MainActivity.cs, `SearchByAlias(...)` Klicka på knappen hanterare:

    ```C#
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Åsidosätta den `OnActivityResult` livscykel metod för att vidarebefordra någon autentisering omdirigeras till en lämplig metod. ADAL innehåller en hjälpmetod för detta i Android:

    ```C#
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Windows-skrivbordet
Gör ett anrop till i MainWindow.xaml.cs, `SearchByAlias(...)` genom att skicka en `WindowInteropHelper` på skrivbordet `PlatformParameters` objekt:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
I DirSearchClient_iOSViewController.cs iOS `PlatformParameters` objektet tar en referens till View-Controller:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows Universal
Öppna MainPage.xaml.cs i den universella Windows- och sedan implementera den `Search` metoden. Den här metoden använder en hjälpmetod i ett projekt för att uppdatera Användargränssnittet vid behov.

```C#
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

## <a name="whats-next"></a>Nästa steg
Nu har du en fungerande Xamarin-app som kan autentisera användare och på ett säkert sätt anropa webb-API: er med hjälp av OAuth 2.0 över fem olika plattformar.

Om du inte redan har fyllts i din klient med användare, nu är det dags att göra det.

1. Kör appen DirectorySearcher och logga sedan in med en användare.
2. Sök efter andra användare baserat på deras UPN.

ADAL gör det enkelt att lägga till vanliga identity-funktioner i appen. Det tar hand om ändrad arbetet, till exempel hantering av cache OAuth protokollstöd presenterar en inloggning Användargränssnittet för användaren och uppdatera gått token. Du behöver bara ett enda API-anrop, `authContext.AcquireToken*(…)`.

För referens, ladda ned den [färdiga exemplet](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (utan dina konfigurationsvärden).

Du kan nu gå vidare till ytterligare identitet scenarier. Till exempel försöka [skydda ett .NET-webb-API med Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
