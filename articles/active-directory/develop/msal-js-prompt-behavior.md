---
title: Beteende för interaktiv förfrågan om fråga (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du anpassar uppmanings beteende i interaktiva anrop med Microsoft Authentication Library för Java Script (MSAL. js).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6654b523cef4623a55bf9f857fd5a723fae2dc47
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921954"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Fråga i MSAL. js interaktiva begär Anden

När en användare har upprättat en aktiv Azure AD-session med flera användar konton uppmanas inloggnings sidan för Azure AD som standard att användaren väljer ett konto innan du loggar in. Användarna ser ingen upplevelse för val av konto om det bara finns en enda autentiserad session med Azure AD.

MSAL. js-biblioteket (från och med v-0.2.4) skickar inte någon prompt-parameter under de interaktiva begär Anden (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` och `acquireTokenPopup`) och tillämpar därmed inte några uppfrågande beteenden. För tysta token-begäranden med hjälp av metoden `acquireTokenSilent` skickar MSAL. js en prompt-parameter inställd på `none`.

Baserat på ditt program scenario kan du kontrol lera frågans beteende för de interaktiva förfrågningarna genom att ställa in prompt-parametern i parametrarna för begäran som skickas till metoderna. Om du till exempel vill starta upplevelsen för konto val:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Följande prompt-värden kan skickas vid autentisering med Azure AD:

**inloggning:** Det här värdet tvingar användaren att ange autentiseringsuppgifter för autentiseringsbegäran.

**select_account:** Med det här värdet får användaren ett konto val som visar alla konton i sessionen.

**medgivande:** Med det här värdet anropas dialog rutan OAuth-medgivande som gör det möjligt för användarna att bevilja behörighet till appen.

**Ingen:** Det här värdet ser till att användaren inte ser någon interaktiv prompt. Vi rekommenderar att du inte skickar det här värdet till interaktiva metoder i MSAL. js eftersom det kan orsaka oväntade beteenden. Använd i stället metoden `acquireTokenSilent` för att få tysta anrop.

## <a name="next-steps"></a>Nästa steg

Läs mer om `prompt`-parametern i OAuth-protokollet [OAuth 2,0](v2-oauth2-implicit-grant-flow.md) som MSAL. js-biblioteket använder.
