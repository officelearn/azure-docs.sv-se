---
title: Azure säkerhetsfunktioner som hjälper dig att med identity management | Microsoft Docs
description: " Den här artikeln innehåller en översikt över viktigaste Azure säkerhetsfunktioner som hjälper med identity management. Microsoft identitets- och hantering av lösningar kan IT-avdelningen skydda åtkomsten till program och resurser i företagets datacenter och i molnet, aktiverar extra kontrollnivåer verifiering, till exempel Multifaktorautentisering och villkorlig åtkomst principer. "
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
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As a IT Pro or decision maker I am trying to learn about identity mangement capabilities in Azure
ms.openlocfilehash: d1933e58cae522b03ced29183440e226d47dfdf9
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435064"
---
# <a name="azure-identity-management-security-overview"></a>Säkerhetsöversikt för Azure identity management

 Identitetshantering innebär för autentisering och auktorisering [säkerhetsobjekt](https://docs.microsoft.com/windows/security/identity-protection/access-control/security-principals). Det innebär också att kontrollera information om dessa säkerhetsobjekt (identiteter). Säkerhetsobjekt (identiteter) kan omfatta tjänster, program, användare, grupper osv. Microsoft identitets- och hantering av lösningar kan IT-avdelningen skydda åtkomsten till program och resurser i företagets datacenter och i molnet. Detta skydd kan ytterligare rekommendationsnivåer för verifiering, till exempel Multifaktorautentisering och principer för villkorlig åtkomst. Övervaka misstänkt aktivitet via avancerad säkerhet reporting, granskning och aviseringar hjälper till att minimera potentiella säkerhetsproblem. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) ger enkel inloggning (SSO) till tusentals molnprogramvara som en tjänst (SaaS)-appar och åtkomst till webbappar som du kör lokalt.
 
Genom att utnyttja säkerhetsfördelarna med Azure Active Directory (AD Azure), kan du:

* Skapa och hantera en enda identitet för varje användare i din hybrid-företag, synkronisera användare, grupper och enheter. 
* Ange SSO-åtkomst till dina program, inklusive tusentals förintegrerade SaaS-appar.
* Aktivera säkerhet för åtkomst genom att tillämpa regelbaserad Multi-Factor Authentication för både lokala och molnprogram.
* Etablera säker fjärråtkomst till lokala webbprogram via Azure AD-programproxy.

Målet med den här artikeln är att tillhandahålla en översikt över viktigaste Azure säkerhetsfunktioner som hjälper med identity management. Vi tillhandahåller även länkar till artiklar som ger information om varje funktion så att du kan läsa mer.  

Artikeln fokuserar på följande grundläggande Azure Identity management-funktioner:

* Enkel inloggning
* Omvänd proxy
* Multi-Factor Authentication
* Rollbaserad åtkomstkontroll (RBAC)
* Säkerhetsövervakning, varningar och maskininlärningsbaserade rapporter
* Hantering av konsumentidentitet och åtkomst
* Enhetsregistrering
* Privileged Identity Management
* Identitetsskydd
* Hybrid identity management/Azure AD connect
* Azure AD-åtkomstgranskningar

## <a name="single-sign-on"></a>Enkel inloggning

Enkel inloggning innebär att de har åtkomst till alla program och resurser som du behöver för att göra affärer, genom att logga in med ett enda användarkonto. När du har loggat in, du kan komma åt alla program som du behöver utan att behöva autentisera dig (exempel: Skriv ett lösenord) en andra gång.

Många organisationer använder SaaS-program som Office 365, Box och Salesforce för användarproduktivitet. Historiskt sett har IT-personal som behövs för att skapa och uppdatera användarkonton i varje SaaS-program separat, och var användarna tvungna att komma ihåg ett lösenord för varje SaaS-program.

Azure AD utökar den lokala Active Directory-miljöer i molnet, där användaren kan använda sina primära organisationskonto för inloggning inte bara till sina domänanslutna enheter och företagets resurser, men också att webb- och SaaS-program de behöver för sitt arbete.

Inte bara användare behöver inte hantera flera uppsättningar med användarnamn och lösenord, kan du etablera eller avetablera programåtkomst automatiskt, baserat på deras organisationsgrupper och deras anställningsstatus. Azure AD introducerar säkerhet och styrning åtkomstkontroller som kan du centralt hantera användarnas åtkomst mellan SaaS-program.

Läs mer:

* [Översikt över enkel inloggning](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../active-directory/manage-apps/what-is-single-sign-on.md)
* [Integrera Azure Active Directory enkel inloggning med SaaS-appar](../active-directory/manage-apps/configure-single-sign-on-portal.md)

## <a name="reverse-proxy"></a>Omvänd proxy

Azure AD Application Proxy kan du publicera lokala program, till exempel [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) platser, [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx), och [IIS](http://www.iis.net/)-baserade appar i ditt privata nätverk och ger säker åtkomst till användare utanför nätverket. Programproxyn erbjuder fjärråtkomst och enkel inloggning för många typer av lokala webbprogram med tusentals SaaS-program som har stöd för Azure AD. Anställda kan logga in på dina appar från hem på sina egna enheter och autentisera via den här molnbaserade proxy.

Läs mer:

* [Aktivera Azure AD Application Proxy](../active-directory/manage-apps/application-proxy-enable.md)
* [Publicera program med Azure AD Application Proxy](../active-directory/active-directory-application-proxy-publish.md)
* [Enkel inloggning med programproxy](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Arbeta med villkorlig åtkomst](../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure Multi-Factor Authentication är en autentiseringsmetod som kräver mer än en verifieringsmetod och lägger till ett viktigt andra säkerhetslager till användarinloggningar och transaktioner. Multi-Factor Authentication hjälper till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål för en enkel inloggningsprocess. Du får stark autentisering via en rad alternativ för verifiering: telefonsamtal, SMS, eller aviseringar i mobilappen eller verifieringskoder och OAuth-token från tredje part.

Läs mer:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Vad är Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Hur Azure Multi-Factor Authentication fungerar](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="rbac"></a>RBAC

RBAC är ett auktoriseringssystem som bygger på Azure Resource Manager som ger detaljerad åtkomsthantering av resurser i Azure. RBAC kan du kontrollera åtkomstnivån som användarna har detaljerat. Du kan exempelvis begränsa en användare kan endast hantera virtuella nätverk och en annan användare att hantera alla resurser i en resursgrupp. Azure innehåller flera inbyggda roller som du kan använda. Följande listar fyra grundläggande inbyggda roller. De första tre gäller för alla resurstyper.

Läs mer:

* [Vad är rollbaserad åtkomstkontroll (RBAC)?](../role-based-access-control/overview.md)
* [Inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Säkerhetsövervakning, varningar och maskininlärningsbaserade rapporter

Säkerhetsövervakning, varningar och machine learning-baserade rapporter som visar avvikande åtkomstmönster kan hjälpa dig att skydda ditt företag. Du kan använda Azure AD-åtkomst och användning rapporter för att få insyn i integriteten och säkerheten i din organisations katalog. Med den här informationen kan kan en directory-administratör bättre avgöra var potentiella säkerhetsrisker kanske finns så att de kan planera på lämpligt sätt du minskar riskerna.

I Azure-portalen rapporter som hör till följande kategorier:

* **Avvikelseidentifiering rapporter**: Innehåller inloggningshändelser som vi inte avvikande. Vårt mål är att se till att du är medveten om sådan aktivitet och gör det möjligt att avgöra om en händelse är misstänkta.
* **Integrerade program rapporter**: Ge insikter om hur molnappar används i din organisation. Azure AD erbjuder integrering med tusentals molnprogram.
* **Felrapporter**: Visa fel som kan uppstå när du etablerar konton för att externa program.
* **Användarspecifika rapporter**: Visa inloggningsaktivitet enhetsdata för en viss användare.
* **Aktivitetsloggar**: Innehåller en post för alla granskade händelser inom den senaste 24 timmarna, senaste 7 dagarna eller senaste 30 dagarna, och ändringar av aktivitet och aktiviteten för återställning och registrering av lösenord.

Läs mer:

* [Visa åtkomst- och användningsrapporterna](../active-directory/active-directory-view-access-usage-reports.md)
* [Kom igång med Azure Active Directory-rapportering](../active-directory/active-directory-reporting-getting-started.md)
* [Azure Active Directory-rapporteringsguide](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Hantering av konsumentidentitet och åtkomst

Azure AD B2C är en högtillgänglig, global identitetshanteringstjänst för konsumentorienterade program som kan skalas till hundratals miljoner identiteter. Den kan integreras över mobila plattformar och webbaserade plattformar. Konsumenterna kan logga in på alla dina program via anpassningsbara upplevelser med sina befintliga sociala konton eller genom att skapa nya autentiseringsuppgifter.

Tidigare skulle programutvecklare som ville registrera kunder och in dem i sina program har skriva egen kod. Och de använde lokala databaser eller system för att lagra användarnamn och lösenord. Azure AD B2C erbjuder din organisation ett bättre sätt att integrera identitetshanteringen i program med hjälp av en säker, standardbaserad plattform och en stor uppsättning utökningsbara principer.

När du använder Azure AD B2C kan kan dina användare registrera dig för dina program med hjälp av sina befintliga sociala konton (Facebook, Google, Amazon, LinkedIn) eller genom att skapa nya autentiseringsuppgifter (e-postadress och lösenord eller användarnamn och lösenord).

Läs mer:

* [Vad är Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C preview: Registrera dig och logga in konsumenter i dina program](../active-directory-b2c/active-directory-b2c-overview.md)
* [Azure Active Directory B2C Preview: Typer av program](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Enhetsregistrering

Azure AD device registration är grunden för enhetsbaserad [villkorlig åtkomst](../active-directory/active-directory-conditional-access-device-registration-overview.md) scenarier. När en enhet registreras ger Azure AD-enhetsregistrering enheten med en identitet som används för att autentisera enheten när en användare loggar in. Den autentiserade enheten och attributen för enheten kan sedan användas för att framtvinga principer för villkorlig åtkomst för program som finns i molnet och lokalt.

När de kombineras med en lösning för hantering av mobila enheter, till exempel Intune, uppdateras enhetens egenskaper i Azure AD med ytterligare information om enheten. Du kan sedan skapa regler för villkorlig åtkomst som säkerställer att åtkomsten från enheter uppfyller dina krav för säkerhet och efterlevnad.

Läs mer:

* [Kom igång med Azure AD device registration](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Automatisk enhetsregistrering med Azure AD för Windows-domänanslutna enheter](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Konfigurera automatisk registrering av Windows-domänanslutna enheter med Azure AD](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Med Azure AD Privileged Identity Management kan du hantera, kontrollera och övervaka Privilegierade identiteter och åtkomst till resurser i Azure AD, samt andra Microsoft-onlinetjänster, till exempel Office 365 och Microsoft Intune.

Ibland behöver användare att utföra Privilegierade åtgärder i Azure eller Office 365-resurser eller i andra SaaS-appar. Detta behov innebär ofta att organisationer behöver ge användare permanent privilegierad åtkomst i Azure AD. Sådan åtkomst är en växande säkerhetsrisk för molnbaserade resurser, eftersom organisationer inte kan tillräckligt övervaka vad användarna gör med sina administratörsprivilegier. Om ett användarkonto med privilegierad åtkomst äventyras, kan dessutom att ett intrång påverka organisationens övergripande molnsäkerhet. Azure AD Privileged Identity Management hjälper dig för att minska denna risk.

Med Azure AD Privileged Identity Management kan du:

* Se vilka användare som är Azure AD-administratörer.
* Aktivera på begäran just-in-time (JIT) administrativ åtkomst till Microsoft-tjänster som Office 365 och Intune.
* Få rapporter om administratören åtkomsthistorik och ändringar i administratör tilldelningar.
* Få aviseringar om åtkomst till en privilegierad roll.

Läs mer:

* [Vad är Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md)
* [Tilldela Azure AD-katalogroller i PIM](../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identitetsskydd

Azure AD Identity Protection är en säkerhetstjänst som tillhandahåller en samlad vy över riskhändelser och potentiella problem som påverkar organisationens identiteter. Identitetsskydd drar nytta av befintliga Azure AD avvikelseidentifiering funktioner, som finns tillgängliga via Azure AD avvikande aktivitet rapporter. Identitetsskydd inför nya typer av riskhändelser som kan identifiera avvikelser i realtid.

Läs mer:

* [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: Azure AD och Identity visa: Förhandsversionen av Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Hybrid identity management/Azure AD connect

Microsofts identitetslösningar omfattar både lokala och molnbaserade funktioner, skapar en enda användaridentitet för autentisering och auktorisering till alla resurser, oavsett plats. Vi kallar det för hybrididentitet. Azure AD Connect är Microsoft-verktyget som har utformats för att uppfylla och uppnå dina hybrididentitetsmål. På så sätt kan du erbjuda en gemensam identitet för dina användare för Office 365- och Azure SaaS-program som är integrerade med Azure AD. Den tillhandahåller följande funktioner:

* Synkronisering
* AD FS och federation-integrering
* Autentisering
* Hälsoövervakning

Läs mer:

* [Hybrid identity faktablad](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Azure AD-teamets blogg](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Azure AD-åtkomstgranskningar

Med åtkomstgranskningar i Azure Active Directory (Azure AD) kan organisationer effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och priviligierade rolltilldelningar.

Läs mer:

* [Azure AD-åtkomstgranskningar](../active-directory/governance/access-reviews-overview.md)
* [Hantera användare med Azure AD-åtkomstgranskningar](../active-directory/governance/access-reviews-overview.md)
