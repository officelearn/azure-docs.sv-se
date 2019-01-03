---
title: Autentisering, registrering för, Redigera profil i Azure Active Directory B2C | Microsoft Docs
description: Hur du skapar ett Windows-skrivbordsprogram som innehåller inloggning, registrering, och profilhantering med hjälp av Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 43da5b32fe3ad8891f89544d0f9bdbd1d4d127d0
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53606190"
---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C: Skapa en Windows-skrivbordsapp
Med hjälp av Azure Active Directory (Azure AD) B2C kan du lägga till kraftfulla självbetjäning Identitetshantering till din skrivbordsapp i några korta steg. Den här artikeln visar hur du skapar en app för .NET Windows Presentation Foundation (WPF) ”uppgiftslista” som innehåller användarens registrering och inloggning och profilhantering. Appen innehåller stöd för registrering och inloggning med ett användarnamn eller e-post. Det omfattar även stöd för registrering och inloggning med konton i sociala medier, till exempel Facebook och Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Skaffa en Azure AD B2C-katalog
Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient. En katalog är en container för alla användare, appar, grupper och mer. Om du inte redan har en B2C-katalog [skapar du en](active-directory-b2c-get-started.md) innan du fortsätter den här guiden.

## <a name="create-an-application"></a>Skapa ett program
Nu måste du skapa en app i B2C-katalogen. Det ger Azure AD den information som tjänsten behöver för att kommunicera säkert med din app. Du skapar en app genom att följa [dessa anvisningar](active-directory-b2c-app-registration.md). Se till att:

* Inkludera en **inbyggd klient** i programmet.
* Kopiera den **omdirigerings-URI** `urn:ietf:wg:oauth:2.0:oob`. Den är standard-URL:en för det här kodexemplet.
* Kopiera **program-ID:t** som har tilldelats din app. Du behöver den senare.

## <a name="create-your-policies"></a>Skapa principer
I Azure AD B2C definieras varje användarupplevelse av en [princip](active-directory-b2c-reference-policies.md). Det här kodexemplet innehåller tre identitetsupplevelser: registrering, inloggning och profilredigering. Du behöver skapa en princip för varje typ. Mer information finns i den [referensartikeln om principer](active-directory-b2c-reference-policies.md#create-a-sign-up-user-flow). Tänk på följande när du skapar de tre principerna:

* Välj antingen **Registrering med användar-ID** eller **Registrering med e-postadress** i bladet för identitetsproviders.
* Välj **Visningsnamn** och andra registreringsattribut i registreringsprincipen.
* Välj **Visningsnamn** och **Objekt-ID** som programanspråk för varje princip. Du kan också välja andra anspråk.
* Kopiera **namnet** på varje princip när du har skapat den. Det bör ha prefixet `b2c_1_`. Du behöver principnamnen senare.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

När du har skapat de tre principerna är du redo att bygga din app.

## <a name="download-the-code"></a>Ladda ned koden
Koden för den här självstudiekursen [finns på GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Om du vill bygga exemplet allteftersom kan du [ladda ned stommen av ett projekt som en ZIP-fil](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Du kan också klona stommen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

Den färdiga appen finns också [som en ZIP-fil](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) eller i `complete`-grenen för samma centrallager.

När du har laddat ned exempelkoden öppnar du SLN-filen i Visual Studio för att sätta igång. Den `TaskClient` projektet är WPF-program som användaren interagerar med. För den här självstudien anropas en backend-uppgift webb-API, Azure och lagrar varje användares att göra-lista. Du behöver inte skapa webb-API, vi har redan det ska fungera för dig.

Om du vill veta hur ett webb-API på ett säkert sätt autentiserar begäranden med hjälp av Azure AD B2C kan ta en titt på [webb-API: komma igång artikel](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Köra principer
Appen kan kommunicera med Azure AD B2C genom att skicka autentiseringsmeddelanden som anger den princip som de vill att köra som en del av HTTP-begäran. För .NET skrivbordsprogram, kan du använda förhandsversionen av Microsoft Authentication Library (MSAL) för att skicka meddelanden för OAuth 2.0-autentisering, köra principer och hämta token som anropa webb-API: er.

### <a name="install-msal"></a>Installera MSAL
Lägg till MSAL till den `TaskClient` projekt med hjälp av Visual Studio Package Manager-konsolen.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Ange din B2C-information
Öppna filen `Globals.cs` och Ersätt varje egenskapsvärden med dina egna. Denna klass används i hela `TaskClient` till som ofta används referensvärden.

```csharp
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="create-the-publicclientapplication"></a>Skapa PublicClientApplication
Den primära klassen av MSAL är `PublicClientApplication`. Den här klassen representerar ditt program i Azure AD B2C-system. När app-initalizes skapar en instans av `PublicClientApplication` i `MainWindow.xaml.cs`. Detta kan användas i hela fönstret.

```csharp
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default. Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>Starta ett flöde för registrering
När en användare väljer att loggar in, som du vill initiera en registrering flöde som använder registreringsprincip som du skapade. Med MSAL kan du bara anropa `pca.AcquireTokenAsync(...)`. De parametrar som du skickar till `AcquireTokenAsync(...)` avgöra vilka token visas den princip som används i autentiseringsbegäran och mycket annat.

```csharp
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API). Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }

            MessageBox.Show(message);
        }

        return;
    }
}
```

### <a name="initiate-a-sign-in-flow"></a>Starta ett flöde för inloggning
Du kan starta ett flöde som loggar in på samma sätt som du startar ett flöde för registrering. När en användare loggar in, att MSAL, samma anropa den här gången med hjälp av inloggnings-principen:

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Starta ett flöde för Redigera-profil
Igen, kan du köra en princip för Redigera-profil på samma sätt:

```csharp
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

Alla dessa fall MSAL antingen returnerar en token i `AuthenticationResult` eller genererar ett undantag. Varje gång du får en token från MSAL, som du kan använda den `AuthenticationResult.User` objektet uppdateras informationen i appen, till exempel Användargränssnittet. ADAL cachelagrar också token för användning i andra delar av programmet.

### <a name="check-for-tokens-on-app-start"></a>Sök efter token på app-start
Du kan också använda MSAL för att hålla reda på användarens inloggning status. I den här appen vill vi att användaren ska förbli inloggade när de stänger appen och öppnar det igen. I den `OnInitialized` Åsidosätt, använder MSAL'S `AcquireTokenSilent` metod för att söka efter cachelagrade token:

```csharp
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache. Proceed without calling the To Do list service.
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
    return;
}
```

## <a name="call-the-task-api"></a>Anropa aktivitetens API
Du har nu använt MSAL för att köra principer och hämta token. Om du vill använda en dessa tokens för att anropa aktivitetens API kan du igen kan använda MSAL'S `AcquireTokenSilent` metod för att söka efter cachelagrade token:

```csharp
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
        }
        else
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }
            MessageBox.Show(message);
        }

        return;
    }
    ...
```

När anropet till `AcquireTokenSilentAsync(...)` lyckas och en token har hittats i cacheminnet, du kan lägga till denna token till den `Authorization` till HTTP-huvud. Uppgiften webb-API kommer att använda den här rubriken för att autentisera begäran att läsa användarens att göra-lista:

```csharp
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Användaren logga ut
Slutligen kan du kan använda MSAL för att avsluta en användarsession med appen när användaren väljer **logga ut**. När du använder MSAL kan görs detta genom att avmarkera alla tokens från token-cache:

```csharp
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Kör exempelappen
Slutligen skapar och kör exemplet. Registrera dig för appen genom att använda en e-postadress eller användarnamn namn. Logga ut och logga in igen som samma användare. Redigera användarens profil. Logga ut och logga med hjälp av en annan användare.

## <a name="add-social-idps"></a>Lägg till sociala IDP: er
Appen stöder för närvarande endast användaren registrera sig och logga in som använder **lokala konton**. Det här är konton som lagras i din B2C-katalog som använder ett användarnamn och lösenord. Med hjälp av Azure AD B2C kan du lägga till stöd för andra identitetsleverantörer (IDP) utan att ändra någon av din kod.

Börja genom att följa instruktionerna i följande artiklar om du vill lägga till sociala IDP: er i din app. För varje IDP som du vill kunna använda, måste du registrera ett program i systemet och få ett klient-ID.

* [Konfigurera Facebook som en IDP](active-directory-b2c-setup-fb-app.md)
* [Konfigurera Google som en IDP](active-directory-b2c-setup-goog-app.md)
* [Konfigurera Amazon som en IDP](active-directory-b2c-setup-amzn-app.md)
* [Konfigurera LinkedIn som en IDP](active-directory-b2c-setup-li-app.md)

När du lägger till identitetsleverantör man till din B2C-katalog måste du redigera var och en av dina tre principer att inkludera de nya IDP: er, enligt beskrivningen i den [referensartikeln om principer](active-directory-b2c-reference-policies.md). När du har sparat dina principer kan du köra appen igen. Du bör se de nya IDP: er som har lagts till som inloggning och registreringsalternativ i var och en av din identitet inträffar.

Du kan experimentera med dina principer och notera effekterna på din exempelapp. Lägg till eller ta bort IDP: er, manipulera programanspråken eller ändra registreringsattribut. Experimentera tills du kan se hur principer, begäranden om autentisering och MSAL knyta ihop.

Referens är färdiga exemplet [tillhandahålls som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Du kan också klona det från GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
