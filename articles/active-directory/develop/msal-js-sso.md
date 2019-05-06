---
title: Enkel inloggning (Microsoft Authentication Library för JavaScript) | Azure
description: Läs mer om att skapa upplevelser för enkel inloggning som är med hjälp av Microsoft Authentication Library för JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a0fb0731b7ac46210294e3766b33bd6a239dcc2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075857"
---
# <a name="single-sign-on-with-msaljs"></a>Enkel inloggning med MSAL.js

Enkel inloggning (SSO) kan användarna ange sina autentiseringsuppgifter för en gång för att logga in och upprätta en session som kan återanvändas i flera program utan att behöva autentisera igen. Detta ger en sömlös upplevelse för användaren och minskar upprepade uppmaningar om autentiseringsuppgifter.

Azure AD tillhandahåller enkel inloggning till program genom att ange en sessions-cookie när användaren autentiseras första gången. MSAL.js biblioteket gör att program kan utnyttja detta på flera sätt.

## <a name="sso-between-browser-tabs"></a>Enkel inloggning mellan flikar i webbläsaren

När ditt program är öppna i flera flikar och du först logga in användaren på en flik, är användaren också inloggad på de andra flikarna utan att behöva ange. MSAL.js cachelagrar ID-token för användaren i webbläsaren `localStorage` och ska logga in användaren till programmet på de andra öppna flikarna.

Som standard använder MSAL.js `sessionStorage` som tillåter inte sessionen som ska delas mellan flikar. För att få enkel inloggning mellan flikar, se till att ange den `cacheLocation` i MSAL.js till `localStorage` enligt nedan.

```javascript
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>Enkel inloggning mellan appar

När en användare autentiseras in en sessionscookie på Azure AD-domänen i webbläsaren. MSAL.js är beroende av den här sessions-cookie för att tillhandahålla enkel inloggning för användaren mellan olika program. MSAL.js cachelagrar också ID-token och åtkomsttoken för användaren i webbläsaren lagringsutrymme per programdomän. SSO-funktionen varierar därför för olika fall:  

### <a name="applications-on-the-same-domain"></a>Program i samma domän

När programmen finns på samma domän, användaren kan logga in på appen en gång och sedan hämta autentiseras för andra appar utan att användaren tillfrågas. MSAL.js utnyttjar de token som cachelagras för användaren på domänen att erbjuda enkel inloggning.

### <a name="applications-on-different-domain"></a>Program på en annan domän

När programmen finns på olika domäner, kan de token som cachelagrats på domän A inte nås av MSAL.js i domän B.

Det innebär att när användare som loggat in på domänen navigerar till ett program i domän B, de ska omdirigeras eller du uppmanas att göra med Azure AD-sidan. Eftersom Azure AD har fortfarande sessions-cookie för användaren, den ska logga in användaren och de behöver inte ange autentiseringsuppgifterna igen. Om användaren har flera användarkonton i sessionen med Azure AD, uppmanas användaren att välja det relevanta kontot logga in med.

### <a name="automatically-select-account-on-azure-ad"></a>Automatiskt välja kontot i Azure AD

I vissa fall kan programmet har åtkomst till användarens autentiseringskontext och vill vill uppmanas att val av Azure AD-konto när flera konton har loggat in.  Detta kan göras på ett par olika sätt:

**Med hjälp av sessions-ID (SID)**

Sessions-ID är ett [valfria anspråk](active-directory-optional-claims.md) som kan konfigureras i ID-token. Det här anspråket tillåter programmet att identifiera användarens Azure AD-session som är oberoende av användarens kontonamn eller användarnamn. Du kan skicka SID i parametrarna till de `acquireTokenSilent` anropa. Detta gör att Azure AD för att kringgå kontovalet. SID är bunden till sessions-cookie och kommer inte mellan webbläsaren sammanhang.

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
> SID kan bara användas med tyst autentiseringsbegäranden av `acquireTokenSilent` anropa i MSAL.js.
Du hittar stegen för att konfigurera valfria anspråk i programmanifestet [här](active-directory-optional-claims.md).

**Med hjälp av inloggningen tips**

Om du inte har SID anspråk konfigurerad eller behöva kringgå konto val av meddelande i anrop för interaktiv autentisering kan du göra det genom att tillhandahålla en `login_hint` i parametrarna och eventuellt en `domain_hint` som `extraQueryParameters` i MSAL.js interaktiva metoder (`loginPopup`, `loginRedirect`, `acquireTokenPopup` och `acquireTokenRedirect`). Exempel:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Du kan få värdena för login_hint och domain_hint genom att läsa anspråk som returneras i ID-token för användaren.

* **loginHint** ska vara inställd på den `preferred_username` anspråk i ID-token.

* **domain_hint** krävs endast som ska skickas när du använder den/Common utfärdare. Tips för domänen bestäms av klient ID(tid).  Om den `tid` anspråk i ID-token är `9188040d-6c67-4c5b-b112-36a304b66dad` är det konsumenter. I annat fall är det organisationer.

Läs [här](v2-oauth2-implicit-grant-flow.md) mer information om värdena för inloggnings-tipset och tips för domänen.

> [!Note]
> Du kan inte skicka SID och login_hint på samma gång. Detta resulterar i felsvaret.

## <a name="sso-without-msaljs-login"></a>Enkel inloggning utan MSAL.js inloggning

Avsiktligt kräver MSAL.js att en inloggning-metoden anropas för att upprätta en användarkontext innan du hämtar token för API: er. Eftersom inloggningsmetoder är interaktiva, ser användaren en uppmaning.

Det finns vissa fall där program har åtkomst till den autentiserade användarens kontext eller ID-token via autentisering initieras i ett annat program och vill använda enkel inloggning för att hämta token utan att första logga in via MSAL.js.

Ett exempel på detta är: En användare är inloggad i en överordnad-webbprogram som är värd för en annan JavaScript-program som körs som ett tillägg eller plugin-programmet.

SSO-upplevelse i det här scenariot kan ske på följande sätt:

Skicka den `sid` om de är tillgängliga (eller `login_hint` och eventuellt `domain_hint`) som begäranparametrar till MSAL.js `acquireTokenSilent` anropa på följande sätt:

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>Enkel inloggning i ADAL.js till MSAL.js update

MSAL.js ger funktionsparitet med ADAL.js för scenarier med Azure AD-autentisering. Att göra migreringen från ADAL.js för MSAL.js enkelt och för att undvika att dina användare att logga in igen i biblioteket läser ID-token som representerar användarens session ADAL.js cache och loggar smidigt in användaren i MSAL.js.  

Om du vill dra nytta av enkel inloggning (SSO)-beteende vid uppdatering från ADAL.js, måste du se till att bibliotek som använder `localStorage` för cachelagring av token. Ange den `cacheLocation` till `localStorage` i både MSAL.js och ADAL.js konfiguration vid initieringen på följande sätt:


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
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

När detta är konfigurerat kommer MSAL.js att kunna läsa cachelagrade tillståndet för den autentiserade användaren i ADAL.js och använder dem för att erbjuda enkel inloggning i MSAL.js.

## <a name="next-steps"></a>Nästa steg

Läs mer om den [enkel inloggning och livslängd för uppdateringstoken](active-directory-configurable-token-lifetimes.md) värden i Azure AD.
