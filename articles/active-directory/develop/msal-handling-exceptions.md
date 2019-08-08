---
title: Fel och undantag (MSAL) | Azure
description: Lär dig hur du hanterar fel och undantag, villkorlig åtkomst och anspråk i MSAL-program.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c294e3bd8ac04454c2d715c665e0da4f9a4f4535
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835035"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Hantera undantag och fel med MSAL
Undantag i Microsoft Authentication Library (MSAL) är avsedda för att utvecklare av appar ska kunna felsöka och inte Visa för slutanvändare. Undantags meddelanden är inte lokaliserade.

När du bearbetar undantag och fel kan du använda själva undantags typen och felkoden för att skilja mellan undantag.  En lista över felkoder finns i [fel koder för autentisering och auktorisering](reference-aadsts-error-codes.md).

## <a name="net-exceptions"></a>.NET-undantag
När du bearbetar undantag kan du använda själva undantags typen och `ErrorCode` medlemmen för att skilja mellan undantag. Värdena för `ErrorCode` är konstanter av typen [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Du kan också ha en titt på fälten [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Om [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) genereras kan fel koden innehålla en kod som du hittar i [fel koder för autentisering och auktorisering](reference-aadsts-error-codes.md).

### <a name="common-exceptions"></a>Vanliga undantag
Här följer de vanliga undantag som kan uppstå och vissa eventuella åtgärder.

| Undantag | Felkod | Åtgärd|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: Användaren eller administratören har inte samtyckt till att använda programmet med ID: t {appId} med namnet {appName}. Skicka en interaktiv auktoriseringsbegäran för den här användaren och resursen.| Du måste först få användar medgivande. Om du inte använder .NET Core (som inte har något webb gränssnitt) anropar du (bara en gång) `AcquireTokeninteractive`. Om du använder .net Core eller inte vill göra något `AcquireTokenInteractive`kan användaren navigera till en URL för att ge medgivande:. https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read För att `AcquireTokenInteractive`anropa:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: Användaren måste använda Multi-Factor Authentication.| Ingen minskning, om MFA har kon figurer ATS för din klient och AAD bestämmer sig för att genomdriva det, måste du återgå till ett interaktivt `AcquireTokenInteractive` flöde `AcquireTokenByDeviceCode`, till exempel eller.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: Anslags typen stöds inte över */vanliga* -eller */consumers* -slutpunkterna. Använd den */organizations* eller klient-/regionsspecifika slut punkten. Du använde */vanliga*.| Som förklaras i meddelandet från Azure AD måste utfärdaren ha en klient eller på annat */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: Begär ande texten måste innehålla följande parameter: ' client_secret eller client_assertion '.| Detta kan inträffa om programmet inte har registrerats som ett offentligt klient program i Azure AD. Redigera manifestet för ditt program i Azure Portal och ange `allowPublicClient` till. `true` |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user-meddelande: Det gick inte att identifiera inloggad användare| Det gick inte att fråga den aktuella Windows-inloggade användaren eller så är den här användaren inte AD eller AAD-ansluten (arbets plats anslutna användare stöds inte). Minskning 1: på UWP kontrollerar du att programmet har följande funktioner: Enterprise-autentisering, privata nätverk (klient och Server), användar konto information. Minskning 2: Implementera din egen logik för att hämta användar namnet (till exempel john@contoso.com) och `AcquireTokenByIntegratedWindowsAuth` Använd formuläret som tar med användar namnet.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Den här metoden förlitar sig på ett protokoll som exponeras av Active Directory (AD). Om en användare skapades i Azure Active Directory utan AD-återställning ("hanterad" användare) kommer den här metoden att Miss förväntas. Användare som skapats i AD och som backas upp av AAD ("federerade" användare) kan dra nytta av den här icke-interaktiva autentiseringsmetoden. Minskning Använd interaktiv autentisering.|

## <a name="javascript-errors"></a>JavaScript-fel

MSAL. js tillhandahåller fel objekt som sammanfattar och klassificerar olika typer av vanliga fel och har ett gränssnitt för att få åtkomst till information om felen, till exempel fel meddelanden, för att hantera dem på rätt sätt.

**Fel objekt**

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
* **AuthError. meddelande:** Detta är samma som errorMessage.
* **AuthError. stack:** Stack spårning för genererade fel. Tillåter spårning till ursprungs punkten för fel.

**Fel typer**

Följande fel typer är tillgängliga:

* *AuthError:* Bas Fels klass för biblioteket MSAL. js, som också används för oväntade fel.

* *ClientAuthError:* Fel klass som anger ett problem med klientautentisering. De flesta fel som kommer från biblioteket kommer att bli ClientAuthErrors. Det kan vara fel som att anropa en inloggnings metod när inloggningen pågår, användare som avbryter inloggningen osv.

* *ClientConfigurationError:* Fel klass som utökar ClientAuthError utlöses innan begär Anden görs när de tilldelade parametrarna för användar konfiguration är felaktiga eller saknas.

* *ServerError* Felklass för att representera de fel strängar som skickas av autentiseringsservern. Detta kan vara fel som ogiltiga format eller parametrar för begäran, eller andra fel som hindrar servern från att autentisera eller auktorisera användaren.

* *InteractionRequiredAuthError:* Felklass utökar ServerError för att representera Server fel som kräver ett interaktivt anrop. Detta utlöses av `acquireTokenSilent` om användaren måste interagera med servern för att ange autentiseringsuppgifter eller tillstånd för autentisering/auktorisering. Felkoderna är "interaction_required", "login_required", "consent_required".

För fel hantering i autentiserings flöden med omdirigerings `acquireTokenRedirect`metoder (`loginRedirect`,) måste du registrera återanropet som anropas med lyckat eller misslyckat efter `handleRedirectCallback()` omdirigeringen med hjälp av metoden enligt följande:

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

Metoderna för att se pop-up (`loginPopup`, `acquireTokenPopup`) returnera löfte så att du kan använda löftes mönstret (. och. Catch) för att hantera dem som de visas:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Åtgärd krävs fel

Ett fel returneras när en UI-interaktion krävs. Det innebär att du har försökt använda en icke-interaktiv metod för att förvärva en token (till exempel `acquireTokenSilent`), men MSAL kunde inte göra det tyst. Möjliga orsaker är:

* Du måste logga in
* Du måste godkänna
* Du måste gå igenom en Multi-Factor Authentication-upplevelse.

Reparationen är att anropa en interaktiv metod som `acquireTokenPopup` eller: `acquireTokenRedirect`

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
När token hämtas i bakgrunden kan ditt program få fel när en [utmanings utmaning för anspråk](conditional-access-dev-guide.md) , till exempel MFA-princip krävs av ett API som du försöker få åtkomst till.

Mönstret för att hantera det här felet är att interaktivt hämta en token med hjälp av MSAL. Att interaktivt förvärva en token frågar användaren och ger dem möjlighet att uppfylla den nödvändiga principen för villkorlig åtkomst.

I vissa fall när du anropar ett API som kräver villkorlig åtkomst kan du få en anspråks utmaning i fel meddelandet från API: et. Om principen för villkorlig åtkomst till exempel är att ha en hanterad enhet (Intune), kommer felet att vara [något som AADSTS53000: Enheten måste hanteras för att få åtkomst till den här resursen](reference-aadsts-error-codes.md) eller något liknande. I det här fallet kan du skicka anspråken i det Hämta token-anropet så att användaren uppmanas att uppfylla den aktuella principen.

### <a name="net"></a>.NET
När du anropar ett API som kräver villkorlig åtkomst från MSAL.NET måste ditt program hantera anspråks utmanings undantag. Detta visas som en [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) där anspråks egenskapen inte är tom. [](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet)

För att hantera anspråks utmaningen måste du använda `.WithClaim()` metoden `PublicClientApplicationBuilder` i klassen.

### <a name="javascript"></a>JavaScript
När du hämtar token tyst (med `acquireTokenSilent`hjälp av) med hjälp av MSAL. js kan programmet få fel meddelanden när en [utmanings utmaning för anspråk](conditional-access-dev-guide.md) som MFA-princip krävs av ett API som du försöker få åtkomst till.

Mönstret för att hantera det här felet är att göra ett interaktivt anrop för att hämta token i `acquireTokenPopup` MSAL `acquireTokenRedirect` . js som eller som i följande exempel:

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

Att interaktivt förvärva token frågar användaren och ger dem möjlighet att uppfylla den nödvändiga principen för villkorlig åtkomst.

När du anropar ett API som kräver villkorlig åtkomst kan du få en anspråks utmaning i fel meddelandet från API: et. I det här fallet kan du skicka de anspråk som returneras i felet, `extraQueryParameters` som i anropet att hämta tokens så att användaren uppmanas att uppfylla lämplig princip:

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>Försöker igen efter fel och undantag

### <a name="http-error-codes-500-600"></a>HTTP-felkoder 500-600
MSAL.NET implementerar en enkel återförsöks funktion för fel med HTTP-felkoderna 500-600.

### <a name="http-429"></a>HTTP 429
När service token-servern (STS) är för upptagen på grund av "för många begär Anden", returnerar den ett HTTP-fel 429 med ett tips om när du kan försöka igen (fältet återförsök efter svar) som en fördröjning i sekunder eller ett datum.

#### <a name="net"></a>.NET
[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) undantags ytan `System.Net.Http.Headers.HttpResponseHeaders` som en egenskap `namedHeaders`. Du kan därför använda ytterligare information från felkoden för att förbättra tillförlitligheten för dina program. I det fall vi precis beskrivet kan du använda `RetryAfterproperty` (av typen `RetryConditionHeaderValue`) och beräkna för att försöka igen.

Här är ett exempel på ett daemon-program (med hjälp av flödet för klientautentiseringsuppgifter), men du kan anpassa det till någon av metoderna som hämtar en token.

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
