---
title: Vill du arkivera vad som är nytt i Azure Active Directory? | Microsoft Docs
description: Avsnittet Nyheter i avsnittet Översikt i den här innehålls uppsättningen innehåller 6 månaders aktivitet. Efter 6 månader tas objekten bort från huvud artikeln och infogas i den här Arkiv artikeln.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fcf1e5966be7708e7c6278839cbfbe5d65cdffa
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149125"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Vill du arkivera vad som är nytt i Azure Active Directory?

Den primära artikeln [Nyheter i Azure Active Directory? viktig](whats-new.md) information innehåller uppdateringar för de senaste sex månaderna, medan den här artikeln innehåller all äldre information.

Vad är nytt i Azure Active Directory? versions information innehåller information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar
- Föråldrade funktioner
- Planer för ändringar

---

## <a name="april-2019"></a>April 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Ny identifiering av Azure AD Threat Intelligence är nu tillgängligt som en del av Azure AD Identity Protection

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD Identity Protection  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Identifiering av Azure AD Threat Intelligence är nu tillgängligt som en del av den uppdaterade Azure AD Identity Protections funktionen. Med den här nya funktionen kan du ange ovanliga användar aktiviteter för en viss användare eller aktivitet som är konsekvent med kända angrepps mönster baserade på Microsofts interna och externa hot informations källor.

Mer information om den uppdaterade versionen av Azure AD Identity Protection finns i de [fyra viktiga Azure AD Identity Protection förbättringarna finns nu i den offentliga för hands](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) versionen av bloggen och [vad som är Azure Active Directory Identity Protection (uppdaterat)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) artiklar. Mer information om identifiering av Azure AD Threat Intelligence finns i artikeln [Azure Active Directory Identity Protection risk identifieringar](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Hantering av Azure AD-berättigande är nu tillgängligt (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets styrning  
**Produkt kapacitet:** Identitets styrning

Hantering av Azure AD-hantering, nu i offentlig för hands version, hjälper kunderna att delegera hantering av åtkomst paket, som definierar hur anställda och affärs partner kan begära åtkomst, vem som måste godkänna och hur länge de har åtkomst. Åtkomst paket kan hantera medlemskap i Azure AD-och Office 365-grupper, roll tilldelningar i företags program och roll tilldelningar för SharePoint Online-webbplatser. Läs mer om hantering av rättigheter i [Översikt över hantering av Azure AD-rättigheter](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Om du vill veta mer om bredden på Azure AD Identity Governance funktioner, inklusive Privileged Identity Management, åtkomst granskningar och användnings villkor, se [Vad är Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurera en namngivnings princip för Office 365-grupper i Azure AD portal (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete

Administratörer kan nu konfigurera en namngivnings princip för Office 365-grupper med hjälp av Azure AD-portalen. Den här ändringen hjälper till att upprätthålla konsekventa namngivnings konventioner för Office 365-grupper som skapats eller redigerats av användare i din organisation.

Du kan konfigurera namngivnings principen för Office 365-grupper på två olika sätt:

- Definiera prefix eller suffix som läggs till automatiskt i ett grupp namn.

- Ladda upp en anpassad uppsättning blockerade ord för din organisation, vilket inte är tillåtet i grupp namn (till exempel "VD, löner, HR").

Mer information finns i [framtvinga en namngivnings princip för Office 365-grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD-aktivitets loggar är nu tillgängliga i Azure Monitor (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Vi presenterar en ny insikts funktion i Log Analytics för att hjälpa dig att åtgärda dina synpunkter på visualiseringar med aktivitets loggarna i Azure AD. Den här funktionen hjälper dig att få insikter om dina Azure AD-resurser med hjälp av våra interaktiva mallar, som kallas arbets böcker. Dessa färdiga arbets böcker kan ge information om appar eller användare och inkludera:

- **Inloggningar.** Innehåller information för appar och användare, inklusive inloggnings plats, klient och version för operativ systemet eller webbläsare och antalet lyckade eller misslyckade inloggningar.

- **Äldre autentisering och villkorlig åtkomst.** Innehåller information för appar och användare som använder äldre autentisering, inklusive Multi-Factor Authentication användning som utlösts av principer för villkorlig åtkomst, appar som använder principer för villkorlig åtkomst och så vidare.

- **Analys av inloggnings problem.** Hjälper dig att avgöra om inloggnings felen inträffar på grund av en användar åtgärd, princip problem eller din infrastruktur.

- **Anpassade rapporter.** Du kan skapa nya eller redigera befintliga arbets böcker som hjälper dig att anpassa insikter-funktionen för din organisation.

Mer information finns i [så här använder du Azure Monitor-arbetsböcker för Azure Active Directory-rapporter](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – april 2019

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I april 2019 har vi lagt till dessa 21 nya appar med stöd för federation i app-galleriet:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Bänkning](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), PageDNA, [EduBrite](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial) [LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect ](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [spetsig anslutning](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (rollbaserad SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent egendoms hantering](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [INDIGGO](https://indiggolead.com/)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nya frekvens alternativ för åtkomst granskningar och flera roll val

**Typ:** Ny funktion  
**Tjänste kategori:** Åtkomst granskningar  
**Produkt kapacitet:** Identitets styrning

Nya uppdateringar i åtkomst granskningar i Azure AD gör att du kan:

- Ändra frekvensen för dina åtkomst granskningar till **halv varje år**, förutom de tidigare befintliga alternativen varje vecka, varje månad, kvartals vis och varje år.

- Välj flera Azure AD-och Azure-resurs roller när du skapar en enda åtkomst granskning. I den här situationen konfigureras alla roller med samma inställningar och alla granskare meddelas på samma tid.

Mer information om hur du skapar en åtkomst granskning finns i [skapa en åtkomst granskning av grupper eller program i åtkomst granskningar för Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-postaviserings system överförs, skickar ny e-postavsändar information för vissa kunder

**Typ:** Ändrad funktion  
**Tjänste kategori:** AD Sync  
**Produkt kapacitet:** Systemet

Azure AD Connect håller på att överföra våra e-postvarnings system, vilket potentiellt visar vissa kunder en ny e-postsändare. För att lösa detta måste du lägga till `azure-noreply@microsoft.com` till din organisations lista över tillåtna eller så kan du inte fortsätta att ta emot viktiga aviseringar från Office 365, Azure eller dina Sync-tjänster.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Ändringar av UPN-suffix har nu slutförts mellan federerade domäner i Azure AD Connect

**Typ:** Fastsatt  
**Tjänste kategori:** AD Sync  
**Produkt kapacitet:** Systemet

Du kan nu ändra en användares UPN-suffix från en federerad domän till en annan federerad domän i Azure AD Connect. Den här korrigeringen innebär att du inte längre bör uppleva FederatedDomainChangeError-felmeddelande under synkroniseringsprocessen eller få ett meddelande om att det inte går att uppdatera det här objektet i Azure Active Directory eftersom attributet [ FederatedUser. UserPrincipalName] är inte giltigt. Uppdatera värdet i dina lokala katalog tjänster.

Mer information finns i [fel sökning av fel under synkronisering](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Ökad säkerhet med hjälp av app Protection-baserad princip för villkorlig åtkomst i Azure AD (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & Skydd för identitets säkerhet

App Protection-baserad villkorlig åtkomst är nu tillgänglig med hjälp av principen **Kräv app-skydd** . Den här nya principen hjälper till att öka din organisations säkerhet genom att bidra till att förhindra:

- Användare får åtkomst till appar utan en Microsoft Intune-licens.

- Användare kan inte hämta en Microsoft Intune skydds princip för appar.

- Användare får åtkomst till appar utan en konfigurerad Microsoft Intune App Protection-princip.

Mer information finns i [så här kräver du app Protection-princip för Cloud app-åtkomst med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nytt stöd för enkel inloggning och villkorlig åtkomst i Azure AD i Microsoft Edge (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Vi har förbättrat Azure AD-supporten för Microsoft Edge, inklusive att tillhandahålla nytt stöd för enkel inloggning och villkorlig åtkomst i Azure AD. Om du tidigare har använt Microsoft Intune Managed Browser kan du nu använda Microsoft Edge i stället.

Mer information om hur du konfigurerar och hanterar enheter och appar med hjälp av villkorlig åtkomst finns i [Kräv hanterade enheter för Cloud app-åtkomst med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) och [Kräv godkända klient program för Cloud app-åtkomst med villkorlig åtkomst ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Mer information om hur du hanterar åtkomst med hjälp av Microsoft Edge med Microsoft Intune-principer finns i [Hantera Internet åtkomst med en Microsoft Intune-skyddad webbläsare](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Mars 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Identitets upplevelse ramverk och anpassad princip support i Azure Active Directory B2C är nu tillgänglig (GA)

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C

Nu kan du skapa anpassade principer i Azure AD B2C, inklusive följande uppgifter, som stöds i stor skala och under vårt Azure SLA:

- Skapa och ladda upp användar transporter för anpassad autentisering med hjälp av anpassade principer.

- Beskriv användar resan steg för steg som utbyten mellan anspråks leverantörer.

- Definiera villkorlig grenning i användar resor.

- Transformera och mappa anspråk för användning i real tids beslut och kommunikation.

- Använd REST API-aktiverade tjänster i dina anpassade användar resor för autentisering. Till exempel med e-postleverantörer, CRMs och tillverkarspecifika auktoriserings system.

- Federera med identitets leverantörer som är kompatibla med OpenIDConnect-protokollet. Till exempel med flera klient organisationer för Azure AD, sociala konto leverantörer eller två-Factor Verification-leverantörer.

För ytterligare information om hur du skapar anpassade principer, se [Developer-anteckningar för anpassade principer i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) och Läs [Alex-Simons blogg inlägg, inklusive fallstudier](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – mars 2019

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I mars 2019 har vi lagt till dessa 14 nya appar med stöd för federation i app-galleriet:

[ISEC7 Mobile Exchange delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [förklarings-baserade gransknings system](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool prestanda](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode ](https://cinode.com/), [Iris intranät](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit-horisonter](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [aktivitet](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nya Zscaler-och Atlassian-etablerings anslutningar i Azure AD-galleriet – mars 2019

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part

Automatisera att skapa, uppdatera och ta bort användar konton för följande appar:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler två](https://aka.ms/ZscalerTwoProvisioning), [Zscaler tre](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Mer information om hur du bättre skyddar din organisation genom att tillhandahålla automatiserade användar konton finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Återställa och hantera dina borttagna Office 365-grupper i Azure AD-portalen

**Typ:** Ny funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete

Nu kan du Visa och hantera dina borttagna Office 365-grupper från Azure AD-portalen. Den här ändringen hjälper dig att se vilka grupper som är tillgängliga för återställning, tillsammans med att låta dig ta bort alla grupper som inte behövs i din organisation Permanent.

Mer information finns i [återställa inaktuella eller borttagna grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Enkel inloggning är nu tillgängligt för Azure AD SAML-skyddade lokala appar via programproxy (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control

Nu kan du tillhandahålla enkel inloggning (SSO) för lokala, SAML-autentiserade appar, tillsammans med fjärråtkomst till dessa appar via programproxyn. Mer information om hur du konfigurerar SAML SSO med dina lokala appar finns i [SAML enkel inloggning för lokala program med Application Proxy (för hands version)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Klient program i begär ande slingor avbryts för att förbättra tillförlitligheten och användar upplevelsen

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Klient program kan felaktigt skicka hundratals av samma inloggnings begär Anden under en kort tids period. Dessa förfrågningar, oavsett om de är lyckade eller inte, bidrar till en dåligt användar upplevelse och förhöjda arbets belastningar för IDP, ökande svars tid för alla användare och för att minska tillgängligheten för IDP.

Den här uppdateringen skickar ett `invalid_grant` fel: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` till klient program som skickar dubbla begär Anden flera gånger under en kort tids period, utöver den normala drifts omfattningen. Klient program som stöter på det här problemet bör visa en interaktiv prompt som kräver att användaren loggar in igen. Mer information om den här ändringen och hur du åtgärdar din app om det här felet påträffas finns i [Vad är nytt för autentisering?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nya gransknings loggar användar upplevelsen är nu tillgänglig

**Typ:** Ändrad funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Vi har skapat en ny sida med **gransknings loggar** för Azure AD som hjälper till att förbättra både läsbarhet och hur du söker efter din information. Om du vill se sidan nya **gransknings loggar** väljer du **gransknings loggar** i avsnittet **aktivitet** i Azure AD.

![Sidan nya gransknings loggar med exempel information](media/whats-new/audit-logs-page.png)

Mer information om sidan nya **gransknings loggar** finns [i gransknings aktivitets rapporter i Azure Active Directory-portalen](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nya varningar och rikt linjer som hjälper dig att förhindra oavsiktlig administratörs utelåsning från felkonfigurerade principer för villkorlig åtkomst

**Typ:** Ändrad funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & Skydd för identitets säkerhet

För att förhindra att administratörer oavsiktligt låser sig själva ut från sina egna klienter via felkonfigurerade principer för villkorlig åtkomst har vi skapat nya varningar och uppdaterad vägledning i Azure Portal. Mer information om den nya vägledningen finns i [Vad är tjänst beroenden i Azure Active Directory villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Förbättrade användnings villkor för slutanvändare på mobila enheter

**Typ:** Ändrad funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Ledning

Vi har uppdaterat våra befintliga användnings villkor för att hjälpa till att förbättra hur du granskar och godkänner användnings villkoren på en mobil enhet. Nu kan du zooma in och ut, gå tillbaka, ladda ned informationen och välja hyperlänkar. Mer information om de uppdaterade användnings villkoren finns i Azure Active Directory användnings [villkor](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nya hämtnings möjligheter för Azure AD-aktivitets loggar är tillgängliga

**Typ:** Ändrad funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Nu kan du hämta stora mängder aktivitets loggar direkt från Azure Portal. Med den här uppdateringen kan du:

- Hämta upp till 250 000 rader.

- Få ett meddelande när nedladdningen är klar.

- Anpassa ditt fil namn.

- Ta reda på utdataformatet, antingen JSON eller CSV.

Mer information om den här funktionen finns i [snabb start: Hämta en gransknings rapport med hjälp av Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Brytar förändring: uppdateringar av villkors utvärderingen av Exchange ActiveSync (EAS)

**Typ:** Planera för ändring  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** Access Control

Vi håller på att uppdatera hur Exchange ActiveSync (EAS) utvärderar följande villkor:

- Användar plats, baserat på land, region eller IP-adress

- Inloggnings risk

- Enhets plattform

Om du tidigare har använt dessa villkor i dina principer för villkorlig åtkomst bör du tänka på att villkors beteendet kan ändras. Om du till exempel tidigare använde villkoret användar plats i en princip kan du se till att principen nu hoppas över baserat på användarens plats.

---

## <a name="february-2019"></a>Februari 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurerbar Azure AD SAML token Encryption (offentlig för hands version) 

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Nu kan du konfigurera en SAML-app som stöds för att ta emot krypterade SAML-token. När Azure AD är konfigurerat och används med en app, krypterar Azure AD de utgivna SAML-beställarna med en offentlig nyckel som hämtats från ett certifikat som lagras i Azure AD.

Mer information om hur du konfigurerar din kryptering för SAML-token finns i [Konfigurera Azure AD SAML token Encryption](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Skapa en åtkomst granskning för grupper eller appar med hjälp av åtkomst granskningar för Azure AD

**Typ:** Ny funktion  
**Tjänste kategori:** Åtkomst granskningar  
**Produkt kapacitet:** Ledning

Du kan nu inkludera flera grupper eller appar i en enda Azure AD Access-granskning för grupp medlemskap eller program tilldelning. Åtkomst granskningar med flera grupper eller appar konfigureras med samma inställningar och alla granskare som ingår meddelas på samma gång.

Mer information om hur du skapar en åtkomst granskning med hjälp av åtkomst granskningar i Azure AD finns i [skapa en åtkomst granskning av grupper eller program i åtkomst granskningar för Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – februari 2019

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I februari 2019 har vi lagt till dessa 27 nya appar med stöd för federation i app-galleriet:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [fat-finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [netstack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Behörighet klicka, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [seismisk](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [dela en dröm](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Knowledge Var LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope-data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [NetOp-portalen](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud av gener](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp produktivitets plattform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Förbättrad kombination av MFA-/SSPR-registrering

**Typ:** Ändrad funktion  
**Tjänste kategori:** Lösen ords återställning via självbetjäning  
**Produkt kapacitet:** Användarautentisering

Som svar på kundfeedback har vi utökat den kombinerade för hands versionen av MFA/SSPR-registrering, vilket hjälper användarna att snabbt registrera sina säkerhets uppgifter för både MFA och SSPR. 

**Följ dessa steg om du vill aktivera den förbättrade upplevelsen för dina användare ' idag:**

1. Som global administratör eller användar administratör loggar du in på Azure Portal och går till **Azure Active Directory > användar inställningar > hantera inställningar för åtkomst panelens för hands versions funktioner**. 

2. I de **användare som kan använda för hands versions funktioner för att registrera och hantera säkerhets information – uppdaterings** alternativ väljer du att aktivera funktionerna för en **vald grupp av användare** eller för **alla användare**.

Under de kommande veckorna kommer vi att ta bort möjligheten att aktivera den tidigare kombinerade för hands versionen av MFA-/SSPR-registrering för klienter som inte redan har den aktiverat.

**Följ dessa steg om du vill se om kontrollen kommer att tas bort för din klient:**

1. Som global administratör eller användar administratör loggar du in på Azure Portal och går till **Azure Active Directory > användar inställningar > hantera inställningar för åtkomst panelens för hands versions funktioner**.  

2. Om de **användare som kan använda alternativet för för hands versions funktioner för att registrera och hantera säkerhets information** har angetts till **ingen**, tas alternativet bort från din klient.

Oavsett om du tidigare har aktiverat den tidigare samlade för hands versionen av MFA/SSPR-registrering för användare eller inte, kommer den gamla upplevelsen att inaktive ras vid ett framtida datum. Därför föreslår vi starkt att du flyttar till den nya, förbättrade upplevelsen så snart som möjligt.

För ytterligare information om den förbättrade registrerings upplevelsen, se de [häftiga förbättringarna i registrerings upplevelsen av Azure AD kombinerat MFA och lösen ords återställning](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Uppdaterad princip hanterings upplevelse för användar flöden

**Typ:** Ändrad funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C

Vi har uppdaterat processen för att skapa och hantera principer för användar flöden (tidigare kallade inbyggda principer) enklare. Den nya upplevelsen är nu standard för alla dina Azure AD-klienter.

Du kan ge ytterligare feedback och förslag genom att använda ikonerna leende eller bister min i avsnittet **Skicka feedback för oss** överst på Portal skärmen.

Mer information om den nya princip hanterings upplevelsen finns i [Azure AD B2C nu har Java Script-anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) -bloggen.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Välj vissa sid element versioner som tillhandahålls av Azure AD B2C

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C

Nu kan du välja en speciell version av sid elementen som tillhandahålls av Azure AD B2C. Genom att välja en viss version kan du testa dina uppdateringar innan de visas på en sida och du kan få förutsägbara beteenden. Dessutom kan du välja att använda vissa sid versioner för att tillåta anpassade JavaScript-anpassningar. Om du vill aktivera den här funktionen går du till sidan **Egenskaper** i dina användar flöden.

Mer information om hur du väljer vissa versioner av sid element finns i avsnittet [Azure AD B2C nu har Java Script-anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) -bloggen.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurerbara lösen ords krav för B2C (GA)

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C

Nu kan du konfigurera din organisations lösen ords komplexitet för dina slutanvändare, i stället för att behöva använda din interna lösen ords princip för Azure AD. Från **egenskaps** bladet för dina användar flöden (kallades tidigare dina inbyggda principer) kan du välja ett lösen ords komplexitet som är **enkelt** eller **starkt**, eller så kan du skapa en **anpassad** uppsättning krav.

Mer information om konfiguration av lösen ords komplexitets krav finns i [Konfigurera komplexitets krav för lösen ord i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nya standardmallar för anpassade funktioner för anpassad autentisering

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C

Du kan använda våra nya standardmallar som finns på **bladet sidlayouter i** dina användar flöden (tidigare kallade inbyggda principer) för att skapa en anpassad anpassad autentisering för dina användare.

Mer information om hur du använder mallarna finns i [Azure AD B2C nu har Java Script-anpassning och många nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Januari 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory B2B-samarbete med autentisering med eng ång slö sen ord (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C

Vi har infört autentisering med eng ång slö sen ord för B2B-gäst användare som inte kan autentiseras via andra sätt som Azure AD, en Microsoft-konto (MSA) eller Google Federation. Den här nya autentiseringsmetoden innebär att gäst användare inte behöver skapa nya Microsoft-konto. I stället kan en gäst användare begära att en tillfällig kod skickas till en e-postadress, samtidigt som du löser in en inbjudan eller använder en delad resurs. Med den här tillfälliga koden kan gäst användaren fortsätta att logga in.

Mer information finns i [e-postautentisering med eng ång slö sen ord (för hands version)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) och bloggen. [Azure AD gör delning och samarbete sömlöst för alla användare med ett konto](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nya inställningar för Azure AD-programproxy cookies

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control

Vi har introducerat tre nya cookie-inställningar som är tillgängliga för dina appar som publiceras via programproxy:

- **Använd endast HTTP-cookie.** Ställer in flaggan **HTTPOnly** på din Application Proxy-åtkomst och sessionscookies. Att aktivera den här inställningen ger ytterligare säkerhets fördelar, till exempel att förhindra att cookies kopieras eller ändras via skript på klient sidan. Vi rekommenderar att du aktiverar den här flaggan (Välj **Ja**) för de extra fördelarna.

- **Använd säker cookie.** Anger **säker** flagga för programproxyns åtkomst och sessionscookies. Att aktivera den här inställningen ger ytterligare säkerhets förmåner genom att se till att cookies bara överförs via säkra TLS-kanaler, t. ex. HTTPS. Vi rekommenderar att du aktiverar den här flaggan (Välj **Ja**) för de extra fördelarna.

- **Använd beständig cookie.** Förhindrar att åtkomst till cookies upphör att gälla när webbläsaren stängs. Dessa cookies sist under åtkomsttoken. Men cookies återställs om förfallo tiden nås eller om användaren manuellt tar bort cookien. Vi rekommenderar att du behåller standardinställningen **Nej**och bara aktiverar inställningen för äldre appar som inte delar cookies mellan processer.

Mer information om de nya cookies finns i [cookie-inställningar för att komma åt lokala program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – januari 2019

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I januari 2019 har vi lagt till dessa 35 nya appar med stöd för federation i app-galleriet:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [personal-palett](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco paraply](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet åtkomst administratör](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [förfallo påminnelse](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial) [, Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [fungerande](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease ](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO system](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ar för företag](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 för Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager ](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [korn Alp](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas-plattform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nya Azure AD Identity Protection förbättringar (offentlig för hands version)

**Typ:** Ändrad funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Vi är glada över att kunna meddela att vi har lagt till följande förbättringar i Azure AD Identity Protection Public Preview-erbjudandet, inklusive:

- Ett uppdaterat och mer integrerat användar gränssnitt

- Ytterligare API:er

- Förbättrad risk bedömning via Machine Learning

- Anpassning av hela produkten över riskfyllda användare och riskfyllda inloggningar

För ytterligare information om förbättringarna, se [Vad är Azure Active Directory Identity Protection (uppdaterat)?](https://aka.ms/IdentityProtectionDocs) för att lära dig mer och dela med dig av dina tankar genom produkt frågorna.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Ny app lock-funktion för Microsoft Authenticator-appen på iOS-och Android-enheter

**Typ:** Ny funktion  
**Tjänste kategori:** Microsoft Authenticator app  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Om du vill behålla dina lösen ord, appdata och appinställningar säkrare, kan du aktivera appens lås funktion i Microsoft Authenticator-appen. Genom att aktivera app-lås kan du bli ombedd att autentisera med hjälp av din PIN-kod eller bio metrisk varje gång du öppnar Microsoft Authenticator-appen.

Mer information finns i [vanliga frågor och svar om Microsoft Authenticator app](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Förbättrade export funktioner för Azure AD Privileged Identity Management (PIM)

**Typ:** Ny funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management

Privileged Identity Management (PIM)-administratörer kan nu exportera alla aktiva och berättigade roll tilldelningar för en speciell resurs, som innehåller roll tilldelningar för alla underordnade resurser. Tidigare var det svårt för administratörer att få en fullständig lista över roll tilldelningar för en prenumeration och de behövde exportera roll tilldelningar för varje enskild resurs.

Mer information finns i [Visa aktivitet och gransknings historik för Azure Resource roles i PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>November/december 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Användare som tas bort från omfånget växlar inte längre till enbart moln konton

**Typ:** Fastsatt  
**Tjänste kategori:** Användar hantering  
**Produkt kapacitet:** Katalogen

>[!Important]
>Vi har hört och förstår din hjälp på grund av den här korrigeringen. Vi har därför återställt den här ändringen tills vi kan göra det lättare för dig att implementera i din organisation.

Vi har åtgärdat ett fel där en användares DirSyncEnabled flagga skulle felaktigt växlas till **false** när Active Directory Domain Services (AD DS)-objektet uteslöts från omfånget för synkronisering och sedan flyttades till pappers korgen i Azure AD på följande synkronisering. Till följd av denna korrigering, om användaren undantas från omfånget för synkronisering och sedan återställs från Azure AD-pappers korgen, är användar kontot kvar som synkroniserat från den lokala AD-platsen som förväntat och kan inte hanteras i molnet eftersom dess auktoritets källa (SoA) förblir som lokal AD.

Före den här korrigeringen uppstod ett problem när flaggan DirSyncEnabled växlades till false. Det gav fel intryck att dessa konton konverterades till molnbaserade objekt och att kontona kan hanteras i molnet. Men kontona har kvar sin SoA som lokalt och alla synkroniserade egenskaper (Shadow-attribut) som kommer från den lokala AD-platsen. Det här tillståndet orsakade flera problem i Azure AD och andra moln arbets belastningar (t. ex. Exchange Online) som förväntar sig att hantera dessa konton som synkroniserade från AD, men som nu fungerar som molnbaserade konton.

För närvarande är det enda sättet att verkligen konvertera ett synkroniserat-från-AD-konto till endast ett moln konto genom att inaktivera DirSync på klient nivån, vilket utlöser en server dels åtgärd för att överföra SoA. Den här typen av SoA-ändring kräver (men är inte begränsad till) rengöring av alla lokala relaterade attribut (till exempel LastDirSyncTime och skuggning) och sändning av en signal till andra moln arbets belastningar så att dess respektive objekt konverteras till ett enbart moln konto för .

Den här korrigeringen förhindrar därför direkta uppdateringar av ImmutableID-attributet för en användare som synkroniseras från AD, som i vissa scenarier tidigare var obligatoriska. Enligt design är ImmutableID för ett objekt i Azure AD, som namnet antyder, avsett att vara oföränderligt. Nya funktioner som implementeras i Azure AD Connect Health-och Azure AD Connect-synkroniseringstjänsten är tillgängliga för att lösa sådana scenarier:

- **Storskalig ImmutableID-uppdatering för många användare i en stegvis metod**
  
  Du måste t. ex. göra en lång AD DS-migrering mellan skogar. Lösning: Använd Azure AD Connect om du vill **Konfigurera käll ankare** och, när användaren migreras, kopiera de befintliga ImmutableID-värdena från Azure AD till den lokala AD DS-användarens ms-DS-attribut-GUID-attribut i den nya skogen. Mer information finns i [using ms-DS-ConsistencyGuid som sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Storskaliga ImmutableID-uppdateringar för många användare i en bild**

  Exempel: när du implementerar Azure AD Connect du göra fel och nu måste du ändra attributet SourceAnchor. Lösning: inaktivera DirSync på klient nivån och rensa alla ogiltiga ImmutableID-värden. Mer information finns i [Inaktivera katalog-synkronisering för Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Matcha lokal användare med en befintlig användare i Azure AD** En användare som har återskapats i AD DS genererar till exempel en dubblett i Azure AD-konto i stället för att matcha det med ett befintligt Azure AD-konto (överblivna objekt). Lösning: Använd Azure AD Connect Health i Azure Portal för att mappa om käll fäst punkten/ImmutableID. Mer information finns i [scenario med överblivna objekt](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Bryta ändring: uppdateringar av schemat för gransknings-och inloggnings loggar via Azure Monitor

**Typ:** Ändrad funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Vi publicerar för närvarande både gransknings-och inloggnings logg strömmar via Azure Monitor, så att du sömlöst kan integrera loggfilerna med dina SIEM-verktyg eller med Log Analytics. Utifrån din feedback och för den här funktionens allmänna tillgänglighets meddelande gör vi följande ändringar i vårt schema. De här schema ändringarna och de relaterade dokumentations uppdateringarna sker den första veckan i januari.

#### <a name="new-fields-in-the-audit-schema"></a>Nya fält i gransknings schema
Vi lägger till ett nytt **Åtgärds typ** fält för att ange vilken typ av åtgärd som utförs på resursen. Du kan till exempel **lägga till**, **Uppdatera**eller **ta bort**.

#### <a name="changed-fields-in-the-audit-schema"></a>Ändrade fält i gransknings schema
Följande fält ändras i gransknings schemat:

|Fältnamn|Vad som ändrats|Gamla värden|Nya värden|
|----------|------------|----------|----------|
|Kategori|Detta är **tjänstens namn** fält. Nu är fältet **gransknings kategorier** . **Tjänst namnet** har bytt namn till fältet **loggedByService** .|<ul><li>Kontoetablering</li><li>Kärnkatalog</li><li>Lösen ords återställning via självbetjäning</li></ul>|<ul><li>Användarhantering</li><li>Grupphantering</li><li>Hantering av appar</li></ul>|
|targetResources|Innehåller **TargetResourceType** på den översta nivån.|&nbsp;|<ul><li>Princip</li><li>App</li><li>Användare</li><li>Grupp</li></ul>|
|loggedByService|Innehåller namnet på tjänsten som skapade gransknings loggen.|Null|<ul><li>Kontoetablering</li><li>Kärnkatalog</li><li>Återställning av lösenord för självbetjäning</li></ul>|
|Resultat|Visar resultatet av gransknings loggarna. Tidigare räknades detta upp, men nu visar vi det faktiska värdet.|<ul><li>0</li><li>1</li></ul>|<ul><li>Lyckades</li><li>Fel</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Ändrade fält i inloggnings schema
Följande fält ändras i inloggnings schema:

|Fältnamn|Vad som ändrats|Gamla värden|Nya värden|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Detta var fältet **conditionalaccessPolicies** . Nu är fältet **appliedConditionalAccessPolicies** .|Ingen förändring|Ingen förändring|
|conditionalAccessStatus|Visar resultatet av den villkorliga åtkomst princip statusen vid inloggning. Tidigare räknades detta upp, men nu visar vi det faktiska värdet.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Lyckades</li><li>Fel</li><li>Används inte</li><li>Disabled</li></ul>|
|appliedConditionalAccessPolicies: resultat|Visar resultatet av en enskild princip status för villkorlig åtkomst vid inloggning. Tidigare räknades detta upp, men nu visar vi det faktiska värdet.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Lyckades</li><li>Fel</li><li>Används inte</li><li>Disabled</li></ul>|

Mer information om schemat finns i [tolka schemat för gransknings loggar i Azure AD i Azure Monitor (för hands version)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Förbättringar av identitets skydd i den övervakade Machine Learning-modellen och risk Poäng motorn

**Typ:** Ändrad funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** Risk Poäng

Förbättringar av motorn för identitets skydd – relaterad användare och inloggnings riskbedömning kan hjälpa till att förbättra precisionen och täckningen av användarnas risker. Administratörer kan se att användar risk nivån inte längre är direkt länkad till risk nivån för vissa identifieringar, och att det finns en ökning av antalet och graden av riskfyllda inloggnings händelser.

Risk identifieringar utvärderas nu av den övervakade Machine Learning-modellen, som beräknar användar risken genom att använda ytterligare funktioner i användarens inloggnings program och ett mönster för identifieringar. Utifrån den här modellen kan administratören hitta användare med hög risk poäng, även om identifieringar som är associerade med den användaren är av låg eller medelhög risk. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Administratörer kan återställa sina egna lösen ord med hjälp av Microsoft Authenticator-appen (offentlig för hands version)

**Typ:** Ändrad funktion  
**Tjänste kategori:** Lösen ords återställning via självbetjäning  
**Produkt kapacitet:** Användarautentisering

Azure AD-administratörer kan nu återställa sina egna lösen ord med hjälp av Microsoft Authenticator app-meddelanden eller en kod från valfri mobilapp eller maskinvarubaserad token. Administratörer kan nu använda två av följande metoder för att återställa sina egna lösen ord:

- Microsoft Authenticator app-avisering

- Annan mobil autentiserare app/maskinvaru-token kod

- E-post

- Telefonsamtal

- Textmeddelande

Mer information om hur du använder Microsoft Authenticator-appen för att återställa lösen ord finns i [Azure AD Self-Service Password rereset-Mobile app and SSPR (för hands version)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Ny administratörs roll för moln enheten i Azure AD (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Enhets registrering och hantering  
**Produkt kapacitet:** Åtkomst kontroll

Administratörer kan tilldela användare till den nya rollen som administratör för moln enhet för att utföra administrativa uppgifter. Användare som tilldelats rollen som moln enhets administratörer kan aktivera, inaktivera och ta bort enheter i Azure AD, tillsammans med att kunna läsa Windows 10 BitLocker-nycklar (om de finns) i Azure Portal.

Mer information om roller och behörigheter finns [i tilldela administratörs roller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Hantera dina enheter med den nya aktivitetens tidstämpel i Azure AD (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Enhets registrering och hantering  
**Produkt kapacitet:** Hantering av enhetens livs cykel

Vi inser att du med tiden måste uppdatera och dra tillbaka dina organisationers enheter i Azure AD för att undvika att ha inaktuella enheter i din miljö. För att hjälpa dig med den här processen uppdaterar Azure AD dina enheter med en ny aktivitets tids stämpling, vilket hjälper dig att hantera din enhets livs cykel.

Mer information om hur du hämtar och använder den här tidsstämpeln finns i [så här gör du: hantera de inaktuella enheterna i Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratörer kan kräva att användarna godkänner användnings villkoren på varje enhet

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Ledning
 
Administratörer kan nu aktivera alternativet **Kräv att användare godkänner varje enhet** för att kräva att användarna godkänner dina användnings villkor på varje enhet som de använder på din klient organisation.

Mer information finns i [avsnittet användnings villkor per enhet i den Azure Active Directory användnings villkors funktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratörer kan konfigurera användnings villkor så att de upphör att gälla baserat på ett återkommande schema

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Ledning
 

Administratörer kan nu aktivera alternativet för att alternativet **upphör** att gälla upphör att gälla för alla dina användare utifrån det angivna återkommande schemat. Schemat kan vara årligen, två per år, varje kvartal eller varje månad. När användnings villkoren upphör att gälla måste användarna godkänna.

Mer information finns i [avsnittet lägga till användnings villkor i funktionen Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)användnings villkor.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Administratörer kan konfigurera användnings villkor så att de upphör att gälla baserat på varje användares schema

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Ledning

Administratörer kan nu ange en varaktighet då användaren måste godkänna användnings villkoren igen. Administratörer kan till exempel ange att användarna måste godkänna ett användnings villkor var 90 dag.

Mer information finns i [avsnittet lägga till användnings villkor i funktionen Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)användnings villkor.
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nya Azure AD Privileged Identity Management-e-postmeddelanden (PIM) för Azure Active Directory roller

**Typ:** Ny funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
Kunder som använder Azure AD Privileged Identity Management (PIM) kan nu få ett e-postmeddelande med veckovis sammandrag, inklusive följande information under de senaste sju dagarna:

- Översikt över de mest kvalificerade och permanenta roll tilldelningarna

- Antal användare som aktiverar roller

- Antal användare som tilldelats roller i PIM

- Antal användare som tilldelats roller utanför PIM

- Antal användare som har gjorts permanenta i PIM

Mer information om PIM och tillgängliga e-postaviseringar finns i [e-postaviseringar i PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Group-based Licensing är nu allmänt tillgänglig

**Typ:** Ändrad funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Katalogen

Gruppbaserad licensiering är utanför den offentliga för hands versionen och är nu allmänt tillgänglig. Som en del av den här allmänna versionen har vi gjort den här funktionen mer skalbar och har lagt till möjligheten att ombearbeta gruppbaserade licens tilldelningar för en enskild användare och möjligheten att använda gruppbaserad licensiering med Office 365 E3/a3-licenser.

Mer information om gruppbaserad licensiering finns [i vad är gruppbaserad licensiering i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – november 2018

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I november 2018 har vi lagt till dessa 26 nya appar med stöd för federation i app-galleriet:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [gra-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [oändlig Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [ Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [drivgarn](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [EverBridge medlems Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps-klassiskt test ](https://test.plexonline.com/signon), [Plex Apps – klassiska](https://www.plexonline.com/signon), [Plex Apps – UX-test](https://test.cloud.plex.com/sso), [Plex-appar – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/), [hantverk-Childcare poster, närvaro, & Financial tracking system](https://getcrafts.ca/craftsregistration) 

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Oktober 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD-loggar fungerar nu med Azure Log Analytics (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Vi är glada över att kunna meddela att du nu kan vidarebefordra dina Azure AD-loggar till Azure Log Analytics! Den här begärda funktionen ger dig ännu bättre åtkomst till analyser för din verksamhet, verksamhet och säkerhet, samt ett sätt att övervaka infrastrukturen. Mer information finns i [Azure Active Directory aktivitets loggar i Azure Log Analytics nu tillgänglig](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blogg.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – oktober 2018

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I oktober 2018 har vi lagt till dessa 14 nya appar med stöd för federation i app-galleriet:

[Mina belönings punkter](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint [, MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [låna](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), dialpad, ON24 [virtuell miljö](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler tre](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [bedömas](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Kontroll](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services e-postaviseringar

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services

Azure AD Domain Services innehåller varningar på Azure Portal om fel konfiguration eller problem med din hanterade domän. De här aviseringarna innehåller steg-för-steg-guider så att du kan försöka åtgärda problemen utan att behöva kontakta supporten.

Från och med oktober kommer du att kunna anpassa aviserings inställningarna för din hanterade domän, så när nya aviseringar inträffar skickas ett e-postmeddelande till en viss grupp med personer, vilket eliminerar behovet av att kontinuerligt kontrol lera portalen efter uppdateringar.

Mer information finns i [meddelande inställningar i Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD Portal stöder användning av ForceDelete-domän-API: et för att ta bort anpassade domäner 

**Typ:** Ändrad funktion  
**Tjänste kategori:** Katalog hantering  
**Produkt kapacitet:** Katalogen

Vi är glada över att kunna meddela att du nu kan använda ForceDelete-domän-API: et för att ta bort dina anpassade domän namn genom att asynkront byta namn på referenser, t. ex. användare, grupper och appar från ditt eget domän namn (contoso.com) till det ursprungliga standard domän namnet ( contoso.onmicrosoft.com).

Den här ändringen hjälper dig att snabbt ta bort dina anpassade domän namn om organisationen inte längre använder namnet eller om du behöver använda domän namnet med en annan Azure AD.

Mer information finns i [ta bort ett anpassat domän namn](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>September 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Uppdaterade administratörs roll behörigheter för dynamiska grupper

**Typ:** Fastsatt  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete

Vi har åtgärdat ett problem så att vissa administratörs roller nu kan skapa och uppdatera regler för dynamiskt medlemskap, utan att behöva vara ägare till gruppen.

Rollerna är:

- Global administratör

- Intune-administratör

- Användar administratör

Mer information finns i [skapa en dynamisk grupp och kontrol lera status](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Förenklade konfigurations inställningar för enkel inloggning (SSO) för vissa appar från tredje part

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Vi inser att det är svårt att konfigurera enkel inloggning (SSO) för SaaS-appar (program vara som en tjänst) på grund av unika egenskaper för varje apps-konfiguration. Vi har skapat en förenklad konfigurations upplevelse för att automatiskt fylla i SSO-konfigurations inställningarna för följande SaaS-appar från tredje part:

- Zendesk

- ArcGis online

- Jamf Pro

Börja använda den här funktionen genom att gå till **konfigurations** sidan för **Azure Portal** > SSO för appen. Mer information finns i [SaaS Application Integration with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – var finns dina data? tvåsidig

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** GoLocal

Välj ditt företags region från **Azure Active Directory – var finns din data** sida för att se vilka Azure-datacenter som rör dina Azure AD-data i vila för alla Azure AD-tjänster. Du kan filtrera informationen efter särskilda Azure AD-tjänster för ditt företags region.

För att få åtkomst till den här funktionen och mer information, se [Azure Active Directory – var finns dina data](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Ny distributions plan tillgänglig för åtkomst panelen för Mina appar

**Typ:** Ny funktion  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** DEFINITION

Kolla in den nya distributions plan som är tillgänglig för åtkomst panelen för Mina appar (https://aka.ms/deploymentplans).
Åtkomst panelen för Mina appar ger användare en enda plats för att hitta och komma åt sina appar. Den här portalen ger också användare självbetjänings möjligheter, till exempel att begära åtkomst till appar och grupper eller att hantera åtkomst till dessa resurser för andras räkning.

Mer information finns i [Vad är min Apps-portalen?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Fliken ny fel sökning och support på sidan inloggnings loggar i Azure Portal

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Fliken ny **fel sökning och support** på sidan **inloggningar** i Azure Portal är avsedd att hjälpa administratörer och support tekniker att felsöka problem som rör Azure AD-inloggningar. Den nya fliken innehåller felkod, fel meddelande och reparations rekommendationer (om sådana finns) för att hjälpa till att lösa problemet. Om du inte kan lösa problemet ger vi också ett nytt sätt att skapa ett support ärende med hjälp av funktionen **Kopiera till Urklipp** , som fyller i fälten för **begäran-ID** och **datum (UTC)** för logg filen i ditt support ärende.  

![Inloggnings loggar som visar den nya fliken](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Förbättrat stöd för anpassade tilläggs egenskaper som används för att skapa regler för dynamiskt medlemskap

**Typ:** Ändrad funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete

Med den här uppdateringen kan du nu Klicka på länken **Hämta anpassade tilläggs egenskaper** från regel verktyget för dynamisk användar grupp, ange ditt unika app-ID och ta emot den fullständiga listan med anpassade tilläggs egenskaper som ska användas när du skapar en regel för dynamiskt medlemskap för användare. Den här listan kan också uppdateras för att få nya anpassade tilläggs egenskaper för appen.

Mer information om hur du använder anpassade tilläggs egenskaper för dynamiska medlemskaps regler finns i [tilläggs egenskaper och anpassade tilläggs egenskaper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klient program för Azure AD App-baserad villkorlig åtkomst

**Typ:** Planera för ändring  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** Identitets säkerhet och skydd

Följande appar finns på listan över [godkända klient program](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Mer information finns här:

- [Azure AD App-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nytt stöd för lösen ords återställning via självbetjäning från Lås skärmen för Windows 7/8/8.1

**Typ:** Ny funktion  
**Tjänste kategori:** SSPR  
**Produkt kapacitet:** Användarautentisering

När du har konfigurerat den här nya funktionen visas en länk för att återställa lösen ordet från **Lås** skärmen på en enhet som kör Windows 7, Windows 8 eller Windows 8,1. Genom att klicka på länken vägleds användaren genom samma flöde för lösen ords återställning som i webbläsaren.

Mer information finns i [så här aktiverar du återställning av lösen ord från Windows 7, 8 och 8,1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Ändrings meddelande: auktoriseringsregler kommer inte längre att vara tillgängliga för åter användning 

**Typ:** Planera för ändring  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Från den 15 november 2018 slutar Azure AD att acceptera tidigare använda autentiseringsnivåer för appar. Den här säkerhets ändringen hjälper till att placera Azure AD i linje med OAuth-specifikationen och tillämpas på både v1-och v2-slutpunkter.

Om appen återanvänder auktoriseringsregler för att hämta tokens för flera resurser, rekommenderar vi att du använder koden för att hämta en uppdateringstoken, och sedan använder den uppdateringstoken för att hämta ytterligare token för andra resurser. Det går bara att använda auktoriseringsregler en gång, men uppdaterade token kan användas flera gånger i flera resurser. En app som försöker återanvända en autentiseringsmetod under OAuth-kodfragmentet får ett invalid_grant-fel.

För detta och andra protokoll-relaterade ändringar, se [den fullständiga listan över vad som är nytt för autentisering](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – september 2018

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I september 2018 har vi lagt till dessa 16 nya appar med stöd för federation i app-galleriet:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Möt rekryterings program vara](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [snö](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), Join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback,](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial) Riverbed Xirrus EasyPass, [RACKSPACE SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO för Azure, SurveyMonkey, [sammankalla](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Stöd för ytterligare metoder för att transformera anspråk

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Vi har introducerat nya omvandlings metoder för anspråk, ToLower () och ToUpper () som kan tillämpas på SAML-tokens från konfigurations sidan för SAML-baserad **enkel inloggning** .

Mer information finns i [så här anpassar du anspråk som utfärdats i SAML-token för företags program i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Uppdaterat SAML-baserat konfigurations gränssnitt för app (för hands version)

**Typ:** Ändrad funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Som en del av det uppdaterade SAML-baserade konfigurations gränssnittet för SAML-baserade appar får du:

- En uppdaterad genom gångs upplevelse för att konfigurera dina SAML-baserade appar.

- Mer information om vad som saknas eller är felaktigt i konfigurationen.

- Möjlighet att lägga till flera e-postadresser för meddelande om förfallo datum för certifikat.

- Nya omvandlings metoder för anspråk, ToLower () och ToUpper () med mera.

- Ett sätt att ladda upp ditt eget token signerings certifikat för dina företags program.

- Ett sätt att ange NameID-formatet för SAML-appar och ett sätt att ange NameID-värdet som katalog tillägg.

Om du vill aktivera den här uppdaterade vyn klickar du på länken **prova vår nya upplevelse** överst på sidan för **enkel inloggning** . Mer information finns i [Självstudier: Konfigurera SAML-baserad enkel inloggning för ett program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).

---

## <a name="august-2018"></a>Augusti 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Ändringar i Azure Active Directory IP-adressintervall

**Typ:** Planera för ändring  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Systemet

Vi presenterar större IP-intervall för Azure AD, vilket innebär att om du har konfigurerat Azure AD IP-adressintervall för brand väggar, routrar eller nätverks säkerhets grupper måste du uppdatera dem. Vi gör den här uppdateringen så att du inte behöver ändra inställningarna för IP-intervall för brand väggen, routern eller nätverks säkerhets grupper igen när Azure AD lägger till nya slut punkter. 

Nätverks trafiken flyttas till de nya intervallen under de kommande två månaderna. Om du vill fortsätta med en oavbruten tjänst måste du lägga till dessa uppdaterade värden till dina IP-adresser före den 10 september 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

Vi rekommenderar starkt att inte ta bort de gamla IP-adressintervall förrän all nätverks trafik har flyttats till de nya intervallen. För uppdateringar om flytten och för att lära dig när du kan ta bort de gamla intervallen, se [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Ändrings meddelande: auktoriseringsregler kommer inte längre att vara tillgängliga för åter användning 

**Typ:** Planera för ändring  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Från den 15 november 2018 slutar Azure AD att acceptera tidigare använda autentiseringsnivåer för appar. Den här säkerhets ändringen hjälper till att placera Azure AD i linje med OAuth-specifikationen och tillämpas på både v1-och v2-slutpunkter.

Om appen återanvänder auktoriseringsregler för att hämta tokens för flera resurser, rekommenderar vi att du använder koden för att hämta en uppdateringstoken, och sedan använder den uppdateringstoken för att hämta ytterligare token för andra resurser. Det går bara att använda auktoriseringsregler en gång, men uppdaterade token kan användas flera gånger i flera resurser. En app som försöker återanvända en autentiseringsmetod under OAuth-kodfragmentet får ett invalid_grant-fel.

För detta och andra protokoll-relaterade ändringar, se [den fullständiga listan över vad som är nytt för autentisering](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Konvergerad säkerhets informations hantering för självbetjänings lösen ord (SSPR) och Multi-Factor Authentication (MFA)

**Typ:** Ny funktion  
**Tjänste kategori:** SSPR  
**Produkt kapacitet:** Användarautentisering
 
Den här nya funktionen hjälper användare att hantera sina säkerhets uppgifter (t. ex. telefonnummer, mobilappar osv.) för SSPR och MFA på en enda plats och erfarenhet. jämfört med tidigare, var det gjordes på två olika platser.

Denna konvergerade upplevelse fungerar också för personer som använder antingen SSPR eller MFA. Om din organisation inte tvingar MFA-eller SSPR-registrering kan användarna fortfarande registrera alla MFA-eller SSPR säkerhets uppgifter som tillåts av din organisation från portalen Mina appar.

Det här är en valbar, offentlig för hands version. Administratörer kan aktivera den nya upplevelsen (om det behövs) för en vald grupp eller för alla användare i en klient organisation. Mer information om konvergerad upplevelse finns i [bloggen för konvergerad upplevelse](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Ny HTTP – endast cookies-inställning i Azure AD Application Proxy-appar

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control

Det finns en ny inställning som kallas **http-Only cookies** i dina Application Proxy-appar. Den här inställningen ger extra säkerhet genom att inkludera flaggan HTTPOnly i HTTP-svars huvudet för både programproxy-åtkomst och sessionscookies, stoppa åtkomst till cookien från ett skript på klient sidan och ytterligare förhindra åtgärder som att kopiera eller ändra cookien. Även om den här flaggan inte har använts tidigare har dina cookies alltid krypterats och skickats med en SSL-anslutning för att skydda mot felaktiga ändringar.

Den här inställningen är inte kompatibel med appar som använder ActiveX-kontroller, t. ex. fjärr skrivbord. Om du är i den här situationen rekommenderar vi att du inaktiverar den här inställningen.

Mer information om inställningen HTTP-Only cookies finns i [Publicera program med hjälp av Azure AD-programproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) för Azure-resurser har stöd för resurs typer för hanterings grupper

**Typ:** Ny funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
Inställningarna för just-in-Time-aktivering och tilldelning kan nu användas för resurs typer för hanterings grupper, precis som du redan har för prenumerationer, resurs grupper och resurser (till exempel virtuella datorer, App Services med mera). Dessutom kan alla med en roll som ger administratörs åtkomst till en hanterings grupp identifiera och hantera resursen i PIM.

Mer information om PIM och Azure-resurser finns i [identifiera och hantera Azure-resurser med hjälp av Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Program åtkomst (för hands version) ger snabbare åtkomst till Azure AD-portalen

**Typ:** Ny funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
I dag, när du aktiverar en roll med hjälp av PIM, kan det ta över 10 minuter innan behörigheterna börjar gälla. Om du väljer att använda program åtkomst, som för närvarande finns i en offentlig för hands version, kan administratörer få åtkomst till Azure AD-portalen så snart aktiverings förfrågan har slutförts.

För närvarande stöder program åtkomst endast Azure AD Portal-upplevelsen och Azure-resurser. Mer information om PIM och program åtkomst finns i [Vad är Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – augusti 2018

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I augusti 2018 har vi lagt till dessa 16 nya appar med stöd för federation i app-galleriet:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [bridgeline obundet](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [recept Labs – mobil-och Webbtestering](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [sätt vi gör](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [Promaster (av Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentation](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F-utgifts rapporter](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Inbyggt stöd för Tableau är nu tillgängligt i Azure AD-programproxy

**Typ:** Ändrad funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control

Med vår uppdatering från OpenID ansluter du till protokollet OAuth 2,0 Code Grant för vårt pre-Authentication-protokoll, behöver du inte längre göra någon ytterligare konfiguration för att använda Tableau med Application Proxy. Protokoll ändringen hjälper också till att programproxy bättre stöder fler moderna appar genom att bara använda HTTP-omdirigeringar som ofta stöds i Java Script-och HTML-taggar.

Mer information om vårt inbyggda stöd för Tableau finns i [Azure AD-programproxy nu med inbyggt stöd för Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nytt stöd för att lägga till Google som en identitets leverantör för B2B-gäst användare i Azure Active Directory (för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C

Genom att konfigurera Federation med Google i din organisation kan du låta inbjudna användare logga in till dina delade appar och resurser med hjälp av sitt befintliga Google-konto, utan att behöva skapa ett personligt Microsoft-konto (MSA: er) eller ett Azure AD-konto.

Det här är en valbar, offentlig för hands version. Mer information om Google Federation finns i [lägga till Google som en identitets leverantör för B2B-gäst användare](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Juli 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Förbättringar av Azure Active Directory e-postaviseringar

**Typ:** Ändrad funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Azure Active Directory (Azure AD)-e-postmeddelanden har nu en uppdaterad design, samt ändringar av avsändar-e-postadressen och avsändar visnings namnet när de skickas från följande tjänster:
 
- Åtkomst granskningar för Azure AD
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Företags program som förfaller certifikat meddelanden
- Meddelanden om företags-app-etablering
 
E-postaviseringarna skickas från följande e-postadress och visnings namn:

- E-post adress: azure-noreply@microsoft.com
- Visnings namn: Microsoft Azure
 
Ett exempel på en del nya e-postdesigner och mer information finns i [e-postmeddelanden i Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD-aktivitets loggar är nu tillgängliga via Azure Monitor

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Azure AD-aktivitets loggarna är nu tillgängliga i den offentliga för hands versionen för Azure Monitor (Azures plattforms oberoende övervaknings tjänst). Azure Monitor erbjuder långsiktig kvarhållning och sömlös integrering, utöver dessa förbättringar:

- Långsiktig kvarhållning genom att vidarebefordra dina loggfiler till ditt eget Azure Storage-konto.

- Sömlös SIEM-integrering, utan att du behöver skriva eller underhålla anpassade skript.

- Sömlös integrering med dina egna anpassade lösningar, analys verktyg eller incident hanterings lösningar.

Mer information om de här nya funktionerna finns i våra blogg [Azure AD-aktivitets loggar i Azure Monitor Diagnostics finns nu i den offentliga för hands versionen och i](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) vår dokumentation, [Azure Active Directory aktivitets loggar i Azure Monitor (för hands version)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Information om villkorlig åtkomst som lagts till i rapporten Azure AD-inloggningar

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** & Skydd för identitets säkerhet
 
Med den här uppdateringen kan du se vilka principer som utvärderas när en användare loggar in, tillsammans med princip resultatet. Dessutom innehåller rapporten nu den typ av klient program som används av användaren, så att du kan identifiera äldre protokoll trafik. Nu kan du söka efter ett korrelations-ID som finns i det användar meddelande som visas och som kan användas för att identifiera och felsöka den matchande inloggnings förfrågan.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Visa äldre autentiseringar via inloggnings aktivitets loggar

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering
 
Med introduktionen av fältet **klient app** i inloggnings aktivitets loggarna kan kunder nu se användare som använder äldre autentiseringar. Kunderna kommer att ha åtkomst till den här informationen med hjälp av inloggnings-MS Graph API eller genom inloggnings aktivitets loggarna i Azure AD portal där du kan använda **klientens app** -kontroll för att filtrera på äldre autentiseringar. Mer information finns i dokumentationen.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – juli 2018

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I juli 2018 har vi lagt till dessa 16 nya appar med stöd för federation i app-galleriet:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [viss admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC-mellanlagring, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [skärm utsändning-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), Powerschool Unified klass rum, [Eli onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar remote support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [NimbleX ](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Föreställ dig webvision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [kunskaps bas](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Ny användar etablering SaaS app integrations – juli 2018

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Med Azure AD kan du automatisera genereringen, underhållet och borttagningen av användar identiteter i SaaS program som Dropbox, Salesforce, ServiceNow och mycket annat. I juli 2018 har vi lagt till stöd för användar etablering för följande program i Azure AD App-galleriet:

- [Cisco-WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonus](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

En lista över alla program som stöder användar etablering i Azure AD-galleriet finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health för Sync – ett enklare sätt att åtgärda överblivna och duplicerade attributvärden för synkroniseringsfel

**Typ:** Ny funktion  
**Tjänste kategori:** AD Connect  
**Produkt kapacitet:** Övervaka & rapportering
 
Azure AD Connect Health introducerar självbetjänings reparation för att hjälpa dig att markera och åtgärda synkroniseringsfel. Den här funktionen felsöker dubbletter av synkroniseringsfel och korrigerar objekt som är överblivna från Azure AD. Den här diagnostiken har följande fördelar:

- Minskar synkroniseringsfel för dubblerade attribut, och tillhandahåller specifika korrigeringar

- Använder en korrigering för dedikerade Azure AD-scenarier och löser fel i ett enda steg

- Ingen uppgradering eller konfiguration krävs för att aktivera och använda den här funktionen

Mer information finns i [diagnostisera och åtgärda dubblerade synkroniseringsfel](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visuella uppdateringar av inloggnings upplevelser för Azure AD och MSA

**Typ:** Ändrad funktion  
**Tjänste kategori:** Azure AD  
**Produkt kapacitet:** Användarautentisering

Vi har uppdaterat användar gränssnittet för Microsofts onlinetjänster inloggnings upplevelse, till exempel för Office 365 och Azure. Den här ändringen gör att skärmarna blir mindre röriga och mer enkla. Mer information om den här ändringen finns i avsnittet [kommande förbättringar av inloggnings upplevelsen för Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Ny version av Azure AD Connect – juli 2018

**Typ:** Ändrad funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel

Den senaste versionen av Azure AD Connect innehåller: 

- Fel korrigeringar och support uppdateringar 

- Allmän tillgänglighet för ping-Federer-integrering

- Uppdateringar till den senaste SQL 2012-klienten 

Mer information om den här uppdateringen finns [Azure AD Connect: versions historik](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Uppdateringar av användar gränssnittet för slutanvändaren

**Typ:** Ändrad funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Ledning

Vi uppdaterar godkännande strängen i användar gränssnittet för TOU slutanvändare.

**Aktuell text.** För att få åtkomst till [tenantName]-resurser måste du godkänna användnings villkoren.<br>**Ny text.** För att få åtkomst till [tenantName]-resursen måste du läsa användnings villkoren.

**Aktuell text:** Om du väljer att acceptera innebär det att du godkänner alla ovanstående användnings villkor.<br>**Ny text:** Klicka på acceptera för att bekräfta att du har läst och förstått användnings villkoren.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Direktautentisering stöder äldre protokoll och program

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Direkt autentisering stöder nu äldre protokoll och appar. Följande begränsningar stöds nu fullt ut:

- Användar inloggningar till äldre Office-klientprogram, Office 2010 och Office 2013, utan att kräva modern autentisering.

- Åtkomst till kalender delning och information om ledig/upptagen i Exchange hybrid-miljöer i Office 2010.

- Användar inloggningar till Skype för företag-klient program utan att kräva modern autentisering.

- Användar inloggningar till PowerShell version 1,0.

- Apple-Programmet för enhetsregistrering (Apple DEP) med installations assistenten för iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Konvergerad hantering av säkerhets information för självbetjäning för återställning av lösen ord och Multi-Factor Authentication

**Typ:** Ny funktion  
**Tjänste kategori:** SSPR  
**Produkt kapacitet:** Användarautentisering

Med den här nya funktionen kan användarna hantera sina säkerhets uppgifter (t. ex. telefonnummer, e-postadress, mobilapp osv.) för självbetjäning för återställning av lösen ord (SSPR) och Multi-Factor Authentication (MFA) i en och samma upplevelse. Användarna kommer inte längre behöva registrera samma säkerhets information för SSPR och MFA i två olika upplevelser. Den här nya upplevelsen gäller även för användare som har antingen SSPR eller MFA.

Om en organisation inte tillämpar MFA-eller SSPR-registrering kan användarna registrera sina säkerhets uppgifter via portalen **Mina appar** . Därifrån kan användare registrera alla metoder som är aktiverade för MFA eller SSPR. 

Det här är en valbar, offentlig för hands version. Administratörer kan aktivera den nya upplevelsen (om det behövs) för en viss grupp användare eller alla användare i en klient organisation.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Använd appen Microsoft Authenticator för att verifiera din identitet när du återställer ditt lösen ord

**Typ:** Ändrad funktion  
**Tjänste kategori:** SSPR  
**Produkt kapacitet:** Användarautentisering

Med den här funktionen kan icke-administratörer verifiera sin identitet när de återställer ett lösen ord med hjälp av ett meddelande eller kod från Microsoft Authenticator (eller någon annan Authenticator-app). När administratörer aktiverar den här metoden för lösen ords återställning via självbetjäning kan användare som har registrerat en mobilapp via aka.ms/mfasetup eller aka.ms/setupsecurityinfo använda sina mobilappar som en verifierings metod när de återställer sitt lösen ord.

Mobile App-aviseringar kan bara aktive ras som en del av en princip som kräver två metoder för att återställa ditt lösen ord.

---

## <a name="june-2018"></a>Juni 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Ändrings meddelande: säkerhets korrigering i det delegerade auktoriseringsvärdet för appar som använder Azure AD aktivitets loggar API

**Typ:** Planera för ändring  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

På grund av vår starkare säkerhet har vi haft gjort en ändring i behörigheterna för appar som använder ett delegerat auktoriseringsarkiv för att få åtkomst till [API: er för Azure AD-aktivitets loggar](https://aka.ms/aadreportsapi). Denna ändring sker den **26 juni 2018**.

Om någon av dina appar använder Azure AD aktivitets logg-API: er, följer du dessa steg för att se till att appen inte bryts när ändringen har utförts.

**Uppdatera dina program behörigheter**

1. Logga in på Azure Portal, Välj **Azure Active Directory**och välj sedan app- **registreringar**.
2. Välj din app som använder Azure AD aktivitets loggar API, Välj **Inställningar**, Välj **nödvändiga behörigheter**och välj sedan **Windows-Azure Active Directory** -API: et.
3. I avsnittet **delegerade behörigheter** i bladet **Aktivera åtkomst** markerar du kryss rutan bredvid **Läs katalog** data och väljer sedan **Spara**.
4. Välj **bevilja behörigheter**och välj sedan **Ja**.
    
    >[!Note]
    >Du måste vara global administratör för att bevilja behörighet till appen.

Mer information finns i avsnittet [Granting Permissions](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) i kraven för att komma åt Azure AD repor ting API-artikeln.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurera TLS-inställningar för att ansluta till Azure AD-tjänster för PCI DSS kompatibilitet

**Typ:** Ny funktion  
**Tjänste kategori:** EJ TILLÄMPLIGT  
**Produkt kapacitet:** Systemet

Transport Layer Security (TLS) är ett protokoll som tillhandahåller sekretess-och data integritet mellan två kommunicerande program och är det mest distribuerade säkerhets protokollet som används idag.

[PCI Security Standards-rådet](https://www.pcisecuritystandards.org/) har fastställt att tidiga versioner av TLS och Secure SOCKETS Layer (SSL) måste inaktive ras för att aktivera nya och säkrare app-protokoll, med krav från och med den **30 juni 2018**. Den här ändringen innebär att om du ansluter till Azure AD-tjänster och kräver PCI DSS-efterlevnad måste du inaktivera TLS 1,0. Det finns flera versioner av TLS, men TLS 1,2 är den senaste versionen som är tillgänglig för Azure Active Directory Services. Vi rekommenderar starkt att du flyttar direkt till TLS 1,2 för kombinationerna både klient/server och webbläsare/Server.

Inaktuella webbläsare kanske inte stöder nyare TLS-versioner, till exempel TLS 1,2. Om du vill se vilka versioner av TLS som stöds av din webbläsare går du till webbplatsen för [SSL-labbet Qualys](https://www.ssllabs.com/) och klickar på **testa din webbläsare**. Vi rekommenderar att du uppgraderar till den senaste versionen av webbläsaren och helst bara aktiverar TLS 1,2.

**Så här aktiverar du TLS 1,2, av webbläsare**

- **Microsoft Edge och Internet Explorer (båda anges med Internet Explorer)**

    1. Öppna Internet Explorer, Välj **verktyg** > **Internet alternativ** > **Avancerat**.
    2. I avsnittet **säkerhet** väljer du **Använd TLS 1,2**och väljer sedan **OK**.
    3. Stäng alla webbläsarfönster och starta om Internet Explorer. 

- **Google Chrome**

    1. Öppna Google Chrome, Skriv *Chrome://Settings/* i adress fältet och tryck på **RETUR**.
    2. Expandera **avancerade** alternativ, gå till **system** -ytan och välj **Öppna proxyinställningar**.
    3. I rutan **Internet egenskaper** väljer du fliken **Avancerat** , går till **säkerhets** avsnittet, väljer **Använd TLS 1,2**och väljer sedan **OK**.
    4. Stäng alla webbläsarfönster och starta om Google Chrome.

- **Mozilla Firefox**

    1. Öppna Firefox, Skriv *About: config* i adress fältet och tryck på **RETUR**.
    2. Sök efter termen, *TLS*och välj sedan posten **Security. TLS. version. Max** .
    3. Ställ in värdet på **3** för att tvinga webbläsaren att använda upp till version TLS 1,2 och välj sedan **OK**.

        >[!NOTE]
        >Firefox version 60,0 stöder TLS 1,3, så du kan också ställa in Security. TLS. version. Max-värdet på **4**.

    4. Stäng alla webb läsar fönster och starta om Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – juni 2018

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I juni 2018 har vi lagt till dessa 15 nya appar med stöd för federation i app-galleriet:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [avräkning av musik](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1,1 token Enabled LOB-app](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [överstämning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [AutoTask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint lokalt](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [förutse CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD Password Protection finns i offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** Användarautentisering

Använd Azure AD Password Protection för att undvika att gissa lösen ord från din miljö lätt gissas. Genom att ta bort dessa lösen ord kan du minska risken för angrepp från angrepp av lösen ord.

Mer specifikt hjälper Azure AD Password Protection dig att:

- Skydda organisationens konton både i Azure AD och Windows Server Active Directory (AD). 
- Hindrar användarna från att använda lösen ord på en lista med fler än 500 av de lösen ord som används oftast och över 1 000 000 tecken ersättning för dessa lösen ord.
- Administrera Azure AD Password Protection från en enda plats i Azure AD-portalen för både Azure AD och den lokala Windows Server AD.

Mer information om lösen ords skydd i Azure AD finns i [eliminera Felaktiga lösen ord i din organisation](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nya princip mal len "alla gäster" som skapas när användnings villkoren skapas

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Ledning

Under skapandet av dina användnings villkor skapas även en ny mall för villkorlig åtkomst för "alla gäster" och "alla appar". Den här nya princip mal len tillämpar den nyligen skapade ToU, vilket effektiviserar skapande och tvångs processen för gäster.

Mer information finns i [Azure Active Directory användningsvillkor-funktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Ny princip mal len anpassad princip för villkorlig åtkomst som skapats när användnings villkoren skapas

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Ledning

Under skapandet av dina användnings villkor skapas även en ny princip mal len anpassad princip för villkorlig åtkomst. Med den här nya princip mal len kan du skapa ToU och sedan omedelbart gå till bladet skapa princip för villkorlig åtkomst, utan att behöva navigera manuellt i portalen.

Mer information finns i [Azure Active Directory användningsvillkor-funktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Ny och omfattande vägledning om hur du distribuerar Azure Multi-Factor Authentication

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** & Skydd för identitets säkerhet
 
Vi har publicerat nya steg-för-steg-instruktioner om hur du distribuerar Azure Multi-Factor Authentication (MFA) i din organisation.

Om du vill visa distributions guiden för MFA går du till lagrings platsen för [identitets distribution](https://aka.ms/DeploymentPlans) på GitHub. Om du vill ge feedback om distributions guiderna använder du [feedback-formuläret för distributions planen](https://aka.ms/deploymentplanfeedback). Om du har frågor om distributions guiderna kan du kontakta oss på [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD-delegerade app Management-roller finns i offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** Access Control

Administratörer kan nu delegera program hanterings uppgifter utan att tilldela rollen global administratör. De nya rollerna och funktionerna är:

- **Nya standard administratörs roller för Azure AD:**

    - **Program administratör.** Ger möjlighet att hantera alla aspekter av alla appar, inklusive registrering, SSO-inställningar, app-tilldelningar och licensiering, inställningar för App-proxy och godkännande (förutom för Azure AD-resurser).

    - **Moln program administratör.** Ger alla funktioner för program administratör, förutom App proxy, eftersom den inte tillhandahåller lokal åtkomst.

    - **Programutvecklare.** Ger möjlighet att skapa registrerings program, även om alternativet **Tillåt användare att registrera appar** är inaktiverat.

- **Ägarskap (Konfigurera per app-registrering och per företags-app, som liknar gruppens ägarskaps process:**
 
    - **Ägare av app-registrering.** Ger möjlighet att hantera alla aspekter av ägd app-registrering, inklusive app-manifestet och lägga till ytterligare ägare.

    - **Ägare av företags program.** Ger möjlighet att hantera många aspekter av ägda företags program, inklusive SSO-inställningar, app-tilldelningar och medgivande (förutom för Azure AD-resurser).

Mer information om den offentliga för hands versionen finns [i Azure AD-delegerade program hanterings roller finns i offentlig för hands version!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blogg. Mer information om roller och behörigheter finns [i tilldela administratörs roller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Maj 2018

### <a name="expressroute-support-changes"></a>ExpressRoute-support ändringar

**Typ:** Planera för ändring  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Systemet  

Program vara som ett tjänst erbjudande, t. ex. Azure Active Directory (Azure AD) är utformad för att fungera bäst genom att gå direkt via Internet, utan att kräva ExpressRoute eller någon annan privat VPN-tunnel. På grund av detta upphör vi att stödja ExpressRoute för Azure AD-tjänster via Azures offentliga peering och Azure-communities i Microsoft-peering, den **1 augusti 2018**. Alla tjänster som påverkas av den här ändringen kan observera att Azure AD-trafik gradvis byter från ExpressRoute till Internet.

Medan vi ändrar vår support vet vi också att det fortfarande finns situationer där du kan behöva använda en dedikerad uppsättning kretsar för din autentisering. Därför fortsätter Azure AD att ge stöd för IP-adressintervall per innehavare med hjälp av ExpressRoute och tjänster som redan ingår i Microsoft-peering med "andra Office 365 Online Services"-communityn. Om dina tjänster påverkas, men du behöver ExpressRoute, måste du göra följande:

- **Om du använder Azures offentliga peering.** Flytta till Microsoft-peering och registrera dig för de **andra Office 365 Online Services-communityn (12076:5100)** . Mer information om hur du flyttar från offentlig Azure-peering till Microsoft-peering finns i artikeln [flytta en offentlig peering till Microsoft-peering](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) .

- **Om du använder Microsoft-peering.** Registrera dig för den **andra Office 365 online service-communityn (12076:5100)** . Mer information om krav för routning finns i [avsnittet stöd för BGP-communities](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) i artikeln ExpressRoute routnings krav.

Om du måste fortsätta att använda dedikerade kretsar måste du prata med ditt Microsoft-konto team om hur du får behörighet att använda den **andra Office 365 online service-communityn (12076:5100)** . Den MS Office-hanterade gransknings tavlan kontrollerar om du behöver dessa kretsar och se till att du förstår de tekniska konsekvenserna av att hålla dem. Obehöriga prenumerationer som försöker skapa väg filter för Office 365 kommer att få ett fel meddelande. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph-API: er för administrativa scenarier för TOU

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Utvecklings miljö
 
Vi har lagt till Microsoft Graph-API: er för administration av Azure ADs användnings villkor. Du kan skapa, uppdatera och ta bort användnings villkoren.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Lägg till Azure AD-slutpunkt för flera klient organisationer som en identitets leverantör i Azure AD B2C

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
Med anpassade principer kan du nu lägga till den vanliga Azure AD-slutpunkten som en identitets leverantör i Azure AD B2C. På så sätt kan du ha en enda post punkt för alla Azure AD-användare som loggar in i dina program. Mer information finns i [Azure Active Directory B2C: Tillåt att användare loggar in på en Azure AD-identitetsprovider med flera innehavare med hjälp av anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Använd interna URL: er för att komma åt appar från var som helst med inloggnings tillägget för Mina appar och Azure-AD-programproxy

**Typ:** Ny funktion  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** DEFINITION
 
Användare kan nu komma åt program via interna URL: er även när de befinner sig utanför företagets nätverk med hjälp av tillägget Mina appar säker inloggning för Azure AD. Detta fungerar med alla program som du har publicerat med Azure AD-programproxy, i alla webbläsare som också har åtkomst panels tillägget installerat. Funktionen för URL-omdirigering aktive ras automatiskt när en användare loggar in i tillägget. Tillägget är tillgängligt för hämtning på [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)och [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory-data i Europa för Europa-kunder

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** GoLocal

Kunder i Europa kräver sina data för att hållas kvar i Europa och inte replikeras utanför Europeiska Data Center för att möta sekretess och europeiska lagar. Den här [artikeln](https://go.microsoft.com/fwlink/?linkid=872328) innehåller detaljerad information om vilken identitets information som lagras i Europa och som även innehåller information om information som kommer att lagras utanför de europeiska data centren. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Ny användar etablering SaaS app integrations – maj 2018

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Med Azure AD kan du automatisera genereringen, underhållet och borttagningen av användar identiteter i SaaS program som Dropbox, Salesforce, ServiceNow och mycket annat. För maj 2018 har vi lagt till stöd för användar etablering för följande program i Azure AD App-galleriet:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Hörn OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

En lista över alla program som stöder användar etablering i Azure AD-galleriet finns [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Azure AD åtkomst granskningar av grupper och app-åtkomst innehåller nu återkommande granskningar

**Typ:** Ny funktion  
**Tjänste kategori:** Åtkomst granskningar  
**Produkt kapacitet:** Ledning
 
Åtkomst granskning av grupper och appar är nu allmänt tillgänglig som en del av Azure AD Premium P2.  Administratörer kommer att kunna konfigurera åtkomst granskningar av grupp medlemskap och program tilldelningar för att automatiskt upprepas med jämna mellanrum, till exempel varje månad eller kvartal.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD-aktivitets loggar (inloggningar och granskning) är nu tillgängliga via MS Graph

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering
 
Azure AD-aktivitets loggar, som innehåller inloggningar och gransknings loggar, är nu tillgängliga via MS Graph. Vi har exponerat två slut punkter genom MS Graph för att komma åt dessa loggar. Ta en titt på våra [dokument](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) för program mässig åtkomst till Azure AD repor ting-API: er för att komma igång. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Förbättringar av B2B-inlösnings upplevelsen och lämna en organisation

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C

**Just-in-Time-inlösen:** När du delar en resurs med en gäst användare som använder B2B-API – behöver du inte skicka ut en särskild inbjudan via e-post. I de flesta fall kan gäst användaren komma åt resursen och tas genom inlösnings upplevelsen just i tiden. Ingen mer påverkan på grund av missade e-postmeddelanden. Du behöver inte längre be gäst användarna om att du klickade på den här inlösnings länken systemet skickade? ". Det innebär att när SPO använder Inbjudnings hanteraren – moln bilagor kan ha samma kanoniska URL för alla användare – internt och externt – i alla tillstånd för inlösen.

**Modern inlösnings upplevelse:** Ingen fler delnings sida för upplösning av delad skärm. Användarna kommer att se en modern medgivande upplevelse med den bjudande organisationens sekretess policy, precis som de gör för appar från tredje part.

**Gäst användare kan lämna organisationen:** När en användares relation med en organisation är över kan de själv betjäna lämna organisationen. Inga fler anrop till den svarande organisationens administratör till "tas bort", inga fler upphöjnings support biljetter.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – maj 2018

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I maj 2018 har vi lagt till dessa 18 nya appar med stöd för federation i vårt app-Galleri:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty styr, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [JAMF Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [ENVI MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate primtal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montagelayout online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), openrulle, [båge Publicering – SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [riskmaterial](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [ReviewSnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nya steg-för-steg-distributions guider för Azure Active Directory

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Katalogen
 
Nya steg-för-steg-instruktioner om hur du distribuerar Azure Active Directory (Azure AD), inklusive självbetjäning för återställning av lösen ord (SSPR), enkel inloggning (SSO), villkorlig åtkomst (CA), App proxy, användar etablering, Active Directory Federation Services (AD FS) (ADFS) till Direktautentisering (PTA) och ADFS till Password hash Sync (PHS).

Om du vill visa distributions guiderna går du till lagrings platsen för [identitets distribution](https://aka.ms/DeploymentPlans) på GitHub. Om du vill ge feedback om distributions guiderna använder du [feedback-formuläret för distributions planen](https://aka.ms/deploymentplanfeedback). Om du har frågor om distributions guiderna kan du kontakta oss på [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Sökning efter företags program – Läs in fler appar

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Har du problem med att hitta dina program/tjänstens huvud namn? Vi har lagt till möjligheten att läsa in fler program i företags program listan Alla program. Som standard visar vi 20 program. Nu kan du klicka på, **läsa in mer** om du vill visa fler program. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Maj-versionen av AADConnect innehåller en offentlig för hands version av integreringen med PingFederate, viktiga säkerhets uppdateringar, många fel korrigeringar och nya fantastiska nya fel söknings verktyg. 

**Typ:** Ändrad funktion  
**Tjänste kategori:** AD Connect  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Maj-versionen av AADConnect innehåller en offentlig för hands version av integreringen med PingFederate, viktiga säkerhets uppdateringar, många fel korrigeringar och nya fantastiska nya fel söknings verktyg. Du hittar viktig information [här](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Åtkomst granskningar för Azure AD: Använd automatiskt

**Typ:** Ändrad funktion  
**Tjänste kategori:** Åtkomst granskningar  
**Produkt kapacitet:** Ledning

Åtkomst granskningar av grupper och appar är nu allmänt tillgängliga som en del av Azure AD Premium P2. En administratör kan konfigurera att automatiskt tillämpa granskarens ändringar av gruppen eller appen när åtkomst granskningen är klar. Administratören kan också ange vad som händer med användarens fortsatta åtkomst om granskarna inte svarade, ta bort åtkomst, behålla åtkomst eller vidta system rekommendationer. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>ID-token kan inte längre returneras med frågan response_mode för nya appar. 

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Appar som skapats den 25 april 2018 kommer inte längre att kunna begära en **id_token** med **frågan** response_mode.  Detta medför att Azure AD infogas med OIDC-specifikationerna och hjälper till att minska din appars attack yta.  Appar som skapats före den 25 april 2018 blockeras inte från att använda **query** -response_mode med en response_type på **id_token**.  Det fel som returnerades när en begäran om att begära en id_token från AAD, är **AADSTS70007: ' fråga ' är inte ett värde som stöds av ' response_mode ' när du begär en token**.

**Fragment** -och **form_post** -response_modes fortsätter att fungera – när du skapar nya program objekt (till exempel för användning av App-proxy) ska du se till att använda någon av dessa response_modes innan de skapar ett nytt program.  

---
 
## <a name="april-2018"></a>April 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C åtkomsttoken är GA

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C 

Nu kan du få åtkomst till webb-API: er som skyddas av Azure AD B2C att använda åtkomsttoken. Funktionen flyttas från offentlig för hands version till GA. GRÄNSSNITTs upplevelsen för att konfigurera Azure AD B2C program och webb-API: er har förbättrats och andra mindre förbättringar har gjorts.
 
Mer information finns i [Azure AD B2C: begär åtkomsttoken](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testa enkel inloggnings konfiguration för SAML-baserade program

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

När du konfigurerar SAML-baserade SSO-program kan du testa integrationen på konfigurations sidan. Om du stöter på ett fel under inloggningen kan du ange felet i test upplevelsen och Azure AD ger dig lösnings steg för att lösa problemet.

Mer information finns här:

- [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Användnings villkoren för Azure AD har nu rapporter ATS per användare

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Fastställ
 
Administratörer kan nu välja en specifik ToU och se alla användare som har samtyckt till den ToU och vilket datum/tid det tog.

Mer information finns i [funktionen användnings villkor för Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: riskfylld IP för AD FS extra näts utelåsning 

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Övervaka & rapportering

Connect Health stöder nu möjligheten att identifiera IP-adresser som överskrider tröskelvärdet på misslyckade U/P-inloggningar per timme eller per dag. Funktionerna i den här funktionen är:

- Omfattande rapport som visar IP-adress och antal misslyckade inloggningar som genererats per timme/dag med anpassningsbart tröskelvärde.
- E-postbaserade aviseringar som visar när en speciell IP-adress har överskridit tröskelvärdet för misslyckade U/P-inloggningar per timme/dag.
- Ett nedladdnings alternativ för att göra en detaljerad analys av data

Mer information finns i avsnittet om [riskfyllda IP-rapporter](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Enkel app-konfiguration med metadatafil eller URL

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

På sidan företags program kan administratörer Ladda upp en SAML-metadatafil för att konfigurera SAML-baserad inloggning för AAD-galleriet och program som inte är Galleri.

Dessutom kan du använda Azure AD Application Federation Metadata-URL för att konfigurera SSO med mål programmet.

Mer information finns i [Konfigurera enkel inloggning till program som inte finns i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD Användningsvillkor nu allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Fastställ
 

Användnings villkoren för Azure AD har flyttats från offentlig för hands version till allmänt tillgängliga.

Mer information finns i [funktionen användnings villkor för Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Tillåt eller blockera inbjudningar till B2B-användare från vissa organisationer

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 

Nu kan du ange vilka partner organisationer du vill dela och samar beta med i Azure AD B2B-samarbete. Om du vill göra detta kan du välja att skapa en lista över vissa domäner som tillåter eller nekar. När en domän blockeras med hjälp av dessa funktioner kan anställda inte längre skicka inbjudningar till personer i den domänen.

Detta hjälper dig att kontrol lera åtkomsten till dina resurser och möjliggör en smidig upplevelse för godkända användare.

Den här B2B-samarbets funktionen är tillgänglig för alla Azure Active Directory kunder och kan användas tillsammans med Azure AD Premium funktioner som villkorlig åtkomst och identitets skydd för mer detaljerad kontroll av när och hur externa affärs användare loggar in i och få åtkomst.

Mer information finns i [tillåta eller blockera inbjudningar till B2B-användare från vissa organisationer](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar som är tillgängliga i Azure AD App-galleriet

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I april 2018 har vi lagt till dessa 13 nya appar med stöd för federation i vårt app-Galleri:

Kriterie HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (on-premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [dynamisk signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [konverterare nu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [ Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), hylla, [SafetyNET](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Bevilja B2B-användare i Azure AD åtkomst till dina lokala program (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C

Som organisation som använder Azure Active Directory (Azure AD) B2B-samarbets funktioner för att bjuda in gäst användare från partner organisationer till din Azure AD, kan du nu ge dessa B2B-användare åtkomst till lokala appar. Dessa lokala appar kan använda SAML-baserad autentisering eller integrerad Windows-autentisering (IWA) med Kerberos-begränsad delegering (KCD).

Mer information finns i [bevilja B2B-användare i Azure AD åtkomst till dina lokala program](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Få självstudier om SSO-integrering från Azure Marketplace

**Typ:** Ändrad funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** integration från tredje part

Om ett program som listas på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) har stöd för SAML-baserad enkel inloggning **, ger du den** integrerings självstudie som är kopplad till det programmet. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Snabbare prestanda för automatisk användar etablering i Azure AD till SaaS-program

**Typ:** Ändrad funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Tidigare kunde kunder som använder Azure Active Directory etablerings anslutningarna för SaaS-program (till exempel Salesforce, ServiceNow och Box) uppleva långsamma prestanda om deras Azure AD-klienter innehåller över 100 000 kombinerade användare och grupper och de använde användar-och grupp tilldelningar för att avgöra vilka användare som ska tillhandahållas.

Den 2 april 2018 distribuerades betydande prestanda förbättringar till Azure AD Provisioning-tjänsten som avsevärt minskar den tid som krävs för att utföra inledande synkroniseringar mellan Azure Active Directory-och mål SaaS-program.

Det innebär att många kunder som hade första synkroniseringen till appar som tog flera dagar eller aldrig har slutförts, är nu inom några minuter eller timmar.

Mer information finns i [Vad händer under etableringen?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Lösen ords återställning via självbetjäning från Windows 10-Lås skärm för Hybrid Azure AD-anslutna datorer

**Typ:** Ändrad funktion  
**Tjänste kategori:** Lösen ords återställning via självbetjäning  
**Produkt kapacitet:** Användarautentisering
 
Vi har uppdaterat Windows 10 SSPR-funktionen som innehåller stöd för datorer som är hybrid Azure AD-anslutna. Den här funktionen är tillgänglig i Windows 10-RS4 gör att användarna kan återställa sina lösen ord från Lås skärmen på en Windows 10-dator. Användare som är aktiverade och registrerade för lösen ords återställning via självbetjäning kan använda den här funktionen.

Mer information finns i [lösen ords återställning i Azure AD från inloggnings skärmen](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Mars 2018
 
### <a name="certificate-expire-notification"></a>Meddelande om förfallo datum för certifikat

**Typ:** Fastsatt  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Azure AD skickar ett meddelande när ett certifikat för ett galleri eller ett program utanför galleriet håller på att gå ut. 

Vissa användare fick inga meddelanden för företags program som kon figurer ATS för SAML-baserad enkel inloggning. Det här problemet har lösts. Azure AD skickar ett meddelande för certifikat som upphör att gälla om 7, 30 och 60 dagar. Du kan se den här händelsen i gransknings loggarna. 

Mer information finns här:

- [Hantera certifikat för federerad enkel inloggning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Granska aktivitets rapporter i Azure Active Directory Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter-och GitHub identitets leverantörer i Azure AD B2C

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
Du kan nu lägga till Twitter eller GitHub som en identitets leverantör i Azure AD B2C. Twitter rör sig från offentlig för hands version till GA. GitHub släpps i offentlig för hands version.

Mer information finns i [Vad är Azure AD B2B-samarbete?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Begränsa webb läsar åtkomst med hjälp av Intune Managed Browser med Azure AD Application-based villkorlig åtkomst för iOS och Android

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & Skydd för identitets säkerhet
 
**Nu i offentlig för hands version!**

**INTUNE Managed Browser SSO:** Dina anställda kan använda enkel inloggning mellan interna klienter (t. ex. Microsoft Outlook) och Intune Managed Browser för alla Azure AD-anslutna appar.

**Stöd för Intune Managed Browser villkorlig åtkomst:** Du kan nu kräva att anställda använder Intune Managed Browser med programbaserade villkorliga åtkomst principer.

Läs mer om det här i [blogg inlägget](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Mer information finns här:

- [Konfigurera programbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Konfigurera principer för hanterade webbläsare](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Cmdletar för App-proxy i PowerShell GA-modulen

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control
 
Stöd för Application Proxy-cmdlets finns nu i PowerShell GA-modulen! Detta kräver att du fortsätter att uppdatera i PowerShell-moduler – om du blir mer än ett år bakom kan vissa cmdlets sluta fungera. 

Mer information finns i [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Inbyggda Office 365-klienter stöds av sömlös enkel inloggning med ett icke-interaktivt protokoll

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Användare som använder inbyggda Office 365-klienter (version 16.0.8730. xxxx och senare) får en tyst inloggnings upplevelse med sömlös SSO. Det här stödet tillhandahålls av tillägget för ett icke-interaktivt protokoll (WS-Trust) till Azure AD.

Mer information finns i [Hur loggar du in på en intern klient med sömlös SSO-användning?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Användarna får en tyst inloggning, med sömlös SSO, om ett program skickar inloggnings förfrågningar till Azure ADs klient slut punkter

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Användarna får en tyst inloggning, med sömlös SSO, om ett program (till exempel `https://contoso.sharepoint.com`) skickar inloggnings förfrågningar till Azure ADs klient slut punkter – det vill säga `https://login.microsoftonline.com/contoso.com/<..>` eller `https://login.microsoftonline.com/<tenant_ID>/<..>` – i stället för Azure ADs vanliga slut punkt (`https://login.microsoftonline.com/common/<...>`).

Mer information finns i [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Du behöver bara lägga till en Azure AD-URL, i stället för två URL: er tidigare, till användarnas intranät zons inställningar för att distribuera sömlös SSO

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Om du vill distribuera sömlös SSO till dina användare behöver du bara lägga till en Azure AD-URL till användarnas intranät zons inställningar med hjälp av grup princip i Active Directory: `https://autologon.microsoftazuread-sso.com`. Tidigare var kunden tvungen att lägga till två URL: er.

Mer information finns i [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar som är tillgängliga i Azure AD App-galleriet

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I mars 2018 har vi lagt till dessa 15 nya appar med stöd för federation i vårt app-Galleri:

[BoxCryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant av FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), vtiger CRM, inblinkande, [amplitud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial) [](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial) [](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial) [, Bersin, Mercell, Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>PIM för Azure-resurser är allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
Om du använder Azure AD Privileged Identity Management för katalog roller kan du nu använda PIM: s tidsbegränsade åtkomst-och tilldelnings funktioner för Azures resurs roller som prenumerationer, resurs grupper, Virtual Machines och andra resurser som stöds genom att Azure Resource Manager. Använd Multi-Factor Authentication när du aktiverar roller just-in-Time och Schemalägg aktiveringar i samordning med godkända ändrings fönster. Den här versionen lägger dessutom till förbättringar som inte är tillgängliga under den offentliga för hands versionen, inklusive ett uppdaterat gränssnitt, arbets flöden för godkännande och möjligheten att utöka roller som upphör snart att gälla och förnya förfallna roller.

Mer information finns i [PIM för Azure-resurser (för hands version)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Lägga till valfria anspråk till dina Apps-token (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Din Azure AD-App kan nu begära anpassade eller valfria anspråk i JWTs eller SAML-token.  Detta är anspråk på den användare eller klient som inte ingår som standard i token, på grund av storleks begränsningar eller tillämplighet.  Detta är för närvarande en offentlig för hands version av Azure AD-appar på v 1.0-och v 2.0-slutpunkterna.  I dokumentationen finns information om vilka anspråk som kan läggas till och hur du redigerar applikations manifestet för att begära dem.  

Mer information finns i [valfria anspråk i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD stöder PKCE för säkrare OAuth-flöden

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Azure AD-dokument har uppdaterats för att anteckna stöd för PKCE, vilket möjliggör säkrare kommunikation under OAuth 2,0-auktoriseringskod.  Både S256-och klartext-code_challenges stöds för v 1.0-och v 2.0-slutpunkter. 

Mer information finns i [begära en auktoriseringskod](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Stöd för etablering av alla attributvärden för användare som är tillgängliga i Get_Workers-API: et för Workday

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Den allmänt tillgängliga för hands versionen av inkommande etablering från Workday till Active Directory och Azure AD stöder nu möjligheten att extrahera och tillhandahålla alla attributvärden som är tillgängliga i Get_Workers-API: et för Workday. Detta lägger till stöd för hundratals ytterligare standard-och anpassade attribut utöver de som levererades med den ursprungliga versionen av arbets dagen inkommande etablerings anslutning.

Mer information finns i: [Anpassa listan över användar egenskaper för arbets dagar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Ändra grupp medlemskap från dynamisk till statisk och vice versa

**Typ:** Ny funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete
 
Det går att ändra hur medlemskap hanteras i en grupp. Detta är användbart om du vill behålla samma grupp namn och ID i systemet, så att alla befintliga referenser till gruppen fortfarande är giltiga. att skapa en ny grupp kräver uppdatering av dessa referenser.
Azure AD Admin Center har uppdaterats för att stödja den här funktionen. Kunder kan nu konvertera befintliga grupper från dynamiskt medlemskap till tilldelat medlemskap och vice versa. De befintliga PowerShell-cmdletarna är också fortfarande tillgängliga.

Mer information finns i [regler för dynamiska medlemskap för grupper i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Förbättrat utloggnings beteende med sömlös SSO

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Även om användare uttryckligen har loggat ut från ett program som skyddas av Azure AD, kommer de automatiskt att loggas in igen med sömlös SSO om de försökte få åtkomst till ett Azure AD-program igen inom sitt företags nätverk från sina domänanslutna enheter. Med den här ändringen stöds utloggning.  Detta gör att användarna kan välja samma eller ett annat Azure AD-konto för att logga in igen i stället för att automatiskt logga in med sömlös SSO.

Mer information finns i [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Version 1.5.402.0 av Application Proxy Connector lanserad

**Typ:** Ändrad funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** & Skydd för identitets säkerhet
 
Den här anslutnings versionen distribueras gradvis till och med november. Den här nya anslutnings versionen innehåller följande ändringar:

- Nu anges cookies på domän nivå i stället för under domän nivå. Detta säkerställer en smidig SSO-upplevelse och förhindrar redundanta autentiseringsförsök.
- Stöd för chunked encoding-begäranden
- Förbättrad övervakning av anslutnings hälsa 
- Flera fel korrigeringar och stabilitets förbättringar

Mer information finns i [förstå Azure AD-programproxy-kopplingar](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Februari 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Förbättrad navigering för att hantera användare och grupper

**Typ:** Planera för ändring  
**Tjänste kategori:** Katalog hantering  
**Produkt kapacitet:** Katalogen

Navigerings miljön för att hantera användare och grupper har effektiviserats. Nu kan du navigera från katalog översikten direkt till listan över alla användare, med enklare åtkomst till listan över borttagna användare. Du kan också navigera från katalog översikten direkt till listan över alla grupper, med enklare åtkomst till inställningar för grupp hantering. Och även från sidan katalog översikt kan du söka efter en användare, grupp, företags program eller registrera appar. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Tillgänglighet för inloggnings-och gransknings rapporter i Microsoft Azure som drivs av 21Vianet (Azure Kina 21Vianet)

**Typ:** Ny funktion  
**Tjänste kategori:** Azure Stack  
**Produkt kapacitet:** Övervaka & rapportering

Azure AD aktivitets logg rapporter är nu tillgängliga i Microsoft Azure som drivs av 21Vianet-instanser (Azure Kina 21Vianet). Följande loggar ingår:

- **Inloggnings loggar** – innehåller alla inloggnings loggar som är kopplade till din klient.

- **Självbetjänings loggar för lösen ords granskning** – innehåller alla SSPR gransknings loggar.

- **Gransknings loggar för katalog hantering** – innehåller alla katalog hanterings relaterade gransknings loggar som användar hantering, program hantering och andra.

Med dessa loggar kan du få insikter om hur din miljö fungerar. Med dessa data kan du:

- Fastställ hur dina användare använder dina appar och tjänster.

- Felsök problem som hindrar användarna från att få jobbet gjort.

Mer information om hur du använder de här rapporterna finns i [Azure Active Directory rapportering](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Använd rollen rapport läsare (icke-administrativ roll) om du vill visa Azure AD-aktivitets rapporter

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Som en del av kundernas synpunkter på att göra det möjligt för icke-administratörer att få åtkomst till Azure AD-aktivitets loggar har vi aktiverat möjligheten för användare som finns i rollen "rapport läsare" för att få åtkomst till inloggningar och gransknings aktiviteter i Azure Portal samt använda våra Graph API: er. 

Mer information om hur du använder de här rapporterna finns i [Azure Active Directory rapportering](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Anställningsnr-anspråk tillgängligt som användarattribut och användar-ID

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Du kan konfigurera **Anställningsnr** som användar-ID och användarattribut för medlems användare och B2B-gäster i SAML-baserade inloggnings program från företags programmets användar gränssnitt.

Mer information finns i [Anpassa anspråk som utfärdats i SAML-token för företags program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Förenklad program hantering med jokertecken i Azure AD-programproxy

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Användarautentisering
 
För att göra program distributionen enklare och minska din administrativa belastning har vi nu stöd för möjligheten att publicera program med hjälp av jokertecken. Om du vill publicera ett jokertecken, kan du följa standard flödet för program publicering, men använda ett jokertecken i interna och externa URL: er.

Mer information finns i [program med jokertecken i Azure Active Directory Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nya cmdletar som stöder konfiguration av programproxy

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Systemet

Den senaste versionen av AzureAD PowerShell Preview-modulen innehåller nya cmdletar som gör det möjligt för kunder att konfigurera Application Proxy-program med hjälp av PowerShell.

De nya cmdletarna är: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nya cmdletar som stöder konfiguration av grupper

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Systemet

Den senaste versionen av AzureAD PowerShell-modulen innehåller cmdlets för att hantera grupper i Azure AD. Dessa cmdletar fanns tidigare tillgängliga i AzureADPreview-modulen och läggs nu till i AzureAD-modulen

De grupp-cmdletar som nu är tillgängliga för allmän tillgänglighet är: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Återställ-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>En ny version av Azure AD Connect är tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** AD Sync  
**Produkt kapacitet:** Systemet
 
Azure AD Connect är det bästa verktyget för att synkronisera data mellan Azure AD och lokala data källor, inklusive Windows Server Active Directory och LDAP.

>[!Important]
>I den här versionen införs ändringar av schema-och Synkroniseringsregel. Tjänsten Azure AD Connect-synkronisering utlöser en fullständig import och fullständig synkronisering efter en uppgradering. Information om hur du ändrar det här beteendet finns i [så här uppskjutar du fullständig synkronisering efter uppgraderingen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Den här versionen har följande uppdateringar och ändringar:

**Korrigerade problem**

- Åtgärda tids periods fönstret för bakgrunds aktiviteter för partitions filtrerings sidan när du växlar till nästa sida.

- En bugg har åtgärd ATS som orsakade åtkomst fel under den anpassade ConfigDB-åtgärden.

- En bugg har åtgärd ATS för att återställa från SQL-anslutningstimeout.

- Ett fel har åtgärd ATS där certifikat med SAN-jokertecken inte har förskotts kontroll.

- Ett fel som gör att MIIServer. exe kraschar under export av AAD Connector har åtgärd ATS.

- En bugg har åtgärd ATS där ett felaktigt lösen ord som är inloggat på DOMÄNKONTROLLANT vid körning orsakade av AAD Connect-guiden för att ändra konfigurationen

**Nya funktioner och förbättringar**
 
- Programtelemetri – administratörer kan ändra den här data klassen på/av.

- Azure AD Health-data – administratörer måste besöka hälso portalen för att kontrol lera sina hälso inställningar. När tjänst principen har ändrats, kommer agenterna att läsa och tillämpa den.

- Tillagda konfigurations åtgärder för tillbakaskrivning av enheten och en förlopps indikator för sid initiering.

- Förbättrad allmän diagnostik med HTML-rapport och fullständig data insamling i en ZIP-text/HTML-rapport.

- Förbättrad tillförlitlighet för automatisk uppgradering och ytterligare telemetri har lagts till för att säkerställa att serverns hälso tillstånd kan fastställas.

- Begränsa behörigheter som är tillgängliga för privilegierade konton på AD Connector-konto. För nya installationer begränsar guiden de behörigheter som privilegierade konton har på MSOL-kontot när MSOL-kontot har skapats. Ändringarna påverkar Express installationer och anpassade installationer med automatiskt skapande av konto.

- Ändrade installations programmet till att inte kräva SA-behörighet vid ren installation av AADConnect.

- Nytt verktyg för att Felsöka synkroniseringsproblem för ett bestämt objekt. För närvarande söker verktyget efter följande saker:

    - UserPrincipalName matchnings fel mellan synkroniserat användar objekt och användar kontot i Azure AD-klienten.
  
    - Om objektet filtreras från synkroniseringen på grund av domän filtrering
  
    - Om objektet filtreras från synkroniseringen på grund av organisations enhetens (OU)-filtrering

- Nytt verktyg för att synkronisera den aktuella lösen ords hashen som lagras i den lokala Active Directory för ett särskilt användar konto. Verktyget kräver ingen lösen ords ändring. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Program som stöder Intune-appskydd principer som har lagts till för användning med Azure AD-programbaserad villkorlig åtkomst

**Typ:** Ändrad funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Vi har lagt till fler program som stöder programbaserad villkorlig åtkomst. Nu kan du få åtkomst till Office 365 och andra Azure AD-anslutna molnappar med dessa godkända klient program.

Följande program kommer att läggas till i slutet av februari:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

Mer information finns här:

- [Godkänt klient program krav](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD App-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Användningsvillkor uppdatering av mobil miljö 

**Typ:** Ändrad funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Fastställ

När användnings villkoren visas kan du nu Klicka på **Visa? Klicka här**. När du klickar på den här länken öppnas de användnings villkor som används internt på enheten. Oavsett tecken storlek i dokumentet eller skärmens storlek kan du zooma och läsa dokumentet efter behov. 

---
 
## <a name="january-2018"></a>Januari 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar som är tillgängliga i Azure AD App-galleriet 

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I januari 2018 lades följande nya appar med stöd för federation i app-galleriet:

[IBM Openpages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federerad Directory och [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Logga in med ytterligare risk upptäckt

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & Skydd för identitets säkerhet

De insikter som du får för en identifierad risk identifiering är knutna till din Azure AD-prenumeration. Med Azure AD Premium P2-versionen får du den mest detaljerade informationen om alla underliggande identifieringar.

Med Azure AD Premium P1-versionen visas identifieringar som inte täcks av din licens som inloggning med risk identifiering med ytterligare risk upptäckt.

Mer information finns i [Azure Active Directory risk identifieringar](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Dölj Office 365-program från slutanvändarens åtkomst paneler

**Typ:** Ny funktion  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** DEFINITION

Nu kan du bättre hantera hur Office 365-program visas på användarens åtkomst paneler via en ny användar inställning. Det här alternativet är användbart om du vill minska antalet appar i en användares åtkomst paneler om du bara vill visa Office-appar i Office-portalen. Inställningen finns i **användar inställningarna** och är märkt, **användare kan bara se Office 365-appar i Office 365-portalen**.

Mer information finns i [Dölj ett program från användarens upplevelse i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Sömlös inloggning i appar aktiverade för inloggning med lösen ord direkt från appens URL 

**Typ:** Ny funktion  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** DEFINITION

Webb läsar tillägget Mina appar är nu tillgängligt via ett användbart verktyg som ger dig funktionen Mina appar enkel inloggning som en genväg i din webbläsare. När användaren har installerat visas en rutmärket-ikon i webbläsaren som ger dem snabb åtkomst till appar. Användarna kan nu dra nytta av:

- Möjligheten att direkt logga in till SSO-baserade appar från appens inloggnings sida
- Starta en app med hjälp av funktionen snabb sökning
- Genvägar till nyligen använda appar från tillägget
- Tillägget är tillgängligt för Microsoft Edge, Chrome och Firefox.
 
Mer information finns i avsnittet [Mina appar säker inloggnings tillägg](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Administrations upplevelsen för Azure AD i Klassisk Azure-portal har dragits tillbaka

**Typ:** Föråldrad   
**Tjänste kategori:** Azure AD  
**Produkt kapacitet:** Katalogen

Från och med den 8 januari 2018 har Azure AD-administrationen i den klassiska Azure-portalen dragits tillbaka. Detta ägde rum i samband med att den klassiska Azure-portalen upphör att gälla. I framtiden bör du använda [Azure AD administrations Center](https://aad.portal.azure.com) för all Portal-baserad administration av Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Webb portalen för PhoneFactor har dragits tillbaka

**Typ:** Föråldrad  
**Tjänste kategori:** Azure AD  
**Produkt kapacitet:** Katalogen
 
Från och med den 8 januari 2018 har webb portalen för PhoneFactor dragits tillbaka. Den här portalen användes för administration av MFA Server, men dessa funktioner har flyttats till Azure Portal på portal.azure.com. 

MFA-konfigurationen finns på: **Azure Active Directory \> MFA Server**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Föråldrade Azure AD-rapporter

**Typ:** Föråldrad  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Hantering av identitets livs cykel  


Med den allmänna tillgängligheten för den nya Azure Active Directory-administrationskonsolen och nya API: er som nu är tillgängliga för både aktivitets-och säkerhets rapporter, har rapport-API: erna under "/Reports"-slut punkten dragits tillbaka från och med den 31 december 2017.

**Vad är tillgängligt?**

Som en del av över gången till den nya administratörs konsolen har vi gjort 2 nya API: er tillgängliga för att hämta Azure AD-aktivitets loggar. De nya API: erna ger omfattande funktioner för filtrering och sortering utöver att tillhandahålla omfattande gransknings-och inloggnings aktiviteter. Data som tidigare var tillgängliga via säkerhets rapporterna kan nu nås via identitets skyddets identifierings-API i Microsoft Graph.

Mer information finns här:

- [Kom igång med API: et för Azure Active Directory rapportering](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Kom igång med Azure Active Directory Identity Protection och Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>December 2017

### <a name="terms-of-use-in-the-access-panel"></a>Användningsvillkor på åtkomst panelen

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Fastställ
 
Nu kan du gå till åtkomst panelen och Visa användnings villkoren som du har accepterat tidigare.

Följ de här stegen:

1. Gå till portalen för mina [appar](https://myapps.microsoft.com)och logga in.

2. Välj ditt namn i det övre högra hörnet och välj sedan **profil** i listan. 

3. Välj **Granska användnings villkor**i din **profil**. 

4. Nu kan du granska användnings villkoren som du har accepterat. 

Mer information finns i [funktionen användnings villkor för Azure AD (för hands version)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Ny inloggnings upplevelse för Azure AD

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD  
**Produkt kapacitet:** Användarautentisering
 
Azure AD-och Microsoft-konto Identity system-UIs har gjorts om så att de har ett enhetligt utseende och känsla. Dessutom samlar inloggnings sidan för Azure AD in användar namnet först, följt av autentiseringsuppgifterna på en andra skärm.

Mer information finns i [den nya inloggnings upplevelsen för Azure AD finns nu i offentlig för hands version](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Färre inloggnings meddelanden: en ny "Håll mig inloggad"-upplevelse för Azure AD-inloggning

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD  
**Produkt kapacitet:** Användarautentisering
 
Kryss rutan **Håll mig inloggad** på inloggnings sidan för Azure AD ersattes med en ny fråga som visas när du har autentiserat dig. 

Om du svarar **Ja** på den här varningen ger tjänsten en beständig uppdateringstoken. Detta är samma sak som när du markerade kryss rutan **Håll mig inloggad** i den gamla upplevelsen. För federerade klienter visas den här prompten när du har autentiserat dig med den federerade tjänsten.

Mer information finns [i färre inloggnings meddelanden: den nya funktionen "Håll mig inloggad" för Azure AD är i för hands version](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Lägg till konfiguration så att användnings villkoren måste expanderas innan de accepteras

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Fastställ
 
Ett alternativ för administratörer kräver att användarna expanderar användnings villkoren innan de accepterar villkoren.

Välj antingen **på** eller **av** för att kräva att användarna expanderar användnings villkoren. Inställningen **on** kräver att användare visar användnings villkoren innan de accepterar dem.

Mer information finns i [funktionen användnings villkor för Azure AD (för hands version)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Omfattnings aktivering för kvalificerade roll tilldelningar

**Typ:** Ny funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
Du kan använda omfattnings aktivering för att aktivera kvalificerade Azure-resurs roll tilldelningar med mindre självständighet än standardinställningarna för den ursprungliga tilldelningen. Ett exempel är om du är tilldelad som ägare till en prenumeration i din klient organisation. Med begränsad aktivering kan du aktivera ägar rollen för upp till fem resurser som ingår i prenumerationen (till exempel resurs grupper och virtuella datorer). Omfånget för aktiveringen kan minska möjligheten att köra oönskade ändringar av kritiska Azure-resurser.

Mer information finns i [Vad är Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nya federerade appar i Azure AD App-galleriet

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I december 2017 har vi lagt till dessa nya appar med stöd för federation i vårt app-Galleri:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI digital butik](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [image Work](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO för Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO för BitBucket av resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD-integrering.

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Godkännande arbets flöden för Azure AD-katalog roller

**Typ:** Ändrad funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
Arbets flödet för godkännande för Azure AD Directory-roller är allmänt tillgängligt.

Med ett arbets flöde för godkännande kan privilegierade roll administratörer kräva att berättigade Rolls medlemmar begär roll aktivering innan de kan använda den privilegierade rollen. Flera användare och grupper kan delegera godkännande ansvar. Berättigade roll medlemmar får aviseringar när godkännande är klart och deras roll är aktiv.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Direktautentisering: Skype for Business-Support

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Direkt autentisering stöder nu användar inloggningar till Skype för företag-klientprogram som stöder modern autentisering, som innehåller online-och hybrid topologier. 

Mer information finns i [Skype for Business-topologier som stöds med modern autentisering](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Uppdateringar till Azure AD Privileged Identity Management för Azure RBAC (för hands version)

**Typ:** Ändrad funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
Med den offentliga för hands versionen av Azure AD Privileged Identity Management (PIM) för Azure Role-baserade Access Control (RBAC) kan du nu:

* Använd bara tillräckligt med administration.
* Kräv godkännande för att aktivera resurs roller.
* Schemalägg en framtida aktivering av en roll som kräver godkännande för både Azure AD-och Azure RBAC-roller.
 
Mer information finns i [Privileged Identity Management för Azure-resurser (för hands version)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>November 2017
 
### <a name="access-control-service-retirement"></a>Access Control tjänstens upphör Ande

**Typ:** Planera för ändring  
**Tjänste kategori:** Access Control tjänst  
**Produkt kapacitet:** Access Control tjänst 

Azure Active Directory Access Control (även kallat Access Control tjänsten) kommer att dras tillbaka om 2018. Mer information som innehåller ett detaljerat rikt linjer för migrering på hög nivå kommer att tillhandahållas under de kommande veckorna. Du kan lämna kommentarer på den här sidan med frågor om tjänsten Access Control och en grupp medlem besvarar dem.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Begränsa webb läsar åtkomst till Intune Managed Browser 

**Typ:** Planera för ändring  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** Identitets säkerhet och skydd

Du kan begränsa webbläsarens åtkomst till Office 365 och andra Azure AD-anslutna molnappar genom att använda Intune Managed Browser som en godkänd app. 

Du kan nu konfigurera följande villkor för program-baserad villkorlig åtkomst:

**Klient program:** Webbläsare

**Vad händer med ändringen?**

I dag blockeras åtkomsten när du använder det här villkoret. När för hands versionen är tillgänglig kräver all åtkomst att programmet Managed Browser används. 

Titta efter den här funktionen och mer information i kommande Bloggar och viktig information. 

Mer information finns i [villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klient program för Azure AD App-baserad villkorlig åtkomst

**Typ:** Planera för ändring  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** Identitets säkerhet och skydd

Följande appar finns på listan över [godkända klient program](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft-Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Mer information finns här:

- [Godkänt klient program krav](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD App-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Användnings villkor för flera språk

**Typ:** Ny funktion    
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Fastställ

Administratörer kan nu skapa nya användnings villkor som innehåller flera PDF-dokument. Du kan tagga dessa PDF-dokument med ett motsvarande språk. Användarna visar PDF-filen med det matchande språket baserat på deras inställningar. Om det inte finns någon matchning visas standard språket.

---
 
### <a name="real-time-password-writeback-client-status"></a>Klient status för tillbakaskrivning av lösen ord i real tid

**Typ:** Ny funktion  
**Tjänste kategori:** Lösen ords återställning via självbetjäning  
**Produkt kapacitet:** Användarautentisering

Nu kan du granska status för din lokala tillbakaskrivning av lösen ord. Det här alternativet är tillgängligt i avsnittet **lokal integration** på sidan [lösen ords återställning](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) . 

Om det finns problem med anslutningen till din lokala tillbakaskrivning-klient visas ett fel meddelande som ger dig följande:

- Information om varför du inte kan ansluta till din lokala tillbakaskrivning-klient.
- En länk till dokumentationen som hjälper dig att lösa problemet. 

Mer information finns i [lokal integrering](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD App-baserad villkorlig åtkomst 
 
**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD  
**Produkt kapacitet:** Identitets säkerhet och skydd

Nu kan du begränsa åtkomsten till Office 365 och andra Azure AD-anslutna molnappar till [godkända klient program](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) som stöder Intune App Protection-principer med hjälp av [Azure AD App-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Intune App Protection-principer används för att konfigurera och skydda företags data i de här klient programmen.

Genom att kombinera [app-baserade](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) med [enhets](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) principer för villkorlig åtkomst har du flexibiliteten att skydda data för personliga och företagets enheter.

Följande villkor och kontroller är nu tillgängliga för användning med app-baserad villkorlig åtkomst:

**Plattforms villkor som stöds**

- iOS
- Android

**Villkor för klient program**

- Mobilappar och skriv bords klienter

**Åtkomstkontroll**

- Kräv godkänd klient app

Mer information finns i [Azure AD App-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Hantera Azure AD-enheter i Azure Portal

**Typ:** Ny funktion  
**Tjänste kategori:** Enhets registrering och hantering  
**Produkt kapacitet:** Identitets säkerhet och skydd

Nu kan du hitta alla enheter som är anslutna till Azure AD och de enhets relaterade aktiviteterna på ett och samma ställe. Det finns en ny administrations upplevelse för att hantera alla enhets identiteter och inställningar i Azure Portal. I den här versionen kan du:

- Visa alla enheter som är tillgängliga för villkorlig åtkomst i Azure AD.
- Visa egenskaper, som innehåller dina hybrid Azure AD-anslutna enheter.
- Hitta BitLocker-nycklar för Azure AD-anslutna enheter, hantera enheten med Intune med mera.
- Hantera inställningar för Azure AD-enhet.

Mer information finns i [Hantera enheter med hjälp av Azure Portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Stöd för macOS som enhets plattform för villkorlig åtkomst i Azure AD 

**Typ:** Ny funktion    
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** Identitets säkerhet och skydd 

Du kan nu inkludera (eller undanta) macOS som enhets plattforms villkor i din Azure AD-princip för villkorlig åtkomst. Med att lägga till macOS i de plattformar som stöds, kan du:

- **Registrera och hantera macOS-enheter med hjälp av Intune.** På samma sätt som för andra plattformar, t. ex. iOS och Android, är ett företags Portal program tillgängligt för macOS för att göra enhetliga registreringar. Du kan använda den nya företagsportalsappen för macOS för att registrera en enhet med Intune och registrera den med Azure AD.
- **Se till att macOS-enheter följer organisationens efterlevnadsprinciper som definierats i Intune.** I Intune på Azure Portal kan du nu konfigurera efterlevnadsprinciper för macOS-enheter. 
- **Begränsa åtkomsten till program i Azure AD till endast kompatibla macOS-enheter.** Redigering av princip för villkorlig åtkomst har macOS som en separat enhets plattforms alternativ. Nu kan du skapa macOS-specifika principer för villkorlig åtkomst för mål programmet som angetts i Azure.

Mer information finns här:

- [Skapa en princip för enhetsefterlevnad för macOS-enheter med Intune](https://aka.ms/macoscompliancepolicy)
- [Villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Nätverks princip Server tillägg för Azure Multi-Factor Authentication 

**Typ:** Ny funktion    
**Tjänste kategori:**  Multi-Factor Authentication  
**Produkt kapacitet:** Användarautentisering

Nätverks princip Server tillägget för Azure Multi-Factor Authentication lägger till molnbaserade Multi-Factor Authentication funktioner till din infrastruktur för autentisering med hjälp av dina befintliga servrar. Med nätverks princip Server tillägget kan du lägga till telefonsamtal, textmeddelande eller telefon programs verifiering till ditt befintliga autentiseringspaket. Du behöver inte installera, konfigurera och underhålla nya servrar. 

Det här tillägget har skapats för organisationer som vill skydda virtuella privata nätverks anslutningar utan att distribuera Azure-Multi-Factor Authentication-server. Nätverks princip Server tillägget fungerar som ett kort mellan RADIUS-och molnbaserade Azure-Multi-Factor Authentication för att tillhandahålla en andra faktor för autentisering för federerade eller synkroniserade användare.

Mer information finns i [integrera din befintliga infrastruktur för nätverks Policy Server med Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Återställ eller ta bort borttagna användare permanent

**Typ:** Ny funktion    
**Tjänste kategori:** Användar hantering  
**Produkt kapacitet:** Katalogen 

I Azure AD Admin Center kan du nu:

- Återställa en borttagen användare. 
- Ta bort en användare permanent.

**Så här provar du:**

1. I Azure AD administrations Center väljer du [alla användare](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) i avsnittet **Hantera** . 

2. I listan **Visa** väljer du **nyligen borttagna användare**. 

3. Välj en eller flera nyligen borttagna användare och Återställ dem sedan eller ta bort dem permanent.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klient program för Azure AD App-baserad villkorlig åtkomst
 
**Typ:** Ändrad funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** Identitets säkerhet och skydd

Följande appar har lagts till i listan över [godkända klient program](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 

Mer information finns här:

- [Godkänt klient program krav](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD App-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Använd "eller" mellan kontroller i en princip för villkorlig åtkomst 

**Typ:** Ändrad funktion    
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** Identitets säkerhet och skydd
 
Du kan nu använda "eller" (Kräv en av de valda kontrollerna) för kontroller för villkorlig åtkomst. Du kan använda den här funktionen för att skapa principer med "eller" mellan åtkomst kontroller. Du kan till exempel använda den här funktionen för att skapa en princip som kräver att en användare loggar in med hjälp av Multi-Factor Authentication "eller" på en kompatibel enhet.

Mer information finns i [kontroller i villkorlig åtkomst för Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Agg regering av risk identifieringar i real tid

**Typ:** Ändrad funktion    
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** Identitets säkerhet och skydd

I Azure AD Identity Protection sammanställs nu alla risk identifieringar i real tid som kommer från samma IP-adress på en bestämd dag för varje typ av risk identifiering. Den här ändringen begränsar mängden risk identifieringar som visas utan några ändringar i användar säkerheten.

Den underliggande real tids identifieringen fungerar varje gången användaren loggar in. Om du har en säkerhets princip för inloggnings risker som är inställd på att Multi-Factor Authentication eller blockera åtkomst, utlöses den fortfarande under varje riskfylld inloggning.
 
---
 
## <a name="october-2017"></a>Oktober 2017

### <a name="deprecate-azure-ad-reports"></a>Föråldrade Azure AD-rapporter

**Typ:** Planera för ändring  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Hantering av identitets livs cykel  

Azure Portal ger dig följande:

- En ny administrations konsol för Azure AD.
- Nya API: er för aktivitets-och säkerhets rapporter.
 
På grund av de här nya funktionerna drogs rapport-API: erna under den/Reports-slutpunkten ut den 10 december 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Automatisk identifiering av inloggnings fält

**Typ:** Fastsatt   
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** Enkel inloggning  

Azure AD stöder automatisk identifiering av inloggnings fält för program som återger ett fält med HTML-användarnamn och lösen ord. De här stegen beskrivs i [hur du automatiskt samlar in inloggnings fält för ett program](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Du kan hitta den här funktionen genom att lägga till ett program som *inte är en Galleri* på sidan **företags program** i [Azure Portal](https://aad.portal.azure.com). Dessutom kan du konfigurera läget för **enkel inloggning** på det nya programmet till **lösenordsbaserad enkel inloggning**, ange en webb-URL och sedan spara sidan.
 
Den här funktionen har tillfälligt inaktiverats på grund av ett tjänst problem. Problemet har lösts och den automatiska identifieringen av inloggnings fält är tillgänglig igen.

---

### <a name="new-multi-factor-authentication-features"></a>Nya Multi-Factor Authentication funktioner

**Typ:** Ny funktion  
**Tjänste kategori:** Multi-Factor Authentication  
**Produkt kapacitet:** Identitets säkerhet och skydd  

Multi-Factor Authentication (MFA) är en viktig del av att skydda din organisation. Följande funktioner har lagts till för att göra autentiseringsuppgifterna mer anpassningsbara och upplevelsen smidigare: 

- Multi-Factor Challenge-resultaten integreras direkt i inloggnings rapporten för Azure AD, vilket omfattar programmerings åtkomst till MFA-resultat.
- MFA-konfigurationen är mer integrerad i konfigurations upplevelsen för Azure AD i Azure Portal.

Med den här offentliga för hands versionen är MFA Management och repor ting en integrerad del av konfigurations upplevelsen för kärnan i Azure AD. Nu kan du hantera funktionen MFA Management Portal i Azure AD-upplevelsen.

Mer information finns i [referens för MFA-rapportering i Azure Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Användningsvillkor

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Fastställ  

Du kan använda användnings villkoren för Azure AD för att presentera information, till exempel relevanta frifordringar för användarnas juridiska krav eller efterlevnad.

Du kan använda användnings villkoren för Azure AD i följande scenarier:

- Allmänna användnings villkor för alla användare i din organisation
- Vissa användnings villkor baserat på en användares attribut (till exempel läkare eller anställda eller inrikes och internationella anställda) som gjorts av dynamiska grupper.
- Specifika användnings villkor för att få åtkomst till affärs program med hög effekt, till exempel Salesforce

Mer information finns i [användnings villkoren för Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Förbättringar för Privileged Identity Management

**Typ:** Ny funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management  

Med Azure AD Privileged Identity Management kan du hantera, kontrol lera och övervaka åtkomsten till Azure-resurser (för hands version) i din organisation för att:

- Prenumerationer
- Resursgrupper
- Virtuella maskiner 

Alla resurser inom Azure Portal som använder Azure RBAC-funktionen kan dra nytta av alla funktioner för säkerhet och livs cykel hantering som Azure AD Privileged Identity Management har att erbjuda.

Mer information finns i [Privileged Identity Management för Azure-resurser](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Åtkomstgranskningar

**Typ:** Ny funktion  
**Tjänste kategori:** Åtkomst granskningar  
**Produkt kapacitet:** Fastställ  

Organisationer kan använda åtkomst granskningar (för hands version) för att effektivt hantera grupp medlemskap och åtkomst till företags program: 

- Du kan certifiera om gästanvändares åtkomst med åtkomstgranskningar av deras åtkomst till program och medlemskap i grupper. Granskare kan effektivt bestämma om du vill tillåta att gäster fortsätter att ha åtkomst baserat på de insikter som tillhandahålls av åtkomst granskningarna.
- Du kan certifiera om medarbetares åtkomst till program och gruppmedlemskap med åtkomstgranskningar.

Du kan samla in åtkomstgranskningskontroller i program som är relevanta för din organisation för att spåra granskningar för efterlevnad eller riskkänsliga program.

Mer information finns i [åtkomst granskningar för Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Dölj program från tredje part från Mina appar och Office 365 App Launcher

**Typ:** Ny funktion  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** Enkel inloggning  

Nu kan du bättre hantera appar som visas på användarnas portaler via en ny **Dölj app** -egenskap. Du kan dölja appar för att få hjälp i fall där app-paneler visas för backend-tjänster eller dubbletter av paneler och färdiga användares program lanseringar. Växlingen är i avsnittet **Egenskaper** i appen från tredje part och är märkt **synlig för användaren?** Du kan också dölja en app via programmering via PowerShell. 

Mer information finns i [Dölj ett program från tredje part från en användares upplevelse i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Vad är tillgängligt?**

 Som en del av över gången till den nya administratörs konsolen är två nya API: er för att hämta Azure AD-aktivitets loggar tillgängliga. De nya API: erna ger omfattande funktioner för filtrering och sortering utöver att tillhandahålla omfattande gransknings-och inloggnings aktiviteter. De data som tidigare var tillgängliga via säkerhets rapporterna kan nu nås via identitets skydds identifieraren API i Microsoft Graph.


## <a name="september-2017"></a>September 2017

### <a name="hotfix-for-identity-manager"></a>Snabb korrigering för Identity Manager

**Typ:** Ändrad funktion  
**Tjänste kategori:** Identitets hanterare  
**Produkt kapacitet:** Hantering av identitets livs cykel  

Ett samlings paket för snabb korrigeringar (build 4.4.1642.0) är tillgängligt från och med 25 september 2017 för Identity Manager 2016 Service Pack 1. Detta sammanslagnings paket:

- Löser problem och lägger till förbättringar.
- Är en kumulativ uppdatering som ersätter alla uppdateringar för Identity Manager 2016 Service Pack 1 upp till build 4.4.1459.0 för Identity Manager 2016. 
- Kräver att du har Identity Manager 2016 build-4.4.1302.0. 

Mer information finns i [samlat snabb korrigerings paket (build 4.4.1642.0) tillgängligt för Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
