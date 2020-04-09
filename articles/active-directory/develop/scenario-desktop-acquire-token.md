---
title: Skaffa en token för att anropa ett webb-API (skrivbordsapp) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar en skrivbordsapp som anropar webb-API:er för att hämta en token för appen
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a5942a9d614bbb06fadb1d4b16d4c68c007434c7
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885326"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Skrivbordsapp som anropar webb-API:er: Skaffa en token

När du har skapat en instans av det offentliga klientprogrammet använder du den för att hämta en token som du sedan använder för att anropa ett webb-API.

## <a name="recommended-pattern"></a>Rekommenderat mönster

Webb-API:et `scopes`definieras av dess . Oavsett vilken erfarenhet du ger i ditt program är mönstret att använda:

- Försök systematiskt att hämta en token `AcquireTokenSilent`från tokencachen genom att anropa .
- Om det här anropet `AcquireToken` misslyckas använder du det flöde `AcquireTokenXX`som du vill använda, vilket representeras här av .

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>I MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>I MSAL för iOS och macOS

Mål C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift:

```swift
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

        if (nsError.domain == MSALErrorDomain &&
            nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
        }
        return
    }
}
```
---

Här är de olika sätten att skaffa token i ett skrivbordsprogram.

## <a name="acquire-a-token-interactively"></a>Skaffa en token interaktivt

I följande exempel visas minimal kod för att få en token interaktivt för att läsa användarens profil med Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>I MSAL.NET

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>Obligatoriska parametrar

`AcquireTokenInteractive`har bara en obligatorisk ``scopes``parameter, , som innehåller en uppräkning av strängar som definierar de scope som en token krävs för. Om token är för Microsoft Graph finns de nödvändiga scopena i API-referensen för varje Microsoft Graph-API i avsnittet "Behörigheter". Om du till exempel vill [visa användarens kontakter](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)måste omfattningen "User.Read", "Contacts.Read" användas. Mer information finns i [Microsoft Graph-behörighetsreferens](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

På Android måste du också ange den `.WithParentActivityOrWindow`överordnade aktiviteten med hjälp av , som visas, så att token kommer tillbaka till den överordnade aktiviteten efter interaktionen. Om du inte anger det genereras ett `.ExecuteAsync()`undantag när du ringer .

### <a name="specific-optional-parameters-in-msalnet"></a>Specifika valfria parametrar i MSAL.NET

#### <a name="withparentactivityorwindow"></a>MedParentActivityOrWindow

Användargränssnittet är viktigt eftersom det är interaktivt. `AcquireTokenInteractive`har en specifik valfri parameter som kan ange, för plattformar som stöder det, det överordnade användargränssnittet. När den används i `.WithParentActivityOrWindow` ett skrivbordsprogram, har en annan typ, vilket beror på plattformen.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Anmärkningar:

- På .NET Standard `object` finns `Activity` det `UIViewController` förväntade på `NSWindow` Android, på `IWin32Window` `IntPr` iOS, på MAC och eller i Windows.
- I Windows måste `AcquireTokenInteractive` du anropa från gränssnittstråden så att den inbäddade webbläsaren får lämplig gränssnittssynssynkontext. Om du inte anropar från gränssnittstråden kan meddelanden inte pumpas korrekt och dödlägescenarier med användargränssnittet. Ett sätt att anropa MICROSOFT Authentication Libraries (MSALs) från gränssnittstråden om du inte `Dispatcher` redan finns i gränssnittstråden är att använda på WPF.
- Om du använder WPF kan du använda `WindowInteropHelper.Handle` klassen för att få ett fönster från en WPF-kontroll. Då samtalet är från en`this`WPF kontroll ( ):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>MedPrompt

`WithPrompt()`används för att styra interaktiviteten med användaren genom att ange en fråga.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Klassen definierar följande konstanter:

- ``SelectAccount``tvingar STS att presentera dialogrutan kontoval som innehåller konton som användaren har en session för. Det här alternativet är användbart när programutvecklare vill låta användare välja mellan olika identiteter. Det här alternativet ger ``prompt=select_account`` MSAL att skicka till identitetsprovidern. Det här alternativet är standardinställningen. Det gör ett bra jobb med att ge bästa möjliga upplevelse baserat på tillgänglig information, till exempel konto och närvaro av en session för användaren. Ändra det inte om du inte har goda skäl att göra det.
- ``Consent``gör det möjligt för programutvecklaren att tvinga användaren att uppmanas att godkännas, även om samtycke beviljades tidigare. I det här fallet `prompt=consent` skickar MSAL till identitetsprovidern. Det här alternativet kan användas i vissa säkerhetsfokuserade program där organisationen styrning kräver att användaren presenteras med dialogrutan medgivande varje gång programmet används.
- ``ForceLogin``gör det möjligt för programutvecklaren att låta användaren uppmanas att ange autentiseringsuppgifter av tjänsten, även om den här användarprompten kanske inte behövs. Det här alternativet kan vara användbart för att låta användaren logga in igen om det går att hämta en token. I det här fallet `prompt=login` skickar MSAL till identitetsprovidern. Ibland används den i säkerhetsfokuserade program där organisationens styrning kräver att användaren signerar varje gång de kommer åt specifika delar av ett program.
- ``Never``(endast för .NET 4.5 och WinRT) kommer inte att fråga användaren, utan försöker i stället använda cookien som lagras i den dolda inbäddade webbvyn. Mer information finns i webbvyer i MSAL.NET. Det kan misslyckas med att använda det här alternativet. I så `AcquireTokenInteractive` fall genererar ett undantag för att meddela att en gränssnittsinteraktion behövs. Du måste använda en `Prompt` annan parameter.
- ``NoPrompt``skickar ingen uppmaning till identitetsleverantören. Det här alternativet är endast användbart för Azure Active Directory (Azure AD) B2C-redigeringsprofilprinciper. Mer information finns i [Azure AD B2C-detaljer](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Den här modifieraren används i ett avancerat scenario där du vill att användaren ska godkänna flera resurser i förväg och du inte vill använda inkrementellt medgivande, som normalt används med MSAL.NET/the Microsoft-identitetsplattform. Mer information finns i [Ha användaren samtycke i förväg för flera resurser](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>MedCustomWebUi

Ett webbgränssnitt är en mekanism för att anropa en webbläsare. Denna mekanism kan vara en dedikerad UI WebBrowser kontroll eller ett sätt att delegera öppna webbläsaren.
MSAL tillhandahåller implementeringar av webbgränssnitt för de flesta plattformar, men det finns fall där du kanske vill vara värd för webbläsaren själv:

- Plattformar som inte uttryckligen omfattas av MSAL, till exempel Blazor, Unity och Mono på stationära datorer.
- Du vill UI testa ditt program och använda en automatiserad webbläsare som kan användas med Selenium.
- Webbläsaren och appen som kör MSAL finns i separata processer.

##### <a name="at-a-glance"></a>I korthet

För att uppnå detta ger `start Url`du till MSAL , som måste visas i en webbläsare val så att slutanvändaren kan ange objekt som deras användarnamn.
När autentiseringen är klar måste appen `end Url`gå tillbaka till MSAL , som innehåller en kod som tillhandahålls av Azure AD.
Värden för `end Url` är `redirectUri`alltid . Om `end Url`du vill avlyssna gör du något av följande:

- Övervaka omdirigeringar av webbläsaren tills `redirect Url` den träffas.
- Låt webbläsaren omdirigera till en URL som du övervakar.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi är en utökningspunkt

`WithCustomWebUi`är en utökningspunkt som du kan använda för att tillhandahålla ett eget användargränssnitt i offentliga klientprogram. Du kan också låta användaren gå igenom slutpunkten /Authorize för identitetsleverantören och låta dem logga in och medgivande. MSAL.NET kan sedan lösa in autentiseringskoden och få en token. Till exempel används det i Visual Studio för att ha elektronprogram (till exempel Visual Studio Feedback) ger webbinteraktion, men låt den MSAL.NET göra det mesta av arbetet. Du kan också använda den om du vill tillhandahålla automatisering av användargränssnittet. I offentliga klientprogram använder MSAL.NET PKCE-standarden (Proof Key for Code Exchange) för att säkerställa att säkerheten respekteras. Endast MSAL.NET kan lösa in koden. Mer information finns i [RFC 7636 - Proof Key for Code Exchange av OAuth Public Clients](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Använd WithCustomWebUi

Gör `.WithCustomWebUI`så här om du vill använda.

  1. Implementera gränssnittet `ICustomWebUi`. Mer information finns på [den här webbplatsen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Implementera `AcquireAuthorizationCodeAsync`en metod och acceptera url:en för auktoriseringskod som beräknas av MSAL.NET. Låt sedan användaren gå igenom interaktionen med identitetsprovidern och returnera url:en som identitetsleverantören skulle ha kallat tillbaka implementeringen tillsammans med auktoriseringskoden. Om du har problem bör `MsalExtensionException` implementeringen skapa ett undantag för att samarbeta med MSAL på ett bra sätt.
  2. I `AcquireTokenInteractive` samtalet använder `.WithCustomUI()` du modifieraren som skickar instansen av ditt anpassade webbgränssnitt.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Exempel på implementering av ICustomWebUi inom testautomation: SeleniumWebUI

Det MSAL.NET teamet har skrivit om gränssnittstesterna för att använda den här utökningsmekanismen. Om du är intresserad kan du titta på [selenwebUI-klassen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) i MSAL.NET källkoden.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Ge en fantastisk upplevelse med SystemWebViewOptions

Från MSAL.NET 4.1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)kan du ange:

- Den URI som`BrowserRedirectError`ska gå till (`HtmlMessageError`) eller HTML-fragmentet som ska visas ( ) vid inloggnings- eller medgivandefel i systemets webbläsare.
- Den URI som`BrowserRedirectSuccess`ska gå till (`HtmlMessageSuccess`) eller HTML-fragmentet som ska visas ( ) vid lyckad inloggning eller samtycke.
- Åtgärden som ska köras för att starta systemwebbläsaren. Du kan ange din egen `OpenBrowserAsync` implementering genom att ange ombudet. Klassen tillhandahåller också en standardimplementering `OpenWithEdgeBrowserAsync` `OpenWithChromeEdgeBrowserAsync` för två webbläsare: respektive för Microsoft Edge och [Microsoft Edge på krom.](https://www.windowscentral.com/faq-edge-chromium)

Om du vill använda den här strukturen skriver du något i stil med följande exempel:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Andra valfria parametrar

Mer information om alla andra `AcquireTokenInteractive`valfria parametrar för finns i [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python tillhandahåller inte en interaktiv hämtningstokenmetod direkt. I stället krävs att programmet skickar en auktoriseringsbegäran i implementeringen av användarinteraktionsflödet för att få en auktoriseringskod. Den här koden kan `acquire_token_by_authorization_code` sedan skickas till metoden för att hämta token.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])    

```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>I MSAL för iOS och macOS

Mål C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

Swift:

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```
---

## <a name="integrated-windows-authentication"></a>Integrerad Windows-autentisering

Om du vill logga in en domänanvändare på en domän eller en Azure AD-ansluten dator använder du Integrerad Windows-autentisering (IWA).

### <a name="constraints"></a>Villkor

- Integrerad Windows-autentisering kan endast användas för *federerade+* användare, det vill säga användare som skapats i Active Directory och backas upp av Azure AD. Användare som skapats direkt i Azure AD utan Active Directory-stöd, så kallade *hanterade* användare, kan inte använda det här autentiseringsflödet. Den här begränsningen påverkar inte användarnamnet och lösenordsflödet.
- IWA är för appar som är skrivna för plattformarna .NET Framework, .NET Core och UWP (Universal Windows Platform).
- IWA kringgår inte multifaktorautentisering (MFA). Om MFA är konfigurerat kan IWA misslyckas om en MFA-utmaning krävs, eftersom MFA kräver användarinteraktion.
  > [!NOTE]
  > Den här är knepig. IWA är icke-interaktiv, men MFA kräver användarinteraktivitet. Du kontrollerar inte när identitetsprovidern begär MFA som ska utföras, gör klientadministratören det. Från våra observationer krävs MFA när du loggar in från ett annat land, när du inte är ansluten via VPN till ett företagsnätverk, och ibland även när du är ansluten via VPN. Förvänta dig inte en deterministisk uppsättning regler. Azure AD använder AI för att kontinuerligt lära sig om MFA krävs. Återgå till en användarfråga som interaktiv autentisering eller enhetskodflöde om IWA misslyckas.

- Den myndighet `PublicClientApplicationBuilder` som har antagits måste vara:
  - Klienten för `https://login.microsoftonline.com/{tenant}/`formuläret `tenant` , där är antingen DET GUID som representerar klient-ID eller en domän som är associerad med klienten.
  - För alla arbets- `https://login.microsoftonline.com/organizations/`och skolkonton: .
  - Microsofts personliga konton stöds inte. Du kan inte använda klienter för /common- eller /consumers.

- Eftersom integrerad Windows-autentisering är ett tyst flöde:
  - Användaren av ditt program måste tidigare ha samtyckt till att använda programmet.
  - Eller klientadministratören måste tidigare ha samtyckt till alla användare i klienten för att kunna använda programmet.
  - Med andra ord:
    - Antingen valde du som utvecklare **grant-knappen** i Azure-portalen själv.
    - Eller en klientadministratör valde **knappen Bevilja/återkalla administratörsgodkännande för {klientdomän}** på fliken **API-behörigheter** för registreringen för programmet. Mer information finns i [Lägga till behörigheter för åtkomst till webb-API:er](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis).
    - Eller så har du gett användarna ett sätt att godkänna programmet. Mer information finns i [Begära individuellt användarmedgivande](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent).
    - Eller så har du angett ett sätt för klientadministratören att godkänna programmet. Mer information finns i [Administratörsmedgivande](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant).

- Det här flödet är aktiverat för .NET-skrivbord, .NET Core och UWP-appar.

Mer information om medgivande finns i [Microsofts identitetsplattformsbehörigheter och samtycke](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent).

### <a name="learn-how-to-use-it"></a>Läs om hur du använder den

# <a name="net"></a>[.NET](#tab/dotnet)

I MSAL.NET måste du använda:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Du behöver normalt bara`scopes`en parameter ( ). Beroende på hur Windows-administratören har konfigurerat principerna kanske program på din Windows-dator inte får söka efter den inloggade användaren. I så fall använder du `.WithUsername()`en andra metod och skickar in användarnamnet för den inloggade användaren som ett UPN-format, till exempel `joe@contoso.com`. På .NET Core är det bara överbelastningen som tar användarnamnet tillgänglig eftersom .NET Core-plattformen inte kan be användarnamnet till operativsystemet.

Följande exempel visar det senaste fallet, med förklaringar av vilken typ av undantag du kan få och deras mildrande åtgärder.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

En lista över möjliga modifierare på AcquireTokenByIntegratedWindowsAuthentication finns [i AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

Detta utdrag är från [MSAL Java dev prover](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Det här flödet stöds ännu inte i MSAL Python.

# <a name="macos"></a>[Macos](#tab/macOS)

Det här flödet gäller inte MacOS.

---

## <a name="username-and-password"></a>Användarnamn och lösenord

Du kan också skaffa en token genom att ange användarnamn och lösenord. Detta flöde är begränsat och rekommenderas inte, men det finns fortfarande användningsfall där det är nödvändigt.

### <a name="this-flow-isnt-recommended"></a>Det här flödet rekommenderas inte

Det här flödet *rekommenderas inte* eftersom det inte är säkert att låta programmet be en användare om deras lösenord. Mer information finns i [Vad är lösningen på det växande problemet med lösenord?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Det önskade flödet för att hämta en token tyst på Windows-domänanslutna datorer är [integrerad Windows-autentisering](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Du kan också använda [enhetskodflödet](https://aka.ms/msal-net-device-code-flow).

> [!NOTE]
> Det är användbart att använda ett användarnamn och lösenord i vissa fall, till exempel DevOps-scenarier. Men om du vill använda ett användarnamn och lösenord i interaktiva scenarier där du anger ditt eget användargränssnitt kan du fundera på hur du ska flytta bort från det. Genom att använda ett användarnamn och lösenord ger du upp ett antal saker:
>
> - Centrala grundsatser för modern identitet. Ett lösenord kan bli phished och spelas upp eftersom en delad hemlighet kan avlyssnas. Det är inte kompatibelt med lösenordslös.
> - Användare som behöver göra MFA kan inte logga in eftersom det inte finns någon interaktion.
> - Användare kan inte göra enkel inloggning (SSO).

### <a name="constraints"></a>Villkor

Följande begränsningar gäller också:

- Användarnamnet och lösenordsflödet är inte kompatibelt med villkorlig åtkomst och multifaktorautentisering. Om din app körs i en Azure AD-klient där klientadministratören kräver multifaktorautentisering kan du därför inte använda det här flödet. Många organisationer gör det.
- Det fungerar bara för arbete och skolkonton (inte MSA).
- Flödet är tillgängligt på .NET-skrivbordet och .NET Core, men inte på UWP.

### <a name="b2c-specifics"></a>B2C-detaljer

Mer information finns i [RoPC (Resource Owner Password Credentials) med B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Använd den

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`innehåller metoden `AcquireTokenByUsernamePassword`.

I följande exempel presenteras ett förenklat fall.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

Följande exempel visar det senaste fallet, med förklaringar av vilken typ av undantag du kan få och deras mildrande åtgärder.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Mer information om alla modifierare som `AcquireTokenByUsernamePassword`kan tillämpas på finns i [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

Följande utdrag är från [MSAL Java dev exempel](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Det här extraktet kommer från [MSAL Python-dev-exemplen](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

Det här flödet stöds inte på MSAL för macOS.

---

## <a name="command-line-tool-without-a-web-browser"></a>Kommandoradsverktyg utan webbläsare

### <a name="device-code-flow"></a>Flöde av enhetskod

Om du skriver ett kommandoradsverktyg som inte har webbkontroller och du inte kan eller inte vill använda de tidigare flödena måste du använda enhetskodflödet.

Interaktiv autentisering med Azure AD kräver en webbläsare. Mer information finns i [Användning av webbläsare](https://aka.ms/msal-net-uses-web-browser). Om du vill autentisera användare på enheter eller operativsystem som inte tillhandahåller en webbläsare kan enhetens kodflöde användaren använda en annan enhet, till exempel en dator eller en mobiltelefon för att logga in interaktivt. Genom att använda enhetskodflödet hämtar programmet token genom en tvåstegsprocess som är utformad för dessa enheter eller OSes. Exempel på sådana program är program som körs på iOT eller kommandoradsverktyg (CLI). Tanken är att:

1. När användarautentisering krävs tillhandahåller appen en kod för användaren. Användaren uppmanas att använda en annan enhet, till exempel en internetansluten `https://microsoft.com/devicelogin`smartphone, för att gå till en webbadress, till exempel . Sedan uppmanas användaren att ange koden. Detta görs leder webbsidan användaren genom en normal autentiseringsupplevelse, vilket inkluderar samtyckesuppmaningar och multifaktorautentisering, om det behövs.

2. När autentiseringen har slutförts tar kommandoradsappen emot de token som krävs via en bakåtkanal och använder dem för att utföra de webb-API-anrop som behövs.

### <a name="use-it"></a>Använd den

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`innehåller en metod `AcquireTokenWithDeviceCode`med namnet .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Den här metoden tar som parametrar:

- Att `scopes` begära en åtkomsttoken för.
- En motringning `DeviceCodeResult`som tar emot .

  ![Egenskaper för DeviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Följande exempelkod visar det senaste fallet, med förklaringar av vilken typ av undantag du kan få och deras begränsning.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
# <a name="java"></a>[Java](#tab/java)

Detta utdrag är från [MSAL Java dev prover](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Det här extraktet kommer från [MSAL Python-dev-exemplen](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[Macos](#tab/macOS)

Det här flödet gäller inte MacOS.

---

## <a name="file-based-token-cache"></a>Filbaserad tokencache

I MSAL.NET tillhandahålls en tokencache i minnet som standard.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Serialisering kan anpassas i Windows-skrivbordsappar och webbappar eller webb-API:er

När det gäller .NET Framework och .NET Core, om du inte gör något extra, varar minnestoken cachen under hela programmet. Om du vill förstå varför serialisering inte tillhandahålls direkt kan du komma ihåg att MSAL .NET-skrivbords- eller .NET Core-program kan vara konsol- eller Windows-program (som skulle ha åtkomst till filsystemet) *utan även* webbprogram eller webb-API:er. Dessa webbappar och webb-API:er kan använda vissa specifika cachemekanismer som databaser, distribuerade cacheminnen och Redis-cacheminnen. Om du vill ha ett beständigt tokencacheprogram i .NET-skrivbordet eller .NET Core måste du anpassa serialiseringen.

Klasser och gränssnitt som ingår i serialisering av tokencache är följande typer:

- ``ITokenCache``, som definierar händelser för att prenumerera på serialiseringsbegäranden för tokencache och metoder för att serialisera eller avserialisera cachen i olika format (ADAL v3.0, MSAL 2.x och MSAL 3.x = ADAL v5.0).
- ``TokenCacheCallback``är en motringning som skickas till händelserna så att du kan hantera serialiseringen. De kommer att anropas ``TokenCacheNotificationArgs``med argument av typen .
- ``TokenCacheNotificationArgs``tillhandahåller endast ``ClientId`` programmet och en referens till den användare som token är tillgänglig för.

  ![Serialiseringsdiagram för tokencache](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET skapar tokencachar åt dig och ger `IToken` dig cacheminnet när `UserTokenCache` du `AppTokenCache` anropar ett programs och egenskaper. Du ska inte implementera gränssnittet själv. Ditt ansvar, när du implementerar en anpassad tokencache serialisering, är att:
>
> - Reagera `BeforeAccess` på `AfterAccess` och händelser, eller deras *Async* motsvarighet. Ombudet`BeforeAccess` ansvarar för att avaktivera cachen. Ombudet `AfterAccess` ansvarar för serialisering av cacheminnet.
> - Förstå att en del av dessa händelser lagrar eller läser in blobbar, som skickas genom händelseargumentet till vilken lagring du vill.

Strategierna är olika beroende på om du skriver en tokencache serialisering för ett offentligt klientprogram, till exempel ett skrivbord, eller ett konfidentiellt klientprogram, till exempel en webbapp eller webb-API eller en daemonapp.

Sedan MSAL v2.x har du flera alternativ. Ditt val beror på om du bara vill serialisera cachen till MSAL.NET-format, vilket är en enhetlig formatcache som är vanlig med MSAL men även på plattformarna. Eller så kanske du också vill stödja den [äldre](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) tokencachen serialisering av ADAL v3.

Anpassningen av tokencache serialisering för att dela SSO-tillståndet mellan ADAL.NET 3.x, ADAL.NET 5.x och MSAL.NET förklaras i en del av exemplet [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

### <a name="simple-token-cache-serialization-msal-only"></a>Enkel tokencache serialisering (endast MSAL)

Följande exempel är en naiv implementering av anpassad serialisering av en tokencache för skrivbordsprogram. Här finns användartokens cache i en fil i samma mapp som programmet.

När du har byggt programmet aktiverar du ``TokenCacheHelper.EnableSerialization()`` serialiseringen `UserTokenCache`genom att anropa och skicka programmet .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Den här hjälpklassen ser ut som följande kodavsnitt:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

En förhandsgranskning av en filcachefilbaserad serialiserare för offentliga klientprogram för skrivbordsprogram som körs på Windows, Mac och Linux finns tillgängligt från [microsoft.Identity.Client.Extensions.Msal-biblioteket](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) med öppen källkod. Du kan inkludera det i dina program från följande NuGet-paket: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Ansvarsfriskrivning: Microsoft.Identity.Client.Extensions.Msal-biblioteket är ett tillägg över MSAL.NET. Klasser i dessa bibliotek kan ta sig in i MSAL.NET i framtiden, i det vill säga eller med att bryta förändringar.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serialisering av dual tokencache (MSAL-enhetlig cache + ADAL v3)

Du kanske vill implementera serialisering av tokencache med formatet Enhetlig cache. Det här formatet är gemensamt för ADAL.NET 4.x och MSAL.NET 2.x och med andra MSALs av samma generation eller äldre, på samma plattform. Inspireras av följande kod:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Den här gången ser hjälpklassen ut som följande kod:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anropa ett webb-API från skrivbordsappen](scenario-desktop-call-api.md)
