---
title: Kom igång med Microsoft Identity Platform UWP | Azure
description: Hur Universell Windows-plattform-program (UWP) kan anropa ett API som kräver åtkomsttoken av Microsoft Identity Platform-slutpunkten.
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 519a86bed6a3a09b476bce6435ae666d655dbe03
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852269"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Anropa Microsoft Graph-API från ett Universell Windows-plattform program (XAML)

> [!div renderon="docs"]

Den här guiden förklarar hur ett internt Universell Windows-plattform-program (UWP) kan begära en åtkomsttoken och sedan anropa Microsoft Graph API. Guiden gäller även andra API: er som kräver åtkomsttoken från Microsoft Identity Platform-slutpunkten.

I slutet av den här guiden anropar programmet ett skyddat API med hjälp av personliga konton. Exempel är outlook.com, live.com och andra. Ditt program anropar också arbets-och skol konton från alla företag eller organisationer som har Azure Active Directory (Azure AD).

>[!NOTE]
> Den här guiden kräver att Visual Studio 2017 med Universell Windows-plattform-utveckling installerat. Se [Hämta inställningar](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) för instruktioner för att hämta och konfigurera Visual Studio för att utveckla universell Windows-plattform appar.

## <a name="how-this-guide-works"></a>Så här fungerar den här guiden

![Visar hur exempel appen som genereras av den här själv studie kursen fungerar](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Den här guiden skapar ett exempel på ett UWP-program som frågar Microsoft Graph API eller ett webb-API som accepterar token från Microsoft Identity Platform-slutpunkten. I det här scenariot läggs en token till i HTTP-begäranden via Authorization-huvudet. Microsoft Authentication Library (MSAL) hanterar hämtning av token och förnyelser.

## <a name="nuget-packages"></a>NuGet-paket

I den här guiden används följande NuGet-paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsofts autentiseringsbibliotek|

## <a name="set-up-your-project"></a>Konfigurera projektet

Det här avsnittet innehåller steg-för-steg-instruktioner för att integrera ett Windows Desktop .NET-program (XAML) med *inloggning med Microsoft*. Sedan kan den fråga webb-API: er som kräver en token, till exempel Microsoft Graph API.

Den här guiden skapar ett program som visar en knapp som frågar Graph API, en utloggnings knapp och text rutor som visar resultatet av anropen.

> [!NOTE]
> Vill du hämta det här exemplets Visual Studio-projekt i stället? [Ladda ned ett projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) och gå vidare till(#register-your-application "steget") [program]registrering för program registrering för att konfigurera kod exemplet innan det körs.

### <a name="create-your-application"></a>Skapa ditt program

1. Välj **Arkiv** > **Nytt** > **Projekt** i Visual Studio.
2. Under **mallar**väljer du **visuella C#** objekt.
3. Välj **Tom app (Universal Windows)** .
4. Ge appen ett namn och välj **OK**.
5. Om du uppmanas väljer du en version för **mål** och **lägsta** versioner och väljer **OK**.

    >![Minimi-och mål versioner](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Lägg till Microsoft Authentication Library i projektet
1. I Visual Studio väljer du **Verktyg** > **NuGet Package Manager** > **Package Manager Console**.
2. Kopiera och klistra in följande kommando i fönstret **Package Manager-konsol** :

    ```powershell
    Install-Package Microsoft.Identity.Client -IncludePrerelease
    ```

> [!NOTE]
> Det här kommandot installerar [Microsoft Authentication Library](https://aka.ms/msal-net). MSAL skaffar, cachelagrar och uppdaterar användar-tokens som har åtkomst till API: er som skyddas av Microsoft Identity Platform.

## <a name="create-your-applications-ui"></a>Skapa ditt programs användar gränssnitt

En **mainpage. XAML** -fil skapas automatiskt som en del av projekt mal len. Öppna den här filen och följ sedan anvisningarna:

* Ersätt programmets **Grid** -nod med följande kod:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Använd MSAL för att hämta en token för Microsoft Graph-API

Det här avsnittet visar hur du använder MSAL för att hämta en token för Microsoft Graph-API.

1.  I **mainpage.XAML.cs**lägger du till referensen för MSAL i klassen:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Ersätt koden för <code>MainPage</code> klassen med följande kod:

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
        /// Call AcquireTokenInteractive - to acquire a token requiring user to sign-in
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

Ett anrop till `AcquireTokenInteractive` metoden resulterar i ett fönster där användarna uppmanas att logga in. Program kräver vanligt vis att användare loggar in interaktivt första gången de behöver åtkomst till en skyddad resurs. De kan också behöva logga in när en tyst åtgärd för att hämta en token Miss lyckas. Ett exempel är när en användares lösen ord har upphört att gälla.

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

`AcquireTokenSilent` Metoden hanterar hämtning av token och förnyelser utan några åtgärder från användaren. När `AcquireTokenInteractive` har körts för första gången och användaren uppmanas att ange autentiseringsuppgifter `AcquireTokenSilent` , ska metoden användas för att begära token för efterföljande anrop eftersom den hämtar token tyst. MSAL kommer att hantera token cache och förnyelse.

Slutligen kan `AcquireTokenSilent` metoden Miss lyckas. Orsaken till ett haveri kan vara att användare antingen har loggat ut eller ändrat sitt lösen ord på en annan enhet. När MSAL upptäcker att problemet kan lösas genom att kräva en interaktiv åtgärd utlöses ett `MsalUiRequiredException` undantag. Ditt program kan hantera detta undantag på två sätt:

* Det kan göra ett anrop `AcquireTokenInteractive` direkt. Det här anropet resulterar i att användaren uppmanas att logga in. Normalt används det här mönstret i online-program där det inte finns något tillgängligt offline-innehåll för användaren. Exemplet som genereras av den här guidade installationen följer mönstret. Du ser det i åtgärd första gången du kör exemplet.
  * Eftersom ingen användare har använt programmet, `accounts.FirstOrDefault()` innehåller ett null-värde och ett `MsalUiRequiredException` undantag genereras.
  * Koden i exemplet hanterar sedan undantaget genom att anropa `AcquireTokenInteractive`. Det här anropet resulterar i att användaren uppmanas att logga in.

* Eller i stället visas ett visuellt meddelande för användarna om att det krävs en interaktiv inloggning. Sedan kan de välja rätt tid för att logga in. Eller så kan programmet försöka `AcquireTokenSilent` igen senare. Ofta används det här mönstret när användare kan använda andra program funktioner utan avbrott. Ett exempel är när offlineinnehåll är tillgängligt i programmet. I det här fallet kan användare bestämma när de vill logga in för att antingen komma åt den skyddade resursen eller uppdatera den inaktuella informationen. Annars kan programmet välja att försöka igen `AcquireTokenSilent` när nätverket återställs efter att det är tillfälligt otillgängligt.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Anropa Microsoft Graph-API med hjälp av den token som du nyss hämtade

* Lägg till följande nya metod i **mainpage.XAML.cs**. Den här metoden används för att skapa `GET` en begäran mot Graph API med hjälp `Authorization` av ett sidhuvud:

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du gör ett REST-anrop mot ett skyddat API

I det här exempel programmet `GetHttpContentWithToken` används metoden för att göra en http- `GET` begäran mot en skyddad resurs som kräver en token. Sedan returnerar metoden innehållet till anroparen. Den här metoden lägger till den hämtade token i **http-Authorization-** huvudet. I det här exemplet är resursen Microsoft Graph-API **mig** -slutpunkt, som visar användarens profil information.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Lägg till en metod för att logga ut användaren

* Om du vill logga ut användaren lägger du till följande metod i **mainpage.XAML.cs**:

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
> MSAL.net använder asynkrona metoder för att förvärva token eller manipulera konton, och därför måste du göra användar gränssnitts-Ed-åtgärder i UI-tråden, och därmed `Dispatcher.RunAsync`de försiktighets åtgärder som krävs för att anropa`ConfigureAwait(false)`

### <a name="more-information-on-sign-out"></a>Mer information om utloggning

`SignOutButton_Click` Metoden tar bort användaren från MSAL-användarcache. Den här metoden instruerar MSAL att glömma den aktuella användaren. Sedan lyckas en framtida begäran om att hämta en token endast om den är interaktiv.
Programmet i det här exemplet stöder en enskild användare. Men MSAL stöder scenarier där mer än ett konto kan loggas in samtidigt. Ett exempel är ett e-postprogram där en användare har flera konton.

## <a name="display-basic-token-information"></a>Visa grundläggande information om token

* Lägg till följande metod i **mainpage.XAML.cs** för att visa grundläggande information om token:

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

ID-token som förvärv ATS via **OpenID Connect** innehåller också en liten delmängd av information som är relevant för användaren. `DisplayBasicTokenInfo`visar grundläggande information som finns i token. Exempel är användarens visnings namn och ID, utgångs datum för token och strängen som representerar åtkomsttoken. Om du väljer API-knappen **anropa Microsoft Graph** flera gånger ser du att samma token återanvändes för efterföljande begär Anden. Du kan också se utgångs datum för utökad när MSAL bestämmer att det är dags att förnya token.

## <a name="register-your-application"></a>Registrera ditt program

Nu måste du registrera ditt program på Microsofts applikations registrerings Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto finns i fler än en Azure AD-klient väljer `Directory + Subscription` du det övre högra hörnet på menyn längst upp på sidan och byter Portal-sessionen till önskad Azure AD-klient.
1. Gå till sidan Microsoft Identity Platform för utvecklare [Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Välj **ny registrering**.
   - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `UWP-App-calling-MSGraph`.
   - I avsnittet **konto typer som stöds** väljer du **konton i valfri organisations katalog och personliga Microsoft-konton (t. ex. Skype, Xbox, Outlook.com)** .
   - Välj **Registrera** för att skapa programmet.
1. På sidan **Översikt över** appen letar du reda på **programmets ID-** värde och registrerar det för senare. Gå tillbaka till Visual Studio, öppna **mainpage.XAML.cs**och Ersätt värdet för ClientId med det program-ID som du nyss registrerade:
1. I listan över sidor för appen väljer du **Autentisering**.
   1. I avsnittet omdirigerings- **URI** : er i listan omdirigerings-URI: er:
   1. I kolumnen **typ** väljer du **offentlig klient (mobil & Desktop)** .
   1. Ange `urn:ietf:wg:oauth:2.0:oob` i kolumnen omdirigerings- **URI** .
1. Välj **Spara**.
1. I listan över sidor för appen väljer du API- **behörigheter**
   - Klicka på knappen **Lägg till en behörighet** och sedan
   - Kontrol lera att **Microsoft API: s** flik är markerad
   - I avsnittet *vanliga API: er för Microsoft* klickar du på **Microsoft Graph**
   - I avsnittet **delegerade behörigheter** kontrollerar du att rätt behörigheter är markerade: **User. Read**. Använd Sök fältet om det behövs.
   - Välj knappen **Lägg till behörigheter**

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Aktivera integrerad autentisering på federerade domäner (valfritt)

För att aktivera Windows-integrerad autentisering när den används med en federerad Azure AD-domän måste applikations manifestet aktivera ytterligare funktioner:

1. Dubbelklicka på **Package. appxmanifest**.
2. Välj fliken **funktioner** och kontrol lera att följande inställningar är aktiverade:

    - Enterprise-autentisering
    - Privata nätverk (klient & Server)
    - Delade användar certifikat

> [!IMPORTANT]
> [Integrerad Windows-autentisering](https://aka.ms/msal-net-iwa) är inte konfigurerat som standard för det här exemplet. Program som begär funktioner för *företags autentisering* eller *delade användar certifikat* kräver en högre verifierings nivå av Windows Store. Alla utvecklare vill inte heller utföra den högre verifierings nivån. Aktivera endast den här inställningen om du behöver Windows-integrerad autentisering med en federerad Azure AD-domän.

## <a name="test-your-code"></a>Testa koden

Om du vill testa programmet väljer du F5 för att köra projektet i Visual Studio. Ditt huvud fönster visas:

![Programmets användar gränssnitt](./media/tutorial-v2-windows-uwp/testapp-ui.png)

När du är redo att testa väljer du **anropa Microsoft Graph API**. Använd sedan ett organisations konto i Azure AD eller en Microsoft-konto, till exempel live.com eller outlook.com, för att logga in. Om det är första gången visas ett fönster där användaren uppmanas att logga in:

![Inloggnings sida](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Samtycke

Första gången du loggar in på ditt program visas en godkännande skärm som liknar följande. Välj **Ja** om du uttryckligen vill ge åtkomst:

![Åtkomst till medgivande skärmen](./media/tutorial-v2-windows-uwp/consentscreen.png)

### <a name="expected-results"></a>Förväntat resultat

Du ser information om användar profiler som returneras av Microsoft Graph API-anropet på skärmen med **API-anrops resultat** :

![Resultat skärm för API-anrop](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Du ser också grundläggande information om den token som `AcquireTokenInteractive` hämtats via eller `AcquireTokenSilent` i rutan token- **information** :

|Egenskap  |Format  |Beskrivning |
|---------|---------|---------|
|**Användarnamn** |<span>user@domain.com</span> |Det användar namn som identifierar användaren.|
|**Token upphör att gälla** |DateTime |Tiden då token upphör att gälla. MSAL utökar förfallo datumet genom att förnya token vid behov.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om omfattningar och delegerade behörigheter

Microsoft Graph API kräver att *användaren. Read* -omfånget ska läsa en användares profil. Det här omfånget läggs automatiskt till som standard i varje program som är registrerat på program registrerings portalen. Andra API: er för Microsoft Graph och anpassade API: er för backend-servern kan kräva ytterligare omfång. Microsoft Graph API kräver *kalendrar. Läs* omfattning för att lista användarens kalendrar.

Om du vill komma åt användarens kalendrar i ett programs kontext lägger du till *kalendrarna. Läs* behörighet för program registrerings informationen. Lägg sedan till *kalendrarna. Läs* omfång till `acquireTokenSilent` anropet.

> [!NOTE]
> Användare kan tillfrågas om ytterligare medgivanden när du ökar antalet omfång.

## <a name="known-issues"></a>Kända problem

### <a name="issue-1"></a>Ärende 1

Du får ett av följande fel meddelanden när du loggar in på ditt program på en federerad Azure AD-domän:

* Inget giltigt klient certifikat hittades i begäran.
* Inga giltiga certifikat hittades i användarens certifikat arkiv.
* Försök igen och välj en annan autentiseringsmetod.

**Orsak** Företags-och certifikat funktioner är inte aktiverade.

**Lösa** Följ stegen i [integrerad autentisering på federerade domäner](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Ärende 2

Du aktiverar [integrerad autentisering på federerade domäner](#enable-integrated-authentication-on-federated-domains-optional) och försöker använda Windows Hello på en Windows 10-dator för att logga in i en miljö med Multi-Factor Authentication konfigurerad. Listan över certifikat visas. Men om du väljer att använda din PIN-kod visas inte PIN-fönstret.

**Orsak** Det här problemet är en känd begränsning i Web Authentication Broker i UWP-program som körs på Windows 10 Desktop. Det fungerar bra på Windows 10 Mobile.

**Korrigera** Välj **Logga in med andra alternativ**. Välj sedan **Logga in med ett användar namn och lösen ord**. Välj **Ange ditt lösen ord**. Gå sedan igenom processen för att autentisera telefonen.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Hjälp oss att förbättra Microsoft Identity Platform. Berätta för oss vad du tycker genom att slutföra en kort enkät med två frågor.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform-undersökning](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
