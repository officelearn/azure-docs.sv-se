---
title: Vill du arkivera vad som är nytt i Azure Active Directory? | Microsoft Docs
description: Vad är ny viktig i översikten delen av här Innehållspaketet innehåller 6 månaderna för aktiviteten. Efter sex månader artiklarna tas bort från den huvudsakliga artikeln och placera den här artikeln i arkivet.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2249be583f10aaf7ddda039fbd6d71729b06c36
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565493"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Vill du arkivera vad som är nytt i Azure Active Directory?

Den primära artikeln [Nyheter i Azure Active Directory? viktig](whats-new.md) information innehåller uppdateringar för de senaste sex månaderna, medan den här artikeln innehåller all äldre information.

Vad är nytt i Azure Active Directory? versions information innehåller information om:

- Den senaste versionen
- Kända problem
- Felkorrigeringar
- Inaktuell funktion
- Planer för ändringar

---

## <a name="july-2019"></a>Juli 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Planera för ändring: programproxy-tjänstens uppdatering som endast stöder TLS 1,2

**Typ:** Planera för ändring  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control

För att hjälpa dig med vår starkaste kryptering kommer vi att börja begränsa åtkomsten till Application Proxy-tjänsten till endast TLS 1,2-protokoll. Den här begränsningen kommer inlednings vis att distribueras till kunder som redan använder TLS 1,2-protokoll, så att du inte ser effekten. Fullständig utfasning av TLS 1,0 och TLS 1,1-protokollen kommer att slutföras den 31 augusti 2019. Kunder som fortfarande använder TLS 1,0 och TLS 1,1 får ett avancerat meddelande om att förbereda inför den här ändringen.

För att upprätthålla anslutningen till Application Proxy-tjänsten i hela den här ändringen rekommenderar vi att du kontrollerar att kombinationerna av klient-och webb läsar servern har uppdaterats för att använda TLS 1,2. Vi rekommenderar också att du tar med alla klient system som används av dina anställda för att få åtkomst till appar som publicerats via tjänsten Application Proxy.

Mer information finns i [lägga till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plan för ändring: design uppdateringar kommer för program galleriet

**Typ:** Planera för ändring  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Nya användar gränssnitts ändringar kommer till design av bladet **Lägg till från galleriet** i bladet **Lägg till ett program** . Med de här ändringarna kan du enkelt hitta dina appar som stöder automatisk etablering, OpenID Connect, Security Assertion Markup Language (SAML) och inloggning med lösen ord (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plan för ändring: borttagning av MFA-serverns IP-adress från Office 365-IP-adressen

**Typ:** Planera för ändring  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Vi tar bort MFA-serverns IP-adress från [Office 365 IP-adress och URL-webbtjänst](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Om du för närvarande använder dessa sidor för att uppdatera brand Väggs inställningarna måste du se till att du även inkluderar listan över IP-adresser som beskrivs i avsnittet **krav för azure Multi-Factor Authentication-Server brand vägg** i artikeln [komma igång med Azure Multi-Factor Authentication-Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Endast app-token kräver att klient programmet finns i resurs klienten

**Typ:** Fastsatt  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Den 26 juli 2019 ändrade vi hur vi tillhandahåller app-only-token via [tilldelningen av klientens autentiseringsuppgifter](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Tidigare kunde appar Hämta token för att anropa andra appar, oavsett om klient programmet fanns i klienten. Vi har uppdaterat det här beteendet så att resurser med en enda klient, ibland kallade webb-API: er, bara kan anropas av klient program som finns i resurs klienten.

Om din app inte finns i resurs klienten får du ett fel meddelande om att `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` åtgärda det här problemet genom att skapa ett huvud namn för klient program tjänsten i klienten, antingen med hjälp av [Administratörs medgivande slut punkten](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) eller [via PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), som säkerställer att klienten har gett appen behörighet att köras i klienten.

Mer information finns i [Vad är nytt för autentisering?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Det befintliga godkännandet mellan klienten och API: et fortsätter inte att krävas. Apparna bör fortfarande utföra sina egna verifierings kontroller.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nytt lösen ords lös inloggning till Azure AD med hjälp av FIDO2-säkerhetsnycklar

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Azure AD-kunder kan nu ange principer för att hantera FIDO2 säkerhets nycklar för deras organisations användare och grupper. Slutanvändare kan även registrera sina säkerhets nycklar genom att använda nycklar för att logga in på sina Microsoft-konton på webbplatser på FIDO enheter, samt logga in på sina Azure AD-anslutna Windows 10-enheter.

Mer information finns i [Aktivera lösen ords lös inloggning för Azure AD (för hands version)](/azure/active-directory/authentication/concept-authentication-passwordless) för administratörs information och [Konfigurera säkerhets information för att använda en säkerhets nyckel (för hands version)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) för information om slutanvändare.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – juli 2019

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I juli 2019 har vi lagt till dessa 18 nya appar med stöd för federation i app-galleriet:

[Ungerboeck program vara](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [starkt mönster Omnichannel kontakt Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [smarta Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [LOOOP](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO-åtkomst för Ethidex Compliance Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [djupet](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [FlipSnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [,](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial)PharmID [WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog-artefaktning](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användar konto etablering för de här nyligen SaaS apparna som stöds

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** Övervaka & rapportering

Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federerad katalog](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Ny Azure AD Domain Services service tag för nätverks säkerhets grupp

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services

Om du är trött på att hantera långa listor över IP-adresser och intervall kan du använda den nya **AzureActiveDirectoryDomainServices** Network Service tag i din Azure-nätverks säkerhets grupp för att skydda inkommande trafik till ditt Azure AD Domain Services virtuella nätverk under nätet.

Mer information om den här nya service tag-koden finns i [nätverks säkerhets grupper för Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nya säkerhets granskningar för Azure AD Domain Services (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services

Vi är glada över att kunna meddela att Azure AD Domain Service-säkerhetsgranskningen är offentlig för hands version. Med säkerhets granskning får du viktiga insikter om dina Authentication Services genom att strömma säkerhets gransknings händelser till riktade resurser, inklusive Azure Storage, Azure Log Analytics-arbetsytor och Azure Event Hub med Azure AD Domain Service Portal.

Mer information finns i [Aktivera säkerhets granskningar för Azure AD Domain Services (för hands version)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nya autentiseringsmetoder användning & insikter (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Lösen ords återställning via självbetjäning  
**Produkt kapacitet:** Övervaka & rapportering

Nya autentiseringsmetoder som används & Insights-rapporter kan hjälpa dig att förstå hur funktioner som Azure Multi-Factor Authentication och återställning av lösen ord för självbetjäning registreras och används i din organisation, inklusive antalet registrerade användare för varje funktion, hur ofta lösen ords återställning via självbetjäning används för att återställa lösen ord och med vilken metod som återställningen sker.

Mer information finns i [användning av autentiseringsmetoder & insikter (för hands version)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nya säkerhets rapporter är tillgängliga för alla Azure AD-administratörer (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Alla Azure AD-administratörer kan nu välja banderollen överst i befintliga säkerhets rapporter, till exempel användare som har **flaggats för risk** rapport, för att börja använda den nya säkerhets upplevelsen som visas i rapporterna **riskfyllda användare** och **riskfyllda inloggningar** . Med tiden kommer alla säkerhets rapporter att flyttas från äldre versioner till de nya versionerna, med de nya rapporterna som ger dig följande ytterligare funktioner:

- Avancerad filtrering och sortering

- Mass åtgärder, t. ex. problem med att ignorera användar risk

- Bekräftelse av komprometterade eller säkrade entiteter

- Risk tillstånd, täcker: vid risk, avstängd, åtgärdad och bekräftat komprometterad

Mer information finns i rapporten om [riskfyllda användare](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) och [riskfyllda inloggningar](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nya säkerhets granskningar för Azure AD Domain Services (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services

Vi är glada över att kunna meddela att Azure AD Domain Service-säkerhetsgranskningen är offentlig för hands version. Med säkerhets granskning får du viktiga insikter om dina Authentication Services genom att strömma säkerhets gransknings händelser till riktade resurser, inklusive Azure Storage, Azure Log Analytics-arbetsytor och Azure Event Hub med Azure AD Domain Service Portal.

Mer information finns i [Aktivera säkerhets granskningar för Azure AD Domain Services (för hands version)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Ny B2B direkt Federation med SAML/WS-utfodras (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C

Direkt federationen hjälper till att göra det enklare för dig att arbeta med partner vars IT-hanterade identitets lösning inte är Azure AD, genom att arbeta med identitets system som stöder SAML-eller WS-utfodras-standarder. När du har konfigurerat en direkt Federations relation med en partner, kan alla nya gäst användare som du bjuder in från domänen samar beta med dig som använder sitt befintliga organisations konto, vilket gör användar upplevelsen mer sömlös.

Mer information finns i [direkt Federation med AD FS och tredje parts leverantörer för gäst användare (för hands version)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användar konto etablering för de här nyligen SaaS apparna som stöds

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** Övervaka & rapportering

Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federerad katalog](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Ny kontroll för dubbletter av grupp namn i Azure AD-portalen

**Typ:** Ny funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete

Nu när du skapar eller uppdaterar ett grupp namn från Azure AD-portalen, kontrollerar vi om du duplicerar ett befintligt grupp namn i din resurs. Om vi bestämmer att namnet redan används av en annan grupp, blir du ombedd att ändra ditt namn.

Mer information finns i [hantera grupper i Azure AD-portalen](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD stöder nu statiska frågeparametrar i svar (omdirigerings-URI: er)

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Azure AD-appar kan nu registrera och använda URI: er för svar (omdirigering) med statiska frågeparametrar (till exempel `https://contoso.com/oauth2?idp=microsoft`) för OAuth 2,0-begäranden. Den statiska Frågeparametern omfattas av sträng matchning för svars-URI: er, precis som andra delar av svars-URI: n. Om det inte finns någon registrerad sträng som matchar URL-kodad omdirigerings-URI, avvisas begäran. Om svars-URI: n hittas används hela strängen för att omdirigera användaren, inklusive parametern för den statiska frågan.

Dynamiska svars-URI: er är fortfarande förbjudna eftersom de utgör en säkerhets risk och inte kan användas för att bevara tillståndsinformation i en autentiseringsbegäran. För det här ändamålet använder du parametern `state`.

För närvarande blockerar appens registrerings skärmar för Azure Portal fortfarande frågeparametrar. Du kan dock redigera appens manifest manuellt för att lägga till och testa frågeparametrar i din app. Mer information finns i [Vad är nytt för autentisering?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Aktivitets loggar (MS Graph API: er) för Azure AD är nu tillgängliga via PowerShell-cmdletar

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Vi är glada över att kunna meddela att Azure AD aktivitets loggar (gransknings-och inloggnings rapporter) nu är tillgängliga via Azure AD PowerShell-modulen. Tidigare kunde du skapa egna skript med MS Graph API-slutpunkter och nu har vi utökat den möjligheten till PowerShell-cmdletar.

Mer information om hur du använder dessa cmdlets finns i [Azure AD PowerShell-cmdlets för rapportering](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Uppdaterade filter kontroller för gransknings-och inloggnings loggar i Azure AD

**Typ:** Ändrad funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Vi har uppdaterat rapporterna för gransknings-och inloggnings loggar så att du nu kan använda olika filter utan att behöva lägga till dem som kolumner i rapport skärmarna. Dessutom kan du bestämma hur många filter som ska visas på skärmen. De här uppdateringarna fungerar tillsammans för att göra dina rapporter lättare att läsa och mer begränsade till dina behov.

Mer information om de här uppdateringarna finns i [filtrera gransknings loggar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) och [filtrera inloggnings aktiviteter](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Juni 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>New riskDetections-API för Microsoft Graph (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Vi är glada över att kunna presentera den nya riskDetections-API: n för Microsoft Graph nu i offentlig för hands version. Du kan använda den här nya API: n för att visa en lista över organisationens identitet skydd – relaterad användare och inloggnings risk identifieringar. Du kan också använda det här API: et för att effektivt fråga dina risk identifieringar, inklusive information om identifierings typ, status, nivå och mycket annat.

Mer information finns i [referens dokumentationen för riskhanterings-API](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – juni 2019

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I juni 2019 har vi lagt till dessa 22 nya appar med stöd för federation i app-galleriet:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN-klient](https://portal.azure.com/), [kostnadin](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [hjälp program hjälp](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz i bilen Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure-konsolen](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML-autentisering](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager för Oracle Inköps handel, Oracle Access Manager för Oracle E-Business Suite, Oracle-IDCS för E-Business Suite, Oracle-IDCS för, Oracle IDCS för JD Edwards

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användar konto etablering för de här nyligen SaaS apparna som stöds

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** Övervaka & rapportering

Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Förhindra](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Mottagare](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Visa real tids förloppet för Azure AD Provisioning-tjänsten

**Typ:** Ändrad funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel

Vi har uppdaterat Azure ADs etablerings miljö för att inkludera en ny förlopps indikator som visar hur långt du befinner dig i användar etablerings processen. Den här uppdaterade upplevelsen ger också information om antalet användare som etablerats under den aktuella cykeln, samt hur många användare som har etablerats till dagens datum.

Mer information finns i [kontrol lera status för användar etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Företags anpassning visas nu på skärmen Logga ut och fel

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Vi har uppdaterat Azure AD så att ditt företags varumärke nu visas på skärmen Logga ut och fel och på inloggnings sidan. Du behöver inte göra något för att aktivera den här funktionen, Azure AD använder bara de till gångar som du redan har konfigurerat i **företags anpassnings** delen av Azure Portal.

Mer information om hur du konfigurerar din företags anpassning finns i [lägga till anpassning till din organisations Azure Active Directory sidor](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure Multi-Factor Authentication-servern (MFA) är inte längre tillgänglig för nya distributioner

**Typ:** Föråldrad  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication i organisationen måste nu använda molnbaserad Azure-Multi-Factor Authentication. Kunder som aktiverade MFA Server tidigare än 1 juli ser ingen ändring. Du kommer fortfarande att kunna ladda ned den senaste versionen, Hämta framtida uppdateringar och generera autentiseringsuppgifter för aktivering.

Mer information finns i [komma igång med Azure Multi-Factor Authentication-Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Mer information om molnbaserad Azure-Multi-Factor Authentication finns i [Planera en molnbaserad Azure-Multi-Factor Authentication distribution](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Maj 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Tjänst förändring: framtida stöd för endast TLS 1,2-protokoll i Application Proxy-tjänsten

**Typ:** Planera för ändring  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control

För att tillhandahålla bästa möjliga kryptering för våra kunder begränsar vi åtkomsten till endast TLS 1,2-protokoll på Application Proxy-tjänsten. Den här ändringen distribueras gradvis till kunder som redan använder TLS 1,2-protokoll, så du bör inte se några ändringar.

Utfasningen av TLS 1,0 och TLS 1,1 sker den 31 augusti 2019, men vi ger ytterligare Avancerat meddelande så att du har tid att förbereda dig för den här ändringen. För att förbereda för den här ändringen kontrollerar du att kombinationen av klient-och webb läsar Server, inklusive alla klienter som användarna använder för att komma åt appar som publicerats via programproxy, har uppdaterats för att använda TLS 1,2-protokollet för att upprätthålla anslutningen till programmet Proxy service. Mer information finns i [lägga till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Använd rapporten användning och insikter för att visa dina app-relaterade inloggnings data

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** Övervaka & rapportering

Nu kan du använda rapporten användning och insikter som finns i avsnittet **företags program** i Azure Portal för att få en programinriktad vy över dina inloggnings data, inklusive information om:

- Mest använda appar för din organisation

- Appar med de mest misslyckade inloggnings programmen

- Vanligaste inloggnings fel för varje app

Mer information om den här funktionen finns i [användnings-och insikts rapport i Azure Active Directory Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatisera din användar etablering för molnappar med Azure AD

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** Övervaka & rapportering

Följ de här nya självstudierna för att använda Azure AD Provisioning-tjänsten för att automatisera skapande, borttagning och uppdatering av användar konton för följande molnbaserade appar:

- [Samstämm](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Du kan också följa den här nya [guiden Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), som innehåller information om hur du etablerar grupp objekt.

Mer information om hur du bättre skyddar din organisation genom att tillhandahålla automatiserade användar konton finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Identitets säkra poäng är nu tillgängligt i Azure AD (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänste kategori:** EJ TILLÄMPLIGT  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Nu kan du övervaka och förbättra din position för identiteter genom att använda funktionen identitet säkra poäng i Azure AD. I funktionen för identitetens säkra poäng används en enda instrument panel för att hjälpa dig:

- Objektivt mäta din position för identiteter baserat på en poäng på mellan 1 och 223.

- Planera för förbättringar av din identitets säkerhet

- Granska lyckade säkerhets förbättringar

Mer information om funktionen för identitets säkerhets Poäng finns [i vad är identitetens säkra poäng i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Ny Appregistreringar upplevelse är nu tillgänglig (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Utvecklings miljö

Den nya [Appregistreringar](https://aka.ms/appregistrations) upplevelsen är nu allmänt tillgänglig. Den här nya upplevelsen innehåller alla viktiga funktioner som du är van vid från Azure Portal och program registrerings portalen och som ökar genom att:

- **Bättre hantering av appar.** I stället för att se dina appar över olika portaler kan du nu se alla dina appar på en plats.

- **Förenklad registrering av appar.** Från den förbättrade navigerings upplevelsen av förbättringar-behörigheten är det nu enklare att registrera och hantera dina appar.

- **Mer detaljerad information.** Du hittar mer information om din app, inklusive snabb starts guider med mera.

Mer information finns i [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/) och [Appregistreringar Experience är nu allmänt tillgänglig!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blogg meddelande.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nya funktioner som är tillgängliga i API för riskfyllda användare för identitets skydd

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Vi är glada över att kunna meddela att du nu kan använda API: erna för riskfyllda användare för att hämta användares risk historik, ignorera riskfyllda användare och bekräfta användare som komprometterade. Den här ändringen hjälper dig att effektivt uppdatera risk statusen för dina användare och förstå deras risk historik.

Mer information finns i [referens dokumentationen för riskfyllda användare](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – maj 2019

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I maj 2019 har vi lagt till dessa 21 nya appar med stöd för federation i app-galleriet:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [riktiga länkar](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [displayer](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales-engagerande](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial),- [system](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [META4 global timme](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobolt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API-moln](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [kontroll](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [näring](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Förbättrade grupper för att skapa och hantera funktioner i Azure AD-portalen

**Typ:** Ny funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete

Vi har gjort förbättringar i de grupprelaterade upplevelserna i Azure AD-portalen. Dessa förbättringar gör det möjligt för administratörer att hantera grupper listor, medlems listor och ge ytterligare skapande alternativ.

Förbättringarna innefattar:

- Grundläggande filtrering efter medlemskaps typ och grupptyp.

- Tillägg av nya kolumner, t. ex. källa och e-postadress.

- Möjlighet att välja flera grupper, medlemmar och ägar listor för enkel borttagning.

- Möjlighet att välja en e-postadress och lägga till ägare när gruppen skapas.

Mer information finns i [skapa en grundläggande grupp och lägga till medlemmar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurera en namngivnings princip för Office 365-grupper i Azure AD portal (allmän tillgänglighet)

**Typ:** Ändrad funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete

Administratörer kan nu konfigurera en namngivnings princip för Office 365-grupper med hjälp av Azure AD-portalen. Den här ändringen hjälper till att upprätthålla konsekventa namngivnings konventioner för Office 365-grupper som skapats eller redigerats av användare i din organisation.

Du kan konfigurera namngivnings principen för Office 365-grupper på två olika sätt:

- Definiera prefix eller suffix som läggs till automatiskt i ett grupp namn.

- Ladda upp en anpassad uppsättning blockerade ord för din organisation, vilket inte är tillåtet i grupp namn (till exempel "VD, löner, HR").

Mer information finns i [framtvinga en namngivnings princip för Office 365-grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API-slutpunkter är nu tillgängliga för Azure AD-aktivitets loggar (allmän tillgänglighet)

**Typ:** Ändrad funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Vi är glada över att kunna meddela allmän tillgänglighet för stöd för Microsoft Graph API-slutpunkter för Azure AD-aktivitets loggar. I den här versionen kan du nu använda version 1,0 av både gransknings loggarna i Azure AD och inloggnings loggarna.

Mer information finns i [Översikt över Azure AD audit log API](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Administratörer kan nu använda villkorlig åtkomst för den kombinerade registrerings processen (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & Skydd för identitets säkerhet  

Administratörer kan nu skapa principer för villkorlig åtkomst för användning av den kombinerade registrerings sidan. Detta omfattar att tillämpa principer för att tillåta registrering om:

- Användare finns i ett betrott nätverk.

- Användare är en låg inloggnings risk.

- Användare finns på en hanterad enhet.

- Användarna godkänner organisationens användnings villkor (TOU).

Om du vill ha mer information om villkorlig åtkomst och återställning av lösen ord kan du se [blogg inlägget för den villkorliga åtkomsten i blogg inlägget för registrering i Azure AD kombinerat MFA och lösen ords återställning](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Mer information om principer för villkorlig åtkomst för den kombinerade registrerings processen finns i [principer för villkorlig åtkomst för kombinerad registrering](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Mer information om funktionen användnings villkor för Azure AD finns i Azure Active Directory användnings [villkor](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>April 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Ny identifiering av Azure AD Threat Intelligence är nu tillgängligt som en del av Azure AD Identity Protection

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD Identity Protection  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Identifiering av Azure AD Threat Intelligence är nu tillgängligt som en del av den uppdaterade Azure AD Identity Protections funktionen. Med den här nya funktionen kan du ange ovanliga användar aktiviteter för en viss användare eller aktivitet som är konsekvent med kända angrepps mönster baserade på Microsofts interna och externa hot informations källor.

Mer information om den uppdaterade versionen av Azure AD Identity Protection finns i de [fyra viktiga Azure AD Identity Protection förbättringarna finns nu i den offentliga för hands](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) versionen av bloggen och [vad som är Azure Active Directory Identity Protection (uppdaterat)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) artiklar. Mer information om identifiering av Azure AD Threat Intelligence finns i artikeln [Azure Active Directory Identity Protection risk identifieringar](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks) .

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

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Bänkning](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), PageDNA, [EduBrite](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial) [LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [spetsig Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (rollbaserad SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent egendoms hantering](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial) [, SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [INDIGGO](https://indiggolead.com/)

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

Azure AD Connect håller på att överföra våra e-postvarnings system, vilket potentiellt visar vissa kunder en ny e-postsändare. För att lösa detta måste du lägga till `azure-noreply@microsoft.com` i din organisations lista över tillåtna eller så kan du inte fortsätta att ta emot viktiga aviseringar från Office 365, Azure eller dina Sync-tjänster.

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

Mer information om hur du konfigurerar och hanterar enheter och appar med hjälp av villkorlig åtkomst finns i [Kräv hanterade enheter för Cloud app-åtkomst med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) och [Kräv godkända klient program för Cloud app-åtkomst med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Mer information om hur du hanterar åtkomst med hjälp av Microsoft Edge med Microsoft Intune-principer finns i [Hantera Internet åtkomst med en Microsoft Intune-skyddad webbläsare](https://docs.microsoft.com/intune/app-configuration-managed-browser).

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

[ISEC7 Mobile Exchange delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [förklarings-baserade gransknings system](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool prestanda](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw, Confirmit](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial)- [horisonter](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [aktivitet](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

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

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [fat-finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [flygstack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial) [, permission](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial)klickning, Brandfolder [, StoregateSmartFile, Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial) [, Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial) [, seismisk](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [dela en dröm](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial) [Knowledge Var LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope-data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [NetOp-portalen](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud av gener](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp produktivitets plattform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

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

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [personal-palett](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco paraply](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet åtkomst administratör](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [förfallo påminnelse](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CORPTAX](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [fungerande](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNEXUS SSO-system](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ar för företag](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 för Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), HighGear [,](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [korn färj Alp](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), Adoddle [cSaas-plattform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nya Azure AD Identity Protection förbättringar (offentlig för hands version)

**Typ:** Ändrad funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Vi är glada över att kunna meddela att vi har lagt till följande förbättringar i Azure AD Identity Protection Public Preview-erbjudandet, inklusive:

- Ett uppdaterat och mer integrerat användar gränssnitt

- Ytterligare API: er

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

## <a name="novemberdecember-2018"></a>November/December 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Användare som tas bort från omfånget växlar inte längre till enbart moln konton

**Typ:** Fastsatt  
**Tjänste kategori:** Användar hantering  
**Produkt kapacitet:** Katalogen

>[!Important]
>Vi har hört och förstår din hjälp på grund av den här korrigeringen. Vi har därför återställt den här ändringen tills vi kan göra det lättare för dig att implementera i din organisation.

Vi har åtgärdat ett fel där en användares DirSyncEnabled flagga skulle felaktigt växlas till **false** när Active Directory Domain Services (AD DS)-objektet uteslöts från omfånget och sedan flyttades till pappers korgen i Azure AD på följande synkroniseringstid. Till följd av denna korrigering, om användaren undantas från omfånget för synkronisering och sedan återställs från Azure AD-pappers korgen, är användar kontot kvar som synkroniserat från den lokala AD-platsen som förväntat och kan inte hanteras i molnet eftersom dess auktoritets källa (SoA) förblir som lokal AD.

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
|Resultat|Visar resultatet av gransknings loggarna. Tidigare räknades detta upp, men nu visar vi det faktiska värdet.|<ul><li>0</li><li>1</li></ul>|<ul><li>Klart</li><li>Fel</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Ändrade fält i inloggnings schema
Följande fält ändras i inloggnings schema:

|Fältnamn|Vad som ändrats|Gamla värden|Nya värden|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Detta var fältet **conditionalaccessPolicies** . Nu är fältet **appliedConditionalAccessPolicies** .|Ingen ändring|Ingen ändring|
|conditionalAccessStatus|Visar resultatet av den villkorliga åtkomst princip statusen vid inloggning. Tidigare räknades detta upp, men nu visar vi det faktiska värdet.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Klart</li><li>Fel</li><li>Används inte</li><li>Inaktiverad</li></ul>|
|appliedConditionalAccessPolicies: resultat|Visar resultatet av en enskild princip status för villkorlig åtkomst vid inloggning. Tidigare räknades detta upp, men nu visar vi det faktiska värdet.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Klart</li><li>Fel</li><li>Används inte</li><li>Inaktiverad</li></ul>|

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

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [gra-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [oändlig Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [drivgarn](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [EverBridge medlems Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps – klassiskt test ](https://test.plexonline.com/signon), [Plex Apps – klassiska](https://www.plexonline.com/signon), [Plex Apps – UX-test](https://test.cloud.plex.com/sso), [Plex-appar – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/), [hantverk-Childcare poster, närvaro, & Financial tracking system](https://getcrafts.ca/craftsregistration) 

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Oktober 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD-loggar nu arbeta med Azure Log Analytics (förhandsversion)

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Vi är glada att kunna meddela att du nu kan vidarebefordra dina loggar med Azure AD till Azure Log Analytics! Den här funktionen för de mest efterfrågade hjälper ger dig ännu bättre åtkomst till analytics för ditt företag, åtgärder, och säkerhet samt ett sätt att övervaka din infrastruktur. Mer information finns i [Azure Active Directory aktivitets loggar i Azure Log Analytics nu tillgänglig](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blogg.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nya federerade appar är tillgängliga i appgalleriet för Azure AD - oktober 2018

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I oktober 2018, har vi lagt till dessa 14 nya appar med stöd för app-galleriet:

[Mina belönings punkter](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [låna](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), dialpad, ON24 [virtuell miljö](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler tre](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [bedömning](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), Workspot- [kontroll](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services e-postaviseringar

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services

Azure AD Domain Services ger aviseringar på Azure portal om felkonfigurationer eller problem med din hanterade domän. Dessa aviseringar innehåller stegvisa guider så att du kan försöka att åtgärda problem utan att behöva kontakta supporten.

Från och med oktober, du kommer att kunna anpassa inställningar för meddelanden för din hanterade domän så att när nya aviseringar sker, skickas ett e-postmeddelande till en utvald grupp personer, vilket eliminerar behovet av att kontinuerligt Kontrollera portal efter uppdateringar.

Mer information finns i [meddelande inställningar i Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD-portalen stöder användning av ForceDelete domänen API att ta bort anpassade domäner 

**Typ:** Ändrad funktion  
**Tjänste kategori:** Katalog hantering  
**Produkt kapacitet:** Katalogen

Vi har glädjen att meddela att du kan nu använda ForceDelete domänen API att ta bort dina egna domännamn namnändringarna asynkront referenser, som användare, grupper och appar från ditt eget domännamn (contoso.com) tillbaka till den initiala domänen namn ( Contoso.onmicrosoft.com).

Den här ändringen hjälper dig att ta bort dina egna domännamn snabbare om din organisation inte längre använder namnet, eller om du vill använda domännamnet med en annan Azure AD.

Mer information finns i [ta bort ett anpassat domän namn](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>September 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Uppdaterade rolladministratörsbehörigheter för dynamiska grupper

**Typ:** Fastsatt  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete

Vi har ett problem har åtgärdats så att specifika administratörsroller kan nu skapa och uppdatera regler för dynamiskt medlemskap, utan att behöva vara ägare av gruppen.

Rollerna är:

- Global administratör

- Intune-administratör

- Användar administratör

Mer information finns i [skapa en dynamisk grupp och kontrol lera status](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Konfigurationsinställningar för förenklad enkel inloggning (SSO) för vissa tredjepartsappar

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Vi förstår att ställa in enkel inloggning (SSO) för programvara som en tjänst (SaaS)-appar kan vara svårt på grund av den unika natur varje apps-konfiguration. Vi har skapat en förenklad konfigurationsupplevelse för att automatiskt fylla SSO-konfigurationsinställningarna för följande tredje parts SaaS-appar:

- Zendesk

- ArcGis Online

- Jamf Pro

Börja använda den här funktionen genom att gå till **konfigurations** sidan för **Azure Portal** > SSO för appen. Mer information finns i [SaaS Application Integration with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – var finns dina data? sidan

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** GoLocal

Välj ditt företags region från **Azure Active Directory – var finns din data** sida för att se vilka Azure-datacenter som rör dina Azure AD-data i vila för alla Azure AD-tjänster. Du kan filtrera informationen efter specifika Azure AD-tjänster för ditt företags region.

För att få åtkomst till den här funktionen och mer information, se [Azure Active Directory – var finns dina data](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>En ny distributionsplan är tillgänglig för panelen Min appåtkomst

**Typ:** Ny funktion  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** DEFINITION

Kolla in den nya distributions plan som är tillgänglig för åtkomst panelen för Mina appar (https://aka.ms/deploymentplans).
Mina appar åtkomstpanelen ger användare en och samma plats för att hitta och komma åt sina appar. Portalen innehåller också användare med självbetjäning möjligheter som begär åtkomst till appar och grupper, eller hantera åtkomst till dessa resurser å andras vägnar.

Mer information finns i [Vad är min Apps-portalen?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>En ny flik för felsökning och support på sidan Inloggningsloggar i Azure Portal

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Fliken ny **fel sökning och support** på sidan **inloggningar** i Azure Portal är avsedd att hjälpa administratörer och support tekniker att felsöka problem som rör Azure AD-inloggningar. Den nya fliken innehåller felkod, fel meddelande och reparations rekommendationer (om sådana finns) för att hjälpa till att lösa problemet. Om du inte kan lösa problemet ger vi också ett nytt sätt att skapa ett support ärende med hjälp av funktionen **Kopiera till Urklipp** , som fyller i fälten för **begäran-ID** och **datum (UTC)** för logg filen i ditt support ärende.  

![Inloggnings loggar som visar den nya fliken](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Förbättrad support för anpassade tilläggsegenskaper som används för att skapa dynamiska medlemskapsregler

**Typ:** Ändrad funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete

Med den här uppdateringen kan du nu Klicka på länken **Hämta anpassade tilläggs egenskaper** från regel verktyget för dynamisk användar grupp, ange ditt unika app-ID och ta emot den fullständiga listan med anpassade tilläggs egenskaper som ska användas när du skapar en regel för dynamiskt medlemskap för användare. Den här listan kan också uppdateras för att hämta alla nya anpassade tilläggsegenskaper för appen.

Mer information om hur du använder anpassade tilläggs egenskaper för dynamiska medlemskaps regler finns i [tilläggs egenskaper och anpassade tilläggs egenskaper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klient program för Azure AD App-baserad villkorlig åtkomst

**Typ:** Planera för ändring  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** Identitets säkerhet och skydd

Följande appar finns på listan över [godkända klient program](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Microsoft To-Do

- Microsoft Stream

Mer information finns i:

- [Azure AD App-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Ny support återställning av lösenord från låsskärmen i Windows 7/8/8.1

**Typ:** Ny funktion  
**Tjänste kategori:** SSPR  
**Produkt kapacitet:** Användarautentisering

När du har konfigurerat den här nya funktionen visas en länk för att återställa lösen ordet från **Lås** skärmen på en enhet som kör Windows 7, Windows 8 eller Windows 8,1. Genom att klicka på länken, leds användaren genom samma flöde för återställning av lösenord som via en webbläsare.

Mer information finns i [så här aktiverar du återställning av lösen ord från Windows 7, 8 och 8,1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Ändringsmeddelande: Auktoriseringskoder kommer inte längre att vara tillgängliga för återanvändning 

**Typ:** Planera för ändring  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Från och med 15 November 2018, att Azure AD sluta acceptera tidigare autentisering-koder för appar. Ändringen security hjälper dig att ta med Azure AD i enlighet med OAuth-specifikationen och tillämpas på både v1 och v2-slutpunkter.

Om din app återanvänder auktoriseringskoder för att hämta token för flera resurser, rekommenderar vi att du använda koden för att få en uppdateringstoken och sedan använda den uppdateringstoken för att hämta ytterligare token för andra resurser. Auktoriseringskoder kan bara användas en gång, men uppdateringstoken kan användas flera gånger i flera resurser. En app som försöker att återanvända en Autentiseringskod under flödet för OAuth-kod får ett invalid_grant-fel.

För detta och andra protokoll-relaterade ändringar, se [den fullständiga listan över vad som är nytt för autentisering](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nya federerade appar finns tillgängliga i Microsoft Azure Active Directory-appgalleriet – september 2018

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I September 2018 har vi lagt till stöd för dessa 16 nya appar med Federation i app-galleriet:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Möt rekryterings program vara](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [snö](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), Join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback, Riverbed](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial)Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO för Azure, SurveyMonkey, [sammankalla](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Support för ytterligare anspråksomvandlingsmetoder

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Vi har introducerat nya omvandlings metoder för anspråk, ToLower () och ToUpper () som kan tillämpas på SAML-tokens från konfigurations sidan för SAML-baserad **enkel inloggning** .

Mer information finns i [så här anpassar du anspråk som utfärdats i SAML-token för företags program i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Uppdaterat användargränssnitt för SAML-baserad appkonfiguration (förhandsversion)

**Typ:** Ändrad funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Som en del av vår uppdaterade SAML-baserad konfiguration av Användargränssnittet får du:

- En uppdaterad genomgång för att konfigurera din SAML-baserade appar.

- Bättre synbarhet om vad som är saknas eller är felaktig i konfigurationen.

- Möjligheten att lägga till flera e-postadresser för meddelande om utgången certifikat.

- Nya metoder för omvandling av anspråk, ToLower() och ToUpper() med mera.

- Ett sätt att överföra din egen token signeringscertifikatet för dina företagsappar.

- Ett sätt att ange NameID-Format för SAML-appar och ett sätt att ange NameID-värde som Katalogtillägg.

Om du vill aktivera den här uppdaterade vyn klickar du på länken **prova vår nya upplevelse** överst på sidan för **enkel inloggning** . Mer information finns i [Självstudier: Konfigurera SAML-baserad enkel inloggning för ett program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).

---

## <a name="august-2018"></a>Augusti 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Ändringar i Azure Active Directory IP-adressintervall

**Typ:** Planera för ändring  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Systemet

Vi presenterar större IP-adressintervall till Azure AD, vilket innebär att om du har konfigurerat Azure AD-IP-adressintervall för brandväggar, routrar eller Nätverkssäkerhetsgrupperna, måste du uppdatera dem. Vi gör den här uppdateringen så att du inte behöver ändra din brandvägg eller router Network Security grupper IP-konfigurationer för intervallet igen när Azure AD lägger till nya slutpunkter. 

Nätverkstrafik flyttas till dessa nya områden under de kommande två månaderna. Om du vill fortsätta med undvika avbrott i tjänsten, måste du lägga till de här uppdaterade värden till IP-adresser innan den 10 September 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

Vi rekommenderar starkt att inte ta bort de gamla IP-adressintervall tills all din nätverkstrafik har flyttats till de nya intervall. För uppdateringar om flytten och för att lära dig när du kan ta bort de gamla intervallen, se [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Ändringsmeddelande: Auktoriseringskoder kommer inte längre att vara tillgängliga för återanvändning 

**Typ:** Planera för ändring  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Från och med 15 November 2018, att Azure AD sluta acceptera tidigare autentisering-koder för appar. Ändringen security hjälper dig att ta med Azure AD i enlighet med OAuth-specifikationen och tillämpas på både v1 och v2-slutpunkter.

Om din app återanvänder auktoriseringskoder för att hämta token för flera resurser, rekommenderar vi att du använda koden för att få en uppdateringstoken och sedan använda den uppdateringstoken för att hämta ytterligare token för andra resurser. Auktoriseringskoder kan bara användas en gång, men uppdateringstoken kan användas flera gånger i flera resurser. En app som försöker att återanvända en Autentiseringskod under flödet för OAuth-kod får ett invalid_grant-fel.

För detta och andra protokoll-relaterade ändringar, se [den fullständiga listan över vad som är nytt för autentisering](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Hantering av konvergerad säkerhetsinformation för återställning av självbetjäningslösenord och Multi-Factor Authentication (MFA)

**Typ:** Ny funktion  
**Tjänste kategori:** SSPR  
**Produkt kapacitet:** Användarautentisering
 
Den här nya funktionen hjälper människor att hantera deras säkerhetsinformation (till exempel, telefonnummer, mobilapp och så vidare) för SSPR och MFA i en enda plats och erfarenheter. jämfört med tidigare, där den har utförts på två olika platser.

Den här konvergerade upplevelsen fungerar även för personer som använder SSPR eller MFA. Även om organisationen inte framtvinga MFA eller SSPR-registrering, kan personer fortfarande registrera MFA eller SSPR info säkerhetsmetoder tillåts av din organisation från portalen Mina appar.

Det här är en valbar offentlig förhandsversion. Administratörer kan aktivera den nya upplevelsen (om det behövs) för en vald grupp eller för alla användare i en klient. Mer information om konvergerad upplevelse finns i [bloggen för konvergerad upplevelse](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nya HTTP-Only-cookies i Microsoft Azure Active Directory-programproxyappar

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control

Det finns en ny inställning som kallas **http-Only cookies** i dina Application Proxy-appar. Den här inställningen ger extra säkerhet, inklusive flaggan HTTPOnly i HTTP-Svarsrubrik för båda Application Proxy åtkomst- och sessionsprinciper cookies, Stopp åtkomst till cookien från ett skript på klientsidan och förhindrar ytterligare åtgärder som att kopiera eller Ändra cookien. Även om den här flaggan inte har använts tidigare, cookies har alltid varit krypterat och överförs med hjälp av en SSL-anslutning för att skydda mot felaktig ändringar.

Den här inställningen är inte kompatibelt med appar som använder ActiveX-kontroller, till exempel Remote Desktop. Om du är i det här fallet rekommenderar vi att du stänger av den här inställningen.

Mer information om inställningen HTTP-Only cookies finns i [Publicera program med hjälp av Azure AD-programproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) för Azure-resurser stöder hanteringsgruppsresurstyper

**Typ:** Ny funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
Inställningar för aktivering och tilldelning av just-In-Time kan nu användas Hanteringsgruppen resurstyper, precis som du redan har gjort för prenumerationer, resursgrupper och resurser (till exempel virtuella datorer, Apptjänster med mera). Vem som helst med en roll med administratörsåtkomst för en Hanteringsgrupp kan dessutom identifiera och hantera resursen i PIM.

Mer information om PIM och Azure-resurser finns i [identifiera och hantera Azure-resurser med hjälp av Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Programåtkomst (förhandsversion) ger snabbare åtkomst till Microsoft Azure Active Directory-portalen

**Typ:** Ny funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
När du aktiverar en roll med hjälp av PIM idag, kan det ta över 10 minuter för behörigheterna som ska börja gälla. Om du väljer att använda programåtkomst, som finns för närvarande i förhandsversion, Administratörer kan komma åt Azure AD-portalen när aktiveringsbegäran har slutförts.

Programåtkomst stöder för närvarande bara Azure AD-portalen och Azure-resurser. Mer information om PIM och program åtkomst finns i [Vad är Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nya federerade appar finns tillgängliga i appgalleriet för Microsoft Azure Active Directory – augusti 2018

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I augusti 2018 har vi lagt till dessa 16 nya appar med stöd för app-galleriet:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [bridgeline-obundna](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [recept Labs – mobil-och Webbtestering](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [sätt vi gör](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [Promaster (av Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentation](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F – utgifts rapporter](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Inbyggd Tableau-support finns nu i Microsoft Azure Active Directory-programproxy

**Typ:** Ändrad funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control

Med vår uppdateringen från OpenID Connect beviljande via OAuth 2.0-protokollet för vår förautentisering-protokollet kan behöver du inte längre någon ytterligare konfiguration för att använda Tableau med Application Proxy. Den här ändringen av protokollet hjälper också till Application Proxy bättre stöd för mer moderna appar med hjälp av endast HTTP-omdirigeringar, som ofta stöds i JavaScript och HTML-taggar.

Mer information om vårt inbyggda stöd för Tableau finns i [Azure AD-programproxy nu med inbyggt stöd för Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Ny support för att lägga till Google som en identitetsprovider för B2B-gästanvändare i Azure Active Directory (förhandsversion)

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C

Genom att konfigurera federation med Google i din organisation, kan du låta inbjudna Gmail användarna loggar in din delade appar och resurser med hjälp av sina befintliga Google-konto utan att behöva skapa en personlig Account (MSA: er) eller ett Azure AD-konto.

Det här är en valbar offentlig förhandsversion. Mer information om Google Federation finns i [lägga till Google som en identitets leverantör för B2B-gäst användare](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Juli 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Förbättringar av e-postfunktionen i Azure Active Directory

**Typ:** Ändrad funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Azure Active Directory (Azure AD) e-postmeddelanden har nu en uppdaterad design, samt ändringar i avsändarens e-postadress och visningsnamn för avsändaren, när de skickas från följande tjänster:
 
- Azure AD-Åtkomstgranskningar
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Enterprise-appen upphör att gälla certifikat-meddelanden
- Etablering tjänstmeddelanden i Enterprise-App
 
E-postmeddelanden skickas från följande e-postadress och ditt visningsnamn:

- E-post adress: azure-noreply@microsoft.com
- Visningsnamn: Microsoft Azure
 
Ett exempel på en del nya e-postdesigner och mer information finns i [e-postmeddelanden i Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD-aktivitetsloggar är nu tillgängliga på Azure Monitor

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Azure AD-aktivitetsloggar finns nu i offentlig förhandsversion för Azure Monitor (Azures plattform hela övervakningstjänsten). Azure Monitor erbjuder långsiktig kvarhållning och smidig integrering, utöver dessa förbättringar:

- Långsiktig kvarhållning av Routning din loggfiler till din egen Azure storage-konto.

- Sömlös SIEM-integrering, utan att behöva skriva eller underhålla anpassade skript.

- Sömlös integrering med dina egna anpassade lösningar, analysverktyg eller lösningar för hantering av incidenter.

Mer information om de här nya funktionerna finns i våra blogg [Azure AD-aktivitets loggar i Azure Monitor Diagnostics finns nu i den offentliga för hands versionen och i](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) vår dokumentation, [Azure Active Directory aktivitets loggar i Azure Monitor (för hands version)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Information om villkorlig åtkomst som lagts till i rapporten Azure AD-inloggningar

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** & Skydd för identitets säkerhet
 
Den här uppdateringen kan du se vilka principer utvärderas när en användare loggar in tillsammans med principen resultatet. Dessutom kan innehåller rapporten nu typ av klientapp som används av användaren, så att du kan identifiera äldre protokolltrafik. Rapporten poster kan nu även genomsökas för ett Korrelations-ID som finns i felmeddelandet användarinriktade och kan användas för att identifiera och Felsök matchande inloggningsbegäran.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Visa äldre autentiseringar via inloggningsaktivitetsloggar

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering
 
Med introduktionen av fältet **klient app** i inloggnings aktivitets loggarna kan kunder nu se användare som använder äldre autentiseringar. Kunder kommer att ha åtkomst till den här informationen med hjälp av inloggnings Microsoft Graph-API: t eller via inloggnings aktivitets loggarna i Azure AD portal där du kan använda **klientens app** -kontroll för att filtrera på äldre autentiseringar. Läs igenom dokumentationen för mer information.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nya federerade appar finns tillgängliga i Azure AD-appgalleriet – juni 2018

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I juli 2018 har vi lagt till dessa 16 nya appar med stöd för app-galleriet:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [viss admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC-mellanlagring, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [skärm utsändning-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), Powerschool Unified klass rum, [Eli onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [NimbleX](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Föreställ dig webvision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [kunskaps bas](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Ny användarförsörjning för SaaS-appintegreringar – juli 2018

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Azure AD kan du automatisera den genereringen, underhållet och borttagningen av användaridentiteter i SaaS-program, till exempel Dropbox, Salesforce, ServiceNow med mera. Vi har lagt till stöd för följande program i Azure AD-appgalleriet för användaretablering för juli 2018:

- [Cisco-WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonus](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

En lista över alla program som stöder användar etablering i Azure AD-galleriet finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health för synkronisering – ett enklare sätt att korrigera överblivna och dubblettattributsynkroniseringsfel på

**Typ:** Ny funktion  
**Tjänste kategori:** AD Connect  
**Produkt kapacitet:** Övervaka & rapportering
 
Azure AD Connect Health introducerar hjälpåtgärder för att du kan markera och åtgärda synkroniseringsfel. Den här funktionen felsöker Duplicerat attribut synkroniseringsfel och korrigeringar objekt som kopplas från Azure AD. Den här diagnos har följande fördelar:

- Begränsar Duplicerat attribut synkroniseringsfel att tillhandahålla specifikt korrigeringar

- Gäller en korrigering för särskilda scenarier för Azure AD, lösa problem i ett enda steg

- Ingen uppgradering eller konfiguration krävs för att aktivera och använda den här funktionen

Mer information finns i [diagnostisera och åtgärda dubblerade synkroniseringsfel](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visuella uppdateringar av Azure AD och MSA-inloggningsupplevelserna

**Typ:** Ändrad funktion  
**Tjänste kategori:** Azure AD  
**Produkt kapacitet:** Användarautentisering

Vi har uppdaterat i Användargränssnittet för Microsoft online services-inloggningen, till exempel för Office 365 och Azure. Den här ändringen gör skärmarna mindre rörig och enklare. Mer information om den här ändringen finns i avsnittet [kommande förbättringar av inloggnings upplevelsen för Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>En ny version av Azure AD Connect – juli 2018

**Typ:** Ändrad funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel

Den senaste versionen av Azure AD Connect innehåller: 

- Felkorrigeringar och support-uppdateringar 

- Allmän tillgänglighet för Ping-federera-integrering

- Uppdateringar till den senaste SQL 2012-klienten 

Mer information om den här uppdateringen finns [Azure AD Connect: versions historik](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Uppdateringar av användar gränssnittet för slutanvändaren

**Typ:** Ändrad funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Ledning

Vi uppdaterar godkännande-strängen i Gränssnittet Användningsvillkoren för slutanvändare.

**Aktuell text.** För att komma åt resurser i [tenantName], måste du godkänna användningsvillkoren.<br>**Ny text.** Du måste läsa användningsvillkoren för att komma åt [tenantName] resurs.

**Aktuell text:** Om du väljer att acceptera innebär det att du godkänner alla ovanstående användnings villkor.<br>**Ny text:** Klicka på acceptera för att bekräfta att du har läst och förstått användnings villkoren.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Direktautentisering stöder äldre protokoll och program

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Direktautentisering nu stöder äldre protokoll och appar. Följande begränsningar finns nu fullständigt stöd:

- Användarinloggningar till äldre Office-program, Office 2010 och Office 2013, utan modern autentisering.

- Åtkomst till delade kalendrar och ledig eller upptagen i Exchange hybridmiljöer på Office 2010 endast.

- Användarinloggningar till Skype för företag-klientprogram utan modern autentisering.

- Användarinloggningar till PowerShell version 1.0.

- I Apples DEP (Apple DEP), med hjälp av iOS-Installationsassistenten. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Hantering av konvergerad säkerhetsinformation för återställning av lösenord genom självbetjäning och Multi-Factor Authentication

**Typ:** Ny funktion  
**Tjänste kategori:** SSPR  
**Produkt kapacitet:** Användarautentisering

Den här nya funktionen kan användarna hantera deras säkerhetsinformation (till exempel telefonnummer, e-postadress, mobilapp och så vidare) för lösenordsåterställning via självbetjäning (SSPR) och Multi-Factor Authentication (MFA) i en enskild upplevelse. Användare kommer inte längre att registrera samma säkerhetsinformationen för SSPR och MFA i två olika upplevelser. Den här nya upplevelsen gäller även för användare som har SSPR eller MFA.

Om en organisation inte tillämpar MFA-eller SSPR-registrering kan användarna registrera sina säkerhets uppgifter via portalen **Mina appar** . Därifrån kan användare registrera andra metoder som aktiverats för MFA eller SSPR. 

Det här är en valbar offentlig förhandsversion. Administratörer kan aktivera den nya upplevelsen (om det behövs) för en grupp av användare eller alla användare i en klient.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Använd Microsoft Authenticator-appen när du ska verifiera din identitet då du återställer ditt lösenord

**Typ:** Ändrad funktion  
**Tjänste kategori:** SSPR  
**Produkt kapacitet:** Användarautentisering

Den här funktionen kan icke-administratörer verifiera sin identitet när du återställer ett lösenord med hjälp av en avisering eller kod från Microsoft Authenticator (eller andra authenticator-appen). När administratörer har aktiverat den här lösenordsåterställning via självbetjäning metod, användare som har registrerat en mobil app via aka.ms/mfasetup eller aka.ms/setupsecurityinfo kan använda sina mobila appar som en verifieringsmetod vid återställning av lösenordet.

Mobilappavisering kan endast aktiveras som en del av en princip som kräver två metoder för att återställa ditt lösenord.

---

## <a name="june-2018"></a>Juni 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Ändringsmeddelande: säkerhetskorrigering till delegerad auktorisering flödet för appar som använder Azure AD-aktivitet loggar API

**Typ:** Planera för ändring  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

På grund av vår starkare säkerhet har vi haft gjort en ändring i behörigheterna för appar som använder ett delegerat auktoriseringsarkiv för att få åtkomst till [API: er för Azure AD-aktivitets loggar](https://aka.ms/aadreportsapi). Denna ändring sker den **26 juni 2018**.

Om någon av dina appar använder Azure AD-aktivitet Log API: er följer du stegen nedan för att se till att appen inte bryta när ändringen har skett.

**Uppdatera dina program behörigheter**

1. Logga in på Azure Portal, Välj **Azure Active Directory**och välj sedan app- **registreringar**.
2. Välj din app som använder Azure AD aktivitets loggar API, Välj **Inställningar**, Välj **nödvändiga behörigheter**och välj sedan **Windows-Azure Active Directory** -API: et.
3. I avsnittet **delegerade behörigheter** i bladet **Aktivera åtkomst** markerar du kryss rutan bredvid **Läs katalog** data och väljer sedan **Spara**.
4. Välj **bevilja behörigheter**och välj sedan **Ja**.
    
    >[!Note]
    >Du måste vara en Global administratör för att bevilja behörigheter till appen.

Mer information finns i avsnittet [Granting Permissions](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) i kraven för att komma åt Azure AD repor ting API-artikeln.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurera TLS för att ansluta till Azure AD-tjänster för PCI DSS-kompatibilitet

**Typ:** Ny funktion  
**Tjänste kategori:** EJ TILLÄMPLIGT  
**Produkt kapacitet:** Systemet

Transport Layer Security (TLS) är ett protokoll som ger sekretess och integritet mellan två kommunicerande program och är den vanligaste säkerhetsprotokoll som används idag.

[PCI Security Standards-rådet](https://www.pcisecuritystandards.org/) har fastställt att tidiga versioner av TLS och Secure SOCKETS Layer (SSL) måste inaktive ras för att aktivera nya och säkrare app-protokoll, med krav från och med den **30 juni 2018**. Den här ändringen innebär att om du ansluter till Azure AD-tjänster och kräva PCI DSS-efterlevnad, måste du inaktivera TLS 1.0. Flera versioner av TLS finns, men TLS 1.2 är den senaste versionen som är tillgängliga för Azure Active Directory Services. Vi rekommenderar starkt att flytta direkt till TLS 1.2 för både klient/server och webbläsaren/server kombinationer.

Inaktuella webbläsare stöder inte nyare TLS-versioner, till exempel TLS 1.2. Om du vill se vilka versioner av TLS som stöds av din webbläsare går du till webbplatsen för [SSL-labbet Qualys](https://www.ssllabs.com/) och klickar på **testa din webbläsare**. Vi rekommenderar att du uppgraderar till den senaste versionen av din webbläsare och aktivera helst endast TLS 1.2.

**Så här aktiverar du TLS 1,2, av webbläsare**

- **Microsoft Edge och Internet Explorer (båda anges med Internet Explorer)**

    1. Öppna Internet Explorer, Välj **verktyg** > **Internet alternativ** > **Avancerat**.
    2. I avsnittet **säkerhet** väljer du **Använd TLS 1,2**och väljer sedan **OK**.
    3. Stänga alla webbläsarfönster och starta om Internet Explorer. 

- **Google Chrome**

    1. Öppna Google Chrome, Skriv *Chrome://Settings/* i adress fältet och tryck på **RETUR**.
    2. Expandera **avancerade** alternativ, gå till **system** -ytan och välj **Öppna proxyinställningar**.
    3. I rutan **Internet egenskaper** väljer du fliken **Avancerat** , går till **säkerhets** avsnittet, väljer **Använd TLS 1,2**och väljer sedan **OK**.
    4. Stänga alla webbläsarfönster och starta om Google Chrome.

- **Mozilla Firefox**

    1. Öppna Firefox, Skriv *About: config* i adress fältet och tryck på **RETUR**.
    2. Sök efter termen, *TLS*och välj sedan posten **Security. TLS. version. Max** .
    3. Ställ in värdet på **3** för att tvinga webbläsaren att använda upp till version TLS 1,2 och välj sedan **OK**.

        >[!NOTE]
        >Firefox version 60,0 stöder TLS 1,3, så du kan också ställa in Security. TLS. version. Max-värdet på **4**.

    4. Stänga alla webbläsarfönster och starta om Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nya federerade appar är tillgängliga i appgalleriet för Azure AD - juni 2018

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I juni 2018 har vi lagt till stöd för dessa 15 nya appar med Federation i app-galleriet:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [att reglera musik](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1,1 token Enabled LOB-app](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [överstämning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [AutoTask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint on-premises](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [förutse CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD-lösenordsskydd är allmänt tillgänglig förhandsversion

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** Användarautentisering

Använd Azure AD-lösenordsskydd för att eliminera enkelt att gissa lösenord från din miljö. Eliminera lösenorden hjälper till att minska risken för angrepp mot ett lösenord besprutningsmedel typen av attacker.

Mer specifikt Azure AD-lösenordsskydd hjälper dig att:

- Skydda din organisations konton i både Azure AD och Windows Server Active Directory (AD). 
- Stoppar dina användare använder lösenord på en lista med mer än 500 av de vanligaste lösenorden och över 1 miljon tecken ersättningen varianter av dessa lösenord.
- Administrera Azure AD-lösenord skydd från en enda plats i Azure AD-portalen för både Azure AD och lokala Windows Server AD.

Mer information om lösen ords skydd i Azure AD finns i [eliminera Felaktiga lösen ord i din organisation](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nya princip mal len "alla gäster" som skapas när användnings villkoren skapas

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Ledning

Under skapandet av dina användnings villkor skapas även en ny mall för villkorlig åtkomst för "alla gäster" och "alla appar". Den här nya principmallen gäller den nyligen skapade ToU effektivisera skapandet och tvingande processen för gäster.

Mer information finns i [Azure Active Directory användningsvillkor-funktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Ny princip mal len anpassad princip för villkorlig åtkomst som skapats när användnings villkoren skapas

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Ledning

Under skapandet av dina användnings villkor skapas även en ny princip mal len anpassad princip för villkorlig åtkomst. Med den här nya princip mal len kan du skapa ToU och sedan omedelbart gå till bladet skapa princip för villkorlig åtkomst, utan att behöva navigera manuellt i portalen.

Mer information finns i [Azure Active Directory användningsvillkor-funktionen](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nya och heltäckande vägledning om hur du distribuerar Azure Multi-Factor Authentication

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** & Skydd för identitets säkerhet
 
Vi har släppt nya stegvisa anvisningar om hur du distribuerar Azure Multi-Factor Authentication (MFA) i din organisation.

Om du vill visa distributions guiden för MFA går du till lagrings platsen för [identitets distribution](https://aka.ms/DeploymentPlans) på GitHub. Om du vill ge feedback om distributions guiderna använder du [feedback-formuläret för distributions planen](https://aka.ms/deploymentplanfeedback). Om du har frågor om distributions guiderna kan du kontakta oss på [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD delegerad apphantering roller finns i offentlig förhandsversion

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** Access Control

Administratörer kan nu delegera hanteringsuppgifter utan att tilldela rollen som Global administratör. De nya roller och funktioner är:

- **Nya standard administratörs roller för Azure AD:**

    - **Program administratör.** Ger möjlighet att hantera alla aspekter av alla appar, inklusive registrering, inställningar för enkel inloggning, tilldelning av appar och licensiering, App proxy-inställningar och godkännande (förutom till Azure AD-resurser).

    - **Moln program administratör.** Beviljar alla programadministratör funktioner, förutom App proxy eftersom det inte ger åtkomst till lokala.

    - **Programutvecklare.** Ger möjlighet att skapa registrerings program, även om alternativet **Tillåt användare att registrera appar** är inaktiverat.

- **Ägarskap (Konfigurera per app-registrering och per företags-app, som liknar gruppens ägarskaps process:**
 
    - **Ägare av app-registrering.** Ger möjlighet att hantera alla aspekter av ägda appregistrering, inklusive appmanifestet och lägga till ytterligare ägare.

    - **Ägare av företags program.** Ger dig möjlighet att hantera många aspekter av ägda enterprise-appar, inklusive inställningar för enkel inloggning, tilldelning av appar och godkännande (förutom till Azure AD-resurser).

Mer information om den offentliga för hands versionen finns [i Azure AD-delegerade program hanterings roller finns i offentlig för hands version!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blogg. Mer information om roller och behörigheter finns [i tilldela administratörs roller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Maj 2018

### <a name="expressroute-support-changes"></a>Ändringar av ExpressRoute-stöd

**Typ:** Planera för ändring  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Systemet  

Programvara som tjänst, som Azure Active Directory (Azure AD) är utformade att fungera bäst genom att gå direkt via Internet, utan att kräva ExpressRoute eller några andra privata VPN-tunnlar. På grund av detta upphör vi att stödja ExpressRoute för Azure AD-tjänster via Azures offentliga peering och Azure-communities i Microsoft-peering, den **1 augusti 2018**. Alla tjänster som påverkas av den här ändringen märker Azure AD-trafik gradvis man växlar från ExpressRoute till Internet.

Även om vi ändrar vår support vi också veta det är fortfarande situationer där du kan behöva använda en dedikerad uppsättning kretsar för din autentiseringstrafik. På grund av detta fortsätter Azure AD att stödja per klient IP intervallbegränsningar med ExpressRoute och tjänster redan på Microsoft-peering ”andra Office 365 Online-tjänster”-communityn. Om dina tjänster som påverkas, men du behöver ExpressRoute, måste du göra följande:

- **Om du använder Azures offentliga peering.** Flytta till Microsoft-peering och registrera dig för de **andra Office 365 Online Services-communityn (12076:5100)** . Mer information om hur du flyttar från offentlig Azure-peering till Microsoft-peering finns i artikeln [flytta en offentlig peering till Microsoft-peering](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) .

- **Om du använder Microsoft-peering.** Registrera dig för den **andra Office 365 online service-communityn (12076:5100)** . Mer information om krav för routning finns i [avsnittet stöd för BGP-communities](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) i artikeln ExpressRoute routnings krav.

Om du måste fortsätta att använda dedikerade kretsar måste du prata med ditt Microsoft-konto team om hur du får behörighet att använda den **andra Office 365 online service-communityn (12076:5100)** . MS Office-hanterade-granskningsnämnd verifierar om du behöver dessa kretsar och kontrollera att du förstår de tekniska effekterna av att hålla dem. Obehörig prenumerationer som försöker skapa flödesfilter för Office 365 får ett felmeddelande. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph API: er för administrativa scenarier för Användningsvillkoren

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Utvecklings miljö
 
Vi har lagt till Microsoft Graph-API: er för administration av Azure ADs användnings villkor. Du kan skapa, uppdatera och ta bort användnings villkoren.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Lägg till slutpunkt för flera innehavare av Azure AD som en identitetsprovider i Azure AD B2C

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
Anpassade principer kan, du nu använda vanliga Azure AD-slutpunkten som en identitetsprovider i Azure AD B2C. På så sätt kan du ha en enda ingångspunkt för alla Azure AD-användare som loggar in i dina program. Mer information finns i [Azure Active Directory B2C: Tillåt att användare loggar in på en Azure AD-identitetsprovider med flera innehavare med hjälp av anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Använda interna URL: er för att komma åt appar från var som helst med vår Mina appar Inloggningstillägg och Azure AD-programproxyn

**Typ:** Ny funktion  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** DEFINITION
 
Användare kan nu komma åt program via interna URL: er även när utanför företagets nätverk med hjälp av den Mina appar skyddat Inloggningstillägg för Azure AD. Detta fungerar med alla program som du har publicerat med hjälp av Azure AD Application Proxy i valfri webbläsare som också har åtkomstpanelen webbläsartillägget installerad. URL-omdirigering funktionerna aktiveras automatiskt när en användare loggar in på tillägget. Tillägget är tillgängligt för hämtning på [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)och [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - Data i Europa för Europa kunder

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** GoLocal

Kunder i Europa kräver att deras data stannar i Europa och replikeras inte utanför Europeiska Datacenter möte sekretess-och Europeiska lagar. Den här [artikeln](https://go.microsoft.com/fwlink/?linkid=872328) innehåller detaljerad information om vilken identitets information som lagras i Europa och som även innehåller information om information som kommer att lagras utanför de europeiska data centren. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nya användaretablering för SaaS-app integreringar - maj 2018

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Azure AD kan du automatisera den genereringen, underhållet och borttagningen av användaridentiteter i SaaS-program, till exempel Dropbox, Salesforce, ServiceNow med mera. Vi har lagt till stöd för följande program i Azure AD-appgalleriet för användaretablering för maj 2018:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Hörn OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

En lista över alla program som stöder användar etablering i Azure AD-galleriet finns [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Azure AD-åtkomstgranskningar av grupper och åtkomst till appen nu ger återkommande granskningar

**Typ:** Ny funktion  
**Tjänste kategori:** Åtkomst granskningar  
**Produkt kapacitet:** Ledning
 
Åtkomstgranskning av grupper och appar är nu allmänt tillgänglig som en del av Azure AD Premium P2.  Administratörer kommer att kunna konfigurera åtkomstgranskningar av gruppmedlemskap och programtilldelningar att återkomma automatiskt med jämna mellanrum, t.ex.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD-aktivitetsloggar (inloggningar och granskning) är nu tillgängliga via MS Graph

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering
 
Azure AD-aktivitets loggar, som innehåller inloggningar och gransknings loggar, är nu tillgängliga via Microsoft Graph API. Vi har exponerat två slut punkter via Microsoft Graph API för att komma åt dessa loggar. Ta en titt på våra [dokument](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) för program mässig åtkomst till Azure AD repor ting-API: er för att komma igång. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Förbättringar av B2B-inlösen upplevelse och lämna en organisation

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C

**Just-in-Time-inlösen:** När du delar en resurs med en gäst användare som använder B2B-API – behöver du inte skicka ut en särskild inbjudan via e-post. I de flesta fall gästanvändaren kan komma åt resursen och kommer att tas via inlösen upplevelse precis i tid. Ingen mer inverkan på grund av missade e-postmeddelanden. Inga fler ber din gästanvändare ”gjorde du klickar på länken inlösen systemet skickat dig”?. Det innebär att när SPO använder inbjudan manager – grumligt bifogade filer kan ha samma canonical URL för alla användare – interna och externa – i alla tillstånd för inlösen.

**Modern inlösnings upplevelse:** Ingen fler delnings sida för upplösning av delad skärm. Användarna ser en modern godkänna erfarenhet i organisationen som bjuder in sekretesspolicy, precis som de gör för appar från tredje part.

**Gäst användare kan lämna organisationen:** När en användares relation med en organisation är över kan de själv betjäna lämna organisationen. Inga fler anrop bjuder in organisationens administratör ”tas bort”, inga fler generering supportärenden.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nya federerade appar är tillgängliga i appgalleriet för Azure AD - maj 2018

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I maj 2018 har vi lagt till dessa 18 nya appar med stöd till vår app-galleriet:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty styr, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [JAMF Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [ENVI MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate primtal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montagelayout online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), openrulle, [Arc Publishing-SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [riskmaterial](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [ReviewSnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Ny stegvisa distributionsguider för Azure Active Directory

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Katalogen
 
Nya steg-för-steg-instruktioner om hur du distribuerar Azure Active Directory (Azure AD), inklusive självbetjäning för återställning av lösen ord (SSPR), enkel inloggning (SSO), villkorlig åtkomst (CA), App proxy, användar etablering, Active Directory Federation Services (AD FS) (ADFS) till Direktautentisering (PTA) och ADFS till Password hash Sync (PHS).

Om du vill visa distributions guiderna går du till lagrings platsen för [identitets distribution](https://aka.ms/DeploymentPlans) på GitHub. Om du vill ge feedback om distributions guiderna använder du [feedback-formuläret för distributions planen](https://aka.ms/deploymentplanfeedback). Om du har frågor om distributions guiderna kan du kontakta oss på [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Företagsprogram Sök – Läs in fler appar

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Svårt att hitta dina program / tjänsthuvudnamn? Vi har lagt till möjligheten att läsa in fler program i enterprise-program listan med alla program. Som standard visar vi 20 program. Nu kan du klicka på, **läsa in mer** om du vill visa fler program. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Maj versionen av AADConnect innehåller en offentlig förhandsversion av integrering med PingFederate, viktiga säkerhetsuppdateringar, många felkorrigeringar och nya bra nya verktyg för felsökning. 

**Typ:** Ändrad funktion  
**Tjänste kategori:** AD Connect  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Maj versionen av AADConnect innehåller en offentlig förhandsversion av integrering med PingFederate, viktiga säkerhetsuppdateringar, många felkorrigeringar och nya bra nya verktyg för felsökning. Du hittar viktig information [här](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD-åtkomstgranskningar: Använd automatiskt

**Typ:** Ändrad funktion  
**Tjänste kategori:** Åtkomst granskningar  
**Produkt kapacitet:** Ledning

Åtkomstgranskningar av grupper och appar är nu allmänt tillgängliga som en del av Azure AD Premium P2. En administratör kan konfigurera för att automatiskt tillämpa granskarens ändringar till den grupp eller en app som åtkomstgranskningen är klar. Administratören kan också ange vad händer med användarens fortsatt åtkomst om granskarna inte svarar, ta bort åtkomst, blockera åtkomst eller ta system rekommendationer. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>ID-token kan inte längre returneras med frågan response_mode för nya appar. 

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Appar som skapats den 25 april 2018 kommer inte längre att kunna begära en **id_token** med hjälp av **fråge** response_mode.  Detta öppnar Azure AD-nivå med OIDC-specifikationer och hjälper till att minska din attackyta för appar.  Appar som skapats före den 25 april 2018 blockeras inte från att använda **frågan** response_mode med en response_type av **id_token**.  Det fel som returnerades när en begäran om att begära en id_token från AAD är **AADSTS70007: ' fråga ' är inte ett giltigt värde för ' response_mode ' när du begär en token**.

**Fragment** -och **form_posts** response_modes fortsätta att fungera – när du skapar nya program objekt (till exempel för användning av App-proxy) bör du se till att använda någon av dessa response_modes innan du skapar ett nytt program.  

---
 
## <a name="april-2018"></a>April 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C-åtkomsttoken är GA

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C 

Du kan nu komma åt webb-API: er skyddas av Azure AD B2C med åtkomsttoken. Funktionen flyttas från allmänt tillgänglig förhandsversion till GA. Användargränssnitt för att konfigurera Azure AD B2C-program och webb-API: er har förbättrats och andra mindre förbättringar har gjorts.
 
Mer information finns i [Azure AD B2C: begär åtkomsttoken](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testa enkel inloggningkonfigurationen för SAML-baserade program

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

När du konfigurerar SAML-baserad enkel inloggning program, kan du testa integrationen på konfigurationssidan. Om det uppstår ett fel när de loggar in kan du ange felet i belastningstester experience och Azure AD tillhandahåller Lösningssteg att lösa ditt problem.

Mer information finns i:

- [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Användnings villkoren för Azure AD har nu rapporter ATS per användare

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Fastställ
 
Administratörer kan nu välja ett visst användningsvillkor och se alla användare som har samtyckt till att användningsvillkoren och datum/tid då det skedde.

Mer information finns i [funktionen användnings villkor för Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: Riskfyllda IP-adresser för AD FS extranät-kontoutelåsningsskydd 

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Övervaka & rapportering

Connect Health nu stöder möjligheten att identifiera IP-adresser som överskrider ett tröskelvärde för misslyckade U/P-inloggningar på grundval av per timme eller dag. Funktionerna i den här funktionen är:

- Omfattande rapport som visar IP-adress och antal misslyckade inloggningar som genereras på en per timme/daglig basis med anpassningsbart tröskelvärde.
- E-postbaserad aviseringar som visar när en specifik IP-adress har passerat tröskelvärdet för misslyckade U/P-inloggningar på varje timme/daglig basis.
- En hämtning-möjlighet att göra en detaljerad analys av data

Mer information finns i avsnittet om [riskfyllda IP-rapporter](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Enkel appkonfiguration med metadatafil eller URL

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

På sidan för Enterprise-program kan administratörer överföra en SAML-metadatafil för att konfigurera SAML-baserad-inloggning för AAD-galleriet och ett icke-galleriet.

Du kan dessutom använda Azure AD application federation metadata-URL för att konfigurera enkel inloggning med målprogrammet.

Mer information finns i [Konfigurera enkel inloggning till program som inte finns i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD-användningsvillkor för nu allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Fastställ
 

Användnings villkoren för Azure AD har flyttats från offentlig för hands version till allmänt tillgängliga.

Mer information finns i [funktionen användnings villkor för Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 

Du kan nu ange vilka partnerorganisationer som du vill dela och samarbeta med i Azure AD B2B-samarbete. Om du vill göra detta måste du skapa lista över specifika tillåter eller nekar domäner. När en domän blockeras med hjälp av dessa funktioner, kan anställda inte längre skicka inbjudningar till personer i domänen.

Detta hjälper dig att styra åtkomsten till dina resurser, samtidigt som en smidig upplevelse för godkända användare.

Den här B2B-samarbets funktionen är tillgänglig för alla Azure Active Directory kunder och kan användas tillsammans med Azure AD Premium funktioner som villkorlig åtkomst och identitets skydd för mer detaljerad kontroll av när och hur externa affärs användare loggar in i och få åtkomst.

Mer information finns i [tillåta eller blockera inbjudningar till B2B-användare från vissa organisationer](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar är tillgängliga i Azure AD-appgalleriet

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I April 2018 har vi lagt till dessa 13 nya appar med stöd till vår app-galleriet:

Kriterie HCM [, FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (on-premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [dynamisk signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [konverterare nu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta prestanda övervakaren](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), hylla, [SafetyNET](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Grant B2B-användare i Azure AD åtkomst till dina lokala program (allmänt tillgänglig förhandsversion)

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C

Som en organisation som använder Azure Active Directory (Azure AD) B2B-samarbetsfunktioner för att bjuda in gästanvändare från partnerorganisationer till din Azure AD nu kan du ange dessa B2B-användare åtkomst till lokala appar. De här lokala apparna kan använda SAML-baserad autentisering eller integrerad Windows-autentisering (IWA) med Kerberos-begränsad delegering (KCD).

Mer information finns i [bevilja B2B-användare i Azure AD åtkomst till dina lokala program](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Hämta SSO-integrationsjälvstudierna från Azure Marketplace

**Typ:** Ändrad funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** integration från tredje part

Om ett program som listas på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) har stöd för SAML-baserad enkel inloggning **, ger du den** integrerings självstudie som är kopplad till det programmet. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Snabbare prestanda för Azure AD automatisk användaretablering för SaaS-program

**Typ:** Ändrad funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Tidigare kunder som använder Azure Active Directory-anslutningsappar för SaaS-program (till exempel Salesforce, ServiceNow och Box) för användaretablering kan uppleva långsam prestanda om deras Azure AD-klienter som finns över 100 000 kombinerade användare och grupper och de använde användar- och tilldelningar för att avgöra vilka användare som ska etableras.

Betydande prestandaförbättringar har distribuerats till Azure AD-etableringstjänsten som avsevärt minska den tid som behövs för att utföra inledande synkronisering mellan Azure Active Directory och mål SaaS-program på den 2 April 2018.

Därför kan många kunder som hade inledande synkronisering till appar som tog många dagar eller aldrig har slutförts kan nu slutföra inom några minuter eller timmar.

Mer information finns i [Vad händer under etableringen?](/azure//active-directory/app-provisioning/how-provisioning-works)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Lösenord för självbetjäning från Windows 10-låsskärmen för Azure AD-hybridanslutna datorer

**Typ:** Ändrad funktion  
**Tjänste kategori:** Lösen ords återställning via självbetjäning  
**Produkt kapacitet:** Användarautentisering
 
Vi har uppdaterat Windows 10 SSPR-funktionen för att inkludera stöd för datorer som är hybrid Azure AD-anslutna. Den här funktionen är tillgänglig i Windows 10 RS4 kan användarna återställa sina lösenord från låsskärmen på en Windows 10-dator. Användare som är aktiverade och som har registrerats för lösenordsåterställning via självbetjäning kan använda den här funktionen.

Mer information finns i [lösen ords återställning i Azure AD från inloggnings skärmen](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Mars 2018
 
### <a name="certificate-expire-notification"></a>Certifikatet upphör att gälla meddelande

**Typ:** Fastsatt  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Azure AD skickar ett meddelande när ett certifikat för ett galleri eller icke-galleriprogram är på väg att upphöra att gälla. 

Vissa användare fick inget meddelanden för företagsprogram som konfigurerats för SAML-baserad enkel inloggning. Det här problemet har lösts. Azure AD skickar meddelande för certifikat som går ut inom 7, 30 och 60 dagar. Du kan se den här händelsen i granskningsloggarna. 

Mer information finns i:

- [Hantera certifikat för federerad enkel inloggning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Granska aktivitets rapporter i Azure Active Directory Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter- och GitHub-Identitetsproviders i Azure AD B2C

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
Du kan nu lägga till Twitter eller GitHub som identitetsprovider i Azure AD B2C. Twitter flyttar från allmänt tillgänglig förhandsversion till GA. GitHub släpps i offentlig förhandsversion.

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

Mer information finns i:

- [Konfigurera programbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Konfigurera principer för hanterade webbläsare](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Approxy-cmdletar i Powershell GA-modulen

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control
 
Stöd för programproxy-cmdletar är nu i Powershell GA-modulen! Detta kräver att du kan hålla dig uppdaterad på Powershell-moduler - om du blir mer än ett år efter, kan vissa cmdletar kan sluta fungera. 

Mer information finns i [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Office 365-inbyggda klienter stöds av sömlös enkel inloggning med ett icke-interaktivt protokoll

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Användare som använder Office 365-inbyggda klienter (version 16.0.8730.xxxx och senare) får en tyst inloggningsupplevelse med sömlös enkel inloggning. Det här stödet tillhandahålls av tillägg ett icke-interaktivt protokoll (WS-Trust) till Azure AD.

Mer information finns i [Hur loggar du in på en intern klient med sömlös SSO-användning?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Användare får en tyst inloggningsupplevelse, med sömlös enkel om ett program skickar inloggningsförfrågningar till slutpunkter för Azure AD-klient

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Användarna får en tyst inloggning, med sömlös SSO, om ett program (till exempel `https://contoso.sharepoint.com`) skickar inloggnings förfrågningar till Azure ADs klient slut punkter – det vill säga `https://login.microsoftonline.com/contoso.com/<..>` eller `https://login.microsoftonline.com/<tenant_ID>/<..>` – i stället för Azure ADs vanliga slut punkt (`https://login.microsoftonline.com/common/<...>`).

Mer information finns i [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Lägga till endast en Azure AD-URL, istället för två URL: er tidigare, till användares Zoninställningar för intranätet att rulla ut sömlös enkel inloggning

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Om du vill distribuera sömlös SSO till dina användare behöver du bara lägga till en Azure AD-URL till användarnas intranät zons inställningar med hjälp av grup princip i Active Directory: `https://autologon.microsoftazuread-sso.com`. Tidigare var kunderna tvungna att lägga till två URL: er.

Mer information finns i [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar är tillgängliga i Azure AD-appgalleriet

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I mars 2018 har vi lagt till dessa 15 nya appar med stöd till vår app-galleriet:

[BoxCryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant av FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), vtiger CRM, inblinkning, [amplitud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin, Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial) [, Trisotech](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial) [digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>PIM för Azure-resurser är allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
Om du använder Azure AD Privileged Identity Management för katalogroller, kan du nu använda PIMS Tidsbundna åtkomst och tilldelning av funktioner för Azure-resursroller som prenumerationer, resursgrupper, virtuella datorer och andra resurser som stöds med Azure Resource Manager. Använda Multifaktorautentisering när du aktiverar roller Just-In-Time och Schemalägg aktiveringar tillsammans med godkända ändra windows. Den här versionen lägger dessutom till förbättringar som inte är tillgängliga under förhandsversionen inklusive ett uppdaterat UI, arbetsflöden för godkännande och möjligheten att utöka roller som upphör snart att gälla och förnya utgångna roller.

Mer information finns i [PIM för Azure-resurser (för hands version)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Lägger till valfria anspråk till din apps tokens (allmänt tillgänglig förhandsversion)

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Azure AD-appen kan du nu begäran anpassade eller valfria anspråk i JWTs eller SAML token.  Det här är anspråk om användaren eller klientorganisationen som inte ingår som standard i token på grund av begränsningar för storlek eller tillämplighet.  Detta är för närvarande i förhandsversion för Azure AD-appar på slutpunkterna v1.0 och v2.0.  Se dokumentationen för information på vilka anspråk som kan läggas till och hur du redigerar ditt programmanifest för att begära dem.  

Mer information finns i [valfria anspråk i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD stöder PKCE för säkrare OAuth-flöden

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Azure AD docs har uppdaterats för att notera stöd för PKCE vilket tillåter säkrare kommunikation under flöde för att bevilja OAuth 2.0-auktoriseringskod.  Både S256 och klartext code_challenges stöds på slutpunkterna v1.0 och v2.0. 

Mer information finns i [begära en auktoriseringskod](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Stöd för etablering av alla användarattributvärden i Workday Get_Workers-API

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Den offentliga förhandsversionen av inkommande etablering från Workday till Active Directory och Azure AD nu har stöd för möjligheten att extrahera och etablera alla attributvärden som är tillgängliga i Workday Get_Workers-API. Detta lägger till stöd för hundratals ytterligare standard och anpassade attribut utöver de som levereras med den första versionen av Workday inbound provisioning connector.

Mer information finns i: [Anpassa listan över användar egenskaper för arbets dagar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Ändrar gruppmedlemskap från dynamisk till statisk och vice versa

**Typ:** Ny funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete
 
Det är möjligt att ändra hur medlemskap hanteras i en grupp. Detta är användbart när du vill behålla samma gruppnamn och ID i systemet, så att alla befintliga referenser i gruppen är fortfarande giltiga. Skapa en ny grupp kräver uppdatering av dessa referenser.
Vi har uppdaterat Azure AD-administratörscentret för att stödja den här funktionen. Nu kan konvertera kunder befintliga grupper från dynamiskt medlemskap till tilldelat medlemskap och vice versa. De befintliga PowerShell-cmdletarna finns också kvar.

Mer information finns i [regler för dynamiska medlemskap för grupper i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Förbättrat utloggningsbeteende med sömlös enkel inloggning

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Tidigare, även om användarna explicit loggat ut från ett program som skyddas av Azure AD, de skulle vara loggas automatiskt in igen med sömlös enkel inloggning om de försöker få åtkomst till en Azure AD-program igen inom deras corpnet från sina domänanslutna enheter. Med den här ändringen stöds utloggning.  På så sätt kan användarna kan välja samma eller en annan Azure AD-konto för att logga tillbaka in med, i stället för att automatiskt som loggat in med sömlös enkel inloggning.

Mer information finns i [Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Programproxy för Anslutningsapp Version 1.5.402.0 har släppts

**Typ:** Ändrad funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** & Skydd för identitets säkerhet
 
Den här connector-version distribueras gradvis via November. Den här nya anslutningsapp-versionen innehåller följande ändringar:

- Anslutningsappen ställer nu in domän på cookies i stället underdomän nivå. Detta säkerställer att en jämnare upplevelse med enkel inloggning och undviker redundanta autentisering anvisningarna.
- Stöd för chunked-kodning begäranden
- Förbättrad connector hälsoövervakning 
- Flera felkorrigeringar och stabilitetsförbättringar

Mer information finns i [förstå Azure AD-programproxy-kopplingar](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Februari 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Förbättrad navigering för att hantera användare och grupper

**Typ:** Planera för ändring  
**Tjänste kategori:** Katalog hantering  
**Produkt kapacitet:** Katalogen

Navigeringsupplevelsen för att hantera användare och grupper har effektiviserats. Du kan nu gå från översikt över directory direkt till listan över alla användare med enklare åtkomst till listan över borttagna användare. Du kan också navigera från en directory-översikt direkt till listan över alla grupper med enklare åtkomst till hanteringsinställningar för gruppen. Och även från översiktssidan directory du kan söka efter en användare, grupp, företagsprogram eller appregistrering. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Tillgänglighet för inloggningar och granska rapporter i Microsoft Azure som drivs av 21Vianet (Azure Kina 21Vianet)

**Typ:** Ny funktion  
**Tjänste kategori:** Azure Stack  
**Produkt kapacitet:** Övervaka & rapportering

Azure AD Activity log rapporter är nu tillgängliga i Microsoft Azure som drivs av 21Vianet (Azure Kina 21Vianet)-instanser. Det ingår följande loggar:

- **Inloggnings loggar** – innehåller alla inloggnings loggar som är kopplade till din klient.

- **Självbetjänings loggar för lösen ords granskning** – innehåller alla SSPR gransknings loggar.

- **Gransknings loggar för katalog hantering** – innehåller alla katalog hanterings relaterade gransknings loggar som användar hantering, program hantering och andra.

Med dessa loggar kan du få insikter om hur din miljö. Med dessa data kan du:

- Avgör hur dina appar och tjänster används av användarna.

- Felsöka problem som hindrar användare från att uträtta sitt arbete.

Mer information om hur du använder de här rapporterna finns i [Azure Active Directory rapportering](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Använda ”rapportläsare” rollen (icke-administratörsroll) för att visa Azure AD-aktivitetsrapporter

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Som en del av kundernas feedback för att ge icke-administratörer till gång till Azure AD-aktivitets loggar har vi aktiverat möjligheten för användare som finns i rollen "rapport läsare" för att få åtkomst till inloggningar och gransknings aktiviteter i Azure Portal samt använda Microsoft Graph Application. 

Mer information om hur du använder de här rapporterna finns i [Azure Active Directory rapportering](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>EmployeeID-anspråk finns tillgängligt som användarattribut och användaridentifierare

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Du kan konfigurera **Anställningsnr** som användar-ID och användarattribut för medlems användare och B2B-gäster i SAML-baserade inloggnings program från företags programmets användar gränssnitt.

Mer information finns i [Anpassa anspråk som utfärdats i SAML-token för företags program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Förenklad programhantering med jokertecken i Azure AD Application Proxy

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Användarautentisering
 
För att underlätta programdistribution och minskar de administrativa omkostnaderna, stöder vi nu möjligheten att publicera program med jokertecken. Om du vill publicera ett jokertecken-program, kan du följer standard publishing programflödet, men använder jokertecken i de interna och externa URL: er.

Mer information finns i [program med jokertecken i Azure Active Directory Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Ny cmdletar som stöder konfiguration av programproxy

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Systemet

Den senaste versionen av AzureAD PowerShell-förhandsversionsmodulen innehåller nya cmdlets som låter kunder konfigurera Application Proxy-program med hjälp av PowerShell.

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
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Ny cmdletar som stöder konfiguration av grupper

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Systemet

Den senaste versionen av AzureAD PowerShell-modulen innehåller cmdlets för att hantera grupper i Azure AD. Dessa cmdletar som tidigare var tillgängliga i modulen AzureADPreview och läggs nu till AzureAD-modulen

Grupp-cmdletar som är nu versionen för allmän tillgänglighet är: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>En ny version av Azure AD Connect är tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** AD Sync  
**Produkt kapacitet:** Systemet
 
Azure AD Connect är det rekommenderade verktyget för att synkronisera data mellan Azure AD och på lokala datakällor, inklusive Windows Server Active Directory och LDAP.

>[!Important]
>Den här bygger introducerar schema- och synkronisera regel ändringar. Den Azure AD Connect-synkroniseringstjänsten utlöser en fullständig Import och fullständig synkronisering steg efter en uppgradering. Information om hur du ändrar det här beteendet finns i [så här uppskjutar du fullständig synkronisering efter uppgraderingen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Den här versionen har följande uppdateringar och ändringar:

**Korrigerade problem**

- Åtgärda tidsinställning fönstret på bakgrundsaktiviteter för Partitionsfiltrering sidan när du växlar till nästa sida.

- Ett fel som orsakade åtkomstfel när den anpassade åtgärden ConfigDB har åtgärdats.

- En bugg att komma tillrätta med tidsgräns för sql-anslutning.

- Ett fel har åtgärdats om certifikat med SAN-jokertecken inte uppfyllde kontroll.

- Ett fel som orsakar miiserver.exe krasch under exporten för AAD-koppling har åtgärdats.

- Ett fel har åtgärdats där ett felaktigt lösenord försök inloggad DC när körs orsakade AAD connect guiden för att ändra konfigurationen

**Nya funktioner och förbättringar**
 
- Programtelemetri - administratörer kan växla den här klassen av data på/av.

- Azure AD Health-data – administratörer måste besök health-portalen för att styra inställningar för deras hälsotillstånd. När tjänsten principen har ändrats, läser agenterna och använda den.

- Lagt till enhetsåtgärder tillbakaskrivning av konfigurationen och en förloppsindikator för att initiera dialogrutan.

- Förbättrad Allmänt diagnostik HTML-rapport och en fullständig datainsamling i en ZIP-Text / HTML-rapport.

- Förbättrad tillförlitlighet för automatisk uppgradering och lagt till ytterligare telemetri för att säkerställa att du kan kontrollera hälsotillståndet för servern.

- Begränsa åtkomstbehörighet till Privilegierade konton för AD-anslutningskontot. För nya installationer guiden begränsar de behörigheter som Privilegierade konton har för MSOL-kontot när du har skapat MSOL-konto. Ändringarna påverkar express-installationer och anpassade installationer med Skapa automatiskt konto.

- Ändra installationsprogrammet för att inte kräva SA privilegiet ren installation av AADConnect.

- Nya verktyg för att Felsök synkroniseringsproblem för ett specifikt objekt. Verktyget kontrollerar för närvarande följande saker:

    - UserPrincipalName matchningsfel mellan synkroniserade användarobjektet och användarkontot i Azure AD-klient.
  
    - Om objektet är filtrerad från synkronisering på grund av filtrering av domän
  
    - Om objektet är filtrerad från synkronisering på grund av organisationsenhet (OU) filtrering

- Nya verktyg för att synkronisera den aktuella lösenords-hash som lagras i en lokal Active Directory för ett visst användarkonto. Verktyget kräver inte en lösenordsändring. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Program som stöder Intune-appskydd principer som har lagts till för användning med Azure AD-programbaserad villkorlig åtkomst

**Typ:** Ändrad funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Vi har lagt till fler program som stöder programbaserad villkorlig åtkomst. Nu kan du få åtkomst till Office 365 och andra Azure AD-anslutna molnappar med hjälp av dessa godkända klientprogram.

Följande program kommer att läggas i slutet av februari:

- Microsoft Power BI

- Startprogram för Microsoft

- Microsoft fakturering

Mer information finns i:

- [Godkänt klient program krav](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD App-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Användningsvillkor uppdatering av mobil miljö 

**Typ:** Ändrad funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Fastställ

När användnings villkoren visas kan du nu Klicka på **Visa? Klicka här**. Om du klickar på den här länken öppnas användningsvillkoren internt på din enhet. Oavsett teckenstorlek på dokumentet eller enhetens skärmstorlek, kan du zooma och läsa dokumentet efter behov. 

---
 
## <a name="january-2018"></a>Januari 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar är tillgängliga i Azure AD-appgalleriet 

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I januari 2018 har följande nya appar med stöd för identitetsfederation lagts till i app-galleriet:

[IBM Openpages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federerad Directory och [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Logga in med identifierad ytterligare risk

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & Skydd för identitets säkerhet

De insikter som du får för en identifierad risk identifiering är knutna till din Azure AD-prenumeration. Med Azure AD Premium P2-versionen får du den mest detaljerade informationen om alla underliggande identifieringar.

Med Azure AD Premium P1-versionen visas identifieringar som inte täcks av din licens som inloggning med risk identifiering med ytterligare risk upptäckt.

Mer information finns i [Azure Active Directory risk identifieringar](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Dölj Office 365-program från slutanvändarens åtkomstpaneler

**Typ:** Ny funktion  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** DEFINITION

Du kan nu bättre hantera hur Office 365-program som visas på användarnas åtkomstpaneler via en ny Användarinställning. Det här alternativet är användbart för att minska antalet appar i åtkomstpaneler för en användare om du vill visa enbart Office-appar i Office-portalen. Inställningen finns i **användar inställningarna** och är märkt, **användare kan bara se Office 365-appar i Office 365-portalen**.

Mer information finns i [Dölj ett program från användarens upplevelse i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Sömlös inloggning till appar som har aktiverats för lösenord SSO direkt från appens Webbadress 

**Typ:** Ny funktion  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** DEFINITION

Webbläsartillägget för Mina appar är nu tillgängligt via ett enkelt verktyg som ger dig den Mina appar enkel inloggning om funktionen som en genväg i webbläsaren. När installationen är klar ikonen användarens waffle i webbläsaren som ger dem snabb åtkomst till appar. Användare kan nu dra nytta av:

- Möjligheten att logga in direkt på lösenord SSO-baserade appar från appens på inloggningssidan
- Starta en app som använder funktionen Snabbsökning
- Genvägar till nyligen använda appar från tillägget
- Tillägget är tillgängligt för Microsoft Edge, Chrome och Firefox.
 
Mer information finns i avsnittet [Mina appar säker inloggnings tillägg](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure AD-administration upplevelse i klassiska Azure-portalen har tagits bort

**Typ:** Föråldrad   
**Tjänste kategori:** Azure AD  
**Produkt kapacitet:** Katalogen

Från och med den 8 januari 2018, Azure AD-administration upplevelse i den klassiska Azure-portalen har tagits bort. Detta ägde rum i samband med den klassiska Azure-portalen själva tas ur bruk. I framtiden bör du använda [Azure AD administrations Center](https://aad.portal.azure.com) för all Portal-baserad administration av Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Webbportalen PhoneFactor har tagits bort

**Typ:** Föråldrad  
**Tjänste kategori:** Azure AD  
**Produkt kapacitet:** Katalogen
 
Från och med den 8 januari 2018 har tagits bort PhoneFactor-webbportalen. Den här portalen användes för administration av MFA-servern, men dessa funktioner har flyttats till Azure-portalen på portal.azure.com. 

MFA-konfigurationen finns på: **Azure Active Directory \> MFA Server**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Avverka Azure AD-rapporter

**Typ:** Föråldrad  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Hantering av identitets livs cykel  


Med den allmänna tillgängligheten för nya Azure Active Directory-Administration-konsolen och nya API: er nu tillgängliga för både aktiviteten och säkerhet, rapporten API: er under ”/ rapporter” slutpunkten har tagits bort från och med slutet av den 31 December 2017.

**Vad är tillgängligt?**

Som en del av övergången till den nya administratörskonsolen, har vi gjort 2 nya API: er tillgängliga för hämtning av Azure AD-aktivitetsloggar. Den nya uppsättningen API: er ger bättre filtrering och sortering funktioner förutom att tillhandahålla bättre gransknings- och inloggningsaktiviteter. Data som tidigare var tillgängliga via säkerhets rapporterna kan nu nås via identitets skyddets identifierings-API i Microsoft Graph.

Mer information finns i:

- [Kom igång med API: et för Azure Active Directory rapportering](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Kom igång med Azure Active Directory Identity Protection och Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>December 2017

### <a name="terms-of-use-in-the-access-panel"></a>Villkor för användning i åtkomstpanelen

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Fastställ
 
Du kan nu gå till åtkomstpanelen och visa användningsvillkoren som du tidigare har accepterat.

Följ de här stegen:

1. Gå till portalen för mina [appar](https://myapps.microsoft.com)och logga in.

2. Välj ditt namn i det övre högra hörnet och välj sedan **profil** i listan. 

3. Välj **Granska användnings villkor**i din **profil**. 

4. Nu kan du granska användningsvillkoren accepteras. 

Mer information finns i [funktionen användnings villkor för Azure AD (för hands version)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nya Azure AD-inloggningen

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD  
**Produkt kapacitet:** Användarautentisering
 
Azure AD och identitetssystem gränssnitt för Microsoft-konto har gjorts om så att de har ett konsekvent utseende. Dessutom inloggningssidan för Azure AD samlar in användarnamnet först, följt av autentiseringsuppgifter på en annan skärm.

Mer information finns i [den nya inloggnings upplevelsen för Azure AD finns nu i offentlig för hands version](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Färre inloggning prompter: en ny ”Håll mig inloggad” upplevelse för Azure AD-inloggningen

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD  
**Produkt kapacitet:** Användarautentisering
 
Kryss rutan **Håll mig inloggad** på inloggnings sidan för Azure AD ersattes med en ny fråga som visas när du har autentiserat dig. 

Om du svarar **Ja** på den här varningen ger tjänsten en beständig uppdateringstoken. Detta är samma sak som när du markerade kryss rutan **Håll mig inloggad** i den gamla upplevelsen. För federerade klienter visar meddelandet efter att du autentisera med federerade tjänsten.

Mer information finns [i färre inloggnings meddelanden: den nya funktionen "Håll mig inloggad" för Azure AD är i för hands version](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Lägg till konfiguration för att kräva användningsvillkor som ska expanderas innan de accepterar

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Fastställ
 
Ett alternativ för administratörer kräver deras användarna expanderar användningsvillkoren innan de accepterar villkoren.

Välj antingen **på** eller **av** för att kräva att användarna expanderar användnings villkoren. Inställningen **on** kräver att användare visar användnings villkoren innan de accepterar dem.

Mer information finns i [funktionen användnings villkor för Azure AD (för hands version)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Begränsade aktivering för kvalificerade rolltilldelningar

**Typ:** Ny funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
Du kan använda begränsade aktivering för att aktivera berättigade Azure-resurs rolltilldelningar med mindre självständigt än de ursprungliga tilldelningen standardvärdena. Ett exempel är om du är tilldelade som ägare till en prenumeration i din klient. Du kan aktivera rollen ägare för upp till fem resurser som ingår i prenumerationen (som resursgrupper och virtuella datorer) med begränsade aktivering. Omfång aktiveringen kan minska risken för körning av oönskade ändringar till viktiga Azure-resurser.

Mer information finns i [Vad är Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nya federerade appar i appgalleriet för Azure AD

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I December 2017 så vi har lagt till stöd för dessa nya appar med Federation till vår app-galleriet:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI digital butik](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [image Worker](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO för Bamboo med resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO för BitBucket med resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD-integrering.

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Godkännandearbetsflöden för Azure AD-katalogroller

**Typ:** Ändrad funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
Arbetsflöde för godkännande för Azure AD-katalogroller är allmänt tillgänglig.

Med arbetsflöden för godkännande, kan privilegierade rollen administratörer kräva berättigade roller medlemmar att begäran om rollaktivering innan de kan använda den privilegierade rollen. Flera användare och grupper kan vara delegerade godkännande ansvarsområden. Berättigade rollmedlemmar ta emot meddelanden när godkännande har slutförts och deras roll är aktiv.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Direktautentisering: Skype för Business support

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Direktautentisering nu stöder användarinloggningar till Skype för företag-klientprogram som har stöd för modern autentisering, vilket innefattar online och hybridtopologier. 

Mer information finns i [Skype for Business-topologier som stöds med modern autentisering](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Uppdateringar av Azure AD Privileged Identity Management för Azure RBAC (förhandsversion)

**Typ:** Ändrad funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
Med den offentliga förhandsversion uppdateringen av Azure AD Privileged Identity Management (PIM) för rollbaserad åtkomstkontroll (RBAC) kan du nu:

* Använd Enough Administration.
* Kräv godkännande för att aktivera resource roller.
* Schemalägga en framtida aktivering av en roll som kräver godkännande för både Azure AD och Azure RBAC-roller.
 
Mer information finns i [Privileged Identity Management för Azure-resurser (för hands version)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>November 2017
 
### <a name="access-control-service-retirement"></a>Access Control service pensionering

**Typ:** Planera för ändring  
**Tjänste kategori:** Access Control tjänst  
**Produkt kapacitet:** Access Control tjänst 

Azure Active Directory Access Control (även kallat Access Control service) kommer att dras tillbaka i slutet av 2018. Mer information som innehåller en detaljerad schema och övergripande migreringsvägledningen ges i de närmaste veckorna. Du kan lämna kommentarer på den här sidan om du har frågor om tjänsten Access Control och en gruppmedlem kommer att besvara dem.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Begränsa webbläsaråtkomst till Intune Managed Browser 

**Typ:** Planera för ändring  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** Identitets säkerhet och skydd

Du kan begränsa webbläsaråtkomst till Office 365 och andra Azure AD-anslutna molnappar genom att använda Intune Managed Browser som en godkänd app. 

Du kan nu konfigurera följande villkor för program-baserad villkorlig åtkomst:

**Klient program:** Webbläsare

**Vad händer med ändringen?**

Idag, blockeras åtkomst när du använder det här tillståndet. När förhandsgranskningen är tillgänglig, kräver all åtkomst användning av managed browser-programmet. 

Leta efter den här funktionen och mer information finns i kommande bloggar och viktig information. 

Mer information finns i [villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klient program för Azure AD App-baserad villkorlig åtkomst

**Typ:** Planera för ändring  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** Identitets säkerhet och skydd

Följande appar finns på listan över [godkända klient program](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- [Microsoft-Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Mer information finns i:

- [Godkänt klient program krav](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD App-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Användningsvillkor med stöd för flera språk

**Typ:** Ny funktion    
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Fastställ

Administratörer kan nu skapa nya användningsvillkor som innehåller flera PDF-dokument. Du kan tagga de här PDF-dokument med ett motsvarande språk. Användarna visas PDF-filen med motsvarande språk baserat på deras preferenser. Om det finns ingen matchning, visas standardspråket.

---
 
### <a name="real-time-password-writeback-client-status"></a>Klientstatus med tillbakaskrivning av lösenord i realtid

**Typ:** Ny funktion  
**Tjänste kategori:** Lösen ords återställning via självbetjäning  
**Produkt kapacitet:** Användarautentisering

Du kan nu granska statusen för din lokala tillbakaskrivningsklient för lösenord. Det här alternativet är tillgängligt i avsnittet **lokal integration** på sidan [lösen ords återställning](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) . 

Om det finns problem med anslutningen till din lokala tillbakaskrivningsklient, visas ett felmeddelande som ger dig med:

- Information om varför du inte kan ansluta till din lokala tillbakaskrivningsklient.
- En länk till dokumentationen som hjälper dig att lösa problemet. 

Mer information finns i [lokal integrering](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD App-baserad villkorlig åtkomst 
 
**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD  
**Produkt kapacitet:** Identitets säkerhet och skydd

Nu kan du begränsa åtkomsten till Office 365 och andra Azure AD-anslutna molnappar till [godkända klient program](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview) som stöder Intune App Protection-principer med hjälp av [Azure AD App-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Intunes appskyddsprinciper används för att konfigurera och skydda företagets data på dessa klientprogram.

Genom att kombinera [app-baserade](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) med [enhets](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) principer för villkorlig åtkomst har du flexibiliteten att skydda data för personliga och företagets enheter.

Följande villkor och kontroller är nu tillgängliga för användning med app-baserad villkorlig åtkomst:

**Plattforms villkor som stöds**

- iOS
- Android

**Villkor för klient program**

- Mobila appar och skrivbordsklienter

**Åtkomstkontroll**

- Kräv godkänd klientapp

Mer information finns i [Azure AD App-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Hantera Azure AD-enheter i Azure portal

**Typ:** Ny funktion  
**Tjänste kategori:** Enhets registrering och hantering  
**Produkt kapacitet:** Identitets säkerhet och skydd

Nu hittar du alla dina enheter som är anslutna till Azure AD och enhetsrelaterade aktiviteterna i ett och samma ställe. Det finns en ny administration-lösning för att hantera alla dina enhetsidentiteter och inställningar i Azure-portalen. I den här versionen kan du:

- Visa alla enheter som är tillgängliga för villkorlig åtkomst i Azure AD.
- Visa egenskaper, som innehåller din hybrid Azure AD-anslutna enheter.
- Hitta BitLocker-nycklar för Azure AD-anslutna enheter, hantera din enhet med Intune och mycket mer.
- Hantera Azure AD-enhet-relaterade inställningar.

Mer information finns i [Hantera enheter med hjälp av Azure Portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Stöd för macOS som enhets plattform för villkorlig åtkomst i Azure AD 

**Typ:** Ny funktion    
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** Identitets säkerhet och skydd 

Du kan nu inkludera (eller undanta) macOS som enhets plattforms villkor i din Azure AD-princip för villkorlig åtkomst. Med hjälp av Mac OS för att enhetsplattformar som stöds kan du:

- **Registrera och hantera macOS-enheter med hjälp av Intune.** Precis som för andra plattformar som iOS och Android finns ett företagets portalprogram är tillgängliga för macOS att enhetlig registreringar. Du kan använda den nya företagsportalappen för macOS för att registrera en enhet med Intune och registrera den med Azure AD.
- **Se till att macOS-enheter följer organisationens efterlevnadsprinciper som definierats i Intune.** I Intune på Azure portal kan ställa du nu in principer för efterlevnad för macOS-enheter. 
- **Begränsa åtkomsten till program i Azure AD till endast kompatibla macOS-enheter.** Redigering av princip för villkorlig åtkomst har macOS som en separat enhets plattforms alternativ. Nu kan du skapa macOS-specifika principer för villkorlig åtkomst för mål programmet som angetts i Azure.

Mer information finns i:

- [Skapa en princip för enhetsefterlevnad för macOS-enheter med Intune](https://aka.ms/macoscompliancepolicy)
- [Villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Network Policy Server-tillägg för Azure Multi-Factor Authentication 

**Typ:** Ny funktion    
**Tjänste kategori:**  Multi-Factor Authentication  
**Produkt kapacitet:** Användarautentisering

NPS-tillägget för Azure Multi-Factor Authentication lägger till molnbaserade Multi-Factor Authentication-funktioner till din infrastruktur för autentisering med hjälp av befintliga servrar. Med Network Policy Server-tillägg du kan lägga till telefonsamtal, textmeddelande eller app telefonverifiering till ditt befintliga autentiseringsflödet. Du behöver inte installera, konfigurera och underhålla nya servrar. 

Det här tillägget har skapats för organisationer som vill skydda virtuella privata nätverksanslutningar utan att distribuera Azure Multi-Factor Authentication-servern. Nätverksprincipservern tillägget fungerar som ett kort mellan RADIUS- och molnbaserade Azure Multi-Factor Authentication för att tillhandahålla en andra faktor autentisering för federerade eller synkroniserade användare.

Mer information finns i [integrera din befintliga infrastruktur för nätverks Policy Server med Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Återställ eller ta bort borttagna användare permanent

**Typ:** Ny funktion    
**Tjänste kategori:** Användar hantering  
**Produkt kapacitet:** Katalogen 

I Azure AD-administrationscentret kan du nu:

- Återställa en borttagen användare. 
- Ta bort en användare permanent.

**Så här provar du:**

1. I Azure AD administrations Center väljer du [alla användare](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) i avsnittet **Hantera** . 

2. I listan **Visa** väljer du **nyligen borttagna användare**. 

3. Välj en eller flera nyligen borttagna användare och återställa dem antingen eller ta bort dem permanent.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klient program för Azure AD App-baserad villkorlig åtkomst
 
**Typ:** Ändrad funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** Identitets säkerhet och skydd

Följande appar har lagts till i listan över [godkända klient program](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Microsoft Planner
- Azure Information Protection 

Mer information finns i:

- [Godkänt klient program krav](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD App-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Använd "eller" mellan kontroller i en princip för villkorlig åtkomst 

**Typ:** Ändrad funktion    
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** Identitets säkerhet och skydd
 
Du kan nu använda "eller" (Kräv en av de valda kontrollerna) för kontroller för villkorlig åtkomst. Du kan använda den här funktionen för att skapa principer med ”eller” mellan åtkomstkontroller. Du kan till exempel använda den här funktionen för att skapa en princip som kräver att en användare att logga in med hjälp av Multi-Factor Authentication ”eller” på en kompatibel enhet.

Mer information finns i [kontroller i villkorlig åtkomst för Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Agg regering av risk identifieringar i real tid

**Typ:** Ändrad funktion    
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** Identitets säkerhet och skydd

I Azure AD Identity Protection sammanställs nu alla risk identifieringar i real tid som kommer från samma IP-adress på en bestämd dag för varje typ av risk identifiering. Den här ändringen begränsar mängden risk identifieringar som visas utan några ändringar i användar säkerheten.

Den underliggande realtidsidentifiering fungerar varje gång användaren loggar in. Om du har en säkerhetsprincip för inloggningsrisk konfigurera Multifaktorautentisering eller blockera åtkomst till utlöst den fortfarande under varje riskfyllda inloggningen.
 
---
 
## <a name="october-2017"></a>Oktober 2017

### <a name="deprecate-azure-ad-reports"></a>Avverka Azure AD-rapporter

**Typ:** Planera för ändring  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Hantering av identitets livs cykel  

Azure-portalen ger dig:

- En ny Azure AD-administrationskonsolen.
- Nya API: er för aktiviteten och säkerhet.
 
På grund av de nya funktionerna rapporten API: er under/Reports slutpunkten drogs tillbaka den 10 December 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Automatisk inloggningsfält identifiering

**Typ:** Fastsatt   
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** Enkel inloggning  

Azure AD stöder automatisk inloggningsfält identifiering för program som renderar en HTML-Användarfält namn och lösenord. De här stegen beskrivs i [hur du automatiskt samlar in inloggnings fält för ett program](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Du kan hitta den här funktionen genom att lägga till ett program som *inte är en Galleri* på sidan **företags program** i [Azure Portal](https://aad.portal.azure.com). Dessutom kan du konfigurera läget för **enkel inloggning** på det nya programmet till **lösenordsbaserad enkel inloggning**, ange en webb-URL och sedan spara sidan.
 
Den här funktionen har tillfälligt inaktiverats på grund av ett problem med tjänsten. Problemet har lösts och automatisk inloggningsfält identifieringen är tillgänglig igen.

---

### <a name="new-multi-factor-authentication-features"></a>Nya Multi-Factor Authentication-funktioner

**Typ:** Ny funktion  
**Tjänste kategori:** Multi-Factor Authentication  
**Produkt kapacitet:** Identitets säkerhet och skydd  

Multifaktorautentisering (MFA) är en viktig del av att skydda din organisation. Om du vill göra mer anpassningsbar autentiseringsuppgifter och erfarenhet smidigare, följande funktioner har lagts till: 

- Multi-Factor utmaning resultat är direkt integrerad i Azure AD-inloggningen rapporten som ger programmatisk åtkomst till MFA-resultat.
- MFA-konfigurationen är mer kommer att integreras i Azure AD-konfigurationen upplevelse i Azure-portalen.

Är en integrerad del av konfigurationsupplevelse för core Azure AD med den här offentliga förhandsversionen kan MFA-hantering och rapportering. Nu kan du hantera MFA management portal-funktionerna i Azure AD-upplevelse.

Mer information finns i [referens för MFA-rapportering i Azure Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Användningsvillkor

**Typ:** Ny funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Fastställ  

Du kan använda Azure AD-användningsvillkor för att presentera information, till exempel relevanta ansvarsfriskrivningar för juridiska krav eller efterlevnadskrav för användare.

Du kan använda Azure AD-användningsvillkor i följande scenarier:

- Allmänna villkor för användningsvillkor för alla användare i din organisation
- Specifika användningsvillkor baserat på en användares attribut (till exempel läkare jämfört med sjuksköterskor) eller inhemska jämfört med internationella medarbetare, som görs av dynamiska grupper
- Specifika användningsvillkor för att komma åt hög inverkan företagsappar som Salesforce

Mer information finns i [användnings villkoren för Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Förbättringar av Privileged Identity Management

**Typ:** Ny funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management  

Du kan hantera, kontrollera och övervaka åtkomst till Azure-resurser (förhandsversion) i organisationen kan med Azure AD Privileged Identity Management:

- Prenumerationer
- Resursgrupper
- Virtuella datorer 

Alla resurser i Azure-portalen som använder Azure RBAC-funktioner kan dra nytta av säkerhets- och hanteringsfunktioner för programlivscykel som Azure AD Privileged Identity Management har att erbjuda.

Mer information finns i [Privileged Identity Management för Azure-resurser](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Åtkomstgranskningar

**Typ:** Ny funktion  
**Tjänste kategori:** Åtkomst granskningar  
**Produkt kapacitet:** Fastställ  

Organisationer kan använda åtkomstgranskningar (förhandsversion) för att effektivt hantera gruppmedlemskap och åtkomst till företagsprogram: 

- Du kan certifiera om gästanvändares åtkomst med åtkomstgranskningar av deras åtkomst till program och medlemskap i grupper. Granskare kan effektivt bestämma om så att gästerna fortsatt åtkomst baserat på de insikter som tillhandahålls av åtkomstgranskningar.
- Du kan certifiera om medarbetares åtkomst till program och gruppmedlemskap med åtkomstgranskningar.

Du kan samla in åtkomstgranskningskontroller i program som är relevanta för din organisation för att spåra granskningar för efterlevnad eller riskkänsliga program.

Mer information finns i [åtkomst granskningar för Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Dölj program från tredje part från Mina program och Office 365-appstartaren

**Typ:** Ny funktion  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** Enkel inloggning  

Nu kan du bättre hantera appar som visas på användarnas portaler via en ny **Dölj app** -egenskap. Du kan dölja appar för att i fall där paneler som visas för backend-tjänster eller dubbla paneler och oreda användarnas app startprogram. Växlingen är i avsnittet **Egenskaper** i appen från tredje part och är märkt **synlig för användaren?** Du kan också dölja en app programmässigt via PowerShell. 

Mer information finns i [Dölj ett program från tredje part från en användares upplevelse i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Vad är tillgängligt?**

 Som en del av övergången till den nya administratörskonsolen, två nya API: er för att hämta Azure AD activity loggar är tillgängliga. Den nya uppsättningen API: er ger bättre filtrering och sortering funktioner förutom att tillhandahålla bättre gransknings- och inloggningsaktiviteter. De data som tidigare var tillgängliga via säkerhets rapporterna kan nu nås via identitets skydds identifieraren API i Microsoft Graph.


## <a name="september-2017"></a>September 2017

### <a name="hotfix-for-identity-manager"></a>Snabbkorrigering för Identity Manager

**Typ:** Ändrad funktion  
**Tjänste kategori:** Identitets hanterare  
**Produkt kapacitet:** Hantering av identitets livs cykel  

En samlad snabbkorrigering (build 4.4.1642.0) är tillgänglig från och med den 25 September 2017 för Identity Manager 2016 Service Pack 1. Det här paketet:

- Löser problem och förbättringar.
- Är en ackumulerad uppdatering som ersätter alla uppdateringar i Identity Manager 2016 Service Pack 1 upp till build 4.4.1459.0 för Identity Manager 2016. 
- Kräver att du har Identity Manager 2016 skapa 4.4.1302.0. 

Mer information finns i [samlat snabb korrigerings paket (build 4.4.1642.0) tillgängligt för Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
