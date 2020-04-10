---
title: Initiera MSAL.js-klientappar | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om att initiera klientprogram med hjälp av Microsoft Authentication Library for JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: fbd700c787a844fa7538ed198f76ed5c06af2c28
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010162"
---
# <a name="initialize-client-applications-using-msaljs"></a>Initiera klientprogram med MSAL.js
I den här artikeln beskrivs initiering av Microsoft Authentication Library for JavaScript (MSAL.js) med en instans av ett användaragentprogram. Programmet för användaragent är en form av offentligt klientprogram där klientkoden körs i en användaragent, till exempel en webbläsare. Dessa klienter lagrar inte hemligheter, eftersom webbläsarkontexten är öppet tillgänglig. Mer information om klientprogramtyper och programkonfigurationsalternativ finns i [översikten](msal-client-applications.md).

## <a name="prerequisites"></a>Krav
Innan du initierar ett program måste du först [registrera det med Azure-portalen](scenario-spa-app-registration.md) så att din app kan integreras med Microsofts identitetsplattform. Efter registreringen kan du behöva följande information (som finns i Azure-portalen):

- Klient-ID (en sträng som representerar ett GUID för ditt program)
- Identitetsleverantörens URL (namngiven instans) och inloggningsmålgruppen för ditt program. Dessa två parametrar kallas gemensamt myndigheten.
- Klient-ID om du skriver en branschprogram endast för din organisation (även namnet single-tenant application).
- För webbappar måste du också ange redirectUri där identitetsleverantören kommer tillbaka till ditt program med säkerhetstoken.

## <a name="initializing-applications"></a>Initiera program

Du kan använda MSAL.js på följande sätt i ett vanligt JavaScript/Typescript-program. Initiera MSAL-autentiseringskontext `UserAgentApplication` genom att instansiera med ett konfigurationsobjekt. Det minsta som krävs config att initiera MSAL.js är klient-ID för ditt program som du bör få från ansökan registrering portalen.

För autentiseringsmetoder med`loginRedirect` `acquireTokenRedirect`omdirigeringsflöden ( och ), i MSAL.js 1.2.x eller `handleRedirectCallback()` tidigare, måste du uttryckligen registrera en motringning för framgång eller fel via metod. Detta behövs eftersom omdirigeringsflöden inte returnerar löften som metoderna med en popup-upplevelse gör. Detta blev valfritt i MSAL.js version 1.3.0.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
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

MSAL.js är utformad för att ha `UserAgentApplication` en enda instans och konfiguration av att representera en enda autentiseringskontext. Flera instanser rekommenderas inte eftersom de orsakar motstridiga cacheposter och beteende i webbläsaren.

## <a name="configuration-options"></a>Konfigurationsalternativ

MSAL.js har ett konfigurationsobjekt som visas nedan som ger en gruppering av konfigurerbara alternativ som är tillgängliga för att skapa en instans av `UserAgentApplication`.

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
    navigateFrameWait?: number;
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

Nedan visas den totala uppsättningen konfigurerbara alternativ som för närvarande stöds i config-objektet:

- **clientID**: Obligatoriskt. Klient-ID för din ansökan, bör du få detta från ansökan registrering portalen.

- **myndighet**: Valfritt. En URL som anger en katalog som MSAL kan begära token från. Standardvärdet är: `https://login.microsoftonline.com/common`.
    * I Azure AD är det av formuläret&lt;&gt;/&lt;https://&gt; &lt;instansmålgrupp , där&gt; instansen `https://login.microsoftonline.com`är &lt;identitetsproviderdomänen (till exempel) och målgrupp&gt; är en identifierare som representerar inloggningsmålgruppen. Detta kan vara följande värden:
        * `https://login.microsoftonline.com/<tenant>`- Klienten är en domän som är associerad till klienten, till exempel contoso.onmicrosoft.com eller GUID som representerar egenskapen `TenantID` för katalogen som bara används för att logga in användare av en viss organisation.
        * `https://login.microsoftonline.com/common`- Används för att logga in användare med arbets- och skolkonton eller ett personligt Microsoft-konto.
        * `https://login.microsoftonline.com/organizations/`- Används för att logga in användare med arbete och skolkonton.
        * `https://login.microsoftonline.com/consumers/`- Används för att logga in användare med endast personligt Microsoft-konto (live).
    * I Azure AD B2C är `https://<instance>/tfp/<tenant>/<policyName>/`det av formuläret , där instansen är Azure AD B2C-domänen dvs {your-tenant-name}.b2clogin.com, är klienten namnet på Azure AD B2C-klienten, dvs {din-klientnamn}.onmicrosoft.com, policyName är namnet på B2C-principen som ska tillämpas.


- **validateAuthority**: Valfritt.  Validera utfärdaren av token. Standardvärdet är `true`. Eftersom myndighetsvärdet är känt och kan skilja sig åt per princip för B2C-ansökningar `false`fungerar inte myndighetsvalideringen och måste ställas in på .

- **redirectUri**: Valfritt.  Omdirigerings-URI för din app, där autentiseringssvar kan skickas och tas emot av din app. Den måste exakt matcha en av de omdirigerings-URI:er som du registrerade i portalen. Standardvärdet `window.location.href`till .

- **postLogoutRedirectUri**: Valfritt.  Omdirigerar användaren `postLogoutRedirectUri` till när du har loggat ut. Standard är `redirectUri`.

- **navigateToLoginRequestUrl**: Valfritt. Möjlighet att stänga av standardnavigering för att starta sidan efter inloggning. Standardvärdet är true. Detta används endast för omdirigeringsflöden.

- **cacheLocation**: Valfritt.  Ställer in webbläsarlagring på antingen `localStorage` eller `sessionStorage`. Standardvärdet är `sessionStorage`.

- **storeAuthStateInCookie**: Valfritt.  Den här flaggan introducerades i MSAL.js v0.2.2 som en korrigering för problem med [autentiseringsloopen](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) i Microsoft Internet Explorer och Microsoft Edge. Aktivera flaggan `storeAuthStateInCookie` som true för att dra nytta av den här korrigeringsfilen. När detta är aktiverat lagrar MSAL.js det tillstånd för auth-begäran som krävs för validering av auth-flödena i webbläsarcookiesna. Som standard är den `false`här flaggan inställd på .

- **logger**: Valfritt.  Ett Logger-objekt med en motringningsinstans som kan tillhandahållas av utvecklaren för att använda och publicera loggar på ett anpassat sätt. Mer information om hur du skickar loggerobjekt finns i [loggning med msal.js](msal-logging.md).

- **loadFrameTimeout**: Valfritt.  Antalet millisekunder av inaktivitet innan ett tokenförnyelsesvar från Azure AD bör betraktas som tidsbestämt. Standard är 6 sekunder.

- **tokenRenewalOffsetSeconds**: Valfritt. Antalet millisekunder som anger det förskjutningsfönster som behövs för att förnya token före utgången. Standard är 300 millisekunder.

- **navigateFrameWait**: Valfritt. Antalet millisekunder som anger väntetiden innan dolda iframes navigerar till sin destination. Standard är 500 millisekunder.

Dessa gäller endast för vidaregång från MSAL-inslagets bibliotek:
- **unprotectedResources**: Valfritt.  Matris med URI:er som är oskyddade resurser. MSAL kommer inte att bifoga en token till utgående begäranden som har dessa URI. Standardvärdet `null`till .

- **protectedResourceMap**: Valfritt.  Detta är mappning av resurser till scope som används av MSAL för att automatiskt koppla åtkomsttoken i webb-API-anrop. En enda åtkomsttoken hämtas för resursen. Så du kan mappa en specifik resurssökväg enligt följande: {"https://graph.microsoft.com/v1.0/me", ["user.read"]}, eller resursens app-URL som: {"https://graph.microsoft.com/", ["user.read", "mail.send"]}. Detta krävs för CORS-samtal. Standardvärdet `null`till .
