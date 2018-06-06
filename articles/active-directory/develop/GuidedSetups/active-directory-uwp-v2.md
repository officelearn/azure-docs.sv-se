---
title: Azure AD v2 UWP komma igång | Microsoft Docs
description: Hur program för universella Windows-plattformen (UWP) kan anropa ett API som kräver åtkomst-token med Azure Active Directory v2-slutpunkten
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
ms.openlocfilehash: c2d5681e30651aac7a09a8ead923015e9a892d42
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34796622"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Anropa Microsoft Graph API från en Uwp-App (XAML)

Den här guiden förklarar hur en programspecifika universella Windowsplattformen (UWP) kan begära en åtkomst-token och sedan anropa Microsoft Graph API. I guiden gäller även för andra API: er som kräver åtkomst-token från Azure Active Directory v2-slutpunkten.

I slutet av den här guiden anropar skyddade API med hjälp av personliga konton i ditt program. Exempel är outlook.com och live.com. Programmet anropar också arbets-och skolkonton från alla företag eller organisation som har Azure Active Directory.

>[!NOTE]
> Den här guiden kräver Visual Studio 2017 med Uwp-utveckling installerad. Se [konfigurera](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) för att hämta och konfigurera Visual Studio för att utveckla Uwp-appar.

### <a name="how-this-guide-works"></a>Hur den här guiden fungerar

![Hur den här guiden fungerar diagram](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

Den här guiden skapar en UWP-appen som frågar Microsoft Graph API eller ett webb-API som accepterar token från Azure Active Directory v2-slutpunkten. I det här scenariot läggs en token på HTTP-förfrågningar via Authorization-huvud. Bibliotek för Microsoft-autentisering (MSAL) hanterar token förvärv av organisationer och uppdateringar.

### <a name="nuget-packages"></a>NuGet-paket

Den här guiden använder följande NuGet-paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft-Autentiseringsbibliotek|


## <a name="set-up-your-project"></a>Konfigurera ditt projekt

Det här avsnittet innehåller stegvisa instruktioner för att integrera Windows Desktop .NET-program (XAML) med *logga In med Microsoft*. Den kan sedan fråga webb-API: er som kräver ett token, till exempel Microsoft Graph API.

Den här guiden skapar ett program som visar en knapp som frågar Graph API och en knapp för utloggning textrutor som visar resultatet av anrop.

>[!NOTE]
> Vill du hämta det här exemplet Visual Studio-projekt i stället? [Hämta ett projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) och gå vidare till den [appregistrering](#register-your-application "programmet registreringssteget") steg för att konfigurera kodexemplet innan den körs.


### <a name="create-your-application"></a>Skapa programmet
1. I Visual Studio väljer **filen** > **ny** > **projekt**.
2. Under **mallar**väljer **Visual C#**.
3. Välj **Tom app (Universal Windows)**.
4. Ge appen och markera **OK**.
5. Välj en version för **mål** och **minsta** versioner och välj **OK**.

    >![Lägsta och mål-versioner](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Lägg till Microsoft Authentication Library i projektet
1. I Visual Studio väljer **verktyg** > **NuGet Package Manager** > **Pakethanterarkonsolen**.
2. Kopiera och klistra in följande kommando i den **Pakethanterarkonsolen** fönster:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> Det här kommandot installerar [Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). MSAL skaffar cachelagrar och uppdaterar användartoken som API: er som skyddas av Azure Active Directory v2.

## <a name="initialize-msal"></a>Initiera MSAL
Det här steget kan du skapa en klass för att hantera interaktion med MSAL, till exempel hantera token.

1. Öppna den **App.xaml.cs** filen och Lägg till referens för MSAL för klassen:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Lägga till följande två rader i appens klass (inuti <code>sealed partial class App : Application</code> block):

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Skapa ditt programs gränssnitt

En **MainPage.xaml** fil skapas automatiskt som en del av projektmallen för. Öppna den här filen och följ sedan instruktionerna:

* Ersätt ditt programs **rutnätet** nod med följande kod:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Använd MSAL för att hämta en token för Microsoft Graph API

Det här avsnittet visar hur du använder MSAL för att hämta en token för Microsoft Graph API.

1.  I **MainPage.xaml.cs**, Lägg till referens för MSAL för klassen:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Ersätt Koden i din <code>MainPage</code> klassen med följande kod:

    ```csharp
    public sealed partial class MainPage : Page
    {
        // Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        // Set the scope for API call to user.read
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
Ett anrop till den `AcquireTokenAsync` metoden resulterar i ett fönster som uppmanar användaren att logga in. Program kräver vanligtvis användare att logga in interaktivt första gången de behöver för att få åtkomst till en skyddad resurs. De kan också behöva logga in när en tyst att hämta en token misslyckas. Ett exempel är när en användares lösenord har upphört att gälla.

#### <a name="get-a-user-token-silently"></a>Hämta token för en användare tyst
Den `AcquireTokenSilentAsync` metoden hanterar token anskaffning och förnyelser utan någon användarinteraktion. Efter `AcquireTokenAsync` körs för första gången och användaren tillfrågas om autentiseringsuppgifter, den `AcquireTokenSilentAsync` metoden som ska användas för att begära token för efterföljande anrop eftersom den hämta token tyst. MSAL hanterar token-cache och förnyelse.

Slutligen den `AcquireTokenSilentAsync` metoden misslyckas. Orsaker till felet kan vara att användare har loggat ut eller ändra sina lösenord på en annan enhet. När MSAL upptäcker att problemet kan lösas genom att kräva en interaktiv åtgärd, den utlöses en `MsalUiRequiredException` undantag. Programmet kan hantera det här undantaget på två sätt:

* Det kan göra att ett anrop mot `AcquireTokenAsync` omedelbart. Det här anropet resulterar i att användaren uppmanas att logga in. Normalt används det här mönstret i Onlineprogram där det finns inga tillgängliga offline innehåll för användaren. Exempel som genererats av den här interaktiv installation följer mönstret. Du kan se den i åtgärden första gången du kör exemplet. 
    * Eftersom ingen användare har använt programmet, `PublicClientApp.Users.FirstOrDefault()` innehåller ett null-värde och ett `MsalUiRequiredException` undantag.
    * Koden i exemplet hanterar undantaget genom att anropa `AcquireTokenAsync`. Det här anropet resulterar i att användaren uppmanas att logga in.

* Eller i stället det utgör en indikering för användare att en interaktiv inloggning krävs. Sedan kan de välja rätt tid att logga in. Eller programmet kan försöka `AcquireTokenSilentAsync` senare. Det här mönstret används ofta när användarna kan använda andra funktioner i programmet utan avbrott. Ett exempel är när offline innehållet är tillgängligt i programmet. I det här fallet kan användare bestämma när de vill logga in att komma åt den skyddade resursen eller uppdatera informationen om gamla. Eller annan programmet kan bestämma att försöka `AcquireTokenSilentAsync` när nätverket återställs efter att den inte tillgänglig för tillfället.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Anropa Microsoft Graph API med hjälp av den token som du precis erhölls

* Lägg till följande nya metod för att **MainPage.xaml.cs**. Den här metoden används för att göra en `GET` begäran mot Graph API med hjälp av ett [auktorisera]-huvud:

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
            // Add the token in Authorization header
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

I det här exempelprogrammet i `GetHttpContentWithToken` metoden används för att en HTTP `GET` begäran mot en skyddad resurs som kräver ett token. Metoden returnerar sedan innehållet till anroparen. Den här metoden lägger till anskaffats token i den **http-auktorisering** huvud. Resursen för det här exemplet är Microsoft Graph API **mig** slutpunkt som visar information om användarens profil.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Lägg till en metod för att logga ut användaren

* Lägg till följande metod för att logga ut användaren genom **MainPage.xaml.cs**:

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

### <a name="more-information-on-sign-out"></a>Mer information om utloggning

Den `SignOutButton_Click` metoden tar bort användaren från användarcachen MSAL. Den här metoden säger MSAL att glömma den aktuella användaren. Sedan lyckas en framtida begäran om att hämta en token bara om den har gjort kan köras interaktivt.
Programmet i det här exemplet har stöd för en enskild användare. Men MSAL har stöd för scenarier där fler än ett konto kan logga in på samma gång. Ett exempel är ett e-postprogram där en användare har flera konton.

## <a name="display-basic-token-information"></a>Visa grundläggande information om token

* Lägg till följande metod för att **MainPage.xaml.cs** att visa grundläggande information om token:

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

ID-token har köpt **OpenID Connect** också innehålla en liten del av information som är relevant för användaren. `DisplayBasicTokenInfo` Visar grundläggande information som finns i token. Exempel är användarens namn och ID, utgångsdatum för token och den sträng som representerar den åtkomst-token. Om du väljer den **anropa Microsoft Graph API** knappen flera gånger, ser du att samma token återanvänts för efterföljande förfrågningar. Du kan också se extended när MSAL bestämmer är det dags att förnya token upphör att gälla.

## <a name="register-your-application"></a>Registrera ditt program

Nu måste du registrera programmet i portalen för registrering av Microsoft-program:
1. Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/portal/register-app) registrera ett program.
2. Ange ett namn för ditt program.
3. Kontrollera att alternativet för **interaktiv installation** är *inte valt*.
4. Välj **lägga till plattformar**väljer **programspecifika**, och välj sedan **spara**.
5. Kopiera GUID i **program-ID**, gå tillbaka till Visual Studio, öppna **App.xaml.cs**, och Ersätt `your_client_id_here` med program-ID som du just har registrerat:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Aktivera integrerad autentisering på externa domäner (valfritt)

Om du vill aktivera Windows-integrerad autentisering när den används med en federerade Azure Active Directory-domän, aktivera programmanifestet ytterligare funktioner:

1. Dubbelklicka på **Package.appxmanifest**.
2. Välj den **funktioner** fliken och se till att följande inställningar har aktiverats:

    - Enterprise-autentisering
    - Privata nätverk (klient och Server)
    - Delade användarcertifikat

3. Öppna **App.xaml.cs** och Lägg till följande rad i app-konstruktorn:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> Windows-integrerad autentisering är inte konfigurerad som standard för det här exemplet. Program som begär *Företagsautentisering* eller *delade användarcertifikat* funktioner kräver en högre säkerhetsnivå för verifiering av Windows Store. Inte alla utvecklare också att utföra den högre nivån av verifiering. Den här inställningen aktiveras endast om du behöver Windows-integrerad autentisering med en federerade Azure Active Directory-domän.


## <a name="test-your-code"></a>Testa din kod

Välj F5 för att köra projektet i Visual Studio för att testa ditt program. Huvudfönstret visas:

![Programmets användargränssnitt](media/active-directory-uwp-v2.md/testapp-ui.png)

När du är redo att testa väljer **anropa Microsoft Graph API**. Använda ett organisationskonto med Microsoft Azure Active Directory eller ett Microsoft-konto, t.ex live.com eller outlook.com, för att logga in. Om det är första gången visas ett fönster som ber användaren att logga in:

![Inloggningssidan](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>Samtycke
Första gången du loggar in på ditt program som du kan välja mellan en medgivande skärm som liknar följande. Välj **Ja** explicit tillstånd att få åtkomst till:

![Åtkomst till ditt medgivande skärmen](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>Förväntat resultat
Du ser användarens profilinformation som returneras av Microsoft Graph API-anrop på den **API-anrop resultat** skärm:

![API-anrop resultat skärmen](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

Du också se grundläggande information om token har köpt `AcquireTokenAsync` eller `AcquireTokenSilentAsync` i den **Token Info** rutan:

|Egenskap   |Format  |Beskrivning |
|---------|---------|---------|
|**Namn** |Användarens fullständiga namn|Användaren förnamn och efternamn.|
|**Användarnamn** |<span>user@domain.com</span> |Användarnamnet som identifierar användaren.|
|**Token upphör att gälla** |DateTime |Den tid när token upphör att gälla. MSAL utökar utgångsdatumet genom att förnya token efter behov.|
|**Åtkomst-Token** |Sträng |Tokensträng som skickas till HTTP-begäranden som kräver en *Authorization-huvud*.|

#### <a name="see-whats-in-the-access-token-optional"></a>Vad är i ett åtkomsttoken (valfritt)
Du kan också kopiera värdet i **åtkomst-Token** och klistra in den i https://jwt.ms att avkoda det och visa listan med anspråk.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver den *user.read* omfång att läsa en användares profil. Detta scope läggs automatiskt som standard i alla program som har registrerats i portalen för registrering av programmet. Andra API: er för Microsoft Graph och anpassade API: er för backend-servern, kan kräva ytterligare scope. Microsoft Graph API kräver den *Calendars.Read* scope för att visa användarens kalendrar.

För att få åtkomst till användarkalendrar i kontexten för ett program måste du lägga till den *Calendars.Read* delegerad behörighet att registreringsinformation för programmet. Lägg sedan till den *Calendars.Read* omfånget för den `acquireTokenSilent` anropa. 

> [!NOTE]
> Användare kan uppmanas att ytterligare medgivanden när du ökar antalet scope.

## <a name="known-issues"></a>Kända problem

### <a name="issue-1"></a>Nummer 1
Du får ett av följande felmeddelanden när du loggar in på ditt program i en federerad Azure Active Directory-domän:
 - Inget giltigt certifikat hittades i begäran.
 - Inga giltiga certifikat finns i användarens certifikatarkiv.
 - Försök igen genom att välja en annan autentiseringsmetod.

**Orsak:** Enterprise-och certifikatet har inte aktiverats.

**Lösning:** följer du stegen i [integrerad autentisering på externa domäner](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problemet 2
Du aktiverar [integrerad autentisering på externa domäner](#enable-integrated-authentication-on-federated-domains-optional) och försök att använda Windows Hello på en Windows 10-dator för att logga in i en miljö med flerfunktionsautentisering som har konfigurerats. Listan över certifikat visas. Om du väljer att använda din PIN-kod exponeras aldrig fönstret PIN-kod.

**Orsak:** problemet är en känd begränsning av webbautentiseringskoordinatorn i UWP-program som körs på Windows 10 desktop. Den fungerar på Windows 10 Mobile.

**Lösning:** Välj **logga in med andra alternativ**. Välj sedan **logga in med ett användarnamn och lösenord**. Välj **ange ditt lösenord**. Gå sedan igenom autentiseringsprocessen phone.
