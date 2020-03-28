---
title: Komma igång med Microsoft identity platform Windows desktop | Microsoft-dokument
description: Hur ett XAML-program (Windows Desktop .NET) kan hämta en åtkomsttoken och anropa ett API som skyddas av Microsofts identitetsplattform.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 422251da25ae0ef911eb723bb0342a84fe99559c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79129920"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Anropa Microsoft Graph API från en Windows Desktop-app

Den här guiden visar hur ett inbyggt XAML-program (Windows Desktop .NET) använder en åtkomsttoken för att anropa Microsoft Graph API. Appen kan också komma åt andra API:er som kräver åtkomsttoken från en Microsoft-identitetsplattform för utvecklare v2.0-slutpunkten. Den här plattformen hette tidigare Azure AD.

När du har slutfört guiden kan programmet anropa ett skyddat API som använder personliga konton (inklusive outlook.com, live.com och andra). Programmet kommer också att använda arbets- och skolkonton från alla företag eller organisationer som använder Azure Active Directory.  

> [!NOTE]
> Guiden kräver Visual Studio 2015 Update 3, Visual Studio 2017 eller Visual Studio 2019. Har du inte någon av dessa versioner? [Ladda ner Visual Studio 2019 gratis.](https://www.visualstudio.com/downloads/)

>[!NOTE]
> Om du inte har gjort det tidigare i Microsofts identitetsplattform rekommenderar vi att du börjar med [Acquire a token och anropar Microsoft Graph API från en Windows-skrivbordsapp](quickstart-v2-windows-desktop.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Så här fungerar exempelappen som genereras av den här guiden

![Visar hur exempelappen som genereras av den här självstudien fungerar](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Exempelprogrammet som du skapar med den här guiden aktiverar ett Windows Desktop-program som frågar microsoft graph-API:et eller ett webb-API som accepterar token från en slutpunkt för Microsoft-identitetsplattform. I det här scenariot lägger du till en token i HTTP-begäranden via auktoriseringshuvudet. Microsoft Authentication Library (MSAL) hanterar tokeninsamling och förnyelse.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Hantera tokeninhämtning för åtkomst till skyddade webb-API:er

När användaren har autentiserats får exempelprogrammet en token som du kan använda för att fråga Microsoft Graph API eller ett webb-API som skyddas av Microsofts identitetsplattform för utvecklare.

API:er som Microsoft Graph kräver en token för att tillåta åtkomst till specifika resurser. En token krävs till exempel för att läsa en användares profil, komma åt en användares kalender eller skicka e-post. Ditt program kan begära en åtkomsttoken med hjälp av MSAL för att komma åt dessa resurser genom att ange API-scope. Den här åtkomsttoken läggs sedan till i HTTP-auktoriseringshuvudet för alla anrop som görs mot den skyddade resursen.

MSAL hanterar cachelagring och uppdatering av åtkomsttoken åt dig, så att ditt program inte behöver det.

## <a name="nuget-packages"></a>NuGet-paket

Den här guiden använder följande NuGet-paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft-autentiseringsbibliotek (MSAL.NET)|

## <a name="set-up-your-project"></a>Konfigurera projektet

I det här avsnittet skapar du ett nytt projekt för att visa hur du integrerar ett Windows Desktop *.NET-program* (XAML) med Inloggning med Microsoft så att programmet kan fråga webb-API:er som kräver en token.

Programmet som du skapar med den här guiden visar en knapp som används för att anropa ett diagram, ett område för att visa resultaten på skärmen och en ut logga ut-knapp.

> [!NOTE]
> Föredrar du att ladda ned det här exemplets Visual Studio-projekt i stället? [Hämta ett projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)och gå till [konfigurationssteget](#register-your-application) för att konfigurera kodexemplet innan du kör det.
>

Så här skapar du programmet:

1. Välj **Arkiv** > **nytt** > **projekt**i Visual Studio.
2. Under **Mallar**väljer du **Visual C#**.
3. Välj **WPF App (.NET Framework)**, beroende på vilken version av Visual Studio-versionen du använder.

## <a name="add-msal-to-your-project"></a>Lägga till MSAL i projektet

1. I Visual Studio väljer du **Tools** > **NuGet Package Manager**> **Package Manager Console**.
2. Klistra in följande Azure PowerShell-kommando i fönstret Package Manager Console:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Med det här kommandot installeras Microsoft Authentication Library. MSAL hanterar anskaffning, cachelagring och uppdatering av användartoken som används för att komma åt API:er som skyddas av Azure Active Directory v2.0
    >

## <a name="register-your-application"></a>Registrera ditt program

Du kan registrera din ansökan på något av två sätt.

### <a name="option-1-express-mode"></a>Alternativ 1: Expressläge

Du kan snabbt registrera din ansökan genom att göra följande:
1. Gå till [Azure-portalen - Programregistrering](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Ange ett namn för programmet och välj **Registrera**.
1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.

### <a name="option-2-advanced-mode"></a>Alternativ 2: Avancerat läge

Du registrerar programmet och lägger till programregistreringsinformationen i din lösning genom att göra följande:
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. Navigera till sidan Microsoft identity platform för utvecklare [Appregistreringar.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Välj **Ny registrering**.
   - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `Win-App-calling-MsGraph`.
   - I avsnittet **Kontotyper som stöds** väljer du **Konton alla organisationskataloger och personliga Microsoft-konton (till exempel Skype, Xbox och Outlook.com)**.
   - Välj **Registrera** för att skapa programmet.
1. I listan över sidor för appen väljer du **Autentisering**.
   1. I avsnittet **Omdirigera URI:er** i listan Omdirigera URI:er:
   1. I kolumnen **TYPE** väljer du **Offentlig klient/inbyggd (mobil & skrivbord)**.
   1. I kolumnen **REDIRECT URI** anger du`https://login.microsoftonline.com/common/oauth2/nativeclient`
1. Välj **Registrera**.
1. Gå till Visual Studio, öppna *App.xaml.cs-filen* `Enter_the_Application_Id_here` och ersätt sedan kodavsnittet nedan med det program-ID som du just registrerade och kopierade.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>Lägga till koden för att initiera MSAL

I det här steget skapar du en klass för att hantera interaktion med MSAL, till exempel hantering av token.

1. Öppna *App.xaml.cs-filen* och lägg sedan till referensen för MSAL i klassen:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Uppdatera appklassen till följande:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Skapa programgränssnittet

Det här avsnittet visar hur ett program kan fråga en skyddad server för server för server för server för server, till exempel Microsoft Graph. 

En *MainWindow.xaml-fil* ska skapas automatiskt som en del av projektmallen. Öppna den här filen och ersätt sedan programmets * \<Grid->* nod med följande kod:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Använda MSAL för att hämta en token för Microsoft Graph API

I det här avsnittet använder du MSAL för att hämta en token för Microsoft Graph API.

1. Lägg *MainWindow.xaml.cs* till referensen för MSAL i klassen i MainWindow.xaml.cs-filen:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Ersätt `MainWindow` klasskoden med följande:

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
                    authResult = await app.AcquireTokenInteractive(scopes)
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

Om `AcquireTokenInteractive` du anropar metoden visas ett fönster där användarna uppmanas att logga in. Program kräver vanligtvis att användare loggar in interaktivt första gången de behöver komma åt en skyddad resurs. De kan också behöva logga in när en tyst åtgärd för att hämta en token misslyckas (till exempel när en användares lösenord har upphört att gälla).

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Metoden `AcquireTokenSilent` hanterar tokenförvärv och förnyelser utan någon användarinteraktion. När `AcquireTokenInteractive` körs för första gången, `AcquireTokenSilent` är den vanliga metoden att använda för att hämta token som kommer åt skyddade resurser för efterföljande samtal, eftersom anrop för att begära eller förnya token görs tyst.

Så småningom `AcquireTokenSilent` kommer metoden att misslyckas. Orsaker till fel kan vara att användaren antingen har loggat ut eller ändrat sitt lösenord på en annan enhet. När MSAL upptäcker att problemet kan lösas genom att `MsalUiRequiredException` kräva en interaktiv åtgärd utlöses ett undantag. Ditt program kan hantera det här undantaget på två sätt:

* Det kan ringa `AcquireTokenInteractive` ett samtal mot omedelbart. Det här anropet leder till att användaren uppmanas att logga in. Det här mönstret används vanligtvis i onlineprogram där det inte finns något tillgängligt offlineinnehåll för användaren. Exemplet som genereras av den här guidade inställningen följer det här mönstret, som du kan se i aktion första gången du kör exemplet. 

* Eftersom ingen användare har `PublicClientApp.Users.FirstOrDefault()` använt programmet innehåller du `MsalUiRequiredException` ett null-värde och ett undantag genereras. 

* Koden i exemplet hanterar sedan undantaget `AcquireTokenInteractive`genom att anropa , vilket resulterar i att användaren uppmanas att logga in.

* Det kan i stället presentera en visuell indikation för användarna att en interaktiv inloggning krävs, så att de kan välja rätt tid att logga in. Eller kan programmet `AcquireTokenSilent` försöka igen senare. Det här mönstret används ofta när användare kan använda andra programfunktioner utan avbrott – till exempel när offlineinnehåll är tillgängligt i programmet. I det här fallet kan användarna bestämma när de vill logga in för att antingen komma åt den skyddade resursen eller uppdatera den inaktuella informationen. Alternativt kan programmet välja att `AcquireTokenSilent` försöka igen när nätverket återställs efter att ha varit tillfälligt otillgängligt.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Anropa Microsoft Graph API med hjälp av den token du just fått

Lägg till följande nya `MainWindow.xaml.cs`metod i . Metoden används för att `GET` göra en begäran mot Graph API med hjälp av ett auktoriserat huvud:

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du gör ett REST-anrop mot ett skyddat API

I det här exempelprogrammet `GetHttpContentWithToken` använder du `GET` metoden för att göra en HTTP-begäran mot en skyddad resurs som kräver en token och sedan returnera innehållet till anroparen. Den här metoden lägger till den förvärvade token i HTTP-auktoriseringshuvudet. För det här exemplet är resursen slutpunkten För Microsoft Graph API *me,* som visar användarens profilinformation.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Lägga till en metod för att logga ut en användare

Om du vill logga ut en `MainWindow.xaml.cs` användare lägger du till följande metod i filen:

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
### <a name="more-information-about-user-sign-out"></a>Mer information om utloggning av användare

Metoden `SignOutButton_Click` tar bort användare från MSAL-användarcachen, vilket effektivt talar om för MSAL att glömma den aktuella användaren så att en framtida begäran om att hämta en token endast kommer att lyckas om den är gjord för att vara interaktiv.

Även om programmet i det här exemplet stöder enskilda användare stöder MSAL scenarier där flera konton kan loggas in samtidigt. Ett exempel är ett e-postprogram där en användare har flera konton.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Visa grundläggande tokeninformation

Om du vill visa grundläggande information om *MainWindow.xaml.cs* token lägger du till följande metod i MainWindow.xaml.cs-filen:

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

Förutom den åtkomsttoken som används för att anropa Microsoft Graph API, efter att användaren loggar in, erhåller MSAL också en ID-token. Den här token innehåller en liten delmängd av information som är relevant för användarna. Metoden `DisplayBasicTokenInfo` visar den grundläggande information som finns i token. Den visar till exempel användarens visningsnamn och ID, samt tokenförfallodatum och strängen som representerar själva åtkomsttoken. Du kan välja knappen *Anropa Microsoft Graph API* flera gånger och se att samma token återanvändades för efterföljande begäranden. Du kan också se utgångsdatumet som utökas när MSAL beslutar att det är dags att förnya token.
<!--end-collapse-->

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

Hjälp oss att förbättra Microsofts identitetsplattform. Berätta vad du tycker genom att fylla i en kort två-fråga undersökning.

> [!div class="nextstepaction"]
> [Undersökning av Microsofts identitetsplattform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
