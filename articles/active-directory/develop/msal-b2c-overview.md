---
title: Lär dig hur program kan samverka med Azure AD B2C med hjälp av Microsoft Authentication Library
description: 'Microsoft Authentication Library (MSAL) gör det möjligt för program att samverka med Azure AD B2C och hämta token för att anropa säkra webb-API: er. Dessa webb-API: er kan vara Microsoft Graph, andra Microsoft API: er, webb-API: er från andra eller ditt eget webb-API.'
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: db05f59faf945e425761fe7a20bad3e263246a39
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849345"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Använd Microsoft Authentication Library för att samverka med Azure Active Directory B2C

Microsoft Authentication Library (MSAL) gör det möjligt för programutvecklare att autentisera användare med sociala och lokala identiteter genom att använda [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C är en identitets hanterings tjänst. Genom att använda den kan du anpassa och styra hur kunderna registrerar sig, loggar in och hanterar sina profiler när de använder dina program.

Med Azure AD B2C kan du också skapa och anpassa användar gränssnittet för dina program för att ge kunderna en sömlös upplevelse.

Den här självstudien visar hur du använder MSAL för att samverka med Azure AD B2C.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har skapat din egen [Azure AD B2C-klient](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)skapar du en nu. Du kan också använda en befintlig Azure AD B2C klient.

## <a name="javascript"></a>JavaScript

Följande steg visar hur ett program med en enda sida kan använda Azure AD B2C för att registrera dig, logga in och anropa ett skyddat webb-API.

### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program

För att implementera autentisering måste du först registrera ditt program. Se [Registrera ditt program](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) för detaljerade anvisningar.

### <a name="step-2-download-the-sample-application"></a>Steg 2: Hämta exempelprogrammet

Hämta exemplet som en zip-fil eller klona den från GitHub:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Steg 3: Konfigurera autentisering

1. Öppna filen **index. html** i exemplet.

1. Konfigurera exemplet med det program-ID och den nyckel som du registrerade tidigare när du registrerade ditt program. Ändra följande kodrader genom att ersätta värdena med namnen på din katalog och dina API:er:

   ```javascript
   // The current application coordinates were pre-registered in a B2C directory.

   const msalConfig = {
       auth:{
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
           b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
           webApi: 'http://localhost:5000/hello',
     };

   // create UserAgentApplication instance
   const myMSALObj = new UserAgentApplication(msalConfig);
   ```

Namnet på [användar flödet](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) i den här självstudien är **B2C_1_signupsignin1**. Om du använder ett annat användar flödes namn anger du värdet för **auktoritet** till det namnet.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Steg 4: Konfigurera ditt program att använda`b2clogin.com`

Du kan använda `b2clogin.com` i stället för `login.microsoftonline.com` en omdirigerings-URL. Du gör det i Azure AD B2C-programmet när du konfigurerar en identitets leverantör för registrering och inloggning.

Användningen av `b2clogin.com` i kontexten för `https://your-tenant-name.b2clogin.com/your-tenant-guid` har följande effekter:

- Microsoft-tjänster förbrukar mindre utrymme i cookie-huvudet.
- URL: erna innehåller inte längre en referens till Microsoft. Till exempel refererar ditt Azure AD B2C program antagligen till `login.microsoftonline.com`.

 Om du `b2clogin.com`vill använda måste du uppdatera konfigurationen av ditt program.  

- Ange egenskapen **validateAuthority** till `false`, så att omdirigeringar med `b2clogin.com` kan ske.

I följande exempel visas hur du kan ställa in egenskapen:

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
> Ditt Azure AD B2C program refererar förmodligen till `login.microsoftonline.com` på flera platser, till exempel användar flödes referenser och token-slutpunkter. Se till att behörighets slut punkten, token Endpoint och utfärdaren har uppdaterats för `your-tenant-name.b2clogin.com`användning.

Följ [det här MSAL-JavaScript-exemplet](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) om hur du använder MSAL Preview för Java Script (MSAL. js). Exemplet hämtar en åtkomsttoken och anropar ett API som skyddas av Azure AD B2C.

## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [Anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Anpassning av användar gränssnitt](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)