---
title: Översikt över Azure Multi Factor-autentisering
description: Lär dig mer om hur Azure Multi-Factor Authentication hjälper till att skydda åtkomsten till data och program samtidigt som användarnas efterfrågan på en enkel inloggningsprocess tillgodoses.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c50232abd12c8c0390409bd7bf72833b4f153e02
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667368"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Så här fungerar Azure Multi-Factor Authentication

Multifaktorautentisering är en process där en användare uppmanas under inloggningsprocessen för ytterligare en form av identifiering, till exempel för att ange en kod på sin mobiltelefon eller för att tillhandahålla en fingeravtrycksskanning.

Om du bara använder ett lösenord för att autentisera en användare, lämnar det en osäker vektor för angrepp. Om lösenordet är svagt eller har exponerats någon annanstans, är det verkligen användaren loggar in med användarnamn och lösenord, eller är det en angripare? När du behöver en andra form av autentisering ökar säkerheten eftersom den här ytterligare faktorn inte är något som är lätt för en angripare att hämta eller duplicera.

![Konceptuell bild av de olika formerna av multifaktorautentisering](./media/concept-mfa-howitworks/methods.png)

Azure Multi Factor Authentication fungerar genom att kräva två eller flera av följande autentiseringsmetoder:

* Något du vet, vanligtvis ett lösenord.
* Något du har, till exempel en betrodd enhet som inte är lätt att duplicera, som en telefon eller maskinvarunyckel.
* Något du är - biometri som ett fingeravtryck eller ansikte scan.

Användare kan registrera sig för både återställning av lösenord för självbetjäning och Azure Multi-Factor Authentication i ett steg för att förenkla upplevelsen av ombordstigning. Administratörer kan definiera vilka former av sekundär autentisering som kan användas. Azure Multi-Factor Autentisering kan också krävas när användare utför en självbetjäning lösenordsåterställning för att ytterligare säkra den processen.

![Autentiseringsmetoder som används på inloggningsskärmen](media/concept-authentication-methods/overview-login.png)

Azure Multi-Factor Authentication hjälper till att skydda åtkomsten till data och program samtidigt som enkelheten upprätthålls för användarna. Det ger ytterligare säkerhet genom att kräva en andra form av autentisering och ger stark autentisering via en rad lättanvända [autentiseringsmetoder](concept-authentication-methods.md). Användare kan eller inte kan ifrågasättas för MFA baserat på konfigurationsbeslut som en administratör gör.

Dina program eller tjänster behöver inte göra några ändringar för att använda Azure Multi-Factor Authentication. Verifieringsuppgångarna är en del av Azure AD-inloggningshändelsen, som automatiskt begär och bearbetar MFA-utmaningen när det behövs.

## <a name="available-verification-methods"></a>Tillgängliga verifieringsmetoder

När en användare loggar in på ett program eller en tjänst och får en MFA-prompt kan de välja mellan ett av sina registrerade former av ytterligare verifiering. En administratör kan kräva registrering av dessa Azure Multi-Factor Verifieringsmetoder, eller användaren kan komma åt sin egen [Min profil](https://myprofile.microsoft.com) för att redigera eller lägga till verifieringsmetoder.

Följande ytterligare former av verifiering kan användas med Azure Multi-Factor Authentication:

* Microsoft Authenticator-appen
* OATH-maskinvarutoken
* SMS
* Röstsamtal

## <a name="how-to-enable-and-use-azure-multi-factor-authentication"></a>Aktivera och använda Azure Multi-Factor Authentication

Användare och grupper kan aktiveras för Azure Multi-Factor Autentisering för att fråga efter ytterligare verifiering under inloggningshändelsen. [Standardvärden för säkerhet](../fundamentals/concept-fundamentals-security-defaults.md) är tillgängliga för alla Azure AD-klienter för att snabbt aktivera användningen av Microsoft Authenticator-appen för alla användare.

För mer detaljerade kontroller kan [principer för villkorlig åtkomst](../conditional-access/overview.md) användas för att definiera händelser eller program som kräver MFA. Dessa principer kan tillåta regelbundna inloggningshändelser när användaren finns i företagsnätverket eller en registrerad enhet, men fråga efter ytterligare verifieringsfaktorer när du är fjärransluten eller på en personlig enhet.

![Översiktsdiagram över hur villkorlig åtkomst fungerar för att skydda inloggningsprocessen](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Nästa steg

Mer information om licensiering finns i [Funktioner och licenser för Azure Multi-Factor Authentication](concept-mfa-licensing.md).

Om du vill se MFA i praktiken aktiverar du Azure Multi-Factor Authentication för en uppsättning testanvändare i följande självstudiekurs:

> [!div class="nextstepaction"]
> [Aktivera Azure Multi-Factor Authentication](tutorial-mfa-applications.md)
