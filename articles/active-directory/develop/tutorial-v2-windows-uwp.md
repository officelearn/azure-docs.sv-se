---
title: Microsoft identitetsplattform UWP komma igång | Azure
description: Hur UWP -program (Universal Windows Platform Applications) kan anropa ett API som kräver åtkomsttoken av slutpunkten för Microsoft-identitetsplattformen.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 205f4a27a7903bc5a1da3fc12e3d4a02b23f58cf
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990930"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Anropa Microsoft Graph API från ett XAML-program (Universal Windows Platform)

> [!div renderon="docs"]

Den här guiden förklarar hur ett inbyggt UWP-program (Universal Windows Platform) kan begära en åtkomsttoken. Programmet anropar sedan Microsoft Graph API. Guiden gäller även andra API:er som kräver åtkomsttoken från slutpunkten för Microsoft-identitetsplattformen.

I slutet av den här guiden anropar ditt program ett skyddat API med hjälp av personliga konton. Exempel är outlook.com, live.com och andra. Ditt program anropar också arbets- och skolkonton från alla företag eller organisationer som har Azure Active Directory (Azure AD).

>[!NOTE]
> Den här guiden kräver Visual Studio med universell windowsplattformsutveckling installerad. Se [Få instruktioner](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) om hur du hämtar och konfigurerar Visual Studio för att utveckla universella Windows-plattformsappar.

>[!NOTE]
> Om du inte har gjort det tidigare i Microsofts identitetsplattform rekommenderar vi att du börjar med [snabbstarten ring Microsoft Graph API från en UWP-program (Universal Windows Platform).](quickstart-v2-uwp.md)

## <a name="how-this-guide-works"></a>Så här fungerar den här guiden

![Visar hur exempelappen som genereras av den här självstudien fungerar](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Den här guiden skapar ett exempel på UWP-program som frågar Microsoft Graph API. I det här fallet läggs en token till i HTTP-begäranden med hjälp av auktoriseringshuvudet. Microsoft Authentication Library (MSAL) hanterar tokenförvärv och förnyelser.

## <a name="nuget-packages"></a>NuGet-paket

Den här guiden använder följande NuGet-paket:

|Bibliotek|Beskrivning|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsofts autentiseringsbibliotek|

## <a name="set-up-your-project"></a>Konfigurera projektet

Det här avsnittet innehåller steg-för-steg-instruktioner för att integrera ett Windows Desktop .NET-program (XAML) med Inloggning med Microsoft. Sedan kan programmet fråga webb-API:er som kräver en token, till exempel Microsoft Graph API.

Den här guiden skapar ett program som visar en knapp som frågar Graph API och en knapp för att logga ut. Den visar också textrutor som innehåller resultatet av samtalen.

> [!NOTE]
> Vill du hämta det här exemplets Visual Studio-projekt i stället för att skapa det? [Hämta ett projekt](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) och gå till [programregistreringssteget](#register-your-application "registreringssteg för ansökan") för att konfigurera kodexemplet innan det körs.

### <a name="create-your-application"></a>Skapa ditt program

1. Öppna Visual Studio och välj **Skapa ett nytt projekt**.
1. I **Skapa ett nytt projekt**väljer du Tom app **(Universella Fönster)** för C# och väljer **Nästa**.
1. I **Konfigurera ditt nya projekt**namnger du appen och väljer **Skapa**.
1. Om du uppmanas till det väljer du valfri version för **Mål-** och **minimiversioner** i **New Universal Windows Platform Project**och väljer **OK**.

   ![Lägsta- och Målversioner](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Lägga till Microsoft Authentication Library i projektet

1. I Visual Studio väljer du **Tools** > **NuGet Package Manager** > **Package Manager Console**.
1. Kopiera och klistra in följande kommando i fönstret **Package Manager Console:**

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > Med det här kommandot installeras [Microsoft Authentication Library](https://aka.ms/msal-net). MSAL förvärvar, cachelagrar och uppdaterar användartoken som använder API:er som skyddas av Microsofts identitetsplattform.

### <a name="create-your-applications-ui"></a>Skapa programmets användargränssnitt

Visual Studio skapar *MainPage.xaml* som en del av projektmallen. Öppna den här filen och ersätt **Grid** sedan programmets grid-nod med följande kod:

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

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Använda MSAL för att hämta en token för Microsoft Graph API

I det här avsnittet visas hur du använder MSAL för att hämta en token för Microsoft Graph API. Gör ändringar i *MainPage.xaml.cs* filen.

1. I *MainPage.xaml.cs*lägger du till följande referenser:

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Ersätt `MainPage` din klass med följande kod:

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
        // - the content of Tenant with the information about the accounts allowed to sign in in your application:
        //   - for Work or School account in your org, use your tenant ID, or domain
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
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Hämta en användartoken interaktivt<a name="more-information"></a>

Metoden `AcquireTokenInteractive` resulterar i ett fönster som uppmanar användarna att logga in. Program kräver vanligtvis att användare loggar in interaktivt första gången för att komma åt en skyddad resurs. De kan också behöva logga in när en tyst åtgärd för att hämta en token misslyckas. Ett exempel är när en användares lösenord har upphört att gälla.

#### <a name="get-a-user-token-silently"></a>Hämta en token obevakat

Metoden `AcquireTokenSilent` hanterar tokenförvärv och förnyelser utan någon användarinteraktion. När `AcquireTokenInteractive` körs för första gången och uppmanar användaren `AcquireTokenSilent` om autentiseringsuppgifter använder du metoden för att begära token för senare anrop. Den metoden hämtar token tyst. MSAL hanterar tokencache och förnyelse.

Så småningom `AcquireTokenSilent` misslyckas metoden. Orsaker till fel är en användare som har loggat ut eller ändrat sitt lösenord på en annan enhet. När MSAL upptäcker att problemet kräver en interaktiv `MsalUiRequiredException` åtgärd genereras ett undantag. Ditt program kan hantera det här undantaget på två sätt:

* Din ansökan `AcquireTokenInteractive` ringer omedelbart. Det här anropet leder till att användaren uppmanas att logga in. Använd normalt den här metoden för onlineprogram där det inte finns något tillgängligt offlineinnehåll för användaren. Exemplet som genereras av den här guidade inställningen följer mönstret. Du ser det i aktion första gången du kör provet.

   Eftersom ingen användare har `accounts.FirstOrDefault()` använt programmet, innehåller ett null-värde och genererar ett `MsalUiRequiredException` undantag.

   Koden i exemplet hanterar sedan undantaget `AcquireTokenInteractive`genom att anropa . Det här anropet leder till att användaren uppmanas att logga in.

* Programmet visar en visuell indikation för användarna att de måste logga in. Sedan kan de välja rätt tid att logga in. Programmet kan försöka `AcquireTokenSilent` igen senare. Använd den här metoden när användare kan använda andra programfunktioner utan avbrott. Ett exempel är när offlineinnehåll är tillgängligt i programmet. I det här fallet kan användarna bestämma när de vill logga in. Programmet kan försöka `AcquireTokenSilent` igen när nätverket inte var tillgängligt för tillfället.

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Anropa Microsoft Graph API med hjälp av den token du just fått

Lägg till följande nya metod för att *MainPage.xaml.cs:*

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

 Den här `GET` metoden gör en begäran `Authorization` från Graph API med hjälp av ett huvud.

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Mer information om hur du gör ett REST-anrop mot ett skyddat API

I det här `GetHttpContentWithToken` exempelprogrammet gör `GET` metoden en HTTP-begäran mot en skyddad resurs som kräver en token. Sedan returnerar metoden innehållet till anroparen. Den här metoden lägger till den förvärvade token i **HTTP-auktoriseringshuvudet.** För det här exemplet är resursen slutpunkten För Microsoft Graph API **me,** som visar användarens profilinformation.

### <a name="add-a-method-to-sign-out-the-user"></a>Lägga till en metod för att logga ut användaren

Om du vill logga ut användaren lägger du till följande metod för att *MainPage.xaml.cs:*

   ```csharp
   /// <summary>
   /// Sign out the current user
   /// </summary>
   private async void SignOutButton_Click(object sender, RoutedEventArgs e)
   {
       IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
       IAccount firstAccount = accounts.FirstOrDefault();

       try
       {
           await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
               ResultText.Text = "User has signed out";
               this.CallGraphButton.Visibility = Visibility.Visible;
                   this.SignOutButton.Visibility = Visibility.Collapsed;
               });
           }
           catch (MsalException ex)
           {
               ResultText.Text = $"Error signing out user: {ex.Message}";
           }
       }
   ```

> [!NOTE]
> MSAL.NET använder asynkrona metoder för att hämta token eller manipulera konton. Du måste stödja gränssnittsåtgärder i gränssnittstråden. Detta är anledningen `Dispatcher.RunAsync` till samtalet och `ConfigureAwait(false)`försiktighetsåtgärder att ringa .

#### <a name="more-information-about-signing-out"></a>Mer information om hur du loggar ut<a name="more-information-on-sign-out"></a>

Metoden `SignOutButton_Click` tar bort användaren från MSAL-användarcachen. Den här metoden talar effektivt msal att glömma den aktuella användaren. En framtida begäran om att hämta en token lyckas endast om den är interaktiv.

Programmet i det här exemplet stöder en enskild användare. MSAL stöder scenarier där användaren kan logga in på mer än ett konto. Ett exempel är ett e-postprogram där en användare har flera konton.

### <a name="display-basic-token-information"></a>Visa grundläggande tokeninformation

Lägg till följande metod för att *MainPage.xaml.cs* för att visa grundläggande information om token:

   ```csharp
   /// <summary>
   /// Display basic information contained in the token. Needs to be called from the UI thread.
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

#### <a name="more-information"></a>Läs mer<a name="more-information-1"></a>

ID-token som förvärvats med **OpenID Connect** innehåller också en liten delmängd av information som är relevant för användaren. `DisplayBasicTokenInfo`visar grundläggande information som finns i token. Den här informationen innehåller användarens visningsnamn och ID. Det innehåller också förfallodatum för token och strängen som representerar själva åtkomsttoken. Om du väljer knappen **Anropa Microsoft Graph API** flera gånger ser du att samma token återanvändades för senare begäranden. Du kan också se utgångsdatumet förlängas när MSAL beslutar att det är dags att förnya token.

### <a name="display-message"></a>Visa meddelande

Lägg till följande nya metod för att *MainPage.xaml.cs:*

   ```csharp
   /// <summary>
   /// Displays a message in the ResultText. Can be called from any thread.
   /// </summary>
   private async Task DisplayMessageAsync(string message)
   {
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
            () =>
            {
                ResultText.Text = message;
            });
        }
   ```

## <a name="register-your-application"></a>Registrera ditt program

Nu måste du registrera din ansökan:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj Azure Active**Directory-appregistreringar** **Azure Active Directory** > .
1. Välj **Ny registrering**. Ange ett meningsfullt programnamn som ska visas för användare av appen, till exempel *UWP-App-calling-MSGraph*.
1. Under **Kontotyper som stöds**väljer du Konton i en **organisationskatalog och personliga Microsoft-konton (t.ex.** **Register**
1. Leta reda på värdet **program -id för program (klient)** och kopiera det på översiktssidan. Gå tillbaka till Visual Studio, öppna *MainPage.xaml.cs*och `ClientId` ersätt värdet för med det här värdet.

Konfigurera autentisering för ditt program:

1. Tillbaka i [Azure-portalen](https://portal.azure.com)under **Hantera**väljer du **Autentisering**.
1. Kontrollera i avsnittet **Omdirigera URI:er** | **föreslagna omdirigerings-URI:er för offentliga klienter (mobil, stationär dator)** **https://login.microsoftonline.com/common/oauth2/nativeclient**.
1. Välj **Spara**.

Konfigurera API-behörigheter för ditt program:

1. Under **Hantera**väljer du **API-behörigheter**.
1. Välj **Lägg till en behörighet** och kontrollera sedan att du har valt **Microsoft-API:er**.
1. Välj **Microsoft Graph**.
1. Välj **Delegerade behörigheter**, sök efter *User.Read* och kontrollera att **User.Read** är markerat.
1. Om du har gjort några ändringar väljer du **Lägg till behörigheter** för att spara dem.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Aktivera integrerad autentisering på federerade domäner (valfritt)

Om du vill aktivera Windows-integrerad autentisering när den används med en federerad Azure AD-domän måste programmanifestet aktivera ytterligare funktioner. Gå tillbaka till ditt program i Visual Studio.

1. Öppna *Package.appxmanifest*.
1. Välj **Funktioner** och aktivera följande inställningar:

   * **Företagsautentisering**
   * **Privata nätverk (klient & server)**
   * **Delade användarcertifikat**

> [!IMPORTANT]
> [Integrerad Windows-autentisering](https://aka.ms/msal-net-iwa) är inte konfigurerad som standard för det här exemplet. Program som `Enterprise Authentication` `Shared User Certificates` begär eller anfunktioner kräver en högre verifieringsnivå av Windows Store. Dessutom vill inte alla utvecklare utföra den högre nivån av verifiering. Aktivera den här inställningen endast om du behöver Windows-integrerad autentisering med en federerad Azure AD-domän.

## <a name="test-your-code"></a>Testa koden

Om du vill testa programmet väljer du F5 för att köra projektet i Visual Studio. Huvudfönstret visas:

![Programmets användargränssnitt](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

När du är redo att testa väljer du **Anropa Microsoft Graph API**. Använd sedan ett Azure AD-organisationskonto eller ett Microsoft-konto, till exempel live.com eller outlook.com, för att logga in. Första gången en användare kör detta visar programmet ett fönster där användaren uppmanas att logga in.

### <a name="consent"></a>Samtycke

Första gången du loggar in på din ansökan visas en medgivandeskärm som liknar följande. Välj **Ja** om du uttryckligen vill medgivande för åtkomst:

![Skärmen Åtkomst med samtycke](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Förväntat resultat

Du ser användarprofilinformation som returneras av Microsoft Graph API-anropet på skärmen **API-samtalsresultat:**

![Skärmen Api-samtalsresultat](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Du ser även grundläggande information `AcquireTokenInteractive` om `AcquireTokenSilent` den token som förvärvats via eller i rutan **Token Info:**

|Egenskap  |Format  |Beskrivning |
|---------|---------|---------|
|`Username` |`user@domain.com` |Användarnamnet som identifierar användaren.|
|`Token Expires` |`DateTime` |Den tidpunkt då token upphör att gälla. MSAL förlänger utgångsdatumet genom att förnya token efter behov.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mer information om scope och delegerade behörigheter

Microsoft Graph API `user.read` kräver att omfattningen läser en användares profil. Det här scopet läggs till som standard i alla program som är registrerade i programregistreringsportalen. Andra API:er för Microsoft Graph och anpassade API:er för backend-servern kan kräva ytterligare scope. Microsoft Graph API kräver `Calendars.Read` till exempel att omfattningen ska kunna lista användarens kalendrar.

Om du vill komma åt användarens kalendrar `Calendars.Read` i samband med ett program lägger du till den delegerade behörigheten i programregistreringsinformationen. Lägg sedan `Calendars.Read` till `acquireTokenSilent` scopet i samtalet.

> [!NOTE]
> Användare kan uppmanas att ange ytterligare medgivanden när du ökar antalet scope.

## <a name="known-issues"></a>Kända problem

### <a name="issue-1"></a>Problem 1

Du får ett av följande felmeddelanden när du loggar in på ditt program på en federerad Azure AD-domän:

* Inget giltigt klientcertifikat hittades i begäran.
* Inga giltiga certifikat hittades i användarens certifikatarkiv.
* Försök att välja en annan autentiseringsmetod igen.

Orsak: Funktioner för företag och certifikat är inte aktiverade.

Lösning: Följ stegen i [Aktivera integrerad autentisering på federerade domäner (valfritt)](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problem 2

Du aktiverar [integrerad autentisering på federerade domäner](#enable-integrated-authentication-on-federated-domains-optional) och försöker använda Windows Hello på en Windows 10-dator för att logga in i en miljö med multifaktorautentisering konfigurerad. Listan över certifikat presenteras. Men om du väljer att använda pinkoden visas aldrig PIN-koden.

Orsak: Det här problemet är en känd begränsning av webbautentiseringsmäklaren i UWP-program som körs på Windows 10-skrivbordet. Det fungerar bra på Windows 10 Mobile.

Lösning: Välj **Logga in med andra alternativ**. Välj sedan **Logga in med ett användarnamn och lösenord**. Välj **Ange ditt lösenord**. Gå sedan igenom telefonens autentiseringsprocess.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
