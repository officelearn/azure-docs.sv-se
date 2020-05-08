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
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 61c0202d12756201d45fe829078d84382e44b54e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584303"
---
# <a name="authentication-vs-authorization"></a>Autentisering kontra auktorisering

I den här artikeln definieras autentisering och auktorisering och du får en kort beskrivning av hur du kan använda Microsoft Identity Platform för att autentisera och auktorisera användare i webbappar, webb-API: er eller appar som anropar skyddade webb-API: er. Om du ser en term som du inte är bekant med kan du prova vår [ord lista](developer-glossary.md) eller våra [Microsoft Identity Platform-videor](identity-videos.md) som beskriver grundläggande begrepp.

## <a name="authentication"></a>Autentisering

**Autentisering** är en process för att bevisa att du är den som du säger. Autentisering förkortas ibland AuthN. Microsoft Identity Platform implementerar [OpenID Connect](https://openid.net/connect/) -protokollet för att hantera autentisering.

## <a name="authorization"></a>Auktorisering

**Auktorisering** innebär att ge en autentiserad part behörighet att göra något. Den anger vilka data du får åtkomst till och vad du kan göra med dessa data. Auktorisering förkortas ibland AuthZ. Microsoft Identity Platform implementerar [OAuth 2,0](https://oauth.net/2/) -protokollet för hantering av auktorisering.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Autentisering och auktorisering med hjälp av Microsoft Identity Platform

I stället för att skapa appar som var och en upprätthåller sin egen användar namn och lösen ords information, vilket innebär en hög administrativ börda när du behöver lägga till eller ta bort användare över flera appar, kan appar delegera det ansvaret till en centraliserad identitets leverantör.

Azure Active Directory (Azure AD) är en centraliserad identitets leverantör i molnet. Genom att delegera autentisering och auktorisering till det möjliggör scenarier som villkorliga åtkomst principer som kräver att en användare befinner sig på en speciell plats, användningen av Multi-Factor Authentication, samt att göra det möjligt för en användare att logga in en gång och sedan loggas in automatiskt till alla webbappar som delar samma centrala katalog. Den här funktionen kallas **enkel inloggning (SSO)**.

Microsoft Identity Platform fören klar autentisering och auktorisering för programutvecklare genom att tillhandahålla identitet som en tjänst, med stöd för bransch standard protokoll som OAuth 2,0 och OpenID Connect, samt bibliotek med öppen källkod för olika plattformar som hjälper dig att snabbt komma igång med att koda. Det gör det möjligt för utvecklare att skapa program som loggar in alla Microsoft-identiteter, Hämta token för att anropa [Microsoft Graph](https://developer.microsoft.com/graph/), andra Microsoft API: er eller API: er som utvecklare har skapat. Mer information finns i [utvecklingen av Microsoft Identity Platform](about-microsoft-identity-platform.md).

## <a name="next-steps"></a>Nästa steg

För andra ämnen som behandlar grundläggande autentisering och auktorisering:

* Se [säkerhetstoken](security-tokens.md) för att lära dig hur åtkomsttoken, uppdaterings-token och ID-token används vid autentisering och auktorisering.
* Se [program modell](application-model.md) för att lära dig mer om hur du registrerar ditt program så att det kan integreras med Microsoft Identity Platform.
* Se [appens inloggnings flöde](app-sign-in-flow.md) för att lära dig om inloggnings flödet för webben, skriv bordet och mobila appar i Microsoft Identity Platform.

Om du vill veta mer om de protokoll som Microsoft Identity Platform implementerar:

* Se [OAuth 2,0-och OpenID Connect-protokoll på Microsoft Identity Platform](active-directory-v2-protocols.md) för mer information om OpenID Connect och OAuth 2,0-standarder.
* Mer information om hur Microsoft Identity Platform stöder enkel inloggning finns i [SAML-protokoll för enkel inloggning](single-sign-on-saml-protocol.md) .
