---
title: Översikt över Microsoft Identity Platform (v 2.0) – Azure
description: Lär dig mer om slut punkten och plattformen för Microsoft Identity Platform (v 2.0).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbbcd854434bcc085f1b63fb864755dd0e928fc9
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852153"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Översikt över Microsoft Identity Platform (v 2.0)

Microsofts identitetsplattform är en vidareutveckling av utvecklarplattformen för Azure Active Directory (Azure AD). Utvecklare kan skapa program som loggar in alla Microsoft-identiteter och hämta token för att anropa Microsoft API: er, till exempel Microsoft Graph eller API: er som utvecklare har skapat. Microsoft Identity Platform består av:

- **OAuth 2,0 och OpenID Connect standard-kompatibel autentiseringstjänst** som gör det möjligt för utvecklare att autentisera alla Microsoft-identiteter, inklusive:
  - Arbets-eller skol konton (etablerade genom Azure AD)
  - Personliga Microsoft-konton (till exempel Skype, Xbox och Outlook.com)
  - Sociala eller lokala konton (via Azure AD B2C)
- **Bibliotek med öppen källkod**: Microsoft Authentication libraries (MSAL) och stöd för andra standard bibliotek
- **Program hanterings Portal**: En registrerings-och konfigurations upplevelse som skapats i Azure Portal, tillsammans med alla andra hanterings funktioner för Azure.
- **API för program konfiguration och PowerShell**: som tillåter programmerings konfiguration av dina program via REST API (Microsoft Graph och Azure Active Directory Graph 1,6) och PowerShell, så att du kan automatisera dina DevOps-uppgifter.
- **Developer-innehåll**: konceptuell och referens dokumentation, snabb starts exempel, kod exempel, självstudier och instruktions guider.

För utvecklare erbjuder Microsoft Identity Platform sömlös integrering i Nyheter i identitets-och säkerhets utrymmet, till exempel lösenordsbaserad autentisering, stegvis autentisering och villkorlig åtkomst.  Du behöver inte implementera sådana funktioner själv: program som är integrerade med Microsofts identitets plattform har internt möjlighet att utnyttja sådana innovationer.

Med Microsoft Identity Platform kan du skriva kod en gång och komma åt alla användare. Du kan bygga en app en gång och låta den fungera på flera plattformar, eller bygga en app som fungerar som en klient samt ett resurs program (API).

## <a name="getting-started"></a>Komma igång

Det behöver inte vara svårt att arbeta med identiteter. Välj ett [scenario](authentication-flows-app-scenarios.md) som gäller för dig – varje scenario Sök väg har en snabb start och en översikts sida för att komma igång på några minuter:

- [Bygga en app med en enda sida](scenario-spa-overview.md)
- [Bygg en webbapp som loggar in användare](scenario-web-app-sign-user-overview.md)
- [Bygg en webbapp som anropar webb-API: er](scenario-web-app-call-api-overview.md)
- [Bygg en skyddad webb-API](scenario-protected-web-api-overview.md)
- [Bygg ett webb-API som anropar webb-API: er](scenario-web-api-call-api-overview.md)
- [Bygg en app för skriv bord](scenario-desktop-overview.md)
- [Bygg en daemon-app](scenario-daemon-overview.md)
- [Bygg en mobilapp](scenario-mobile-overview.md)

Följande diagram visar vanliga scenarier för autentisering av appar – Använd den som referens när du integrerar Microsoft Identity Platform med din app.

[![Program scenarier i Microsoft Identity Platform](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om grundläggande autentisering, rekommenderar vi att du börjar med följande avsnitt:

- [Autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md)
- [Grundläggande om autentisering](authentication-scenarios.md)
- [Program-och tjänst huvud namn](app-objects-and-service-principals.md)
- [Mål grupper](v2-supported-account-types.md)
- [Behörigheter och tillstånd](v2-permissions-and-consent.md)
- [ID-token](id-tokens.md) och [åtkomsttoken](access-tokens.md)

Bygg ett data omfattande program som anropar [Microsoft Graph](https://docs.microsoft.com/graph/overview).

När du är redo att starta din app i en **produktions miljö**kan du läsa följande metod tips:

- [Aktivera loggning](msal-logging.md) i ditt program.
- Aktivera telemetri i ditt program.
- Aktivera [proxyservrar och anpassa HTTP-klienter](msal-net-provide-httpclient.md).
- Testa din integrering genom att följa [Check lista för Microsoft Identity Platform-integration](identity-platform-integration-checklist.md).

## <a name="learn-more"></a>Lär dig mer

Om du planerar att bygga ett kund program som loggar in på sociala och lokala identiteter kan du läsa mer i [Azure AD B2C översikt](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).
