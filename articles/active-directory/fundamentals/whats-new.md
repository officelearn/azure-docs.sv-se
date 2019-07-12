---
title: Nyheter Viktig information – Azure Active Directory | Microsoft Docs
description: Lär dig vad är nytt med Azure Active Directory, som föråldrade funktioner för de senaste versionsanteckningarna, kända problem, felkorrigeringar och kommande ändringar.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: af215c80148010d526c951e7a5128d6e4622b1cd
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625575"
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?

>Håll dig informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` i din ![RSS-feed läsare ikonen](./media/whats-new/feed-icon-16x16.png) feed läsare.

Azure AD får förbättringar med jämna mellanrum. Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

- Den senaste versionen
- Kända problem
- Felkorrigeringar
- Inaktuell funktion
- Planer för ändringar

Den här sidan uppdateras varje månad, så gå tillbaka till den regelbundet. Om du letar efter objekt som är äldre än sex månader kan du hitta dem i den [Arkiveringsjobb för vad som är nytt i Azure Active Directory](whats-new-archive.md).

---

## <a name="june-2019"></a>Juni 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Ny riskDetections API för Microsoft Graph (offentlig förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** Identity Protection  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Vi är glada att kunna presentera den nya riskDetections API för Microsoft Graph är nu i offentlig förhandsversion. Du kan använda den här nya API: et för att visa en lista över organisationens Identity Protection-relaterade användar- och inloggningsrisk identifieringar. Du kan också använda den här API: et för att mer effektivt fråga din risk-identifieringar, inklusive information om identifiering av typen, status, nivå med mera.

Mer information finns i den [riskerar identifiering av API-referensdokumentation](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nya federerade appar är tillgängliga i appgalleriet för Azure AD - juni 2019

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** 3 part Integration

I juni 2019 har vi lagt till stöd för dessa 22 nya appar med Federation i app-galleriet:

[Azure AD-SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN-klienten](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [ Helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz i bilen Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [ Oracle Cloud infrastruktur-konsolen](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML-autentisering](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx ](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Accessmanager för Oracle Retail-marknadsföring, Oracle Accessmanager för Oracle E-Business Suite, Oracle IDCS för E-Business Suite, Oracle IDCS för PeopleSoft, Oracle IDCS för JD Edwards

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera etablering av användarkonto för dessa nya stöds SaaS-appar

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Övervakning och rapportering

Du kan nu automatisera skapa, uppdatera och ta bort användarkonton för dessa nya integrerade appar:

- [Zooma](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Läs mer om hur du bättre skydda din organisation genom att använda automatiska användarkontoetablering [automatisera användaretablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Visa i realtid förloppet för den Azure AD-etableringstjänsten

**Typ:** Ändrad funktion  
**Tjänstekategori:** App-etablering  
**Produkten kapacitet:** Identitetslivcykelhantering

Vi har uppdaterat Azure AD etableringsupplevelsen för att inkludera en ny förloppsindikator som visar hur långt du befinner dig i processen för användaretablering. Den här uppdaterade upplevelsen tillhandahåller även information om hur många användare som tillhandahålls under den aktuella cykeln, samt hur många användare har etablerats hittills.

Mer information finns i [Kontrollera status för användaretablering](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Företagsanpassning nu visas på Logga ut och fel skärmar

**Typ:** Ändrad funktion  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Användarautentisering

Vi har uppdaterat Azure AD så att din företagsanpassning nu visas på Logga ut och fel skärmar, samt på inloggningssidan. Du behöver göra något för att aktivera den här funktionen, Azure AD använder bara de resurser som du redan har konfigurerat i den **Varumärkesexponering** område i Azure-portalen.

Läs mer om hur du konfigurerar din företagsanpassning [Lägg till företagsanpassning till din organisations Azure Active Directory-sidor](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure Multi-Factor Authentication (MFA) Server är inte längre tillgänglig för nya distributioner

**Typ:** Inaktuell  
**Tjänstekategori:** MFA  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Från och med den 1 juli 2019 erbjuder Microsoft inte längre MFA Server för nya distributioner. Nya kunder som vill kräva multifaktorautentisering i deras organisation, måste nu använda molnbaserade Azure Multi-Factor Authentication. Kunder som har aktiverat MFA Server före 1 juli Se inte en ändring. Du kommer fortfarande att kunna hämta den senaste versionen, hämta framtida uppdateringar och skapa autentiseringsuppgifter för aktivering.

Mer information finns i [komma igång med Azure Multi-Factor Authentication Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Läs mer om molnbaserad Azure Multi-Factor Authentication, [planera en molnbaserad Azure Multi-Factor Authentication-distribution](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Maj 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Tjänsten ändring: Framtida stöd för TLS 1.2 protokoll på Application Proxy-tjänsten

**Typ:** Planera för ändring  
**Tjänstekategori:** App Proxy  
**Produkten kapacitet:** Åtkomstkontroll

För att ge bästa klassens kryptering för våra kunder, vi begränsa åtkomsten till endast TLS 1.2 protokoll på Application Proxy-tjänsten. Den här ändringen distribueras gradvis till kunder som redan bara använder TLS 1.2-protokoll, så att du inte bör se några ändringar.

Utfasning av TLS 1.0 och TLS 1.1 sker på den 31 augusti 2019 men tillhandahåller vi ytterligare i förväg, så att du har tid att förbereda för den här ändringen. Förbereda för den här ändringen se till att din klient-server och webbläsaren servrar kombinationer, inklusive alla klienter som används för att få åtkomst till program som publicerats via programproxy, uppdateras för att använda TLS 1.2-protokollet för att behålla anslutningen till programmet Proxy-tjänsten. Mer information finns i [lägga till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Använda rapporten användningen och insikter för att visa din app-relaterade inloggningsdata

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Övervakning och rapportering

Du kan nu använda rapporten användningen och insikter finns i den **företagsprogram** område i Azure portal, för att få en programcentrerad vy över dina loggar in data, inklusive information om:

- Översta använda appar i organisationen.

- Appar med flest misslyckade inloggningar

- Främsta Inloggningsfel för varje app

Mer information om den här funktionen finns i [användningen och insikter för rapporten i Azure Active Directory-portalen](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatisera din användaretablering molnbaserade appar med hjälp av Azure AD

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Övervakning och rapportering

Följ de här nya självstudiekurserna för att använda Azure AD Provisioning-tjänsten för att automatisera skapandet, borttagning, och uppdatering av användarkonton för följande molnbaserade appar:

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Du kan även följa det här nya [Dropbox självstudien](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), som innehåller information om hur du etablerar gruppobjekt.

Läs mer om hur du bättre skydda din organisation via automatisk användarkontoetablering [automatisera användaretablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Identitet säker poängen är nu tillgängliga i Azure AD (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänstekategori:** Gäller inte  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Du kan nu övervaka och förbättra din identitet säkerhetsposition med hjälp av identiteten skydda poäng funktionen i Azure AD. Identiteten för secure poäng funktionen använder en enda instrumentpanel som hjälper dig att:

- Sakligt mäta din säkerhetsposition identitet, baserat på ett värde mellan 1 och 223.

- Planera för din identitet säkerhetsförbättringar

- Granska framgången för din säkerhetsförbättringar

Läs mer om poäng för identitet säkerhetsfunktion [vad är den säkra poängen identitet i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>App är-registreringar nu tillgängliga (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Utvecklarupplevelse

Den nya [appregistreringar](https://aka.ms/appregistrations) upplevelse är nu allmänt tillgängliga. Den här nya upplevelsen omfattar alla viktiga funktioner som du är bekant med från Azure-portalen och programregistreringsportalen och förbättrat dem. via:

- **Bättre apphantering.** I stället för att se dina appar över olika portalerna, kan du nu se alla dina appar på en plats.

- **Förenklad appregistrering.** Förbättrad navigering erfarenheter förbättringar behörighet val av tidigare är det nu enklare att registrera och hantera dina appar.

- **Mer detaljerad information.** Du hittar mer information om din app, inklusive snabbstartguider och mycket mer.

Mer information finns i [Microsofts identitetsplattform](https://docs.microsoft.com/azure/active-directory/develop/) och [registreringar appupplevelsen är nu allmänt tillgänglig!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blogginlägget om.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nya funktioner som är tillgängliga i riskfylld användare API: et för Identity Protection

**Typ:** Ny funktion  
**Tjänstekategori:** Identity Protection  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Vi har glädjen att meddela att du nu använder API: et för riskfyllda användare att hämta användarnas Riskhistorik, Stäng riskabla användare och för att bekräfta användare som äventyras. Den här ändringen hjälper dig att mer effektivt uppdatera Riskstatus för dina användare och förstå deras Riskhistorik.

Mer information finns i den [riskfylld användare API-referensdokumentation](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nya federerade appar är tillgängliga i appgalleriet för Azure AD - maj 2019

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** 3 part Integration

I maj 2019 har vi lagt till stöd för dessa 21 nya appar med Federation i app-galleriet:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [riktig länkar](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo försäljning engagera](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 globala HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum arbetsplats](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobolt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API molnet](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Kontroll](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Förbättrad grupper skapandet och hanteringen upplevelser i Azure AD-portalen

**Typ:** Ny funktion  
**Tjänstekategori:** Grupphantering  
**Produkten kapacitet:** Samarbete

Vi har gjort förbättringar för grupper-relaterade-upplevelser i Azure AD-portalen. Dessa förbättringar kan administratörer att bättre hantera grupper listor, medlemmar listor, och ange alternativ för att skapa ytterligare.

Förbättringarna innefattar:

- Grundläggande filtrering av Medlemskapstyp och grupptyp.

- Tillägg av nya kolumner, till exempel käll- och e-postadress.

- Möjlighet att välja flera grupper, medlemmar och ägare visas för enkelt borttagning.

- Möjlighet att välja en e-postadress och Lägg till ägare när gruppen skapas.

Mer information finns i [skapa en basgrupp och lägga till medlemmar med hjälp av Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurera en namnprincip för Office 365-grupper i Azure AD-portalen (allmän tillgänglighet)

**Typ:** Ändrad funktion  
**Tjänstekategori:** Grupphantering  
**Produkten kapacitet:** Samarbete

Administratörer kan nu konfigurera en namnprincip för Office 365-grupper, med hjälp av Azure AD-portalen. Den här ändringen hjälper till att tillämpa konsekventa namngivningskonventioner för Office 365-grupper som skapats eller redigerats av användare i din organisation.

Du kan konfigurera namnprincip för Office 365-grupper på två olika sätt:

- Definiera prefix eller suffix som läggs automatiskt till ett gruppnamn.

- Ladda upp en anpassad uppsättning blockerade ord för din organisation, vilket inte är tillåtna i gruppnamn (till exempel ”CEO, lönelistor, HR”).

Mer information finns i [tillämpa en princip för namngivning för Office 365-grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API-slutpunkter är nu tillgängliga för Azure AD-aktivitetsloggar (allmän tillgänglighet)

**Typ:** Ändrad funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering

Vi är glada över att kunna presentera allmän tillgänglighet för Microsoft Graph API-slutpunkter stöd för Azure AD activity logs. Med den här versionen kan du nu använda Version 1.0 av Azure AD granska loggar, samt logga in loggarna API: er.

Mer information finns i [Azure AD audit API översikten över](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Administratörer kan nu använda villkorlig åtkomst för registreringen av kombinerade (offentlig förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** Villkorlig åtkomst  
**Produkten kapacitet:** Identitetssäkerhet och skydd  

Administratörer kan nu skapa principer för villkorlig åtkomst för användning av den kombinerade registreringssidan. Det innebär att tillämpa principer för att tillåta registrering om:

- Användarna är i ett betrott nätverk.

- Användarna är låg risk logga in.

- Användarna är på en hanterad enhet.

- Användare godkänner du användningsvillkoren för organisationens (TOU).

Mer information om villkorlig åtkomst och återställning av lösenord som du kan se den [villkorlig åtkomst för Azure AD kombineras MFA och blogginlägg upplevelse för registrering för lösenordsåterställning](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Läs mer om principer för villkorlig åtkomst för registreringen av kombinerade [principer för villkorlig åtkomst för registrering av kombinerade](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Mer information om Azure AD-termer Använd funktion finns i [Azure Active Directory användningsvillkor Använd funktion](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>April 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-in-refreshed-azure-ad-identity-protection"></a>Nya Azure AD intelligence hotidentifiering är nu tillgängligt i uppdateras Azure AD Identity Protection

**Typ:** Ny funktion  
**Tjänstekategori:** Azure AD Identity Protection  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Azure AD intelligence hotidentifiering är nu tillgängligt i uppdateras Azure AD Identity Protection. Den här nya funktionen hjälper till att ange användaraktivitet som är ovanlig för en viss användare eller som är konsekvent med kända angreppsmönster baserat på Microsofts interna och externa hotinformation.

Mer information om den uppdatera versionen av Azure AD Identity Protection finns i den [fyra större förbättringar i Azure AD Identity Protection finns nu i offentlig förhandsversion](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) blogg och [vad är Azure Active Directory Identitetsskydd (uppdateras)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) artikeln. Mer information om tillgångsinformation hotidentifiering i Azure AD finns i den [riskhändelser för Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) artikeln.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD rättigheten management är nu tillgängliga (offentlig förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** Identity Governance  
**Produkten kapacitet:** Identity Governance

Azure AD rättigheten management nu hjälper i offentlig förhandsversion, kunder att delegera hanteringen av åtkomst-paket, som definierar hur anställda och affärspartner kan begära åtkomst, som måste godkänna och hur länge de har åtkomst. Åtkomst-paket kan hantera medlemskap i Azure AD och Office 365-grupper, rolltilldelningar i enterprise-program och rolltilldelningar för SharePoint Online-platser. Läs mer om rätt management på den [översikt över Azure AD rättigheten management](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Mer information om dessa nyheter Identitetsstyrning för Azure AD-funktioner, inklusive Privileged Identity Management, åtkomstgranskningar och användningsvillkor, finns i [vad är Azure AD Identity styrning?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurera en namnprincip för Office 365-grupper i Azure AD-portalen (offentlig förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** Grupphantering  
**Produkten kapacitet:** Samarbete

Administratörer kan nu konfigurera en namnprincip för Office 365-grupper, med hjälp av Azure AD-portalen. Den här ändringen hjälper till att tillämpa konsekventa namngivningskonventioner för Office 365-grupper som skapats eller redigerats av användare i din organisation.

Du kan konfigurera namnprincip för Office 365-grupper på två olika sätt:

- Definiera prefix eller suffix som läggs automatiskt till ett gruppnamn.

- Ladda upp en anpassad uppsättning blockerade ord för din organisation, vilket inte är tillåtna i gruppnamn (till exempel ”CEO, lönelistor, HR”).

Mer information finns i [tillämpa en princip för namngivning för Office 365-grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD-aktivitetsloggar är nu tillgängliga i Azure Monitor (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering

För att hantera din feedback om visualiseringar med aktivitetsloggar för Azure AD kan presenterar vi en ny funktion för insikter i Log Analytics. Den här funktionen kan du få insikter om din Azure AD-resurser med hjälp av våra interaktiva mallar som kallas arbetsböcker. Dessa fördefinierade arbetsböcker kan ge den informationen till appar eller användare och omfattar:

- **Inloggningar.** Innehåller information om appar och användare, inklusive inloggningsplats, operativsystemet och används eller webbläsarklienten och version och antalet lyckade eller misslyckade inloggningar.

- **Äldre autentisering och Villkorsbaserad åtkomst.** Innehåller information om appar och användare som använder äldre autentisering och Multifaktorautentisering användning som utlöses av principer för villkorlig åtkomst, appar med principer för villkorlig åtkomst, och så vidare.

- **Inloggningsfel analys.** Hjälper dig att avgöra om din inloggningsfel inträffar på grund av en användaråtgärd, problem med principer eller din infrastruktur.

- **Anpassade rapporter.** Du kan skapa nya eller redigera befintliga arbetsböcker för att anpassa funktionen insikter för din organisation.

Mer information finns i [hur du använder Azure Monitor-arbetsböcker för Azure Active Directory-rapporter](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nya federerade appar är tillgängliga i appgalleriet för Azure AD - April 2019

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** 3 part Integration

I April 2019 har vi lagt till stöd för dessa 21 nya appar med Federation i app-galleriet:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [ Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Ansluta](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel ansluta](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (Role-based SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent kapital Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certifikathanteraren](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nya åtkomstgranskningar frekvens alternativ och flera Systemroll

**Typ:** Ny funktion  
**Tjänstekategori:** Åtkomstgranskningar  
**Produkten kapacitet:** Identity Governance

Nya uppdateringar i Azure AD-åtkomstgranskningar kan du:

- Ändra frekvensen för din åtkomst kodgranskningar till **delvis annually**, utöver de tidigare befintliga alternativen för varje vecka, månadsvis, kvartalsvis och årligen.

- Välj flera Azure AD och granska Azure-resursroller när du skapar en enkel åtkomst. I så fall kan alla roller har ställts in med samma inställningar och alla granskare får ett meddelande på samma gång.

Läs mer om hur du skapar en åtkomstgranskning [skapa en åtkomstgranskning av grupper eller program i Azure AD-åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-avisering systemen övergår, Skicka ny information om e-avsändaren för en del kunder

**Typ:** Ändrad funktion  
**Tjänstekategori:** AD Sync  
**Produkten kapacitet:** Plattform

Azure AD Connect håller övergår vår e-avisering systemen potentiellt som visar vissa kunder en ny e-post-avsändare. För att lösa det, måste du lägga till `azure-noreply@microsoft.com` till din organisation att listan eller du kan inte fortsätta att få viktiga aviseringar från dina Office 365, Azure eller Sync-tjänsterna.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>UPN-suffix ändringar är nu klar mellan federerade domäner i Azure AD Connect

**Typ:** Fast  
**Tjänstekategori:** AD Sync  
**Produkten kapacitet:** Plattform

Du kan nu ändra en användares UPN-suffix från en federerade domän till en annan federerade domän i Azure AD Connect. Den här snabbkorrigeringen innebär att du bör inte längre uppstår FederatedDomainChangeError felmeddelande under synkroniseringscykeln eller ta emot ett meddelande e-postmeddelande som anger ”, det går inte att uppdatera det här objektet i Azure Active Directory eftersom attributet [ FederatedUser.UserPrincipalName] är inte giltig. Uppdatera värdet i de lokala katalogtjänsterna ”.

Mer information finns i [felsöka fel under synkronisering](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Ökad säkerhet med hjälp av app protection-baserade principen för villkorlig åtkomst i Azure AD (förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** Villkorlig åtkomst  
**Produkten kapacitet:** Identitetssäkerhet och skydd

App protection-baserad villkorlig åtkomst är nu tillgänglig med hjälp av den **kräver appskydd** princip. Den här nya principen som hjälper dig för att öka säkerheten för din organisation genom att hjälpa att förhindra att:

- Användare får åtkomst till appar utan en licens för Microsoft Intune.

- Användare som att det inte går att hämta en appskyddsprincip för Microsoft Intune.

- Användare får åtkomst till appar utan ett konfigurerat Microsoft Intune-appskyddsprincip.

Mer information finns i [kräva appens skyddsprincip för åtkomst till molnet appen med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nya stödet för Azure AD enkel inloggning och villkorlig åtkomst i Microsoft Edge (förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** Villkorlig åtkomst  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Vi har utökat vår Azure AD-stöd för Microsoft Edge, inklusive att tillhandahålla nya stödet för Azure AD enkel inloggning och villkorlig åtkomst. Om du tidigare har använt Microsoft Intune Managed Browser, kan du nu använda Microsoft Edge i stället.

Mer information om att konfigurera och hantera dina enheter och appar med hjälp av villkorlig åtkomst finns i [kräver hanterade enheter för åtkomst till molnet appen med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) och [kräver godkända klientprogram för molnet åtkomst till appen med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Mer information om hur du hanterar åtkomst med hjälp av Microsoft Edge med Microsoft Intune-principer finns i [hantera Internetåtkomst med en Microsoft Intune-princip-skyddade webbläsare](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Mars 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Identitetsramverk och anpassad princip för stöd i Azure Active Directory B2C är nu tillgänglig (GA)

**Typ:** Ny funktion  
**Tjänstekategori:** B2C – Konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C

Nu kan du skapa anpassade principer i Azure AD B2C, inklusive följande uppgifter, som stöds i skala och under serviceavtal för Azure:

- Skapa och överför anpassad autentisering användaren resor med hjälp av anpassade principer.

- Beskriv användaren resor stegvisa som utbyten mellan Anspråksproviders.

- Definiera villkorlig branchning i utbildning för användaren.

- Omvandla och mappa anspråk för användning i fatta beslut i realtid och kommunikation.

- Använda REST API-aktiverade tjänster i din utbildning för anpassad autentisering-användaren. Till exempel med e-postleverantörer, CRM och upphovsrättsskyddad auktorisering system.

- Federera med identitetsleverantörer som är kompatibla med OpenIDConnect-protokollet. Till exempel med flera innehavare Azure AD, konto-leverantörer via sociala nätverk eller verifiering av två faktorer providers.

Mer information om hur du skapar anpassade principer finns i [kommentarer för utvecklare för anpassade principer i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) och läsa [Alex Simon blogginlägg, inklusive fallstudier](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nya federerade appar är tillgängliga i appgalleriet för Azure AD - mars 2019

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** 3 part Integration

I mars 2019 har vi lagt till stöd för dessa 14 nya appar med Federation i app-galleriet:

[ISEC7 Mobile Exchange ombud](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Förklaring granskning prissystem](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Läs](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool prestanda frågor](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranät](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit vyer](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [Aktivitetsse](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nya Zscaler och Atlassians etablering anslutningar i Azure AD-galleriet – mars 2019

**Typ:** Ny funktion  
**Tjänstekategori:** App-etablering  
**Produkten kapacitet:** 3 part Integration

Automatisera skapa, uppdatera och ta bort användarkonton för följande appar:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler en](https://aka.ms/ZscalerOneProvisioning), [Zscaler två](https://aka.ms/ZscalerTwoProvisioning), [Zscaler tre](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud ](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassians molnet](https://aka.ms/atlassianCloudProvisioning)

Läs mer om hur du bättre skydda din organisation via automatisk användarkontoetablering [automatisera användaretablering för SaaS-program med Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Återställa och hantera dina borttagen Office 365-grupper i Azure AD-portalen

**Typ:** Ny funktion  
**Tjänstekategori:** Grupphantering  
**Produkten kapacitet:** Samarbete

Du kan nu visa och hantera dina Office 365-grupper som har tagits bort från Azure AD-portalen. Den här ändringen hjälper dig att se vilka grupper som är tillgängliga för att återställa, tillsammans med så att du permanent ta bort alla grupper som inte krävs för din organisation.

Mer information finns i [återställning har upphört att gälla eller borttagna grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Enkel inloggning är nu tillgängligt för Azure AD SAML-säkrad lokala appar via programproxy (offentlig förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** App Proxy  
**Produkten kapacitet:** Åtkomstkontroll

Nu kan du ange en enkel inloggning (SSO)-upplevelse för on-premises, SAML-autentiserade appar, tillsammans med fjärråtkomst till de här apparna via programproxy. Läs mer om hur du ställer in SAML SSO med dina lokala [SAML enkel inloggning för lokala program med Application Proxy (förhandsversion)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Klientappar i begäran loopar avbryts för att förbättra tillförlitlighet och användarupplevelse

**Typ:** Ny funktion  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Användarautentisering

Klientappar kan felaktigt utfärda hundratals samma inloggningsbegäranden under en kort tidsperiod. Dessa begäranden oavsett om de är lyckades eller inte, alla bidra till en dålig användarupplevelse och förhöjd arbetsbelastningar för IDP: N, ökar svarstiden för alla användare och minska tillgängligheten för IDP: N.

Den här uppdateringen skickar en `invalid_grant` fel: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` på klientappar som utfärda duplicerade flera gånger under en kort tidsperiod, utanför omfattningen för normal drift. Klientappar som stöta på det här problemet ska visa en interaktiv prompt, eftersom användaren måste logga in igen. Mer information om den här ändringen och om hur du åtgärdar din app om det här felet finns i [vad är nytt för autentisering?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Ny granskningsloggar användarupplevelse nu tillgänglig

**Typ:** Ändrad funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering

Vi har skapat en ny Azure AD **granskningsloggar** sidan för att förbättra läsbarheten och hur du söker efter din information. Se den nya **granskningsloggar** väljer **granskningsloggar** i den **aktivitet** avsnitt av Azure AD.

![Ny granskningsloggar på sidan med exempel info](media/whats-new/audit-logs-page.png)

Mer information om den nya **granskningsloggar** sidan ser [granska aktivitetsrapporter i Azure Active Directory-portalen](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nya varningar och vägledning för att förhindra oavsiktlig administratör utelåsning från felkonfigurerad principer för villkorlig åtkomst

**Typ:** Ändrad funktion  
**Tjänstekategori:** Villkorlig åtkomst  
**Produkten kapacitet:** Identitetssäkerhet och skydd

För att förhindra att administratörer av misstag låser sig av sina egna klienter via felkonfigurerad principer för villkorlig åtkomst, har vi skapat nya varningar och uppdaterade riktlinjer i Azure-portalen. Mer information om nya vägledning finns i [vad är tjänstens beroenden i Azure Active Directory villkorsstyrd åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Förbättrad slutanvändarens användningsvillkor upplevelser för användning på mobila enheter

**Typ:** Ändrad funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning

Vi har uppdaterat våra befintliga villkor för användning upplevelser för att förbättra hur du kan granska och godkänna användningsvillkoren på en mobil enhet. Du kan nu Zooma in och ut, gå tillbaka, ladda ned informationen och välj hyperlänkar. Läs mer om de uppdaterade villkoren för användning, [Azure Active Directory användningsvillkor Använd funktion](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Hämta upplevelse som är tillgängliga för nya Azure AD-aktivitetsloggar

**Typ:** Ändrad funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering

Du kan nu hämta stora mängder aktivitetsloggar direkt från Azure-portalen. Den här uppdateringen kan du:

- Hämta upp till 250 000 rader.

- Få ett meddelande när nedladdningen är klar.

- Anpassa ditt filnamn.

- Fastställa din utdataformat JSON- eller CSV.

Mer information om den här funktionen finns i [snabbstarten: Ladda ned en granskningsrapport med Azure portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Icke-bakåtkompatibel ändring: Uppdateringar till villkorsutvärdering av Exchange ActiveSync (EAS)

**Typ:** Planera för ändring  
**Tjänstekategori:** Villkorlig åtkomst  
**Produkten kapacitet:** Åtkomstkontroll

Vi håller på att uppdatera hur Exchange ActiveSync (EAS) utvärderar följande villkor:

- Användarplats, baserat på land, region eller IP-adress

- Inloggningsrisk

- Enhetsplattform

Om du har tidigare använt dessa villkor i principer för villkorlig åtkomst kan du vara medveten om att villkoret beteende kan ändras. Till exempel om du tidigare använt platsvillkoret för användare i en princip, kanske du upptäcker principen nu hoppas över baserat på platsen för dina användare.

---

## <a name="february-2019"></a>Februari 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurerbara Azure AD SAML-tokenkryptering (offentlig förhandsversion) 

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** SSO

Du kan nu konfigurera alla SAML-appar som stöds för att ta emot krypterad SAML-tokens. När konfigureras och används med en app, krypterar de utgivna SAML-intyg med hjälp av en offentlig nyckel som hämtas från ett certifikat som lagras i Azure AD i Azure AD.

Läs mer om hur du konfigurerar SAML-tokenkryptering, [konfigurera Azure AD-SAML-tokenkryptering](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Skapa en åtkomstgranskning efter grupper eller appar med hjälp av Azure AD-Åtkomstgranskningar

**Typ:** Ny funktion  
**Tjänstekategori:** Åtkomstgranskningar  
**Produkten kapacitet:** Styrning

Du kan nu inkludera flera grupper eller appar i en enda Azure AD-åtkomstgranskning för gruppmedlemskap eller apptilldelning. Åtkomstgranskningar med flera grupper eller appar som är konfigurerade med samma inställningar och alla inkluderade granskare får ett meddelande på samma gång.

Mer information om hur du skapar en åtkomstgranskning med hjälp av Azure AD-Åtkomstgranskningar, finns i [skapa en åtkomstgranskning av grupper eller program i Azure AD-Åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nya federerade appar är tillgängliga i appgalleriet för Azure AD - februari 2019

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** 3 part Integration
 
I februari 2019 har vi lagt till stöd för dessa 27 nya appar med Federation i app-galleriet:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Klicka på behörighet, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [seismisk ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Resurs A dröm](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods integrering molnet](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Knowledge var som helst LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope Data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud av Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp produktivitet plattform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Förbättrad kombinerade MFA/SSPR-registrering

**Typ:** Ändrad funktion  
**Tjänstekategori:** Lösenordsåterställning självbetjäning via  
**Produkten kapacitet:** Användarautentisering

Som svar på feedback från kunder har vi förbättrat den kombinerade MFA/SSPR-registrering förhandsversion upplevelsen, hjälpa användarna att snabbt registrera deras säkerhetsinformation för både MFA och SSPR. 

**Aktivera förbättrad upplevelse för dina användare idag, gör så här:**

1. Som global administratör eller Användaradministratör kan logga in på Azure-portalen och gå till **Azure Active Directory > Inställningar > Hantera inställningar för åtkomst till panelen förhandsversionsfunktioner**. 

2. I den **användare som kan använda förhandsversionen av funktioner för att registrera och hantera säkerhetsinformation – uppdatera** måste du välja att aktivera funktioner för en **vald grupp av användare** eller för **alla användare** .

Under de närmaste veckorna, vi kommer att ta bort möjligheten att aktivera den gamla kombinerade MFA/SSPR-registrering förhandsversion upplevelsen för klienter som inte redan har den aktiverad.

**Följ dessa steg om du vill se om kontrollen tas bort för din klient:**

1. Som global administratör eller Användaradministratör kan logga in på Azure-portalen och gå till **Azure Active Directory > Inställningar > Hantera inställningar för åtkomst till panelen förhandsversionsfunktioner**.  

2. Om den **användare som kan använda funktionerna förhandsgranskning för att registrera och hantera säkerhetsinformation** alternativet är inställt på **ingen**, alternativet tas bort från din klient.

Förhandsgranska upplevelse för användare, oavsett om du tidigare har aktiverat den gamla kombinerade MFA/SSPR-registreringen eller inte, den gamla upplevelsen kommer att stängas av för ett datum i framtiden. På grund av detta föreslår starkt vi att du flyttar till den nya och förbättrade upplevelsen så snart som möjligt.

Läs mer om registrering av förbättrad upplevelse, den [lågfrekvent förbättringar av Azure AD kombineras MFA och återställning av lösenord registrering](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Uppdaterade policyn hanteringsupplevelse för användarflöden

**Typ:** Ändrad funktion  
**Tjänstekategori:** B2C – Konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C

Vi har uppdaterat principen skapandet och hanteringen processen för användarflöden (tidigare kallat, inbyggda principer) enklare. Den här nya upplevelsen är nu standard för alla Azure AD-klienter.

Du kan ange ytterligare feedback och förslag med hjälp av Leende eller frown ikonerna i det **Skicka oss feedback** överst i portalen skärmen.

Mer information om den nya princip hanteringsupplevelsen finns i den [Azure AD B2C har nu JavaScript anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogg.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Välj specifik sida elementet versioner som tillhandahålls av Azure AD B2C

**Typ:** Ny funktion  
**Tjänstekategori:** B2C – Konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C

Du kan nu välja en specifik version av sidelement som tillhandahålls av Azure AD B2C. Genom att välja en specifik version, kan du testa uppdateringarna innan de visas på en sida och du kan få förutsägbara beteende. Dessutom kan du nu välja för att framtvinga specifik sida versioner om du vill tillåta JavaScript anpassningar. Om du vill aktivera den här funktionen går du till den **egenskaper** sida i dina användarflöden.

Läs mer om hur du väljer specifika versioner för sidelement i [Azure AD B2C har nu JavaScript anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogg.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurerbara slutanvändarens lösenordskrav för B2C (GA)

**Typ:** Ny funktion  
**Tjänstekategori:** B2C – Konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C

Du kan nu ställa in din organisations lösenordskomplexitet för dina slutanvändare, i stället för att använda din ursprungliga Azure AD-lösenordsprincip. Från den **egenskaper** bladet för dina användare flöden (kallades tidigare din inbyggda principer), som du kan välja ett lösenordskomplexitet **enkel** eller **stark**, eller så kan du Skapa en **anpassad** kraven.

Mer information om konfiguration av lösenord komplexiteten krav finns i [konfigurera komplexitetskrav för lösenord i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Ny standardmallarna för anpassade företagsanpassad autentiseringsupplevelse

**Typ:** Ny funktion  
**Tjänstekategori:** B2C – Konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C

Du kan använda våra nya standardmallarna som finns på den **sidan layouter** bladet av dina användare (tidigare kallat inbyggda principer), märkesprodukter autentiseringsupplevelse för användarna att skapa en anpassad.

Mer information om hur du använder mallar finns i [Azure AD B2C har nu JavaScript anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Januari 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory B2B-samarbete med engångskod autentisering (offentlig förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** B2B  
**Produkten kapacitet:** B2B/B2C

Vi har introducerat engångskod autentisering (OTP) för B2B-gästanvändare som inte kan autentiseras via annat sätt som Azure AD, en Microsoft-konto (MSA) eller Google-federation. Den här nya autentiseringsmetoden innebär att gäster som användarna inte behöver skapa ett nytt microsoftkonto. I stället kan en gästanvändare medan du löser in inbjudan eller åtkomst till en delad resurs kan begära en tillfällig kod som ska skickas till en e-postadress. Med den här tillfälliga koden kan kan gästanvändaren fortsätta att logga in.

Mer information finns i [e-post engångskod authentication (förhandsversion)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) och bloggen [Azure AD gör delning och samarbete sömlös för alla användare med ett konto](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nya Azure AD Application Proxy cookie-inställningar

**Typ:** Ny funktion  
**Tjänstekategori:** App Proxy  
**Produkten kapacitet:** Åtkomstkontroll

Vi har introducerat tre nya cookie-inställningar, tillgängliga för dina appar som har publicerats via programproxy:

- **Använd endast HTTP-cookie.** Anger den **HTTPOnly** flaggan Application Proxy åtkomst- och sessionsprinciper cookies. Aktivera den här inställningen ger ytterligare fördelar, till exempel hjälper till att förhindra att kopiera eller ändra av cookies via klientskript. Vi rekommenderar att du aktiverar den här flaggan (Välj **Ja**) för ytterligare fördelar.

- **Använd en säker cookie.** Anger den **Secure** flaggan Application Proxy åtkomst- och sessionsprinciper cookies. Aktivera den här inställningen ger ytterligare säkerhetsfördelarna genom att kontrollera att cookies överförs endast säkra TLS-kanaler, till exempel HTTPS. Vi rekommenderar att du aktiverar den här flaggan (Välj **Ja**) för ytterligare fördelar.

- **Använda beständiga cookie.** Förhindrar åtkomst cookies upphör att gälla när webbläsaren stängs. Dessa cookies räcker för livslängden för åtkomst-token. Cookies återställs om förfallotid har uppnåtts eller om användaren manuellt tar bort cookien. Vi rekommenderar att du behålla standardinställningen **nr**, endast aktivera inställningen för äldre program som inte delar cookies mellan processer.

Mer information om nya cookies finns i [Cookie-inställningar för att komma åt lokala program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nya federerade appar är tillgängliga i appgalleriet för Azure AD - januari 2019

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** 3 part Integration
 
I januari 2019 har vi lagt till stöd för dessa 35 nya appar med Federation i app-galleriet:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [personal paletten](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco samlingsnamnet](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Administratör för Internet](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [upphör att gälla påminnelse](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Stäng](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO-System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [Ar för Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 för Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn passagerarfartyget ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas plattform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nya förbättringar för Azure AD Identity Protection (förhandsversion)

**Typ:** Ändrad funktion  
**Tjänstekategori:** Identity Protection  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Vi är glada att tillkännage att vi har lagt till följande förbättringar på erbjudandet för Azure AD Identity Protection offentlig förhandsversion, inklusive:

- En uppdaterad och mer integrerat användargränssnitt

- Ytterligare API:er

- Förbättrad riskbedömning maskininlärning

- Alla produkter justering för riskfyllda användare och riskfyllda inloggningar

Mer information om förbättringarna finns [vad är Azure Active Directory Identity Protection (uppdateras)?](https://aka.ms/IdentityProtectionDocs) Läs mer och dina synpunkter via anvisningarna i produkten.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Ny App Lock-funktionen för Microsoft Authenticator-appen på iOS och Android-enheter

**Typ:** Ny funktion  
**Tjänstekategori:** Microsoft Authenticator-appen  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Om du vill skydda din enstaka lösenord, programinformation och app-inställningar kan aktivera du App Lock-funktionen i Microsoft Authenticator-appen. Aktivera App Lock innebär att du ska och att autentisera med hjälp av din PIN-kod eller biometriska varje gång du öppnar Microsoft Authenticator-appen.

Mer information finns i den [Microsoft Authenticator-appen vanliga frågor och svar](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Förbättrad Azure AD Privileged Identity Management (PIM) exportera funktioner

**Typ:** Ny funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapacitet:** Privileged Identity Management

Privileged Identity Management (PIM) administratörer kan nu exportera alla aktiva och berättigade rolltilldelningar för en specifik resurs, vilket innefattar rolltilldelningar för alla underordnade resurser. Tidigare, det var svårt för administratörer att få en fullständig lista över rolltilldelningar för en prenumeration och de tvungna att exportera rolltilldelningar för varje specifik resurs.

Mer information finns i [visa aktivitet och granska historik för Azure-resursroller i PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
