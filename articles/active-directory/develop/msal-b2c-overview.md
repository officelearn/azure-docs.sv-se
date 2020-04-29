---
title: Använda MSAL med Azure Active Directory B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: 'Microsoft Authentication Library för Java Script (MSAL. js) gör det möjligt för program att arbeta med Azure AD B2C och hämta token för att anropa säkra webb-API: er. Dessa webb-API: er kan vara Microsoft Graph, andra Microsoft API: er, webb-API: er från andra eller ditt eget webb-API.'
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8e076dfd6670265d458eb35d8e1b3e4500009a12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534490"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Använd Microsoft Authentication Library för Java Script för att arbeta med Azure Active Directory B2C

[Microsoft Authentication Library för Java Script (MSAL. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) gör det möjligt för JavaScript-utvecklare att autentisera användare med sociala och lokala identiteter med hjälp av [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Genom att använda Azure AD B2C som en identitets hanterings tjänst kan du anpassa och styra hur kunderna registrerar sig, loggar in och hanterar sina profiler när de använder dina program.

Med Azure AD B2C kan du också anpassa användar gränssnittet för dina program under autentiseringsprocessen för att ge kunderna en sömlös upplevelse.

Den här artikeln visar hur du använder MSAL. js för att arbeta med Azure AD B2C och sammanfattar viktiga punkter som du bör vara medveten om. En fullständig diskussion och själv studie kurs finns i [Azure AD B2C-dokumentationen](https://docs.microsoft.com/azure/active-directory-b2c/overview).

## <a name="prerequisites"></a>Krav

Om du inte redan har skapat din egen [Azure AD B2C klient](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)börjar du med att skapa en nu (du kan också använda en befintlig Azure AD B2C klient om du redan har en).

Den här demonstrationen innehåller två delar:

- så här skyddar du ett webb-API.
- registrera ett program på en sida *för att autentisera och anropa webb* -API: et.

## <a name="nodejs-web-api"></a>Node.js-webb-API

> [!NOTE]
> För närvarande är MSAL. js för Node fortfarande under utveckling (se [översikten](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). Under tiden rekommenderar vi att du använder [Passport-Azure-AD](https://github.com/AzureAD/passport-azure-ad), ett autentiseringspaket för Node. js som har utvecklats och stöds av Microsoft.

Följande steg visar hur ett **webb-API** kan använda Azure AD B2C för att skydda sig själv och exponera valda omfång för ett klient program.

### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program

För att skydda ditt webb-API med Azure AD B2C måste du först registrera det. Se [Registrera ditt program](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) för detaljerade anvisningar.

### <a name="step-2-download-the-sample-application"></a>Steg 2: Ladda ned exempel programmet

Hämta exemplet som en zip-fil eller klona den från GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Steg 3: Konfigurera autentisering

1. Öppna filen `config.js` i exemplet.

2. Konfigurera exemplet med de programautentiseringsuppgifter som du fick tidigare när du registrerade ditt program. Ändra följande rader med kod genom att ersätta värdena med namnen på din clientID, värd, tenantId och princip namn.

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Mer information finns i detta [Node. js B2C-webb-API-exempel](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>Java Script SPA

Följande steg visar hur ett program med en **enda sida** kan använda Azure AD B2C för att registrera dig, logga in och anropa ett skyddat webb-API.

### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program

För att implementera autentisering måste du först registrera ditt program. Se [Registrera ditt program](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) för detaljerade anvisningar.

### <a name="step-2-download-the-sample-application"></a>Steg 2: Ladda ned exempel programmet

Hämta exemplet som en zip-fil eller klona den från GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Steg 3: Konfigurera autentisering

Det finns två intressanta punkter när du konfigurerar ditt program:

- Konfigurera API-slutpunkt och exponerade omfång
- Konfigurera autentiseringsmetoder och token-scope

1. Öppna filen `apiConfig.js` i exemplet.

2. Konfigurera exemplet med de parametrar som du fick tidigare när du registrerade ditt webb-API. Ändra följande rader med kod genom att ersätta värdena med adressen för ditt webb-API och exponerade omfång.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. Öppna filen `authConfig.js` i exemplet.

4. Konfigurera exemplet med de parametrar som du fick tidigare när du registrerade ditt program på en sida. Ändra följande rader med kod genom att ersätta värdena med dina ClientId, utfärda metadata och token för token-begäran.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Mer information finns i [Java Script B2C-exempelprogram med en sida](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>Nästa steg

Läs mer om:
- [Användar flöden](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [UX-anpassning](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
