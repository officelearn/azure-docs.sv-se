---
title: Använd MSAL med Azure Active Directory B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) gör det möjligt för program att samverka med Azure AD B2C och hämta token för att anropa skyddade webb-API:er. Dessa webb-API:er kan vara Microsoft Graph, andra Microsoft-API:er, webb-API:er från andra eller ditt eget webb-API.
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
ms.openlocfilehash: e25564e64410701754390024a5bcfd39321343e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696460"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Använda Microsoft Authentication Library för att samverka med Azure Active Directory B2C

Med MSAL (Microsoft Authentication Library) kan programutvecklare autentisera användare med sociala och lokala identiteter med hjälp av [Azure Active Directory B2C (Azure AD B2C).](https://docs.microsoft.com/azure/active-directory-b2c/) Azure AD B2C är en identitetshanteringstjänst. Genom att använda den kan du anpassa och styra hur kunder registrerar sig, loggar in och hanterar sina profiler när de använder dina program.

Med Azure AD B2C kan du också skapa varumärke och anpassa användargränssnittet för dina program så att det ger dina kunder en sömlös upplevelse.

Den här självstudien visar hur du använder MSAL för att samverka med Azure AD B2C.

## <a name="prerequisites"></a>Krav

Om du inte redan har skapat din egen [Azure AD B2C-klient](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)skapar du en nu. Du kan också använda en befintlig Azure AD B2C-klientorganisation.

## <a name="javascript"></a>JavaScript

Följande steg visar hur ett ensidigt program kan använda Azure AD B2C för att registrera dig, logga in och anropa ett skyddat webb-API.

### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program

För att implementera autentisering måste du först registrera ditt program. Se [Registrera din ansökan](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) för detaljerade steg.

### <a name="step-2-download-the-sample-application"></a>Steg 2: Ladda ner exempelprogrammet

Hämta exemplet som en zip-fil eller klona det från GitHub:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Steg 3: Konfigurera autentisering

1. Öppna **filen index.html** i exemplet.

1. Konfigurera exemplet med klient-ID och nyckel som du spelade in tidigare när du registrerade programmet. Ändra följande kodrader genom att ersätta värdena med namnen på din katalog och dina API:er:

   ```javascript
   // The current application coordinates were pre-registered in a B2C tenant.

    var appConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };

    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902" //This is your client/application ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    // create UserAgentApplication instance
    const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   ```

Namnet på [användarflödet](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) i den här självstudien är **B2C_1_signupsignin1**. Om du använder ett annat användarnamn anger du **myndighetsvärdet** till det namnet.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Steg 4: Konfigurera ditt program så att det används`b2clogin.com`

Du kan `b2clogin.com` använda `login.microsoftonline.com` i stället för som en omdirigerings-URL. Du gör det i ditt Azure AD B2C-program när du konfigurerar en identitetsleverantör för registrering och inloggning.

Användning `b2clogin.com` av i `https://your-tenant-name.b2clogin.com/your-tenant-guid` samband med har följande effekter:

- Microsoft-tjänster förbrukar mindre utrymme i cookie-huvudet.
- Webbadresserna innehåller inte längre någon referens till Microsoft. Ditt Azure AD B2C-program refererar `login.microsoftonline.com`till exempel förmodligen till .

 Om `b2clogin.com`du vill använda måste du uppdatera konfigurationen av programmet.  

- Ange egenskapen **validateAuthority** till `false`, `b2clogin.com` så att omdirigeringar som används kan ske.

I följande exempel visas hur du kan ange egenskapen:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> Ditt Azure AD B2C-program `login.microsoftonline.com` refererar förmodligen till på flera ställen, till exempel dina användarflödesreferenser och tokenslutpunkter. Kontrollera att auktoriseringsslutpunkten, tokenslutpunkten och utfärdaren har uppdaterats för att använda `your-tenant-name.b2clogin.com`.

Följ [det här MSAL JavaScript-exemplet](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) om hur du använder MSAL Preview för JavaScript (MSAL.js). Exemplet hämtar en åtkomsttoken och anropar ett API som skyddas av Azure AD B2C.

## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [Anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Anpassning av användargränssnitt](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)