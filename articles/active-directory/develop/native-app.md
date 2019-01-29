---
title: Inbyggda appar i Azure Active Directory
description: Beskriver vad som är inbyggda appar och grunderna på protocol flow, registrering och token upphör att gälla för den här apptypen.
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
ms.openlocfilehash: af3c75364346a1e2e91c0c6d942df5c47b16ffed
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096661"
---
# <a name="native-apps"></a>Inbyggda appar

Inbyggda appar är program som anropar ett webb-API för en användares räkning. Det här scenariot är byggt på beviljandetypen för OAuth 2.0 auktorisering kod med en offentlig klient enligt beskrivningen i avsnitt 4.1 i den [OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749). Internt program hämtar en åtkomsttoken för användaren med hjälp av OAuth 2.0-protokollet. Den här åtkomst-token som sedan skickas i begäran till webb-API som ger användaren behörighet och returnerar önskad resurs.

## <a name="diagram"></a>Diagram

![Internt program för att webb-API-Diagram](./media/authentication-scenarios/native_app_to_web_api.png)

## <a name="protocol-flow"></a>Protocol flow

Om du använder AD-Autentiseringsbibliotek, hanteras de flesta av protokolldetaljer som beskrivs nedan för dig, som popup-fönster i webbläsaren, tokencachelagring och hantering av uppdateringstoken.

1. Använd en webbläsare som popup internt program gör en begäran till slutpunkten för auktorisering i Azure AD. Den här begäran innehåller program-ID och omdirigerings-URI: N för internt program som visas i Azure-portalen och program-ID-URI för webb-API. Om användaren inte har redan har loggat in uppmanas de att logga in igen
1. Azure AD autentiserar användaren. Om det är ett program med flera innehavare och medgivande som krävs för att använda programmet, måste användaren tillstånd om de inte redan gjort det. Efter att godkänna och efter lyckad autentisering, utfärdar Azure AD ett auktorisering kod svar tillbaka till klientprogrammets omdirigerings-URI.
1. När Azure AD utfärdar ett auktorisering kod svar tillbaka till omdirigeringen-URI, klientprogrammet stoppar webbläsare interaktion och extraherar Auktoriseringskoden från svaret. Med den här auktoriseringskod klientprogrammet skickar en begäran till tokenslutpunkten för Azure AD som innehåller Auktoriseringskoden, information om klientprogrammet (program-ID och omdirigerings-URI) och önskad resurs (program-ID-URI för den webb-API).
1. Auktoriseringskod och information om klient-API för program- och verifieras av Azure AD. Vid lyckad validering returnerar två token i Azure AD: en JWT-token för åtkomst och en uppdatering JWT-token. Dessutom kan returnerar Azure AD grundläggande information om användare, till exempel deras Visa namn och klient-ID.
1. Över HTTPS använder klientprogrammet returnerade JWT-åtkomsttoken för att lägga till JWT-sträng med en ”ägar” beteckning i auktoriseringshuvudet för begäran till webb-API. Webb-API: verifierar JWT-token och om verifieringen lyckas, returnerar önskad resurs.
1. När åtkomsttoken upphör att gälla får klientprogrammet ett felmeddelande som anger att användaren behöver för att autentisera igen. Om programmet har en giltig uppdateringstoken kan användas den för att få en ny åtkomsttoken utan att fråga användaren att logga in igen. Om uppdatera-token upphör att gälla, måste programmet för interaktiv autentisering användaren igen.

> [!NOTE]
> Uppdateringstoken som utfärdats av Azure AD kan användas för åtkomst till flera resurser. Om du har ett klientprogram som har behörighet att anropa två webb-API: er kan till exempel uppdateringstoken användas för att hämta en åtkomsttoken till andra webb-API samt.

## <a name="code-samples"></a>Kodexempel

Se kodexempel för internt program till webb-API-scenarier. Och kom tillbaka ofta – vi lägga till nya exempel ofta. [Internt program till webb-API](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Appregistrering

Om du vill registrera ett program med Azure AD v1.0 slutpunkten, se [registrera en app med Azure AD v1.0 slutpunkten](quickstart-v1-add-azure-ad-app.md).

* Enskild klient - internt program och webb-API måste vara registrerade i samma katalog i Azure AD. Webb-API kan konfigureras för att exponera en uppsättning behörigheter som används för att begränsa internt program åtkomst till dess resurser. Klientprogrammet sedan markerar önskade behörigheter i listrutan ”behörigheter till andra program” i Azure-portalen.
* Flera innehavare - först internt program bara registrerad i utvecklaren eller utgivarens katalog. Andra har internt program konfigurerats för att ange de behörigheter som krävs ska fungera. Den här listan över behörigheter som krävs visas i en dialogruta när en användare eller administratör i målmappen ger du ditt medgivande till programmet, vilket gör dem tillgängliga för deras organisation. Vissa program kräver endast användarnivå behörigheter, som alla användare i organisationen kan godkänna. Andra program som kräver på administratörsnivå som en användare i organisationen inte kan godkänna. Endast en directory-administratören kan samtycka till program som kräver den här behörighetsnivån. När användaren eller administratören godkänner, registreras endast webb-API i sin katalog. 

## <a name="token-expiration"></a>Giltighetstid för token

När internt program använder dess auktoriseringskod för att hämta en JWT åtkomsttoken, emot tas även en uppdatering JWT-token. Uppdateringstoken kan användas för att autentisera användaren igen utan att behöva logga in igen när åtkomsttoken upphör att gälla. Den här uppdateringstoken används sedan för att autentisera användaren, vilket innebär att en ny åtkomsttoken och uppdateringstoken.

## <a name="next-steps"></a>Nästa steg

- Läs mer om andra [programtyper och scenarier](app-types.md)
- Lär dig mer om Azure AD [grunder](authentication-scenarios.md)
