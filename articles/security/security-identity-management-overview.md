---
title: Azure säkerhetsfunktioner som hjälper med identity management | Microsoft Docs
description: " Den här artikeln innehåller en översikt över grundläggande Azure säkerhetsfunktioner som hjälper till med Identitetshantering. Microsoft identitets- och lösningar hjälp IT skydda åtkomst till program och resurser i företagets datacenter och i molnet, aktivera nivåer av verifiering, till exempel Multifaktorautentisering och villkorlig åtkomst principer. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 8763f1dca110a43586619c09f5d25c340c177b09
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300665"
---
# <a name="azure-identity-management-security-overview"></a>Översikt över säkerheten i Azure identity management
Microsoft identitets- och lösningar hjälp IT skydda åtkomst till program och resurser i företagets datacenter och i molnet. Dessa skydd gör nivåer av verifiering, till exempel Multifaktorautentisering och principer för villkorlig åtkomst. Misstänkt aktivitet med avancerad säkerhet reporting, granskning och aviseringar hjälper dig att minimera potentiella säkerhetsproblem. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) ger enkel inloggning (SSO) till tusentals moln programvara som en tjänst (SaaS)-appar och åtkomst till webbprogram som du kör lokalt.

Genom att utnyttja säkerhetsfördelarna med Azure Active Directory (Azure AD), kan du:

* Skapa och hantera en enstaka identitet för varje användare inom företaget hybrid synkronisera användare, grupper och enheter.
* Ange SSO-åtkomst till program, inklusive tusentals förintegrerade SaaS-appar.
* Aktivera åtkomst programsäkerhet genom att tillämpa regelbaserad Multifaktorautentisering för både lokala program och molnprogram.
* Etablera säker fjärråtkomst till lokala webbprogram via Azure AD Application Proxy.

Syftet med den här artikeln är att ge en översikt av grundläggande Azure säkerhetsfunktioner som hjälper till med Identitetshantering. Vi kan också innehålla länkar till artiklar som ger information om varje funktion så kan du läsa mer.  

Artikeln fokuserar på följande Azure Identity management grundfunktionerna:

* Enkel inloggning
* Omvänd proxy
* Multi-Factor Authentication
* Säkerhetsövervakning, aviseringar och machine learning-baserade rapporter
* Hantering av konsumentidentitet och åtkomst
* Enhetsregistrering
* Privileged identity management
* Identitetsskydd
* Hybrididentitetshantering

## <a name="single-sign-on"></a>Enkel inloggning
SSO innebär att du får tillgång till alla program och resurser som du behöver göra affärer, genom att logga in bara en gång med ett enda användarkonto. När du är inloggad du har åtkomst till alla program som du behöver, utan som krävs för att autentisera (Skriv till exempel ett lösenord) en andra gång.

Många organisationer förlita sig på SaaS-program, till exempel Office 365 och rutan Salesforce för produktiviteten för användaren. Tidigare IT-personal som behövs för att skapa och uppdatera användarkonton i varje SaaS-program separat, och var användarna tvungna att ett lösenord för varje SaaS-program.

Azure AD utökar lokala Active Directory-miljöer i molnet, så att användarna kan använda sina primära organisationskonto för att logga in inte bara till deras domänanslutna enheter och företagets resurser, men också att webb- och SaaS-program de behöver för sitt arbete.

Inte bara användare behöver inte hantera flera uppsättningar av användarnamn och lösenord kan du etablera eller avetablera programåtkomst automatiskt, baserat på deras organisationens grupper och deras anställningsstatus. Azure AD introducerar säkerhet och styrning åtkomstkontroll som du centralt hantera användarnas åtkomst i SaaS-program.

Läs mer:

* [Översikt över enkel inloggning](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../active-directory/manage-apps/what-is-single-sign-on.md)
* [Integrera Azure Active Directory enkel inloggning med SaaS-appar](../active-directory/manage-apps/configure-single-sign-on-portal.md)

## <a name="reverse-proxy"></a>Omvänd proxy
Azure AD Application Proxy kan du publicera lokala program som [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) platser [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx), och [IIS](http://www.iis.net/)-baserade appar i ditt privata nätverk och ger säker åtkomst till användare utanför nätverket. Application Proxy ger fjärråtkomst och enkel inloggning för många typer av lokala webbprogram med tusentals SaaS-program som har stöd för Azure AD. Anställda kan logga in till appar från hem på sina egna enheter och autentisera via denna molnbaserade proxy.

Läs mer:

* [Aktivera Azure AD Application Proxy](../active-directory/manage-apps/application-proxy-enable.md)
* [Publicera program med Azure AD Application Proxy](../active-directory/active-directory-application-proxy-publish.md)
* [Enkel inloggning med Application Proxy](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Arbeta med villkorlig åtkomst](../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Azure Multi-Factor Authentication är en autentiseringsmetod som kräver att mer än en verifieringsmetod och lägger till ett kritiskt andra säkerhetslager till användarinloggningar och transaktioner. Multi-Factor Authentication hjälper dig att skydda åtkomst till data och program och uppfyller efterfrågan från användarna för en process för enkel inloggning. Den ger stark autentisering via en mängd alternativ för verifiering: telefonsamtal, textmeddelanden, eller mobilapp meddelanden eller verifieringskoder och tredje parts OAuth-token.

Läs mer:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Vad är Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Hur Azure Multi-Factor Authentication fungerar](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Säkerhetsövervakning, aviseringar och machine learning-baserade rapporter
Säkerhetsövervakning, aviseringar och machine learning-baserade rapporter som identifierar inkonsekventa åtkomstmönster kan hjälpa dig att skydda din verksamhet. Du kan använda Azure AD-åtkomst och användning rapporter för att få insyn i integritet och säkerheten för din organisations katalog. Med den här informationen kan fastställa en katalogadministratör bättre där möjliga säkerhetsrisker kan ligga så att de kan planera på lämpligt sätt du minskar riskerna.

I Azure-portalen rapporter som hör till följande kategorier:

* **Avvikelseidentifiering rapporter**: innehålla logga in händelser som vi avvikande. Vårt mål är att se till att du är medveten om sådan aktivitet och gör att du kan avgöra om en händelse är misstänkt.
* **Integrerad rapporter från Application**: ge insikter om hur molnprogram används i din organisation. Azure AD möjliggör integrering med tusentals molnprogram.
* **Felrapporter**: ange fel som kan uppstå när du etablerar konton till externa program.
* **Användarspecifika rapporter**: Visa inloggningsaktivitet enhetsdata för en viss användare.
* **Aktivitetsloggar**: innehåller en post på alla granskade händelser inom de senaste 24 timmarna, senaste 7 dagarna eller senaste 30 dagarna, och ändringar av aktiviteten och aktiviteten för återställning och registrering av lösenord.

Läs mer:

* [Visa åtkomst- och användningsrapporterna](../active-directory/active-directory-view-access-usage-reports.md)
* [Kom igång med Azure Active Directory reporting](../active-directory/active-directory-reporting-getting-started.md)
* [Guide för rapportering i Azure Active Directory](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Hantering av konsumentidentitet och åtkomst
Azure AD B2C är en hög tillgänglighet, globala identity management-tjänsten för konsumentinriktade program som kan skalas till flera hundra miljoner identiteter. Den kan integreras över mobila plattformar och webbaserade plattformar. Dina användare kan logga in på alla program via anpassningsbara upplevelser genom att använda sina befintliga sociala konton eller genom att skapa nya autentiseringsuppgifter.

Tidigare programutvecklare som ville registrera kunder och logga in dem i sina program tvungna att skriva egen kod. Och de använde lokala databaser eller system för att lagra användarnamn och lösenord. Azure AD B2C erbjuder organisationen ett bättre sätt att integrera identitetshanteringen i program med hjälp av en säker, standardbaserad plattform och en stor uppsättning utökningsbara principer.

När du använder Azure AD B2C kan kan dina användare registrera dig för ditt program med hjälp av sina befintliga sociala konton (Facebook, Google, Amazon, LinkedIn) eller genom att skapa nya autentiseringsuppgifter (e-postadress och lösenord eller användarnamn och lösenord).

Läs mer:

* [Vad är Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C preview: registrera och logga in användare i dina program](../active-directory-b2c/active-directory-b2c-overview.md)
* [Azure Active Directory B2C Preview: Typer av program](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Enhetsregistrering
Azure AD device registration är grunden för enhetsbaserad [villkorlig åtkomst](../active-directory/active-directory-conditional-access-device-registration-overview.md) scenarier. När en enhet är registrerad innehåller enhetsregistrering för Azure AD en identitet som används för att autentisera enheten när en användare loggar in till enheten. Den autentiserade enheten och attributen för enheten kan sedan användas för att tillämpa principer för villkorlig åtkomst för program som finns i molnet och lokalt.

När den kombineras med en lösning för hantering av mobila enheter, till exempel Intune, uppdateras attributen i Azure AD med ytterligare information om enheten. Du kan sedan skapa regler för villkorlig åtkomst som säkerställer att åtkomsten från enheter som uppfyller dina krav för säkerhet och efterlevnad.

Läs mer:

* [Kom igång med Azure AD device registration](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Automatisk enhetsregistrering med Azure AD för Windows-domänanslutna enheter](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Konfigurera automatisk registrering av Windows-domänanslutna enheter med Azure AD](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Privileged identity management
Med Azure AD Privileged Identity Management kan du hantera, styr och övervaka Privilegierade identiteter och åtkomst till resurser i Azure AD samt andra Microsoft online services, till exempel Office 365 och Microsoft Intune.

Användarna måste ibland att utföra Privilegierade åtgärder i Azure eller Office 365 resurser eller i andra SaaS-appar. Detta behov innebär ofta att organisationer behöver ge användarna permanent privilegierad åtkomst i Azure AD. Sådan åtkomst är en allt större säkerhetsrisk för molnbaserade resurser eftersom organisationer inte tillräckligt övervaka vad användarna gör med sina administratörsprivilegier. Om ett användarkonto med privilegierad åtkomst äventyras, kan dessutom som en överträdelse påverka organisationens övergripande säkerheten för molnet. Azure AD Privileged Identity Management hjälper till att minska denna risk.

Med Azure AD Privileged Identity Management kan du:

* Se vilka användare som är administratörer för Azure AD.
* Aktivera på begäran, just-in-time (JIT) administratörsbehörighet för Microsoft-tjänster som Office 365 och Intune.
* Hämta rapporter om administratören åtkomsthistorik och ändringar i administratörstilldelningar.
* Få meddelanden om åtkomst till en privilegierad roll.

Läs mer:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Roller i Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-roles.md)
* [Azure AD Privileged Identity Management: Så att lägga till eller ta bort en användarroll](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identitetsskydd
Azure AD Identity Protection är en security-tjänst som tillhandahåller en samlad vy riskhändelser och potentiella sårbarheter som påverkar din organisations identiteter. Identitetsskydd drar nytta av de befintliga avvikelseidentifiering funktionerna i Azure AD, som är tillgängliga via Azure AD avvikande aktivitet rapporter. Identitetsskydd introducerar också nya risk händelsetyper som kan identifiera avvikelser i realtid.

Läs mer:

* [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: Azure AD och Identity visa: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Hybrididentitetshantering
Microsoft-metoden till identitet ska vara lokalt och i molnet, skapar en enda användaridentitet för autentisering och auktorisering för alla resurser, oavsett plats.

Läs mer:

* [Vitboken för hybrid identity](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Azure AD-teamets blogg](https://blogs.technet.microsoft.com/ad/)
