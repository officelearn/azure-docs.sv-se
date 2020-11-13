---
title: Fel och undantag (MSAL)
titleSuffix: Microsoft identity platform
description: Lär dig hur du hanterar fel och undantag, villkorlig åtkomst och anspråk på anspråk i MSAL-program.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02a08cc0400b4d65577c13282ca4c23cac1d21dc
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578934"
---
# <a name="handle-msal-exceptions-and-errors"></a>Hantera undantag och fel i MSAL

Den här artikeln ger en översikt över de olika typerna av fel och rekommendationer för att hantera vanliga inloggnings fel.

## <a name="msal-error-handling-basics"></a>Grundläggande fel hantering för MSAL

Undantag i Microsoft Authentication Library (MSAL) är avsedda för att utvecklare av appar ska kunna felsöka – inte för att visa för slutanvändare. Undantags meddelanden är inte lokaliserade.

När du bearbetar undantag och fel kan du använda själva undantags typen och felkoden för att skilja mellan undantag.  En lista över felkoder finns i [fel koder för autentisering och auktorisering](reference-aadsts-error-codes.md).

Under inloggningen kan du stöta på fel om medgivanden, villkorlig åtkomst (MFA, enhets hantering, platsbaserade begränsningar), utfärdande och inlösen av token och användar egenskaper.

Se följande avsnitt som stämmer överens med det språk som du använder för mer information om fel hantering för din app.

## <a name="net"></a>[.NET](#tab/dotnet)

När du bearbetar .NET-undantag kan du använda undantags typen och `ErrorCode` medlemmen för att skilja mellan undantag. `ErrorCode` värdena är konstanter av typen [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Du kan också ha en titt på fälten [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)och [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Om [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) genereras kan du testa [autentiserings-och auktoriseringsfel](reference-aadsts-error-codes.md) för att se om koden visas där.

### <a name="common-net-exceptions"></a>Vanliga .NET-undantag

Här följer de vanliga undantag som kan uppstå och vissa möjliga åtgärder:  

| Undantag | Felkod | Åtgärd|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: användaren eller administratören har inte samtyckt till att använda programmet med ID {appId} med namnet {appName}. Skicka en interaktiv auktoriseringsbegäran för den här användaren och resursen.| Du måste först få användar medgivande. Om du inte använder .NET Core (som inte har något webb gränssnitt) anropar du (bara en gång) `AcquireTokeninteractive` . Om du använder .NET Core eller inte vill göra något `AcquireTokenInteractive` kan användaren navigera till en URL för att ge medgivande: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read` . för att anropa `AcquireTokenInteractive` : `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: användaren måste använda [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md).| Det finns ingen minskning. Om MFA har kon figurer ATS för din klient organisation och Azure Active Directory (AAD) bestämmer sig för att genomdriva det måste du återgå till ett interaktivt flöde, till exempel `AcquireTokenInteractive` eller `AcquireTokenByDeviceCode` .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: anslags typen stöds inte över */vanliga* -eller */consumers* -slutpunkterna. Använd den */organizations* eller klient-/regionsspecifika slut punkten. Du använde */vanliga*.| Som förklaras i meddelandet från Azure AD måste utfärdaren ha en klient eller på annat */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: begär ande texten måste innehålla följande parameter: `client_secret or client_assertion` .| Detta undantag kan genereras om programmet inte har registrerats som ett offentligt klient program i Azure AD. Redigera manifestet för ditt program i Azure Portal och ange `allowPublicClient` till `true` . |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: Det gick inte att identifiera inloggad användare| Biblioteket kunde inte fråga den aktuella Windows-inloggade användaren eller så är den här användaren inte AD eller AAD-ansluten (arbets plats anslutna användare stöds inte). Minskning 1: på UWP kontrollerar du att programmet har följande funktioner: Enterprise-autentisering, privata nätverk (klient och Server), information om användar konton. Minskning 2: implementera din egen logik för att hämta användar namnet (till exempel john@contoso.com ) och Använd `AcquireTokenByIntegratedWindowsAuth` formuläret som tar med användar namnet.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Den här metoden förlitar sig på ett protokoll som exponeras av Active Directory (AD). Om en användare skapades i Azure Active Directory utan AD-återställning ("hanterad" användare) kommer den här metoden att Miss förväntas. Användare som skapats i AD och som backas upp av AAD ("federerade" användare) kan dra nytta av den här icke-interaktiva autentiseringsmetoden. Minskning: Använd interaktiv autentisering.|

### `MsalUiRequiredException`

En av de gemensamma status koderna som returnerades från MSAL.NET vid anrop `AcquireTokenSilent()` är `MsalError.InvalidGrantError` . Den här status koden innebär att programmet ska anropa autentiseringspaket igen, men i interaktivt läge (AcquireTokenInteractive eller AcquireTokenByDeviceCodeFlow för offentliga klient program och gör en utmaning i Web Apps). Detta beror på att ytterligare användar åtgärder krävs innan autentiseringstoken kan utfärdas.

I de flesta fall då `AcquireTokenSilent` detta Miss lyckas beror det på att token cache inte har tokens som matchar din begäran. Åtkomsttoken upphör att gälla om 1 timme och `AcquireTokenSilent` försöker hämta en ny baserat på en uppdateringstoken (i OAuth2 villkor är detta "Refresh token"-flödet). Det här flödet kan också inte utföras av olika orsaker, till exempel om en klient organisations administratör konfigurerar mer strikta inloggnings principer. 

Interaktionen syftar på att användaren ska göra en åtgärd. Några av dessa villkor är enkla för användarna att lösa (till exempel acceptera användnings villkor med ett enda klick) och vissa kan inte lösas med den aktuella konfigurationen (till exempel att datorn i fråga måste ansluta till ett visst företags nätverk). Vissa hjälper användaren att konfigurera Multi-Factor Authentication eller installera Microsoft Authenticator på deras enheter.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` klassificering av klassificering

MSAL exponerar ett `Classification` fält, som du kan läsa för att ge en bättre användar upplevelse, till exempel för att tala om för användaren att deras lösen ord har upphört att gälla eller att de måste ge medgivande till att använda vissa resurser. De värden som stöds är en del av `UiRequiredExceptionClassification` uppräkningen:

| Klassificering    | Innebörd           | Rekommenderad hantering |
|-------------------|-------------------|----------------------|
| BasicAction | Villkoret kan lösas genom användar interaktion under det interaktiva autentiserings flödet. | Anropa AcquireTokenInteractively (). |
| AdditionalAction | Villkoret kan lösas med hjälp av ytterligare återställnings interaktion med systemet, utanför det interaktiva autentiserings flödet. | Anropa AcquireTokenInteractively () för att visa ett meddelande som förklarar åtgärds åtgärden. Anrops programmet kan välja att dölja flöden som kräver additional_action om användaren inte är klar med åtgärden för att åtgärda problemet. |
| MessageOnly      | Villkoret kan inte lösas just nu. Om du startar ett interaktivt autentiseringsschema visas ett meddelande som förklarar villkoret. | Anropa AcquireTokenInteractively () för att visa ett meddelande som förklarar villkoret. AcquireTokenInteractively () returnerar UserCanceled-fel när användaren har läst meddelandet och stängt fönstret. Anrops programmet kan välja att dölja flöden som resulterar i message_only om användaren inte har nytta av meddelandet.|
| ConsentRequired  | Användar medgivande saknas eller har återkallats. | Anropa AcquireTokenInteractively () för att användaren ska kunna ge sitt medgivande. |
| UserPasswordExpired | Användarens lösen ord har upphört att gälla. | Anropa AcquireTokenInteractively () så att användaren kan återställa sina lösen ord. |
| PromptNeverFailed| Interaktiv autentisering anropades med parameter tolken = aldrig, tvinga MSAL att förlita sig på webbläsarens cookies och inte att visa webbläsaren. Detta har misslyckats. | Anropa AcquireTokenInteractively () utan prompt. None |
| AcquireTokenSilentFailed | MSAL SDK har inte tillräckligt med information för att hämta en token från cachen. Detta kan bero på att det inte finns några token i cachen eller om det inte gick att hitta något konto. Fel meddelandet innehåller mer information.  | Anropa AcquireTokenInteractively (). |
| Inget    | Det finns ingen ytterligare information. Villkoret kan lösas genom användar interaktion under det interaktiva autentiserings flödet. | Anropa AcquireTokenInteractively (). |

## <a name="net-code-example"></a>Exempel på .NET-kod

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
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
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

## <a name="javascript"></a>[JavaScript](#tab/javascript)

MSAL.js innehåller fel objekt som kan vara abstrakta och klassificera de olika typerna av vanliga fel. Den innehåller också ett gränssnitt för att få åtkomst till information om felen, till exempel fel meddelanden, för att hantera dem på rätt sätt.

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
- `AuthError.message`: Samma som `errorMessage` .
- `AuthError.stack`: Stack spårning för utlösta fel.

### <a name="error-types"></a>Fel typer

Följande fel typer är tillgängliga:

- `AuthError`: Bask lass klassen för MSAL.jss biblioteket, används också för oväntade fel.

- `ClientAuthError`: Fel klass, som anger ett problem med klientautentisering. De flesta fel som kommer från biblioteket kommer att bli ClientAuthErrors. Dessa fel beror på saker som att anropa en inloggnings metod när inloggningen redan pågår, att användaren avbryter inloggningen och så vidare.

- `ClientConfigurationError`: Fel klass, utökningar `ClientAuthError` som genererats innan begär Anden görs när de tilldelade parametrarna för användar konfiguration är felaktiga eller saknas.

- `ServerError`: Klassen Error representerar de fel strängar som skickats av autentiseringsservern. Detta kan vara fel som ogiltiga format eller parametrar för begäran, eller andra fel som hindrar servern från att autentisera eller auktorisera användaren.

- `InteractionRequiredAuthError`: Fel klass, utökar `ServerError` för att representera Server fel som kräver ett interaktivt anrop. Det här felet uppstår av `acquireTokenSilent` om användaren måste interagera med servern för att ange autentiseringsuppgifter eller tillstånd för autentisering/auktorisering. Felkoderna är `"interaction_required"` , `"login_required"` och `"consent_required"` .

För fel hantering i autentiserings flöden med omdirigerings metoder ( `loginRedirect` , `acquireTokenRedirect` ) måste du registrera återanropet, som anropas med lyckat eller misslyckat efter omdirigeringen med hjälp av `handleRedirectCallback()` metoden enligt följande:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Metoderna för att se pop-up ( `loginPopup` , `acquireTokenPopup` ) returnera löfte så att du kan använda löftes mönstret (. och. Catch) för att hantera dem som de visas:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Fel som kräver interaktion

Ett fel returneras när du försöker använda en icke-interaktiv metod för att förvärva en token, till exempel `acquireTokenSilent` , men MSAL inte kunde göra det tyst.

Möjliga orsaker är:

- Du måste logga in
- Du måste godkänna
- Du måste gå igenom en Multi-Factor Authentication-upplevelse.

Reparationen är att anropa en interaktiv metod som `acquireTokenPopup` eller `acquireTokenRedirect` :

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

## <a name="python"></a>[Python](#tab/python)

I MSAL för python förmedlas de flesta fel som ett retur värde från API-anropet. Felet visas som en ord lista som innehåller JSON-svaret från Microsoft Identity Platform.

* Ett lyckat svar innehåller `"access_token"` nyckeln. Svars formatet definieras av OAuth2-protokollet. Mer information finns i [5,1 svar](https://tools.ietf.org/html/rfc6749#section-5.1)
* Ett fel svar innehåller `"error"` vanligt vis `"error_description"` . Svars formatet definieras av OAuth2-protokollet. Mer information finns i [5,2-fel svar](https://tools.ietf.org/html/rfc6749#section-5.2)

När ett fel returneras `"error_description"` innehåller nyckeln ett läsbart meddelande, vilket i sin tur vanligt vis innehåller en felkod för Microsoft Identity Platform. Mer information om de olika fel koderna finns i [fel koder för autentisering och auktorisering](./reference-aadsts-error-codes.md).

I MSAL för python är undantag sällsynt eftersom de flesta fel hanteras genom att returnera ett felvärde. `ValueError`Undantaget utlöses bara när det är problem med hur du försöker använda biblioteket, till exempel när API-parameter (s) har fel format.

## <a name="java"></a>[Java](#tab/java)

I MSAL för Java finns det tre typer av undantag: `MsalClientException` , `MsalServiceException` och `MsalInteractionRequiredException` . allt ärver från `MsalException` .

- `MsalClientException` genereras när ett fel inträffar som är lokalt i biblioteket eller på enheten.
- `MsalServiceException` utlöses när STS (Secure token service) returnerar ett fel svar eller ett annat nätverks fel inträffar.
- `MsalInteractionRequiredException` genereras när UI-interaktion krävs för att autentiseringen ska lyckas.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` exponerar HTTP-huvuden som returneras i begär anden till STS. Få åtkomst till dem via `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

En av de vanliga status koderna som returnerades från MSAL för Java vid anrop `AcquireTokenSilently()` är `InvalidGrantError` . Det innebär att ytterligare användar åtgärder krävs innan en autentiseringstoken kan utfärdas. Programmet bör anropa autentiseringspaket igen, men i interaktivt läge genom att skicka `AuthorizationCodeParameters` eller `DeviceCodeParameters` för offentliga klient program.

I de flesta fall då `AcquireTokenSilently` Miss lyckas, beror det på att token cache inte har en token som matchar din begäran. Åtkomsttoken upphör att gälla om en timme och kommer att `AcquireTokenSilently` försöka hämta en ny utifrån en uppdateringstoken. I OAuth2 villkor är det här uppdaterings-token. Det här flödet kan också inte utföras av olika anledningar, t. ex. När en klient administratör konfigurerar mer strikta inloggnings principer.

Vissa villkor som resulterar i detta fel är lätta för användarna att lösa. De kan till exempel behöva godkänna användnings villkoren. Eller så kanske begäran inte kan uppfyllas med den aktuella konfigurationen eftersom datorn måste ansluta till ett enskilt företags nätverk.

MSAL visar ett `reason` fält som du kan använda för att ge en bättre användar upplevelse. Fältet kan till exempel `reason` leda till att användaren anger att deras lösen ord har upphört att gälla eller att de måste ge tillåtelse att använda vissa resurser. De värden som stöds är en del av  `InteractionRequiredExceptionReason` uppräkningen:

| Orsak | Innebörd | Rekommenderad hantering |
|---------|-----------|-----------------------------|
| `BasicAction` | Villkoret kan lösas genom användar interaktion under det interaktiva autentiserings flödet | Anropa `acquireToken` med interaktiva parametrar |
| `AdditionalAction` | Villkoret kan lösas med hjälp av ytterligare återställnings interaktion med systemet utanför det interaktiva autentiserings flödet. | Anropa `acquireToken` med interaktiva parametrar för att visa ett meddelande som förklarar vilken åtgärd som ska vidtas. Den anropande appen kan välja att dölja flöden som kräver ytterligare åtgärder om användaren inte är tvungen att slutföra åtgärden. |
| `MessageOnly` | Villkoret kan inte lösas just nu. Starta interaktiva autentiserings flöde för att visa ett meddelande som förklarar villkoret. | Anropa `acquireToken` med interaktiva parametrar för att visa ett meddelande som förklarar villkoret. `acquireToken` Returnerar `UserCanceled` felet när användaren har läst meddelandet och stängt fönstret. Appen kan välja att dölja flöden som resulterar i ett meddelande om användaren inte har nytta av meddelandet. |
| `ConsentRequired`| Användar medgivande saknas eller har återkallats. |Anropa `acquireToken` med interaktiva parametrar så att användaren kan ge sitt medgivande. |
| `UserPasswordExpired` | Användarens lösen ord har upphört att gälla. | Anropa `acquireToken` med interaktiv parameter så att användaren kan återställa sina lösen ord |
| `None` |  Mer information finns. Villkoret kan lösas av användar interaktion under det interaktiva autentiserings flödet. | Anropa `acquireToken` med interaktiva parametrar |

### <a name="code-example"></a>Kod exempel

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacos"></a>[iOS/macOS](#tab/iosmacos)

Den fullständiga listan med MSAL för iOS-och macOS-fel visas i [MSALError-uppräkningen](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Alla MSAL-producerade fel returneras med `MSALErrorDomain` domän.

För system fel returnerar MSAL originalet `NSError` från system-API: et. Om till exempel hämtning av token Miss lyckas på grund av brist på nätverks anslutning, returnerar MSAL ett fel med `NSURLErrorDomain` domänen och `NSURLErrorNotConnectedToInternet` koden.

Vi rekommenderar att du hanterar minst följande två MSAL-fel på klient sidan:

- `MSALErrorInteractionRequired`: Användaren måste göra en interaktiv begäran. Det finns många villkor som kan leda till det här felet, till exempel en utgången autentiseringsbegäran eller behovet av ytterligare autentiseringskrav. Anropa det MSAL interaktiva token för hämtnings-API för att återställa. 

- `MSALErrorServerDeclinedScopes`: Vissa eller alla omfattningar nekades. Bestäm om du vill fortsätta med endast beviljade omfattningar eller stoppa inloggnings processen.

> [!NOTE]
> `MSALInternalError`Uppräkningen ska endast användas för referens och fel sökning. Försök inte att automatiskt hantera dessa fel vid körning. Om din app påträffar något av de fel som faller under `MSALInternalError` , kanske du vill visa ett allmänt användar meddelande som förklarar vad som hände.

Till exempel `MSALInternalErrorBrokerResponseNotReceived` innebär att användaren inte slutförde autentiseringen och returnerade manuellt till appen. I det här fallet bör appen Visa ett allmänt fel meddelande som förklarar att autentiseringen inte slutförts och att de försöker autentisera igen.

Följande mål-C-exempel kod visar metod tips för hantering av några vanliga fel villkor:

```objc
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

---

## <a name="conditional-access-and-claims-challenges"></a>Utmaningarna för villkorlig åtkomst och anspråk

När token hämtas i bakgrunden kan ditt program få fel när en [utmanings utmaning för anspråk](../azuread-dev/conditional-access-dev-guide.md) , till exempel MFA-princip, krävs av ett API som du försöker få åtkomst till.

Mönstret för att hantera det här felet är att interaktivt hämta en token med hjälp av MSAL. Att interaktivt förvärva en token frågar användaren och ger dem möjlighet att uppfylla den nödvändiga principen för villkorlig åtkomst.

I vissa fall när du anropar ett API som kräver villkorlig åtkomst kan du få en anspråks utmaning i fel meddelandet från API: et. Om den villkorliga åtkomst principen till exempel är att ha en hanterad enhet (Intune), kommer felet att vara något som liknar [AADSTS53000: din enhet måste hanteras för att få åtkomst till den här resursen](reference-aadsts-error-codes.md) eller något liknande. I det här fallet kan du skicka anspråken i det Hämta token-anropet så att användaren uppmanas att uppfylla den aktuella principen.

### <a name="net"></a>.NET

När du anropar ett API som kräver villkorlig åtkomst från MSAL.NET måste ditt program hantera anspråks utmanings undantag. Detta visas som en [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) där [anspråks](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) egenskapen inte är tom.

För att hantera anspråks utmaningen måste du använda `.WithClaim()` metoden i `PublicClientApplicationBuilder` klassen.

### <a name="javascript"></a>JavaScript

När du hämtar token tyst (med `acquireTokenSilent` ) med hjälp av MSAL.js kan ditt program få fel när en [utmanings utmaning för anspråk](../azuread-dev/conditional-access-dev-guide.md) , till exempel MFA-princip krävs av ett API som du försöker få åtkomst till.

Mönstret för att hantera det här felet är att göra ett interaktivt anrop för att hämta token i MSAL.js som `acquireTokenPopup` eller `acquireTokenRedirect` som i följande exempel:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

Att interaktivt förvärva token frågar användaren och ger dem möjlighet att uppfylla den nödvändiga principen för villkorlig åtkomst.

När du anropar ett API som kräver villkorlig åtkomst kan du få en anspråks utmaning i fel meddelandet från API: et. I det här fallet kan du skicka de anspråk som returneras i fel till `claimsRequest` fältet i `AuthenticationParameters.ts` klassen för att uppfylla den aktuella principen. 

Mer information finns i [begära ytterligare anspråk](active-directory-optional-claims.md) .

### <a name="msal-for-ios-and-macos"></a>MSAL för iOS och macOS

MSAL för iOS och macOS gör att du kan begära vissa anspråk i både interaktiva och tysta token för hämtning.

Om du vill begära anpassade anspråk anger du `claimsRequest` i `MSALSilentTokenParameters` eller `MSALInteractiveTokenParameters` .

Mer information finns i [begäran om anpassade anspråk med MSAL för iOS och MacOS](request-custom-claims.md) .

## <a name="retrying-after-errors-and-exceptions"></a>Försöker igen efter fel och undantag

Du förväntas införa egna återförsöks principer när du anropar MSAL. MSAL gör HTTP-anrop till AAD-tjänsten och tillfälliga fel kan uppstå, till exempel om nätverket kan stängas av eller servern är överbelastad.  

### <a name="http-error-codes-500-600"></a>HTTP-felkoder 500-600

MSAL.NET implementerar en enkel återförsöks funktion för fel med HTTP-felkoderna 500-600.

### <a name="http-429"></a>HTTP 429

När service token Server (STS) är överbelastad med för många begär Anden returneras HTTP-fel 429 med ett tips om hur länge du kan försöka igen i fältet `Retry-After` svar.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) -ytor `System.Net.Http.Headers.HttpResponseHeaders` som en egenskap `namedHeaders` . Du kan använda ytterligare information från felkoden för att förbättra tillförlitligheten för dina program. I det fall som beskrivs kan du använda `RetryAfterproperty` (av typen `RetryConditionHeaderValue` ) och beräkna när du vill försöka igen.

Här är ett exempel på ett daemon-program som använder flödet för klientautentiseringsuppgifter. Du kan anpassa detta till någon av metoderna för att förvärva en token.

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
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
