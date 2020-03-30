---
title: Nyheter Viktig information - Azure Active Directory | Microsoft-dokument
description: Lär dig vad som är nytt med Azure Active Directory, till exempel de senaste versionsanteckningarna, kända problem, buggfixar, inaktuella funktioner och kommande ändringar.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f547d67dcb2880b0b51088bc17c43eb9436a0903
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369620"
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?

>Få ett meddelande om när du ska gå tillbaka till `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` den ![här sidan](./media/whats-new/feed-icon-16x16.png) för uppdateringar genom att kopiera och klistra in den här webbadressen: i ikonläsaren för RSS-flödesläsare.

Azure AD tar emot förbättringar löpande. För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln dig information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar
- Föråldrade funktioner
- Planer för förändringar

Den här sidan uppdateras varje månad, så se över den regelbundet. Om du letar efter objekt som är äldre än sex månader kan du hitta dem i [Arkiv för Nyheter i Azure Active Directory](whats-new-archive.md).

---

## <a name="march-2020"></a>Mars 2020

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Användare med standardåtkomstrollen kommer att vara i utrymme för etablering

**Typ:** Planera för förändring  
**Servicekategori:** Etablering av appar  
**Produktkapacitet:** Hantering av identitetslivscykeln
 
Historiskt sett har användare med standardåtkomstrollen inte omfattas av etablering. Vi har hört feedback om att kunder vill att användare med den här rollen ska vara i utrymme för etablering. Vi arbetar med att distribuera en ändring så att alla nya etableringskonfigurationer gör det möjligt för användare med standardåtkomstrollen att etableras. Gradvis ändrar vi beteendet för befintliga etableringskonfigurationer för att stödja etablering av användare med den här rollen. Ingen kundåtgärd krävs. Vi publicerar en uppdatering av vår [dokumentation](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) när ändringen är på plats.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B-samarbete kommer att vara tillgängligt i Microsoft Azure som drivs av 21Vianet-klienter (Azure China 21Vianet)

**Typ:** Planera för förändring  
**Servicekategori:** B2b  
**Produktkapacitet:** B2B/B2C
 
Azure AD B2B-samarbetsfunktionerna kommer att göras tillgängliga i Microsoft Azure som drivs av 21Vianet-klienter (Azure China 21Vianet), vilket gör det möjligt för användare i en Azure China 21Vianet-klient att samarbeta sömlöst med användare i andra Azure China 21Vianet-klienter. [Läs mer om Azure AD B2B-samarbete](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B Samarbetsanb inbjudan e-post omdesign

**Typ:** Planera för förändring  
**Servicekategori:** B2b  
**Produktkapacitet:** B2B/B2C
 
De [e-postmeddelanden](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) som skickas av Azure AD B2B-samarbetsanbjudan för att bjuda in användare till katalogen kommer att omformas för att göra inbjudningsinformationen och användarens nästa steg tydligare.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>HomeRealmDiscovery principändringar visas i granskningsloggarna

**Typ:** Fast  
**Servicekategori:** Revision  
**Produktkapacitet:** Övervakning & rapportering
 
Vi fixade ett fel där ändringar i [HomeRealmDiscovery-principen](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) inte ingick i granskningsloggarna. Du kommer nu att kunna se när och hur politiken ändrades, och av vem. 

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B-samarbete tillgängligt i Azure Government-klienter

**Typ:** Ny funktion  
**Servicekategori:** B2b  
**Produktkapacitet:** B2B/B2C
 
Azure AD B2B-samarbetsfunktionerna är nu tillgängliga mellan vissa Azure Government-klienter.  Om du vill ta reda på om din klient har möjlighet att använda dessa funktioner följer du instruktionerna på [Hur kan jag se om B2B-samarbete är tillgängligt i min Azure US Government-klient?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor-integrering för Azure-loggar är nu tillgänglig i Azure Government

**Typ:** Ny funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering
 
Azure Monitor-integrering med Azure AD-loggar är nu tillgänglig i Azure Government. Du kan dirigera Azure AD-loggar (gransknings- och inloggningsloggar) till ett lagringskonto, händelsenav och logganalys. Läs detaljerad [dokumentation](https://aka.ms/aadlogsinamd) samt [distributionsplaner för rapportering och övervakning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) av Azure AD-scenarier.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Uppdatering av identitetsskydd i Azure-myndigheter

**Typ:** Ny funktion  
**Servicekategori:** Identitetsskydd  
**Produktkapacitet:** Skydd & identitetssäkerhet

Vi är glada över att kunna dela med oss av att vi nu har distribuerat den uppdaterade [Azure AD Identity Protection-upplevelsen](https://aka.ms/IdentityProtectionDocs) i [Microsoft Azure Government-portalen](https://portal.azure.us/). För mer information, se vårt [tillkännagivande blogginlägg](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Haveriberedskap: Hämta och lagra etableringskonfigurationen

**Typ:** Ny funktion  
**Servicekategori:** Etablering av appar  
**Produktkapacitet:** Hantering av identitetslivscykeln
 
Azure AD-etableringstjänsten tillhandahåller en omfattande uppsättning konfigurationsfunktioner. Kunderna måste kunna spara sin konfiguration så att de kan referera till den senare eller återställa till en fungerande version. Vi har lagt till möjligheten att ladda ner din etableringskonfiguration som en JSON-fil och ladda upp den när du behöver den. [Läs mer](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (återställning av lösenord med självbetjäning) kräver nu två grindar för administratörer i Microsoft Azure som drivs av 21Vianet (Azure China 21Vianet) 

**Typ:** Ändrad funktion  
**Servicekategori:** Återställning av lösenord med självbetjäning  
**Produktkapacitet:** Skydd & identitetssäkerhet
 
Tidigare i Microsoft Azure som drivs av 21Vianet (Azure China 21Vianet), administratörer som använder självbetjäning lösenordsåterställning (SSPR) för att återställa sina egna lösenord behövs bara en "gate" (utmaning) för att bevisa sin identitet. I offentliga och andra nationella moln, administratörer i allmänhet måste använda två grindar för att bevisa sin identitet när du använder SSPR. Men eftersom vi inte stödde SMS eller telefonsamtal i Azure China 21Vianet tillät vi en-gate lösenordsåterställning av administratörer.

Vi skapar SSPR-funktionsparitet mellan Azure China 21Vianet och det offentliga molnet. Framöver måste administratörer använda två grindar när du använder SSPR. SMS, telefonsamtal och meddelanden och koder för autentiseringsappar stöds. [Läs mer](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>Lösenordslängden är begränsad till 256 tecken

**Typ:** Ändrad funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering
 
För att säkerställa tillförlitligheten i Azure AD-tjänsten är användarlösenord nu begränsade till 256 tecken. Användare med lösenord längre än detta kommer att uppmanas att ändra sitt lösenord vid efterföljande inloggning, antingen genom att kontakta sin admin eller genom att använda självbetjäning funktionen lösenordsåterställning.

Denna förändring aktiverades den 13 mars 2020, kl 10:00 PST (18:00 UTC), och felet är AADSTS 50052, InvalidPasswordExceedsMaxLength. Se [meddelandet om brytningsändring](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) för mer information.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD-inloggningsloggar är nu tillgängliga för alla kostnadsfria klienter via Azure-portalen

**Typ:** Ändrad funktion  
**Servicekategori:** Rapportering  
**Produktkapacitet:** Övervakning & rapportering
 
Från och med nu kan kunder som har kostnadsfria klienter komma åt [Azure AD-inloggningsloggarna från Azure-portalen i](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) upp till 7 dagar. Tidigare var inloggningsloggar endast tillgängliga för kunder med Azure Active Directory Premium-licenser. Med den här ändringen kan alla klienter komma åt dessa loggar via portalen.

> [!NOTE]
> Kunder behöver fortfarande en premiumlicens (Azure Active Directory Premium P1 eller P2) för att komma åt inloggningsloggarna via Microsoft Graph API och Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Alternativet Förtolkning av katalogomfattande grupper från Allmänna inställningar för grupper på Azure-portalen

**Typ:** Deprecated  
**Servicekategori:** Gruppledning  
**Produktkapacitet:** Samarbete

För att ge kunderna ett mer flexibelt sätt att skapa katalogomfattande grupper som bäst uppfyller deras behov har vi ersatt alternativet Katalogomfattande grupper från > **gruppinställningarna** i **Azure-portalen** med en länk till [dynamisk gruppdokumentation](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership). **Groups** Vi har förbättrat vår dokumentation för att inkludera fler instruktioner så att administratörer kan skapa användargrupper som inkluderar eller utesluter gästanvändare.

---

## <a name="february-2020"></a>Februari 2020

### <a name="upcoming-changes-to-custom-controls"></a>Kommande ändringar av anpassade kontroller

**Typ:** Planera för förändring  
**Servicekategori:** Mfa  
**Produktkapacitet:** Skydd & identitetssäkerhet
 
Vi planerar att ersätta den aktuella förhandsversionen av anpassade kontroller med en metod som gör att autentiseringsfunktioner som tillhandahålls av partner kan fungera sömlöst med Azure Active Directory-administratören och slutanvändarens upplevelser. Idag står partner MFA-lösningar inför följande begränsningar: de fungerar först efter att ett lösenord har angetts; De fungerar inte som MFA för step-up-autentisering i andra viktiga scenarier. och de integreras inte med slutanvändare eller administrativa autentiseringsuppgifter. Den nya implementeringen gör det möjligt för partnerbaserade autentiseringsfaktorer att fungera tillsammans med inbyggda faktorer för viktiga scenarier, inklusive registrering, användning, MFA-anspråk, öka autentiseringen, rapporteringen och loggningen. 

Anpassade kontroller kommer att fortsätta att stödjas i förhandsversion tillsammans med den nya designen tills den når allmän tillgänglighet. Då ger vi kunderna tid att migrera till den nya designen. På grund av begränsningarna i den nuvarande metoden kommer vi inte ombord på nya leverantörer förrän den nya designen är tillgänglig. Vi arbetar nära kunder och leverantörer och kommer att kommunicera tidslinjen när vi kommer närmare. [Läs mer](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identity Secure Score - MFA förbättring åtgärd uppdateringar

**Typ:** Planera för förändring  
**Servicekategori:** Mfa  
**Produktkapacitet:** Skydd & identitetssäkerhet
 
För att återspegla behovet av företag att säkerställa den yttersta säkerheten när de tillämpar principer som fungerar med deras verksamhet, är Microsoft Secure Score ta bort tre förbättringsåtgärder centrerad kring multifaktorautentisering (MFA) och lägga till två.

Följande förbättringsåtgärder kommer att tas bort:

- Registrera alla användare för MFA
- Kräv MFA för alla användare
- Kräv MFA för Azure AD-privilegierade roller

Följande förbättringsåtgärder kommer att läggas till:

- Se till att alla användare kan slutföra MFA för säker åtkomst
- Kräv MFA för administrativa roller

Dessa nya förbättringsåtgärder kräver registrering av användare eller administratörer för MFA i katalogen och upprätta rätt uppsättning principer som passar dina organisationsbehov. Huvudsyftet är att ha flexibilitet samtidigt som alla användare och administratörer kan autentisera med flera faktorer eller riskbaserade frågor om identitetsverifiering. Detta kan ske i form av att ange standardvärden för säkerhet som gör att Microsoft kan bestämma när användare ska utmanas för MFA eller ha flera principer som tillämpar begränsade beslut. Som en del av dessa uppdateringar av förbättringsåtgärder inkluderas inte längre grundläggande skyddsprinciper i bedömningsberäkningar. [Läs mer om vad som kommer i Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>SKU-val av Azure AD-domäntjänster

**Typ:** Ny funktion  
**Servicekategori:** Azure AD-domäntjänster  
**Produktkapacitet:** Azure AD-domäntjänster
 
Vi har hört feedback om att Azure AD Domain Services-kunder vill ha mer flexibilitet när de väljer prestandanivåer för sina instanser. Från och med den 1 februari 2020 bytte vi från en dynamisk modell (där Azure AD bestämmer prestanda- och prisnivån baserat på antal objekt) till en självvalsmodell. Nu kan kunderna välja en prestandanivå som matchar deras miljö. Den här ändringen gör det också möjligt för oss att aktivera nya scenarier som Resource Forests och Premium-funktioner som dagliga säkerhetskopior. Objekträkningen är nu obegränsad för alla SKU:er, men vi fortsätter att erbjuda objekträkningsförslag för varje nivå.

**Ingen omedelbar kundåtgärd krävs.** För befintliga kunder avgör den dynamiska nivån som användes den 1 februari 2020 den nya standardnivån. Det finns ingen prissättning eller prestandapåverkan till följd av den här ändringen. Framöver måste Azure AD DS-kunder utvärdera prestandakrav när deras katalogstorlek och arbetsbelastningsegenskaper ändras. Växla mellan tjänstnivåer fortsätter att vara en no-downtime-åtgärd, och vi kommer inte längre automatiskt att flytta kunder till nya nivåer baserat på tillväxten av deras katalog. Dessutom kommer det inte att bli några prisökningar, och nya priser kommer att anpassas till vår nuvarande faktureringsmodell. Mer information finns i [dokumentationen till Azure AD DS SKU:er](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) och [prissidan för Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nya federerade appar tillgängliga i Azure AD App-galleriet – februari 2020

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part
 
I februari 2020 har vi lagt till dessa 31 nya appar med federationsstöd i appgalleriet: 

[IamIP Patent Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO för Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), I Case of Crisis - Online [Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC Cloud Design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [Biodlare Azure AD Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), Korn Ferry [Bedömningar](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada Command](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), New [Relic (Limited Release)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), [Ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [Mall Chooser för lag,](https://links.officeatwork.com/templatechooser-download-teams) [Beesy](https://www.beesy.me/index.php/site/login), [Hälsostödssystem](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [VÄGGMÅLNING](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink för lärare och skolor](https://www.thinglink.com/), [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [Reprints Desk - Artikel Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nya etableringsanslutningar i Azure AD Application Gallery – februari 2020

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part
 
Nu kan du automatisera skapandet, uppdateringen och ta bort användarkonton för dessa nyligen integrerade appar:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Mer information om hur du skyddar din organisation bättre genom att använda automatisk etablering av användarkonton finns i [Automatisera användaretablering till SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD-stöd för FIDO2-säkerhetsnycklar i hybridmiljöer

**Typ:** Ny funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering
 
Vi presenterar den offentliga förhandsversionen av Azure AD-stöd för FIDO2-säkerhetsnycklar i hybridmiljöer. Användare kan nu använda FIDO2-säkerhetsnycklar för att logga in på sina Hybrid Azure AD-anslutna Windows 10-enheter och få sömlös inloggning till sina lokala resurser och molnresurser. Support för hybridmiljöer har varit den mest efterfrågade funktionen från våra lösenordslösa kunder sedan vi först lanserade den offentliga förhandsversionen för FIDO2-stöd i Azure AD-anslutna enheter. Lösenordslös autentisering med hjälp av avancerad teknik som biometri och kryptering med offentliga/privata nycklar ger bekvämlighet och användarvänlighet samtidigt som den är säker. Med den här offentliga förhandsversionen kan du nu använda modern autentisering som FIDO2-säkerhetsnycklar för att komma åt traditionella Active Directory-resurser. Mer information finns i [SSO till lokala resurser](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises). 

För att komma igång, besök [aktivera FIDO2 säkerhetsnycklar för din klient för](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) steg-för-steg-instruktioner. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Den nya upplevelsen för Mitt konto är nu allmänt tillgänglig

**Typ:** Ändrad funktion  
**Servicekategori:** Min profil/konto  
**Produktkapacitet:** Användarupplevelser
 
Mitt konto, one stop shop för alla slutanvändarkontohanteringsbehov, är nu allmänt tillgänglig! Slutanvändare kan komma åt den nya webbplatsen via URL eller i huvudet på den nya upplevelsen Mina appar. Läs mer om alla funktioner för självbetjäning som den nya upplevelsen erbjuder på [Översikt över Min kontoportal](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Url:en för min kontowebbplats uppdateras till myaccount.microsoft.com

**Typ:** Ändrad funktion  
**Servicekategori:** Min profil/konto  
**Produktkapacitet:** Användarupplevelser
 
Den nya slutanvändarens upplevelse för Mitt `https://myaccount.microsoft.com` konto kommer att uppdatera webbadressen till nästa månad. Hitta mer information om upplevelsen och alla självbetjäningsfunktioner för kontot som den erbjuder slutanvändare på [Portalen Mitt konto.](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)

---
 
## <a name="january-2020"></a>Januari 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Den nya Portalen Mina appar är nu allmänt tillgänglig

**Typ:** Planera för förändring  
**Servicekategori:** Mina appar  
**Produktkapacitet:** Användarupplevelser
 
Uppgradera din organisation till den nya Portalen Mina appar som nu är allmänt tillgänglig! Mer information om den nya portalen och samlingarna i [Skapa samlingar på Portalen Mina appar](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Arbetsytor i Azure AD har bytt namn till samlingar

**Typ:** Ändrad funktion  
**Servicekategori:** Mina appar   
**Produktkapacitet:** Användarupplevelser
 
Arbetsytor, filter administratörer kan konfigurera för att organisera sina användare apps, kommer nu att kallas samlingar. Mer information om hur du konfigurerar dem i [Skapa samlingar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Registrering och inloggning via Azure AD B2C Phone med anpassad princip (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** B2C - Hantering av konsumentidentiteter  
**Produktkapacitet:** B2B/B2C
 
Med telefonnummer registrering och inloggning, utvecklare och företag kan tillåta sina kunder att registrera dig och logga in med ett engångslösenord skickas till användarens telefonnummer via SMS. Med den här funktionen kan kunden också ändra sitt telefonnummer om de förlorar åtkomsten till sin telefon. Med kraften i anpassade principer kan telefonuppskrivning och inloggning utvecklare och företag kommunicera sitt varumärke via sidanpassning. Ta reda på hur [du konfigurerar telefonanmälan och loggar in med anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nya etableringsanslutningar i Azure AD Application Gallery – januari 2020

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part
 
Nu kan du automatisera skapandet, uppdateringen och ta bort användarkonton för dessa nyligen integrerade appar:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Mer information om hur du skyddar din organisation bättre genom att använda automatisk etablering av användarkonton finns i [Automatisera användaretablering till SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nya federerade appar tillgängliga i Azure AD App-galleriet – januari 2020

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part
 
I januari 2020 har vi lagt till dessa 33 nya appar med federationsstöd i appgalleriet: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), `https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access`, [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot](https://leavebot.io/#home), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [SmartWork](https://www.intumit.com/english/SmartWork.html), [Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN - Azure AD SSO Gateway för Oracle E-Business Suite - EBS, PeopleSoft och JDE ,](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial) [Hosted MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), `https://sites.lumapps.com/login`, [Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [ Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB för Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient Conduct Manager Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login), [PingFlow Authentication](https://app-staging.pingview.io/), [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), `https://app.sandwai.com/`, [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Två nya identitetsskyddsidentifieringar

**Typ:** Ny funktion  
**Servicekategori:** Identitetsskydd  
**Produktkapacitet:** Skydd & identitetssäkerhet
 
Vi har lagt till två nya inloggningslänkade identifieringstyper i Identity Protection: Misstänkta regler för inkorgsmanipulering och omöjliga resor. Dessa offlineidentifieringar identifieras av MICROSOFT Cloud App Security (MCAS) och påverkar användarens och inloggningsrisken i Identity Protection. Mer information om dessa identifieringar finns i våra [inloggningsrisktyper](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Breaking Change: URI Fragment kommer inte att genomföras genom inloggningsomdirigering

**Typ:** Ändrad funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering
 
Från och med den 8 februari 2020, när en begäran skickas till login.microsoftonline.com för att logga in en användare, kommer tjänsten att lägga till ett tomt fragment i begäran.  Detta förhindrar en klass av omdirigera attacker genom att se till att webbläsaren utplånar alla befintliga fragment i begäran. Inget program bör ha ett beroende av det här beteendet. Mer information finns i [Bryta ändringar](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) i dokumentationen till Microsofts identitetsplattform.

---

## <a name="december-2019"></a>December 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrera SAP SuccessFactors-etablering i Azure AD och lokal AD (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Etablering av appar  
**Produktkapacitet:** Hantering av identitetslivscykeln

Du kan nu integrera SAP SuccessFactors som en auktoritär identitetskälla i Azure AD. Den här integreringen hjälper dig att automatisera livscykeln för heltäckande identitet, inklusive användning av HR-baserade händelser, som nyanställningar eller uppsägningar, för att styra etableringen av Azure AD-konton.

Mer information om hur du konfigurerar SAP SuccessFactors inkommande etablering till Azure AD finns i automatisk [etableringshandledning för SAP SuccessFactors.](https://aka.ms/SAPSuccessFactorsInboundTutorial)

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Stöd för anpassade e-postmeddelanden i Azure AD B2C (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** B2C - Hantering av konsumentidentiteter  
**Produktkapacitet:** B2B/B2C

Du kan nu använda Azure AD B2C för att skapa anpassade e-postmeddelanden när användarna registrerar sig för att använda dina appar. Genom att använda DisplayControls (för närvarande i förhandsversion) och en e-postleverantör från tredje part (till exempel [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)eller ett anpassat REST API) kan du använda din egen e-postmall, **Från** adress och ämnestext, samt stödja inställningar för lokalisering och anpassat engångslösenord (OTP).

Mer information finns [i Anpassad e-postverifiering i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Ersättning av originalprinciper med säkerhetsstandarder

**Typ:** Ändrad funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** Identitetssäkerhet och identitetsskydd

Som en del av en säker modell för autentisering tar vi bort de befintliga grundläggande skyddsprinciperna från alla klienter. Avlägsnandet är avsett för slutförande i slutet av februari. Ersättningen för dessa grundläggande skyddsprinciper är säkerhetsstandarder. Om du har använt grundläggande skyddsprinciper måste du planera att gå över till den nya standardprincipen för säkerhet eller till villkorlig åtkomst. Om du inte har använt dessa principer finns det ingen åtgärd för dig att vidta.

Mer information om de nya standardvärdena för säkerhet finns i [Vad är standardvärden för säkerhet?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Mer information om principer för villkorlig åtkomst finns i [Vanliga principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>November 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Stöd för attributet SameSite och Chrome 80

**Typ:** Planera för förändring  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Användarautentisering

Som en del av en säker modell för cookies ändrar webbläsaren Chrome 80 `SameSite` hur den behandlar cookies utan attributet. Alla cookies som inte `SameSite` anger attributet behandlas som om `SameSite=Lax`den var inställd på , vilket resulterar i att Chrome blockerar vissa scenarier för cookiedelning mellan domäner som din app kan vara beroende av. För att bibehålla det äldre Chrome-beteendet kan du använda `SameSite=None` attributet och lägga till ytterligare `Secure` ett attribut, så cookies på flera webbplatser kan bara nås via HTTPS-anslutningar. Chrome beräknas vara klart senast den 4 februari 2020.

Vi rekommenderar alla våra utvecklare att testa sina appar med den här vägledningen:

- Ange standardvärdet för inställningen **Använd säker cookie** till **Ja**.

- Ange standardvärdet för attributet **SameSite** till **Ingen**.

- Lägg till `SameSite` ytterligare ett attribut **för Secure**.

Mer information finns [i Kommande cookieändringar på samma plats i ASP.NET och ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) och [Potentiella störningar på kundens webbplatser och Microsofts produkter och tjänster i Chrome version 79 och senare](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Ny snabbkorrigering för MICROSOFT Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Typ:** Fast  
**Servicekategori:** Microsofts identitetshanterare  
**Produktkapacitet:** Hantering av identitetslivscykeln

Ett samlad snabbkorrigeringspaket (version 4.6.34.0) är tillgängligt för Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Det här sammanslagningspaketet löser problem och lägger till förbättringar som beskrivs i avsnittet "Problem som har åtgärdats och förbättringar som lagts till i den här uppdateringen".

Samlad uppdateringsuppdatering finns i [Microsoft Identity Manager 2016 Service Pack 2 (version 4.6.34.0) Samlad uppdatering](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Ny AKTIVITETSRAPPORT FÖR AD FS-appar som hjälper dig att migrera appar till Azure AD (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Sso

Använd den nya aktivitetsrapporten för AD FS-appar (Active Directory Federation Services) i Azure-portalen för att identifiera vilka av dina appar som kan migreras till Azure AD. Rapporten bedömer alla AD FS-appar för kompatibilitet med Azure AD, söker efter eventuella problem och ger vägledning om hur du förbereder enskilda appar för migrering.

Mer information finns i [Använda AD FS-programaktivitetsrapporten för att migrera program till Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nytt arbetsflöde för användare att begära administratörsmedgivande (Offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Åtkomstkontroll

Det nya arbetsflödet för administratörsgodkännande ger administratörer ett sätt att bevilja åtkomst till appar som kräver administratörsgodkännande. Om en användare försöker komma åt en app, men inte kan ge sitt samtycke, kan de nu skicka en begäran om administratörsgodkännande. Begäran skickas via e-post och placeras i en kö som är tillgänglig från Azure-portalen, till alla administratörer som har utsetts till granskare. När en granskare vidtar åtgärder på en väntande begäran meddelas de begärande användarna om åtgärden.

Mer information finns i [Konfigurera arbetsflödet för administratörsgodkännande (förhandsversion)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Ny konfigurationsupplevelse för Azure AD-appregistreringar token för hantering av valfria anspråk (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** Utvecklarupplevelse

Det nya **konfigurationsbladet för Token för Azure AD-appregistreringar** token på Azure-portalen visar nu apputvecklare en dynamisk lista över valfria anspråk för sina appar. Den här nya upplevelsen hjälper till att effektivisera Azure AD-appmigreringar och minimera valfria anspråksmisskonfigurationer.

Mer information finns i [Ange valfria anspråk till din Azure AD-app](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nytt arbetsflöde för godkännande i två steg i Azure AD-berättigandehantering (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** Hantering av rättigheter

Vi har infört ett nytt arbetsflöde för godkännande i två steg som gör att du kan kräva två godkännare för att godkänna en användares begäran till ett åtkomstpaket. Du kan till exempel ange det så att den begärande användarens chef först måste godkänna, och sedan kan du också kräva att en resursägare godkänner. Om en av godkännarna inte godkänner det beviljas inte åtkomst.

Mer information finns i [Ändra inställningar för begäran och godkännande för ett åtkomstpaket i Azure AD-berättigandehantering](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Uppdateringar av sidan Mina appar tillsammans med nya arbetsytor (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Mina appar  
**Produktkapacitet:** Integration från tredje part

Nu kan du anpassa organisationens användares sätt att se och komma åt den uppdaterade upplevelsen av Mina appar. Den här nya upplevelsen innehåller också den nya funktionen för arbetsytor, vilket gör det enklare för användarna att hitta och ordna appar.

Mer information om den nya upplevelsen av Mina appar och hur du skapar arbetsytor finns i [Skapa arbetsytor på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Googles sociala ID-stöd för Azure AD B2B-samarbete (allmän tillgänglighet)

**Typ:** Ny funktion  
**Servicekategori:** B2b  
**Produktkapacitet:** Användarautentisering

Nytt stöd för att använda Googles sociala ID(Gmail-konton) i Azure AD bidrar till att göra samarbetet enklare för dina användare och partner. Det finns inte längre något behov av att dina partner skapar och hanterar ett nytt Microsoft-specifikt konto. Microsoft Teams stöder nu google-användare fullt ut på alla klienter och i de vanliga och klientrelaterade slutpunkterna för autentisering.

Mer information finns i [Lägga till Google som identitetsleverantör för B2B-gästanvändare](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge Mobile-stöd för villkorlig åtkomst och enkel inloggning (allmän tillgänglighet)

**Typ:** Ny funktion  
**Servicekategori:** Villkorlig åtkomst  
**Produktkapacitet:** Skydd & identitetssäkerhet

Azure AD för Microsoft Edge på iOS och Android stöder nu Azure AD Enkel inloggning och villkorlig åtkomst:

- **Microsoft Edge enkel inloggning (SSO):** Enkel inloggning är nu tillgänglig för alla inbyggda klienter (till exempel Microsoft Outlook och Microsoft Edge) för alla Azure AD-anslutna appar.

- **Villkorlig åtkomst för Microsoft Edge:** Genom programbaserade principer för villkorlig åtkomst måste användarna använda Microsoft Intune-skyddade webbläsare, till exempel Microsoft Edge.

Mer information om villkorlig åtkomst och SSO med Microsoft Edge finns i [microsoft edge mobile-supporten för villkorlig åtkomst och enkel inloggning nu allmänt tillgängligt](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) blogginlägg. Mer information om hur du konfigurerar klientappar med [appbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) eller [enhetsbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)finns i Hantera [webbåtkomst med hjälp av en Microsoft Intune-principskyddad webbläsare](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Hantering av Azure AD-berättigande (allmän tillgänglighet)

**Typ:** Ny funktion  
**Servicekategori:** Andra  
**Produktkapacitet:** Hantering av rättigheter

Azure AD-berättigandehantering är en ny identitetsstyrningsfunktion som hjälper organisationer att hantera identitets- och åtkomstlivscykel i stor skala. Den här nya funktionen hjälper dig genom att automatisera arbetsflöden för åtkomstbegäran, komma åt tilldelningar, granskningar och förfallodatum för grupper, appar och SharePoint Online-webbplatser.

Med Azure AD-berättigandehantering kan du mer effektivt hantera åtkomst både för anställda och även för användare utanför organisationen som behöver åtkomst till dessa resurser.

Mer information finns i [Vad är hantering av Azure AD-berättigande?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera etablering av användarkonton för dessa nyligen stödda SaaS-appar

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part  

Nu kan du automatisera skapandet, uppdateringen och ta bort användarkonton för dessa nyligen integrerade appar:

[SAP Cloud Platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Mer information om hur du skyddar din organisation bättre genom att använda automatisk etablering av användarkonton finns i [Automatisera användaretablering till SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nya federerade appar tillgängliga i Azure AD App-galleriet – november 2019

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part

I november 2019 har vi lagt till dessa 21 nya appar med federationsstöd i appgalleriet:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access för medlemmar (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign On (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial) `https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279`, , [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [Foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets Idea & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [Jisc Registrering av studentväljare](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nytt och förbättrat Azure AD-programgalleri

**Typ:** Ändrad funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Sso

Vi har uppdaterat Azure AD-programgalleriet för att göra det enklare för dig att hitta förintegrerade appar som stöder etablering, OpenID Connect och SAML på din Azure Active Directory-klient.

Mer information finns i [Lägga till ett program i din Azure Active Directory-klient](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Längdgräns för approlldefinition från 120 till 240 tecken

**Typ:** Ändrad funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Sso

Vi har hört från kunder att längdgränsen för approlldefinitionsvärdet i vissa appar och tjänster är för kort på 120 tecken. Som svar har vi ökat den maximala längden på rollvärdesdefinitionen till 240 tecken.

Mer information om hur du använder programspecifika rolldefinitioner finns i [Lägga till approller i ditt program och ta emot dem i token](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---

## <a name="october-2019"></a>Oktober 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Deprecation av identityRiskEvent API för Azure AD Identity Protection riskidentifieringar  

**Typ:** Planera för förändring  
**Servicekategori:** Identitetsskydd  
**Produktkapacitet:** Skydd & identitetssäkerhet

Som svar på feedback från utvecklare kan Azure AD Premium P2-prenumeranter nu utföra komplexa frågor på Azure AD Identity Protections riskidentifieringsdata med hjälp av det nya riskdetection API:et för Microsoft Graph. Den befintliga [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API-betaversionen slutar returnera data runt **den 10 januari 2020**. Om din organisation använder api:et för identityRiskEvent bör du övergå till det nya riskDetection API:et.

Mer information om det nya riskDetection API finns i [referensdokumentationen för API-referensdokumentation för riskidentifiering](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Stöd för programproxy för Attributet SameSite och Chrome 80

**Typ:** Planera för förändring  
**Servicekategori:** App Proxy  
**Produktkapacitet:** Åtkomstkontroll

Ett par veckor före webbläsarversionen av Chrome 80 planerar vi att uppdatera hur Application Proxy-cookies behandlar attributet **SameSite.** Med lanseringen av Chrome 80 behandlas alla cookies som inte anger attributet **SameSite** som om den var inställd på `SameSite=Lax`.

För att undvika potentiellt negativa effekter på grund av den här ändringen uppdaterar vi åtkomsten till programproxy och sessionscookies genom att:

- Ange standardvärdet för inställningen **Använd säker cookie** till **Ja**.

- Ange standardvärdet för attributet **SameSite** till **Ingen**.

    >[!NOTE]
    > Application Proxy access cookies har alltid överförts uteslutande via säkra kanaler. Dessa ändringar gäller endast sessionscookies.

Mer information om inställningarna för cookies för programproxy finns i [Cookie-inställningar för åtkomst till lokala program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Appregistreringar (äldre) och konvergerad apphantering från Application Registration Portal (apps.dev.microsoft.com) kommer inte längre att vara tillgängliga

**Typ:** Planera för förändring  
**Servicekategori:** Ej mycket  
**Produktkapacitet:** Utvecklarupplevelse

Inom en snar framtid kan användare med Azure AD-konton inte längre registrera och hantera konvergerade program med hjälp av Application Registration Portal (apps.dev.microsoft.com) eller registrera och hantera program i Appregistreringarna (äldre) erfarenhet i Azure-portalen.

Mer information om den nya appregistreringsupplevelsen finns [i appregistreringarna i Utbildningsguiden för Azure Portal](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Användare behöver inte längre registrera om under migreringen från MFA per användare till villkorad åtkomstbaserad MFA

**Typ:** Fast  
**Servicekategori:** Mfa  
**Produktkapacitet:** Skydd & identitetssäkerhet

Vi har åtgärdat ett känt problem där när användare var tvungna att registrera om de inaktiverades för MFA (Multi-Factor Authentication) per användare och sedan aktiveras för MFA via en princip för villkorlig åtkomst.

Om du vill att användarna ska registrera om kan du välja alternativet **Obligatorisk omregistrering av MFA** från användarens autentiseringsmetoder i Azure AD-portalen. Mer information om hur du migrerar användare från MFA per användare till Villkorsstyrd åtkomstbaserad MFA finns i [Konvertera användare från MFA per användare till villkorsstyrd åtkomstbaserad MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nya funktioner för att omvandla och skicka anspråk i din SAML-token

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Sso

Vi har lagt till ytterligare funktioner som hjälper dig att anpassa och skicka anspråk i din SAML-token. Dessa nya funktioner inkluderar:

- Ytterligare anspråksomvandlingsfunktioner, vilket hjälper dig att ändra värdet du skickar i anspråket.

- Möjlighet att tillämpa flera omvandlingar på ett enda anspråk.

- Möjlighet att ange anspråkskällan, baserat på användartypen och den grupp som användaren tillhör.

Detaljerad information om dessa nya funktioner, inklusive hur du använder dem, finns [i Anpassa anspråk som utfärdats i SAML-token för företagsprogram](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Ny sida för mina inloggningar för slutanvändare i Azure AD

**Typ:** Ny funktion  
**Servicekategori:** Autentiseringar (inloggningar)  
**Produktkapacitet:** Övervakning & rapportering

Vi har lagt till en ny sidahttps://mysignins.microsoft.com) med Mina inloggningar ( så att organisationens användare kan visa sin senaste **inloggningshistorik** för att kontrollera om det finns någon ovanlig aktivitet. På den här nya sidan kan användarna se:

- Om någon försöker gissa sitt lösenord.

- Om en angripare har loggat in på sitt konto och från vilken plats.

- Vilka appar angriparen försökte komma åt.

Mer information finns [i användarna kan nu kontrollera sin inloggningshistorik för ovanlig aktivitetsblogg.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066)

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migrering av Azure AD Domain Services (Azure AD DS) från klassiska till virtuella Azure Resource Manager-nätverk

**Typ:** Ny funktion  
**Servicekategori:** Azure AD-domäntjänster  
**Produktkapacitet:** Azure AD-domäntjänster

Till våra kunder som har fastnat på klassiska virtuella nätverk - vi har goda nyheter för dig! Du kan nu utföra en engångsmigrering från ett klassiskt virtuellt nätverk till ett befintligt virtuellt resurshanterarenätverk. När du har flyttat till det virtuella nätverket Resource Manager kan du dra nytta av ytterligare och uppgraderade funktioner som detaljerade lösenordsprinciper, e-postmeddelanden och granskningsloggar.

Mer information finns i [Förhandsgranska - Migrera Azure AD Domain Services från den klassiska virtuella nätverksmodellen till Resource Manager](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Uppdateringar av azure AD B2C-sidkontraktslayouten

**Typ:** Ny funktion  
**Servicekategori:** B2C - Hantering av konsumentidentiteter  
**Produktkapacitet:** B2B/B2C

Vi har infört några nya ändringar i version 1.2.0 av sidkontraktet för Azure AD B2C. I den här uppdaterade versionen kan du nu styra belastningsordningen för dina element, vilket också kan hjälpa till att stoppa flimmer som händer när formatmallen (CSS) läses in.

En fullständig lista över de ändringar som gjorts i sidkontraktet finns i [ändringsloggen För version](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Uppdatera till sidan Mina appar tillsammans med nya arbetsytor (offentlig förhandsversion)

**Typ:** Ny funktion  
**Servicekategori:** Mina appar  
**Produktkapacitet:** Åtkomstkontroll

Nu kan du anpassa organisationens användares sätt att se och komma åt den helt nya upplevelsen av Mina appar, inklusive att använda den nya funktionen för arbetsytor för att göra det enklare för dem att hitta appar. De nya arbetsytorna fungerar som ett filter för de appar som organisationens användare redan har åtkomst till.

Mer information om hur du distribuerar den nya upplevelsen av Mina appar och skapar arbetsytor finns i [Skapa arbetsytor på portalen Mina appar (förhandsversion).](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Stöd för den månatliga aktiva användarbaserade faktureringsmodellen (Allmän tillgänglighet)

**Typ:** Ny funktion  
**Servicekategori:** B2C - Hantering av konsumentidentiteter  
**Produktkapacitet:** B2B/B2C

Azure AD B2C stöder nu månatliga active users (MAU) fakturering. MAU-fakturering baseras på antalet unika användare med autentiseringsaktivitet under en kalendermånad. Befintliga kunder kan när som helst byta till den här nya faktureringsmetoden.

Från och med den 1 november 2019 faktureras alla nya kunder automatiskt med den här metoden. Den här faktureringsmetoden gynnar kunderna genom kostnadsfördelar och möjligheten att planera i förväg.

Mer information finns i [Uppgradera till månatliga faktureringsmodell för aktiva användare](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nya federerade appar tillgängliga i Azure AD App-galleriet – oktober 2019

**Typ:** Ny funktion  
**Servicekategori:** Företagsappar  
**Produktkapacitet:** Integration från tredje part

I oktober 2019 har vi lagt till dessa 35 nya appar med federationsstöd i appgalleriet:

[I händelse av kris - Mobil](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [Tact](https://tact.ai/assistant/), [OpusCapita Cash Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [Contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue Koordinat - Europa](https://www.hirevue.com/), [HireVue Koordinat - USOnly](https://www.hirevue.com/), [HireVue Koordinat - USA](https://www.hirevue.com/), [ WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [Mail Luck!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teamie](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [Speaking Email for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care Navigation System ](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Mer information om apparna finns i [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du listar ditt program i Azure AD-appgalleriet finns [i Lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Menyalternativ för konsoliderad säkerhet i Azure AD-portalen

**Typ:** Ändrad funktion  
**Servicekategori:** Identitetsskydd  
**Produktkapacitet:** Skydd & identitetssäkerhet

Du kan nu komma åt alla tillgängliga Azure AD-säkerhetsfunktioner från det nya **menyalternativet Säkerhet** och från **sökfältet** i Azure-portalen. Dessutom kommer den **Security** nya säkerhetsmålsidan, kallad **Säkerhet – Komma igång,** att tillhandahålla länkar till vår offentliga dokumentation, säkerhetsvägledning och distributionsguider.

Den nya **säkerhetsmenyn** innehåller:

- Villkorlig åtkomst
- Identity Protection
- Security Center
- Identitetssäker poäng
- Autentiseringsmetoder
- Mfa
- Riskrapporter - Riskfyllda användare, Riskfyllda inloggningar, Riskdetekteringar
- Och mer...

Mer information finns i [Säkerhet - Komma igång](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Förfalloprincipen för Office 365-grupper har förbättrats med automatisk förnyelse

**Typ:** Ändrad funktion  
**Servicekategori:** Gruppledning  
**Produktkapacitet:** Hantering av identitetslivscykeln

Förfalloprincipen för Office 365-grupper har förbättrats för att automatiskt förnya grupper som används aktivt av dess medlemmar. Grupper förnyas automatiskt baserat på användaraktivitet i alla Office 365-appar, inklusive Outlook, SharePoint och Teams.

Den här förbättringen bidrar till att minska gruppens förfallodatummeddelanden och hjälper till att se till att aktiva grupper fortsätter att vara tillgängliga. Om du redan har en aktiv förfalloprincip för dina Office 365-grupper behöver du inte göra något för att aktivera den nya funktionen.

Mer information finns i [Konfigurera principen för förfallodatum för Office 365-grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Uppdaterad upplevelse för att skapa Azure AD Domain Services (Azure AD DS)

**Typ:** Ändrad funktion  
**Servicekategori:** Azure AD-domäntjänster  
**Produktkapacitet:** Azure AD-domäntjänster

Vi har uppdaterat Azure AD Domain Services (Azure AD DS) för att inkludera en ny och förbättrad skapandeupplevelse som hjälper dig att skapa en hanterad domän med bara tre klick! Dessutom kan du nu ladda upp och distribuera Azure AD DS från en mall.

Mer information finns i [Självstudiekurs: Skapa och konfigurera en Azure Active Directory Domain Services-instans](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---
