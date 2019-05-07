---
title: Microsoft identity-plattformen UWP komma igång | Azure
description: Hur program för universell Windows-plattform (UWP) kan anropa ett API som kräver åtkomst-token med Microsoft identity-plattformen slutpunkten.
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
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 448858efeaae4c3e2a41d41181e9ec74b03223f6
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138257"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Anropa Microsoft Graph API från en Universal Windows Platform-program (XAML)

> [!div renderon="docs"]
> [!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Den här guiden beskriver hur ett internt Universal Windows Platform (UWP)-program kan begära en åtkomst-token och sedan anropa Microsoft Graph API. I guiden gäller även för andra API: er som kräver åtkomst-token från slutpunkten för Microsoft identity-plattformen.

I slutet av den här guiden anropar ett skyddade API med hjälp av personliga konton i ditt program. Exempel är outlook.com, live.com och andra. Programmet anropar även arbets-och skolkonton från alla företag eller organisation som har Azure Active Directory (AD Azure).

>[!NOTE]
> Den här guiden kräver Visual Studio 2017 med Universal Windows Platform-utveckling som är installerad. Se [konfigureras](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) för att hämta och konfigurera Visual Studio för att utveckla Universal Windows Platform-appar.

## <a name="how-this-guide-works"></a>Så här fungerar den här guiden

![Visar hur exempelapp som genererats av den här kursen fungerar](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Den här guiden skapar ett UWP-exempelprogram som frågar Microsoft Graph API eller ett webb-API som accepterar token från slutpunkten för Microsoft identity-plattformen. Det här scenariot läggs en token till HTTP-förfrågningar via auktoriseringsrubriken. Microsoft Authentication Library (MSAL) hanterar token anskaffning och förnyelser.

## <a name="nuget-packages"></a>NuGet-paket

Den här guiden använder följande NuGet-paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsofts autentiseringsbibliotek|

## <a name="set-up-your-project"></a>Konfigurera projektet

Det här avsnittet innehåller stegvisa instruktioner för att integrera ett Windows Desktop .NET-program (XAML) med *logga In med Microsoft*. Den kan sedan fråga webb-API: er som kräver en token, till exempel Microsoft Graph API.

Den här guiden skapar ett program som visar en knapp som frågar Graph API, en utloggningsknapp och textrutor som visar resultatet av anrop.

> [!NOTE]
> Vill du hämta det här exemplet Visual Studio-projekt i stället? [Ladda ned ett projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) och gå vidare till den [programregistrering](#register-your-application "program registreringssteget") steg för att konfigurera kodexemplet innan den körs.

### <a name="create-your-application"></a>Skapa ditt program

1. Välj **Arkiv** > **Nytt** > **Projekt** i Visual Studio.
2. Under **mallar**väljer **Visual C#**.
3. Välj **Tom app (Universal Windows)**.
4. Ge appen ett namn och välj **OK**.
5. Om det händer väljer du någon version för **Target** och **minsta** versioner och välj **OK**.

    >![Lägsta och mål-versioner](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Lägg till Microsoft Authentication Library i projektet
1. I Visual Studio väljer du **Verktyg** > **NuGet Package Manager** > **Package Manager Console**.
2. Kopiera och klistra in följande kommando i den **Pakethanterarkonsolen** fönster:

    ```powershell
    Install-Package Microsoft.Identity.Client -IncludePrerelease
    ```

> [!NOTE]
> Det här kommandot installerar [Microsoft Authentication Library](https://aka.ms/msal-net). MSAL förvärvar, cachelagrar och uppdaterar användartoken som har åtkomst till API: er som skyddas av Microsoft identity-plattformen.

## <a name="create-your-applications-ui"></a>Skapa programmets användargränssnitt

En **MainPage.xaml** fil skapas automatiskt som en del av din projektmall. Öppna den här filen och följ sedan instruktionerna:

* Ersätt ditt programs **Grid** noden med följande kod:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Om du Använd MSAL för att hämta en token för Microsoft Graph API

Det här avsnittet visar hur du använder MSAL för att hämta en token för Microsoft Graph API.

1.  I **MainPage.xaml.cs**, lägga till referensen för MSAL i klassen:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Ersätt Koden i din <code>MainPage</code> klassen med följande kod:

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - Te content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";        

        public IPublicClientApplication PublicClientApp { get; } 

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

         // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.            
         IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false); 
         IAccount firstAccount = accounts.FirstOrDefault();

         try
         {
          authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
         }
         catch (MsalUiRequiredException ex)
         {
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    ```

### <a name="more-information"></a>Mer information

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt

Ett anrop till den `AcquireTokenInteractive` metoden resulterar i ett fönster som uppmanar användaren att logga in. Program kräver vanligtvis användare att logga in interaktivt första gången som de behöver för att få åtkomst till en skyddad resurs. De kan också behöva logga in när en tyst åtgärden att hämta en token misslyckas. Ett exempel är när en användares lösenord har upphört att gälla.

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Den `AcquireTokenSilent` metoden hanterar token anskaffning och förnyelser utan någon användarinteraktion. Efter `AcquireTokenInteractive` körs för första gången och användaren uppmanas att ange autentiseringsuppgifter för den `AcquireTokenSilent` metoden ska användas för att begära token för efterföljande anrop eftersom token får obevakat. MSAL hanterar token-cache och förnyelse.

Slutligen den `AcquireTokenSilent` metoden misslyckas. Orsaker till felet kan vara att användare har loggat ut eller ändrat sitt lösenord på en annan enhet. När MSAL upptäcker att problemet kan lösas genom att kräva en interaktiv åtgärd, det utlöses en `MsalUiRequiredException` undantag. Programmet kan hantera det här undantaget på två sätt:

* Det kan göra ett anrop mot `AcquireTokenInteractive` omedelbart. Det här anropet resulterar i uppmanar användaren att logga in. Det här mönstret används vanligtvis i online-program där det finns inga tillgängliga offline innehåll för användaren. I exemplet som genererats av den här guidade konfigurationen följer mönstret. Du kan se den i åtgärden först gången du kör exemplet.
  * Eftersom ingen användare har använt programmet, `accounts.FirstOrDefault()` innehåller ett null-värde och ett `MsalUiRequiredException` undantagsfel.
  * Koden i exemplet hanterar undantaget genom att anropa `AcquireTokenInteractive`. Det här anropet resulterar i uppmanar användaren att logga in.

* Eller i stället det utgör en visuell indikering för användare att det krävs en interaktiv inloggning. Sedan kan de välja rätt tid att logga in. Eller programmet kan försöka `AcquireTokenSilent` senare. Det här mönstret används ofta, när användarna kan använda andra programfunktionen utan avbrott. Ett exempel är när offline innehållet är tillgängligt i programmet. I det här fallet kan användare bestämma när de vill logga in på antingen komma åt den skyddade resursen eller uppdatera gammal information. Eller annan programmet kan bestämma att försöka igen `AcquireTokenSilent` när nätverket har återställts efter det var inte tillgänglig för tillfället.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Anropa Microsoft Graph API med hjälp av den token som du precis fick

* Lägg till följande nya metod till **MainPage.xaml.cs**. Den här metoden används för att göra en `GET` begäran mot Graph API med hjälp av en `Authorization` rubrik:

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
            request.Headers.Authorization = 
              new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du gör ett REST-anrop mot ett skyddade API

I det här exempelprogrammet den `GetHttpContentWithToken` metod för att göra HTTP `GET` begäran mot en skyddad resurs som kräver ett token. Metoden returnerar sedan innehållet till anroparen. Den här metoden lägger till förvärvade token i den **HTTP-auktorisering** rubrik. I det här exemplet resursen är Microsoft Graph API **mig** slutpunkt, vilket visar användarens profilinformation.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Lägg till en metod för att logga ut användaren

* Lägg till följande metod för att logga ut användaren genom **MainPage.xaml.cs**:

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private async void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync
                                                              .ConfigureAwait(false);
        IAccount firstAccount = accounts.FirstOrDefault();

        try
        {
            await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                ResultText.Text = "User has signed-out";
                this.CallGraphButton.Visibility = Visibility.Visible;
                    this.SignOutButton.Visibility = Visibility.Collapsed;
                });
            }
            catch (MsalException ex)
            {
                ResultText.Text = $"Error signing-out user: {ex.Message}";
            }
        }
    ```

> [!NOTE]
> MSAL.NET använder asynkrona metoder för att hämta token eller ändra konton och därför måste du ta hand om gör UI-ed-åtgärder i UI-tråden, därför den `Dispatcher.RunAsync`, och de säkerhetsåtgärder som att anropa `ConfigureAwait(false)`

### <a name="more-information-on-sign-out"></a>Mer information om utloggning

Den `SignOutButton_Click` metoden tar bort användaren från MSAL användarcachen. Den här metoden meddelar effektivt MSAL att glömma den aktuella användaren. En framtida begäran att hämta en token kan sedan lyckas bara om det har gjort att köras interaktivt.
Programmet i det här exemplet har stöd för en enskild användare. Men MSAL har stöd för scenarier där fler än ett konto kan logga in på samma gång. Ett exempel är ett e-postprogram där en användare har flera konton.

## <a name="display-basic-token-information"></a>Visa grundläggande information för token

* Lägg till följande metod i **MainPage.xaml.cs** att visa grundläggande information om token:

    ```csharp
    /// <summary>
    /// Display basic information contained in the token. Needs to be called from the UI thead.
    /// </summary>
    private void DisplayBasicTokenInfo(AuthenticationResult authResult)
    {
        TokenInfoText.Text = "";
        if (authResult != null)
        {
            TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
            TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        }
    }
    ```

### <a name="more-information"></a>Mer information

ID-token har köpt **OpenID Connect** också innehålla en liten del av information som är relevant för användaren. `DisplayBasicTokenInfo` Visar grundläggande information som ingår i en token. Exempel är användarens namn och ID, utgångsdatum för token och den sträng som representerar den åtkomst-token. Om du väljer den **anropa Microsoft Graph API** knappen flera gånger, ser du att samma token återanvänts för efterföljande förfrågningar. Du kan också se utökade när MSAL beslutar är det dags att förnya token upphör att gälla.

## <a name="register-your-application"></a>Registrera ditt program

Nu måste du registrera ditt program i portalen för registrering av Microsoft-program:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto finns i mer än en Azure AD-klient, Välj `Directory + Subscription` i det övre högra hörnet på menyn på sidan och växeln portal sessionen med önskade Azure AD-klient.
1. Gå till Microsoft identity-plattformen för utvecklare [appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan.
1. Välj **ny registrering**.
   - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `UWP-App-calling-MSGraph`.
   - I den **stöds kontotyper** väljer **konton alla organisationskatalog och personliga Microsoft-konton (t.ex. Skype, Xbox, Outlook.com)**.
   - Välj **Registrera** för att skapa programmet.
1. I appen **översikt** sidan, hitta den **(klient)-ID: T** värde och spara den till senare. Gå tillbaka till Visual Studio, öppna **MainPage.xaml.cs**, och Ersätt värdet för ClientId med program-ID som du just registrerade:
1. I listan över sidor för appen väljer du **Autentisering**.
   1. I den **omdirigerings-URI: er** avsnittet i listan över omdirigerings-URI: er:
   1. I den **typ** kolumn väljer **offentlig klient (mobila och stationära)**.
   1. Ange `urn:ietf:wg:oauth:2.0:oob` i den **OMDIRIGERINGS-URI** kolumn.
1. Välj **Spara**.
1. I listan över sidor för appen, Välj **API-behörigheter**
   - Klicka på den **lägga till en behörighet** knappen och sedan,
   - Se till att den **Microsoft API** är markerad
   - I den *ofta används Microsoft APIs* klickar du på **Microsoft Graph**
   - I den **delegerade behörigheter** Kontrollera att rätt behörigheter kontrolleras: **User.Read**. Använd sökrutan om det behövs.
   - Välj den **Lägg till behörigheter** knappen

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Aktivera integrerad autentisering på federerade domäner (valfritt)

För att aktivera Windows-Integrated autentisering när den används med en federerad Azure AD-domän, applikationsmanifestet måste aktivera ytterligare funktioner:

1. Dubbelklicka på **Package.appxmanifest**.
2. Välj den **funktioner** fliken och kontrollera att följande inställningar är aktiverat:

    - Enterprise-autentisering
    - Privat nätverk (klient och Server)
    - Delade användarcertifikat

> [!IMPORTANT]
> [Integrerad Windows-autentisering](https://aka.ms/msal-net-iwa) konfigureras inte som standard för det här exemplet. Program som begär *Företagsautentisering* eller *delade användarcertifikat* funktioner kräver en högre säkerhetsnivå för verifiering av Windows Store. Dessutom bör alla utvecklare att utföra den högre nivån verifieringsmetod. Aktivera den här inställningen bara om du behöver Windows-integrerad autentisering med en federerad Azure AD-domän.

## <a name="test-your-code"></a>Testa koden

Testa ditt program genom att välja F5 för att köra projektet i Visual Studio. Huvudfönstret visas:

![Programmets användargränssnitt](./media/tutorial-v2-windows-uwp/testapp-ui.png)

När du är redo att testa väljer **anropa Microsoft Graph API**. Använd sedan ett Azure AD-organisationskonto eller ett Microsoft-konto, t.ex live.com eller outlook.com, för att logga in. Om det är första gången visas ett fönster som ber användaren att logga in:

![På inloggningssidan](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Samtycke

Första gången du loggar in på ditt program, visas en godkännandeskärmen som liknar följande. Välj **Ja** uttryckligen samtycker till att komma åt:

![Godkännandeskärmen för åtkomst](./media/tutorial-v2-windows-uwp/consentscreen.png)

### <a name="expected-results"></a>Förväntat resultat

Du ser information om användarprofiler returnerades av Microsoft Graph API-anrop på den **API samtalsresultat** skärmen:

![Anropa API-resultat skärmen](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Du också se grundläggande information om den token som anskaffats `AcquireTokenInteractive` eller `AcquireTokenSilent` i den **tokeninformation** box:

|Egenskap   |Format  |Beskrivning |
|---------|---------|---------|
|**Användarnamn** |<span>user@domain.com</span> |Användarnamnet som identifierar användaren.|
|**Token upphör att gälla** |DateTime |Den tid när token upphör att gälla. MSAL förlänger utgångsdatumet genom att förnya token efter behov.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API kräver den *user.read* omfattning att läsa en användares profil. Det här omfånget läggs automatiskt som standard i alla program som har registrerats i portalen för registrering av programmet. Andra API: er för Microsoft Graph och anpassade API: er för backend-servern, kan kräva ytterligare scope. Microsoft Graph API kräver den *Calendars.Read* omfattning att lista användarens kalendrar.

Om du vill få åtkomst till användarkalendrar i kontexten för ett program måste du lägga till den *Calendars.Read* delegerad behörighet att registreringsinformation för programmet. Lägg sedan till den *Calendars.Read* begränsa omfånget till den `acquireTokenSilent` anropa.

> [!NOTE]
> Användarna uppmanas för ytterligare medgivanden när du ökar antalet omfång.

## <a name="known-issues"></a>Kända problem

### <a name="issue-1"></a>Nummer 1

Du får något av följande felmeddelanden visas när du loggar in på ditt program i en federerad Azure AD-domän:

* Inget giltigt klientcertifikat hittades i begäran.
* Inga giltiga certifikat finns i användarens certifikatarkiv.
* Försök igen genom att välja en annan autentiseringsmetod.

**Orsak:** Enterprise-och certifikatet har inte aktiverats.

**Lösning:** Följ stegen i [integrerad autentisering på federerade domäner](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problemet 2

Du aktiverar [integrerad autentisering på federerade domäner](#enable-integrated-authentication-on-federated-domains-optional) och försök att använda Windows Hello på en Windows 10-dator för att logga in på en miljö med multifaktorautentisering som har konfigurerats. Listan över certifikat som visas. Om du väljer att använda din PIN-kod exponeras aldrig fönstret PIN-kod.

**Orsak:** Det här problemet är en känd begränsning av webbautentiseringskoordinatorn i UWP-program som körs på Windows 10 desktop. Den fungerar på Windows 10 Mobile.

**Lösning:** Välj **logga in med andra alternativ**. Välj sedan **logga in med ett användarnamn och lösenord**. Välj **ange ditt lösenord**. Gå sedan igenom phone autentiseringsprocessen.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
