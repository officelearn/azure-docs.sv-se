---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 13497c8be578990b58cd6d6524eb0e945f8619c2
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59498434"
---
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Om du Använd MSAL för att hämta en token för Microsoft Graph API

I det här avsnittet använder MSAL för att hämta en token för Microsoft Graph API.

1. I den *MainWindow.xaml.cs* filen, lägga till referensen för MSAL i klassen:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Ersätt den `MainWindow` klassen koden med följande:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes, this)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
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
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Mer information

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Anropa den `AcquireTokenInteractive` metoden resulterar i ett fönster som uppmanar användaren att logga in. Program kräver vanligtvis användare att logga in interaktivt första gången som de behöver för att få åtkomst till en skyddad resurs. De kan också behöva logga in när en tyst åtgärden att hämta en token misslyckas (till exempel när en användares lösenord har upphört att gälla).

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Den `AcquireTokenSilent` metoden hanterar token anskaffning och förnyelser utan någon användarinteraktion. Efter `AcquireTokenInteractive` körs för första gången `AcquireTokenSilent` är den vanliga metoden för att använda för att hämta token som kommer åt skyddade resurser för efterföljande anrop, eftersom anrop till begära eller förnya token görs tyst.

Slutligen den `AcquireTokenSilent` metoden misslyckas. Orsaker till felet kan vara att användaren har loggat ut eller ändrat sitt lösenord på en annan enhet. När MSAL upptäcker att problemet kan lösas genom att kräva en interaktiv åtgärd, det utlöses en `MsalUiRequiredException` undantag. Programmet kan hantera det här undantaget på två sätt:

* Det kan göra ett anrop mot `AcquireTokenInteractive` omedelbart. Det här anropet resulterar i uppmanar användaren att logga in. Det här mönstret används vanligtvis i online-program där det finns inga tillgängliga offline innehåll för användaren. I exemplet som genererats av den här guidade konfigurationen följer detta mönster som du kan se i åtgärden först gången du kör exemplet. 

* Eftersom ingen användare har använt programmet, `PublicClientApp.Users.FirstOrDefault()` innehåller ett null-värde och ett `MsalUiRequiredException` undantagsfel. 

* Koden i exemplet hanterar undantaget genom att anropa `AcquireTokenInteractive`, vilket innebär att uppmanar användaren att logga in.

* Det kan i stället presentera en visuell indikering för användare som en interaktiv inloggning krävs, så att de kan välja rätt tid att logga in. Eller programmet kan försöka `AcquireTokenSilent` senare. Det här mönstret används ofta när användare kan använda andra programfunktionen utan avbrott, till exempel när offline innehållet är tillgängligt i programmet. I det här fallet kan användare bestämma när de vill logga in på antingen komma åt den skyddade resursen eller uppdatera gammal information. Du kan också programmet kan bestämma att försöka igen `AcquireTokenSilent` när nätverket har återställts efter att ha varit otillgänglig.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Anropa Microsoft Graph API med hjälp av den token som du precis fick

Lägg till följande nya metod till din `MainWindow.xaml.cs`. Metoden används för att göra en `GET` begäran mot Graph API med hjälp av ett auktorisera-huvud:

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du gör ett REST-anrop mot ett skyddade API

I det här exempelprogrammet använder du den `GetHttpContentWithToken` metod för att göra HTTP `GET` begäran mot en skyddad resurs som kräver ett token och återgå sedan innehållet till anroparen. Den här metoden lägger till förvärvade token i auktoriseringshuvudet för HTTP. I det här exemplet resursen är Microsoft Graph API *mig* slutpunkt, vilket visar användarens profilinformation.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Lägg till en metod för att logga ut en användare

Om du vill logga ut en användare måste du lägga till följande metod för att din `MainWindow.xaml.cs` fil:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync(); 

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault()); 
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
### <a name="more-information-about-user-sign-out"></a>Mer information om användaren logga ut

Den `SignOutButton_Click` metoden tar bort användare från användarcachen MSAL som effektivt talar om MSAL att glömma den aktuella användaren så att en framtida begäran att hämta en token lyckas bara om det görs för att köras interaktivt.

Även om programmet i det här exemplet har stöd för enskild användare, stöder MSAL scenarier där flera konton kan logga in på samma gång. Ett exempel är ett e-postprogram där en användare har flera konton.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Visa grundläggande information för token

Om du vill visa grundläggande information om token, lägger du till följande metod för att din *MainWindow.xaml.cs* fil:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Mer information

Förutom den åtkomst-token som används för att anropa Microsoft Graph API, när användaren loggar in, hämtar MSAL också en ID-token. Den här variabeln innehålla en liten del av information som är relevanta för användare. Den `DisplayBasicTokenInfo` metoden visar grundläggande information som finns i token. Exempelvis visas användarens namn och ID, samt den token upphör att gälla och den sträng som representerar den åtkomst-token. Du kan välja den *anropa Microsoft Graph API* knappen flera gånger och se att samma token återanvänts för efterföljande förfrågningar. Du kan också se förfallodatum förlängs när MSAL beslutar att det är dags att förnya token.
<!--end-collapse-->
