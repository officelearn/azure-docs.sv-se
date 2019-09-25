---
title: 'Mobilapp som anropar webb-API: er – hämtar en token för appen | Microsoft Identity Platform'
description: 'Lär dig hur du skapar en mobilapp som anropar webb-API: er (Hämta en token för appen)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 454d62f871290d2e7dd8d0eee4b1a2429625a5fc
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268256"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Mobilapp som anropar webb-API: er – hämta en token

Innan du kan börja anropa skyddade webb-API: er behöver din app en åtkomsttoken. Den här artikeln vägleder dig genom processen för att hämta en token med hjälp av Microsoft Authentication Library (MSAL).

## <a name="scopes-to-request"></a>Omfattningar som ska begäras

När du begär en token måste du definiera ett omfång. Omfånget avgör vilka data som din app kan komma åt.  

Det enklaste sättet är att kombinera det önskade webb-API `App ID URI` : et med `.default`omfånget. Om du gör det meddelar Microsoft Identity Platform att appen kräver alla omfattningar som anges i portalen.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Hämta token

### <a name="acquire-tokens-via-msal"></a>Hämta token via MSAL

MSAL gör att appar kan hämta token tyst och interaktivt. Anropa bara de här metoderna och MSAL returnerar en åtkomsttoken för de begärda omfattningarna. Rätt mönster är att utföra en tyst begäran och återgå till en interaktiv begäran.

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
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

**Försök att hämta en token i bakgrunden:**

Mål-C:

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
            
        // Access token to call the Web API
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
 
Införliva

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
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

**Försök sedan att förvärva `MSALErrorInteractionRequired`token interaktivt om MSAL returnerar:**

Mål-C:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
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

Införliva

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
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

MSAL för iOS och macOS stöder olika modifierare när du hämtar token interaktivt eller tyst.
* [Vanliga parametrar för att hämta en token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parametrar för interaktiv token-hämtning](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parametrar för hämtning av tyst token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

I följande exempel visas minimal kod för att få en token interaktivt för att läsa användarens profil med Microsoft Graph.

```CSharp
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

`AcquireTokenInteractive`har endast en obligatorisk parameter ``scopes``som innehåller en uppräkning av strängar som definierar de omfång som en token krävs för. Om token är för Microsoft Graph, finns de obligatoriska omfattningarna i API-referensen för varje Microsoft Graph API i avsnittet med namnet "Permissions". Om du till exempel vill [lista användarens kontakter](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)måste omfattningen "User. Read", "Contacts. Read" användas. Se även [Microsoft Graph behörighets referens](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Om du inte har angett det när du skapar appen på Android måste du också ange den överordnade aktiviteten (med hjälp av `.WithParentActivityOrWindow`, se nedan) så att token återgår till den överordnade aktiviteten efter interaktionen. Om du inte anger det genereras ett undantag vid anrop `.ExecuteAsync()`.

#### <a name="specific-optional-parameters-in-msalnet"></a>Vissa valfria parametrar i MSAL.NET

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()`används för att styra interaktivitet med användaren genom att ange en prompt

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Klassen definierar följande konstanter:

- ``SelectAccount``: tvingar STS att Visa dialog rutan för val av konto som innehåller konton som användaren har en session för. Det här alternativet är användbart när program utvecklare vill låta användaren välja mellan olika identiteter. Det här alternativet MSAL för att ``prompt=select_account`` skicka till identitets leverantören. Det här alternativet är standard och det gör det bra att tillhandahålla den bästa möjliga upplevelsen baserat på tillgänglig information (konto, närvaro av en session för användaren och så vidare. ...). Ändra det inte om du inte har en lämplig anledning att göra det.
- ``Consent``: gör att programutvecklaren kan tvinga användaren att tillfrågas om godkännande även om medgivande beviljades tidigare. I det här fallet skickar `prompt=consent` MSAL till identitets leverantören. Det här alternativet kan användas i vissa säkerhets fokuserade program där organisationens styrning kräver att användaren visas medgivande dialog rutan varje gången programmet används.
- ``ForceLogin``: gör det möjligt för programutvecklaren att be användaren att ange autentiseringsuppgifter för tjänsten även om denna användar fråga inte behövs. Det här alternativet kan vara användbart om hämtning av en token Miss lyckas, så att användaren kan logga in igen. I det här fallet skickar `prompt=login` MSAL till identitets leverantören. Nu har vi sett att det används i vissa säkerhetsfokuserade program där organisationens styrning kräver att användaren loggar in varje gång de kommer åt specifika delar av ett program.
- ``Never``(endast för .NET 4,5 och WinRT) kommer inte att fråga användaren, utan försöker istället använda den cookie som lagras i den dolda inbäddade vyn (se nedan: Webbvyer i MSAL.NET). Det går inte att använda det här alternativet, och `AcquireTokenInteractive` i så fall utlöses ett undantag för att meddela att ett UI-samspel krävs, och du behöver `Prompt` använda en annan parameter.
- ``NoPrompt``: Det går inte att skicka någon prompt till identitets leverantören. Det här alternativet är bara användbart för Azure AD B2C Redigera profil principer (se [B2C-information](https://aka.ms/msal-net-b2c-specificities)).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Den här modifieraren används i ett avancerat scenario där du vill att användaren ska godkänna flera resurser på förhand (och inte vill använda det stegvisa godkännandet som normalt används med MSAL.NET/Microsoft Identity Platform v 2.0). Mer information finns i [så här gör du för att få användar medgivande för flera resurser](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Andra valfria parametrar

Läs mer om alla andra valfria parametrar för `AcquireTokenInteractive` från referens dokumentationen för [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

### <a name="acquire-tokens-via-the-protocol"></a>Hämta token via protokollet

Vi rekommenderar inte att du använder protokollet direkt. Om du gör det stöder appen inte vissa scenarier för enkel inloggning (SSO), enhets hantering och villkorlig åtkomst.

När du använder protokollet för att hämta token för mobila appar måste du göra två begär Anden: Hämta en auktoriseringskod och Utbyt den för en token.

#### <a name="get-authorization-code"></a>Hämta auktoriseringskod

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>Hämta åtkomst och uppdatera token

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
> [Anropar ett webb-API](scenario-mobile-call-api.md)
