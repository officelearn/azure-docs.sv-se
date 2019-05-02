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
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a50dadb7ae401a5655745a799e6e9fcebb8bb886
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935931"
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

## <a name="april-2019"></a>April 2019

### <a name="azure-active-directory-azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure Active Directory (Azure AD) rättigheten management är nu tillgängliga (offentlig förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** Identitetshantering  
**Produkten kapacitet:** Identitetshantering

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

- **Äldre autentisering och villkorsbaserad åtkomst.** Innehåller information om appar och användare som använder äldre autentisering och Multifaktorautentisering användning som utlöses av principer för villkorlig åtkomst, appar med principer för villkorlig åtkomst, och så vidare.

- **Inloggningsfel analys.** Hjälper dig att avgöra om din inloggningsfel inträffar på grund av en användaråtgärd, problem med principer eller din infrastruktur.

- **Anpassade rapporter.** Du kan skapa nya eller redigera befintliga arbetsböcker för att anpassa funktionen insikter för din organisation.

Mer information finns i [hur du använder Azure Monitor-arbetsböcker för Azure Active Directory-rapporter](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nya federerade appar är tillgängliga i appgalleriet för Azure AD - April 2019

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration

I April 2019 har vi lagt till stöd för dessa 21 nya appar med Federation i app-galleriet:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol--tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [ Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Ansluta](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel ansluta](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (Role-based SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent kapital Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certifikathanteraren](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nya åtkomstgranskningar frekvens alternativ och flera Systemroll

**Typ:** Ny funktion  
**Tjänstekategori:** Åtkomstgranskningar  
**Produkten kapacitet:** Identitetshantering

Nya uppdateringar i Azure AD-åtkomstgranskningar kan du:

- Ändra frekvensen för din åtkomst kodgranskningar till **delvis annually**, utöver de tidigare befintliga alternativen för varje vecka, månadsvis, kvartalsvis och årligen.

- Välj flera Azure AD och granska Azure-resursroller när du skapar en enkel åtkomst. I så fall kan alla roller har ställts in med samma inställningar och alla granskare får ett meddelande på samma gång.

Läs mer om hur du skapar en åtkomstgranskning [skapa en åtkomstgranskning av grupper eller program i Azure AD-åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-avisering systemen övergår, Skicka ny information om e-avsändaren för en del kunder

**Typ:** Ändrad funktion  
**Tjänstekategori:** AD Sync  
**Produkten kapacitet:** Plattform

Azure AD Connect håller övergår vår e-avisering systemen potentiellt som visar vissa kunder en ny e-post-avsändare. För att lösa det, måste du lägga till `azure-noreply@microsoft.com` till din organisations lista över tillåtna eller om du inte att kunna fortsätta att få viktiga aviseringar från dina Office 365, Azure eller Sync-tjänsterna.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>UPN-suffix ändringar är nu klar mellan federerade domäner i Azure AD Connect

**Typ:** Korrigerat  
**Tjänstekategori:** AD Sync  
**Produkten kapacitet:** Plattform

Du kan nu ändra en användares UPN-suffix från en federerade domän till en annan federerade domän i Azure AD Connect. Den här snabbkorrigeringen innebär att du bör inte längre uppstår FederatedDomainChangeError felmeddelande under synkroniseringscykeln eller ta emot ett meddelande e-postmeddelande som anger ”, det går inte att uppdatera det här objektet i Azure Active Directory eftersom attributet [ FederatedUser.UserPrincipalName] är inte giltig. Uppdatera värdet i de lokala katalogtjänsterna ”.

Mer information finns i [felsöka fel under synkronisering](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Ökad säkerhet med hjälp av principen för app-protection-baserad villkorlig åtkomst i Azure AD (förhandsversion)

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
**Tjänstekategori:** B2C – konsumentidentitetshantering  
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
**Produkten kapacitet:** Tredjepartsintegration

I mars 2019 har vi lagt till stöd för dessa 14 nya appar med Federation i app-galleriet:

[ISEC7 Mobile Exchange ombud](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Förklaring granskning prissystem](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Läs](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool prestanda frågor](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranät](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit vyer](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [Aktivitetsse](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nya Zscaler och Atlassians etablering anslutningar i Azure AD-galleriet – mars 2019

**Typ:** Ny funktion  
**Tjänstekategori:** App-etablering  
**Produkten kapacitet:** Tredjepartsintegration

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

För att förhindra att administratörer av misstag låser sig av sina egna klienter via felkonfigurerad principer för villkorlig åtkomst, har vi skapat nya varningar och uppdaterade riktlinjer i Azure-portalen. Mer information om nya vägledning finns i [vad är tjänstens beroenden i Azure Active Directory villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Förbättrad slutanvändarens användningsvillkor upplevelser för användning på mobila enheter

**Typ:** Ändrad funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning

Vi har uppdaterat våra befintliga villkor för användning upplevelser för att förbättra hur du kan granska och godkänna användningsvillkoren på en mobil enhet. Du kan nu Zooma in och ut, gå tillbaka, ladda ned informationen och välj hyperlänkar. Läs mer om de uppdaterade villkoren för användning, [Azure Active Directory-villkoren i Använd funktion](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

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

**Typ:** Förändringsplan  
**Tjänstekategori:** Villkorlig åtkomst  
**Produkten kapacitet:** Åtkomstkontroll

Vi håller på att uppdatera hur Exchange ActiveSync (EAS) utvärderar följande villkor:

- Användarplats, baserat på land, region eller IP-adress

- Inloggningsrisk

- Enhetsplattform

Om du har tidigare använt dessa villkor i principer för villkorlig åtkomst kan du vara medveten om att villkoret beteende kan ändras. Till exempel om du tidigare använt platsvillkoret för användare i en princip, kanske du upptäcker principen nu hoppas över baserat på platsen för dina användare.

---

## <a name="february-2019"></a>Februari 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurerbar Azure AD SAML-tokenkryptering (allmänt tillgänglig förhandsversion) 

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Enkel inloggning

Du kan nu konfigurera alla SAML-appar som stöds för att ta emot krypterad SAML-tokens. När konfigureras och används med en app, krypterar de utgivna SAML-intyg med hjälp av en offentlig nyckel som hämtas från ett certifikat som lagras i Azure AD i Azure AD.

Läs mer om hur du konfigurerar SAML-tokenkryptering, [konfigurera Azure AD-SAML-tokenkryptering](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Skapa en åtkomstgranskning för grupper eller appar som använder Azure AD-åtkomstgranskningar

**Typ:** Ny funktion  
**Tjänstekategori:** Åtkomstgranskningar  
**Produkten kapacitet:** Styrning

Du kan nu inkludera flera grupper eller appar i en enda Azure AD-åtkomstgranskning för gruppmedlemskap eller apptilldelning. Åtkomstgranskningar med flera grupper eller appar som är konfigurerade med samma inställningar och alla inkluderade granskare får ett meddelande på samma gång.

Mer information om hur du skapar en åtkomstgranskning med hjälp av Azure AD-Åtkomstgranskningar, finns i [skapa en åtkomstgranskning av grupper eller program i Azure AD-Åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – februari 2019

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration
 
I februari 2019 har vi lagt till stöd för dessa 27 nya appar med Federation i app-galleriet:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Klicka på behörighet, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [seismisk ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Resurs A dröm](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods integrering molnet](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Knowledge var som helst LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope Data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud av Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp produktivitet plattform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Förbättrad kombinerad MFA SSPR-registrering

**Typ:** Ändrad funktion  
**Tjänstekategori:** Lösenordsåterställning via självbetjäning  
**Produkten kapacitet:** Användarautentisering
 
Som svar på feedback från kunder har vi förbättrat den kombinerade MFA/SSPR-registrering förhandsversion upplevelsen, hjälpa användarna att snabbt registrera deras säkerhetsinformation för både MFA och SSPR. 

**Följ dessa steg om du vill aktivera förbättrad upplevelse för användarna i dag:**

1. Som global administratör eller Användaradministratör kan logga in på Azure-portalen och gå till **Azure Active Directory > Inställningar > Hantera inställningar för åtkomst till panelen förhandsversionsfunktioner**. 

2. I den **användare som kan använda förhandsversionen av funktioner för att registrera och hantera säkerhetsinformation – uppdatera** måste du välja att aktivera funktioner för en **vald grupp av användare** eller för **alla användare** .

Under de närmaste veckorna, vi kommer att ta bort möjligheten att aktivera den gamla kombinerade MFA/SSPR-registrering förhandsversion upplevelsen för klienter som inte redan har den aktiverad.

**Följ dessa steg om du vill se om kontrollen tas bort för din klient:**

1. Som global administratör eller Användaradministratör kan logga in på Azure-portalen och gå till **Azure Active Directory > Inställningar > Hantera inställningar för åtkomst till panelen förhandsversionsfunktioner**.  

2. Om den **användare som kan använda funktionerna förhandsgranskning för att registrera och hantera säkerhetsinformation** alternativet är inställt på **ingen**, alternativet tas bort från din klient.

Förhandsgranska upplevelse för användare, oavsett om du tidigare har aktiverat den gamla kombinerade MFA/SSPR-registreringen eller inte, den gamla upplevelsen kommer att stängas av för ett datum i framtiden. På grund av detta föreslår starkt vi att du flyttar till den nya och förbättrade upplevelsen så snart som möjligt.

Läs mer om registrering av förbättrad upplevelse, den [lågfrekvent förbättringar av Azure AD kombineras MFA och återställning av lösenord registrering](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Principhanteringsupplevelsen för användarflöden har uppdaterats

**Typ:** Ändrad funktion  
**Tjänstekategori:** B2C – konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C

Vi har uppdaterat principen skapandet och hanteringen processen för användarflöden (tidigare kallat, inbyggda principer) enklare. Den här nya upplevelsen är nu standard för alla Azure AD-klienter.

Du kan ange ytterligare feedback och förslag med hjälp av Leende eller frown ikonerna i det **Skicka oss feedback** överst i portalen skärmen.

Mer information om den nya princip hanteringsupplevelsen finns i den [Azure AD B2C har nu JavaScript anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogg.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Välj specifika sidelementsversioner som tillhandahålls av Azure AD B2C

**Typ:** Ny funktion  
**Tjänstekategori:** B2C – konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C

Du kan nu välja en specifik version av sidelement som tillhandahålls av Azure AD B2C. Genom att välja en specifik version, kan du testa uppdateringarna innan de visas på en sida och du kan få förutsägbara beteende. Dessutom kan du nu välja för att framtvinga specifik sida versioner om du vill tillåta JavaScript anpassningar. Om du vill aktivera den här funktionen går du till den **egenskaper** sida i dina användarflöden.

Läs mer om hur du väljer specifika versioner för sidelement i [Azure AD B2C har nu JavaScript anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogg.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurerbara lösenordskrav för slutanvändare för B2C (GA)

**Typ:** Ny funktion  
**Tjänstekategori:** B2C – konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C

Du kan nu ställa in din organisations lösenordskomplexitet för dina slutanvändare, i stället för att använda din ursprungliga Azure AD-lösenordsprincip. Från den **egenskaper** bladet för dina användare flöden (kallades tidigare din inbyggda principer), som du kan välja ett lösenordskomplexitet **enkel** eller **stark**, eller så kan du Skapa en **anpassad** kraven.

Mer information om konfiguration av lösenord komplexiteten krav finns i [konfigurera komplexitetskrav för lösenord i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nya standardmallar för anpassade autentiseringsupplevelser

**Typ:** Ny funktion  
**Tjänstekategori:** B2C – konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C

Du kan använda våra nya standardmallarna som finns på den **sidan layouter** bladet av dina användare (tidigare kallat inbyggda principer), märkesprodukter autentiseringsupplevelse för användarna att skapa en anpassad.

Mer information om hur du använder mallar finns i [Azure AD B2C har nu JavaScript anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Januari 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory B2B-samarbete som använder autentisering med engångslösenord (allmänt tillgänglig förhandsversion)

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

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – januari 2019

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration
 
I januari 2019 har vi lagt till stöd för dessa 35 nya appar med Federation i app-galleriet:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [personal paletten](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco samlingsnamnet](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Administratör för Internet](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [upphör att gälla påminnelse](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Stäng](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO-System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [Ar för Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 för Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn passagerarfartyget ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas plattform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nya Azure AD Identity Protection-förbättringar (förhandsversion)

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

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Ny App Lock-funktion för Microsoft Authenticator-appen på iOS- och Android-enheter

**Typ:** Ny funktion  
**Tjänstekategori:** Microsoft Authenticator-appen  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Om du vill skydda din enstaka lösenord, programinformation och app-inställningar kan aktivera du App Lock-funktionen i Microsoft Authenticator-appen. Aktivera App Lock innebär att du ska och att autentisera med hjälp av din PIN-kod eller biometriska varje gång du öppnar Microsoft Authenticator-appen.

Mer information finns i den [Microsoft Authenticator-appen vanliga frågor och svar](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Förbättrade exportfunktioner i Azure AD Privileged Identity Management (PIM)

**Typ:** Ny funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapacitet:** Privileged Identity Management

Privileged Identity Management (PIM) administratörer kan nu exportera alla aktiva och berättigade rolltilldelningar för en specifik resurs, vilket innefattar rolltilldelningar för alla underordnade resurser. Tidigare, det var svårt för administratörer att få en fullständig lista över rolltilldelningar för en prenumeration och de tvungna att exportera rolltilldelningar för varje specifik resurs.

Mer information finns i [visa aktivitet och granska historik för Azure-resursroller i PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>November/December 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Användare som tagits bort från den här synkroniseringsomfattningen växlar inte längre till enbart moln-konton

**Typ:** Korrigerat  
**Tjänstekategori:** Användarhantering  
**Produkten kapacitet:** Katalog

>[!Important]
>Vi har hört och förstå din frustrationen på grund av den här snabbkorrigeringen. Därför kan har vi återställts ändringen tills att vi kan göra korrigeringen enklare att implementera i din organisation.

Vi har ett fel har åtgärdats där flaggan DirSyncEnabled för en användare skulle bytas felaktigt en uppmaning till **FALSKT** när Active Directory Domain Services (AD DS)-objektet har undantas från omfånget för synkronisering och sedan flyttades till Papperskorgen i Azure AD på följande synkroniseringscykel. Till följd av denna snabbkorrigering om användaren uteslutas från synkronisering av omfång och därefter återställts från Azure AD-Papperskorgen, användarkontot är kvar som synkroniseras från en lokal AD, som förväntat och kan inte hanteras i molnet eftersom dess auktoritetskälla (SoA) finns kvar som lokala AD.

Innan den här snabbkorrigeringen uppstod ett problem när flaggan DirSyncEnabled har växlats till False. Denna lösning gav fel intryck av att dessa konton har omvandlats till molnbaserad objekt och att konton som kan hanteras i molnet. Dock konton fortfarande kvar sina SoA som lokalt och alla synkroniserade egenskaper (shadow attribut) kommer från lokala AD. Det här tillståndet orsakade flera problem i Azure AD och andra arbetsbelastningar i molnet (till exempel Exchange Online) som förväntas hantera dessa konton som synkroniseras från AD, men har nu fungerar, t.ex. molnbaserad konton.

För tillfället är det enda sättet att verkligen konvertera ett synkroniserad från AD-konto till molnbaserad konto genom att inaktivera DirSync på klientnivån, vilket då även utlöser en backend-åtgärder för att överföra SoA. Den här typen av SoA ändring kräver (men är inte begränsat till) rensar alla lokala relaterade attribut (till exempel LastDirSyncTime och shadow attribut) och skicka en signal till andra arbetsbelastningar att ha sitt respektive objekt konverteras till ett endast molnbaserat konto för .

Den här snabbkorrigeringen förhindrar därför direkt uppdateringar på attributet ImmutableID för en användare som synkroniseras från AD, vilket i vissa scenarier tidigare krävdes. Avsiktligt är ImmutableID för ett objekt i Azure AD som namnet antyder avsedd att vara inte kan ändras. Nya funktioner som implementeras i Azure AD Connect Health och Azure AD Connect-synkronisering klienten är tillgängliga för att bemöta sådana scenarier:

- **Uppdatering av storskaliga ImmutableID för många användare i en stegvis metod**
  
  Exempelvis kan behöva du göra en långa migrering för AD DS-skog. Lösning: Använda Azure AD Connect till **konfigurera Källankare** och när du migrerar kopierar du befintliga ImmutableID värden från Azure AD till den lokala AD DS-användarens ms-DS-konsekvens-Guid-attribut för den nya skogen. Mer information finns i [med ms-DS-ConsistencyGuid som sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Storskaliga ImmutableID uppdateringar för många användare i en bild**

  Till exempel när du implementerar Azure AD Connect du gör ett misstag och nu behöver du ändra SourceAnchor-attribut. Lösning: Inaktivera DirSync på klientnivån och rensa alla ogiltiga ImmutableID-värden. Mer information finns i [stänga av katalogsynkronisering för Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Rematch lokala användare med en befintlig användare i Azure AD** exempelvis kan en användare som har varit återskapas i AD DS genererar en dubblett i Azure AD-konto i stället för rematching med ett befintligt Azure AD-konto (överblivna objekt). Lösning: Använda Azure AD Connect Health i Azure-portalen för att mappa om källan ankare/ImmutableID. Mer information finns i [Orphaned objekt scenariot](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Icke-bakåtkompatibel ändring: Uppdateringar av gransknings- och logga in loggar schema via Azure Monitor

**Typ:** Ändrad funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering

Vi publicerar för närvarande både gransknings- och logga in log strömmar via Azure Monitor så att du kan smidigt integrera loggfilerna med din SIEM-verktyg eller med Log Analytics. Baserat på din feedback och inför den här funktionen är allmänt meddelande gör vi följande ändringar i våra schemat. Dessa schemaändringar och dess relaterade dokumentationsuppdateringar sker genom den första veckan i januari.

#### <a name="new-fields-in-the-audit-schema"></a>Nya fält i gransknings-schema
Vi lägger till en ny **åtgärdstypen** fältet om du vill ange vilken typ av åtgärd som utförs på resursen. Till exempel **Lägg till**, **uppdatering**, eller **ta bort**.

#### <a name="changed-fields-in-the-audit-schema"></a>Ändrade fält i gransknings-schema
Följande fält ändras Audit schemat:

|Fältnamn|Vad som ändrats|Gamla värden|Nya värden|
|----------|------------|----------|----------|
|Category|Det var den **tjänstnamn** fält. Nu är det den **Audit kategorier** fält. **Tjänstnamnet** har bytt namn till den **loggedByService** fält.|<ul><li>Kontoetablering</li><li>Kärnkatalog</li><li>Lösenordsåterställning via självbetjäning</li></ul>|<ul><li>Användarhantering</li><li>Grupphantering</li><li>Apphantering</li></ul>|
|targetResources|Innehåller **TargetResourceType** på den översta nivån.|&nbsp;|<ul><li>Princip</li><li>App</li><li>Användare</li><li>Grupp</li></ul>|
|loggedByService|Innehåller namnet på tjänsten som genererade granskningsloggen.|Null|<ul><li>Kontoetablering</li><li>Kärnkatalog</li><li>Återställning av lösenord för självbetjäning</li></ul>|
|Resultat|Ger resultatet för granskningsloggar. Tidigare var detta räknades upp, men vi nu visar det faktiska värdet.|<ul><li>0</li><li>1</li></ul>|<ul><li>Lyckades</li><li>Fel</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Ändrade fält i inloggning-schema
Logga in schemat ändras följande fält:

|Fältnamn|Vad som ändrats|Gamla värden|Nya värden|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Det var den **conditionalaccessPolicies** fält. Nu är det den **appliedConditionalAccessPolicies** fält.|Ingen förändring|Ingen förändring|
|conditionalAccessStatus|Ger resultatet för villkorlig åtkomst Principstatusen vid inloggning. Tidigare var detta räknades upp, men vi nu visar det faktiska värdet.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Lyckades</li><li>Fel</li><li>Används inte</li><li>Disabled</li></ul>|
|appliedConditionalAccessPolicies: result|Ger resultatet för enskilda villkorlig åtkomst Principstatus vid inloggning. Tidigare var detta räknades upp, men vi nu visar det faktiska värdet.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Lyckades</li><li>Fel</li><li>Används inte</li><li>Disabled</li></ul>|

Mer information om schemat finns i [tolkningar Azure AD granskningsloggar schemat i Azure Monitor (förhandsversion)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Identity Protection-förbättringar till den övervakade maskininlärningsmodellen och riskpoängsmotorn

**Typ:** Ändrad funktion  
**Tjänstekategori:** Identity Protection  
**Produkten kapacitet:** Riskpoäng

Förbättringar av Identity Protection-relaterade användare och logga in risken utvärdering av motorn kan hjälpa till att förbättra användaren risk Precision och täckning. Administratörer kanske märker att risknivån är inte längre direkt kopplad till risknivån för specifika identifieringar och att det finns en ökning av antalet och andelen riskfyllda inloggningshändelser.

Risk identifieringar är nu utvärdera de övervakade machine learning-modell som beräknar användarrisk genom att använda ytterligare funktioner i användarens inloggningar och ett mönster av identifieringar. Baserat på den här modellen kan kanske administratören användare med hög riskpoäng, även om identifieringar som är associerade med användaren är för låg eller medelhög risk. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Administratörer kan återställa sina egna lösenord med Microsoft Authenticator-appen (förhandsversion)

**Typ:** Ändrad funktion  
**Tjänstekategori:** Lösenordsåterställning via självbetjäning  
**Produkten kapacitet:** Användarautentisering

Azure AD-administratörer kan nu återställa sina egna lösenord med meddelanden för Microsoft Authenticator-appen eller en kod från en mobil autentiserare eller maskinvara token. Om du vill återställa sina egna lösenord, blir nu administratörer kan använda två av följande metoder:

- Avisering för Microsoft Authenticator-appen

- Andra mobil autentiserare / maskinvara token kod

- E-post

- Telefonsamtal

- Textmeddelande

Läs mer om hur du använder Microsoft Authenticator-appen för att återställa lösenord, [Azure AD lösenordsåterställning via självbetjäning - mobilappen och SSPR (förhandsversion)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Ny Azure AD-molnenhetsadministratörsroll (förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** Hantering och registrering av enhet  
**Produkten kapacitet:** Åtkomstkontroll

Administratörer kan tilldela användare till den nya Molnenhetsadministratör rollen att utföra administrationsåtgärder i cloud enhet. Användare som har tilldelats rollen Cloud Enhetsadministratörer kan aktivera, inaktivera och ta bort enheter i Azure AD, tillsammans med ska kunna läsa Windows 10 BitLocker-nycklar (om sådan finns) i Azure-portalen.

Mer information om roller och behörigheter finns i [Tilldela administratörsroller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Hantera dina enheter med den nya aktivitetstidsstämpeln i Azure AD (förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** Hantering och registrering av enhet  
**Produkten kapacitet:** Hantering av enhetslivscykel

Vi förstår att tiden du måste uppdatera och dra tillbaka din organisations enheter i Azure AD för att förhindra att inaktuella enheter i din miljö. För att hjälpa till med den här processen, uppdaterar Azure AD nu dina enheter med en ny aktivitet tidsstämpel, vilket hjälper dig att hantera enhetslivscykeln för din.

Läs mer om hur du hämtar och använder den här tidsstämpeln [How To: Hantera inaktuella enheter i Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratörer kan kräva att användare att godkänna användningsvillkor på varje enhet

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning
 
Administratörer kan nu aktivera den **användare måste samtycka på alla enheter** att kräva användarna godkänna dina användningsvillkor på varje enhet de använder på din klient.

Mer information finns i den [Per enhet användningsvillkor Använd avsnittet Azure Active Directory villkoren för användning funktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratörer kan konfigurera användningsvillkor att förfalla enligt ett återkommande schema

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning
 

Administratörer kan nu aktivera den **upphör att gälla medgivanden** alternativ för att göra användningsvillkor upphör att gälla för alla användare baserat på ditt angivna återkommande schema. Schemat kan vara årligen, bi årligen, Kvartalsvis eller varje månad. När användningsvillkoren upphör att gälla, måste användarna måste godkänna.

Mer information finns i den [lägga till användningsvillkor Använd avsnittet Azure Active Directory villkoren för användning funktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Administratörer kan konfigurera användningsvillkor så att de förfaller baserat på varje användares schema

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning

Administratörer kan nu ange en varaktighet som användaren måste godkänna användningsvillkor. Administratörer kan till exempel ange att användare måste godkänna användningsvillkoren efter 90 dagar.

Mer information finns i den [lägga till användningsvillkor Använd avsnittet Azure Active Directory villkoren för användning funktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nya e-postmeddelanden för Azure Active Directory-roller i Azure AD Privileged Identity Management (PIM)

**Typ:** Ny funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapacitet:** Privileged Identity Management
 
Kunder som använder Azure AD Privileged Identity Management (PIM) kan nu få en veckovis sammanfattad e-post, inklusive följande information för de senaste sju dagarna:

- Översikt över de främsta berättigade och permanenta rolltilldelningarna

- Antal användare aktiverar roller

- Antalet användare som tilldelas till roller i PIM

- Antalet användare som tilldelas till roller utanför PIM

- Antal användare ”göras permanent” i PIM

Läs mer om PIM och de tillgängliga e-postmeddelanden, [e-postmeddelanden i PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Gruppbaserad licensiering är nu allmänt tillgänglig

**Typ:** Ändrad funktion  
**Tjänstekategori:** Annat  
**Produkten kapacitet:** Katalog

Gruppbaserad licensiering är utanför offentlig förhandsversion och är nu allmänt tillgänglig. Som en del av den här kommersiella utgåvan blir tillgänglig, vi har gjort den här funktionen mer skalbart och har lagt till möjligheten att Ombearbeta gruppbaserad licensiering tilldelningar för en enskild användare och möjligheten att använda gruppbaserad licensiering med Office 365 E3/A3-licenser.

Mer information om gruppbaserad licensiering finns i [nyheter gruppbaserad licensiering i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – november 2018

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration
 
I November 2018 har vi lagt till dessa 26 nya appar med stöd för app-galleriet:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [gr Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [ftimme](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://www.drivedollar.com/Account/Login), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [oändlig Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [ HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy för företag centrala 365](https://accounting.zenegy.com/), [Everbridge medlem Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex appar – Klassiska Test](https://test.plexonline.com/signon), [Plex appar – klassisk](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex appar – UX](https://cloud.plex.com/sso), [Plex appar – IAM](https://accounts.plex.com/), [KONST - barnomsorg poster, närvaro och finansiella spårningssystemet](https://getcrafts.ca/craftsregistration) 

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Oktober 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD loggar nu arbete med Azure Log Analytics (allmänt tillgänglig förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering

Vi är glada att kunna meddela att du nu kan vidarebefordra dina loggar med Azure AD till Azure Log Analytics! Den här funktionen för de mest efterfrågade hjälper ger dig ännu bättre åtkomst till analytics för ditt företag, åtgärder, och säkerhet samt ett sätt att övervaka din infrastruktur. Mer information finns i den [Azure Active Directory-aktivitetsloggar i Azure Log Analytics finns nu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blogg.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nya federerade appar är tillgängliga i Azure AD-appgalleriet – oktober 2018

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration
 
I oktober 2018, har vi lagt till dessa 14 nya appar med stöd för app-galleriet:

[Mina Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 virtuell miljö](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler tre](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot kontroll](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>E-postmeddelanden för Azure AD Domain Services

**Typ:** Ny funktion  
**Tjänstekategori:** Azure AD Domain Services  
**Produkten kapacitet:** Azure AD Domain Services

Azure AD Domain Services ger aviseringar på Azure portal om felkonfigurationer eller problem med din hanterade domän. Dessa aviseringar innehåller stegvisa guider så att du kan försöka att åtgärda problem utan att behöva kontakta supporten.

Från och med oktober, du kommer att kunna anpassa inställningar för meddelanden för din hanterade domän så att när nya aviseringar sker, skickas ett e-postmeddelande till en utvald grupp personer, vilket eliminerar behovet av att kontinuerligt Kontrollera portal efter uppdateringar.

Mer information finns i [meddelandeinställningar i Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD-portalen stöder användning av domän-API:et ForceDelete för att bort anpassade domäner 

**Typ:** Ändrad funktion  
**Tjänstekategori:** Kataloghantering  
**Produkten kapacitet:** Katalog

Vi har glädjen att meddela att du kan nu använda ForceDelete domänen API att ta bort dina egna domännamn namnändringarna asynkront referenser, som användare, grupper och appar från ditt eget domännamn (contoso.com) tillbaka till den initiala domänen namn ( Contoso.onmicrosoft.com).

Den här ändringen hjälper dig att ta bort dina egna domännamn snabbare om din organisation inte längre använder namnet, eller om du vill använda domännamnet med en annan Azure AD.

Mer information finns i [ta bort ett anpassat domännamn](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---
