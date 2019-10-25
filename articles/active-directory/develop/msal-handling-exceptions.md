---
title: Fel och undantag (MSAL)
titleSuffix: Microsoft identity platform
description: Lär dig hur du hanterar fel och undantag, villkorlig åtkomst och anspråk på anspråk i MSAL-program.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: cc5139cb4d463b88f21b0c7a2c7e0383abdb9e1f
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803116"
---
# <a name="handle-msal-exceptions-and-errors"></a>Hantera undantag och fel i MSAL

Den här artikeln ger en översikt över de olika typerna av fel och rekommendationer för att hantera vanliga inloggnings fel.

## <a name="msal-error-handling-basics"></a>Grundläggande fel hantering för MSAL

Undantag i Microsoft Authentication Library (MSAL) är avsedda för att utvecklare av appar ska kunna felsöka och inte Visa för slutanvändare. Undantags meddelanden är inte lokaliserade.

När du bearbetar undantag och fel kan du använda själva undantags typen och felkoden för att skilja mellan undantag.  En lista över felkoder finns i [fel koder för autentisering och auktorisering](reference-aadsts-error-codes.md).

Vid tyst eller interaktiv hämtning av token kan appar komma över fel under inloggningen, till exempel fel om medgivanden, villkorlig åtkomst (MFA, enhets hantering, platsbaserade begränsningar), utfärdande och inlösen av token och användare egenskaperna.

## <a name="msal-for-ios-and-macos-errors"></a>MSAL för iOS-och macOS-fel

Den fullständiga listan med fel visas i [MSALError-uppräkningen](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Alla MSAL-producerade fel returneras med `MSALErrorDomain` domän. 

För systemfel returnerar MSAL den ursprungliga `NSError` från system-API: et. Om till exempel hämtning av token Miss lyckas på grund av brist på nätverks anslutning, returnerar MSAL ett fel med `NSURLErrorDomain`-domänen och `NSURLErrorNotConnectedToInternet`s kod.

Vi rekommenderar att du hanterar minst följande två MSAL-fel på klient sidan:

- `MSALErrorInteractionRequired`: användaren måste göra en interaktiv begäran. Det finns många villkor som kan leda till det här felet, till exempel en utgången autentiseringsbegäran eller behovet av ytterligare autentiseringskrav. Anropa det MSAL interaktiva token för hämtnings-API för att återställa. 

- `MSALErrorServerDeclinedScopes`: vissa eller alla omfattningar nekades. Bestäm om du vill fortsätta med endast beviljade omfattningar eller stoppa inloggnings processen.

> [!NOTE]
> `MSALInternalError` Enum ska endast användas för referens och fel sökning. Försök inte att automatiskt hantera dessa fel vid körning. Om din app påträffar något av de fel som faller under `MSALInternalError`kanske du vill visa ett allmänt användar meddelande som förklarar vad som hände.

`MSALInternalErrorBrokerResponseNotReceived` innebär till exempel att användaren inte slutförde autentiseringen och returnerade manuellt till appen. I det här fallet bör appen Visa ett allmänt fel meddelande som förklarar att autentiseringen inte slutförts och att de försöker autentisera igen.

Följande mål-C-exempel kod visar metod tips för hantering av några vanliga fel villkor:

Objective-C
```ObjC
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

Swift
```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

## <a name="net-exceptions"></a>.NET-undantag

När du bearbetar undantag kan du använda själva undantags typen och `ErrorCode` medlem för att skilja mellan undantag. `ErrorCode` värden är konstanter av typen [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Du kan också ha en titt på fälten [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)och [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Om [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) genereras kan du testa [autentiserings-och auktoriseringsfel](reference-aadsts-error-codes.md) för att se om koden visas där.

### <a name="common-exceptions"></a>Vanliga undantag

Här följer de vanliga undantag som kan uppstå och vissa möjliga åtgärder:  

| Undantag | Felkod | Åtgärd|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: användaren eller administratören har inte samtyckt till att använda programmet med ID {appId} med namnet {appName}. Skicka en interaktiv auktoriseringsbegäran för den här användaren och resursen.| Du måste först få användar medgivande. Om du inte använder .NET Core (som inte har något webb gränssnitt) kan du anropa (en gång) `AcquireTokeninteractive`. Om du använder .NET Core eller inte vill göra en `AcquireTokenInteractive`kan användaren navigera till en URL för att ge medgivande: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read. så här anropar du `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: användaren måste använda Multi-Factor Authentication (MFA).| Det finns ingen minskning. Om MFA har kon figurer ATS för din klient organisation och Azure Active Directory (AAD) bestämmer sig för att genomdriva det måste du återgå till ett interaktivt flöde, till exempel `AcquireTokenInteractive` eller `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: anslags typen stöds inte över */vanliga* -eller */consumers* -slutpunkterna. Använd den */organizations* eller klient-/regionsspecifika slut punkten. Du använde */vanliga*.| Som förklaras i meddelandet från Azure AD måste utfärdaren ha en klient eller på annat */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: begär ande texten måste innehålla följande parameter: `client_secret or client_assertion`.| Detta undantag kan genereras om programmet inte har registrerats som ett offentligt klient program i Azure AD. Redigera manifestet för ditt program i Azure Portal och ange `allowPublicClient` till `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: det gick inte att identifiera inloggad användare| Biblioteket kunde inte fråga den aktuella Windows-inloggade användaren eller så är den här användaren inte AD eller AAD-ansluten (arbets plats anslutna användare stöds inte). Minskning 1: på UWP kontrollerar du att programmet har följande funktioner: Enterprise-autentisering, privata nätverk (klient och Server), information om användar konton. Minskning 2: implementera din egen logik för att hämta användar namnet (till exempel john@contoso.com) och Använd formuläret `AcquireTokenByIntegratedWindowsAuth` som tar med användar namnet.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Den här metoden förlitar sig på ett protokoll som exponeras av Active Directory (AD). Om en användare skapades i Azure Active Directory utan AD-återställning ("hanterad" användare) kommer den här metoden att Miss förväntas. Användare som skapats i AD och som backas upp av AAD ("federerade" användare) kan dra nytta av den här icke-interaktiva autentiseringsmetoden. Minskning: Använd interaktiv autentisering.|

### `MsalUiRequiredException`

En av de vanliga status koderna som returnerades från MSAL.NET när `AcquireTokenSilent()` anropas `MsalError.InvalidGrantError`. Den här status koden innebär att programmet ska anropa autentiseringspaket igen, men i interaktivt läge (AcquireTokenInteractive eller AcquireTokenByDeviceCodeFlow för offentliga klient program och gör en utmaning i Web Apps). Detta beror på att ytterligare användar åtgärder krävs innan autentiseringstoken kan utfärdas.

I de flesta fall då `AcquireTokenSilent` Miss lyckas beror det på att token cache inte har tokens som matchar din begäran. Åtkomsttoken upphör att gälla om 1 timme och `AcquireTokenSilent` försöker hämta en ny baserat på en uppdateringstoken (i OAuth2-termer är detta "Refresh token"-flödet). Det här flödet kan också inte utföras av olika orsaker, till exempel om en klient organisations administratör konfigurerar mer strikta inloggnings principer. 

Interaktionen syftar på att användaren ska göra en åtgärd. Några av dessa villkor är enkla för användarna att lösa (till exempel acceptera användnings villkor med ett enda klick) och vissa kan inte lösas med den aktuella konfigurationen (till exempel att datorn i fråga måste ansluta till ett visst företags nätverk). Vissa hjälper användaren att konfigurera Multi-Factor Authentication eller installera Microsoft Authenticator på deras enheter.

### <a name="msaluirequiredexception-classification-enumeration"></a>uppräkning av `MsalUiRequiredException` klassificering

MSAL exponerar ett `Classification`-fält som du kan läsa för att ge en bättre användar upplevelse, till exempel för att tala om för användaren att deras lösen ord har upphört att gälla eller att de måste ge medgivande till att använda vissa resurser. De värden som stöds är en del av `UiRequiredExceptionClassification` Enum:

| Klassificering    | Betydelse           | Rekommenderad hantering |
|-------------------|-------------------|----------------------|
| BasicAction | Villkoret kan lösas genom användar interaktion under det interaktiva autentiserings flödet. | Anropa AcquireTokenInteractively (). |
| AdditionalAction | Villkoret kan lösas med hjälp av ytterligare återställnings interaktion med systemet, utanför det interaktiva autentiserings flödet. | Anropa AcquireTokenInteractively () för att visa ett meddelande som förklarar åtgärds åtgärden. Anrops programmet kan välja att dölja flöden som kräver additional_action om användaren inte är klar med åtgärden för att åtgärda problemet. |
| MessageOnly      | Villkoret kan inte lösas just nu. Om du startar ett interaktivt autentiseringsschema visas ett meddelande som förklarar villkoret. | Anropa AcquireTokenInteractively () för att visa ett meddelande som förklarar villkoret. AcquireTokenInteractively () returnerar UserCanceled-fel när användaren har läst meddelandet och stängt fönstret. Anrops programmet kan välja att dölja flöden som resulterar i message_only om användaren inte har nytta av meddelandet.|
| ConsentRequired  | Användar medgivande saknas eller har återkallats. | Anropa AcquireTokenInteractively () för att användaren ska kunna ge sitt medgivande. |
| UserPasswordExpired | Användarens lösen ord har upphört att gälla. | Anropa AcquireTokenInteractively () så att användaren kan återställa sina lösen ord. |
| PromptNeverFailed| Interaktiv autentisering anropades med parameter tolken = aldrig, tvinga MSAL att förlita sig på webbläsarens cookies och inte att visa webbläsaren. Detta har misslyckats. | Anropa AcquireTokenInteractively () utan prompt. None |
| AcquireTokenSilentFailed | MSAL SDK har inte tillräckligt med information för att hämta en token från cachen. Detta kan bero på att det inte finns några token i cachen eller om det inte gick att hitta något konto. Fel meddelandet innehåller mer information.  | Anropa AcquireTokenInteractively (). |
| Inget    | Det finns ingen ytterligare information. Villkoret kan lösas genom användar interaktion under det interaktiva autentiserings flödet. | Anropa AcquireTokenInteractively (). |

## <a name="code-example"></a>Kodexempel

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
   res = await application.AcquireTokenInteractive(scopes)
                          .ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```


## <a name="javascript-errors"></a>JavaScript-fel

MSAL. js innehåller fel objekt som kan vara abstrakta och klassificera de olika typerna av vanliga fel. Den innehåller också ett gränssnitt för att få åtkomst till information om felen, till exempel fel meddelanden, för att hantera dem på rätt sätt.

### <a name="error-object"></a>Fel objekt

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

Genom att utöka fel klassen har du åtkomst till följande egenskaper:
* **AuthError. meddelande:**  Samma som errorMessage.
* **AuthError. stack:** Stack spårning för genererade fel. Tillåter spårning till ursprungs punkten för fel.

### <a name="error-types"></a>Fel typer

Följande fel typer är tillgängliga:

- `AuthError`: Bask lass klassen för biblioteket MSAL. js, som också används för oväntade fel.

- `ClientAuthError`: fel klass, som anger ett problem med klientautentisering. De flesta fel som kommer från biblioteket kommer att bli ClientAuthErrors. Dessa fel beror på saker som att anropa en inloggnings metod när inloggningen redan pågår, att användaren avbryter inloggningen och så vidare.

- `ClientConfigurationError`: fel klass, utökar `ClientAuthError` utlöst innan begär Anden görs när de tilldelade parametrarna för användar konfiguration är felaktiga eller saknas.

- `ServerError`: klassen Error representerar de fel strängar som skickats av autentiseringsservern. Detta kan vara fel som ogiltiga format eller parametrar för begäran, eller andra fel som hindrar servern från att autentisera eller auktorisera användaren.

- `InteractionRequiredAuthError`: fel klass, utökar `ServerError` för att representera Server fel, vilket kräver ett interaktivt anrop. Det här felet uppstår i `acquireTokenSilent` om användaren måste interagera med servern för att ange autentiseringsuppgifter eller tillstånd för autentisering/auktorisering. Felkoder är `"interaction_required"`, `"login_required"`och `"consent_required"`.

För fel hantering i autentiserings flöden med omdirigerings metoder (`loginRedirect`, `acquireTokenRedirect`) måste du registrera återanropet, som anropas med lyckat eller misslyckat efter omdirigeringen med `handleRedirectCallback()` metod enligt följande:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Metoderna för pop-up-upplevelse (`loginPopup`, `acquireTokenPopup`) returnerar löfte, så du kan använda löftes mönstret (. och. Catch) för att hantera dem som de visas:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Interaktion krävs, fel

Ett fel returneras när du försöker använda en icke-interaktiv metod för att förvärva en token som `acquireTokenSilent`, men MSAL inte kunde göra det tyst.

Möjliga orsaker är:

- Du måste logga in
- Du måste godkänna
- Du måste gå igenom en Multi-Factor Authentication-upplevelse.

Reparationen är att anropa en interaktiv metod som `acquireTokenPopup` eller `acquireTokenRedirect`:

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="conditional-access-and-claims-challenges"></a>Utmaningarna för villkorlig åtkomst och anspråk

När token hämtas i bakgrunden kan ditt program få fel när en [utmanings utmaning för anspråk](conditional-access-dev-guide.md) , till exempel MFA-princip, krävs av ett API som du försöker få åtkomst till.

Mönstret för att hantera det här felet är att interaktivt hämta en token med hjälp av MSAL. Att interaktivt förvärva en token frågar användaren och ger dem möjlighet att uppfylla den nödvändiga principen för villkorlig åtkomst.

I vissa fall när du anropar ett API som kräver villkorlig åtkomst kan du få en anspråks utmaning i fel meddelandet från API: et. Om den villkorliga åtkomst principen till exempel är att ha en hanterad enhet (Intune), kommer felet att vara något som liknar [AADSTS53000: din enhet måste hanteras för att få åtkomst till den här resursen](reference-aadsts-error-codes.md) eller något liknande. I det här fallet kan du skicka anspråken i det Hämta token-anropet så att användaren uppmanas att uppfylla den aktuella principen.

### <a name="net"></a>.NET

När du anropar ett API som kräver villkorlig åtkomst från MSAL.NET måste ditt program hantera anspråks utmanings undantag. Detta visas som en [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) där [anspråks](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) egenskapen inte är tom.

För att hantera anspråks utmaningen måste du använda metoden `.WithClaim()` i `PublicClientApplicationBuilder`-klassen.

### <a name="javascript"></a>JavaScript

När du hämtar token tyst (med `acquireTokenSilent`) med hjälp av MSAL. js kan programmet få fel meddelanden när en [utmanings utmaning för anspråk](conditional-access-dev-guide.md) som MFA-princip krävs av ett API som du försöker få åtkomst till.

Mönstret för att hantera det här felet är att göra ett interaktivt anrop för att hämta token i MSAL. js som `acquireTokenPopup` eller `acquireTokenRedirect` som i följande exempel:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

Att interaktivt förvärva token frågar användaren och ger dem möjlighet att uppfylla den nödvändiga principen för villkorlig åtkomst.

När du anropar ett API som kräver villkorlig åtkomst kan du få en anspråks utmaning i fel meddelandet från API: et. I det här fallet kan du skicka de anspråk som returneras i fel till fältet `claimsRequest` i klassen `AuthenticationParameters.ts` för att uppfylla den aktuella principen. 

Mer information finns i [begära ytterligare anspråk](active-directory-optional-claims.md) .

### <a name="msal-for-ios-and-macos"></a>MSAL för iOS och macOS

MSAL för iOS och macOS gör att du kan begära vissa anspråk i både interaktiva och tysta token för hämtning.

Ange `claimsRequest` i `MSALSilentTokenParameters` eller `MSALInteractiveTokenParameters`för att begära anpassade anspråk.

Mer information finns i [begäran om anpassade anspråk med MSAL för iOS och MacOS](request-custom-claims.md) .

## <a name="retrying-after-errors-and-exceptions"></a>Försöker igen efter fel och undantag

Du förväntas införa egna återförsöks principer när du anropar MSAL. MSAL gör HTTP-anrop till AAD-tjänsten och tillfälliga fel kan uppstå, till exempel om nätverket kan stängas av eller servern är överbelastad.  

### <a name="http-error-codes-500-600"></a>HTTP-felkoder 500-600

MSAL.NET implementerar en enkel återförsöks funktion för fel med HTTP-felkoderna 500-600.

### <a name="http-429"></a>HTTP 429

När service token Server (STS) är överbelastad med för många begär Anden returneras HTTP-fel 429 med ett tips om hur länge du kan försöka igen i fältet `Retry-After` svar.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) -ytor `System.Net.Http.Headers.HttpResponseHeaders` som en egenskap `namedHeaders`. Du kan använda ytterligare information från felkoden för att förbättra tillförlitligheten för dina program. I det fall som beskrivs kan du använda `RetryAfterproperty` (av typen `RetryConditionHeaderValue`) och beräkna när du vill försöka igen.

Här är ett exempel på ett daemon-program som använder flödet för klientautentiseringsuppgifter. Du kan anpassa detta till någon av metoderna för att förvärva en token.

```csharp
do
{
 retry = false;
 TimeSpan? delay;
 try
 {
  result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                        .ExecuteAsync();
 }
 catch (MsalServiceException serviceException)
 {
  if (ex.ErrorCode == "temporarily_unavailable")
  {
   RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
   if (retryAfter.Delta.HasValue)
   {
    delay = retryAfter.Delta;
   }
   else if (retryAfter.Date.HasValue)
   {
    delay = retryAfter.Date.Value.Offset;
   }
  }
 }

    …
 if (delay.HasValue)
 {
  Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
  retry = true;
 }
} while (retry);
```
