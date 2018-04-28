---
title: Azure AD v2 UWP komma igång | Microsoft Docs
description: Hur Universal Windows Platform (XAML) program anropar en API som kräver åtkomst-token i Azure Active Directory v2 slutpunkten
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4db14bc250bf9d6740380f3c4376f43d6f315b01
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Anropa Microsoft Graph API från ett program för universella Windowsplattformen (UWP)

Den här guiden visar hur en programspecifika Universal Windows Platform (XAML) kan få en åtkomsttoken och sedan använda den här åtkomst toke för att anropa Microsoft Graph API eller andra API: er som kräver åtkomst-token från Azure Active Directory v2-slutpunkten.

I slutet av den här guiden kommer programmet att kunna anropa en skyddad API med hjälp av personliga konton (inklusive outlook.com och live.com) samt arbets- och skolkonton från alla företag eller organisation som har Azure Active Directory.  

> Den här guiden kräver Visual Studio 2017 med Uwp-utveckling installerad. Kontrollera detta [artikel](https://docs.microsoft.com/windows/uwp/get-started/get-set-up "konfigurera Visual Studio för UWP") instruktioner om hur du hämtar och konfigurera Visual Studio att utveckla Universal Windows Platform appar.

### <a name="how-this-guide-works"></a>Hur den här guiden fungerar

![Hur den här guiden fungerar](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

Det exempelprogram som skapats av den här guiden kan ett Uwp-program till Microsoft Graph API eller ett webb-API som accepterar token från Azure Active Directory v2 slutpunkten. I det här scenariot läggs en token på HTTP-förfrågningar via Authorization-huvud. Token förvärv och förnyelse hanteras av Microsoft Authentication Library (MSAL).

### <a name="nuget-packages"></a>NuGet-paket

Den här guiden använder följande NuGet-paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|


## <a name="set-up-your-project"></a>Konfigurera ditt projekt

Det här avsnittet innehåller stegvisa instruktioner för hur du skapar ett nytt projekt för att demonstrera hur du integrerar Windows Desktop .NET-program (XAML) med *logga In med Microsoft* så att den kan fråga webb-API: er som kräver en token.

Program som skapats av den här guiden visar en knapp för att ett diagram och visa resultaten på skärmen och en knapp för utloggning.

> Om du vill hämta det här exemplet Visual Studio-projekt i stället? [Hämta ett projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) och gå vidare till den [appregistrering](#register-your-application "programmet registreringssteget") steg för att konfigurera kodexemplet innan du kör.


### <a name="create-your-application"></a>Skapa programmet
1. I Visual Studio: **filen** > **nya** > **projekt**<br/>
2. Under *mallar*väljer **Visual C#**
3. Välj **tom App (Universal Windows)**
4. Ge det ett namn och klicka på 'Ok'.
5. Om du uppmanas minskade välja en version för *mål* och *minsta* version och klicka på 'Ok':<br/><br/>![Lägsta och mål-versioner](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Lägg till Microsoft Authentication Library (MSAL) i projektet
1. I Visual Studio: **verktyg** > **Nuget Package Manager** > **Package Manager-konsolen**
2. Kopiera och klistra in följande kommando i fönstret Package Manager-konsolen:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> Paketet ovan installerar den [Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). MSAL hanterar hämtning av cachelagring och uppdatera användartoken som används för åtkomst till API: er som skyddas av Azure Active Directory v2.

## <a name="initialize-msal"></a>Initiera MSAL
Det här steget kan du skapa en klass för att hantera interaktion med MSAL bibliotek, t.ex hantering av token.

1. Öppna den **App.xaml.cs** filen och Lägg till referens för MSAL bibliotek i klassen:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Lägga till följande två rader i appens klass (inuti <code>sealed partial class App : Application</code> block):

    ```csharp
    //Below is the clientId of your app registration. 
    //You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Skapa ditt programs gränssnitt

En **MainPage.xaml** fil automatiskt ska skapas som en del av projektmallen för. Öppna den här filen och följ sedan instruktionerna:

1.  Ersätt ditt programs **<Grid>** nod med:

    ```xml
    <Grid>
        <StackPanel Background="Azure">
            <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
                <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
                <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
            </StackPanel>
            <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
            <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
        </StackPanel>
    </Grid>
    ```
    
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Använd Microsoft Authentication Library (MSAL) för att hämta en token för Microsoft Graph API

Det här avsnittet visar hur du använder MSAL för att hämta en token för Microsoft Graph API.

1.  I **MainPage.xaml.cs**, Lägg till referens för MSAL bibliotek i klassen:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Ersätt Koden i din <code>MainPage</code> klassen med följande:

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };
    
        public MainPage()
        {
            this.InitializeComponent();
        }
    
        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;
    
            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
    
                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
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
    }
    ```

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

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Anropa använder token som du precis har köpt Microsoft Graph API

1. Lägg till följande nya metod för att din **MainPage.xaml.cs**. Metoden används för att göra en `GET` begäran mot Graph API med hjälp av ett auktorisera-huvud:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du skapar ett REST-anrop mot ett skyddade API

I det här exempelprogrammet i `GetHttpContentWithToken` metoden används för att en HTTP `GET` begäran mot en skyddad resurs som kräver ett token och returnera innehållet till anroparen. Den här metoden lägger till anskaffats token i den *HTTP Authorization-huvud*. Resursen för det här exemplet är Microsoft Graph API *mig* slutpunkt – som visar information om användarens profil.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Lägg till en metod för att logga ut användaren

1. Lägg till följande metod för att logga ut användaren genom **MainPage.xaml.cs**:

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

### <a name="more-info-on-sign-out"></a>Mer information om utloggning

Metoden `SignOutButton_Click` tar bort användaren från MSAL användarcachen – det säger MSAL du glömmer bort den aktuella användaren så att en framtida begäran om att hämta en token kan bara genomföras om det görs för att interaktivt.
Även om programmet i det här exemplet har stöd för en enskild användare, stöder MSAL scenarier där flera konton kan vara inloggad på samma gång – ett exempel är ett e-postprogram där en användare har flera konton.

## <a name="display-basic-token-information"></a>Visa grundläggande Information om Token

1. Lägg till följande metod för att din **MainPage.xaml.cs** att visa grundläggande information om token:

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

### <a name="more-information"></a>Mer information

ID-token har köpt *OpenID Connect* också innehålla en liten del av information som är relevant för användaren. `DisplayBasicTokenInfo` Visar grundläggande information som finns i token: exempelvis användarens namn och ID, samt token upphör att gälla och den sträng som representerar åtkomst token sig själv. Den här informationen visas att se. Du kan träffa på **anropa Microsoft Graph API** knappen flera gånger och se att samma token återanvänts för efterföljande förfrågningar. Du kan också se förfallodatum förlängs när MSAL bestämmer det är dags att förnya token.

## <a name="register-your-application"></a>Registrera ditt program

Nu måste du registrera ditt program i den *Microsoft Programregistreringsportalen*:
1. Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app) registrera ett program
2. Ange ett namn för ditt program 
3. Kontrollera att alternativet för interaktiv installation är markerat
4. Klicka på **lägga till plattformar**och välj **programspecifika** och tryck på Spara
5. Kopiera GUID i program-ID, gå tillbaka till Visual Studio, öppna **App.xaml.cs** och Ersätt `your_client_id_here` med program-ID som du just har registrerat:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Aktivera integrerad autentisering på externa domäner (valfritt)

Applikationsmanifestet måste aktivera ytterligare funktioner för att aktivera Windows-integrerad autentisering när den används med en federerade Azure Active Directory-domän:

1. Dubbelklicka på **Package.appxmanifest**
2. Välj **funktioner** fliken och se till att följande inställningar har aktiverats:

    - Enterprise-autentisering
    - Privata nätverk (klient och Server)
    - Delade användarcertifikat 

3. Öppna **App.xaml.cs**, och Lägg till följande i App-konstruktorn:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> Windows-integrerad autentisering är inte konfigurerad som standard för det här exemplet eftersom program som begär den *Enterprise-autentisering* eller *delade användarcertifikat* funktioner kräver en högre verificationby Windows Store och inte alla utvecklare du vill utföra den högre nivån av verifiering. Aktivera den här inställningen bara om du behöver Windows-integrerad autentisering med en federerade Azure Active Directory-domän.


## <a name="test-your-code"></a>Testa din kod

För att testa ditt program, trycker du på `F5` köra projektet i Visual Studio. Main-fönster visas:

![Programmets användargränssnitt](media/active-directory-uwp-v2.md/testapp-ui.png)

När du är redo att testa klickar du på *anropa Microsoft Graph API* och Använd Microsoft Azure Active Directory (organisationskonto) eller ett Account (live.com, outlook.com) för att logga in. Om det är första gången visas ett fönster som ber användaren logga in:

![Inloggningssidan](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>Medgivande
Första gången du loggar in på ditt program, visas en medgivande skärm som liknar följande, där du måste acceptera explicit:

![Medgivande skärmen](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>Förväntat resultat
Du bör se användarens profilinformation som returneras av Microsoft Graph API-anrop på skärmen API-anrop resultat:

![Skärmen](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

Du bör också se grundläggande information om token har köpt `AcquireTokenAsync` eller `AcquireTokenSilentAsync` i rutan Token information:

|Egenskap  |Format  |Beskrivning |
|---------|---------|---------|
|**Namn** |Användarens fullständiga namn |Användaren förnamn och efternamn.|
|**Användarnamn** |<span>user@domain.com</span> |Användarnamnet som används för att identifiera användaren.|
|**Token upphör att gälla** |DateTime |Tiden då token upphör att gälla. MSAL utökar utgångsdatumet genom att förnya token efter behov.|
|**Åtkomst-Token** |Sträng |Tokensträng som skickas till HTTP-begäranden som kräver en *Authorization-huvud*.|

#### <a name="see-what-is-in-the-access-token-optional"></a>Se vad som finns i den åtkomst-token (valfritt)
Du kan också kopiera värdet i Access Token och klistra in den i https://jwt.ms att avkoda det och visa listan med anspråk.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver den *user.read* omfång att läsa en användares profil. Detta scope läggs automatiskt som standard i alla program som har registrerats i portalen för registrering av programmet. Andra för Microsoft Graph-API: er, samt anpassade API: er för backend-servern, kan kräva ytterligare scope. Microsoft Graph API kräver den *Calendars.Read* scope för att visa användarens kalendrar.

För att få åtkomst till användarkalendrar i kontexten för ett program måste du lägga till den *Calendars.Read* delegerad behörighet att registreringsinformation för programmet. Lägg sedan till den *Calendars.Read* omfånget för den `acquireTokenSilent` anropa. 

> [!NOTE]
> Användare kan uppmanas att ytterligare medgivanden när du ökar antalet scope.

## <a name="known-issues"></a>Kända problem

### <a name="issue-1"></a>Problem 1:
Får du ett av följande fel när inloggningen på ditt program på en federerad Azure Active Directory-domän:
 - Har inget giltigt certifikat hittades i begäran.
 - Inga giltiga certifikat finns i användarens certifikatarkiv.
 - Försök igen genom att välja en annan autentiseringsmetod ”.

**Orsak:** Enterprise-och certifikat inte har aktiverats

**Lösning:** följer du stegen i [integrerad autentisering på externa domäner](#enable-integrated-authentication-on-federated-domains-optional)

### <a name="issue-2"></a>Problem 2:
Du aktiverar enare [integrerad autentisering på externa domäner](#enable-integrated-authentication-on-federated-domains-optional) och försök att använda Windows Hello på en Windows 10-dator för att logga in en en miljö med flera-factor-autentisering har konfigurerat listan över certifikat visas , men om du väljer att använda din PIN-kod fönstret PIN-koden aldrig visas.

**Orsak:** detta är en känd begränsning med webbautentiseringskoordinatorn i UWP-program som körs på Windows 10 desktop (fungerar bra i Windows 10 Mobile)

**Lösning:** som tillfällig lösning kan användarna måste välja att logga in med andra alternativ och välj sedan *logga in med ett användarnamn och lösenord* i stället väljer Ange ditt lösenord och sedan gå igenom telefon-autentisering.

