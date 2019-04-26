---
title: Webb-API-appar i Azure Active Directory
description: Beskriver vilka API-webbprogram och grunderna på protocol flow, registrering och token upphör att gälla för den här apptypen.
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
ms.openlocfilehash: b477171be0f306431b0f7c5965ebede4f4680c22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60249938"
---
# <a name="web-api"></a>Webb-API

Webb-API-appar är webbprogram som måste hämta resurser från ett webb-API. I det här scenariot finns det två identitetstyper av som webbprogrammet kan använda för att autentisera och anropa webb-API:

- **Programidentitet** – det här scenariot använder OAuth 2.0-klientautentiseringsuppgifter för att autentisera sig som programmet och få åtkomst till webb-API. När du använder en Programidentitet, webb-API kan bara identifiera att webbprogrammet anropar det, eftersom webb-får API: et inte någon information om användaren. Om programmet tar emot information om användaren, så skickas den via programprotokollet och är inte signerat av Azure AD. Webb-API litar på att användaren autentiseras för webbprogrammet. Därför är det här mönstret kallas ett betrodda undersystem.
- **Delegerad användaridentitet** – det här scenariot kan utföras på två sätt: OpenID Connect och OAuth 2.0-auktoriseringskod med en konfidentiell klient. Webbprogrammet hämtar en åtkomsttoken för användaren, vilket bevisar för webb-API som användaren har autentiserats till webbprogrammet och att webbprogrammet kunde erhålla en delegerad användaridentitet för att anropa webb-API. Den här åtkomsttoken skickas i begäran till webb-API som ger användaren behörighet och returnerar önskad resurs.

Både program-ID och delegerad identitet användartyper diskuteras i flödet nedan. Den viktigaste skillnaden mellan dem är att måste den delegerade användaridentiteten först skaffa en auktoriseringskod innan användaren kan logga in och få åtkomst till webb-API.

## <a name="diagram"></a>Diagram

![Webbprogram till webb-API-diagram](./media/authentication-scenarios/web_app_to_web_api.png)

## <a name="protocol-flow"></a>Protocol flow

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Ge programmet identitet med hjälp av OAuth 2.0-klientautentiseringsuppgifter

1. En användare är inloggad i Azure AD i webbprogrammet (se den **Webbappar** avsnitt för mer information).
1. Webbprogrammet måste hämta en åtkomsttoken så att den kan autentisera till webb-API och hämta önskad resurs. Den gör en begäran till tokenslutpunkten för Azure AD, ger autentiseringsuppgifter, program-ID och API: er webbprogram ID-URI.
1. Azure AD autentiserar programmet och returnerar en JWT åtkomst-token som används för att anropa webb-API.
1. Över HTTPS använder webbprogrammet returnerade JWT-åtkomsttoken för att lägga till JWT-sträng med en ”ägar” beteckning i auktoriseringshuvudet för begäran till webb-API. Webb-API: verifierar JWT-token och om verifieringen lyckas, returnerar önskad resurs.

### <a name="delegated-user-identity-with-openid-connect"></a>Delegerad användaridentiteter med OpenID Connect

1. En användare är inloggad i ett webbprogram med Azure AD (se webbläsarens webbprogram ovan). Om användaren av webbprogrammet inte har ännu godkänt att så att det webbaserade programmet och anropa webb-API för dess räkning, måste användaren godkänna. Programmet visas de behörigheter som krävs och om någon av dessa finns på administratörsnivå normal användare i katalogen inte kommer att kunna godkänna. Förfarandet medgivande gäller endast för program för flera innehavare, inte enskild klient program, som programmet har redan behörighet. När användaren är inloggad, fick webbprogrammet ett ID-token med information om användaren, samt en auktoriseringskod.
1. Med den auktoriseringskod som utfärdats av Azure AD kan skickar webbprogrammet en begäran till tokenslutpunkten för Azure AD som innehåller Auktoriseringskoden, information om klientprogrammet (program-ID och omdirigerings-URI) och önskad resurs (program-ID URI för webb-API).
1. Auktoriseringskod och information om webbprogram och webb-API verifieras av Azure AD. Vid lyckad validering returnerar två token i Azure AD: en JWT-token för åtkomst och en uppdatering JWT-token.
1. Över HTTPS använder webbprogrammet returnerade JWT-åtkomsttoken för att lägga till JWT-sträng med en ”ägar” beteckning i auktoriseringshuvudet för begäran till webb-API. Webb-API: verifierar JWT-token och om verifieringen lyckas, returnerar önskad resurs.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Delegerad användaridentiteter med OAuth 2.0-auktoriseringskod

1. En användare är redan inloggad på ett webbprogram, vars autentiseringsmetod är oberoende av Azure AD.
1. Webbprogrammet kräver en auktoriseringskod att hämta en åtkomsttoken, så att den skickar en begäran via webbläsaren till auktoriseringsslutpunkten för Azure AD, vilket ger det program-ID och omdirigerings-URI för det webbaserade programmet efter en lyckad autentisering. Användaren loggar in till Azure AD.
1. Om användaren av webbprogrammet inte har ännu godkänt att så att det webbaserade programmet och anropa webb-API för dess räkning, måste användaren godkänna. Programmet visas de behörigheter som krävs och om någon av dessa finns på administratörsnivå normal användare i katalogen inte kommer att kunna godkänna. Den här medgivande gäller både enstaka och flera innehavare program. Enskild klient-om kan en administratör utföra admin medgivande till medgivande för sina användare. Detta kan göras med hjälp av den `Grant Permissions` knappen i den [Azure-portalen](https://portal.azure.com). 
1. När användaren har godkänt får webbprogrammet auktoriseringskod som krävs för att hämta en åtkomsttoken.
1. Med den auktoriseringskod som utfärdats av Azure AD kan skickar webbprogrammet en begäran till tokenslutpunkten för Azure AD som innehåller Auktoriseringskoden, information om klientprogrammet (program-ID och omdirigerings-URI) och önskad resurs (program-ID URI för webb-API).
1. Auktoriseringskod och information om webbprogram och webb-API verifieras av Azure AD. Vid lyckad validering returnerar två token i Azure AD: en JWT-token för åtkomst och en uppdatering JWT-token.
1. Över HTTPS använder webbprogrammet returnerade JWT-åtkomsttoken för att lägga till JWT-sträng med en ”ägar” beteckning i auktoriseringshuvudet för begäran till webb-API. Webb-API: verifierar JWT-token och om verifieringen lyckas, returnerar önskad resurs.

## <a name="code-samples"></a>Kodexempel

Se kodexempel för webbprogram till webb-API-scenarier. Och kom tillbaka ofta – nya exempel läggs ofta. Web [program till webb-API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Appregistrering

Om du vill registrera ett program med Azure AD v1.0 slutpunkten, se [registrera en app med Azure AD v1.0 slutpunkten](quickstart-v1-add-azure-ad-app.md).

* Enskild klient – för både programidentiteten och delegerade användaren identitet fall kan webbappen och webb-API måste registreras i samma katalog i Azure AD. Webb-API kan konfigureras för att exponera en uppsättning behörigheter som används för att begränsa webbprogrammets åtkomst till dess resurser. Om en delegerad användaridentitetstypen används webbprogrammet måste välja önskade behörigheter från den **behörigheter för andra program** nedrullningsbara menyn i Azure-portalen. Det här steget krävs inte om identitet programtyp som används.
* Flera innehavare-först webbprogrammet har konfigurerats för att ange de behörigheter som krävs ska fungera. Den här listan över behörigheter som krävs visas i en dialogruta när en användare eller administratör i målmappen ger du ditt medgivande till programmet, vilket gör dem tillgängliga för deras organisation. Vissa program kräver endast användarnivå behörigheter, som alla användare i organisationen kan godkänna. Andra program som kräver på administratörsnivå som en användare i organisationen inte kan godkänna. Endast en directory-administratören kan samtycka till program som kräver den här behörighetsnivån. När användaren eller administratören godkänner, registreras webbappen och webb-API både i sin katalog.

## <a name="token-expiration"></a>Giltighetstid för token

När webbprogrammet använder dess auktoriseringskod för att hämta en JWT åtkomsttoken, emot tas även en uppdatering JWT-token. Uppdateringstoken kan användas för att autentiseras på nytt användaren utan att behöva logga in igen när åtkomsttoken upphör att gälla. Den här uppdateringstoken används sedan för att autentisera användaren, vilket innebär att en ny åtkomsttoken och uppdateringstoken.

## <a name="next-steps"></a>Nästa steg

- Läs mer om andra [programtyper och scenarier](app-types.md)
- Lär dig mer om Azure AD [grunder](authentication-scenarios.md)
