---
title: Web apps i Azure Active Directory
description: Beskriver vilka webbappar är och grunderna på protocol flow, registrering och token upphör att gälla för den här apptypen.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0337f3de40e75ada86c0a5265fc99b34f1c2ecae
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58367924"
---
# <a name="web-apps"></a>Webbappar

Webbappar är program som autentiserar en användare i en webbläsare till ett webbprogram. I det här scenariot uppmanar webbprogrammet användarens webbläsare att logga in dem till Azure AD. Azure AD returnerar ett svar via användarens webbläsare, som innehåller anspråk om användaren i en säkerhetstoken. Det här scenariot stöder inloggning med OpenID Connect, SAML 2.0 och WS-Federation-protokoll.

## <a name="diagram"></a>Diagram

![Autentiseringsflödet för webbläsaren till webbprogram](./media/authentication-scenarios/web_browser_to_web_api.png)

## <a name="protocol-flow"></a>Protocol flow

1. När en användare besöker programmet och behöver logga in, omdirigeras via en begäran till autentiseringsslutpunkten för i Azure AD.
1. Användaren loggar in på sidan logga in.
1. Om autentiseringen lyckas skapar en autentiseringstoken Azure AD och returnerar ett svar till programmets svars-URL som har konfigurerats i Azure-portalen. För ett produktionsprogram måste svars-URL vara HTTPS. Den returnerade token innehåller anspråk om användaren och Azure AD som krävs av programmet för att validera token.
1. Programmet verifierar token med hjälp av en offentlig signeringsnyckel och information om utfärdare är tillgänglig på federationsmetadatadokumentet för Azure AD. När programmet verifierar token, startar en ny session med användaren. Den här sessionen gör att användaren kan komma åt programmet tills det upphör att gälla.

## <a name="code-samples"></a>Kodexempel

Se kodexempel för webbläsarens web Programscenarier. Och kontrollera tillbaka ofta eftersom nya exempel läggs ofta.

## <a name="app-registration"></a>Appregistrering

Om du vill registrera ett webbprogram, se [registrera en app med Azure AD v1.0 slutpunkten](quickstart-v1-add-azure-ad-app.md).

* Enskild klient - om du skapar ett program för din organisation kan den registreras i företagets katalog med hjälp av Azure portal.
* Flera innehavare - om du skapar ett program som kan användas av användare utanför organisationen, det måste vara registrerad i företagets katalog, men även måste registreras i varje organisations katalog som kommer att använda programmet. Du kan inkludera en registreringsprocess för dina kunder som gör det möjligt för dem att godkänna ditt program för att göra programmet tillgängligt i sin katalog. När de registrerar sig för ditt program, kommer de att visas en dialogruta som visar de behörigheter som programmet kräver och sedan alternativet för att godkänna. Beroende på behörigheterna som krävs kanske en administratör i den andra organisationen måste ge ditt medgivande. När användaren eller administratören godkänner, registreras programmet i sin katalog.

## <a name="token-expiration"></a>Giltighetstid för token

Användarens session upphör att gälla när livslängd för token som utfärdas av Azure AD upphör att gälla. Ditt program kan förkorta denna tidsperiod om du vill, till exempel logga ut användare baserat på en period av inaktivitet. När sessionen upphör att gälla, uppmanas användaren att logga in igen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [programtyper och scenarier](app-types.md)
* Lär dig mer om Azure AD [grunder](authentication-scenarios.md)
