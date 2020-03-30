---
title: Enkel inloggning (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om hur du skapar enstaka inloggningsupplevelser med hjälp av Microsoft Authentication Library for JavaScript (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262859"
---
# <a name="single-sign-on-with-msaljs"></a>Enkel inloggning med MSAL.js

Enkel inloggning (SSO) gör det möjligt för användare att ange sina autentiseringsuppgifter en gång för att logga in och upprätta en session som kan återanvändas över flera program utan att behöva autentisera igen. Detta ger en sömlös upplevelse för användaren och minskar upprepade uppmaningar om autentiseringsuppgifter.

Azure AD tillhandahåller SSO-funktioner för program genom att ange en sessionscookie när användaren autentiserar första gången. MSAL.js-biblioteket gör det möjligt för program att utnyttja detta på några sätt.

## <a name="sso-between-browser-tabs"></a>SSO mellan webbläsarflikar

När programmet är öppet på flera flikar och du först loggar in användaren på en flik loggas användaren också in på de andra flikarna utan att bli tillfrågad. MSAL.js cachelagrar ID-token för `localStorage` användaren i webbläsaren och loggar in användaren på programmet på de andra öppna flikarna.

Som standard använder `sessionStorage` MSAL.js som inte tillåter att sessionen delas mellan flikar. För att få SSO mellan flikar, `cacheLocation` se till att `localStorage` ställa in i MSAL.js till som visas nedan.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>SSO mellan appar

När en användare autentiserar ställs en sessionscookie in på Azure AD-domänen i webbläsaren. MSAL.js förlitar sig på den här sessionscookien för att tillhandahålla SSO för användaren mellan olika program. MSAL.js cachelagrar även ID-token och åtkomsttoken för användaren i webbläsarlagringen per programdomän. Därför varierar SSO-beteendet för olika fall:  

### <a name="applications-on-the-same-domain"></a>Program på samma domän

När program finns på samma domän kan användaren logga in på en app en gång och sedan autentiseras till de andra apparna utan en uppmaning. MSAL.js utnyttjar token som cachelagrats för användaren på domänen för att tillhandahålla SSO.

### <a name="applications-on-different-domain"></a>Program på olika domäner

När program finns på olika domäner kan token som cachelagras på domän A inte nås av MSAL.js i domän B.

Det innebär att när användare som är inloggade på domän A navigerar till ett program på domän B omdirigeras eller uppmanas de att göra azure AD-sidan. Eftersom Azure AD fortfarande har användarsessionscookien loggar den in användaren och de behöver inte ange autentiseringsuppgifterna igen. Om användaren har flera användarkonton i session med Azure AD uppmanas användaren att välja det relevanta kontot att logga in med.

### <a name="automatically-select-account-on-azure-ad"></a>Välj konto automatiskt på Azure AD

I vissa fall har programmet åtkomst till användarens autentiseringskontext och vill undvika azure AD-kontovalsprompten när flera konton är inloggade.  Detta kan göras på några olika sätt:

**Använda sessions-ID (SID)**

Sessions-ID är ett [valfritt anspråk](active-directory-optional-claims.md) som kan konfigureras i ID-token. Med det här anspråket kan programmet identifiera användarens Azure AD-session oberoende av användarens kontonamn eller användarnamn. Du kan skicka SID i parametrarna för begäran till samtalet. `acquireTokenSilent` Detta gör det möjligt för Azure AD att kringgå kontovalet. SID är bundet till sessionscookien och kommer inte att korsa webbläsarkontexter.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> SID kan endast användas med tysta `acquireTokenSilent` autentiseringsbegäranden som görs av anrop i MSAL.js.
Du hittar stegen för att konfigurera valfria anspråk i programmanifestet [här](active-directory-optional-claims.md).

**Använda inloggningstips**

Om du inte har sid-anspråk konfigurerat eller behöver kringgå kontovalsprompten `login_hint` i interaktiva autentiseringsanrop, kan du göra det genom att ange en i parametrarna för begäran `domain_hint` och eventuellt en som `extraQueryParameters` i de interaktiva metoderna FÖR MSAL.js (`loginPopup`, `loginRedirect` `acquireTokenPopup` och `acquireTokenRedirect`). Ett exempel:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Du kan hämta värdena för login_hint och domain_hint genom att läsa anspråken som returneras i ID-token för användaren.

* **loginHint** ska ställas `preferred_username` in på anspråket i ID-token.

* **domain_hint** behöver endast skickas när den /common-myndigheten används. Domäntipset bestäms av klient-ID(tid).  Om `tid` anspråket i ID-token är `9188040d-6c67-4c5b-b112-36a304b66dad` det konsumenter. Annars är det organisationer.

Läs [här](v2-oauth2-implicit-grant-flow.md) för mer information om värden för inloggningstips och domäntips.

> [!Note]
> Du kan inte passera SID och login_hint samtidigt. Detta kommer att resultera i felsvar.

## <a name="sso-without-msaljs-login"></a>SSO utan MSAL.js-inloggning

Msal.js kräver avsiktligt att en inloggningsmetod anropas för att upprätta en användarkontext innan token hämtas för API:er. Eftersom inloggningsmetoder är interaktiva ser användaren en uppmaning.

Det finns vissa fall där program har åtkomst till den autentiserade användarens kontext eller ID-token genom autentisering som initierats i ett annat program och vill utnyttja SSO för att hämta token utan att först logga in via MSAL.js.

Ett exempel på detta är: En användare är inloggad i ett överordnat webbprogram som är värd för ett annat JavaScript-program som körs som ett tillägg eller plugin.

SSO-erfarenheten i det här scenariot kan uppnås på följande sätt:

Skicka `sid` om tillgängligt `login_hint` (eller `domain_hint`eventuellt) som parametrar för begäran `acquireTokenSilent` till MSAL.js-anropet enligt följande:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO i ADAL.js till MSAL.js uppdatering

MSAL.js ger funktionsparitet med ADAL.js för Azure AD-autentiseringsscenarier. Om du vill göra migreringen från ADAL.js till MSAL.js enkel och för att undvika att uppmana användarna att logga in igen läser biblioteket ID-token som representerar användarens session i ADAL.js-cachen och signerar sömlöst i användaren i MSAL.js.  

Om du vill dra nytta av beteendet för enkel inloggning (SSO) när du uppdaterar från `localStorage` ADAL.js måste du se till att biblioteken använder för cachelagring av token. Ställ `cacheLocation` in `localStorage` konfigurationen i både MSAL.js och ADAL.js vid initieringen enligt följande:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

När detta har konfigurerats kommer MSAL.js att kunna läsa det cachelagrade tillståndet för den autentiserade användaren i ADAL.js och använda det för att tillhandahålla SSO i MSAL.js.

## <a name="next-steps"></a>Nästa steg

Läs mer om värden för [enstaka inloggningssessioner och tokenlivstid](active-directory-configurable-token-lifetimes.md) i Azure AD.
