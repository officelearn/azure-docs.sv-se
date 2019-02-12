---
title: Komma igång med Azure AD Xamarin | Microsoft Docs
description: Skapa Xamarin-program som integreras med Azure AD för inloggning och anrop till Azure AD-skyddade API:er med hjälp av OAuth.
services: active-directory
documentationcenter: xamarin
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 42d0a068be1afb3e2f5a3fb1687556513b4582c6
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55693005"
---
# <a name="quickstart-build-a-xamarin-app-that-integrates-microsoft-sign-in"></a>Snabbstart: Skapa en Xamarin-app som integrerar Microsoft-inloggning

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Med Xamarin kan du skriva mobilappar i C# som kan köras på iOS, Android och Windows (mobila enheter och PC-datorer). Om du bygger en app med hjälp av Xamarin gör Azure Active Directory (Azure AD) det enkelt att autentisera användare med deras Azure AD-konton. Appen kan även på ett säkert sätt använda valfritt webb-API som skyddas av Azure AD, till exempel Office 365-API:er eller Azure API.

Azure AD tillhandahåller Active Directory Authentication Library (ADAL) till Xamarin-appar som behöver åtkomst till skyddade resurser. Det enda syftet med ADAL är att göra det enkelt för appar för att få åtkomsttoken. För att demonstrera hur enkelt det är visar den här artikeln hur du skapar DirectorySearcher-appar som:

* Körs på iOS, Android, Windows-skrivbordet, Windows Phone och Windows Store.
* Använder ett enda portabelt klassbibliotek (PCL) för att autentisera användare och hämta token för Azure AD Graph API.
* Söker i en katalog efter användare med ett visst UPN.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ladda ned [projektets stomme](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) eller ladda ned det [färdiga exemplet](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Varje nedladdning är en Visual Studio 2013-lösning.
* Du behöver även ha en Azure AD-klientorganisation där du kan skapa användare och registrera appen. Om du inte redan har en klientorganisation kan du [läsa om hur du skaffar en](quickstart-create-new-tenant.md).

När du är redo följer du procedurerna i följande fyra avsnitt.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Steg 1: Konfigurera din Xamarin-utvecklingsmiljö

Eftersom den här självstudien omfattar projekt för iOS, Android och Windows behöver du ha både Visual Studio och Xamarin. För att skapa den nödvändiga miljön slutför du processen i [Set up and install Visual Studio and Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) (Konfigurera och installera Visual Studio och Xamarin) på MSDN. Anvisningarna omfattar material som du kan granska om du vill veta mer om Xamarin medan du väntar på att installationerna ska slutföras.

När du har slutfört konfigurationen öppnar du lösningen i Visual Studio. Där hittar du sex projekt: fem plattformsspecifika projekt och en PCL, DirectorySearcher.cs, som delas mellan alla plattformar.

## <a name="step-2-register-the-directorysearcher-app"></a>Steg 2: Registrera DirectorySearcher-appen

För att göra så att appen kan få token behöver du först registrera den i din Azure AD-klientorganisation och bevilja den behörighet för åtkomst till Azure AD Graph API. Så här gör du:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på ditt konto i fältet längst upp. Under **kataloglistan** väljer du sedan den Active Directory-klientorganisation där du vill registrera appen.
3. Klicka på **Alla tjänster** i den vänstra fönsterrutan och välj sedan **Azure Active Directory**.
4. Klicka på **Appregistreringar** och välj sedan **Lägg till**.
5. Följ anvisningarna för att skapa ett nytt **internt klientprogram**.
  * **Namn** beskriver appen för användarna.
  * **Omdirigerings-URI** är ett schema och en strängkombination som Azure AD använder till att returnera tokensvar. Ange ett värde (till exempel http://DirectorySearcher).
6. När du har slutfört registreringen tilldelar Azure AD appen ett unikt program-ID. Kopiera värdet från fliken **Program** eftersom du behöver det senare.
7. På sidan **Inställningar** väljer du **Nödvändiga behörigheter** och sedan **Lägg till**.
8. Välj **Microsoft Graph** som API. Under **Delegerade behörigheter** lägger du till behörigheten **Läsa katalogdata**. Den här åtgärden gör det möjligt för appen att köra frågor mot Graph API för användare.

## <a name="step-3-install-and-configure-adal"></a>Steg 3: Installera och konfigurera ADAL

Nu när du har en app i Azure AD kan du installera ADAL och skriva din identitetsrelaterade kod. För att ADAL ska kunna kommunicera med Azure AD behöver du ge det viss information om appregistreringen.

1. Lägg till ADAL i DirectorySearcher-projektet med hjälp av Package Manager-konsolen.

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

    Observera att två biblioteksreferenser läggs till varje projekt: PCL-delen av ADAL och en plattformsspecifik del.
2. I projektet DirectorySearcherLib öppnar du DirectorySearcher.cs.
3. Ersätt klassmedlemsvärdena med de värden som du angav i Azure-portalen. Din kod refererar till de här värdena när den använder ADAL.

  * *tenant* är domänen för din Azure AD-klientorganisation (till exempel contoso.onmicrosoft.com).
  * *clientId* är klient-ID för appen som du kopierade från portalen.
  * *returnUri* är omdirigerings-URI som du angav i portalen (till exempel http://DirectorySearcher).

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Steg 4: Använda ADAL för att hämta token från Azure AD

Nästan allt av appens autentiseringslogik finns i `DirectorySearcher.SearchByAlias(...)`. Allt som behövs i de plattformsspecifika projekten är att skicka en sammanhangsberoende parameter till `DirectorySearcher`-PCL.

1. Öppna DirectorySearcher.cs och lägg sedan till en ny parameter i metoden `SearchByAlias(...)`. `IPlatformParameters` är den sammanhangsberoende parameter som kapslar in de plattformsspecifika objekt som ADAL behöver för att utföra autentiseringen.

    ```csharp
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Initiera `AuthenticationContext`, som är den primära klassen för ADAL. Den här åtgärden skickar ADAL de koordinater som behövs för kommunikation med Azure AD.
3. Anropa `AcquireTokenAsync(...)`, som accepterar objektet `IPlatformParameters` och anropar det autentiseringsflöde som krävs för en token ska returneras till appen.

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

    `AcquireTokenAsync(...)` försöker först att returnera en token för den begärda resursen (Graph API i det här fallet) utan att ombe användarna att ange sina autentiseringsuppgifter (via cachelagring eller uppdatering av gamla token). Vid behov visar den användarna Azure AD-inloggningssidan innan begärd token hämtas.
4. Koppla åtkomsttoken till Graph API-begäran i den **auktoriseringshuvudet**:

    ```csharp
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

Det var allt för `DirectorySearcher`-PCL och appens identitetsrelaterade kod. Allt som återstår är att anropa metoden `SearchByAlias(...)` i varje plattforms vyer och vid behov lägga till kod för att korrekt hantera UI-livscykeln.

### <a name="android"></a>Android
1. I MainActivity.cs lägger du till ett anrop i `SearchByAlias(...)` i hanteraren för knapptryck:

    ```csharp
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Åsidosätta livscykelmetoden `OnActivityResult` för att vidarebefordra autentiseringsomdirigeringar till lämplig metod. ADAL tillhandahåller en hjälpmetod för det här i Android:

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

I MainWindow.xaml.cs gör du ett anrop till `SearchByAlias(...)` genom att skicka en `WindowInteropHelper` i skrivbordets `PlatformParameters`-objekt:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
I DirSearchClient_iOSViewController.cs tar iOS-objektet `PlatformParameters` en referens till View Controller:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows Universal
I Windows Universal öppnar du MainPage.xaml.cs och implementerar sedan metoden `Search`. Den här metoden använder en hjälpmetod i ett delat projekt för att uppdatera användargränssnittet efter behov.

```csharp
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Nu har du en fungerande Xamarin-app som kan autentisera användare och på ett säkert sätt anropa webb-API:er med hjälp av OAuth 2.0 på fem olika plattformar.

## <a name="step-5-populate-your-tenant"></a>Steg 5: Fylla i din klientorganisation 

Om du inte redan har fyllt i klientorganisationen med användare är det dags att göra det nu.

1. Kör din DirectorySearcher-app och logga sedan in med någon av användarna.
2. Sök efter andra användare utifrån deras UPN.

## <a name="next-steps"></a>Nästa steg

ADAL gör det enkelt att införa vanliga identitetsfunktioner i appen. Det tar hand om allt grovjobb åt dig, såsom cachehantering, stöd för OAuth-protokoll, visa ett inloggningsgränssnitt för användaren och uppdatera token som gått ut. Du behöver bara känna till ett enda API-anrop: `authContext.AcquireToken*(…)`.

* Ladda ned det [färdiga exemplet](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (utan dina konfigurationsvärden).
* Lär dig [skydda ett .NET-webb-API med Azure AD](quickstart-v1-dotnet-webapi.md).
