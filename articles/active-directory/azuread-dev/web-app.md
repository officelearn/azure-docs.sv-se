---
title: Webbappar i Azure Active Directory
description: Beskriver vad webbappar är och grunderna för protokollflöde, registrering och tokenförfallodatum för den här apptypen.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154414"
---
# <a name="web-apps"></a>Webbappar

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Webbappar är program som autentiserar en användare i en webbläsare till ett webbprogram. I det här fallet styr webbprogrammet användarens webbläsare för att logga in dem på Azure AD. Azure AD returnerar ett inloggningssvar via användarens webbläsare, som innehåller anspråk om användaren i en säkerhetstoken. Det här scenariot stöder inloggning med openid connect-, SAML 2.0- och WS-Federation-protokollen.

## <a name="diagram"></a>Diagram

![Autentiseringsflöde för webbläsare till webbprogram](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>Protokollflöde

1. När en användare besöker programmet och behöver logga in omdirigeras de via en inloggningsbegäran till autentiseringsslutpunkten i Azure AD.
1. Användaren loggar in på inloggningssidan.
1. Om autentiseringen lyckas skapar Azure AD en autentiseringstoken och returnerar ett inloggningssvar till programmets svars-URL som konfigurerades i Azure-portalen. För ett produktionsprogram ska den här svars-URL:en vara HTTPS. Den returnerade token innehåller anspråk om användaren och Azure AD som krävs av programmet för att validera token.
1. Programmet validerar token med hjälp av en offentlig signeringsnyckel och utfärdare information som är tillgänglig på federationen metadatadokument för Azure AD. När programmet har validerat token startar den en ny session med användaren. Den här sessionen gör det möjligt för användaren att komma åt programmet tills det upphör att gälla.

## <a name="code-samples"></a>Kodexempel

Se kodexemplen för webbläsare till scenarier för webbprogram. Och kom tillbaka ofta när nya exempel läggs till ofta.

## <a name="app-registration"></a>Appregistrering

Om du vill registrera en webbapp finns [i Registrera en app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* En enda klient - Om du skapar ett program bara för din organisation måste det registreras i företagets katalog med hjälp av Azure-portalen.
* Flera innehavare - Om du skapar ett program som kan användas av användare utanför organisationen måste det vara registrerat i företagets katalog, men måste också registreras i varje organisations katalog som ska använda programmet. Om du vill göra ditt program tillgängligt i deras katalog kan du inkludera en registreringsprocess för dina kunder som gör det möjligt för dem att godkänna ditt program. När de registrerar sig för ditt program visas en dialogruta som visar de behörigheter som programmet kräver och sedan alternativet att godkänna. Beroende på vilka behörigheter som krävs kan en administratör i den andra organisationen behöva ge sitt samtycke. När användaren eller administratören samtycker registreras programmet i deras katalog.

## <a name="token-expiration"></a>Token förfallodatum

Användarens session upphör att gälla när livslängden för den token som utfärdats av Azure AD upphör att gälla. Ditt program kan förkorta den här tidsperioden om så önskas, till exempel att logga ut användare baserat på en period av inaktivitet. När sessionen går ut uppmanas användaren att logga in igen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [programtyper och scenarier](app-types.md)
* Lär dig mer om grunderna i Azure [AD-autentisering](v1-authentication-scenarios.md)
