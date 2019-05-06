---
title: Utvecklingen av Microsoft identity-plattformen – Azure
description: Läs mer om Microsoft identity-plattformen, en utveckling av Azure Active Directory (Azure AD)-tjänsten och utvecklare identitetsplattformen.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: celested
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0b912b6f3fe42c724468347f9b3a7f0b4efa054
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067956"
---
# <a name="evolution-of-microsoft-identity-platform"></a>Utvecklingen av Microsoft identity-plattformen

Microsoft Identity-plattformen är en utveckling av identitetstjänsten och utvecklingsplattformen Azure Active Directory (Azure AD). Det hjälper utvecklare att bygga program som loggar du in användare och hämta token för att anropa API: er, till exempel Microsoft Graph eller API: er som utvecklare har byggt. Det består av en autentiseringstjänst, bibliotek med öppen källkod, programregistrering och konfiguration (via en developer-portalen och API-program), fullständig utvecklardokumentation, Snabbstart exempel, kodexempel, självstudier, guider, och andra developer-innehåll. Microsoft Identity-plattformen stöder branschstandardprotokoll som OAuth 2.0 och OpenID Connect.

Fram tills nu, har de flesta utvecklare arbetat med Azure AD v1.0-plattformen för att autentisera arbets- och skolkonton (som tillhandahålls av Azure AD) genom att begära token från Azure AD-v1.0 slutpunkten med hjälp av Azure AD-Autentiseringsbiblioteket (ADAL), Azure-portalen för programregistrering- och konfigurations- och Azure AD Graph API för programmässig programkonfiguration.

Med Microsoft identity-plattformen (v2.0), expandera och nå att dessa typer av användare:

- Arbets- och skolkonton konton (Azure AD som etablerats-konton)
- Personliga konton (till exempel Outlook.com eller Hotmail.com)
- Dina kunder som tar med sin egen e-post eller sociala identitet (till exempel LinkedIn, Facebook, Google) via Azure AD B2C-erbjudande

Med Microsofts enhetlig identitetsplattform du skriver koden en gång och autentisera alla Microsoft-identitet i ditt program. Det finns ett helt öppen källkod library, även kallat Microsoft Authentication Library (MSAL) för flera plattformar. MSAL är enkel att använda, ger bra enkel inloggning (SSO) inträffar för dina användare, hjälper dig att uppnå hög tillförlitlighet och prestanda, och har utvecklats med Microsoft Secure Development Lifecycle (SDL). När du anropar API: er, kan du konfigurera ditt program att kunna utnyttja inkrementella medgivande, där du kan fördröja begäran om godkännande för mer inkräktande scope tills programmets användning garanterar detta vid körning.

Du kan använda Azure portal för att registrera och konfigurera ditt program och använder Microsoft Graph API för programmässig programkonfiguration.

Uppdatera ditt program i din egen takt. Program som skapats med ADA-biblioteken fortfarande användas. Blandade programmet portföljer, som består av program som skapats med ADAL och program som skapats med MSAL bibliotek, stöds också. Det innebär att program som använder den senaste ADAL och den senaste MSAL levererar SSO i portfölj, som tillhandahålls av den delade token cachen mellan dessa bibliotek. Program som uppdateras från ADAL till MSAL upprätthåller användartillstånd logga in om du uppgraderar.

## <a name="microsoft-identity-platform-experience"></a>Microsoft Identity-plattformsgränssnittet

I diagrammet nedan visas Microsoft Identity-gränssnittet på hög nivå, bland annat appregistreringsfunktioner, SDK:er, slutpunkter och identiteter som stöds.

![Microsoft Identity-plattformen i dag](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Appupplevelsen för registrering

Azure-portalen **[appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908)** upplevelse är en en Portal för att hantera alla program som du har integrerat med Microsoft identity-plattformen. Om du har använt portalen för registrering av program, från och med hjälp av Azure portal registrering appupplevelsen i stället.

För integrering med Azure AD B2C (när du autentiserar sociala eller lokala identiteter), måste du registrera ditt program i en B2C-klient. Den här upplevelsen är också en del av Azure-portalen.

Den **application API i Microsoft Graph** förhandsvisas just nu. Använd detta API för att programmässigt konfigurera ditt program som är integrerade med Microsoft identity-plattformen för att autentisera alla Microsoft-identitet. Tills detta API är allmänt tillgängligt, bör du använda API: et för Azure AD Graph 1.6 och programmanifestet.

### <a name="msal-libraries"></a>MSAL bibliotek

Du kan använda biblioteket MSAL för att bygga program som autentiserar alla Microsoft-identiteter. MSAL-biblioteken i .NET är allmänt tillgängliga. MSAL-bibliotek för JavaScript, iOS och Android finns i förhandsversion och är lämplig för användning i en produktionsmiljö. Vi tillhandahåller samma nivå produktionssupport för MSAL bibliotek i en förhandsversion som vi gör för versioner av MSAL och ADAL som är allmänt tillgängliga.

Du kan också använda MSAL-bibliotek för att integrera ditt program med Azure AD B2C.

-Serversidans bibliotek för att skapa webbappar och webb-API: er är allmänt tillgängliga: [ASP.NET](https://docs.microsoft.com/aspnet/overview) och [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Microsoft identity-plattformen slutpunkt

Microsoft identity-plattformen (v2.0) slutpunkt är nu OIDC certifierade. Det fungerar med Microsoft Authentication Libraries (MSAL) eller andra standardkompatibel bibliotek. Den implementerar mänskliga läsbara scope, i enlighet med branschstandarder.

## <a name="next-steps"></a>Nästa steg

Läs mer om v1.0 och v2.0.

* [Översikt över Microsoft identity-plattformen (v2.0)](v2-overview.md)
* [Azure Active Directory för utvecklare (v1.0) översikt](v1-overview.md)