---
title: Kom igång med Microsoft Identity Platform Windows-skrivbordet | Microsoft Docs
description: Hur ett Windows Desktop .NET-program (XAML) kan hämta en åtkomsttoken och anropa ett API som skyddas av Microsoft Identity Platform.
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
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f816091e3e8682069a950ff6f6eb839e285bb2f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512432"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Anropa API: et för Microsoft Graph från en Windows Desktop-app

Den här guiden visar hur ett ursprungligt Windows Desktop .NET-program (XAML) använder en åtkomsttoken för att anropa Microsoft Graph-API: et. Appen kan också komma åt andra API: er som kräver åtkomsttoken från en Microsoft Identity Platform för utvecklare v 2.0-slut punkter. Den här plattformen hette tidigare Azure AD.

När du har slutfört guiden kommer programmet att kunna anropa ett skyddat API som använder personliga konton (inklusive outlook.com, live.com och andra). Programmet kommer också att använda arbets-och skol konton från alla företag eller organisationer som använder Azure Active Directory.  

> [!NOTE]
> Guiden kräver Visual Studio 2015 uppdatering 3, Visual Studio 2017 eller Visual Studio 2019. Har du inte någon av dessa versioner? [Ladda ned Visual Studio 2019 gratis](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hur exempel appen som genereras av den här hand boken fungerar

![Visar hur exempel appen som genereras av den här själv studie kursen fungerar](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Exempel programmet som du skapar med den här guiden möjliggör ett Windows-skrivbord som frågar Microsoft Graph-API: t eller ett webb-API som accepterar tokens från en Microsoft Identity-Platform-slutpunkt. I det här scenariot lägger du till en token i HTTP-begäranden via Authorization-huvudet. Microsoft Authentication Library (MSAL) hanterar hämtning och förnyelse av token.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Hantering av hämtning av token för åtkomst till skyddade webb-API: er

När användaren har autentiserats tar exempel programmet emot en token som du kan använda för att fråga Microsoft Graph-API eller ett webb-API som skyddas av Microsoft Identity Platform för utvecklare.

API: er som Microsoft Graph kräver en token för att tillåta åtkomst till vissa resurser. Till exempel krävs en token för att läsa en användar profil, komma åt en användares kalender eller skicka e-post. Ditt program kan begära en åtkomsttoken genom att använda MSAL för att komma åt resurserna genom att ange API-omfång. Denna åtkomsttoken läggs sedan till i HTTP-Authorization-huvudet för varje anrop som görs mot den skyddade resursen.

MSAL hanterar cachelagring och uppdaterar åtkomsttoken åt dig, så att ditt program inte behöver det.

## <a name="nuget-packages"></a>NuGet-paket

I den här guiden används följande NuGet-paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL.NET)|

## <a name="set-up-your-project"></a>Konfigurera projektet

I det här avsnittet skapar du ett nytt projekt för att demonstrera hur du integrerar ett Windows Desktop .NET-program (XAML) med *inloggning med Microsoft* så att programmet kan fråga webb-API: er som kräver en token.

Det program som du skapar med den här guiden visar en knapp som används för att anropa en graf, ett område för att visa resultaten på skärmen och en utloggnings knapp.

> [!NOTE]
> Vill du hämta det här exemplets Visual Studio-projekt i stället? [Ladda ned ett projekt](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)och hoppa till [konfigurations steget](#register-your-application) för att konfigurera kod exemplet innan du kör det.
>

Gör så här för att skapa ditt program:

1. Välj **Arkiv** > **Nytt** > **Projekt** i Visual Studio.
2. Under **mallar**väljer du **visuella C#** objekt.
3. Välj **WPF-app (.NET Framework)** , beroende på vilken version av Visual Studio-versionen som du använder.

## <a name="add-msal-to-your-project"></a>Lägg till MSAL i projektet

1. I Visual Studio väljer du **Verktyg** > **NuGet Package Manager**> **Package Manager Console**.
2. I fönstret Package Manager-konsol klistrar du in följande Azure PowerShell kommando:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Det här kommandot installerar Microsoft Authentication Library. MSAL hanterar hämtning, cachelagring och uppdatering av användartoken som används för att komma åt de API: er som skyddas av Azure Active Directory v 2.0
    >

## <a name="register-your-application"></a>Registrera ditt program

Du kan registrera ditt program på något av två sätt.

### <a name="option-1-express-mode"></a>Alternativ 1: Express läge

Du kan snabbt registrera ditt program genom att göra följande:
1. Gå till [Azure Portal-program registrering](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Ange ett namn för programmet och välj **Registrera**.
1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.

### <a name="option-2-advanced-mode"></a>Alternativ 2: Avancerat läge

Du registrerar programmet och lägger till programregistreringsinformationen i din lösning genom att göra följande:
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Välj **ny registrering**.
   - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `Win-App-calling-MsGraph`.
   - I avsnittet **Kontotyper som stöds** väljer du **Konton alla organisationskataloger och personliga Microsoft-konton (till exempel Skype, Xbox och Outlook.com)** .
   - Välj **Registrera** för att skapa programmet.
1. I listan över sidor för appen väljer du **Autentisering**.
   1. I avsnittet omdirigerings- **URI** : er i listan omdirigerings-URI: er:
   1. I kolumnen **typ** väljer du **offentlig klient (mobil & Desktop)** .
   1. I kolumnen omdirigerings- **URI** , anger du`urn:ietf:wg:oauth:2.0:oob`
1. Välj **Spara**.
1. Gå till Visual Studio, öppna filen *app.XAML.cs* och Ersätt `Enter_the_Application_Id_here` i kodfragmentet nedan med det program-ID som du precis registrerade och kopierade.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>Lägg till koden för att initiera MSAL

I det här steget skapar du en-klass för att hantera interaktion med MSAL, till exempel hantering av token.

1. Öppna filen *app.XAML.cs* och Lägg sedan till referensen för MSAL i klassen:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Uppdatera klassen app till följande:

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

## <a name="create-the-application-ui"></a>Skapa programmets användar gränssnitt

Det här avsnittet visar hur ett program kan fråga en skyddad backend-server, till exempel Microsoft Graph. 

En *MainWindow. XAML* -fil ska skapas automatiskt som en del av projekt mal len. Öppna den här filen och ersätt sedan programmets  *\<rutnät >* nod med följande kod:

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

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Använda MSAL för att hämta en token för Microsoft Graph-API: et

I det här avsnittet använder du MSAL för att hämta en token för Microsoft Graph-API: et.

1. I *MainWindow.XAML.cs* -filen lägger du till referensen för MSAL i klassen:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. `MainWindow` Ersätt klass koden med följande:

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

Anrop av `AcquireTokenInteractive` metoden resulterar i ett fönster som gör att användarna kan logga in. Program kräver vanligt vis att användare loggar in interaktivt första gången de behöver åtkomst till en skyddad resurs. De kan också behöva logga in när en tyst åtgärd för att hämta en token Miss lyckas (till exempel när en användares lösen ord har upphört att gälla).

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

`AcquireTokenSilent` Metoden hanterar hämtning av token och förnyelser utan några åtgärder från användaren. När `AcquireTokenInteractive` har körts för första `AcquireTokenSilent` gången är den vanliga metoden att använda för att hämta token som har åtkomst till skyddade resurser för efterföljande anrop, eftersom anrop till begäran eller förnyelse av token görs i bakgrunden.

Till `AcquireTokenSilent` slut kommer metoden att Miss Miss kan. Orsaken till ett haveri kan vara att användaren antingen har loggat ut eller ändrat sitt lösen ord på en annan enhet. När MSAL upptäcker att problemet kan lösas genom att kräva en interaktiv åtgärd utlöses ett `MsalUiRequiredException` undantag. Ditt program kan hantera detta undantag på två sätt:

* Det kan göra ett anrop `AcquireTokenInteractive` direkt. Det här anropet resulterar i att användaren uppmanas att logga in. Det här mönstret används vanligt vis i online-program där det inte finns något tillgängligt offline-innehåll för användaren. Exemplet som genereras av den här guidade installationen följer det här mönstret, som du kan se i åtgärd första gången du kör exemplet. 

* Eftersom ingen användare har använt programmet, `PublicClientApp.Users.FirstOrDefault()` innehåller ett null-värde och ett `MsalUiRequiredException` undantag genereras. 

* Koden i exemplet hanterar sedan undantaget genom att anropa `AcquireTokenInteractive`, vilket innebär att användaren måste logga in.

* Den kan i stället presentera ett visuellt objekt för användare om att en interaktiv inloggning krävs, så att de kan välja rätt tid för att logga in. Eller så kan programmet försöka `AcquireTokenSilent` igen senare. Det här mönstret används ofta när användare kan använda andra funktioner utan avbrott – till exempel när offlineinnehåll är tillgängligt i programmet. I det här fallet kan användare bestämma när de vill logga in för att antingen komma åt den skyddade resursen eller uppdatera den inaktuella informationen. Alternativt kan programmet välja att försöka igen `AcquireTokenSilent` när nätverket återställs efter att ha varit tillfälligt otillgängligt.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Anropa Microsoft Graph-API med hjälp av den token som du nyss hämtade

Lägg till följande nya metod i `MainWindow.xaml.cs`. Metoden används för att skapa en `GET` begäran mot Graph API med hjälp av ett auktoriserande huvud:

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

I det här exempel programmet använder `GetHttpContentWithToken` du metoden för att göra en http- `GET` begäran mot en skyddad resurs som kräver en token och sedan returnera innehållet till anroparen. Den här metoden lägger till den hämtade token i HTTP-Authorization-huvudet. I det här exemplet är resursen Microsoft Graph-API *mig* -slutpunkt, som visar användarens profil information.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Lägga till en metod för att logga ut en användare

Om du vill logga ut en användare lägger du till följande metod `MainWindow.xaml.cs` i filen:

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
### <a name="more-information-about-user-sign-out"></a>Mer information om användar utloggning

`SignOutButton_Click` Metoden tar bort användare från MSAL User cache, som i praktiken instruerar MSAL att glömma den aktuella användaren så att en framtida begäran om att hämta en token Miss lyckas endast om den görs aktiv.

Även om programmet i det här exemplet stöder enskilda användare, stöder MSAL scenarier där flera konton kan loggas samtidigt. Ett exempel är ett e-postprogram där en användare har flera konton.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Visa grundläggande information om token

Om du vill visa grundläggande information om token lägger du till följande metod i din *MainWindow.XAML.cs* -fil:

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

Förutom den åtkomsttoken som används för att anropa Microsoft Graph-API: et kan MSAL också hämta en ID-token när användaren loggar in. Denna token innehåller en liten delmängd av information som är relevant för användarna. `DisplayBasicTokenInfo` Metoden visar den grundläggande information som finns i token. Till exempel visas användarens visnings namn och ID, samt utgångs datum för token och strängen som representerar åtkomsttoken. Du kan välja *anrops Microsoft Graph API* -knappen flera gånger och se att samma token återanvändes för efterföljande begär Anden. Du kan också se förfallo datumet som utökas när MSAL bestämmer att det är dags att förnya token.
<!--end-collapse-->

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

Hjälp oss att förbättra Microsoft Identity Platform. Berätta för oss vad du tycker genom att slutföra en kort enkät med två frågor.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform-undersökning](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
