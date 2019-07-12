---
title: Microsoft identity-plattformen (v2.0) översikt – Azure
description: Läs mer om Microsoft identity-plattformen (v2.0) slutpunkt och plattform.
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93e4337f0593933a4e877f391df8132a9b2cd4af
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702688"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Översikt över Microsoft identity-plattformen (v2.0)

Microsofts identitetsplattform är en vidareutveckling av utvecklarplattformen för Azure Active Directory (Azure AD). Det hjälper utvecklare att bygga program som loggar in alla Microsoft-identiteter och hämta token för att anropa Microsoft APIs, till exempel Microsoft Graph eller API: er som utvecklare har byggt. Microsoft identity-plattformen består av:

- **OAuth 2.0 och OpenID Connect standard-kompatibla autentiseringstjänst** som ger utvecklare möjlighet att autentisera alla Microsoft-identitet, inklusive:
  - Arbets- eller skolkonto konton (tillhandahålls genom Azure AD)
  - Personliga Microsoft-konton (till exempel Skype, Xbox och Outlook.com)
  - Sociala eller lokala konton (via Azure AD B2C)
- **Bibliotek med öppen källkod**: Microsoft Authentication Libraries (MSAL) och stöd för andra standardkompatibel bibliotek
- **Program-hanteringsportalen**: En registrering och konfigurering upplevelse som bygger på Azure-portalen, tillsammans med alla dina andra Azure management-funktioner.
- **Programkonfiguration API och PowerShell**: vilket gör att programkonfiguration av dina program via REST-API (Microsoft Graph och Azure Active Directory Graph 1.6) och PowerShell, så att du kan automatisera dina DevOps-uppgifter.
- **Developer innehåll**: konceptuell och referera till dokumentation, Snabbstart exempel, kodexempel, självstudier och instruktionsguider.

För utvecklare erbjuder Microsoft identity-plattformen sömlös integrering i innovationer i området identitet och säkerhet, till exempel konfiguration av lösenordsfri autentisering, Step Up autentisering och Villkorsbaserad åtkomst.  Du behöver inte implementera sådan funktion själv: program som är integrerade med Microsofts identitetsplattform internt dra nytta av sådana innovationer.

Med Microsoft identity-plattformen, kan du skriver koden en gång och nå alla användare. Du kan skapa en app på en gång och har den arbeta över många plattformar, eller skapa en app som fungerar som en klient som resursprogrammet (API).

## <a name="getting-started"></a>Komma igång

Det behöver inte vara svårt att arbeta med identiteter. Välj ett scenario som passar dig – varje scenario sökväg har en Snabbstart och en översiktssida för att komma igång på bara några minuter:

- [Skapa en ensidesapp](scenario-spa-overview.md)
- [Skapa en webbapp som loggar in användare](scenario-web-app-sign-user-overview.md)
- [Skapa en webbapp som anropar webb-API: er](scenario-web-app-call-api-overview.md)
- [Skapa en skyddad webb-API](scenario-protected-web-api-overview.md)
- [Skapa en webb-API som anropar webb-API: er](scenario-web-api-call-api-overview.md)
- [Skapa en skrivbordsapp](scenario-desktop-overview.md)
- [Skapa en daemon-app](scenario-daemon-overview.md)
- [Skapa en mobilapp](scenario-mobile-overview.md)

I följande tabell visas vanliga autentiseringsscenarier i appen – Använd den som referens när du integrerar Microsoft identity-plattformen med din app.

[![Programscenarier i Microsoft identity-plattformen](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer information om grundbegreppen för autentisering, rekommenderar vi att du börjar med följande avsnitt:

- [Grundläggande om autentisering](authentication-scenarios.md)
- [Program och tjänstens huvudnamn](app-objects-and-service-principals.md)
- [målgrupper](v2-supported-account-types.md)
- [Behörigheter och samtycke](v2-permissions-and-consent.md)
- [ID-token](id-tokens.md) och [åtkomsttoken](access-tokens.md)

Skapa ett dataintensivt program som anropar [Microsoft Graph](https://docs.microsoft.com/graph/overview).

När du är redo att starta appen i en **produktionsmiljö**, granska dessa metodtips:

- [Aktivera loggning](msal-logging.md) i ditt program.
- Aktivera telemetri i programmet.
- Aktivera [proxyservrar och anpassa HTTP-klienter](msal-net-provide-httpclient.md).
- Testa din integrering genom att följa den [Microsoft identity-plattformen integration checklista](identity-platform-integration-checklist.md).

## <a name="learn-more"></a>Läs mer

Om du planerar att skapa en kundinriktad program som loggar in sociala och lokala identiteter, finns i den [översikt över Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).
