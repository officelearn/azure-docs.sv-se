---
title: Autentisering mot auktorisering | Azure
titleSuffix: Microsoft identity platform
description: Lär dig grunderna för autentisering och auktorisering i Microsoft Identity Platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 095bc5594cf9b6eaf0df7929ea83c25a401793e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86252121"
---
# <a name="authentication-vs-authorization"></a>Autentisering mot auktorisering

I den här artikeln definieras autentisering och auktorisering och du får en kort beskrivning av hur du kan använda Microsoft Identity Platform för att autentisera och auktorisera användare i webbappar, webb-API: er eller appar som anropar skyddade webb-API: er. Om du ser en term som du inte är bekant med kan du prova vår [ord lista](developer-glossary.md) eller våra [Microsoft Identity Platform-videor](identity-videos.md) som beskriver grundläggande begrepp.

## <a name="authentication"></a>Autentisering

**Autentisering** är en process för att bevisa att du är den som du säger. Autentisering förkortas ibland AuthN. Microsoft Identity Platform implementerar [OpenID Connect](https://openid.net/connect/) -protokollet för att hantera autentisering.

## <a name="authorization"></a>Auktorisering

**Auktorisering** innebär att ge en autentiserad part behörighet att göra något. Den anger vilka data du får åtkomst till och vad du kan göra med dessa data. Auktorisering förkortas ibland AuthZ. Microsoft Identity Platform implementerar [OAuth 2,0](https://oauth.net/2/) -protokollet för hantering av auktorisering.

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>Autentisering och auktorisering med Microsoft Identity Platform

I stället för att skapa appar som var och en upprätthåller sin egen användar namn och lösen ords information, vilket innebär en hög administrativ börda när du behöver lägga till eller ta bort användare över flera appar, kan appar delegera det ansvaret till en centraliserad identitets leverantör.

Azure Active Directory (Azure AD) är en centraliserad identitets leverantör i molnet. Genom att delegera autentisering och auktorisering till det kan du använda scenarier som principer för villkorlig åtkomst som kräver att en användare befinner sig på en speciell plats, användningen av [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) (kallas ibland för TVÅFAKTORAUTENTISERING eller 2fa), samt att göra det möjligt för en användare att logga in en gång och sedan loggas in automatiskt på alla webbappar som delar samma centrala katalog. Den här funktionen kallas **enkel inloggning (SSO)**.

Microsoft Identity Platform fören klar auktorisering och autentisering för programutvecklare genom att tillhandahålla identitet som en tjänst, med stöd för bransch standard protokoll som OAuth 2,0 och OpenID Connect, samt bibliotek med öppen källkod för olika plattformar som hjälper dig att snabbt komma igång med att koda. Det gör det möjligt för utvecklare att skapa program som loggar in alla Microsoft-identiteter, Hämta token för att anropa [Microsoft Graph](https://developer.microsoft.com/graph/), andra Microsoft API: er eller API: er som utvecklare har skapat.

Följande är en kort jämförelse av de olika protokoll som används av Microsoft Identity Platform:

* **OAuth vs OpenID Connect**: OAuth används för auktorisering och OpenID Connect (OIDC) används för autentisering. OpenID Connect är byggt på OAuth 2,0, så terminologin och flödet liknar de två. Du kan även både autentisera en användare (med OpenID Connect) och få åtkomst behörighet till en skyddad resurs som användaren äger (med OAuth 2,0) i en begäran. Mer information finns i [OAuth 2,0-och OpenID Connect-protokoll](active-directory-v2-protocols.md) och [OpenID Connect-protokoll](v2-protocols-oidc.md).
* **OAuth vs SAML**: OAuth används för auktorisering och SAML används för autentisering. Se [Microsoft Identity Platform och OAuth 2,0 SAML Bearer](v2-saml-bearer-assertion.md) reformat-flöde för mer information om hur de två protokollen kan användas tillsammans för att både autentisera en användare (med SAML) och få åtkomst behörighet till en skyddad resurs (med OAuth 2,0).
* **OpenID Connect vs SAML**: både OpenID Connect och SAML används för att autentisera en användare och används för att aktivera enkel inloggning. SAML-autentisering används ofta med identitets leverantörer som Active Directory Federation Services (AD FS) (ADFS) federerad till Azure AD och används därför ofta i företags program. OpenID Connect används ofta för appar som är helt i molnet, till exempel mobilappar, webbplatser och webb-API: er.

## <a name="next-steps"></a>Nästa steg

För andra ämnen som behandlar grundläggande autentisering och auktorisering:

* Se [säkerhetstoken](security-tokens.md) för att lära dig hur åtkomsttoken, uppdaterings-token och ID-token används vid auktorisering och autentisering.
* Se [program modell](application-model.md) för att lära dig mer om hur du registrerar ditt program så att det kan integreras med Microsoft Identity Platform.
* Se [appens inloggnings flöde](app-sign-in-flow.md) för att lära dig om inloggnings flödet för webben, skriv bordet och mobila appar i Microsoft Identity Platform.

* Mer information om de protokoll som används av Microsoft Identity Platform finns i [OAuth 2,0-och OpenID Connect-protokoll på Microsoft Identity Platform](active-directory-v2-protocols.md).
* Se [single Sign-On SAML-protokoll](single-sign-on-saml-protocol.md) för mer information om hur Microsoft Identity Platform stöder enkel inloggning.
* Mer information om hur du kan implementera enkel inloggning i din app finns i [enkel inloggning till program i Azure Active Directory](../manage-apps/what-is-single-sign-on.md) .
