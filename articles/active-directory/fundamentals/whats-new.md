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
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45a7477fa312a172579c6b4717e9f679ac253e1b
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823815"
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?

>Bli informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` klistra in ![den här URL:](./media/whats-new/feed-icon-16x16.png) en i din RSS feed reader-ikon feed reader.

Azure AD får förbättringar med jämna mellanrum. Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

- Den senaste versionen
- Kända problem
- Felkorrigeringar
- Inaktuell funktion
- Planer för ändringar

Den här sidan uppdateras varje månad, så gå tillbaka till den regelbundet. Om du letar efter objekt som är äldre än sex månader kan du hitta dem i [arkivet för nyheter i Azure Active Directory](whats-new-archive.md).

---

## <a name="july-2019"></a>Juli 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Planera för ändring: Programproxy-tjänstens uppdatering som endast stöder TLS 1,2

**Bastyp** Förändringsplan  
**Tjänste kategori:** App Proxy  
**Produkt kapacitet:** Åtkomstkontroll

För att hjälpa dig med vår starkaste kryptering kommer vi att börja begränsa åtkomsten till Application Proxy-tjänsten till endast TLS 1,2-protokoll. Den här begränsningen kommer inlednings vis att distribueras till kunder som redan använder TLS 1,2-protokoll, så att du inte ser effekten. Fullständig utfasning av TLS 1,0 och TLS 1,1-protokollen kommer att slutföras den 31 augusti 2019. Kunder som fortfarande använder TLS 1,0 och TLS 1,1 får ett avancerat meddelande om att förbereda inför den här ändringen.

För att upprätthålla anslutningen till Application Proxy-tjänsten i hela den här ändringen rekommenderar vi att du kontrollerar att kombinationerna av klient-och webb läsar servern har uppdaterats för att använda TLS 1,2. Vi rekommenderar också att du tar med alla klient system som används av dina anställda för att få åtkomst till appar som publicerats via tjänsten Application Proxy.

Mer information finns i [lägga till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Planera för ändring: Design uppdateringar kommer för program galleriet

**Bastyp** Förändringsplan  
**Tjänste kategori:** Företagsappar  
**Produkt kapacitet:** SSO

Nya användar gränssnitts ändringar kommer till design av bladet **Lägg till från galleriet** i bladet **Lägg till ett program** . Med de här ändringarna kan du enkelt hitta dina appar som stöder automatisk etablering, OpenID Connect, Security Assertion Markup Language (SAML) och inloggning med lösen ord (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Planera för ändring: Borttagning av MFA-serverns IP-adress från Office 365-IP-adressen

**Bastyp** Förändringsplan  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** Identitetssäkerhet och skydd

Vi tar bort MFA-serverns IP-adress från [Office 365 IP-adress och URL-webbtjänst](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Om du för närvarande förlitar dig på dessa sidor för att uppdatera brand Väggs inställningarna måste du även se till att du inkluderar listan över IP-adresser som beskrivs i avsnittet om **brand Väggs krav för Azure Multi-Factor Authentication-Server** i avsnittet [komma igång med artikeln Azure Multi-Factor Authentication Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Endast app-token kräver att klient programmet finns i resurs klienten

**Bastyp** Korrigerat  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Den 26 juli 2019 ändrade vi hur vi tillhandahåller app-only-token via tilldelningen av [klientens autentiseringsuppgifter](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Tidigare kunde appar Hämta token för att anropa andra appar, oavsett om klient programmet fanns i klienten. Vi har uppdaterat det här beteendet så att resurser med en enda klient, ibland kallade webb-API: er, bara kan anropas av klient program som finns i resurs klienten.

Om din app inte finns i resurs klienten får du ett fel meddelande som säger `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` att du kan åtgärda det här problemet genom att skapa klientens tjänst huvud namn i klienten, antingen med hjälp av administratörs [medgivande slut punkten](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) eller [via PowerShell ](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), som säkerställer att klienten har gett appen behörighet att köras i klienten.

Mer information finns i [Vad är nytt för autentisering?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Det befintliga godkännandet mellan klienten och API: et fortsätter inte att krävas. Apparna bör fortfarande utföra sina egna verifierings kontroller.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nytt lösen ords lös inloggning till Azure AD med hjälp av FIDO2-säkerhetsnycklar

**Bastyp** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Azure AD-kunder kan nu ange principer för att hantera FIDO2 säkerhets nycklar för deras organisations användare och grupper. Slutanvändare kan även registrera sina säkerhets nycklar med hjälp av nycklarna för att logga in på sina Microsoft-konton på webbplatser på FIDO enheter, samt logga in på sina Azure AD-anslutna Windows 10-enheter.

Mer information finns i [Aktivera lösen ords lös inloggning för Azure AD (för hands version)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) för administratörs information och [Konfigurera säkerhets information för att använda en säkerhets nyckel (för hands version)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) för information om slutanvändare.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – juli 2019

**Bastyp** Ny funktion  
**Tjänste kategori:** Företagsappar  
**Produkt kapacitet:** Tredjepartsintegration

I juli 2019 har vi lagt till dessa 18 nya appar med stöd för federation i app-galleriet:

[Ungerboeck program vara](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [starkt mönster Omnichannel kontakt Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [smarta Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [LOOOP](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO-åtkomst för Ethidex Compliance Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [djupet](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [ Abstrakt](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [procent](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [FlipSnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial),, [](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial)PharmID [WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog-artefakt](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användar konto etablering för de här nyligen SaaS apparna som stöds

**Bastyp** Ny funktion  
**Tjänste kategori:** Företagsappar  
**Produkt kapacitet:** Övervakning och rapportering

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

**Bastyp** Ny funktion  
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services

Om du är trött på att hantera långa listor över IP-adresser och intervall kan du använda den nya **AzureActiveDirectoryDomainServices** Network Service tag i din Azure-nätverks säkerhets grupp för att skydda inkommande trafik till din Azure AD Domain Services virtuella nätverks under nät.

Mer information om den här nya service tag-koden finns i [nätverks säkerhets grupper för Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/network-considerations#default-network-service-group).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nya säkerhets granskningar för Azure AD Domain Services (offentlig för hands version)

**Bastyp** Ny funktion  
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services

Vi är glada över att kunna meddela att Azure AD Domain Service-säkerhetsgranskningen är offentlig för hands version. Med säkerhets granskning får du viktiga insikter om dina Authentication Services genom att strömma säkerhets gransknings händelser till riktade resurser, inklusive Azure Storage, Azure Log Analytics-arbetsytor och Azure Event Hub med Azure AD Domain Service Portal.

Mer information finns i [Aktivera säkerhets granskningar för Azure AD Domain Services (för hands version)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nya autentiseringsmetoder användning & insikter (offentlig för hands version)

**Bastyp** Ny funktion  
**Tjänste kategori:** Lösenordsåterställning via självbetjäning  
**Produkt kapacitet:** Övervakning och rapportering

Den nya autentiseringsmetoden användning & Insights-rapporter kan hjälpa dig att förstå hur funktioner som Azure Multi-Factor Authentication och återställning av lösen ord för självbetjäning registreras och används i din organisation, inklusive antalet registrerade användare för varje funktion, hur ofta lösen ords återställning via självbetjäning används för att återställa lösen ord och med vilken metod som återställningen sker.

Mer information finns i [användning av autentiseringsmetoder & insikter (för hands version)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nya säkerhets rapporter är tillgängliga för alla Azure AD-administratörer (offentlig för hands version)

**Bastyp** Ny funktion  
**Tjänste kategori:** Identity Protection  
**Produkt kapacitet:** Identitetssäkerhet och skydd

Alla Azure AD-administratörer kan nu välja banderollen överst i befintliga säkerhets rapporter, till exempel användare som har **flaggats för risk** rapport, för att börja använda den nya säkerhets upplevelsen som visas i rapporterna **riskfyllda användare** och riskfyllda **inloggningar** . Med tiden kommer alla säkerhets rapporter att flyttas från äldre versioner till de nya versionerna, med de nya rapporterna som ger dig följande ytterligare funktioner:

- Avancerad filtrering och sortering

- Mass åtgärder, t. ex. problem med att ignorera användar risk

- Bekräftelse av komprometterade eller säkrade entiteter

- Risk tillstånd, omfattar: Vid risk, avstängd, åtgärdad och bekräftat komprometterad

Mer information finns i rapporten om [riskfyllda användare](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-users-report) och [riskfyllda inloggningar](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-sign-ins-report).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nya säkerhets granskningar för Azure AD Domain Services (offentlig för hands version)

**Bastyp** Ny funktion  
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services

Vi är glada över att kunna meddela att Azure AD Domain Service-säkerhetsgranskningen är offentlig för hands version. Med säkerhets granskning får du viktiga insikter om dina Authentication Services genom att strömma säkerhets gransknings händelser till riktade resurser, inklusive Azure Storage, Azure Log Analytics-arbetsytor och Azure Event Hub med Azure AD Domain Service Portal.

Mer information finns i [Aktivera säkerhets granskningar för Azure AD Domain Services (för hands version)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Ny B2B direkt Federation med SAML/WS-utfodras (offentlig för hands version)

**Bastyp** Ny funktion  
**Tjänste kategori:** B2B  
**Produkt kapacitet:** B2B/B2C

Direkt federationen hjälper till att göra det enklare för dig att arbeta med partner vars IT-hanterade identitets lösning inte är Azure AD, genom att arbeta med identitets system som stöder SAML-eller WS-utfodras-standarder. När du har konfigurerat en direkt Federations relation med en partner, kan alla nya gäst användare som du bjuder in från domänen samar beta med dig som använder sitt befintliga organisations konto, vilket gör användar upplevelsen mer sömlös.

Mer information finns i [direkt Federation med AD FS och tredje parts leverantörer för gäst användare (för hands version)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användar konto etablering för de här nyligen SaaS apparna som stöds

**Bastyp** Ny funktion  
**Tjänste kategori:** Företagsappar  
**Produkt kapacitet:** Övervakning och rapportering

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

**Bastyp** Ny funktion  
**Tjänste kategori:** Grupphantering  
**Produkt kapacitet:** Samarbete

Nu när du skapar eller uppdaterar ett grupp namn från Azure AD-portalen, kontrollerar vi om du duplicerar ett befintligt grupp namn i din resurs. Om vi bestämmer att namnet redan används av en annan grupp, blir du ombedd att ändra ditt namn.

Mer information finns i [hantera grupper i Azure AD-portalen](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD stöder nu statiska frågeparametrar i svar (omdirigerings-URI: er)

**Bastyp** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Azure AD-appar kan nu registrera och använda URI: er för svar (omdirigering) med statiska frågeparametrar `https://contoso.com/oauth2?idp=microsoft`(till exempel) för OAuth 2,0-begäranden. Den statiska Frågeparametern omfattas av sträng matchning för svars-URI: er, precis som andra delar av svars-URI: n. Om det inte finns någon registrerad sträng som matchar URL-kodad omdirigerings-URI, avvisas begäran. Om svars-URI: n hittas används hela strängen för att omdirigera användaren, inklusive parametern för den statiska frågan.

Dynamiska svars-URI: er är fortfarande förbjudna eftersom de utgör en säkerhets risk och inte kan användas för att bevara tillståndsinformation i en autentiseringsbegäran. För det här ändamålet använder `state` du parametern.

För närvarande blockerar appens registrerings skärmar för Azure Portal fortfarande frågeparametrar. Du kan dock redigera appens manifest manuellt för att lägga till och testa frågeparametrar i din app. Mer information finns i [Vad är nytt för autentisering?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Aktivitets loggar (MS Graph API: er) för Azure AD är nu tillgängliga via PowerShell-cmdletar

**Bastyp** Ny funktion  
**Tjänste kategori:** Rapportering  
**Produkt kapacitet:** Övervakning och rapportering

Vi är glada över att kunna meddela att Azure AD aktivitets loggar (gransknings-och inloggnings rapporter) nu är tillgängliga via Azure AD PowerShell-modulen. Tidigare kunde du skapa egna skript med MS Graph API-slutpunkter och nu har vi utökat den möjligheten till PowerShell-cmdletar.

Mer information om hur du använder dessa cmdlets finns i [Azure AD PowerShell-cmdlets för rapportering](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Uppdaterade filter kontroller för gransknings-och inloggnings loggar i Azure AD

**Bastyp** Ändrad funktion  
**Tjänste kategori:** Rapportering  
**Produkt kapacitet:** Övervakning och rapportering

Vi har uppdaterat rapporterna för gransknings-och inloggnings loggar så att du nu kan använda olika filter utan att behöva lägga till dem som kolumner i rapport skärmarna. Dessutom kan du bestämma hur många filter som ska visas på skärmen. De här uppdateringarna fungerar tillsammans för att göra dina rapporter lättare att läsa och mer begränsade till dina behov.

Mer information om de här uppdateringarna finns i [filtrera gransknings loggar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) och [filtrera inloggnings aktiviteter](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>2019 juni

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>New riskDetections-API för Microsoft Graph (offentlig för hands version)

**Bastyp** Ny funktion  
**Tjänste kategori:** Identity Protection  
**Produkt kapacitet:** Identitetssäkerhet och skydd

Vi är glada över att kunna presentera den nya riskDetections-API: n för Microsoft Graph nu i offentlig för hands version. Du kan använda den här nya API: n för att visa en lista över organisationens identitet skydd – relaterad användare och inloggnings risk identifieringar. Du kan också använda det här API: et för att effektivt fråga dina risk identifieringar, inklusive information om identifierings typ, status, nivå och mycket annat.

Mer information finns i [referens dokumentationen](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)för riskhanterings-API.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – juni 2019

**Bastyp** Ny funktion  
**Tjänste kategori:** Företagsappar  
**Produkt kapacitet:** Tredjepartsintegration

I juni 2019 har vi lagt till dessa 22 nya appar med stöd för federation i app-galleriet:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN-klient](https://portal.azure.com/), [kostnadin](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Hjälp](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial)verktyg för hjälp, [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz i bilen Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure-konsolen](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML-autentisering](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager för Oracle Inköps handel, Oracle Access Manager för Oracle E-Business Suite, Oracle-IDCS för E-Business Suite, Oracle-IDCS för, Oracle IDCS för JD Edwards

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användar konto etablering för de här nyligen SaaS apparna som stöds

**Bastyp** Ny funktion  
**Tjänste kategori:** Företagsappar  
**Produkt kapacitet:** Övervakning och rapportering

Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Förhindra](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Mottagare](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Visa real tids förloppet för Azure AD Provisioning-tjänsten

**Bastyp** Ändrad funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Livscykelhantering för identiteter

Vi har uppdaterat Azure ADs etablerings miljö för att inkludera en ny förlopps indikator som visar hur långt du befinner dig i användar etablerings processen. Den här uppdaterade upplevelsen ger också information om antalet användare som etablerats under den aktuella cykeln, samt hur många användare som har etablerats till dagens datum.

Mer information finns i [kontrol lera status för användar etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Företags anpassning visas nu på skärmen Logga ut och fel

**Bastyp** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Vi har uppdaterat Azure AD så att ditt företags varumärke nu visas på skärmen Logga ut och fel och på inloggnings sidan. Du behöver inte göra något för att aktivera den här funktionen, Azure AD använder bara de till gångar som du redan har konfigurerat i **företags anpassnings** delen av Azure Portal.

Mer information om hur du konfigurerar din företags anpassning finns i [lägga till anpassning till din organisations Azure Active Directory sidor](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure Multi-Factor Authentication-servern (MFA) är inte längre tillgänglig för nya distributioner

**Bastyp** Inaktuell  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** Identitetssäkerhet och skydd

Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication i organisationen måste nu använda molnbaserad Azure Multi-Factor Authentication. Kunder som aktiverade MFA Server tidigare än 1 juli ser ingen ändring. Du kommer fortfarande att kunna ladda ned den senaste versionen, Hämta framtida uppdateringar och generera autentiseringsuppgifter för aktivering.

Mer information finns i [komma igång med Azure Multi-Factor Authentication Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Mer information om molnbaserad Azure Multi-Factor Authentication finns i [Planera en molnbaserad Azure Multi-Factor Authentication-distribution](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Maj 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Tjänst ändring: Framtida stöd för endast TLS 1,2-protokoll i Application Proxy-tjänsten

**Bastyp** Förändringsplan  
**Tjänste kategori:** App Proxy  
**Produkt kapacitet:** Åtkomstkontroll

För att tillhandahålla bästa möjliga kryptering för våra kunder begränsar vi åtkomsten till endast TLS 1,2-protokoll på Application Proxy-tjänsten. Den här ändringen distribueras gradvis till kunder som redan använder TLS 1,2-protokoll, så du bör inte se några ändringar.

Utfasningen av TLS 1,0 och TLS 1,1 sker den 31 augusti 2019, men vi ger ytterligare Avancerat meddelande så att du har tid att förbereda dig för den här ändringen. För att förbereda för den här ändringen kontrollerar du att kombinationen av klient-och webb läsar Server, inklusive alla klienter som användarna använder för att komma åt appar som publicerats via programproxy, har uppdaterats för att använda TLS 1,2-protokollet för att upprätthålla anslutningen till programmet Proxy service. Mer information finns i [lägga till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Använd rapporten användning och insikter för att visa dina app-relaterade inloggnings data

**Bastyp** Ny funktion  
**Tjänste kategori:** Företagsappar  
**Produkt kapacitet:** Övervakning och rapportering

Nu kan du använda rapporten användning och insikter som finns i avsnittet **företags program** i Azure Portal för att få en programinriktad vy över dina inloggnings data, inklusive information om:

- Mest använda appar för din organisation

- Appar med de mest misslyckade inloggnings programmen

- Vanligaste inloggnings fel för varje app

Mer information om den här funktionen finns i [användnings-och insikts rapport i Azure Active Directory Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatisera din användar etablering för molnappar med Azure AD

**Bastyp** Ny funktion  
**Tjänste kategori:** Företagsappar  
**Produkt kapacitet:** Övervakning och rapportering

Följ de här nya självstudierna för att använda Azure AD Provisioning-tjänsten för att automatisera skapande, borttagning och uppdatering av användar konton för följande molnbaserade appar:

- [Samstämm](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Du kan också följa den här nya [guiden Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), som innehåller information om hur du etablerar grupp objekt.

Mer information om hur du bättre skyddar din organisation genom att tillhandahålla automatiserade användar konton finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Identitets säkra poäng är nu tillgängligt i Azure AD (allmän tillgänglighet)

**Bastyp** Ny funktion  
**Tjänste kategori:** Gäller inte  
**Produkt kapacitet:** Identitetssäkerhet och skydd

Nu kan du övervaka och förbättra din position för identiteter genom att använda funktionen identitet säkra poäng i Azure AD. I funktionen för identitetens säkra poäng används en enda instrument panel för att hjälpa dig:

- Objektivt mäta din position för identiteter baserat på en poäng på mellan 1 och 223.

- Planera för förbättringar av din identitets säkerhet

- Granska lyckade säkerhets förbättringar

Mer information om funktionen för identitets säkerhets Poäng finns [i vad är identitetens säkra poäng i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Ny Appregistreringar upplevelse är nu tillgänglig (allmän tillgänglighet)

**Bastyp** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Developer-upplevelse

Den nya [Appregistreringar](https://aka.ms/appregistrations) upplevelsen är nu allmänt tillgänglig. Den här nya upplevelsen innehåller alla viktiga funktioner som du är van vid från Azure Portal och program registrerings portalen och som ökar genom att:

- **Bättre hantering av appar.** I stället för att se dina appar över olika portaler kan du nu se alla dina appar på en plats.

- **Förenklad registrering av appar.** Från den förbättrade navigerings upplevelsen av förbättringar-behörigheten är det nu enklare att registrera och hantera dina appar.

- **Mer detaljerad information.** Du hittar mer information om din app, inklusive snabb starts guider med mera.

Mer information finns i [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/) och [Appregistreringar Experience är nu allmänt tillgänglig!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blogg meddelande.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nya funktioner som är tillgängliga i API för riskfyllda användare för identitets skydd

**Bastyp** Ny funktion  
**Tjänste kategori:** Identity Protection  
**Produkt kapacitet:** Identitetssäkerhet och skydd

Vi är glada över att kunna meddela att du nu kan använda API: erna för riskfyllda användare för att hämta användares risk historik, ignorera riskfyllda användare och bekräfta användare som komprometterade. Den här ändringen hjälper dig att effektivt uppdatera risk statusen för dina användare och förstå deras risk historik.

Mer information finns i [referens dokumentationen för riskfyllda användare](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – maj 2019

**Bastyp** Ny funktion  
**Tjänste kategori:** Företagsappar  
**Produkt kapacitet:** Tredjepartsintegration

I maj 2019 har vi lagt till dessa 21 nya appar med stöd för federation i app-galleriet:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [riktiga länkar](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial)displayer, [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales](https://toutapp.com/login)-engagerande, [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [system](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [META4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Arbets plats](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobolt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), webMethods [API-moln](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [näring](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Förbättrade grupper för att skapa och hantera funktioner i Azure AD-portalen

**Bastyp** Ny funktion  
**Tjänste kategori:** Grupphantering  
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

**Bastyp** Ändrad funktion  
**Tjänste kategori:** Grupphantering  
**Produkt kapacitet:** Samarbete

Administratörer kan nu konfigurera en namngivnings princip för Office 365-grupper med hjälp av Azure AD-portalen. Den här ändringen hjälper till att upprätthålla konsekventa namngivnings konventioner för Office 365-grupper som skapats eller redigerats av användare i din organisation.

Du kan konfigurera namngivnings principen för Office 365-grupper på två olika sätt:

- Definiera prefix eller suffix som läggs till automatiskt i ett grupp namn.

- Ladda upp en anpassad uppsättning blockerade ord för din organisation, vilket inte är tillåtet i grupp namn (till exempel "VD, löner, HR").

Mer information finns i [framtvinga en namngivnings princip för Office 365-grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API-slutpunkter är nu tillgängliga för Azure AD-aktivitets loggar (allmän tillgänglighet)

**Bastyp** Ändrad funktion  
**Tjänste kategori:** Rapportering  
**Produkt kapacitet:** Övervakning och rapportering

Vi är glada över att kunna meddela allmän tillgänglighet för stöd för Microsoft Graph API-slutpunkter för Azure AD-aktivitets loggar. I den här versionen kan du nu använda version 1,0 av både gransknings loggarna i Azure AD och inloggnings loggarna.

Mer information finns i [Översikt över Azure AD audit log API](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Administratörer kan nu använda villkorlig åtkomst för den kombinerade registrerings processen (offentlig för hands version)

**Bastyp** Ny funktion  
**Tjänste kategori:** Villkorad åtkomst  
**Produkt kapacitet:** Identitetssäkerhet och skydd  

Administratörer kan nu skapa principer för villkorlig åtkomst för användning av den kombinerade registrerings sidan. Detta omfattar att tillämpa principer för att tillåta registrering om:

- Användare finns i ett betrott nätverk.

- Användare är en låg inloggnings risk.

- Användare finns på en hanterad enhet.

- Användarna godkänner organisationens användnings villkor (TOU).

Om du vill ha mer information om villkorlig åtkomst och återställning av lösen ord kan du se [blogg inlägget för den villkorliga åtkomsten i blogg inlägget för registrering i Azure AD kombinerat MFA och lösen ords återställning](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Mer information om principer för villkorlig åtkomst för den kombinerade registrerings processen finns i [principer för villkorlig åtkomst för kombinerad registrering](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Mer information om funktionen användnings villkor för Azure AD finns i Azure Active Directory användnings [villkor](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>April 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-in-refreshed-azure-ad-identity-protection"></a>Ny identifiering av Azure AD Threat Intelligence är nu tillgängligt i uppdaterade Azure AD Identity Protection

**Bastyp** Ny funktion  
**Tjänste kategori:** Azure AD Identity Protection  
**Produkt kapacitet:** Identitetssäkerhet och skydd

Identifiering av Azure AD Threat Intelligence är nu tillgängligt i den uppdaterade Azure AD Identity Protection. Med den här nya funktionen kan du ange en användar aktivitet som är ovanlig för en viss användare eller som är konsekvent med kända angrepps mönster baserade på Microsofts interna och externa hot information.

Mer information om den uppdaterade versionen av Azure AD Identity Protection finns i de [fyra viktiga Azure AD Identity Protection förbättringarna finns nu i den offentliga för hands](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) versionen av bloggen och [vad som är Azure Active Directory Identity Protection (uppdaterat)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) artiklar. Mer information om identifiering av Azure AD Threat Intelligence finns i artikeln [Azure Active Directory Identity Protection risk händelser](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Hantering av Azure AD-berättigande är nu tillgängligt (offentlig för hands version)

**Bastyp** Ny funktion  
**Tjänste kategori:** Identity Governance  
**Produkt kapacitet:** Identity Governance

Hantering av Azure AD-hantering, nu i offentlig för hands version, hjälper kunderna att delegera hantering av åtkomst paket, som definierar hur anställda och affärs partner kan begära åtkomst, vem som måste godkänna och hur länge de har åtkomst. Åtkomst paket kan hantera medlemskap i Azure AD-och Office 365-grupper, roll tilldelningar i företags program och roll tilldelningar för SharePoint Online-webbplatser. Läs mer om hantering av rättigheter i [Översikt över hantering av Azure AD-rättigheter](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Om du vill veta mer om bredden på Azure AD Identity Governance funktioner, inklusive Privileged Identity Management, åtkomst granskningar och användnings villkor, se [Vad är Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurera en namngivnings princip för Office 365-grupper i Azure AD portal (offentlig för hands version)

**Bastyp** Ny funktion  
**Tjänste kategori:** Grupphantering  
**Produkt kapacitet:** Samarbete

Administratörer kan nu konfigurera en namngivnings princip för Office 365-grupper med hjälp av Azure AD-portalen. Den här ändringen hjälper till att upprätthålla konsekventa namngivnings konventioner för Office 365-grupper som skapats eller redigerats av användare i din organisation.

Du kan konfigurera namngivnings principen för Office 365-grupper på två olika sätt:

- Definiera prefix eller suffix som läggs till automatiskt i ett grupp namn.

- Ladda upp en anpassad uppsättning blockerade ord för din organisation, vilket inte är tillåtet i grupp namn (till exempel "VD, löner, HR").

Mer information finns i [framtvinga en namngivnings princip för Office 365-grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD-aktivitets loggar är nu tillgängliga i Azure Monitor (allmän tillgänglighet)

**Bastyp** Ny funktion  
**Tjänste kategori:** Rapportering  
**Produkt kapacitet:** Övervakning och rapportering

Vi presenterar en ny insikts funktion i Log Analytics för att hjälpa dig att åtgärda dina synpunkter på visualiseringar med aktivitets loggarna i Azure AD. Den här funktionen hjälper dig att få insikter om dina Azure AD-resurser med hjälp av våra interaktiva mallar, som kallas arbets böcker. Dessa färdiga arbets böcker kan ge information om appar eller användare och inkludera:

- **Inloggningar.** Innehåller information för appar och användare, inklusive inloggnings plats, klient och version för operativ systemet eller webbläsare och antalet lyckade eller misslyckade inloggningar.

- **Äldre autentisering och villkorlig åtkomst.** Innehåller information för appar och användare som använder äldre autentisering, inklusive Multi-Factor Authentication-användning som utlöses av principer för villkorlig åtkomst, appar som använder principer för villkorlig åtkomst och så vidare.

- **Analys av inloggnings problem.** Hjälper dig att avgöra om inloggnings felen inträffar på grund av en användar åtgärd, princip problem eller din infrastruktur.

- **Anpassade rapporter.** Du kan skapa nya eller redigera befintliga arbets böcker som hjälper dig att anpassa insikter-funktionen för din organisation.

Mer information finns i [så här använder du Azure Monitor-arbetsböcker för Azure Active Directory-rapporter](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – april 2019

**Bastyp** Ny funktion  
**Tjänste kategori:** Företagsappar  
**Produkt kapacitet:** Tredjepartsintegration

I april 2019 har vi lagt till dessa 21 nya appar med stöd för federation i app-galleriet:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), Citrix NetScaler, [shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Bänkning](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), PageDNA, [](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial)EduBrite [LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial) [RStudio Connect ](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [spetsig anslutning](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (rollbaserad SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent egendoms hantering](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [INDIGGO](https://indiggolead.com/)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nya frekvens alternativ för åtkomst granskningar och flera roll val

**Bastyp** Ny funktion  
**Tjänste kategori:** Åtkomstgranskningar  
**Produkt kapacitet:** Identity Governance

Nya uppdateringar i åtkomst granskningar i Azure AD gör att du kan:

- Ändra frekvensen för dina åtkomst granskningar till **halv varje år**, förutom de tidigare befintliga alternativen varje vecka, varje månad, kvartals vis och varje år.

- Välj flera Azure AD-och Azure-resurs roller när du skapar en enda åtkomst granskning. I den här situationen konfigureras alla roller med samma inställningar och alla granskare meddelas på samma tid.

Mer information om hur du skapar en åtkomst granskning finns i [skapa en åtkomst granskning av grupper eller program i åtkomst granskningar för Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-postaviserings system överförs, skickar ny e-postavsändar information för vissa kunder

**Bastyp** Ändrad funktion  
**Tjänste kategori:** AD Sync  
**Produkt kapacitet:** Plattform

Azure AD Connect håller på att överföra våra e-postvarnings system, vilket potentiellt visar vissa kunder en ny e-postsändare. För att lösa detta måste du lägga `azure-noreply@microsoft.com` till i din organisations lista över tillåtna eller så kan du inte fortsätta att ta emot viktiga aviseringar från Office 365, Azure eller dina Sync-tjänster.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Ändringar av UPN-suffix har nu slutförts mellan federerade domäner i Azure AD Connect

**Bastyp** Korrigerat  
**Tjänste kategori:** AD Sync  
**Produkt kapacitet:** Plattform

Du kan nu ändra en användares UPN-suffix från en federerad domän till en annan federerad domän i Azure AD Connect. Den här korrigeringen innebär att du inte längre bör uppleva FederatedDomainChangeError-felmeddelande under synkroniseringsprocessen eller få ett meddelande om att det inte går att uppdatera det här objektet i Azure Active Directory eftersom attributet [ FederatedUser. UserPrincipalName] är inte giltigt. Uppdatera värdet i dina lokala katalog tjänster.

Mer information finns i [fel sökning av fel under synkronisering](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Ökad säkerhet med hjälp av app Protection-baserad princip för villkorlig åtkomst i Azure AD (offentlig för hands version)

**Bastyp** Ny funktion  
**Tjänste kategori:** Villkorad åtkomst  
**Produkt kapacitet:** Identitetssäkerhet och skydd

App Protection-baserad villkorlig åtkomst är nu tillgänglig med hjälp av principen **Kräv app-skydd** . Den här nya principen hjälper till att öka din organisations säkerhet genom att bidra till att förhindra:

- Användare får åtkomst till appar utan en Microsoft Intune-licens.

- Användare kan inte hämta en Microsoft Intune skydds princip för appar.

- Användare får åtkomst till appar utan en konfigurerad Microsoft Intune App Protection-princip.

Mer information finns i [så här kräver du app Protection-princip för Cloud app-åtkomst med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nytt stöd för enkel inloggning och villkorlig åtkomst i Azure AD i Microsoft Edge (offentlig för hands version)

**Bastyp** Ny funktion  
**Tjänste kategori:** Villkorad åtkomst  
**Produkt kapacitet:** Identitetssäkerhet och skydd

Vi har förbättrat Azure AD-supporten för Microsoft Edge, inklusive att tillhandahålla nytt stöd för enkel inloggning och villkorlig åtkomst i Azure AD. Om du tidigare har använt Microsoft Intune Managed Browser kan du nu använda Microsoft Edge i stället.

Mer information om hur du konfigurerar och hanterar enheter och appar med hjälp av villkorlig åtkomst finns i [Kräv hanterade enheter för Cloud app-åtkomst med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) och [Kräv godkända klient program för Cloud app-åtkomst med villkorlig åtkomst ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Mer information om hur du hanterar åtkomst med hjälp av Microsoft Edge med Microsoft Intune-principer finns i [Hantera Internet åtkomst med en Microsoft Intune-skyddad webbläsare](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Mars 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Identitets upplevelse ramverk och anpassad princip support i Azure Active Directory B2C är nu tillgänglig (GA)

**Bastyp** Ny funktion  
**Tjänste kategori:** B2C – konsumentidentitetshantering  
**Produkt kapacitet:** B2B/B2C

Nu kan du skapa anpassade principer i Azure AD B2C, inklusive följande uppgifter, som stöds i stor skala och under vårt Azure SLA:

- Skapa och ladda upp användar transporter för anpassad autentisering med hjälp av anpassade principer.

- Beskriv användar resan steg för steg som utbyten mellan anspråks leverantörer.

- Definiera villkorlig grenning i användar resor.

- Transformera och mappa anspråk för användning i real tids beslut och kommunikation.

- Använd REST API-aktiverade tjänster i dina anpassade användar resor för autentisering. Till exempel med e-postleverantörer, CRMs och tillverkarspecifika auktoriserings system.

- Federera med identitets leverantörer som är kompatibla med OpenIDConnect-protokollet. Till exempel med flera klient organisationer för Azure AD, sociala konto leverantörer eller två-Factor Verification-leverantörer.

För ytterligare information om hur du skapar anpassade principer, se Developer- [anteckningar för anpassade principer i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) och Läs [Alex-Simons blogg inlägg, inklusive fallstudier](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – mars 2019

**Bastyp** Ny funktion  
**Tjänste kategori:** Företagsappar  
**Produkt kapacitet:** Tredjepartsintegration

I mars 2019 har vi lagt till dessa 14 nya appar med stöd för federation i app-galleriet:

[ISEC7 Mobile Exchange delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [förklarings-baserade gransknings system](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool prestanda](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode ](https://cinode.com/), [Iris intranät](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial)-horisonter, [aktivitet](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nya Zscaler-och Atlassian-etablerings anslutningar i Azure AD-galleriet – mars 2019

**Bastyp** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Tredjepartsintegration

Automatisera att skapa, uppdatera och ta bort användar konton för följande appar:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler två](https://aka.ms/ZscalerTwoProvisioning), [Zscaler tre](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Mer information om hur du bättre skyddar din organisation genom att tillhandahålla automatiserade användar konton finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Återställa och hantera dina borttagna Office 365-grupper i Azure AD-portalen

**Bastyp** Ny funktion  
**Tjänste kategori:** Grupphantering  
**Produkt kapacitet:** Samarbete

Nu kan du Visa och hantera dina borttagna Office 365-grupper från Azure AD-portalen. Den här ändringen hjälper dig att se vilka grupper som är tillgängliga för återställning, tillsammans med att låta dig ta bort alla grupper som inte behövs i din organisation Permanent.

Mer information finns i [återställa inaktuella eller borttagna grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Enkel inloggning är nu tillgängligt för Azure AD SAML-skyddade lokala appar via programproxy (offentlig för hands version)

**Bastyp** Ny funktion  
**Tjänste kategori:** App Proxy  
**Produkt kapacitet:** Åtkomstkontroll

Nu kan du tillhandahålla enkel inloggning (SSO) för lokala, SAML-autentiserade appar, tillsammans med fjärråtkomst till dessa appar via programproxyn. Mer information om hur du konfigurerar SAML SSO med dina lokala appar finns i [SAML enkel inloggning för lokala program med Application Proxy (för hands version)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Klient program i begär ande slingor avbryts för att förbättra tillförlitligheten och användar upplevelsen

**Bastyp** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Klient program kan felaktigt skicka hundratals av samma inloggnings begär Anden under en kort tids period. Dessa förfrågningar, oavsett om de är lyckade eller inte, bidrar till en dåligt användar upplevelse och förhöjda arbets belastningar för IDP, ökande svars tid för alla användare och för att minska tillgängligheten för IDP.

Den här uppdateringen skickar `invalid_grant` ett fel `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` : till klient program som utfärdar dubbla förfrågningar flera gånger under en kort tids period, utöver normal drifts omfattning. Klient program som stöter på det här problemet bör visa en interaktiv prompt som kräver att användaren loggar in igen. Mer information om den här ändringen och hur du åtgärdar din app om det här felet påträffas finns i [Vad är nytt för autentisering?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nya gransknings loggar användar upplevelsen är nu tillgänglig

**Bastyp** Ändrad funktion  
**Tjänste kategori:** Rapportering  
**Produkt kapacitet:** Övervakning och rapportering

Vi har skapat en ny sida med **gransknings loggar** för Azure AD som hjälper till att förbättra både läsbarhet och hur du söker efter din information. Om du vill se sidan nya **gransknings loggar** väljer du **gransknings loggar** i avsnittet **aktivitet** i Azure AD.

![Sidan nya gransknings loggar med exempel information](media/whats-new/audit-logs-page.png)

Mer information om sidan nya **gransknings loggar** finns [i gransknings aktivitets rapporter i Azure Active Directory-portalen](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nya varningar och rikt linjer som hjälper dig att förhindra oavsiktlig administratörs utelåsning från felkonfigurerade principer för villkorlig åtkomst

**Bastyp** Ändrad funktion  
**Tjänste kategori:** Villkorad åtkomst  
**Produkt kapacitet:** Identitetssäkerhet och skydd

För att förhindra att administratörer oavsiktligt låser sig själva ut från sina egna klienter via felkonfigurerade principer för villkorlig åtkomst har vi skapat nya varningar och uppdaterad vägledning i Azure Portal. Mer information om den nya vägledningen finns i [Vad är tjänst beroenden i Azure Active Directory villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Förbättrade användnings villkor för slutanvändare på mobila enheter

**Bastyp** Ändrad funktion  
**Tjänste kategori:** Användningsvillkor  
**Produkt kapacitet:** Styrning

Vi har uppdaterat våra befintliga användnings villkor för att hjälpa till att förbättra hur du granskar och godkänner användnings villkoren på en mobil enhet. Nu kan du zooma in och ut, gå tillbaka, ladda ned informationen och välja hyperlänkar. Mer information om de uppdaterade användnings villkoren finns i Azure Active Directory användnings [villkor](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nya hämtnings möjligheter för Azure AD-aktivitets loggar är tillgängliga

**Bastyp** Ändrad funktion  
**Tjänste kategori:** Rapportering  
**Produkt kapacitet:** Övervakning och rapportering

Nu kan du hämta stora mängder aktivitets loggar direkt från Azure Portal. Med den här uppdateringen kan du:

- Hämta upp till 250 000 rader.

- Få ett meddelande när nedladdningen är klar.

- Anpassa ditt fil namn.

- Ta reda på utdataformatet, antingen JSON eller CSV.

Mer information om den här funktionen finns i [snabb start: Hämta en gransknings rapport med hjälp av Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Icke-bakåtkompatibel ändring: Uppdateringar av villkors utvärderingen av Exchange ActiveSync (EAS)

**Bastyp** Förändringsplan  
**Tjänste kategori:** Villkorad åtkomst  
**Produkt kapacitet:** Åtkomstkontroll

Vi håller på att uppdatera hur Exchange ActiveSync (EAS) utvärderar följande villkor:

- Användar plats, baserat på land, region eller IP-adress

- Inloggningsrisk

- Enhetsplattform

Om du tidigare har använt dessa villkor i dina principer för villkorlig åtkomst bör du tänka på att villkors beteendet kan ändras. Om du till exempel tidigare använde villkoret användar plats i en princip kan du se till att principen nu hoppas över baserat på användarens plats.

---

## <a name="february-2019"></a>Februari 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurerbar Azure AD SAML-tokenkryptering (allmänt tillgänglig förhandsversion) 

**Bastyp** Ny funktion  
**Tjänste kategori:** Företagsappar  
**Produkt kapacitet:** SSO

Nu kan du konfigurera en SAML-app som stöds för att ta emot krypterade SAML-token. När Azure AD är konfigurerat och används med en app, krypterar Azure AD de utgivna SAML-beställarna med en offentlig nyckel som hämtats från ett certifikat som lagras i Azure AD.

Mer information om hur du konfigurerar din kryptering för SAML-token finns i [Konfigurera Azure AD SAML token](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)Encryption.

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Skapa en åtkomstgranskning för grupper eller appar som använder Azure AD-åtkomstgranskningar

**Bastyp** Ny funktion  
**Tjänste kategori:** Åtkomstgranskningar  
**Produkt kapacitet:** Styrning

Du kan nu inkludera flera grupper eller appar i en enda Azure AD Access-granskning för grupp medlemskap eller program tilldelning. Åtkomst granskningar med flera grupper eller appar konfigureras med samma inställningar och alla granskare som ingår meddelas på samma gång.

Mer information om hur du skapar en åtkomst granskning med hjälp av åtkomst granskningar i Azure AD finns i [skapa en åtkomst granskning av grupper eller program i åtkomst granskningar för Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nya federerade appar tillgängliga i Azure AD-appgalleriet – februari 2019

**Bastyp** Ny funktion  
**Tjänste kategori:** Företagsappar  
**Produkt kapacitet:** Tredjepartsintegration
 
I februari 2019 har vi lagt till dessa 27 nya appar med stöd för federation i app-galleriet:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [fat](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7)-finger [](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), netstack [, Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Behörighet klicka, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [seismisk](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [dela en dröm](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Knowledge Var LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope-data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [NetOp-portalen](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud av gener](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp produktivitets plattform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Förbättrad kombinerad MFA SSPR-registrering

**Bastyp** Ändrad funktion  
**Tjänste kategori:** Lösenordsåterställning via självbetjäning  
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

För ytterligare information om den förbättrade registrerings upplevelsen, se de häftiga förbättringarna i [registrerings upplevelsen av Azure AD kombinerat MFA och lösen ords återställning](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Principhanteringsupplevelsen för användarflöden har uppdaterats

**Bastyp** Ändrad funktion  
**Tjänste kategori:** B2C – konsumentidentitetshantering  
**Produkt kapacitet:** B2B/B2C

Vi har uppdaterat processen för att skapa och hantera principer för användar flöden (tidigare kallade inbyggda principer) enklare. Den nya upplevelsen är nu standard för alla dina Azure AD-klienter.

Du kan ge ytterligare feedback och förslag genom att använda ikonerna leende eller bister min i avsnittet **Skicka feedback för oss** överst på Portal skärmen.

Mer information om den nya princip hanterings upplevelsen finns i [Azure AD B2C nu har Java Script-anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) -bloggen.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Välj specifika sidelementsversioner som tillhandahålls av Azure AD B2C

**Bastyp** Ny funktion  
**Tjänste kategori:** B2C – konsumentidentitetshantering  
**Produkt kapacitet:** B2B/B2C

Nu kan du välja en speciell version av sid elementen som tillhandahålls av Azure AD B2C. Genom att välja en viss version kan du testa dina uppdateringar innan de visas på en sida och du kan få förutsägbara beteenden. Dessutom kan du välja att använda vissa sid versioner för att tillåta anpassade JavaScript-anpassningar. Om du vill aktivera den här funktionen går du till sidan **Egenskaper** i dina användar flöden.

Mer information om hur du väljer vissa versioner av sid element finns i avsnittet [Azure AD B2C nu har Java Script-anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) -bloggen.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurerbara lösenordskrav för slutanvändare för B2C (GA)

**Bastyp** Ny funktion  
**Tjänste kategori:** B2C – konsumentidentitetshantering  
**Produkt kapacitet:** B2B/B2C

Nu kan du konfigurera din organisations lösen ords komplexitet för dina slutanvändare, i stället för att behöva använda din interna lösen ords princip för Azure AD. Från **egenskaps** bladet för dina användar flöden (kallades tidigare dina inbyggda principer) kan du välja ett lösen ords komplexitet som är **enkelt** eller **starkt**, eller så kan du skapa en **anpassad** uppsättning krav.

Mer information om konfiguration av lösen ords komplexitets krav finns i [Konfigurera komplexitets krav för lösen ord i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nya standardmallar för anpassade autentiseringsupplevelser

**Bastyp** Ny funktion  
**Tjänste kategori:** B2C – konsumentidentitetshantering  
**Produkt kapacitet:** B2B/B2C

Du kan använda våra nya standardmallar som finns på bladet sidlayouter i dina användar flöden (tidigare kallade inbyggda principer) för att skapa en anpassad anpassad autentisering för dina användare.

Mer information om hur du använder mallarna finns i [Azure AD B2C nu har Java Script-anpassning och många nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---
