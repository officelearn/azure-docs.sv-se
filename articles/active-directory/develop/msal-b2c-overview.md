---
title: Lär dig hur du integrerar med Azure AD B2C med hjälp av Microsoft Authentication Library (MSAL)
description: 'Microsoft Authentication Library (MSAL) gör det möjligt för apputvecklare att integrera med Azure AD B2C och hämta token för att kunna anropa säkra webb-API: er. Dessa webb-API: er kan vara Microsoft Graph, andra Microsoft-APIS, från tredje part webb-API: er eller Web API.'
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: celested
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
ms.openlocfilehash: 87bd8834f840c2246bf3adc1d1f9cd9b8f635915
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65191007"
---
# <a name="integrate-microsoft-authentication-library-msal-with-azure-active-directory-b2c"></a>Integrera Microsoft Authentication Library (MSAL) med Azure Active Directory B2C

Microsoft Authentication Library (MSAL) gör det möjligt för apputvecklare att autentisera användare med sociala och lokala identiteter med [Azure Active Directory (Azure AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/). Azure Active Directory (Azure AD) B2C är en identitetshanteringstjänst som hjälper dig att anpassa och styra hur kunderna registrerar sig, loggar in och hanterar sina profiler när de använder dina program.

Azure Active Directory (Azure AD) B2C kan du anpassar och anpassa användargränssnittet (UI) för dina program att förse kunderna en sömlös upplevelse också.

Den här självstudien visar hur du använder Microsoft Authentication Library (MSAL) för att integrera med Azure Active Directory (Azure AD) B2C.


## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte redan har skapat dina egna [Azure AD B2C-klient](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), skapa en nu. Du kan använda en befintlig Azure AD B2C-klient. 

## <a name="javascript"></a>Javascript

Följande steg visar hur ett enkelsidigt program kan använda Azure AD B2C för användarens registrering och inloggning och anropa ett skyddat webb-API.

### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program

Om du vill implementera autentisering, måste du först registrera ditt program. Om du vill registrera din app, Följ [registrera ditt program](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) detaljerade anvisningar.

### <a name="steps-2-download-applications"></a>Steg 2: Hämta program

Ladda ned en zip-fil eller klona exemplet från GitHub.
>Git-klonen https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git

### <a name="steps-3-authentication"></a>Steg 3: Autentisering

1. Öppna filen index.html i exemplet.

2. Konfigurera exemplet med program-ID och nyckel som du antecknade tidigare när du registrerar ditt program. Ändra följande kodrader genom att ersätta värdena med namnen på din katalog och dina API:er:

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

Namnet på den [användarflödet](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) används i den här självstudien är B2C_1_signupsignin1. Om du använder ett annat användarnamn för flödet kan du använda ditt användarnamn för flow i värdet.


### <a name="configure-application-to-use-b2clogincom"></a>Konfigurera program för att använda `b2clogin.com`

Du kan använda `b2clogin.com` i stället för `login.microsoftonline.com` som en omdirigering URL: en, när du ställer in en identitetsprovider för registrering och inloggning i ditt Azure Active Directory (Azure AD) B2C-program.

**`b2clogin.com`** i.e 
`https://your-tenant-name.b2clogin.com/your-tenant-guid` används för följande:

- Minskar utrymmet som används i rubriken cookie av Microsoft-tjänster.
- Din URL: er inkluderar inte längre en referens till Microsoft. Till exempel refererar Azure AD B2C-program förmodligen till login.microsoftonline.com


 Om du vill använda 'b2clogin.com', som du behöver uppdatera konfigurationen av ditt program.  

1. Uppdatera ValidateAuthority: Ange den **validateAuthority** egenskap `false`. När **validateAuthority** har angetts till false, omdirigeringar tillåts att b2clogin.com.

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
> Azure AD B2C-programmet avser förmodligen login.microsoftonline.com på flera platser, till exempel användarreferenser för flödet och token-slutpunkter. Kontrollera att din auktoriseringsslutpunkt och tokenslutpunkten utfärdare har uppdaterats för att använda din klient name.b2clogin.com.

Följ den här [MSAL JS exempel](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) om hur du använder förhandsversionen av Microsoft Authentication Library för JavaScript (msal.js) för att få en åtkomsttoken och anropa ett API som skyddas av Azure AD B2C.

## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [Anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Anpassning av användargränssnitt](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)