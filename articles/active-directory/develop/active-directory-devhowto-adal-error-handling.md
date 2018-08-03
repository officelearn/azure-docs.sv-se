---
title: Metodtips för felhantering för Azure Active Directory Authentication Library (ADAL) klienter
description: Ger vägledning och bästa praxis för ADAL-klientprogram för felhantering.
services: active-directory
documentationcenter: ''
author: danieldobalian
manager: mtillman
ms.author: celested
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.custom: ''
ms.openlocfilehash: 6f3075884131415efa62851b6e2db43bc00b39b8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448330"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Metodtips för felhantering för Azure Active Directory Authentication Library (ADAL) klienter

Den här artikeln innehåller råd om vilken typ av fel att utvecklare kan uppstå när du använder ADAL för att autentisera användare. När du använder ADAL, finns det flera fall där utvecklare kan behöva gå in och hantera fel. Rätt felhantering säkerställer en fantastisk slutanvändarupplevelse och begränsar antalet gånger som användaren måste logga in.

I den här artikeln förklarar vi specialfall för varje plattform som stöds av ADAL och hur ditt program kan hantera varje fall korrekt. Fel-vägledningen är uppdelat i två bredare kategorier, baserat på de tokenförvärv mönster som anges av ADAL API: er:

- **AcquireTokenSilent**: klienten försöker hämta en token tyst (inget användargränssnitt) och kan misslyckas om ADAL misslyckas. 
- **AcquireToken**: klienten försöka tyst förvärv, men kan också utföra interaktiva begäranden som kräver inloggning.

> [!TIP]
> Det är en bra idé att logga alla fel och undantag när du använder ADAL och Azure AD. Endast är inte användbart för att förstå den övergripande hälsan för ditt program, men är också viktigt när du felsöker problem med bredare. När ditt program kan återställa från vissa fel, kan de tipset på bredare designproblem som kräver ändringar i koden för att lösa. 
> 
> Implementera felvillkor omfattas i det här dokumentet, ska du logga felkod och beskrivning av skäl som beskrivs ovan. Se den [fel och loggning referens](#error-and-logging-reference) exempel loggning kod. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent försöker hämta en token med garanti att användaren inte se en User Interface (UI). Det finns flera fall där tyst förvärv kan misslyckas och måste hanteras via interaktiva begäranden eller av en standardhanterare som. Vi gör en djupdykning i detaljerna i när och hur du använder varje fall i avsnitten som följer.

Det finns en uppsättning med fel som genereras av operativsystemet, vilket kan kräva felhantering specifika för programmet. Mer information finns i avsnittet ”operativsystem” fel i [fel och loggning referens](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Programscenarier

- [Inbyggd klient](active-directory-dev-glossary.md#native-client) program (iOS, Android, .NET Desktop eller Xamarin)
- [Webbklienten](active-directory-dev-glossary.md#web-client) program som anropar en [resource](active-directory-dev-glossary.md#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Fel och lämpliga åtgärder

Grunden, det finns två fall av AcquireTokenSilent fel:

| Ärende | Beskrivning |
|------|-------------|
| **Fall 1**: fel kan matchas med en interaktiv inloggning | En interaktiv förfrågan är nödvändigt för fel som orsakats av brist på giltig token. Mer specifikt kräver cache-sökning och en ogiltig/utgången uppdateringstoken ett AcquireToken-anrop för att lösa.<br><br>I dessa fall måste slutanvändaren uppmanas att logga in. Programmet kan du göra en interaktiv förfrågan omedelbart efter användarinteraktion (till exempel trycka en knapp för inloggning) eller senare. Valet beror på önskat beteende för programmet.<br><br>Visa koden i följande avsnitt för den här specifika fall och de fel som diagnostiserar problemet.|
| **Fall 2**: fel kan inte matchas med en interaktiv inloggning | För nätverk och tillfälligt/tillfälliga fel eller andra fel löser utför en interaktiv AcquireToken-begäran inte problemet. Onödiga anvisningarna för interaktiv inloggning kan också vara frustrerande för användarna. ADAL försöker automatiskt en enda återförsök för de flesta fel på AcquireTokenSilent fel.<br><br>Klientprogrammet kan också göras ett nytt försök vid en senare tidpunkt, men när och hur du gör det är beroende av programmets beteende och önskad slutanvändarens upplevelse. Programmet kan till exempel göra en AcquireTokenSilent försök igen efter några minuter eller som svar på en slutanvändarens-åtgärd. Ett omedelbart återförsök resulterar i programmet begränsas och bör inte göras.<br><br>En efterföljande återförsök med samma fel innebär inte klienten bör göra en interaktiv förfrågan med AcquireToken, eftersom den inte löser felet.<br><br>Visa koden i följande avsnitt för den här specifika fall och de fel som diagnostiserar problemet. |

### <a name="net"></a>.NET

Vägledningen innehåller exempel för felhantering tillsammans med ADAL-metoder: 

- acquireTokenSilentAsync(...)
- acquireTokenSilentSync(...) 
- [inaktuell] acquireTokenSilent(...)
- [inaktuell] acquireTokenByRefreshToken(...) 

Koden skulle kunna implementeras på följande sätt:

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

Vägledningen innehåller exempel för felhantering tillsammans med ADAL-metoder: 

- acquireTokenSilentSync(...)
- acquireTokenSilentAsync(...)
- [inaktuell] acquireTokenSilent(...)

Koden skulle kunna implementeras på följande sätt:

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

Vägledningen innehåller exempel för felhantering tillsammans med ADAL-metoder: 

- acquireTokenSilentWithResource(...)

Koden skulle kunna implementeras på följande sätt:

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

## <a name="acquiretoken"></a>AcquireToken

AcquireToken är ADAL standardmetoden används för att hämta token. I fall där användaridentitet krävs AcquireToken försöker hämta en token tyst första, så visar Användargränssnittet om det behövs (om inte PromptBehavior.Never skickas). I fall där Programidentitet krävs AcquireToken försöker hämta en token, men visar inte Användargränssnittet eftersom det finns inga slutanvändaren. 

Vid hantering av AcquireToken fel felhantering är beroende på plattformen och scenario programmet försöker uppnå. 

Operativsystemet kan också skapa en uppsättning med fel som kräver felhantering, beroende på det specifika programmet. Mer information finns i ”fel i operativsystemet” i [fel och loggning referens](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Programscenarier

- Interna klientprogram (iOS, Android, .NET Desktop eller Xamarin)
- Webbprogram som anropar en resurs API (.NET)
- En sida-program (JavaScript)
- Tjänst-till-tjänst-program (.NET, Java)
  - Alla scenarier, inklusive on-behalf-of
  - On-Behalf-of specifika scenarier

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Fel och lämpliga åtgärder: interna klientprogram

Om du bygger ett internt klientprogram, finns det några fel vid hantering av fall att tänka på som är relaterade till nätverksproblem, tillfälliga fel och andra plattformsspecifika fel. I de flesta fall inte bör ett program utför omedelbart återförsök men i stället väntar användarinteraktion som begär en inloggning. 

Det finns några särskilda fall där en enda återförsök kan lösa problemet. Till exempel när en användare måste aktivera data på en enhet eller slutföra Azure AD-broker hämta efter det första felet. 

I fall där inte kan ett program uppvisa gränssnitt som gör att slutanvändaren kan utföra vissa interaktion som begär ett nytt försök. Om enheten misslyckades för ett offline fel, försök till exempel en ”försök att logga in igen”-knapp som uppmanar en AcquireToken i stället för att omedelbart återförsök felet. 

Felhantering i interna program kan definieras av två fall:

|  |  |
|------|-------------|
| **Fall 1**:<br>Icke-Återförsöksbar fel (de flesta fall) | 1. Försök inte omedelbart återförsök. Presentera slutanvändaren Användargränssnittet baserat på det specifika felet som anropar ett nytt försök (”försök att logga in igen”, ”ladda ned Azure AD broker-program”, osv). |
| **Fall 2**:<br>Återförsökbart fel | 1. Utföra en enda återförsök eftersom användaren har angett ett tillstånd som resulterar i en succé.<br><br>2. Om återförsöket misslyckas närvarande slutanvändaren Användargränssnittet baserat på det specifika felet som anropar ett nytt försök (”försök att logga in igen”, ”ladda ned Azure AD-koordinatorappen” osv.). |

> [!IMPORTANT]
> Om ett användarkonto har överförts till ADAL i en tyst anrop och misslyckas, kan den efterföljande interaktiva begäran att slutanvändaren kan logga in med ett annat konto. Efter en lyckad AcquireToken med ett användarkonto, måste programmet verifiera inloggade användaren matchar de programmen lokala användarobjektet. Ett matchningsfel inte genererar ett undantag (förutom i Objective C), men bör övervägas i fall där en användare är känt lokalt innan autentiseringsbegäranden (t.ex. en tyst misslyckade anrop).
>

#### <a name="net"></a>.NET

Följande riktlinjer visar exempel för felhantering i tillsammans med alla icke-tysta AcquireToken(...) ADAL-metoder, *utom*: 

- AcquireTokenAsync (…, IClientAssertionCertification,...)
- AcquireTokenAsync (…, ClientCredential,...)
- AcquireTokenAsync (…, ClientAssertion,...)
- AcquireTokenAsync(...,UserAssertion,...)   

Koden skulle kunna implementeras på följande sätt:

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
> ADAL .NET har en extra beräkningen som den stöder PromptBehavior.Never dit beteenden som AcquireTokenSilent.
>

Vägledningen innehåller exempel för felhantering tillsammans med ADAL-metoder: 

- acquireToken(..., PromptBehavior.Never)

Koden skulle kunna implementeras på följande sätt:

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

Följande riktlinjer visar exempel för felhantering i tillsammans med alla icke-tysta AcquireToken(...) ADAL-metoder. 

Koden skulle kunna implementeras på följande sätt:

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

Följande riktlinjer visar exempel för felhantering i tillsammans med alla icke-tysta AcquireToken(...) ADAL-metoder. 

Koden skulle kunna implementeras på följande sätt:

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

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Fel och lämpliga åtgärder: webbprogram som anropar en resurs API (.NET)

Om du skapar en .NET-webbapp som anropar hämtar en token med hjälp av en auktoriseringskod för en resurs, den enda kod som krävs är en standardhanterare för allmän. 

Vägledningen innehåller exempel för felhantering tillsammans med ADAL-metoder: 

- AcquireTokenByAuthorizationCodeAsync(...)

Koden skulle kunna implementeras på följande sätt:

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

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Fel och lämpliga åtgärder: den enda sidan program (adal.js)

Om du bygger en ensidesapp med AcquireToken adal.js är koden för felhantering liknande den i en typisk tyst anrop. I synnerhet adal.js visar AcquireToken aldrig ett gränssnitt. 

En misslyckad AcquireToken har följande fall:

|  |  |
|------|-------------|
| **Fall 1**:<br>Matchas med en interaktiv förfrågan | 1. Om det inte går att login(), utför inte omedelbart återförsök. Gör endast när användaråtgärd frågar ett nytt försök.|
| **Fall 2**:<br>Inte Resolvable med en interaktiv förfrågan. Fel är återförsöksbar. | 1. Ett enda nytt försök att utföra eftersom större användaren har angett ett tillstånd som resulterar i en succé.<br><br>2. Om återförsöket misslyckas närvarande användaren med en åtgärd baserat på vilket fel som kan anropa ett nytt försök (”försök logga in igen”). |
| **Fall 3**:<br>Inte Resolvable med en interaktiv förfrågan. Fel är inte återförsöksbar. | 1. Försök inte omedelbart återförsök. Ge användaren en åtgärd baserat på vilket fel som kan anropa ett nytt försök (”försök logga in igen”). |

Koden skulle kunna implementeras på följande sätt:

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

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Fel och lämpliga åtgärder: tjänst-till-tjänst-program (.NET)

Om du bygger en tjänst-till-tjänst-program som använder AcquireToken finns några nyckelfel som din kod måste hantera. Den enda lösningen att felet är att returnera felet tillbaka till anropande appen (för on-behalf-of-fall) eller använda en strategi för återförsök. 

#### <a name="all-scenarios"></a>Alla scenarier

För *alla* tjänst-till-tjänst Programscenarier, bland annat on-behalf-of:

- Försök inte ett omedelbart återförsök. ADAL försök som en enda försök för vissa misslyckade förfrågningar. 
- Endast fortsätta försöker igen när en användare eller app-åtgärd är anvisningarna för ett nytt försök. Till exempel ska en daemon-program som fungerar med vissa set-intervall vänta tills nästa intervall och försök igen.

Vägledningen innehåller exempel för felhantering tillsammans med ADAL-metoder: 

- AcquireTokenAsync (…, IClientAssertionCertification,...)
- AcquireTokenAsync (…, ClientCredential,...)
- AcquireTokenAsync (…, ClientAssertion,...)
- AcquireTokenAsync (…, UserAssertion,...)

Koden skulle kunna implementeras på följande sätt:

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

#### <a name="on-behalf-of-scenarios"></a>On-behalf-of-scenarier

För *on-behalf-of* Programscenarier med tjänst-till-tjänst.

Vägledningen innehåller exempel för felhantering tillsammans med ADAL-metoder: 

- AcquireTokenAsync (…, UserAssertion,...)

Koden skulle kunna implementeras på följande sätt:

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

Vi har skapat en [komplett exempel](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) som visar det här scenariot.

## <a name="error-and-logging-reference"></a>Referens för fel och loggning

### <a name="logging-personal-identifiable-information-pii--organizational-identifiable-information-oii"></a>Loggning av personligt identifierbar Information (PII) & organisationens identifierbar Information (OII)
Som standard ADAL loggning inte samla in och logga all personligt Identifierbar eller OII. Biblioteket kan apputvecklare aktivera detta via en set-metod i klassen loggaren. Genom att aktivera personligt Identifierbar eller OII ansvarar appen för att på ett säkert sätt hantera mycket känslig data och som uppfyller alla efterlevnadskrav.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>ADAL-biblioteket fel

Utforska specifika ADAL-fel källkoden i den [azure-activedirectory-biblioteket-för-dotnet-lagringsplatsen](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) är den bästa referensen till fel.

#### <a name="guidance-for-error-logging-code"></a>Vägledning för felkoden för loggning

ADAL .NET loggning ändras beroende på vilken plattform som du arbetar på. Referera till den [loggning wiki](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) för kod om hur du aktiverar loggning.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>ADAL-biblioteket fel

Utforska specifika ADAL-fel källkoden i den [lagringsplatsen för azure-activedirectory-biblioteket-för-android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) är den bästa referensen till fel.

#### <a name="operating-system-errors"></a>Fel i operativsystemet

Android OS-fel är tillgängliga via AuthenticationException i ADAL, kan identifieras som ”SERVER_INVALID_REQUEST” och kan vara mer detaljerade via felbeskrivningar. 

En fullständig lista över vanliga fel och vilka steg som ska vidtas när din app eller användare stöter på dem, finns det [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>Vägledning för felkoden för loggning

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

#### <a name="adal-library-errors"></a>ADAL-biblioteket fel

Utforska specifika ADAL-fel källkoden i den [lagringsplatsen för azure-activedirectory-biblioteket-för-objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) är den bästa referensen till fel.

#### <a name="operating-system-errors"></a>Fel i operativsystemet

iOS-fel kan uppstå under inloggning när användarna använder Webbvyer och typen av autentisering. Detta kan bero på villkor som SSL-fel, timeout-fel eller nätverksfel:

- För delning av rätt inloggningar är inte beständiga och cachen visas tom. Du kan lösa genom att lägga till följande kodrad i nyckelringen: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Åtgärden ändras beroende på applogiken för NsUrlDomain-uppsättningen med fel. Se den [NSURLErrorDomain referensdokumentation](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) för specifika instanser som kan hanteras.
- Se [ADAL Obj-C vanliga problem](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) lista över vanliga fel som underhålls av ADAL Objective-C-teamet.

#### <a name="guidance-for-error-logging-code"></a>Vägledning för felkoden för loggning

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

### <a name="guidance-for-error-logging-code---javascript"></a>Vägledning för felloggning kod – JavaScript 

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

* [Azure AD Developer's Guide] [AAD-Dev-Guide]
* [Azure AD-Autentiseringsbibliotek] [AAD-Auth-bibliotek]
* [Azure AD-Autentiseringsscenarier] [AAD-Auth-scenarier]
* [Integrera program med Azure Active Directory] [AAD-integrering-Apps]

Använd kommentarsavsnittet nedan om du vill ge feedback och hjälp oss att förfina och forma vårt innehåll.

[![Logga in knappen][AAD-Sign-In]] [ AAD-Sign-In] 
 <!--Reference style links --> [AAD-Auth-bibliotek]:./active-directory-authentication-libraries.md [AAD-Auth-scenarier]: [./active-directory-authentication-scenarios.md AAD-Dev-Guide]:azure-ad-developers-guide.md [AAD-integrering-Apps]:./active-directory-integrating-applications.md [AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

