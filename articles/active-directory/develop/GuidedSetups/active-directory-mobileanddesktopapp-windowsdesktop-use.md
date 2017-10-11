---
title: "Azure AD v2 Windows Desktop komma igång - använda | Microsoft Docs"
description: "Hur Windows Desktop .NET (XAML) program anropar en API som kräver åtkomst-token i Azure Active Directory v2 slutpunkten"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 826ba0a00b26993d4f37f0a8ce587d7bb77e7eb4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Använd Microsoft Authentication Library (MSAL) för att hämta en token för Microsoft Graph API

Det här avsnittet visar hur du använder MSAL för att hämta en token Microsoft Graph API.

1.  I `MainWindow.xaml.cs`, Lägg till referens för MSAL bibliotek i klassen:

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Ersätt <code>MainWindow</code> klassen kod med:
</li>
</ol>

```csharp
public partial class MainWindow : Window
{
    //Set the API Endpoint to Graph 'me' endpoint
    string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

    //Set the scope for API call to user.read
    string[] _scopes = new string[] { "user.read" };


    public MainWindow()
    {
        InitializeComponent();
    }

    /// <summary>
    /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
    /// </summary>
    private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
    {
        AuthenticationResult authResult = null;

        try
        {
            authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
        }
        catch (MsalUiRequiredException ex)
        {
            // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
            System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
            }
            catch (MsalException msalex)
            {
                ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
            }
        }
        catch (Exception ex)
        {
            ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
            return;
        }

        if (authResult != null)
        {
            ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Mer information
#### <a name="getting-a-user-token-interactive"></a>Hämta en användartoken interaktiva
Anropar den `AcquireTokenAsync` metoden resulterar i ett fönster som uppmanar användaren att logga in. Program kräver vanligen en användare att logga in interaktivt första gången de behöver för att få åtkomst till en skyddad resurs, eller när en tyst åtgärd att hämta en token misslyckas (t.ex. användarens lösenord har upphört att gälla).

#### <a name="getting-a-user-token-silently"></a>Hämta token för en användare tyst
`AcquireTokenSilentAsync`hanterar token förvärv av organisationer och förnyelse utan någon användarinteraktion. Efter `AcquireTokenAsync` körs för första gången `AcquireTokenSilentAsync` är vanliga metod för att hämta token som används för att komma åt skyddade resurser för efterföljande anrop - eftersom anrop till begära eller förnya token görs tyst.
Slutligen `AcquireTokenSilentAsync` misslyckas – t.ex. användaren har loggat ut eller har ändrat sitt lösenord på en annan enhet. När MSAL upptäcker att problemet kan lösas genom att kräva en interaktiv åtgärd, den utlöses en `MsalUiRequiredException`. Programmet kan hantera det här undantaget på två sätt:

1.  Gör ett anrop mot `AcquireTokenAsync` direkt, vilket innebär att användaren uppmanas att logga in. Det här mönstret används vanligtvis i Onlineprogram där det finns inget offline innehåll i programmet för användaren. Genereras av den här interaktiv installation används det här mönstret: du kan se den i åtgärden första gången du köra exemplet: eftersom ingen användare har använt programmet, `PublicClientApp.Users.FirstOrDefault()` innehåller ett null-värde och ett `MsalUiRequiredException` undantag. Koden i exemplet hanterar undantaget genom att anropa `AcquireTokenAsync` ledde till att användaren uppmanas att logga in.

2.  Program kan också göra en indikering för användaren som en interaktiv inloggning krävs, så att användaren kan välja att logga in rätt tidpunkt eller programmet kan försöka `AcquireTokenSilentAsync` vid ett senare tillfälle. Detta används vanligtvis när användaren kan använda andra funktioner i programmet utan att något stör – t.ex, det finns offline innehåll i programmet. I det här fallet användaren kan välja när de vill logga in till den skydda resursen eller uppdatera inaktuell information eller ditt program kan välja att försök `AcquireTokenSilentAsync` när nätverket återställs efter att ha tillfälligt otillgänglig.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Anropa använder token som du precis har köpt Microsoft Graph API

1. Lägg till den nya metoden nedan till dina `MainWindow.xaml.cs`. Metoden används för att göra en `GET` begäran mot Graph API med hjälp av ett auktorisera-huvud:

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```
<!--start-collapse-->
### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du skapar ett REST-anrop mot ett skyddade API

I det här exempelprogrammet i `GetHttpContentWithToken` metoden används för att en HTTP `GET` begäran mot en skyddad resurs som kräver ett token och returnera innehållet till anroparen. Den här metoden lägger till anskaffats token i den *HTTP Authorization-huvud*. Resursen för det här exemplet är Microsoft Graph API *mig* slutpunkt – som visar information om användarens profil.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Lägg till en metod för att logga ut användaren

1. Lägg till följande metod för att din `MainWindow.xaml.cs` logga ut användaren:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    if (App.PublicClientApp.Users.Any())
    {
        try
        {
            App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Mer information om utloggning

`SignOutButton_Click`tar bort användaren från MSAL användarcachen – detta talar effektivt MSAL för att den aktuella användaren har glömt så att en framtida begäran om att hämta en token lyckas bara om det görs för att interaktivt.
Även om programmet i det här exemplet har stöd för en enskild användare, stöder MSAL scenarier där flera konton kan vara inloggad på samma gång – ett exempel är ett e-postprogram där en användare har flera konton.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Visa grundläggande Information om Token

1. Lägg till följande metod för att till din `MainWindow.xaml.cs` att visa grundläggande information om token:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
        TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```
<!--start-collapse-->
### <a name="more-information"></a>Mer information

Token har köpt *OpenID Connect* också innehålla en liten del av information som är relevant för användaren. `DisplayBasicTokenInfo`Visar grundläggande information som finns i token: exempelvis användarens namn och ID, samt token upphör att gälla och den sträng som representerar åtkomst token sig själv. Den här informationen visas att se. Du kan träffa på *anropa Microsoft Graph API* knappen flera gånger och se att samma token återanvänts för efterföljande förfrågningar. Du kan också se förfallodatum förlängs när MSAL bestämmer det är dags att förnya token.
<!--end-collapse-->

