---
title: Skaffa en token för att anropa ett webb-API (mobilappar) | Azure
titleSuffix: Microsoft identity platform
description: Läs om hur du skapar en mobilapp som anropar webb-API:er. (Hämta en token för appen.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 5750f4a5aa62b33c7d793b3e0c34f304ce1b187e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535935"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Skaffa en token för en mobilapp som anropar webb-API:er

Innan appen kan anropa skyddade webb-API:er behöver den en åtkomsttoken. I den här artikeln får du hjälp med en token med hjälp av Microsoft Authentication Library (MSAL).

## <a name="define-a-scope"></a>Definiera ett scope

När du begär en token måste du definiera ett scope. Omfattningen avgör vilka data din app kan komma åt.

Det enklaste sättet att definiera ett scope är `App ID URI` att `.default`kombinera de önskade webb-API:erna med omfånget . Den här definitionen talar om för Microsofts identitetsplattform att appen kräver alla scope som anges i portalen.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Hämta token

### <a name="acquire-tokens-via-msal"></a>Skaffa token via MSAL

MSAL tillåter appar att hämta tokens tyst och interaktivt. När du `AcquireTokenSilent()` `AcquireTokenInteractive()`ringer eller returnerar MSAL en åtkomsttoken för de begärda scope. Det korrekta mönstret är att göra en tyst begäran och sedan falla tillbaka till en interaktiv begäran.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

#### <a name="ios"></a>iOS

Försök först att skaffa en token tyst:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";

MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        // Access token to call the web API
        NSString *accessToken = result.accessToken;
    }

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"

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

    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

Om MSAL `MSALErrorInteractionRequired`returnerar kan du prova att hämta token interaktivt:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
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

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

MSAL för iOS och macOS stöder olika modifierare för att få en token interaktivt eller tyst:
* [Vanliga parametrar för att hämta en token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parametrar för att få en interaktiv token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parametrar för att få en tyst token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

I följande exempel visas den minimala koden för att få en token interaktivt. I exemplet används Microsoft Graph för att läsa användarens profil.

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

#### <a name="mandatory-parameters-in-msalnet"></a>Obligatoriska parametrar i MSAL.NET

`AcquireTokenInteractive`har bara en obligatorisk `scopes`parameter: . Parametern `scopes` räknar upp strängar som definierar de scope som en token krävs för. Om token är för Microsoft Graph kan du hitta de scope som krävs i API-referensen för varje Microsoft Graph API. Gå till avsnittet Behörigheter i referensen.

Om du till exempel vill [visa användarens kontakter](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)använder du scopet "User.Read", "Contacts.Read". Mer information finns i [Microsoft Graph-behörighetsreferens](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

På Android kan du ange överordnad `PublicClientApplicationBuilder`aktivitet när du skapar appen med hjälp av . Om du inte anger den överordnade aktiviteten vid den tidpunkten `.WithParentActivityOrWindow` kan du senare ange den med hjälp av som i följande avsnitt. Om du anger överordnad aktivitet återgår token till den överordnade aktiviteten efter interaktionen. Om du inte anger det, `.ExecuteAsync()` genererar samtalet ett undantag.

#### <a name="specific-optional-parameters-in-msalnet"></a>Specifika valfria parametrar i MSAL.NET

I följande avsnitt beskrivs de valfria parametrarna i MSAL.NET.

##### <a name="withprompt"></a>MedPrompt

Parametern `WithPrompt()` styr interaktivitet med användaren genom att ange en fråga.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Klassen definierar följande konstanter:

- `SelectAccount`tvingar säkerhetstokentjänsten (STS) att presentera dialogrutan för kontoval. Dialogrutan innehåller de konton som användaren har en session för. Du kan använda det här alternativet när du vill låta användaren välja mellan olika identiteter. Det här alternativet ger `prompt=select_account` MSAL att skicka till identitetsprovidern.

    Konstanten `SelectAccount` är standard, och det ger effektivt bästa möjliga upplevelse baserat på tillgänglig information. Den tillgängliga informationen kan innehålla konto, närvaro av en session för användaren och så vidare. Ändra inte denna standard om du inte har en god anledning att göra det.
- `Consent`gör att du kan fråga användaren om samtycke även om samtycke beviljades tidigare. I det här fallet `prompt=consent` skickar MSAL till identitetsprovidern.

    Du kanske vill `Consent` använda konstanten i säkerhetsfokuserade program där organisationsstyrningen kräver att användarna ser dialogrutan medgivande varje gång de använder programmet.
- `ForceLogin`gör det möjligt för tjänsten att fråga användaren efter autentiseringsuppgifter även om uppmaningen inte behövs.

    Det här alternativet kan vara användbart om tokeninsamlingen misslyckas och du vill låta användaren logga in igen. I det här fallet `prompt=login` skickar MSAL till identitetsprovidern. Du kanske vill använda det här alternativet i säkerhetsfokuserade program där organisationsstyrningen kräver att användaren loggar in varje gång de kommer åt specifika delar av programmet.
- `Never`är för endast .NET 4.5 och Windows Runtime (WinRT). Den här konstanten frågar inte användaren, men den försöker använda cookien som lagras i den dolda inbäddade webbvyn. Mer information finns i [Använda webbläsare med MSAL.NET](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers).

    Om det här `AcquireTokenInteractive` alternativet misslyckas genererar du ett undantag för att meddela dig om att en gränssnittsinteraktion behövs. Då måste du `Prompt` använda en annan parameter.
- `NoPrompt`skickar ingen uppmaning till identitetsleverantören.

    Det här alternativet är endast användbart för redigeringsprofilprinciper i Azure Active Directory B2C. Mer information finns i [B2C-detaljer](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Använd `WithExtraScopeToConsent` modifieraren i ett avancerat scenario där du vill att användaren ska ge förhandsgodkännande till flera resurser. Du kan använda den här modifieraren när du inte vill använda inkrementellt medgivande, som normalt används med MSAL.NET eller Microsoft identity platform 2.0. Mer information finns i [Ha användaren samtycke i förväg för flera resurser](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

Här är ett kodexempel:

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Andra valfria parametrar

Mer information om de `AcquireTokenInteractive`andra valfria parametrarna för finns i [referensdokumentationen för AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Hämta token via protokollet

Vi rekommenderar inte direkt att använda protokollet för att hämta token. Om du gör det stöder appen inte vissa scenarier som innebär enkel inloggning (SSO), enhetshantering och villkorlig åtkomst.

När du använder protokollet för att hämta token för mobilappar gör du två begäranden:

* Hämta en auktoriseringskod.
* Byt ut koden mot en token.

#### <a name="get-an-authorization-code"></a>Hämta en auktoriseringskod

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Få åtkomst och uppdatera token

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anropa ett webb-API](scenario-mobile-call-api.md)
