---
title: Azure-säkerhetsfunktioner som hjälper till med identitetshantering | Microsoft-dokument
description: " Den här artikeln innehåller en översikt över de grundläggande Azure-säkerhetsfunktionerna som hjälper till med identitetshantering. Microsofts identitets- och åtkomsthanteringslösningar hjälper IT att skydda åtkomsten till program och resurser i företagets datacenter och in i molnet, vilket möjliggör ytterligare valideringsnivåer, till exempel multifaktorautentisering och villkorlig åtkomst Politik. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: f61b6193a0d2082296a17128b41d7220f9b7e05f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565901"
---
# <a name="azure-identity-management-security-overview"></a>Säkerhetsöversikt över Azure Identity Management

 Identitetshantering är processen att autentisera och godkänna [säkerhetsobjekt](/windows/security/identity-protection/access-control/security-principals). Det handlar också om att kontrollera information om dessa huvudmän (identiteter). Säkerhetsobjekt (identiteter) kan omfatta tjänster, program, användare, grupper osv. Microsofts lösningar för identitets- och åtkomsthantering hjälper IT att skydda åtkomsten till program och resurser i företagets datacenter och in i molnet. Ett sådant skydd möjliggör ytterligare valideringsnivåer, till exempel principer för multifaktorautentisering och villkorlig åtkomst. Genom att övervaka misstänkt aktivitet genom avancerad säkerhetsrapportering, granskning och aviseringar kan du minska potentiella säkerhetsproblem. [Azure Active Directory Premium](/azure/active-directory/active-directory-editions) ger enkel inloggning (SSO) till tusentals molnprogram som en tjänst (SaaS) appar och tillgång till webbappar som du kör lokalt.
 
Genom att dra nytta av säkerhetsfördelarna med Azure Active Directory (Azure AD) kan du:

* Skapa och hantera en enda identitet för varje användare i hybridföretaget och synkronisera användare, grupper och enheter. 
* Ge SSO åtkomst till dina program, inklusive tusentals förintegrerade SaaS-appar.
* Skydda programåtkomsten genom att tillämpa regelbaserad multifaktorautentisering får både lokala program och molnprogram.
* Etablera säker fjärråtkomst till lokala webbprogram via Azure AD Application Proxy.

Målet med den här artikeln är att ge en översikt över de grundläggande Azure-säkerhetsfunktionerna som hjälper till med identitetshantering. Vi tillhandahåller också länkar till artiklar som ger information om varje funktion så att du kan läsa mer.  

Artikeln fokuserar på följande grundläggande Azure Identity management-funktioner:

* Enkel inloggning
* Omvänd proxy
* Multifaktorautentisering
* Rollbaserad åtkomstkontroll (RBAC)
* Säkerhetsövervakning, aviseringar och maskininlärningsbaserade rapporter
* Hantering av konsumentidentitet och åtkomst
* Enhetsregistrering
* Privileged Identity Management
* Identitetsskydd
* Hybrididentitetshantering/Azure AD-anslutning
* Azure AD-åtkomstgranskningar

## <a name="single-sign-on"></a>Enkel inloggning

SSO innebär att kunna komma åt alla program och resurser som du behöver göra affärer, genom att logga in endast en gång med ett enda användarkonto. När du har loggat in kan du komma åt alla program du behöver utan att behöva autentisera (till exempel skriva ett lösenord) en andra gång.

Många organisationer förlitar sig på SaaS-program som Office 365, Box och Salesforce för användarproduktivitet. Historiskt sett måste IT-personal skapa och uppdatera användarkonton individuellt i varje SaaS-program, och användarna var tvungna att komma ihåg ett lösenord för varje SaaS-program.

Azure AD utökar lokala Active Directory-miljöer till molnet, vilket gör det möjligt för användare att använda sitt primära organisationskonto för att logga in inte bara på sina domänanslutna enheter och företagsresurser, utan även till alla webb- och SaaS-program som de behöver för sina jobb.

Användarna behöver inte bara hantera flera uppsättningar användarnamn och lösenord, du kan etablera eller avetableringsprogramåtkomst automatiskt, baserat på deras organisationsgrupper och deras medarbetarstatus. Azure AD introducerar säkerhets- och åtkomststyrningskontroller som du centralt kan hantera användarnas åtkomst över SaaS-program.

Läs mer:

* [Översikt över enkel inloggning](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Integrera enkel Azure Active Directory-inloggning med SaaS-appar](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md)

## <a name="reverse-proxy"></a>Omvänd proxy

Med Azure AD Application Proxy kan du publicera lokala program, till exempel [SharePoint-webbplatser,](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)och [IIS-baserade](https://www.iis.net/)appar i ditt privata nätverk och ger säker åtkomst till användare utanför nätverket. Programproxy ger fjärråtkomst och SSO för många typer av lokala webbprogram med de tusentals SaaS-program som Azure AD stöder. Anställda kan logga in på dina appar hemifrån på sina egna enheter och autentisera via den här molnbaserade proxyn.

Läs mer:

* [Aktivera Azure AD-programproxy](/azure/active-directory/manage-apps/application-proxy-enable)
* [Publicera program med Azure AD Application Proxy](/azure/active-directory/active-directory-application-proxy-publish)
* [Enkel inloggning med programproxy](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Arbeta med villkorlig åtkomst](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multifaktorautentisering

Azure Multi-Factor Authentication är en autentiseringsmetod som kräver användning av mer än en verifieringsmetod och lägger till ett kritiskt andra säkerhetslager för användarloggningar och transaktioner. Multifaktorautentisering hjälper till att skydda åtkomsten till data och program samtidigt som användarnas efterfrågan på en enkel inloggningsprocess tillgodoses. Den ger stark autentisering via en rad verifieringsalternativ: telefonsamtal, textmeddelanden eller meddelanden om mobilappar eller verifieringskoder och OAuth-token från tredje part.

Läs mer:

* [Multifaktorautentisering](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Vad är Azure Multi-Factor Authentication?](/azure/active-directory/authentication/multi-factor-authentication)
* [Så fungerar Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="rbac"></a>RBAC

RBAC är ett auktoriseringssystem som bygger på Azure Resource Manager som ger detaljerad åtkomsthantering av resurser i Azure. MED RBAC kan du fjärrstyra åtkomstnivån som användarna har. Du kan till exempel begränsa en användare till att bara hantera virtuella nätverk och en annan användare för att hantera alla resurser i en resursgrupp. Azure innehåller flera inbyggda roller som du kan använda. Följande listar fyra grundläggande inbyggda roller. De första tre gäller för alla resurstyper.

Läs mer:

* [Vad är rollbaserad åtkomstkontroll (RBAC)?](/azure/role-based-access-control/overview)
* [Inbyggda roller för Azure-resurser](/azure/role-based-access-control/built-in-roles)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Säkerhetsövervakning, aviseringar och maskininlärningsbaserade rapporter

Säkerhetsövervakning, aviseringar och maskininlärningsbaserade rapporter som identifierar inkonsekventa åtkomstmönster kan hjälpa dig att skydda ditt företag. Du kan använda Azure AD-åtkomst- och användningsrapporter för att få insyn i integriteten och säkerheten i organisationens katalog. Med den här informationen kan en katalogadministratör bättre avgöra var eventuella säkerhetsrisker kan ligga så att de kan planera för att minska dessa risker på lämpligt sätt.

I Azure-portalen kan rapporter delas in i följande kategorier:

* **Avvikelserapporter:** Innehåller inloggningshändelser som vi fann vara avvikande. Vårt mål är att göra dig medveten om sådan aktivitet och göra det möjligt för dig att avgöra om en händelse är misstänkt.
* **Integrerade programrapporter**: Ge insikter om hur molnprogram används i din organisation. Azure AD erbjuder integrering med tusentals molnprogram.
* **Felrapporter**: Ange fel som kan uppstå när du etablerar konton till externa program.
* **Användarspecifika rapporter**: Visa aktivitetsdata för enheten för en viss användare.
* **Aktivitetsloggar**: Innehåller en post över alla granskade händelser under de senaste 24 timmarna, de senaste 7 dagarna eller de senaste 30 dagarna och gruppaktivitetsändringar och lösenordsåterställning och registreringsaktivitet.

Läs mer:

* [Visa åtkomst- och användningsrapporterna](/azure/active-directory/active-directory-view-access-usage-reports)
* [Komma igång med Azure Active Directory-rapportering](/azure/active-directory/active-directory-reporting-getting-started)
* [Rapporteringsguide för Azure Active Directory](/azure/active-directory/active-directory-reporting-guide)

## <a name="consumer-identity-and-access-management"></a>Hantering av konsumentidentitet och åtkomst

Azure AD B2C är en mycket tillgänglig, global identitetshanteringstjänst för konsumentinriktade program som skalas till hundratals miljoner identiteter. Den kan integreras över mobila plattformar och webbaserade plattformar. Dina konsumenter kan logga in på alla dina program via anpassningsbara upplevelser genom att använda sina befintliga sociala konton eller genom att skapa nya autentiseringsuppgifter.

Tidigare skulle programutvecklare som ville registrera sig kunder och logga in dem på sina program ha skrivit sin egen kod. Och de använde lokala databaser eller system för att lagra användarnamn och lösenord. Azure AD B2C erbjuder din organisation ett bättre sätt att integrera hantering av konsumentidentiteter i program med hjälp av en säker, standardbaserad plattform och en stor uppsättning utökningsbara principer.

När du använder Azure AD B2C kan dina konsumenter registrera sig för dina program genom att använda sina befintliga sociala konton (Facebook, Google, Amazon, LinkedIn) eller genom att skapa nya autentiseringsuppgifter (e-postadress och lösenord eller användarnamn och lösenord).

Läs mer:

* [Vad är Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C Preview: Registrera och logga in användare i dina program](../../active-directory-b2c/overview.md)
* [Förhandsversion av Azure Active Directory B2C: Typer av program](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>Enhetsregistrering

Azure AD-enhetsregistrering är grunden för [enhetsbaserade scenarier för villkorlig åtkomst.](/azure/active-directory/active-directory-conditional-access-device-registration-overview) När en enhet har registrerats förser Azure AD-enhetsregistrering enheten med en identitet som den använder för att autentisera enheten när en användare loggar in. Den autentiserade enheten och attributen för enheten kan sedan användas för att tillämpa principer för villkorlig åtkomst för program som finns i molnet och lokalt.

I kombination med en lösning för hantering av mobila enheter, till exempel Intune, uppdateras enhetsattributen i Azure AD med ytterligare information om enheten. Du kan sedan skapa regler för villkorlig åtkomst som tillämpar åtkomst från enheter för att uppfylla dina standarder för säkerhet och efterlevnad.

Läs mer:

* [Komma igång med registrering av Azure AD-enheter](/azure/active-directory/active-directory-conditional-access-device-registration-overview)
* [Automatisk enhetsregistrering med Azure AD för Windows-domänanslutna enheter](/azure/active-directory/active-directory-conditional-access-automatic-device-registration)
* [Konfigurera automatisk registrering av Windows-domänanslutna enheter med Azure AD](/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Med Azure AD Privileged Identity Management kan du hantera, kontrollera och övervaka dina privilegierade identiteter och åtkomst till resurser i Azure AD samt andra Microsoft-onlinetjänster, till exempel Office 365 och Microsoft Intune.

Användare behöver ibland utföra privilegierade åtgärder i Azure- eller Office 365-resurser eller i andra SaaS-appar. Det här behovet innebär ofta att organisationer måste ge användarna permanent privilegierad åtkomst i Azure AD. Sådan åtkomst är en växande säkerhetsrisk för molnbaserade resurser, eftersom organisationer inte kan övervaka vad användarna gör tillräckligt med administratörsbehörigheten. Om ett användarkonto med privilegierad åtkomst äventyras kan dessutom en överträdelse påverka organisationens övergripande molnsäkerhet. Azure AD Privileged Identity Management hjälper till att minska den här risken.

Med Azure AD Privileged Identity Management kan du:

* Se vilka användare som är Azure AD-administratörer.
* Aktivera administrationsåtkomst på begäran och bara i tid (JIT) till Microsoft-tjänster som Office 365 och Intune.
* Hämta rapporter om administratörsåtkomsthistorik och ändringar i administratörstilldelningar.
* Få aviseringar om åtkomst till en privilegierad roll.

Läs mer:

* [Vad är Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Tilldela Azure AD-katalogroller i PIM](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identitetsskydd

Azure AD Identity Protection är en säkerhetstjänst som ger en konsoliderad vy över riskidentifieringar och potentiella sårbarheter som påverkar organisationens identiteter. Identity Protection drar nytta av befintliga Azure AD-avvikelseidentifieringsfunktioner, som är tillgängliga via Azure AD Anomalous Activity-rapporter. Identity Protection introducerar också nya typer av riskidentifiering som kan identifiera avvikelser i realtid.

Läs mer:

* [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview)
* [Kanal 9: Azure AD och Identity Show: Förhandsversion av identitetsskydd](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Hybrididentitetshantering/Azure AD-anslutning

Microsofts identitetslösningar omfattar både lokala och molnbaserade funktioner, skapar en enda användaridentitet för autentisering och auktorisering till alla resurser, oavsett plats. Vi kallar det för hybrididentitet. Azure AD Connect är Microsoft-verktyget som har utformats för att uppfylla och uppnå dina hybrididentitetsmål. På så sätt kan du erbjuda en gemensam identitet för dina användare för Office 365- och Azure SaaS-program som är integrerade med Azure AD. Den tillhandahåller följande funktioner:

* Synkronisering
* AD FS och federationsintegration
* Passera genom autentisering
* Hälsoövervakning

Läs mer:

* [Vitt papper för hybrididentitet](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Azure AD-teamblogg](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Azure AD-åtkomstgranskningar

Med åtkomstgranskningar i Azure Active Directory (Azure AD) kan organisationer effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och priviligierade rolltilldelningar.

Läs mer:

* [Azure AD-åtkomstgranskningar](../../active-directory/governance/access-reviews-overview.md)
* [Hantera användare med Azure AD-åtkomstgranskningar](../../active-directory/governance/access-reviews-overview.md)
