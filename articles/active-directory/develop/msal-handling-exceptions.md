---
title: Fel och undantag (MSAL)
titleSuffix: Microsoft identity platform
description: Lär dig hur du hanterar fel och undantag, villkorlig åtkomst och anspråksutmaningar i MSAL-program.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/22/2019
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: f78c64fc0ba25dc3310b24e873dbae266ea2f281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050323"
---
# <a name="handle-msal-exceptions-and-errors"></a>Hantera MSAL-undantag och fel

Den här artikeln innehåller en översikt över olika typer av fel och rekommendationer för hantering av vanliga inloggningsfel.

## <a name="msal-error-handling-basics"></a>Grunderna i MSAL-felhantering

Undantag i MICROSOFT Authentication Library (MSAL) är avsedda för apputvecklare att felsöka – inte för visning för slutanvändare. Undantagsmeddelanden är inte lokaliserade.

När du bearbetar undantag och fel kan du använda själva undantagstypen och felkoden för att skilja mellan undantag.  En lista över felkoder finns i [Autentiserings- och auktoriseringsfelkoder](reference-aadsts-error-codes.md).

Under inloggningen kan du stöta på fel om medgivanden, villkorlig åtkomst (MFA, Enhetshantering, Platsbaserade begränsningar), tokenutgivning och inlösen samt användaregenskaper.

Mer information om felhantering för din app finns i följande avsnitt som matchar det språk du använder.

## <a name="net"></a>[.NET](#tab/dotnet)

När du bearbetar .NET-undantag kan du `ErrorCode` använda själva undantagstypen och medlemmen för att skilja mellan undantag. `ErrorCode`värden är konstanter av typen [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Du kan också ta en titt på fälten [MsalClientException,](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet) [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)och [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Om [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) har kastats kan du prova [autentiserings- och auktoriseringsfelkoder](reference-aadsts-error-codes.md) för att se om koden finns med där.

### <a name="common-net-exceptions"></a>Vanliga .NET-undantag

Här är de vanliga undantagen som kan genereras och några möjliga mildrande åtgärder:  

| Undantag | Felkod | Åtgärd|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: Användaren eller administratören har inte samtyckt till att använda programmet med ID {appId} med namnet {appName}. Skicka en interaktiv auktoriseringsbegäran för den här användaren och resursen.| Du måste få användarens medgivande först. Om du inte använder .NET Core (som inte har något webbgränssnitt) `AcquireTokeninteractive`ringer du (endast en gång) . Om du använder .NET core eller inte `AcquireTokenInteractive`vill göra en kan användaren navigera `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`till en URL för att ge sitt samtycke: . att `AcquireTokenInteractive`ringa:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: Användaren måste använda MFA (Multifaktor authentication).| Det finns ingen lindring. Om MFA är konfigurerat för din klient och Azure Active Directory (AAD) beslutar att genomdriva `AcquireTokenInteractive` `AcquireTokenByDeviceCode`den, måste du återställa till ett interaktivt flöde som eller .|
| [MsalServiceUtställning](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: Bidragstypen stöds inte via slutpunkterna */common* eller */consumers.* Använd *slutpunkten /organisationer* eller klientspecifik. Du använde */common*.| Som förklaras i meddelandet från Azure AD måste myndigheten ha en klient eller på annat sätt */organisationer*.|
| [MsalServiceUtställning](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: Brödtexten för begäran `client_secret or client_assertion`måste innehålla följande parameter: .| Det här undantaget kan genereras om ditt program inte har registrerats som ett offentligt klientprogram i Azure AD. I Azure-portalen redigerar du manifestet `true`för ditt program och ställer in på `allowPublicClient` . |
| [MsalClientUtställning](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: Det gick inte att identifiera inloggad användare| Biblioteket kunde inte fråga den aktuella Windows-inloggade användaren eller så är den här användaren inte AD eller AAD ansluten (arbetsplatss anslutna användare stöds inte). Begränsning 1: kontrollera att programmet har följande funktioner: Företagsautentisering, Privata nätverk (klient och server), användarkontoinformation på UWP. Begränsning 2: Implementera din egen logik för john@contoso.comatt hämta `AcquireTokenByIntegratedWindowsAuth` användarnamnet (till exempel) och använd formuläret som tar i användarnamnet.|
| [MsalClientUtställning](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Den här metoden är beroende av ett protokoll som exponeras av Active Directory (AD). Om en användare skapades i Azure Active Directory utan AD-stöd ("hanterad" användare) misslyckas den här metoden. Användare som skapats i AD och backas upp av AAD ("federerade" användare) kan dra nytta av denna icke-interaktiva autentiseringsmetod. Begränsning: Använd interaktiv autentisering.|

### `MsalUiRequiredException`

En av vanliga statuskoder som returneras `AcquireTokenSilent()` från `MsalError.InvalidGrantError`MSAL.NET när du ringer är . Den här statuskoden innebär att programmet ska anropa autentiseringsbiblioteket igen, men i interaktivt läge (AcquireTokenInteractive eller AcquireTokenByDeviceCodeFlow för offentliga klientprogram och göra en utmaning i webbappar). Detta beror på att ytterligare användarinteraktion krävs innan autentiseringstoken kan utfärdas.

För det mesta `AcquireTokenSilent` när misslyckas beror det på att tokencachen inte har token som matchar din begäran. Åtkomsttoken upphör att gälla `AcquireTokenSilent` om 1 timme och försöker hämta en ny baserat på en uppdateringstoken (i OAuth2-termer är detta flödet "Uppdatera token"). Det här flödet kan också misslyckas av olika skäl, till exempel om en klientadministratör konfigurerar strängare inloggningsprinciper. 

Interaktionen syftar till att få användaren att göra en åtgärd. Vissa av dessa villkor är lätta för användare att lösa (till exempel acceptera användarvillkor med ett enda klick), och vissa kan inte lösas med den aktuella konfigurationen (till exempel måste maskinen i fråga ansluta till ett visst företagsnätverk). Vissa hjälper användaren att konfigurera multifaktorautentisering eller installera Microsoft Authenticator på sin enhet.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`klassificering uppräkning

MSAL exponerar `Classification` ett fält som du kan läsa för att ge en bättre användarupplevelse, till exempel för att tala om för användaren att deras lösenord har upphört att gälla eller att de måste ge samtycke till att använda vissa resurser. Värdena som stöds `UiRequiredExceptionClassification` är en del av uppräkningen:

| Klassificering    | Betydelse           | Rekommenderad hantering |
|-------------------|-------------------|----------------------|
| Grundläggande åtgärd | Villkoret kan lösas genom användarinteraktion under det interaktiva autentiseringsflödet. | Ring AcquireTokenInteractively(). |
| Ytterligare åtgärd | Villkoret kan lösas genom ytterligare avhjälpande interaktion med systemet, utanför det interaktiva autentiseringsflödet. | Anropa AcquireTokenInteractively() för att visa ett meddelande som förklarar den avhjälpande åtgärden. Anropande program kan välja att dölja flöden som kräver additional_action om det är osannolikt att användaren slutför den korrigerande åtgärden. |
| MessageOnly      | Villkoret kan inte lösas just nu. Om du startar interaktivt autentiseringsflöde visas ett meddelande som förklarar villkoret. | Anropa AcquireTokenInteractively() för att visa ett meddelande som förklarar villkoret. AcquireTokenInteractively() returnerar UserCanceled-fel när användaren läser meddelandet och stänger fönstret. Anropande program kan välja att dölja flöden som resulterar i message_only om det är osannolikt att användaren kommer att dra nytta av meddelandet.|
| SamtyckeFörvärkt  | Användarens medgivande saknas eller har återkallats. | Ring AcquireTokenInteractively() för användaren att ge sitt samtycke. |
| UserPasswordExpirerad | Användarens lösenord har upphört att gälla. | Ring AcquireTokenInteractively() så att användaren kan återställa sitt lösenord. |
| PromptNeverFailed| Interaktiv autentisering anropades med parametern prompt=never, vilket tvingade MSAL att förlita sig på webbläsarcookies och inte visa webbläsaren. Detta har misslyckats. | Anropa AcquireTokenInteractively() utan Fråga.Ingen |
| FörvärvTokenSilentFailed | MSAL SDK har inte tillräckligt med information för att hämta en token från cachen. Detta kan bero på att inga token finns i cacheminnet eller att ett konto inte hittades. Felmeddelandet innehåller mer information.  | Ring AcquireTokenInteractively(). |
| Inget    | Inga ytterligare detaljer finns. Villkoret kan lösas genom användarinteraktion under det interaktiva autentiseringsflödet. | Ring AcquireTokenInteractively(). |

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

## <a name="javascript"></a>[Javascript](#tab/javascript)

MSAL.js tillhandahåller felobjekt som abstraherar och klassificerar de olika typerna av vanliga fel. Det ger också gränssnitt för att komma åt specifika detaljer om fel som felmeddelanden för att hantera dem på rätt sätt.

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

Genom att utöka felklassen har du tillgång till följande egenskaper:
- `AuthError.message`: Samma `errorMessage`som .
- `AuthError.stack`: Stackspårning för uppslängda fel.

### <a name="error-types"></a>Feltyper

Följande feltyper är tillgängliga:

- `AuthError`: Basfelklass för MSAL.js-biblioteket, som också används för oväntade fel.

- `ClientAuthError`: Felklass, som betecknar ett problem med klientautentisering. De flesta fel som kommer från biblioteket är ClientAuthErrors. Dessa fel beror på saker som att ringa en inloggningsmetod när inloggning redan pågår, användaren avbryter inloggningen och så vidare.

- `ClientConfigurationError`: Felklass, `ClientAuthError` utökar kastas innan begäranden görs när de angivna användarkonfigurationsparametrarna är felformade eller saknas.

- `ServerError`: Felklass, representerar felsträngar som skickas av autentiseringsservern. Det kan bero på fel som ogiltiga format eller parametrar för begäran, eller andra fel som hindrar servern från att autentisera eller auktorisera användaren.

- `InteractionRequiredAuthError`: Felklass, `ServerError` utökas för att representera serverfel, som kräver ett interaktivt anrop. Det här felet `acquireTokenSilent` genereras av om användaren är skyldig att interagera med servern för att ge autentiseringsuppgifter eller medgivande för autentisering/auktorisering. Felkoder `"interaction_required"`inkluderar `"login_required"`, `"consent_required"`och .

För felhantering i autentiseringsflöden med omdirigeringsmetoder (`loginRedirect`, `acquireTokenRedirect`måste du registrera motringningen, som anropas med framgång eller fel efter `handleRedirectCallback()` omdirigeringsmetoden enligt följande:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Metoderna för popup-upplevelse`loginPopup` `acquireTokenPopup`( , ) returnerar löften, så att du kan använda löftesmönstret (.then och .catch) för att hantera dem som visas:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Fel som kräver interaktion

Ett fel returneras när du försöker använda en icke-interaktiv `acquireTokenSilent`metod för att hämta en token, till exempel , men MSAL kunde inte göra det tyst.

Möjliga orsaker är:

- du måste logga in
- du måste samtycka
- du behöver gå igenom en multifaktorautentiseringsupplevelse.

Reparationen är att anropa en `acquireTokenPopup` interaktiv `acquireTokenRedirect`metod som eller:

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

I MSAL för Python förmedlas de flesta fel som ett returvärde från API-anropet. Felet representeras som en ordlista som innehåller JSON-svaret från Microsofts identitetsplattform.

* Ett lyckat svar `"access_token"` innehåller nyckeln. Svarets format definieras av OAuth2-protokollet. Mer information finns i [5.1 Lyckat svar](https://tools.ietf.org/html/rfc6749#section-5.1)
* Ett felsvar `"error"` innehåller `"error_description"`och vanligtvis . Svarets format definieras av OAuth2-protokollet. Mer information finns i [5.2 Felsvar](https://tools.ietf.org/html/rfc6749#section-5.2)

När ett fel returneras `"error_description"` innehåller nyckeln ett meddelande som kan läsas av människor. som i sin tur vanligtvis innehåller en Microsoft identity platform felkod. Mer information om de olika felkoderna finns i [Autentiserings- och auktoriseringsfelkoder](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes).

I MSAL för Python är undantag sällsynta eftersom de flesta fel hanteras genom att returnera ett felvärde. Undantaget `ValueError` genereras bara när det finns ett problem med hur du försöker använda biblioteket – till exempel när API-parameterer är felformaterade.

## <a name="java"></a>[Java](#tab/java)

I MSAL för Java finns det `MsalClientException`tre `MsalServiceException`typer `MsalInteractionRequiredException`av undantag: , , och ; alla som `MsalException`ärver från .

- `MsalClientException`genereras när ett fel uppstår som är lokalt för biblioteket eller enheten.
- `MsalServiceException`genereras när den säkra tokentjänsten (STS) returnerar ett felsvar eller ett annat nätverksfel inträffar.
- `MsalInteractionRequiredException`genereras när gränssnittsinteraktion krävs för att autentiseringen ska lyckas.

### <a name="msalserviceexception"></a>MsalServiceUtställning

`MsalServiceException`visar HTTP-huvuden som returneras i begäranden till STS. Få tillgång till dem via`MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

En av vanliga statuskoder som returneras `AcquireTokenSilently()` från `InvalidGrantError`MSAL för Java när du ringer är . Det innebär att ytterligare användarinteraktion krävs innan en autentiseringstoken kan utfärdas. Programmet ska anropa autentiseringsbiblioteket igen, `AuthorizationCodeParameters` men `DeviceCodeParameters` i interaktivt läge genom att skicka eller för offentliga klientprogram.

För det mesta `AcquireTokenSilently` när misslyckas beror det på att tokencachen inte har en token som matchar din begäran. Åtkomsttoken upphör att gälla `AcquireTokenSilently` om en timme och försöker få en ny baserat på en uppdateringstoken. I OAuth2 termer är detta flödet Uppdatera token. Det här flödet kan också misslyckas av olika skäl, till exempel när en klientadministratör konfigurerar strängare inloggningsprinciper.

Vissa villkor som resulterar i det här felet är enkla för användare att lösa. De kan till exempel behöva godkänna användarvillkor. Eller kanske begäran inte kan uppfyllas med den aktuella konfigurationen eftersom datorn måste ansluta till ett visst företagsnätverk.

MSAL exponerar `reason` ett fält som du kan använda för att ge en bättre användarupplevelse. `reason` Fältet kan till exempel leda till att du talar om för användaren att deras lösenord har upphört att gälla eller att de måste ge sitt medgivande för att använda vissa resurser. Värdena som stöds `InteractionRequiredExceptionReason` är en del av uppräkningen:

| Orsak | Betydelse | Rekommenderad hantering |
|---------|-----------|-----------------------------|
| `BasicAction` | Villkoret kan lösas genom användarinteraktion under det interaktiva autentiseringsflödet | Ring `acquireToken` med interaktiva parametrar |
| `AdditionalAction` | Villkoret kan lösas genom ytterligare avhjälpande interaktion med systemet utanför det interaktiva autentiseringsflödet. | Anropa `acquireToken` med interaktiva parametrar för att visa ett meddelande som förklarar den korrigerande åtgärden som ska vidtas. Den anropande appen kan välja att dölja flöden som kräver ytterligare åtgärder om det är osannolikt att användaren slutför den korrigerande åtgärden. |
| `MessageOnly` | Villkoret kan inte lösas just nu. Starta interaktivt autentiseringsflöde för att visa ett meddelande som förklarar villkoret. | Anropa `acquireToken` med interaktiva parametrar för att visa ett meddelande som förklarar villkoret. `acquireToken`returnerar `UserCanceled` felet efter att användaren har läst meddelandet och stängt fönstret. Appen kan välja att dölja flöden som resulterar i meddelande om det är osannolikt att användaren kommer att dra nytta av meddelandet. |
| `ConsentRequired`| Användarens medgivande saknas eller har återkallats. |Ring `acquireToken` med interaktiva parametrar så att användaren kan ge sitt samtycke. |
| `UserPasswordExpired` | Användarens lösenord har upphört att gälla. | Ring `acquireToken` med interaktiv parameter så att användaren kan återställa sitt lösenord |
| `None` |  Ytterligare information finns. Villkoret kan lösas genom användarinteraktion under det interaktiva autentiseringsflödet. | Ring `acquireToken` med interaktiva parametrar |

### <a name="code-example"></a>Exempel på kod

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

Den fullständiga listan över MSAL för iOS- och macOS-fel visas i [MSALError-uppräkningen](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Alla MSAL-producerade fel returneras `MSALErrorDomain` med domänen.

För systemfel returnerar MSAL `NSError` originalet från system-API:et. Om tokeninsamling till exempel misslyckas på grund av brist på `NSURLErrorDomain` nätverksanslutning `NSURLErrorNotConnectedToInternet` returnerar MSAL ett fel med domänen och koden.

Vi rekommenderar att du hanterar minst följande två MSAL-fel på klientsidan:

- `MSALErrorInteractionRequired`: Användaren måste göra en interaktiv begäran. Det finns många villkor som kan leda till det här felet, till exempel en förfallen autentiseringssession eller behovet av ytterligare autentiseringskrav. Anropa API:et för interaktiv tokeninsamling för att återställa MSAL. 

- `MSALErrorServerDeclinedScopes`: Vissa eller alla omfattningar har avvisats. Bestäm om du bara vill fortsätta med de beviljade scopea eller stoppa inloggningsprocessen.

> [!NOTE]
> Uppräkningen `MSALInternalError` ska endast användas för referens och felsökning. Försök inte att automatiskt hantera dessa fel vid körning. Om din app stöter på något `MSALInternalError`av de fel som faller under kanske du vill visa ett allmänt meddelande som visas som en allmän användare som står inför ett meddelande som förklarar vad som hände.

Innebär till `MSALInternalErrorBrokerResponseNotReceived` exempel att användaren inte slutförde autentiseringen och manuellt återvände till appen. I det här fallet bör appen visa ett allmänt felmeddelande som förklarar att autentiseringen inte slutfördes och föreslå att de försöker autentisera igen.

Följande exempelkod för Mål C visar bästa praxis för hantering av vissa vanliga felvillkor:

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

## <a name="conditional-access-and-claims-challenges"></a>Villkorad åtkomst och anspråksutmaningar

När du får token tyst kan ditt program få fel när en [begäran om villkorlig åtkomst-anspråksutmaning,](../azuread-dev/conditional-access-dev-guide.md) till exempel MFA-principen, krävs av ett API som du försöker komma åt.

Mönstret för hantering av det här felet är att interaktivt hämta en token med MSAL. Interaktivt hämta en token frågar användaren och ger dem möjlighet att uppfylla den nödvändiga principen villkorlig åtkomst.

I vissa fall när du anropar ett API som kräver villkorlig åtkomst kan du få en anspråksutmaning i felet från API:et. Om principen villkorlig åtkomst till exempel ska ha en hanterad enhet (Intune) blir felet ungefär [AADSTS53000: Enheten måste hanteras för att komma åt den här resursen](reference-aadsts-error-codes.md) eller något liknande. I det här fallet kan du skicka anspråken i det inhämtningstokenanrop så att användaren uppmanas att uppfylla rätt princip.

### <a name="net"></a>.NET

När du anropar ett API som kräver villkorlig åtkomst från MSAL.NET måste ditt program hantera undantag för anspråksutmaning. Detta visas som en [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) där egenskapen [Fordringar](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) inte är tom.

För att hantera anspråksutmaningen `.WithClaim()` måste du använda `PublicClientApplicationBuilder` klassens metod.

### <a name="javascript"></a>JavaScript

När du hämtar token `acquireTokenSilent`tyst (med hjälp av) med MSAL.js kan ditt program få fel när en [begäran om villkorlig åtkomst-anspråksutmaning,](../azuread-dev/conditional-access-dev-guide.md) till exempel MFA-principen, krävs av ett API som du försöker komma åt.

Mönstret för att hantera det här felet är att ringa ett `acquireTokenPopup` interaktivt anrop för att hämta token i MSAL.js, till exempel eller `acquireTokenRedirect` som i följande exempel:

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

Att interaktivt hämta token frågar användaren och ger dem möjlighet att uppfylla den obligatoriska principen för villkorlig åtkomst.

När du anropar ett API som kräver villkorlig åtkomst kan du få en anspråksutmaning i felet från API:et. I det här fallet kan du skicka anspråk `claimsRequest` som returneras i felet till fältet för `AuthenticationParameters.ts` klassen för att uppfylla rätt princip. 

Se [Begära ytterligare anspråk](active-directory-optional-claims.md) för mer information.

### <a name="msal-for-ios-and-macos"></a>MSAL för iOS och macOS

MSAL för iOS och macOS kan du begära specifika anspråk i både interaktiva och tysta token förvärv scenarier.

Om du vill `claimsRequest` begära `MSALSilentTokenParameters` `MSALInteractiveTokenParameters`anpassade anspråk anger du i eller .

Mer information [finns i Begär anpassade anspråk med MSAL för iOS och macOS.](request-custom-claims.md)

## <a name="retrying-after-errors-and-exceptions"></a>Försöker igen efter fel och undantag

Du förväntas implementera dina egna principer för återförsök när du anropar MSAL. MSAL gör HTTP-anrop till AAD-tjänsten och tillfälliga fel kan uppstå, till exempel kan nätverket gå ner eller servern är överbelastad.  

### <a name="http-error-codes-500-600"></a>HTTP-felkoder 500-600

MSAL.NET implementerar en enkel mekanism för ett nytt försök för fel med HTTP-felkoder 500-600.

### <a name="http-429"></a>HTTP 429

När SERVICE Token Server (STS) är överbelastad med för många begäranden returneras HTTP-fel 429 `Retry-After` med en antydan om hur länge tills du kan försöka igen i svarsfältet.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) `System.Net.Http.Headers.HttpResponseHeaders` ytor som `namedHeaders`en egenskap . Du kan använda ytterligare information från felkoden för att förbättra tillförlitligheten i dina program. I det beskrivna fallet `RetryAfterproperty` kan du `RetryConditionHeaderValue`använda (av typen) och beräkna när du ska försöka igen.

Här är ett exempel för ett daemon-program som använder klientautentiseringsflödet. Du kan anpassa detta till någon av metoderna för att hämta en token.

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
