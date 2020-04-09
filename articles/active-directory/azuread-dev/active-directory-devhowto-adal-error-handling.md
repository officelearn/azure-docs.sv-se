---
title: Metodtips för ADAL-klientappfelhantering | Azure
description: Ger vägledning för felhantering och metodtips för ADAL-klientprogram.
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 8973412b2d6575d524874ba05b34af7661655e19
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981077"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Metodtips för felhantering för ADAL-klienter (Azure Active Directory Authentication Library)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Den här artikeln innehåller vägledning om vilken typ av fel som utvecklare kan stöta på när de använder ADAL för att autentisera användare. När du använder ADAL finns det flera fall där en utvecklare kan behöva gå in och hantera fel. Korrekt felhantering säkerställer en bra slutanvändarupplevelse och begränsar antalet gånger slutanvändaren behöver logga in.

I den här artikeln utforskar vi de specifika fallen för varje plattform som stöds av ADAL och hur ditt program kan hantera varje ärende på rätt sätt. Felvägledningen är uppdelad i två bredare kategorier, baserat på de tokeninhämtningsmönster som tillhandahålls av ADAL API:er:

- **AcquireTokenSilent**: Klienten försöker hämta en token tyst (inget användargränssnitt) och kan misslyckas om ADAL misslyckas. 
- **AcquireToken**: Kunden kan försöka tysta förvärv, men kan också utföra interaktiva begäranden som kräver inloggning.

> [!TIP]
> Det är en bra idé att logga alla fel och undantag när du använder ADAL och Azure AD. Loggar är inte bara till hjälp för att förstå den allmänna hälsan för ditt program, men är också viktiga när du felsöker bredare problem. Även om ditt program kan återställas från vissa fel, kan de antyda bredare designproblem som kräver kodändringar för att lösa. 
> 
> När du implementerar felvillkoren i det här dokumentet bör du logga felkoden och beskrivningen av de skäl som diskuterats tidigare. Se [referensen Fel och loggning](#error-and-logging-reference) för exempel på loggningskod. 
>

## <a name="acquiretokensilent"></a>FörvärvaTokenSilent

AcquireTokenSilent försöker få en token med garantin att slutanvändaren inte ser ett användargränssnitt (UI). Det finns flera fall där tyst förvärv kan misslyckas och måste hanteras via interaktiva begäranden eller av en standardhanterare. Vi dyker in i detaljerna i när och hur man använder varje fall i de avsnitt som följer.

Det finns en uppsättning fel som genereras av operativsystemet, vilket kan kräva felhantering som är specifika för programmet. Mer information finns i avsnittet ["Operativsystem" i Fel- och loggningsreferens](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Programscenarier

- [Inbyggda klientprogram](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#native-client) (iOS, Android, .NET Desktop eller Xamarin)
- [Webbklientprogram](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#web-client) som anropar en [resurs](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Felfall och åtgärdsbara steg

I grund ochen finns det två fall av AcquireTokenSilent fel:

| Ärende | Beskrivning |
|------|-------------|
| **Fall 1:** Felet kan lösas med en interaktiv inloggning | För fel som orsakas av brist på giltiga token krävs en interaktiv begäran. Cache-sökning och en ogiltig/utgången uppdateringstoken kräver ett AcquireToken-anrop för att matcha.<br><br>I dessa fall måste slutanvändaren uppmanas att logga in. Programmet kan välja att göra en interaktiv begäran omedelbart, efter slutanvändarens interaktion (till exempel trycka på en inloggningsknapp) eller senare. Valet beror på det önskade beteendet för programmet.<br><br>Se koden i följande avsnitt för det här specifika fallet och de fel som diagnostiserar den.|
| **Fall 2**: Felet kan inte lösas med en interaktiv inloggning | För nätverks- och tillfälliga/tillfälliga fel eller andra fel löser inte en interaktiv AcquireToken-begäran problemet genom att utföra en interaktiv AcquireToken-begäran. Onödiga interaktiva inloggningsmeddelanden kan också hindra slutanvändare. ADAL försöker automatiskt ett enda nytt försök för de flesta fel på AcquireTokenSilent-fel.<br><br>Klientprogrammet kan också försöka ett nytt försök vid något senare tillfälle, men när och hur är beroende av programmets beteende och önskad slutanvändarupplevelse. Programmet kan till exempel göra ett AcquireTokenSilent-återförsök efter några minuter eller som svar på någon slutanvändaråtgärd. Ett omedelbart återförsök kommer att resultera i att programmet begränsas och bör inte försökas.<br><br>Ett efterföljande återförsök som misslyckas med samma fel innebär inte att klienten ska göra en interaktiv begäran med AcquireToken, eftersom den inte löser felet.<br><br>Se koden i följande avsnitt för det här specifika fallet och de fel som diagnostiserar den. |

### <a name="net"></a>.NET

Följande vägledning ger exempel på felhantering i samband med ADAL-metoder: 

- acquireTokenSilentAsync(...)
- acquireTokenSilentSync(...) 
- [föråldrad] förvärvaTokenSilent(...)
- [föråldrad] förvärvaTokenByRefreshToken(...) 

Din kod skulle implementeras på följande sätt:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request. 
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

Följande vägledning ger exempel på felhantering i samband med ADAL-metoder: 

- acquireTokenSilentSync(...)
- acquireTokenSilentAsync(...)
- [föråldrad] förvärvaTokenSilent(...)

Din kod skulle implementeras på följande sätt:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

Följande vägledning ger exempel på felhantering i samband med ADAL-metoder: 

- acquireTokenSilentWithResource(...)

Din kod skulle implementeras på följande sätt:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application. 

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>FörvärvaToken

AcquireToken är standardmetoden ADAL som används för att hämta token. I de fall där användaridentitet krävs försöker AcquireToken hämta en token tyst först och visar sedan användargränssnittet om det behövs (såvida inte PromptBehavior.Never skickas). I de fall där programidentitet krävs försöker AcquireToken hämta en token, men visar inte användargränssnittet eftersom det inte finns någon slutanvändare. 

Vid hantering av AcquireToken-fel är felhanteringen beroende av plattformen och scenariot som programmet försöker uppnå. 

Operativsystemet kan också generera en uppsättning fel som kräver felhantering beroende på det specifika programmet. Mer information finns i "Operativsystemfel" i [Fel- och loggningsreferens](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Programscenarier

- Inbyggda klientprogram (iOS, Android, .NET Desktop eller Xamarin)
- Webbprogram som anropar ett resurs-API (.NET)
- Ensidiga program (JavaScript)
- Service-till-tjänst-program (.NET, Java)
  - Alla scenarier, inklusive för
  - För specifika scenarier på uppdrag

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Felfall och åtgärdsbara steg: Inbyggda klientprogram

Om du skapar ett inbyggt klientprogram finns det några felhanteringsärenden att ta hänsyn till som rör nätverksproblem, tillfälliga fel och andra plattformsspecifika fel. I de flesta fall bör ett program inte utföra omedelbara återförsök, utan i stället vänta på slutanvändarens interaktion som uppmanar en inloggning. 

Det finns några särskilda fall där ett enda nytt försök kan lösa problemet. Till exempel när en användare behöver aktivera data på en enhet, eller slutfört Azure AD-mäkleri hämtning efter det första felet. 

I fel fall kan ett program presentera användargränssnittet så att slutanvändaren kan utföra en viss interaktion som uppmanar ett nytt försök. Om enheten till exempel misslyckades för ett offlinefel, en "Försök logga in igen"-knappen som uppmanar en AcquireToken-återförsök i stället för att omedelbart försöka igen felet. 

Felhantering i inbyggda program kan definieras av två fall:

|  |  |
|------|-------------|
| **Fall 1**:<br>Fel som inte kan försökas igen (de flesta fall) | 1. Försök inte omedelbart att försöka igen. Presentera användargränssnittet för slutanvändare baserat på det specifika felet som anropar ett nytt försök (till exempel "Försök logga in igen" eller "Ladda ned Azure AD-mäklarprogram"). |
| **Fall 2:**<br>Fel som kan försökas om | 1. Utföra ett enda nytt försök som slutanvändaren kan ha angett ett tillstånd som resulterar i en framgång.<br><br>2. Om återförsök misslyckas, presentera slutanvändarens användargränssnitt baserat på det specifika felet som anropar ett nytt försök ("Försök att logga in igen", "Ladda ned Azure AD-mäklarappen", etc.). |

> [!IMPORTANT]
> Om ett användarkonto skickas till ADAL i ett tyst samtal och misslyckas, tillåter den efterföljande interaktiva begäran slutanvändaren att logga in med ett annat konto. Efter en lyckad AcquireToken med ett användarkonto måste programmet verifiera den inloggade användaren matchar programmens lokala användarobjekt. En obalans genererar inte ett undantag (utom i mål C), men bör övervägas i de fall där en användare är känd lokalt före autentiseringsbegäranden (som ett misslyckat tyst anrop).
>

#### <a name="net"></a>.NET

Följande vägledning ger exempel på felhantering i samband med alla icke-tysta AcquireToken(...) ADAL-metoder, *utom:* 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(..., ClientCredential, ...)
- AcquireTokenAsync(..., ClientAssertion, ...)
- AcquireTokenAsync(..., UserAssertion,...)   

Din kod skulle implementeras på följande sätt:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> ADAL .NET har en extra faktor eftersom den stöder PromptBehavior.Never, som har beteende som AcquireTokenSilent.
>

Följande vägledning ger exempel på felhantering i samband med ADAL-metoder: 

- acquireToken(..., PromptBehavior.Never)

Din kod skulle implementeras på följande sätt:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

Följande vägledning ger exempel på felhantering i samband med alla icke-tysta AcquireToken(...) ADAL-metoder. 

Din kod skulle implementeras på följande sätt:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

Följande vägledning ger exempel på felhantering i samband med alla icke-tysta AcquireToken(...) ADAL-metoder. 

Din kod skulle implementeras på följande sätt:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Felfall och åtgärdsbara steg: Webbprogram som anropar ett resurs-API (.NET)

Om du skapar en .NET-webbapp som anropar får en token med hjälp av en auktoriseringskod för en resurs, är den enda kod som krävs en standardhanterare för det allmänna skiftet. 

Följande vägledning ger exempel på felhantering i samband med ADAL-metoder: 

- AcquireTokenByAuthorizationCodeAsync(...)

Din kod skulle implementeras på följande sätt:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Felfall och åtgärdsbara steg: Ensidiga program (adal.js)

Om du skapar ett ensidigt program med adal.js med AcquireToken liknar felhanteringskoden för ett vanligt tyst anrop. Specifikt i adal.js visar AcquireToken aldrig ett användargränssnitt. 

En misslyckad AcquireToken har följande fall:

|  |  |
|------|-------------|
| **Fall 1**:<br>Kan lösas med en interaktiv begäran | 1. Om inloggning() misslyckas, utför inte omedelbart återförsök. Försök först igen efter att användaråtgärden uppmanar ett nytt försök.|
| **Fall 2:**<br>Kan inte lösas med en interaktiv begäran. Felet kan försökas igen. | 1. Utföra ett enda nytt försök som slutanvändarens huvud har angett ett tillstånd som resulterar i en framgång.<br><br>2. Om ett nytt försök misslyckas, presentera slutanvändaren en åtgärd som baseras på det specifika felet som kan anropa ett nytt försök ("Försök logga in igen"). |
| **Fall 3:**<br>Kan inte lösas med en interaktiv begäran. Felet kan inte försökas igen. | 1. Försök inte omedelbart att försöka igen. Presentera en åtgärd för slutanvändaren baserat på det specifika felet som kan anropa ett nytt försök ("Försök logga in igen"). |

Din kod skulle implementeras på följande sätt:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Felfall och åtgärdsbara steg: service-till-tjänst-program (endast.NET)

Om du skapar ett service-to-service-program som använder AcquireToken finns det några nyckelfel som koden måste hantera. Den enda som används för att felas är att returnera felet tillbaka till den anropande appen (för för flera ärenden) eller tillämpa en strategi för återförsök. 

#### <a name="all-scenarios"></a>Alla scenarier

För *alla* scenarier för tjänst-till-tjänst-program, inklusive för:

- Försök inte att försöka igen. ADAL försöker ett enda nytt försök för vissa misslyckade begäranden. 
- Fortsätt bara att försöka igen efter att en användare eller appåtgärd uppmanar ett nytt försök. Ett daemonprogram som fungerar på ett visst visst visst intervall bör till exempel vänta tills nästa intervall för att försöka igen.

Följande vägledning ger exempel på felhantering i samband med ADAL-metoder: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- FörvärvaTokenAsync(...,ClientCredential, ...)
- AcquireTokenAsync(...,ClientAssertion, ...)
- AcquireTokenAsync(...,UserAssertion, ...)

Din kod skulle implementeras på följande sätt:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>För scenariernas räkning

För scenarier för service-till-tjänst-program för *service-till-tjänst.For-on-behalf-of* service-to-service application scenarios.

Följande vägledning ger exempel på felhantering i samband med ADAL-metoder: 

- AcquireTokenAsync(..., UserAssertion, ...)

Din kod skulle implementeras på följande sätt:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

Vi har skapat ett [komplett exempel](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) som visar det här scenariot.

## <a name="error-and-logging-reference"></a>Fel- och loggningsreferens

### <a name="logging-personal-identifiable-information--organizational-identifiable-information"></a>Logga personlig identifierbar information & organisatorisk identifierbar information 
Som standard samlar ADAL-loggning inte in eller loggar någon personlig identifierbar information eller organisations identifierbar information. Biblioteket gör det möjligt för apputvecklare att aktivera detta via en setter i klassen Logger. Genom att logga personlig identifierbar information eller organisatorisk identifierbar information tar appen ansvar för att hantera mycket känsliga data på ett säkert sätt och uppfylla eventuella lagstadgade krav.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>ADAL-biblioteksfel

För att utforska specifika ADAL-fel är källkoden i [den azure-activedirectory-library-for-dotnet-databasen](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) den bästa felreferensen.

#### <a name="guidance-for-error-logging-code"></a>Vägledning för felloggningskod

ADAL .NET loggning ändras beroende på vilken plattform som bearbetas. Se [loggningswikin](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) för kod om hur du aktiverar loggning.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>ADAL-biblioteksfel

För att utforska specifika ADAL-fel är källkoden i [den azure-activedirectory-library-for-android-databasen](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) den bästa felreferensen.

#### <a name="operating-system-errors"></a>Operativsystemfel

Android OS-fel exponeras via AuthenticationException i ADAL, kan identifieras som "SERVER_INVALID_REQUEST", och kan vara ytterligare granulat genom felbeskrivningarna. 

En fullständig lista över vanliga fel och vilka åtgärder du bör vidta när appen eller slutanvändarna stöter på dem finns i [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>Vägledning för felloggningskod

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode. 
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// By default, the `Logger` does not capture any PII or OII

// PII or OII will be logged
Logger.getInstance().setEnablePII(true);

// To STOP logging PII or OII, use the following setter
Logger.getInstance().setEnablePII(false);


// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>ADAL-biblioteksfel

För att utforska specifika ADAL-fel är källkoden i [den azure-activedirectory-library-for-objc-databasen](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) den bästa felreferensen.

#### <a name="operating-system-errors"></a>Operativsystemfel

iOS-fel kan uppstå vid inloggning när användare använder webbvyer och vilken typ av autentisering det innebär. Detta kan orsakas av tillstånd som TLS-fel, timeout eller nätverksfel:

- För berättigandedelning är inloggningar inte beständiga och cachen visas tom. Du kan lösa genom att lägga till följande kodrad i nyckelringen:`[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- För NsUrlDomain-uppsättningen fel ändras åtgärden beroende på applogiken. Se [referensdokumentationen NSURLErrorDomain](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) för specifika instanser som kan hanteras.
- Se [ADAL Obj-C Vanliga problem](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) för listan över vanliga fel som upprätthålls av ADAL Objective-C-teamet.

#### <a name="guidance-for-error-logging-code"></a>Vägledning för felloggningskod

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Vägledning för felloggningskod - JavaScript 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```

## <a name="related-content"></a>Relaterat innehåll

* [Azure AD-autentiseringsbibliotek][AAD-Auth-Libraries]
* [Scenarier för Azure AD-autentisering][AAD-Auth-Scenarios]
* [Integrera program med Azure Active Directory][AAD-Integrating-Apps]

Använd avsnittet kommentarer som följer, för att ge feedback och hjälpa oss att förfina och forma vårt innehåll.

[![Visar knappen "Logga in med Microsoft"][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Integrating-Apps]:../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

