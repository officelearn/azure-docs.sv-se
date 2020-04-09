---
title: Översikt över Microsoft identity platform (v2.0) – Azure
description: Lär dig mer om Slutpunkten och plattformen för Microsoft Identity Platform (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 2e5bbbd311d71f2925e86ae756b36de7194aa9fb
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886253"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Översikt över Microsofts identitetsplattform (v2.0)

Microsofts identitetsplattform är en vidareutveckling av utvecklarplattformen för Azure Active Directory (Azure AD). Det gör det möjligt för utvecklare att skapa program som loggar in alla Microsoft-identiteter och få token att anropa Microsoft API:er, till exempel Microsoft Graph, eller API:er som utvecklare har byggt. Microsofts identitetsplattform består av:

- **OAuth 2.0 och OpenID Connect standardkompatibel autentiseringstjänst** som gör det möjligt för utvecklare att autentisera alla Microsoft-identiteter, inklusive:
  - Arbets- eller skolkonton (etableras via Azure AD)
  - Personliga Microsoft-konton (till exempel Skype, Xbox och Outlook.com)
  - Sociala eller lokala konton (via Azure AD B2C)
- **Bibliotek med öppen källkod**: Microsoft Authentication Libraries (MSAL) och stöd för andra standardkompatibla bibliotek
- **Programhanteringsportal:** En registrerings- och konfigurationsupplevelse som är inbyggd i Azure-portalen, tillsammans med alla dina andra Azure-hanteringsfunktioner.
- **Api för programkonfiguration och PowerShell**: som möjliggör programmatisk konfiguration av dina program via Microsoft Graph API och PowerShell, så att du kan automatisera dina DevOps-uppgifter.
- **Utvecklarinnehåll:** konceptuell och referensdokumentation, snabbstartsexempel, kodexempel, självstudier och instruktioner.

För utvecklare erbjuder Microsofts identitetsplattform sömlös integrering i innovationer inom identitets- och säkerhetsutrymmet, till exempel lösenordslös autentisering, steguppautentisering och villkorlig åtkomst.  Du behöver inte implementera sådana funktioner själv: program som är integrerade med Microsofts identitetsplattform utnyttjar sådana innovationer internt.

Med Microsofts identitetsplattform kan du skriva kod en gång och nå alla användare. Du kan skapa en app en gång och få den att fungera på många plattformar, eller skapa en app som fungerar som en klient samt ett resursprogram (API).

## <a name="getting-started"></a>Komma igång

Det behöver inte vara svårt att arbeta med identiteter. 

Titta på en [video på Microsofts identitetsplattform](identity-videos.md) för att lära dig grunderna. 

Välj ett [scenario](authentication-flows-app-scenarios.md) som gäller för dig – varje scenariosökväg har en snabbstart och en översiktssida för att komma igång på några minuter:

- [Skapa en ensidesapp](scenario-spa-overview.md)
- [Skapa en webbapp som loggar in användare](scenario-web-app-sign-user-overview.md)
- [Skapa en webbapp som anropar webb-API:er](scenario-web-app-call-api-overview.md)
- [Skapa ett skyddat webb-API](scenario-protected-web-api-overview.md)
- [Skapa ett webb-API som anropar webb-API:er](scenario-web-api-call-api-overview.md)
- [Skapa en skrivbordsapp](scenario-desktop-overview.md)
- [Skapa en app för daemon](scenario-daemon-overview.md)
- [Skapa en mobilapp](scenario-mobile-overview.md)

I följande diagram beskrivs vanliga scenarier för autentiseringsappar – använd det som referens när du integrerar Microsofts identitetsplattform med din app.

[![Programscenarier i Microsofts identitetsplattform](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om grundläggande autentiseringsbegrepp rekommenderar vi att du börjar med följande avsnitt:

- [Autentiseringsflöden och programscenarier](authentication-flows-app-scenarios.md)
- [Grundläggande om autentisering](authentication-scenarios.md)
- [Huvudansvariga för program och tjänster](app-objects-and-service-principals.md)
- [Publik](v2-supported-account-types.md)
- [Behörigheter och samtycke](v2-permissions-and-consent.md)
- [ID-token och](id-tokens.md) [åtkomsttoken](access-tokens.md)

Skapa ett datarikt program som anropar [Microsoft Graph](https://docs.microsoft.com/graph/overview).

När du är redo att starta appen i en **produktionsmiljö**läser du igenom följande metodtips:

- [Aktivera inloggning](msal-logging.md) i ditt program.
- Aktivera telemetri i ditt program.
- Aktivera [proxyservrar och anpassa HTTP-klienter](msal-net-provide-httpclient.md).
- Testa integreringen genom att följa checklistan för integrering av [Microsoft-identitetsplattform](identity-platform-integration-checklist.md).

## <a name="learn-more"></a>Läs mer

Om du planerar att skapa ett kundinriktadt program som loggar in i sociala och lokala identiteter läser du [översikten över Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).
