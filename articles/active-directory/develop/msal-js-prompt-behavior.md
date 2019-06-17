---
title: Uppmana beteende i interaktiva begäranden (Microsoft Authentication Library för JavaScript) | Azure
description: Lär dig mer om hur du anpassar beteende i interaktiva anrop med hjälp av Microsoft Authentication Library för JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
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
ms.openlocfilehash: dd0d736345f312f1a1d6f8f029b41429a3e5f0a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544276"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Beteende vid fråga i MSAL.js interaktiva begäranden

När en användare har etablerat en active Azure AD-session med flera användarkonton, uppmanar Azure AD-inloggningssidan som standard användaren att välja ett konto innan du fortsätter att logga in. Användarna ser inte en kontoval uppstår om det finns bara en enda autentiserad session med Azure AD.

MSAL.js biblioteket (med början i v0.2.4) inte skicka en promptparameter under interaktiva begäranden (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` och `acquireTokenPopup`), och därmed tillämpar inte någon beteende. För tyst tokenbegäranden med det `acquireTokenSilent` metoden MSAL.js skickar en fråga parameter angetts till `none`.

Baserat på ditt program scenario kan styra du fråga beteendet för interaktiva begäranden av inställning promptparameter i parametrarna som skickas till-metoderna. Exempel: Om du vill anropa upplevelsen för val av konto:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Följande fråga värden kan skickas när du autentiserar med Azure AD:

**login:** Det här värdet kommer att tvinga användaren att ange autentiseringsuppgifter på autentiseringsbegäran.

**select_account:** Det här värdet ger användaren en upplevelse för val av konto listar alla konton i sessionen.

**medgivande:** Det här värdet anropar dialogen för OAuth-medgivande som tillåter användare att ge behörighet till appen.

**Ingen:** Det här värdet garanterar att användaren inte finns i den interaktiva prompten. Vi rekommenderar inte att skicka det här värdet till interaktiva metoder i MSAL.js som den kan ha oväntat beteende. Använd i stället den `acquireTokenSilent` metod för att uppnå tyst anrop.

## <a name="next-steps"></a>Nästa steg

Läs mer om den `prompt` parametern i den [implicit beviljande av OAuth 2.0](v2-oauth2-implicit-grant-flow.md) protokoll som använder vilka MSAL.js.
