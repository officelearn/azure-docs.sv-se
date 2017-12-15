
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Använd MSAL för att hämta en token för Microsoft Graph API

I det här avsnittet använder du MSAL för att hämta en token för Microsoft Graph API.

1.  I den *MainWindow.xaml.cs* fil, Lägg till referens för MSAL för klassen:

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. Ersätt den `MainWindow` klassen koden med följande:

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
#### <a name="get-a-user-token-interactively"></a>Hämta token för en användare interaktivt
Anropar den `AcquireTokenAsync` metoden resulterar i ett fönster som uppmanar användaren att logga in. Program kräver vanligtvis användare att logga in interaktivt första gången de behöver för att få åtkomst till en skyddad resurs. De kan också behöva logga in när en tyst att hämta en token misslyckas (till exempel när en användares lösenord har upphört att gälla).

#### <a name="get-a-user-token-silently"></a>Hämta token för en användare tyst
Den `AcquireTokenSilentAsync` metoden hanterar token anskaffning och förnyelser utan någon användarinteraktion. Efter `AcquireTokenAsync` körs för första gången `AcquireTokenSilentAsync` är den vanliga metoden för att hämta token som kommer åt skyddade resurser för efterföljande anrop, eftersom anrop till begära eller förnya token görs tyst.

Slutligen den `AcquireTokenSilentAsync` metoden misslyckas. Orsaker till felet kan vara att användaren har loggat ut eller ändra sina lösenord på en annan enhet. När MSAL upptäcker att problemet kan lösas genom att kräva en interaktiv åtgärd, den utlöses en `MsalUiRequiredException` undantag. Programmet kan hantera det här undantaget på två sätt:

* Det kan göra att ett anrop mot `AcquireTokenAsync` omedelbart. Det här anropet resulterar i att användaren uppmanas att logga in. Det här mönstret används vanligtvis i Onlineprogram där det finns inga tillgängliga offline innehåll för användaren. Exempel som genererats av den här interaktiv installation följer detta mönster som du kan se i åtgärden första gången du köra exemplet. 
    * Eftersom ingen användare har använt programmet, `PublicClientApp.Users.FirstOrDefault()` innehåller ett null-värde och ett `MsalUiRequiredException` undantag. 
    * Koden i exemplet hanterar undantaget genom att anropa `AcquireTokenAsync`, vilket innebär att användaren uppmanas att logga in.

* Det kan i stället presentera en indikering för användare som en interaktiv inloggning krävs, så att de kan välja rätt tid att logga in. Eller programmet kan försöka `AcquireTokenSilentAsync` senare. Det här mönstret används ofta när användare kan använda andra funktioner i programmet utan avbrott – exempelvis när offline innehållet är tillgängligt i programmet. I det här fallet kan användare bestämma när de vill logga in att komma åt den skyddade resursen eller uppdatera informationen om gamla. Du kan också programmet kan bestämma att försöka `AcquireTokenSilentAsync` när nätverket återställs efter att ha varit tillfälligt otillgänglig.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Anropa Microsoft Graph API med hjälp av den token som du precis erhölls

Lägg till följande nya metod för att din `MainWindow.xaml.cs`. Metoden används för att göra en `GET` begäran mot Graph API med hjälp av ett auktorisera-huvud:

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du använder ett REST-anrop mot ett skyddade API

I det här exempelprogrammet använder du den `GetHttpContentWithToken` metod för att göra en HTTP `GET` begäran mot en skyddad resurs som kräver ett token och returnera innehållet till anroparen. Den här metoden lägger till anskaffats token i auktoriseringshuvudet för HTTP. Resursen för det här exemplet är Microsoft Graph API *mig* slutpunkt som visar information om användarens profil.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Lägg till en metod för att logga ut användare

Om du vill logga ut en användare, lägger du till följande metod för att din `MainWindow.xaml.cs` fil:

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
### <a name="more-information-about-user-sign-out"></a>Mer information om användaren utloggning

Den `SignOutButton_Click` metoden tar bort användare från användarcachen MSAL som säger MSAL att glömma den aktuella användaren så att en framtida begäran om att hämta en token lyckas bara om det görs för att interaktivt.

Även om programmet i det här exemplet stöder en enskild användare, stöder MSAL scenarier där flera konton kan logga in på samma gång. Ett exempel är ett e-postprogram där en användare har flera konton.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Visa grundläggande information om token

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
        TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
        TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```
<!--start-collapse-->
### <a name="more-information"></a>Mer information

Förutom den åtkomst-token som används för att anropa Microsoft Graph API när användaren loggar in, hämtar MSAL också en ID-token. Denna token innehåller en liten del av information som är relevant för användarna. Den `DisplayBasicTokenInfo` metoden visar grundläggande information som finns i token. Om du till exempel visar användarens namn och ID, samt token upphör att gälla och den sträng som representerar den åtkomst-token. Du kan välja den *anropa Microsoft Graph API* knappen flera gånger och se att samma token återanvänts för efterföljande förfrågningar. Du kan också se förfallodatum förlängs när MSAL bestämmer det är dags att förnya token.
<!--end-collapse-->

