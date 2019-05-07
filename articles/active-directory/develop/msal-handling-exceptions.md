---
title: Fel och undantag (MSAL) | Azure
description: Lär dig att hantera fel och undantag, villkorlig åtkomst och anspråk utmana i MSAL program.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 43c98181c926410bea2acf64bf1ed4d588c12616
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138974"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Hantering av undantag och fel med MSAL
Undantag i Microsoft Authentication Library (MSAL) är avsedda för apputvecklare att felsöka och inte för att visa för slutanvändare. Undantag meddelanden är inte lokaliserade.

Vid bearbetning av undantag och fel, kan du använda Undantagstypen själva och felkoden för att skilja mellan undantag.  En lista över felkoder finns i [felkoder för autentisering och auktorisering](reference-aadsts-error-codes.md).

## <a name="net-exceptions"></a>.NET-undantag
Vid bearbetning av undantag, du kan använda Undantagstypen själva och `ErrorCode` -medlem för att skilja mellan undantag. Värdena för `ErrorCode` är konstanter av typen [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet#fields).

Du kan också ha en titt på fälten för [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet#fields), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields), [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet#fields).

Om [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) genereras, felet kod kan innehålla en kod som du hittar i [felkoder för autentisering och auktorisering](reference-aadsts-error-codes.md).

### <a name="common-exceptions"></a>Vanliga undantag
Här följer vanliga undantag som kan uppkomma och vissa möjliga åtgärder.

| Undantag | Felkod | Åtgärd|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: Användaren eller administratören har inte godkänt att använda programmet med ID {appId} med namnet {appName}. Skicka en interaktiv auktoriseringsbegäran för den här användaren och resursen.| Du måste hämta användargodkännande första. Om du inte använder .NET Core (som inte har någon Webbgränssnittet) kan anropa (endast en gång) `AcquireTokeninteractive`. Om du använder .NET core eller inte vill göra en `AcquireTokenInteractive`, användaren kan navigera till en URL för att ge ditt medgivande: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read . Att anropa `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: Användaren måste använda multifaktorautentisering.| Det finns ingen minskning - om MFA har konfigurerats för din klient och AAD bestämmer sig för att använda den, måste du återgå till ett flöde för interaktiva som `AcquireTokenInteractive` eller `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields) |AADSTS90010: Beviljandetypen stöds inte över den */vanliga* eller */consumers* slutpunkter. Använd den */organizations* eller klientspecifik slutpunkt. Du använde */vanliga*.| Enligt beskrivningen i meddelandet från Azure AD, utfärdaren måste ha en klient eller på annat sätt */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields) | AADSTS70002: Begärandetexten måste innehålla följande parameter: ' client_secret eller client_assertion'.| Detta kan inträffa om programmet inte har registrerat som en offentlig klient i Azure AD. I Azure-portalen, redigera manifest för ditt program och ange den `allowPublicClient` till `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user meddelande: Det gick inte att identifiera inloggade användaren| Biblioteket kunde inte fråga efter den aktuella Windows inloggade användaren eller den här användaren är inte AD eller AAD anslutna (arbetsplatsen anslutna användare stöds inte). Lösning 1: på UWP, kontrollera att programmet har följande funktioner: Enterprise-autentisering, privat nätverk (klient och Server), Information om användarkonton. Lösning 2: Implementera din egen logik för att hämta användarnamnet (till exempel john@contoso.com) och använda den `AcquireTokenByIntegratedWindowsAuth` formuläret som använder den användarnamnet.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Den här metoden är beroende av ett protokoll som exponeras av Active Directory (AD). Om en användare har skapats i Azure Active Directory utan AD säkerhetskopiering (”hanterad” användare), inte den här metoden. Användare som har skapats i AD och backas upp av AAD (”externa” användare) kan dra nytta av den här icke-interaktiva metoden för autentisering. Lösning: Använda interaktiv autentisering.|

## <a name="javascript-errors"></a>JavaScript-fel

MSAL.js innehåller fel-objekt som abstrakt och klassificerar olika typer av vanliga fel och har ett användargränssnitt för tillgång till viss information om fel, till exempel felmeddelanden för att hantera dem på rätt sätt.

**Fel uppstod när objektet**

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
Genom att utöka klassen error, har du tillgång till följande egenskaper:
* **AuthError.message:** Det här är samma som errorMessage.
* **AuthError.stack:** Stackspårning för utlöses fel. Tillåter spårning till ursprunget tidpunkt av fel.

**Feltyper**

Följande feltyper av är tillgängliga:

* *AuthError:* Grundläggande fel klass för MSAL.js-biblioteket, som också används för oväntade fel.

* *ClientAuthError:* Fel-klass som anger ett problem med klientautentisering. De flesta fel som kommer från biblioteket blir ClientAuthErrors. Dessa kan vara fel, till exempel anropa en inloggningsmetod när inloggningen är pågående, användare avbryter inloggning, osv.

* *ClientConfigurationError:* Fel-klass som utökar ClientAuthError genereras innan begäranden som görs när viss användare config parametrar är felaktig eller saknas.

* *ServerError:* Fel-klass som representerar felsträngar som skickas av autentiseringsservern. Dessa kan vara fel, till exempel ogiltig begäran format eller parametrar eller andra fel som förhindrar servern från autentisering och auktorisering av användaren.

* *InteractionRequiredAuthError:* Fel-klass som utökar ServerError för att representera serverfel som kräver ett interaktiva anrop. Det genereras av `acquireTokenSilent` om användaren måste interagera med servern för att ange autentiseringsuppgifter eller medgivande för autentisering/auktorisering. Error codes include "interaction_required", "login_required", "consent_required".

För felhantering i autentiseringsflöden med omdirigera metoder (`loginRedirect`, `acquireTokenRedirect`), måste du registrera motringningen som kallas eller inte när du har använt den omdirigering `handleRedirectCallback()` metoden på följande sätt:

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

Metoder för popup-upplevelse (`loginPopup`, `acquireTokenPopup`) returnerar löften, så du kan använda det promise mönstret (.och och .catch) för att hantera dem enligt:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Användaren behöver fel

Ett fel returneras när en gränssnittsinteraktioner krävs. Det innebär att du har försökt att använda en icke-interaktiv metod för att skaffa en token (till exempel `acquireTokenSilent`), men MSAL kan inte göra det tyst. Möjliga orsaker är:

* Du måste logga in
* Du måste du godkänna
* Du måste gå igenom en multifaktorautentisering upplevelse.

Det kan göra är att anropa en interaktiv-metod som `acquireTokenPopup` eller `acquireTokenRedirect`:

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

## <a name="conditional-access-and-claims-challenges"></a>Villkorlig åtkomst och anspråk utmaningar
När du hämtar token tyst ditt program kan få felmeddelanden när en [villkorlig åtkomst anspråk utmaning](conditional-access-dev-guide.md) som principen för MFA krävs av ett API du försöker komma åt.

Mönster för att hantera det här felet är att interaktivt hämta en token med MSAL. Interaktivt skaffa en token som uppmanar användaren och ger dem möjlighet att uppfylla principen för villkorlig åtkomst som krävs.

I vissa fall när du anropar en API som kräver villkorlig åtkomst kan få du en utmaning anspråk felet från API: et. För instansen om principen för villkorlig åtkomst är att ha en hanterad enhet (Intune) felet är något som liknar [AADSTS53000: Din enhet krävs för att kunna hanteras för att komma åt den här resursen](reference-aadsts-error-codes.md) eller liknande. I det här fallet kan du skicka anspråken i token anropet hämta så att användaren uppmanas att uppfylla lämplig princip.

### <a name="net"></a>.NET
När du anropar en API som kräver villkorlig åtkomst från MSAL.NET behöver ditt program hantera anspråk utmaning undantag. Detta visas som en [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) där den [anspråk](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) egenskapen inte vara tom.

För att hantera utmaningen för anspråk, kommer du behöva använda den `.WithClaim()` -metoden för den `PublicClientApplicationBuilder` klass.

### <a name="javascript"></a>JavaScript
Vid hämtning av token tyst (med hjälp av `acquireTokenSilent`) genom att använda MSAL.js, ditt program får fel när en [villkorlig åtkomst anspråk utmaning](conditional-access-dev-guide.md) som principen för MFA krävs av ett API du försöker komma åt.

Mönster för att hantera det här felet är att göra ett interaktiva anrop för att hämta token i MSAL.js som `acquireTokenPopup` eller `acquireTokenRedirect` som i följande exempel:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    myMSALObj.acquireTokenPopup(accessTokenRequest).then(
        function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
});
```

Interaktivt skaffa token som uppmanar användaren och ger dem möjlighet att uppfylla principen för villkorlig åtkomst som krävs.

När du anropar en API som kräver villkorlig åtkomst, får du en utmaning för anspråk i fel från API: et. I det här fallet kan du skicka anspråk som returneras av misstag som `extraQueryParameters` i anropet för att hämta token så att användaren uppmanas att uppfylla lämplig princip:

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>Försöker igen efter fel och undantag

### <a name="http-error-codes-500-600"></a>Felkoder i HTTP 500-600
MSAL.NET implementerar en enkel återförsök-när mekanism för fel med HTTP-fel kodar 500 – 600.

### <a name="http-429"></a>HTTP 429
När tjänsten Token Server (STS) är för upptagen på grund av ”för många begäranden”, returneras ett fel HTTP 429 med ett tips om när du kan prova igen (svarsfältet Retry-After) som en fördröjning i sekunder, eller ett datum.

#### <a name="net"></a>.NET
Den [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) undantag ytor `System.Net.Http.Headers.HttpResponseHeaders` som en egenskap `namedHeaders`. Du kan därför utnyttja ytterligare information från felkoden att förbättra tillförlitligheten för dina program. Du kan använda i det fallet vi som beskrivs i `RetryAfterproperty` (av typen `RetryConditionHeaderValue`) och beräkna när att försöka igen.

Här är ett exempel på en daemon-program (med flödet), men du kan anpassa efter att någon av metoderna som en token hämtades.

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
