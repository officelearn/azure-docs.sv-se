---
title: Azure AD-Xamarin komma igång | Microsoft Docs
description: 'Skapa Xamarin-program som integreras med Azure AD för att logga in och anropa Azure AD-skyddade API: er med OAuth.'
services: active-directory
documentationcenter: xamarin
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8af6846da78d12460b7866297c9802c5dab20a69
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967531"
---
# <a name="quickstart-build-a-xamarin-app-that-integrates-microsoft-sign-in"></a>Snabbstart: Skapa en Xamarin-app som integrerar Microsoft-inloggning

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Du kan skriva mobilappar med Xamarin i C# som kan köras på iOS, Android och Windows (mobila enheter och datorer). Om du bygger en app med Xamarin, gör du enkelt att autentisera användare med sina Azure AD-konton med hjälp av Azure Active Directory (AD Azure). Appen kan också på ett säkert sätt att använda alla webb-API som skyddas av Azure AD, som API: er för Office 365 eller Azure-API.

För Xamarin-appar som behöver åtkomst till skyddade resurser, tillhandahåller Azure AD Active Directory Authentication Library (ADAL). Den enda avsikten med ADAL är att göra det enkelt för appar för att få åtkomst-token. För att demonstrera hur lätt det är den här artikeln visar hur du skapar DirectorySearcher appar som:

* Kör på iOS, Android, Windows Desktop, Windows Phone och Windows Store.
* Använda en enda portabelt klassbibliotek (PCL) för att autentisera användare och hämta token för Azure AD Graph API.
* Söka i en katalog för användare med en viss UPN.

## <a name="prerequisites"></a>Förutsättningar

* Ladda ned den [stommen av ett projekt](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip), eller hämta den [färdiga exemplet](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Varje version är en Visual Studio 2013-lösning.
* Du behöver också en Azure AD-klient att skapa användare och registrera appen. Om du inte redan har en klient, [Lär dig hur du skaffa ett](quickstart-create-new-tenant.md).

När du är redo, följer du procedurerna i följande fyra avsnitt.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Steg 1: Ställ in din utvecklingsmiljö för Xamarin

Eftersom den här självstudien omfattar projekt för iOS, Android och Windows, måste både Visual Studio och Xamarin. För att skapa nödvändiga miljön, slutför du processen i [ange installation av Visual Studio och Xamarin och](https://msdn.microsoft.com/library/mt613162.aspx) på MSDN. Anvisningarna omfattar material som du kan granska om du vill veta mer om Xamarin under tiden du väntar för installationer ska slutföras.

När du har slutfört installationen, öppnar du lösningen i Visual Studio. Där hittar du sex projekt: fem plattformsspecifika projekt och en PCL, DirectorySearcher.cs som delas mellan alla plattformar.

## <a name="step-2-register-the-directorysearcher-app"></a>Steg 2: Registrera appen DirectorySearcher

Om du vill att appen ska kunna hämta token, måste du först registrera det i Azure AD-klienten och bevilja behörighet att komma åt Azure AD Graph API. Så här gör du:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på ditt konto i det översta fältet. Sedan, under den **Directory** väljer du Active Directory-klient där du vill registrera appen.
3. Klicka på **alla tjänster** i den vänstra rutan och välj sedan **Azure Active Directory**.
4. Klicka på **appregistreringar**, och välj sedan **Lägg till**.
5. Att skapa en ny **internt klientprogram**, följ anvisningarna.
  * **Namn på** beskriver appen till användare.
  * **Omdirigerings-URI** är en kombination av schema och strängen som Azure AD använder för att returnera tokensvar. Ange ett värde (till exempel http://DirectorySearcher).
6. När du har slutfört registreringen, tilldelar Azure AD appen ett unikt program-ID. Kopiera värdet från den **program** fliken eftersom du behöver senare.
7. På den **inställningar** väljer **nödvändiga behörigheter**, och välj sedan **Lägg till**.
8. Välj **Microsoft Graph** som API: et. Under **delegerade behörigheter**, lägga till den **läsa kataloginformation** behörighet. Den här åtgärden gör det möjligt för appen att fråga Graph-API: N för användare.

## <a name="step-3-install-and-configure-adal"></a>Steg 3: Installera och konfigurera ADAL

Nu när du har en app i Azure AD kan du skriva koden identitetsrelaterade installera ADAL. Om du vill aktivera ADAL för att kommunicera med Azure AD, ger du dem till viss information om registreringen.

1. Lägg till ADAL DirectorySearcher-projektet genom att använda Package Manager-konsolen.

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

    Observera att två referenser för bibliotek läggs till varje projekt: PCL-delen av ADAL och en plattformsspecifik del.
2. Öppna DirectorySearcher.cs i DirectorySearcherLib-projektet.
3. Ersätt klassen medlemsvärden med de värden som du angav i Azure-portalen. Din kod refererar till dessa värden när den använder ADAL.

  * Den *klient* är domänen för din Azure AD-klient (exempel: contoso.onmicrosoft.com).
  * Den *clientId* är klient-ID för appen, som du kopierade från portalen.
  * Den *returnUri* är omdirigerings-URI som du angav på portalen (till exempel http://DirectorySearcher).

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Steg 4: Använda ADAL för att hämta token från Azure AD

Nästan alla appens autentiseringslogiken ligger i `DirectorySearcher.SearchByAlias(...)`. Allt som behövs i de plattformsspecifika projekt är att skicka en sammanhangsberoende parametern till den `DirectorySearcher` PCL.

1. Öppna DirectorySearcher.cs och Lägg sedan till en ny parameter till den `SearchByAlias(...)` metoden. `IPlatformParameters` är sammanhangsberoende parametern som kapslar in plattformsspecifika objekten som ADAL krävs för att utföra autentiseringen.

    ```csharp
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Initiera `AuthenticationContext`, som är den primära klassen av ADAL. Den här åtgärden skickar ADAL koordinaterna som behövs för att kommunicera med Azure AD.
3. Anropa `AcquireTokenAsync(...)`, som accepterar den `IPlatformParameters` objekt och anropar autentiseringsflödet som krävs för att returnera en token till appen.

    ```csharp
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

    `AcquireTokenAsync(...)` först försöker denna att returnera en token för den begärda resursen (Graph API i det här fallet) utan att fråga användaren måste ange sina autentiseringsuppgifter (via cachelagring eller uppdatera gamla token). Vid behov visar den användare inloggningssidan för Azure AD innan du hämtar begärd token.
4. Koppla åtkomsttoken till Graph API-begäran i den **auktorisering** rubrik:

    ```csharp
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

Det är allt för den `DirectorySearcher` PCL och appen är identitetsrelaterade kod. Allt som återstår är att anropa den `SearchByAlias(...)` -metod i vyer för varje plattform och vid behov, lägga till kod för att korrekt hantera UI-livscykeln.

### <a name="android"></a>Android
1. I MainActivity.cs, lägger du till ett anrop till `SearchByAlias(...)` i knappen klickar du på hanterare:

    ```csharp
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Åsidosätta den `OnActivityResult` livscykel metod för att vidarebefordra någon autentisering omdirigeras till en lämplig metod. ADAL tillhandahåller en hjälpmetod för detta i Android:

    ```csharp
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Windows-skrivbordet

I MainWindow.xaml.cs, göra ett anrop till `SearchByAlias(...)` genom att skicka en `WindowInteropHelper` på skrivbordet `PlatformParameters` objekt:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
I DirSearchClient_iOSViewController.cs iOS `PlatformParameters` objektet följer en referens till View-Controller:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows Universal
Öppna MainPage.xaml.cs i Windows Universal och sedan implementera den `Search` metoden. Den här metoden använder en helper-metod i ett delat projekt för att uppdatera Användargränssnittet som behövs.

```csharp
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Nu har du en fungerande Xamarin-app som kan autentisera användare och på ett säkert sätt anropa webb-API: er med hjälp av OAuth 2.0 på fem olika plattformar.

## <a name="step-5-populate-your-tenant"></a>Steg 5: Lägg till i din klient 

Om du inte har redan fyllt i din klient med användare, är det dags att göra detta.

1. Kör DirectorySearcher appen och logga sedan in med en av användarna.
2. Sök efter andra användare baserat på deras UPN.

## <a name="next-steps"></a>Nästa steg

ADAL gör det enkelt att införliva vanliga identitetsfunktioner i appen. Det tar hand om ändrad arbetet åt dig, till exempel hantering av cache, stöd för OAuth-protokoll, presentera användaren med ett användargränssnitt, och uppdatera upphört att gälla token. Du behöver bara ett enda API-anrop `authContext.AcquireToken*(…)`.

* Ladda ned den [färdiga exemplet](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (utan dina konfigurationsvärden).
* Lär dig hur du [skydda ett .NET webb-API med Azure AD](quickstart-v1-dotnet-webapi.md).
