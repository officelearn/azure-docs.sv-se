---
title: Grunderna i Azures identitetshantering | Microsoft Docs
description: Molnbaserade identiteter är nu det bästa sättet att behålla kontrollen över, och insyn i, hur och när användare får åtkomst till företagets program och data.
keywords: ''
author: eross-msft
manager: mtillman
ms.reviewer: jsnow
ms.author: lizross
ms.date: 08/07/2018
ms.topic: overview
ms.prod: ''
ms.service: active-directory
ms.component: fundamentals
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 327cecd129befb56c33d7fcf2d59ee5b58a18549
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42022869"
---
# <a name="fundamentals-of-azure-identity-management"></a>Grunderna i Azures identitetshantering

Då allt fler digitala företagsresurser finns utanför företagets nätverk, i molnet och på enheter, blir det nödvändigt med en bra molnbaserad identitets- och hanteringslösning. Molnbaserade identiteter är nu det bästa sättet att behålla kontrollen över, och insyn i, hur och när användare får åtkomst till företagets program och data.

Microsoft har skyddat molnbaserade identiteter i mer än ett årtionde och nu är dessa skyddssystem tillgängliga för dig, med [Azure Active Directory (AD)](active-directory-whatis.md). Med Azure AD kan företagsadministratörer enkelt garantera användar- och administratörsansvar med bättre säkerhet och styrning än någonsin.

Azure AD Premium är en molnbaserad identitets- och åtkomsthanteringslösning med avancerade skyddsfunktioner som möjliggör en säker identitet för alla appar, identitetsskydd (förbättrad med [Microsoft Intelligence Security Graph](https://www.microsoft.com/security/intelligence)), och Privileged Identity Management. Azure AD Premium är inte bara ett övervaknings- eller rapporteringsverktyg, utan kan även skydda användarnas identiteter i realtid och gör att du kan skapa riskbaserade, anpassningsbara åtkomstprinciper för att skydda organisationens data.

Titta på den här korta videon för en snabb översikt över identitetshantering och skydd i Azure AD:
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Microsoft tillhandahåller inte bara en identitet som du kan använda överallt, utan också en uppsättning verktyg för att automatisera, skydda och hantera IT i organisationen. Trots alla molntjänster finns det fortfarande ett behov av att hantera och kontrollera IT-uppgifter som samtal till supportavdelningen för lösenordsåterställning, hantering av användargrupper och programförfrågningar. Och för att göra det ännu mer komplicerat – medarbetare tar nu med sig sina personliga enheter till arbetet och använder SaaS-program, så att behålla kontrollen över program i företagets datacenter och offentliga molnplattformar är en tuff utmaning.

[!INCLUDE [identity](../../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Anslut lokal Active Directory till Azure AD och Office 365
Organisationer som har gjort stora investeringar i lokal Active Directory kan utöka investeringarna till molnet genom att integrera lokala kataloger med Azure AD i [hybrididentitetshantering](https://aka.ms/aadframework). När du gör detta blir dina användare mer produktiva genom att ge en gemensam identitet för åtkomst till resurser oavsett plats. Användare och organisationer kan sedan använda enkel inloggning (SSO) för att komma åt både lokala resurser och molntjänster som Office 365.

[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) är det enda verktyg du behöver för att få integrationen gjord. Med Azure AD Connect får du funktioner för att stödja dina identitetssynkroniseringsbehov och det ersätter äldre versioner av identitetsintegrationsverktyg som DirSync och Azure AD Sync. Med Azure AD Connect möjliggörs identitetshantering och -synkronisering mellan lokalt och Azure AD genom:

- Synkronisering – Den här komponenten är ansvarig för att skapa användare, grupper och andra objekt. Den är också ansvarig för att se till att identitetsinformationen för dina lokala användare och grupper matchar molnet. Tillbakaskrivning av lösenord kan också aktiveras för att synkronisera lokala kataloger när en användare uppdaterar sitt lösenord i Azure AD.
- Autentisering – När Azure AD är ditt nya kontrollplan är autentisering grunden för molnåtkomst. Att välja rätt autentiseringsmetod är ett viktigt beslut i konfigurationen av en Azure AD-hybrididentitetslösning. Läs [den här guiden](https://aka.ms/auth-options) när du ska välja mellan molnautentisering layoutstödlinje/direktautentisering eller federerad autentisering (AD FS) för organisationen.
- Hälsoövervakning – [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) kan tillhandahålla robust övervakning och en central plats på Azure-portalen för att övervaka den här aktiviteten.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Öka produktiviteten och minska supportkostnader med självbetjänings- och enkel inloggning-miljöer

Medarbetare är mer produktiva när de har ett enda användarnamn och lösenord att komma ihåg och ett konsekvent gränssnitt på alla enheter. De sparar också tid när de kan utföra självbetjäningsuppgifter som att [återställa ett glömt lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) eller begära åtkomst till ett program utan att vänta på hjälp från supporten.

Azure AD [utökar lokal Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) till molnet, så att användare kan använda sitt primära organisationskonto för både domänanslutna enheter, företagsresurser och alla webb- och SaaS-program de behöver för att få jobbet gjort. Utöver att inte behöva komma ihåg flera uppsättningar av användarnamn och lösenord kan användarnas programåtkomst också automatiskt etableras (eller avetableras) utifrån deras gruppmedlemskap i organisationen och deras status som medarbetare. Och du kan kontrollera den åtkomsten för galleriappar eller för dina egna lokala appar som du har utvecklat och publicerat genom [Azure AD-programproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="manage-and-control-access-to-corporate-resources"></a>Hantera och kontrollera åtkomst till företagsresurser
Microsofts lösningar för identitets- och åtkomsthantering hjälper IT-avdelningen att skydda åtkomsten till program och resurser i företagets datacenter och i molnet, vilket möjliggör ytterligare nivåer av verifiering som [multifaktorautentisering](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) och [principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). Övervakning av misstänkt aktivitet genom avancerad säkerhetsrapportering, -granskning och -avisering hjälper till att minska potentiella säkerhetsproblem.

Principer för villkorlig åtkomst i Azure AD Premium ger dig, företagsadministratören, möjlighet att skapa principbaserade åtkomstregler för alla Azure AD-anslutna program (SaaS-appar, anpassade appar som körs i molnet eller i lokala webbprogram). Azure AD utvärderar dessa principer i realtid och tillämpar dem varje gång en användare försöker komma åt ett program. Med Azures identitetsskyddsprinciper kan du automatiskt vidta åtgärder när misstänkt aktivitet upptäcks. Dessa åtgärder kan vara att blockera åtkomst för användare i farozonen, tillämpa multifaktorautentisering och återställa användarlösenord om autentiseringsuppgifterna verkar ha avslöjats.


## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management

Med [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started), som ingår i Azure Active Directory Premium P2, kan du identifiera, begränsa och övervaka administrativa konton och deras åtkomst till resurser i Azure Active Directory och andra Microsoft-onlinetjänster. Du får även hjälp att administrera administrativ åtkomst på begäran under exakt den tid du behöver.

Privileged Identity Management kan tillämpa administratörsbehörighet på begäran så att administratörer kan begära multifaktorautentisering, tillfällig höjning av behörigheten under förkonfigurerade tidsperioder innan deras konton återgår till normalt användartillstånd.

## <a name="benefits-of-azure-identity"></a>Fördelarna med Azure-identitet

Med Azure-identitetshantering kan du:

-   Skapa och hantera en enda identitet för varje användare i hela företaget och synkronisera användare, grupper och enheter med [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

-   Ge åtkomst med enkel inloggning till programmen, inklusive tusentals förintegrerade SaaS-appar eller ge säker fjärråtkomst till lokala SaaS-appar med [Azure AD-programproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

-   Skydda programåtkomsten genom att tillämpa regelbaserad [multifaktorautentisering](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) får både lokala program och molnprogram.

-   Förbättra användarnas produktivitet med [lösenordsåterställning via självbetjäning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) och grupp- och programåtkomstbegäranden med [MyApps-portalen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help).

-   Dra nytta av den [höga tillgängligheten och tillgängligheten](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications) i en global molnbaserad identitets- och åtkomsthanteringslösning i företagsklass.

## <a name="next-steps"></a>Nästa steg
[Läs mer om lösningar för Azure-identitet](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)
