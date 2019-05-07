---
title: Initiera klientprogram (Microsoft Authentication Library för JavaScript) | Azure
description: Läs mer om initiering av klientprogram som använder Microsoft Authentication Library för JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7990566ca9cd93e79b8356cfd15fda03a7469695
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138307"
---
# <a name="initialize-client-applications-using-msaljs"></a>Initiera klientprogram som använder MSAL.js
Den här artikeln beskriver initiering av Microsoft Authentication Library för JavaScript (MSAL.js) med en instans av ett användar-agent-program. Användaragent-programmet är en typ av offentliga klientprogram som klientkoden körs i en användaragent till exempel en webbläsare. Dessa klienter lagrar inte hemligheter, eftersom webbläsaren kontexten är öppet tillgängliga. Mer information om klienten programtyper och programmets konfigurationsalternativ i [översikt](msal-client-applications.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du initierar ett program, måste du först [registrera den med Azure-portalen](scenario-spa-app-registration.md) så att din app kan integreras med Microsoft identity-plattformen. Efter registreringen behöver du följande information (som finns i Azure portal):

- Klient-ID (en sträng som representerar ett GUID för ditt program)
- Den URL för identitetsprovider (med namnet instansen) och logga in-målgruppen för ditt program. Dessa två parametrar kallas sammantaget behörighet.
- Klient-ID om du skriver en line-of-business-program endast för din organisation (som också namngivna enda klient program).
- För webbappar måste du också ange redirectUri där identitetsprovidern returnerar i ditt program med säkerhetstoken.

## <a name="initializing-applications"></a>Initiering av program

Du kan använda MSAL.js på följande sätt i en vanlig JavaScript/Typescript-program. Initiera MSAL autentiseringskontext genom att instansiera `UserAgentApplication` med ett konfigurationsobjekt. Minsta nödvändiga konfig att initiera MSAL.js är clientID av programmet som du bör få från på programregistreringsportalen.

För autentiseringsmetoder med omdirigera flöden (`loginRedirect` och `acquireTokenRedirect`), måste du registrera ett återanrop för slutförande- eller felstatus via `handleRedirectCallback()` metod. Detta behövs eftersom omdirigering flöden inte returnerar löften liksom metoder med en popup-upplevelse.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL.js är utformad för att ha en enda instans och konfiguration av den `UserAgentApplication` som representerar en enkel kontext. Flera instanser rekommenderas inte eftersom de orsakar konflikt cacheposter och beteende i webbläsaren.

## <a name="configuration-options"></a>Konfigurationsalternativ

MSAL.js har en konfiguration för objektet som visas nedan som innehåller en gruppering av konfigurerbara alternativ som är tillgängliga för att skapa en instans av `UserAgentApplication`.

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

Nedan visas den fullständiga uppsättningen konfigurerbara alternativ som stöds för närvarande i konfigurationsobjekt:

- **clientID**: Krävs. ClientID av ditt program, du bör få det från på programregistreringsportalen.

- **Utfärdare av**: Valfri. En URL som anger en katalog som MSAL kan begära token från. Standardvärdet är: `https://login.microsoftonline.com/common`.
    * I Azure AD som det är i formatet https://&lt;instans&gt;/&lt;målgrupp&gt;, där &lt;instans&gt; är den identity providern domänen (till exempel `https://login.microsoftonline.com`) och &lt;målgrupp&gt; är en identifierare som representerar målgruppen logga in. Detta kan vara följande värden:
        * `https://login.microsoftonline.com/<tenant>`-klient är en domän som är kopplade till klienten, t.ex contoso.onmicrosoft.com eller GUID som representerar den `TenantID` egenskapen för den katalog som endast används för att logga in användare av en viss organisation.
        * `https://login.microsoftonline.com/common`– Används för att logga in användare med arbets- och skolkonton eller ett personligt Microsoft-konto.
        * `https://login.microsoftonline.com/organizations/`– Används för att logga in användare med arbets-och skolkonton.
        * `https://login.microsoftonline.com/consumers/` – Används för att logga in användare med endast personligt Microsoft-konto (live).
    * I Azure AD B2C, är det med formatet `https://<instance>/tfp/<tenant>/<policyName>/`, där-instansen är Azure AD B2C-domänen, klient är namnet på Azure AD B2C-klient, policyName är namnet på B2C-principen ska tillämpas.


- **validateAuthority**: Valfri.  Verifiera utfärdaren av token. Standardvärdet är `true`. För B2C-program, eftersom utfärdare-värdet är känd och kan vara olika per princip, utfärdare verifieringen fungerar inte och måste anges `false`.

- **redirectUri**: Valfri.  Omdirigerings-URI för din app, där autentiseringssvar kan skickas och tas emot av din app. Det måste exakt matcha en av omdirigerings-URI: er som du registrerade i portalen, förutom att det måste vara URL-kodas. Som standard `window.location.href`.

- **postLogoutRedirectUri**: Valfri.  Omdirigerar användaren till `postLogoutRedirectUri` efter utloggning. Standardvärdet är `redirectUri`.

- **navigateToLoginRequestUrl**: Valfri. Möjlighet att inaktivera standard navigering startsidan efter inloggning. Standardvärdet är true. Det här används endast för omdirigerings-flöden.

- **cacheLocation**: Valfri.  Anger webbläsare storage till antingen `localStorage` eller `sessionStorage`. Standardvärdet är `sessionStorage`.

- **storeAuthStateInCookie**: Valfri.  Den här flaggan introducerades i MSAL.js v0.2.2 som en korrigering av den [loop autentiseringsproblem](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) på Microsoft Internet Explorer och Microsoft Edge. Aktivera flaggan `storeAuthStateInCookie` till SANT för att dra nytta av detta åtgärda. När den är aktiverad, lagrar MSAL.js tillståndet autentisering som krävs för verifiering av auth-flöden i cookies. Den här flaggan anges som standard till `false`.

- **logger**: Valfri.  Ett Logger-objekt med en motringningsinstansen som kan tillhandahållas av utvecklaren att förbruka och publicera loggar på ett anpassat sätt. Mer information om överföringen av loggaren objektet finns [loggning med msal.js](msal-logging.md).

- **loadFrameTimeout**: Valfri.  Antalet millisekunder av inaktivitet innan ett token förnyelse svar från Azure AD bör övervägas tidsgränsen. Standardvärdet är 6 sekunder.

- **tokenRenewalOffsetSeconds**: Valfri. Antalet millisekunder som anger fönstret för förskjutning för att förnya token innan certifikatet går ut. Standardvärdet är 300 millisekunder.

Detta gäller endast för att skickas från MSAL Angular wrapper-biblioteket:
- **unprotectedResources**: Valfri.  Matris med URI: er som är oskyddade resurser. MSAL kommer inte att koppla en token till utgående begäranden som har dessa URI: N. Som standard `null`.

- **protectedResourceMap**: Valfri.  Detta mappning av resurser för att omfattningar som används av MSAL för att automatiskt bifoga åtkomsttoken i web API-anrop. En enda åtkomsttoken hämtas för resursen. Så du kan mappa en specifik resurssökväg på följande sätt: {”https://graph.microsoft.com/v1.0/me”, [”user.read”]}, eller app-URL: en för resursen som: {”https://graph.microsoft.com/”, [”user.read”, ”mail.send”]}. Detta krävs för CORS-anrop. Som standard `null`.
