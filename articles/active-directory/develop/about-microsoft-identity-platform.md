---
title: Utveckling av Microsofts identitetsplattform - Azure
description: Lär dig mer om Microsoft identity platform, en utveckling av Azure Active Directory (Azure AD) identitetstjänst och utvecklarplattform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 12/09/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.openlocfilehash: 8714b7a96197cb4a59b29bada31b5559961bf8e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78300221"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Utvecklingen av Microsofts identitetsplattform

Microsofts identitetsplattform är en vidareutveckling av utvecklarplattformen för Azure Active Directory (Azure AD). Det gör det möjligt för utvecklare att skapa program som loggar in användare, få token att anropa API:er, till exempel Microsoft Graph, eller API:er som utvecklare har byggt. Den består av en autentiseringstjänst, bibliotek med öppen källkod, programregistrering och konfiguration (via en utvecklarportal och program-API), fullständig utvecklardokumentation, snabbstartsexempel, kodexempel, självstudier, instruktionsguider och annat utvecklarinnehåll. Microsoft Identity-plattformen stöder branschstandardprotokoll som OAuth 2.0 och OpenID Connect.

Hittills har de flesta utvecklare arbetat med Azure AD v1.0-plattformen för att autentisera arbets- och skolkonton (som etablerats av Azure AD) genom att begära token från Azure AD v1.0-slutpunkten med Hjälp av Azure AD Authentication Library (ADAL), Azure portal för programregistrering och konfiguration samt Microsoft Graph API för programmatisk programkonfiguration.

Med den enhetliga Microsoft-identitetsplattformen (v2.0) kan du skriva kod en gång och autentisera alla Microsoft-identiteter i ditt program. För flera plattformar rekommenderas det fullt stödda OPEN-Source Microsoft Authentication Library (MSAL) för användning mot identitetsplattformens slutpunkter. MSAL är enkel att använda, ger stora SSO-upplevelser (Single Sign-on) för användarna, hjälper dig att uppnå hög tillförlitlighet och prestanda och utvecklas med hjälp av Microsoft Secure Development Lifecycle (SDL). När du anropar API:er kan du konfigurera ditt program för att dra nytta av inkrementellt medgivande, vilket gör att du kan fördröja begäran om samtycke för mer invasiva scope tills programmets användning garanterar detta vid körning.  MSAL stöder också Azure Active Directory B2C, så att dina kunder använder sina önskade sociala, företag eller lokala kontoidentiteter för att få enkel inloggning till dina program och API:er.

Med Microsoft identity-plattformen utökar du räckvidden till den här typen av användare:

- Arbets- och skolkonton (Azure AD-etablerade konton)
- Personliga konton (till exempel Outlook.com eller Hotmail.com)
- Dina kunder som tar med egen e-post eller social identitet (till exempel LinkedIn, Facebook, Google) via MSAL och Azure AD B2C

Du kan använda Azure-portalen för att registrera och konfigurera ditt program och använda Microsoft Graph API för programmatisk programkonfiguration.

Uppdatera din ansökan i din egen takt. Program som skapats med ADAL-bibliotek stöds fortfarande. Blandade programportföljer, som består av program byggda med ADAL och program byggda med MSAL-bibliotek, stöds också. Det innebär att program som använder den senaste ADAL och den senaste MSAL kommer att leverera SSO i hela portföljen, som tillhandahålls av den delade tokencachen mellan dessa bibliotek. Program som uppdateras från ADAL till MSAL behåller användarens inloggningstillstånd vid uppgradering.

## <a name="microsoft-identity-platform-experience"></a>Microsoft Identity-plattformsgränssnittet

I diagrammet nedan visas Microsoft Identity-gränssnittet på hög nivå, bland annat appregistreringsfunktioner, SDK:er, slutpunkter och identiteter som stöds.

![Microsoft Identity-plattformen i dag](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Erfarenhet av appregistrering

Azure portal **[App registreringar](https://go.microsoft.com/fwlink/?linkid=2083908)** erfarenhet är en portal upplevelse för att hantera alla program som du har integrerat med Microsoft identity platform. Om du har använt application registration portalen, börja använda Azure portal app registrering erfarenhet istället.

För integrering med Azure AD B2C (vid autentisering av sociala eller lokala identiteter) måste du registrera ditt program i en Azure AD B2C-klientorganisation. Den här upplevelsen är också en del av Azure-portalen.

Använd [program-API:et](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0) för att program som är integrerade med Microsofts identitetsplattform konfigureras på ett program som konfigurerar alla Microsoft-identiteter.

### <a name="msal-libraries"></a>MSAL-bibliotek

Du kan använda MSAL-biblioteket för att skapa program som autentiserar alla Microsoft-identiteter. MSAL-biblioteken i .NET och JavaScript är allmänt tillgängliga. MSAL-bibliotek för iOS och Android är i förhandsversion och lämpliga för användning i en produktionsmiljö. Vi ger samma produktionsnivå stöd för MSAL bibliotek i förhandsversion som vi gör för versioner av MSAL och ADAL som är allmänt tillgängliga.

Du kan också använda MSAL-biblioteken för att integrera ditt program med Azure AD B2C.

Bibliotek på serversidan för att skapa webbappar och webb-API:er är allmänt tillgängliga: [ASP.NET](https://docs.microsoft.com/aspnet/overview) och [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Slutpunkt för Microsoft-identitetsplattform

Microsoft identity platform (v2.0) slutpunkt är nu OIDC-certifierad. Det fungerar med Microsoft Authentication Libraries (MSAL) eller något annat standardkompatibelt bibliotek. Den implementerar läsbara ändamål för människor, i enlighet med branschstandarder.

## <a name="next-steps"></a>Nästa steg

Läs mer om v1.0 och v2.0.

* [Översikt över Microsofts identitetsplattform (v2.0)](v2-overview.md)
* [Översikt över Azure Active Directory för utvecklare (v1.0)](../azuread-dev/v1-overview.md)
