---
title: Snabb beteende för interaktiv begäran (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig att anpassa snabbt beteende i interaktiva samtal med hjälp av Microsoft Authentication Library for JavaScript (MSAL.js).
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
ms.openlocfilehash: 778e89655019a49a30904fbe8d8e6aedf1833e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695984"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Snabbt beteende i MSAL.js interaktiva begäranden

När en användare har upprättat en aktiv Azure AD-session med flera användarkonton uppmanas som standard användaren att välja ett konto innan han fortsätter att logga in. Användarna ser inte en kontovalsupplevelse om det bara finns en enda autentiserat möte med Azure AD.

MSAL.js-biblioteket (med början i v0.2.4) skickar inte en`loginRedirect` `loginPopup`promptparameter under de interaktiva begärandena ( , `acquireTokenRedirect` och `acquireTokenPopup`) och framtvingar därför inte något snabbt beteende. För tysta tokenbegäranden med `acquireTokenSilent` metoden skickar MSAL.js `none`en promptparameteruppsättning till .

Baserat på ditt programscenario kan du styra promptbeteendet för de interaktiva begärandena genom att ange promptparametern i de begärandeparametrar som skickas till metoderna. Om du till exempel vill anropa kontovalsupplevelsen:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Följande promptvärden kan skickas vid autentisering med Azure AD:

**logga in:** Det här värdet tvingar användaren att ange autentiseringsuppgifter på autentiseringsbegäran.

**select_account:** Det här värdet ger användaren en kontovalsupplevelse med alla konton i session.

**samtycke:** Det här värdet anropar OAuth-samtyckesdialogen som tillåter användare att bevilja behörigheter till appen.

**ingen:** Det här värdet säkerställer att användaren inte ser någon interaktiv fråga. Vi rekommenderar att du inte skickar det här värdet till interaktiva metoder i MSAL.js eftersom det kan ha oväntade beteenden. Använd i `acquireTokenSilent` stället metoden för att uppnå tysta anrop.

## <a name="next-steps"></a>Nästa steg

Läs mer `prompt` om parametern i [OAuth 2.0 implicit beviljande](v2-oauth2-implicit-grant-flow.md) protokoll som MSAL.js bibliotek använder.
