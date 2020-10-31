---
title: Nyheter Viktig information – Azure Active Directory | Microsoft Docs
description: Lär dig vad som är nytt med Azure Active Directory. till exempel senaste viktig information, kända problem, fel korrigeringar, inaktuella funktioner och kommande ändringar.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f08efc8a10c50dcfcc6da884396dea88695dbb2a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096027"
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?

>Bli informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL: en `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` i din ![ RSS feed reader-ikon ](./media/whats-new/feed-icon-16x16.png) feed reader.

Azure AD tar emot förbättringar kontinuerligt. För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar
- Föråldrade funktioner
- Planer för ändringar

Den här sidan uppdateras varje månad, så du kan uppdatera den regelbundet. Om du söker efter objekt som är äldre än sex månader kan du hitta dem i [arkivera efter nyheter i Azure Active Directory](whats-new-archive.md).

---
## <a name="october-2020"></a>Oktober 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Azure AD lokala hybrid agenter som påverkas av ändringar i Azure TLS-certifikat

**Typ:** Planera för ändring  
**Tjänste kategori:** EJ TILLÄMPLIGT  
**Produkt kapacitet:** Systemet

Microsoft uppdaterar Azure-tjänster för att använda TLS-certifikat från en annan uppsättning rot certifikat utfärdare (ca: er). Den här uppdateringen beror på att de aktuella CA-certifikaten inte uppfyller något av kraven för CA/webbläsarens forum bas linje. Den här ändringen påverkar Azure AD hybrid-agenter som installerats lokalt och har en fast lista över rot certifikat och måste uppdateras för att lita på nya certifikat utfärdare.

Den här ändringen resulterar i avbrott i tjänsten om du inte omedelbart vidtar åtgärder. Dessa agenter innehåller [programproxy-anslutningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) för fjärråtkomst till lokala, genom [strömnings](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) agenter som gör att användarna kan logga in på program med samma lösen ord och för [hands versioner av moln](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) tjänster som utför AD till Azure AD Sync. 

Om du har en miljö med brand Väggs regler som tillåter utgående samtal till enbart en speciell lista över återkallade certifikat (CRL) måste du tillåta följande CRL-och OCSP-URL: er. Fullständig information om ändrings-och CRL-och OCSP-URL: er för att ge åtkomst till finns i  [Azure TLS-certifikat ändringar](../../security/fundamentals/tls-certificate-changes.md).

## <a name="september-2020"></a>September 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nya etablerings anslutningar i Azure AD Application Gallery – september 2020

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Säkerhets medvetenhet om Webroot](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Moln etablering, offentlig för hands versions uppdatering

**Typ:** Ny funktion  
**Tjänste kategori:** **Produkt kapacitet** för Azure AD Cloud-etablering: identitets livs cykel hantering
 
Azure AD Connect moln etablering offentlig för hands version uppdatera funktioner två större förbättringar som har utvecklats från kundfeedback: 

- Attribut mappnings upplevelse via Azure Portal

    Med den här funktionen kan IT-administratörer mappa användar-, grupp-eller kontakt-attribut från AD till Azure AD med olika mappnings typer som finns idag. Attributmappning är en funktion som används för att standardisera värdena för attributen som flödar från Active Directory till Azure Active Directory. En kan bestämma om attributvärdet ska mappas direkt som det är från AD till Azure AD eller om du använder uttryck för att transformera attributvärdena när du konfigurerar användare. [Läs mer](../cloud-provisioning/how-to-attribute-mapping.md)

- Etablering på begäran eller testa användar upplevelsen

    När du har konfigurerat konfigurationen kanske du vill testa för att se om användar omvandlingen fungerar som förväntat innan den tillämpas på alla användare i omfånget. Med etablering på begäran kan IT-administratörer ange unikt namn för en AD-användare och se om de synkroniseras som förväntat. Etablering på begäran är ett bra sätt att se till att de mappningar för attribut som du tidigare fungerade som förväntat. [Läs mer](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Granskad BitLocker-återställning i Azure AD – offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** Enhets åtkomst hantering  
**Produkt kapacitet:** Hantering av enhetens livs cykel
 
När IT-administratörer eller slutanvändare läser de BitLocker-återställnings nycklar som de har åtkomst till, skapar Azure Active Directory nu en Gransknings logg som samlar in vem som har åtkomst till återställnings nyckeln. Samma granskning ger information om enheten som BitLocker-nyckeln var kopplad till.

Slutanvändare kan [komma åt sina återställnings nycklar via mitt konto](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). IT-administratörer kan komma åt återställnings nycklar via [nyckel-API: t för BitLocker-återställning i beta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) eller via Azure AD-portalen. Mer information finns i [Visa eller kopiera BitLocker-nycklar i Azure AD-portalen](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>Den inbyggda rollen team enheters administratör

**Typ:** Ny funktion  
**Tjänste kategori:** RBAC  
**Produkt kapacitet:** Access Control
 
Användare med rollen [team enheter administratör](../roles/permissions-reference.md#teams-devices-administrator) kan hantera [team-certifierade enheter](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) från Team administrations centret. 

Med den här rollen kan användaren Visa alla enheter på ett enkelt sätt, med möjlighet att söka efter och filtrera enheter. Användaren kan också kontrol lera information om varje enhet, inklusive inloggat konto och enhetens märke och modell. Användaren kan ändra inställningarna på enheten och uppdatera program varu versionerna. Den här rollen beviljar inte behörigheter för att kontrol lera team aktiviteter och enhetens kvalitet.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Avancerade fråge funktioner för katalog objekt

**Typ:** Ny funktion  
**Tjänste kategori:** MS Graph  
**Produkt kapacitet:** Utvecklings miljö
 
Alla nya fråge funktioner som introducerats för katalog objekt i Azure AD-API: er är nu tillgängliga i 5 1.0-slutpunkten och produktions klara. Utvecklare kan räkna, söka efter, filtrera och sortera katalog objekt och relaterade länkar med hjälp av standard-OData-operatörer.

Mer information finns i dokumentationen [här](https://aka.ms/BlogPostMezzoGA)och du kan också skicka feedback med den här [korta undersökningen](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Offentlig för hands version: utvärdering av kontinuerlig åtkomst för klienter som har konfigurerat principer för villkorlig åtkomst

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
En utvärdering av kontinuerlig åtkomst (CAE) är nu tillgänglig i offentlig för hands version för Azure AD-klienter med principer för villkorlig åtkomst. Med CAE utvärderas viktiga säkerhets händelser och principer i real tid. Detta inkluderar konto inaktive ring, återställning av lösen ord och plats ändring. Läs mer i utvärderingen av [kontinuerlig åtkomst](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Offentlig för hands version: be användare som begär ett åtkomst paket ytterligare frågor för att förbättra godkännande beslut

**Typ:** Ny funktion  
**Tjänste kategori:** Hantering av användar åtkomst  
**Produkt kapacitet:** Hantering av rättigheter
 
Administratörer kan nu kräva att användare som begär ett åtkomst paket svarar på ytterligare frågor utöver bara affärs skäl i Azure AD-hanteringens åtkomst Portal. Användarnas svar visas sedan för god kännare för att hjälpa dem att fatta ett mer exakt beslut om godkännande av åtkomst. Mer information finns i [samla in ytterligare information om begär Ande för godkännande (för hands version)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview).
 
---

### <a name="public-preview-enhanced-user-management"></a>Offentlig för hands version: förbättrad användar hantering

**Typ:** Ny funktion  
**Tjänste kategori:** Användar hantering  
**Produkt kapacitet:** Användar hantering
 

Azure AD-portalen har uppdaterats för att göra det enklare att hitta användare på sidorna alla användare och borttagna användare. Ändringarna i förhands granskningen är: 
- Mer synliga användar egenskaper, inklusive objekt-ID, status för katalog synkronisering, skapande typ och identitets utfärdare.
- Nu kan du söka i kombinerad sökning efter namn, e-post och objekt-ID: n.
- Utökad filtrering efter användar typ (medlem, gäst och ingen), status för katalog synkronisering, typ av katalog, företags namn och domän namn.
- Nya sorterings funktioner för egenskaper som namn, User Principal Name och borttagnings datum.
- Ett nytt totalt antal användare som uppdaterar med eventuella sökningar eller filter.

Mer information finns i avsnittet om [förbättringar av användar hantering (för hands version) i Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Nytt antecknings fält för företags program

**Typ:** Ny funktion  
**Tjänste kategori:** **Produkt kapacitet** för företags appar: SSO

Du kan lägga till kostnads fria text anteckningar i företags program. Du kan lägga till all relevant information som kan hjälpa dig att hantera program i företags program. Mer information finns i [snabb start: konfigurera egenskaper för ett program i din Azure Active Directory (Azure AD)-klient](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – september 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I september 2020 har vi lagt till följande 34 nya program i vårt app-galleri med stöd för federation:

[VMware-Horisont – enhetlig åtkomst-Gateway](), [puls säkra datorer](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData för företag](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions](../saas-apps/cybersolutions-cybermail-tutorial.md)CYBERMAILΣ, [LimbleCMMS](https://auth.limblecmms.com/), [glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [köns lämplighet](https://app.genderfitness.com/), [Coeo-portalen](https://my.coeo.com/), [grammatik](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [Raumfürraum](../saas-apps/raumfurraum-tutorial.md) [, Saviynt,](../saas-apps/saviynt-tutorial.md) [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [Mobile LOCKer](../saas-apps/mobile-locker-tutorial.md), [Zengine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), Protected [Identity & Access Management](https://my.priva.com/), Nitro [Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [Fexa](../saas-apps/fexa-tutorial.md), [Secure Signing Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Secure Signing Enterprise Portal AAD setup](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [Wistec online](https://wisteconline.com/auth/oidc), [Oracle-skydd med F5 Big-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

Du kan också hitta dokumentationen för alla program härifrån: https://aka.ms/AppsTutorial .

Information om hur du visar ditt program i Azure AD App-galleriet finns här: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Ny Delegerings roll i hantering av Azure AD-rättighets hantering: Access Package Assignment Manager

**Typ:** Ny funktion  
**Tjänste kategori:** Hantering av användar åtkomst  
**Produkt kapacitet:** Hantering av rättigheter
 
En ny Access Package Assignment Manager-roll har lagts till i hantering av Azure AD-hantering för att ge detaljerade behörigheter för att hantera tilldelningar. Nu kan du delegera uppgifter till en användare med den här rollen, som kan delegera uppdrags hanteringen av ett Access-paket till en affärs ägare. Men ett Access Package tilldelnings hanterare kan inte ändra åtkomst paket principer eller andra egenskaper som anges av administratörerna. 

Med den nya rollen drar du nytta av de lägsta behörigheterna som krävs för att delegera hantering av tilldelningar och upprätthålla administrativ kontroll för alla andra paket konfigurationer för åtkomst. Läs mer i [rättighets hanterings roller](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Ändringar av Privileged Identity Managementns onboarding-flöde

**Typ:** Ändrad funktion  
**Tjänste kategori:** Privileged Identity Management  
**Produkt kapacitet:** Privileged Identity Management
 
Tidigare behövde onboarding till Privileged Identity Management (PIM) användar medgivande och ett onboarding-flöde i PIM-bladet som inkluderade registreringen i Azure MFA. Med den senaste integrationen av PIM-upplevelsen i bladet Azure AD-roller och-administratörer tar vi bort den här upplevelsen. Alla innehavare med giltig P2-licens registreras automatiskt i PIM.

Onboarding till PIM har ingen direkt negativ inverkan på din klient. Du kan vänta på följande ändringar:
- Ytterligare tilldelnings alternativ, till exempel aktiva kontra berättigade med start-och slut tid när du gör en tilldelning i något av webbrollerna PIM eller Azure AD och administratörer. 
- Ytterligare omfångs metoder, som administrativa enheter och anpassade roller, introducerades direkt i tilldelnings upplevelsen. 
- Om du är global administratör eller administratör för privilegierade roller kan du börja få ytterligare e-postmeddelanden som PIM veckovis sammandrag. 
- Du kan också se MS-PIM-tjänstens huvud namn i gransknings loggen som är relaterad till roll tilldelningen. Den här förväntade ändringen påverkar inte ditt vanliga arbets flöde.

 Mer information finns i [börja använda Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Hantering av Azure AD-hantering: fönstret Välj för åtkomst paket resurser visas nu som standard de resurser som för närvarande finns i den valda katalogen

**Typ:** Ändrad funktion  
**Tjänste kategori:** Hantering av användar åtkomst  
**Produkt kapacitet:** Hantering av rättigheter
 

I flödet för att skapa åtkomst paket, under fliken resurs roller, ändras beteendet för val fönster. För närvarande är standard beteendet att visa alla resurser som ägs av användaren och resurserna som läggs till i den valda katalogen. 

Den här funktionen kommer att ändras så att endast de resurser som för närvarande läggs till i katalogen visas som standard, så att användarna enkelt kan välja resurser från katalogen. Uppdateringen hjälper till att identifiera resurser som ska läggas till i åtkomst paket och minskar risken för att oavsiktligt lägga till resurser som ägs av användaren som inte är en del av katalogen. Mer information finns i [skapa ett nytt Access-paket i hantering av Azure AD-behörighet](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---

## <a name="august-2020"></a>Augusti 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Uppdateringar av krav för Azure Multi-Factor Authentication-server-brandvägg

**Typ:** Planera för ändring  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Från och med 1 oktober 2020 kräver Azure MFA Server Firewall-krav ytterligare IP-intervall.

Om du har utgående brand Väggs regler i din organisation uppdaterar du reglerna så att dina MFA-servrar kan kommunicera med alla IP-adressintervall som behövs. IP-intervallen beskrivs i [Azure Multi-Factor Authentication-Server brand Väggs krav](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Kommande ändringar av användar upplevelsen i identitetens säkra Poäng

**Typ:** Planera för ändring  
**Tjänste kategori:** **Produkt kapacitet** för identitets skydd: identitets säkerhet & skydd

Vi uppdaterar portalen för identitetens säkra poäng så att den överensstämmer med de ändringar som introducerades i Microsofts [nya version](/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)av säkerhets poängen. 

För hands versionen med ändringarna är tillgänglig i början av september. Ändringarna i för hands versionen är:
- "Identifiera säker Poäng" byter namn till "säkra Poäng för identitet" för varumärkes anpassning med Microsofts säkra Poäng
- Punkter normaliserade till standard skala och rapporteras i procent i stället för punkter

I den här för hands versionen kan kunderna växla mellan den befintliga upplevelsen och den nya upplevelsen. Den här för hands versionen kommer att vara sist i slutet av november 2020. Efter förhands granskningen dirigeras kunderna automatiskt till den nya UX-upplevelsen.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nya begränsade gäst åtkomst behörigheter i Azure AD – offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** Access Control   
**Produkt kapacitet:** Användar hantering

Vi har uppdaterat katalog nivå behörigheter för gäst användare. Med dessa behörigheter kan administratörer kräva ytterligare begränsningar och kontroller av åtkomst till externa gäst användare. Administratörer kan nu lägga till ytterligare begränsningar för externa gästers åtkomst till användar-och grupp profil-och medlemskaps information. Med den här offentliga för hands versionen kan kunder hantera externa användares åtkomst i skala genom dölja grupp medlemskap, inklusive att begränsa gäst användare från att se medlemskap i de grupper som de finns i.

Läs mer i [begränsade gäst åtkomst behörigheter](../enterprise-users/users-restrict-guest-permissions.md) och [användares standard behörigheter](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Allmän tillgänglighet för delta frågor för tjänstens huvud namn

**Typ:** Ny funktion  
**Tjänste kategori:** MS Graph  
**Produkt kapacitet:** Utvecklings miljö
 
Microsoft Graph delta fråga stöder nu resurs typen i v 1.0:
- Tjänstens huvudnamn

Nu kan klienter snabbt spåra ändringar i resurserna och tillhandahålla den bästa lösningen för att synkronisera ändringar av dessa resurser med ett lokalt data lager. Information om hur du konfigurerar dessa resurser i en fråga finns i [använda delta fråga för att spåra ändringar i Microsoft Graph data](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Allmän tillgänglighet för delta frågor för oAuth2PermissionGrant

**Typ:** Ny funktion  
**Tjänste kategori:** MS Graph  
**Produkt kapacitet:** Utvecklings miljö

Microsoft Graph delta fråga stöder nu resurs typen i v 1.0:
- OAuth2PermissionGrant

Klienter kan nu spåra ändringar i dessa resurser effektivt och ger den bästa lösningen för att synkronisera ändringar av dessa resurser med ett lokalt data lager. Information om hur du konfigurerar dessa resurser i en fråga finns i [använda delta fråga för att spåra ändringar i Microsoft Graph data](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – augusti 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I augusti 2020 har vi lagt till följande 25 nya program i vårt app-galleri med stöd för federation:

[Backup365](https://portal.backup365.io/login), [tvål](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [Enlyft Dynamics 365-anslutning](http://enlyft.com/), [Serraview för användning av program varu lösningar](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [uniq](https://web.uniq.app/), [synlig](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum-kurs program vara exakt sökväg](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira](../saas-apps/altamira-hrm-tutorial.md), WireWheel [, ZIX, GreenLight](../saas-apps/wirewheel-tutorial.md) [efterlevnad och avbildning](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [GENETEC företags affärs kontroll plattform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [VeraSMART](https://www.clearance.network/), [Amiko](../saas-apps/isams-tutorial.md), twingate [,](../saas-apps/verasmart-tutorial.md)Scalefusion [,](https://amiko.web.rivero.app/) [Bpanda](https://auth.twingate.com/signup), [tratt](https://nestiolistings.com/sso/oidc/azure/authorize/) [, Vivun, Fortigate](https://scalefusion.com/users/sign_in/), [Wandera](https://goto.bpanda.com/login) [kalender Connect](https://app.vivun.com/dashboard/calendar/connect), [SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md),- [slutanvändare](https://www.wandera.com/)

Du kan också hitta dokumentationen för alla program härifrån https://aka.ms/AppsTutorial

För att lista ditt program i Azure AD App-galleriet läser du informationen här https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Resurs skogar är nu tillgängliga för Azure AD DS 

**Typ:** Ny funktions **tjänst kategori:** Azure AD Domain Services   
**Produkt kapacitet:** Azure AD Domain Services
 
Funktionen för resurs skogar i Azure AD Domain Services är nu allmänt tillgänglig. Nu kan du aktivera auktorisering utan att lösen ordets hash-synkronisering använder Azure AD Domain Services, inklusive smartkort-auktorisering. Läs mer i [replik uppsättnings koncept och funktioner för Azure Active Directory Domain Services (för hands version)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Regional replik stöd för Azure AD DS-hanterade domäner är nu tillgängliga

**Typ:** Ny funktion   
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services
 
Du kan expandera en hanterad domän så att den har fler än en replik uppsättning per Azure AD-klient. Replik uppsättningar kan läggas till i alla peer-kopplat virtuella nätverk i alla Azure-regioner som har stöd för Azure AD Domain Services. Ytterligare replik uppsättningar i olika Azure-regioner ger geografisk haveri beredskap för äldre program om en Azure-region försätts i offlineläge. Läs mer i [replik uppsättnings koncept och funktioner för Azure Active Directory Domain Services (för hands version)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Allmän tillgänglighet för Azure AD My Sign-Ins

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Slut användar upplevelser
 
Azure AD My Sign-Ins är en ny funktion som gör det möjligt för företags användare att granska sin inloggnings historik för att söka efter ovanliga aktiviteter. Dessutom gör den här funktionen att slutanvändarna kan rapportera "det här inte jag" eller "det var jag" på misstänkta aktiviteter. Mer information om hur du använder den här funktionen finns i [Visa och söka efter din senaste inloggnings aktivitet från sidan mina Sign-Ins](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>SAP SuccessFactors HR driven användar etablering till Azure AD är nu allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Nu kan du integrera SAP-SuccessFactors som den auktoritativa identitets källan med Azure AD och automatisera hela identitets livs cykeln från slut punkt till slut punkt med hjälp av HR-händelser som nya anställningar och uppsägningar för att driva etablering och avetablering av konton i Azure AD. 

Mer information om hur du konfigurerar SAP SuccessFactors inkommande etablering i Azure AD finns i själv studie kursen [Konfigurera SAP SuccessFactors för att Active Directory användar etablering](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Anpassad öppen ID Anslut MS Graph API-stöd för Azure AD B2C

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
Tidigare kunde anpassade öppen ID Connect-providrar endast läggas till eller hanteras via Azure Portal. Nu kan Azure AD B2C-kunder lägga till och hantera dem via Microsoft Graph API: er beta version också. Information om hur du konfigurerar den här resursen med API: er finns i [identityProvider resurs typ](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Tilldela inbyggda Azure AD-roller till moln grupper

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD-roller  
**Produkt kapacitet:** Access Control

Nu kan du tilldela inbyggda Azure AD-roller till moln grupper med den här nya funktionen. Du kan till exempel tilldela rollen SharePoint-administratör till Contoso_SharePoint_Admins grupp. Du kan också använda PIM för att göra gruppen till en behörig medlem i rollen, i stället för att bevilja ständig åtkomst. Information om hur du konfigurerar den här funktionen finns i [använda moln grupper för att hantera roll tilldelningar i Azure Active Directory (för hands version)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Den inbyggda rollen insikter för affärs ledare finns nu tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD-roller  
**Produkt kapacitet:** Access Control
 
Användare i rollen insikter affärs ledare kan komma åt en uppsättning instrument paneler och insikter via [M365 Insights-programmet](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Detta omfattar fullständig åtkomst till alla instrument paneler och visade insikter och data utforsknings funktioner. Användare i den här rollen har dock inte åtkomst till produkt konfigurations inställningar, vilket är ansvaret för rollen insikter-administratör. Mer information om den här rollen finns [i administratörs roll behörigheter i Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Den inbyggda rollen insikts administratör finns nu tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD-roller  
**Produkt kapacitet:** Access Control
 
Användare i rollen insikter-administratör kan komma åt fullständig uppsättning administrativa funktioner i [M365 Insights-programmet](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). En användare med den här rollen kan läsa katalog information, övervaka tjänstens hälsa, stöd för fil support och få åtkomst till inställningarna för Insights-administratörer. Mer information om den här rollen finns [i administratörs roll behörigheter i Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Program administratör och moln program administratör kan hantera tilläggs egenskaper för program

**Typ:** Ändrad funktion  
**Tjänste kategori:** Azure AD-roller  
**Produkt kapacitet:** Access Control
 
Tidigare kunde endast den globala administratören hantera [tilläggs egenskapen](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http). Nu är det möjligt att aktivera den här funktionen för program administratören och moln program administratören.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2 Hotfix 4.6.263.0 och kopplingar 1.1.1301.0

**Typ:** Ändrad funktion  
**Tjänste kategori:** Microsoft Identity Manager  
**Produkt kapacitet:** Hantering av identitets livs cykel

Det finns ett [samlat snabb korrigerings paket (build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) för Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Det här samlade paketet innehåller uppdateringar för MIM CM, MIM Synchronization Manager och PAM-komponenter. Dessutom innehåller MIM Generic Connectors 1.1.1301.0 uppdateringar för graf Connector.

---
 
## <a name="july-2020"></a>Juli 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Som IT-administratör vill jag att mål klient program ska använda villkorlig åtkomst

**Typ:** Planera för ändring   
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Med GA-versionen av klient program villkoret i villkorlig åtkomst gäller nu nya principer som standard för alla klient program. Detta inkluderar äldre autentiserings klienter. Befintliga principer är oförändrade men inställningen *Konfigurera Ja/Nej* tas bort från befintliga principer för att enkelt se vilka klient program som tillämpas på principen. 

När du skapar en ny princip måste du se till att undanta användare och tjänst konton som fortfarande använder äldre autentisering. Om du inte gör det kommer de att blockeras. [Läs mer](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Kommande korrigeringar för SCIM-efterlevnad

**Typ:** Planera för ändring  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Azure AD Provisioning-tjänsten utnyttjar SCIM-standarden för integrering med program. Vår implementering av SCIM-standarden utvecklas och vi förväntar oss att göra ändringar i vårt beteende i hur vi utför KORRIGERINGs åtgärder samt anger egenskapen "aktiv" på en resurs. [Läs mer](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Grupp ägar inställningen på Azure Admin Portal kommer att ändras

**Typ:** Planera för ändring  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete

Inställningen ägare på sidan allmänna inställningar för inställningar kan konfigureras för att begränsa ägar tilldelnings behörigheter till en begränsad grupp användare i Azure admin-portalen och åtkomst panelen. Vi kommer snart att ha möjlighet att tilldela behörighet för grupp ägare, inte bara på dessa två UX-portaler, men tillämpar även principen på Server delen för att ge ett konsekvent beteende mellan slut punkter, till exempel PowerShell och Microsoft Graph. 

Vi börjar med att inaktivera den aktuella inställningen för de kunder som inte använder den och kommer att erbjuda ett alternativ för att omfånget användare för grupp ägarens privilegium under de kommande månaderna. Information om hur du uppdaterar grupp inställningar finns i redigera grupp information med [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory registrerings tjänsten avslutar stödet för TLS 1,0 och 1,1

**Typ:** Planera för ändring  
**Tjänste kategori:** Enhets registrering och hantering  
**Produkt kapacitet:** Systemet

TLS (Transport Layer Security) 1,2 och uppdaterings servrar och klienter kommer snart att kommunicera med Azure Active Directory Device Registration-tjänsten. Stöd för TLS 1,0 och 1,1 för kommunikation med Azure AD Device Registration Service kommer att tas ur bruk:
- Den 31 augusti 2020, i alla suveräna moln (GCC hög, DoD osv.)
- Den 30 oktober 2020 i alla kommersiella moln

[Läs mer](../devices/reference-device-registration-tls-1-2.md) om TLS 1,2 för registrerings tjänsten för Azure AD.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Inloggnings loggar för Windows Hello för företag visas i inloggnings loggar för Azure AD

**Typ:** Fastsatt  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering
 
Windows Hello för företag gör att slutanvändarna kan logga in på Windows-datorer med en gest (t. ex. en PIN-kod eller bio metrisk). Azure AD-administratörer kan vilja särskilja inloggnings program för Windows Hello för företag från andra Windows-inloggningar som en del av en organisations resan till lösen ords lös autentisering. 

Administratörer kan nu se om en Windows-autentisering använder Windows Hello för företag genom att kontrol lera fliken autentiseringsinformation för en Windows-inloggnings händelse på bladet Azure AD Sign-Ins i Azure Portal. Windows Hello för företag-autentiseringar kommer att innehålla "WindowsHelloForBusiness" i fältet autentiseringsmetod. Mer information om hur du tolkar Sign-In loggar finns i [dokumentationen för inloggnings loggar](../reports-monitoring/concept-sign-ins.md).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Korrigeringar för att gruppera borttagnings beteende och prestanda förbättringar

**Typ:** Fastsatt  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
När en grupp har ändrats från "i omfång" till "out-of-Scope" och en administratör klickade på Starta innan ändringen slutfördes, togs inte gruppobjektet bort. Nu kommer gruppobjektet att tas bort från mål programmet när det hamnar utanför omfånget (inaktiverat, Borttaget, ej tilldelat eller inte godkänt omfångs filter). [Läs mer](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Offentlig för hands version: administratörer kan nu lägga till anpassat innehåll i e-postmeddelandet till granskare när de skapar en åtkomst granskning

**Typ:** Ny funktion  
**Tjänste kategori:** Åtkomst granskningar  
**Produkt kapacitet:** Identitets styrning
 
När en ny åtkomst granskning skapas får granskaren ett e-postmeddelande som begär att de ska kunna slutföra åtkomst granskningen. Många av våra kunder har bett om möjlighet att lägga till anpassat innehåll i e-postmeddelandet, till exempel kontakt information eller annat ytterligare stöd för att hjälpa granskaren. 

Som är tillgänglig i den offentliga för hands versionen kan administratörer ange anpassat innehåll i e-postmeddelandet som skickas till granskare genom att lägga till innehåll i avsnittet "Avancerad" i åtkomst granskningar för Azure AD. Vägledning om hur du skapar åtkomst granskningar finns i [skapa en åtkomst granskning av grupper och program i åtkomst granskningar i Azure AD](../governance/create-access-review.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Authorization Code Flow för appar på en enda sida som är tillgängliga

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Utvecklings miljö
 
På grund av moderna webb läsar begränsningar från tredje part, till exempel Safari ITP, måste SPAs använda auktoriseringskod i stället för det implicita flödet för att upprätthålla SSO och MSAL.js v 2. x stöder nu Authorization Code Flow. 

Det finns motsvarande uppdateringar av Azure Portal så att du kan uppdatera din SPA och skriva "Spa" och använda kod flödet för autentisering. Se [Logga in användare och få en åtkomsttoken i ett Java Script spa med hjälp av auth Code Flow](../develop/quickstart-v2-javascript-auth-code.md) för ytterligare vägledning.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD-programproxy stöder nu Fjärrskrivbordstjänster webb klienten

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control

Azure AD-programproxy stöder nu webb klienten Fjärrskrivbordstjänster (RDS). RDS-webbklienten ger användare åtkomst till fjärr skrivbords infrastrukturen via valfri HTLM5 webbläsare, till exempel Microsoft Edge, Internet Explorer 11, Google Chrome osv. Användare kan interagera med fjärrappar eller skriv bord som de skulle ha en lokal enhet från var som helst. Genom att använda Azure AD-programproxy kan du öka säkerheten för din RDS-distribution genom att tvinga Förautentiserings-och villkorliga åtkomst principer för alla typer av rika klient program. Vägledning finns i [publicera fjärr skrivbord med Azure AD-programproxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Nästa generations Azure AD B2C användar flöden i offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
Förenklad användar flödes upplevelse erbjuder funktions paritet med förhands gransknings funktioner och är start för alla nya funktioner. Användarna kommer att kunna aktivera nya funktioner inom samma användar flöde, vilket minskar behovet av att skapa flera versioner med varje ny funktions version. Slutligen fören klar det nya, användarvänliga UX valet och skapandet av användar flöden. Prova nu genom att [skapa ett användar flöde](../../active-directory-b2c/tutorial-create-user-flows.md). 

Mer information om användare flöden finns [i användar flödes versioner i Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md#:~:text=    User flow  ,account. Usi ...  1 more rows ).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – juli 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I juli 2020 har vi lagt till följande 55 nya program i vårt app-galleri med stöd för federation:

[Clap Your händerna](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor-valvet](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects receptionist](https://msteams.peterconnects.com/), [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [mynt konstruktion](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [Reback](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US), [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [DataSite](../saas-apps/datasite-tutorial.md), [bloggi](../saas-apps/blogin-tutorial.md), [IntSights](../saas-apps/intsights-tutorial.md) [, kpifire](../saas-apps/kpifire-tutorial.md), [TextLine](../saas-apps/textline-tutorial.md), [Cloud Academy-SSO](../saas-apps/cloud-academy-sso-tutorial.md), [Community Spark](../saas-apps/community-spark-tutorial.md), [ChatWork](../saas-apps/chatwork-tutorial.md), [CloudSign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan data hubb enkel inloggning](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [utgående](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [Right cybersäkerhet ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [Verme](../saas-apps/verme-tutorial.md), [lenses.io](../saas-apps/lensesio-tutorial.md), [taga](../saas-apps/momenta-tutorial.md), [öka](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [TellMe bot](https://tellme365liteweb.azurewebsites.net/), [inspirera](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/), [Smartschool (skol Management System)](https://smartschoolz.com/login), [Zepto-intelligent timekeeping](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), Trackplan, [skedda](http://www.trackplanfm.com/), [WhosOnLocation](../saas-apps/skedda-tutorial.md), [Coggle](../saas-apps/whos-on-location-tutorial.md), [Kemp: LoadMaster](https://kemptechnologies.com/cloud-load-balancer/), BrowserStack [enkel inloggning](../saas-apps/browserstack-single-sign-on-tutorial.md) [Coggle](../saas-apps/coggle-tutorial.md)

Du kan också hitta dokumentationen för alla program härifrån https://aka.ms/AppsTutorial

För att lista ditt program i Azure AD App-galleriet läser du informationen här. https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Nya etablerings anslutningar i Azure AD Application Gallery – juli 2020

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part

Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för den nyligen integrerade appen [LinkedIn-inlärning](../saas-apps/linkedin-learning-provisioning-tutorial.md).

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Visa roll tilldelningar för alla omfattningar och möjlighet att ladda ned dem till en CSV-fil

**Typ:** Ändrad funktion  
**Tjänste kategori:** Azure AD-roller  
**Produkt kapacitet:** Access Control
 
Nu kan du Visa roll tilldelningar för alla omfattningar för en roll på fliken "roller och administratörer" i Azure AD-portalen. Du kan också hämta roll tilldelningarna för varje roll till en CSV-fil. Vägledning om hur du visar och lägger till roll tilldelningar finns i [Visa och tilldela administratörs roller i Azure Active Directory](../roles/manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Inaktuellitet i Azure Multi-Factor Authentication Software Development (Azure MFA SDK)

**Typ:** Föråldrad  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Azure Multi-Factor Authentication Software Development (Azure MFA SDK) nådde slutet av livs längden den 14 november 2018, som först lanserades i november 2017. Microsoft kommer att stänga av SDK-tjänsten gällande den 30 september 2020. Alla anrop till SDK: n kommer att Miss inträffat.

Om din organisation använder Azure MFA SDK måste du migrera den 30 september 2020:
- Azure MFA SDK för MIM: om du använder SDK med MIM bör du migrera till Azure MFA Server och aktivera Privileged Access Management (PAM) genom att följa dessa [anvisningar](/microsoft-identity-manager/working-with-mfaserver-for-mim).   
- Azure MFA SDK för anpassade appar: Överväg att integrera din app i Azure AD och använda villkorlig åtkomst för att genomdriva MFA. Gå igenom den här [sidan](../manage-apps/plan-an-application-integration.md)för att komma igång. 

---

## <a name="june-2020"></a>Juni 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Villkor för användar risk i princip för villkorlig åtkomst

**Typ:** Planera för ändring  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet
 

Med stöd för användar risker i Azure AD-princip för villkorlig åtkomst kan du skapa flera principer för användar risk. Olika minimi nivåer för användar risk kan krävas för olika användare och appar. Utifrån användar risk kan du skapa principer för att blockera åtkomst, kräva Multi-Factor Authentication, säkra lösen ords ändringar eller omdirigera till Microsoft Cloud App Security för att framtvinga en replikeringsprincip, till exempel ytterligare granskning.

Användar risk villkoret kräver Azure AD Premium P2 eftersom det använder Azure Identity Protection, som är ett P2-erbjudande. Mer information om villkorlig åtkomst finns i dokumentationen för [villkorlig åtkomst för Azure AD](../conditional-access/index.yml).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO stöder nu appar som kräver att SPNameQualifier anges vid begäran

**Typ:** Fastsatt  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Vissa SAML-program kräver att SPNameQualifier returneras i intygets ämne när det begärs. Azure AD svarar nu korrekt när en SPNameQualifier begärs i NameID-principen för begäran. Detta fungerar även för SP-initierad inloggning och IdP initierad inloggning kommer att följa.  Mer information om SAML-protokoll i Azure Active Directory finns i [Single Sign-On SAML-protokollet](../develop/single-sign-on-saml-protocol.md).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B-samarbete har stöd för att bjuda in MSA och Google-användare i Azure Government-klienter

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 

Azure Government klienter som använder B2B-samarbets funktionerna kan nu bjuda in användare som har ett Microsoft-eller Google-konto. Om du vill ta reda på om din klient kan använda dessa funktioner följer du anvisningarna på [Hur vet jag om B2B-samarbete är tillgängligt i min Azure amerikanska myndighets klient?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Användar objekt i MS Graph v1 innehåller nu externalUserState-och externalUserStateChangedDateTime-egenskaper

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 

Egenskaperna externalUserState och externalUserStateChangedDateTime kan användas för att hitta inbjudna B2B-gäster som inte har accepterat sina inbjudningar än och som skapar automatisering, som att ta bort användare som inte har accepterat sina inbjudningar efter ett visst antal dagar. De här egenskaperna är nu tillgängliga i MS Graph v1. Vägledning om hur du använder dessa egenskaper finns i [användar resurs typ](/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Hantera autentiseringsbegäranden i Azure AD villkorlig åtkomst är nu allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Med hanterings funktionerna för autentisering kan du konfigurera hur ofta dina användare måste ange inloggnings uppgifter och om de behöver ange autentiseringsuppgifter efter att de har stängt och öppnat om webbläsare för att få större säkerhet och flexibilitet i din miljö.
 
Dessutom används hantering av autentisering för att endast använda den första Factor Authentication på Azure AD-anslutna, hybrid Azure AD-anslutna och registrerade Azure AD-enheter. Nu gäller hantering av autentiserings-sessioner även för MFA. Mer information finns i [Konfigurera hantering av autentisering med villkorlig åtkomst](../conditional-access/howto-conditional-access-session-lifetime.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – juni 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I juni 2020 har vi lagt till följande 29 nya program i vårt app-galleri med stöd för federation:

[Shopify plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [grindar](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [Raketa](../saas-apps/raketa-tutorial.md), [segment](../saas-apps/segment-tutorial.md), [AI revisor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.io](../saas-apps/proto.io-tutorial.md), [Gatekeeper](https://www.gatekeeperhq.com/), [hubb Planner](../saas-apps/hub-planner-tutorial.md), [Ansira-partner go-to-Marketing-verktygslåda](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation i molnet](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [kisi fysisk säkerhet](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pymetrics](../saas-apps/pymetrics-tutorial.md), [noll](https://www.teamzero.com/), [instation](https://instation.invillia.com/), [EDX för Business SAML 2,0-integration](../saas-apps/edx-for-business-saml-integration-tutorial.md), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](../saas-apps/smartkargo-tutorial.md), [PKIsigning-plattform](https://platform.pkisigning.nl/), [SiteIntel](../saas-apps/siteintel-tutorial.md), [fält-ID](../saas-apps/field-id-tutorial.md) [, kurs utbud](https://smallstep.com/sso-ssh/) [SAML](../saas-apps/curricula-saml-tutorial.md), [perforce Helix Core-Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md) [,](https://cloud.metacompliance.com/)  

Du kan också hitta dokumentationen för alla program härifrån https://aka.ms/AppsTutorial . För att lista ditt program i Azure AD App-galleriet läser du informationen här: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>API-kopplingar för externa identiteter för självbetjänings registrering finns nu i offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 
API-kopplingar för externa identiteter gör att du kan använda webb-API: er för att integrera självbetjänings registrering med externa moln system. Det innebär att du nu kan anropa webb-API: er som vissa steg i ett registrerings flöde för att utlösa molnbaserade anpassade arbets flöden. Du kan till exempel använda API-kopplingar för att:

- Integrera med ett anpassat godkännande arbets flöden.
- Utföra identitets bevisning
- Verifiera indata från användaren
- Skriv över användarattribut
- Kör anpassad affärs logik

Mer information om alla upplevelser som är möjliga med API-kopplingar finns i [använda API-kopplingar för att anpassa och utöka](../external-identities/api-connectors-overview.md)självbetjänings registrering eller [Anpassa extern identitet självbetjänings registrering med Web API-integreringar](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Etablera på begäran och få användare i dina appar på några sekunder

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Azure AD Provisioning-tjänsten arbetar för närvarande med en cyklisk bas. Tjänsten körs var 40: e minut. Med [etablerings funktionen på begäran](https://aka.ms/provisionondemand) kan du välja en användare och etablera dem på några sekunder. Med den här funktionen kan du snabbt felsöka etablerings problem, utan att behöva göra en omstart för att tvinga etablerings cykeln att starta igen. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Ny behörighet för att använda hantering av Azure AD-berättigande i grafen

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Hantering av rättigheter
 
En ny delegerad behörighet EntitlementManagement. Read. all är nu tillgänglig för användning med rättighets hanterings-API: et i Microsoft Graph beta. Mer information om tillgängliga API: er finns i [arbeta med API för hantering av Azure AD-hantering](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>API: er för identitets skydd som är tillgängliga i v 1.0

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
RiskyUsers-och riskDetections-Microsoft Graph-API: er är nu allmänt tillgängliga. Nu när de är tillgängliga på v 1.0-slutpunkten, bjuder vi in dig att använda dem i produktionen. Mer information finns i [Microsoft Graph-dokumenten](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Känslighets etiketter för att tillämpa principer på Microsoft 365 grupper är nu allmänt tillgängliga

**Typ:** Ny funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete
 

Nu kan du skapa känslighets etiketter och använda etikett inställningar för att tillämpa principer på Microsoft 365 grupper, inklusive sekretess (offentlig eller privat) och åtkomst princip för externa användare. Du kan skapa en etikett med sekretess policyn som privat och extern användar åtkomst princip för att inte tillåta att gäst användare läggs till. När en användare använder den här etiketten i en grupp, kommer gruppen att vara privat och inga gäst användare får läggas till i gruppen. 

Känslighets etiketter är viktiga för att skydda affärs kritiska data och gör att du kan hantera grupper i skala på ett kompatibelt och säkert sätt. Vägledning om hur du använder känslighets etiketter finns i [tilldela känslighets etiketter till Microsoft 365 grupper i Azure Active Directory (för hands version)](../enterprise-users/groups-assign-sensitivity-labels.md).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Uppdateringar som stöder Microsoft Identity Manager för Azure AD Premium kunder

**Typ:** Ändrad funktion  
**Tjänste kategori:** Microsoft Identity Manager  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Azure-support är nu tillgängligt för Azure AD integration-komponenter i Microsoft Identity Manager 2016, genom slutet av det utökade stödet för Microsoft Identity Manager 2016. Läs mer på [support uppdatering för Azure AD Premium kunder som använder Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Användningen av grupp medlemskaps villkor i SSO-anspråks konfigurationen höjs

**Typ:** Ändrad funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Tidigare var antalet grupper som du kan använda när du villkorligt ändrar anspråk baserat på grupp medlemskap i en enskild program konfiguration begränsad till 10. Användningen av grupp medlemskaps villkor i SSO-anspråks konfigurationen har nu ökat till högst 50 grupper. Mer information om hur du konfigurerar anspråk finns i konfiguration av [SSO-anspråk för företags program](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Aktivera grundläggande formatering på inloggnings sidans text komponent i företags anpassning.

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Företags anpassnings funktionen på Azure AD/Microsoft 365 inloggnings upplevelsen har uppdaterats så att kunden kan lägga till hyperlänkar och enkel formatering, inklusive fetstil, understrykning och kursiv stil. Vägledning om hur du använder den här funktionen finns i [lägga till anpassning till din organisations Azure Active Directory inloggnings sida](./customize-branding.md).

---

### <a name="provisioning-performance-improvements"></a>Prestanda förbättringar för etablering

**Typ:** Ändrad funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Etablerings tjänsten har uppdaterats för att minska tiden då en [stegvis cykel](../app-provisioning/how-provisioning-works.md#incremental-cycles) slutförs. Det innebär att användare och grupper kommer att tillhandahållas i sina program snabbare än tidigare. Alla nya etablerings jobb som skapats efter 6/10/2020 kommer automatiskt att dra nytta av prestanda förbättringarna. Alla program som kon figurer ATS för etablering före 6/10/2020 måste starta om en gång efter 6/10/2020 för att dra nytta av prestanda förbättringarna. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Att presentera utfasningen av ADAL-och MS Graph-paritet

**Typ:** Föråldrad  
**Tjänste kategori:** EJ TILLÄMPLIGT  
**Produkt kapacitet:** Hantering av enhetens livs cykel

Nu när Microsoft Authentication libraries (MSAL) är tillgängligt kommer vi inte längre att lägga till nya funktioner i Azure Active Directory Authentication libraries (ADAL) och säkerhets korrigeringarna upphör den 30 juni 2022. Mer information om hur du migrerar till MSAL finns i [migrera program till Microsoft Authentication Library (MSAL)](../develop/msal-migration.md).

Dessutom har vi utfört arbetet för att göra alla Azure AD Graph-funktioner tillgängliga via MS Graph. Azure AD Graph-API: er får därför bara Bugfix och säkerhets korrigeringar till och med den 30 juni 2022. Mer information finns i [Uppdatera dina program för att använda Microsoft Authentication Library och Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
 
## <a name="may-2020"></a>Maj 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Dra tillbaka egenskaper i inloggningar-, riskyUsers-och riskDetections-API: er

**Typ:** Planera för ändring  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & skydd för identitets säkerhet

För närvarande används uppräknade typer för att representera egenskapen riskType i både riskDetections-API och riskyUserHistoryItem (i för hands version). Uppräknade typer används också för egenskapen riskEventTypes i inloggningar-API: et. Nu kommer vi att representera dessa egenskaper som strängar. 

Kunderna ska övergå till egenskapen riskEventType i beta-riskDetections och riskyUserHistoryItem-API: et och för att riskEventTypes_v2 egenskap i beta inloggningar-API: n den 9 september 2020. Vid det datumet kommer vi att ta bort de aktuella egenskaperna för riskType och riskEventTypes. Mer information finns i [ändringar i egenskaper för risk händelser och API: er för identitets skydd på Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/).

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Utfasning av egenskapen riskEventTypes i inloggningar v 1.0 API på Microsoft Graph

**Typ:** Planera för ändring  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** & skydd för identitets säkerhet

Uppräknade typer kommer att växla till sträng typer när de representerar risk händelse egenskaper i Microsoft Graph september 2020. Förutom att påverka för hands versions-API: erna påverkar den här ändringen även inloggningar-API: t för produktion.

Vi har infört en ny riskEventsTypes_v2 (String)-egenskap för inloggningar v 1.0-API: et. Vi kommer att dra tillbaka den aktuella riskEventTypes-egenskapen (Enum) den 11 juni 2022 i enlighet med vår Microsoft Graph utfasnings princip. Kunderna ska övergå till riskEventTypes_v2-egenskapen i v 1.0 inloggningar-API: n den 11 juni 2022. Mer information finns i [utfasningen av egenskapen riskEventTypes i inloggningar v 1.0 API på Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Kommande ändringar av MFA-e-postmeddelanden

**Typ:** Planera för ändring  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & skydd för identitets säkerhet
 

Vi gör följande ändringar i e-postmeddelandena för Cloud MFA:

E-postaviseringar kommer att skickas från följande adress: azure-noreply@microsoft.com och msonlineservicesteam@microsoftonline.com . Vi uppdaterar innehållet i meddelanden om bedrägeri aviseringar för att bättre illustrera de steg som krävs för att avblockera användningen.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Ny självbetjänings registrering för användare i federerade domäner som inte kan komma åt Microsoft Teams eftersom de inte har synkroniserats med Azure Active Directory.

**Typ:** Planera för ändring  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 

För närvarande kan användare som finns i domäner federerade i Azure AD, men som inte har synkroniserats till klienten, inte komma åt team. Med början i slutet av juni gör den här nya funktionen att de kan göra det genom att utöka den befintliga e-postverifierade inloggnings funktionen. Detta gör det möjligt för användare som kan logga in till en federerad IdP, men som ännu inte har ett användar objekt i Azure-ID, för att skapa ett användar objekt automatiskt och autentiseras för team. Deras användar objekt kommer att markeras som "självbetjänings registrering". Det här är en utökning av den befintliga funktionen för att verifiera e-postverifiering som användare i hanterade domäner kan göra och kan kontrol leras med hjälp av samma flagga. Den här ändringen kommer att slutföras under följande två månader. Titta efter dokumentations uppdateringar [här](../enterprise-users/directory-self-service-signup.md).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Kommande korrigering: OIDC identifierings dokument för Azure Government molnet uppdateras för att referera till rätt graf-slutpunkter.

**Typ:** Planera för ändring  
**Tjänste kategori:** Suveräna moln  
**Produkt kapacitet:** Användarautentisering
 
Från och med juni börjar OIDC identifierings dokument [Microsoft Identity Platform och OpenID Connect-protokollet](../develop/v2-protocols-oidc.md) på [Azure Government moln](../develop/authentication-national-cloud.md) slut punkten (login.microsoftonline.us) att returnera rätt [nationell moln](/graph/deployments) slut punkt ( https://graph.microsoft.us eller https://dod-graph.microsoft.us) , baserat på den klient som tillhandahölls).  Den ger för närvarande den felaktiga graph.microsoft.com msgraph_host-fältet (graf endpoint).  

Den här fel korrigeringen distribueras gradvis över cirka 2 månader.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government användare kommer inte längre att kunna logga in på login.microsoftonline.com

**Typ:** Planera för ändring  
**Tjänste kategori:** Suveräna moln  
**Produkt kapacitet:** Användarautentisering
 
Den officiella Azure Active Directory-utfärdaren (Azure AD) för Azure Government ändrats från till den 1 juni 2018 https://login-us.microsoftonline.com https://login.microsoftonline.us . Om du äger ett program i en Azure Government-klient måste du uppdatera ditt program för att kunna logga in användare i en. us-slutpunkt.

Från och med den 5 maj kommer Azure AD att börja verkställa slut punkts ändringen, vilket hindrar Azure Government användare från att logga in på appar som finns i Azure Government-klienter med hjälp av den offentliga slut punkten (microsoftonline.com). Påverkade appar kommer att börja se ett fel AADSTS900439-USGClientNotSupportedOnPublicEndpoint. 

Det kommer att finnas en gradvis distribution av den här ändringen med tvång som förväntas vara slutförd för alla appar i juni 2020. Mer information finns i [blogg inlägget Azure Government](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>SAML-begäran om enkel utloggning skickar nu NameID i rätt format

**Typ:** Fastsatt  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
När en användare klickar på utloggning (t. ex. i portalen för appar) skickar Azure AD ett SAML-meddelande med en enkel utloggning till varje app som är aktiv i användarsessionen och har en konfigurerad utloggnings-URL. Dessa meddelanden innehåller en NameID i ett beständigt format.

Om den ursprungliga SAML-inloggningens token använder ett annat format för NameID (t. ex. e-post/UPN) kan SAML-appen inte korrelera NameID i utloggnings meddelandet till en befintlig session (eftersom NameIDs som används i båda meddelandena är olika), vilket gjorde att utloggnings meddelandet ignorerades av SAML-appen och användaren är inloggad. Den här snabb korrigeringen gör utloggnings meddelandet konsekvent med den NameID som har kon figurer ATS för programmet.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>Rollen som hybrid identitets administratör är nu tillgänglig med moln etablering

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD Cloud-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
IT-administratörer kan börja använda den nya rollen "hybrid administratör" som den minst privilegierade rollen för att konfigurera Azure ADConnect Cloud-etablering. Med den nya rollen behöver du inte längre använda den globala administratörs rollen för att konfigurera och konfigurera moln etablering. [Läs mer](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – maj 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I maj 2020 har vi lagt till följande 36 nya program i vårt app-galleri med stöd för federation:

[Moula](https://moula.com.au/pay/merchants), [surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/)engagerande, [Powell-team](https://powell-software.com/en/powell-teams-en/), [TalentSoft Assistant](https://msteams.talent-soft.com/), [ASC Recording Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-engagerad](https://b-engaged.se/), [Competella kontakt Center Workgroup](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft-identitet](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [insvit](../saas-apps/insuite-tutorial.md), [ändrings process hantering](../saas-apps/change-process-management-tutorial.md), [cyara CX Assurance-plattform](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [Smart global styrning](../saas-apps/smart-global-governance-tutorial.md), [Prezi](../saas-apps/prezi-tutorial.md), [Mapbox](../saas-apps/mapbox-tutorial.md), [Datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [WHimsical](../saas-apps/whimsical-tutorial.md) [, Trelica,](../saas-apps/trelica-tutorial.md) [EasySSO for Confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO för BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md), EasySSO [för Bamboo](../saas-apps/easysso-for-bamboo-tutorial.md), [Torii](../saas-apps/torii-tutorial.md), [Axiad](../saas-apps/axiad-cloud-tutorial.md) [, ColorTokens](../saas-apps/humanage-tutorial.md) [, ZTNA](../saas-apps/anyone-home-crm-tutorial.md) [CCH](../saas-apps/colortokens-ztna-tutorial.md) [,](../saas-apps/ice-contact-center-tutorial.md) Tagetik [, ShareVault](../saas-apps/sharevault-tutorial.md) [, Vyond](../saas-apps/textexpander-tutorial.md) [, TextExpander,](../saas-apps/vyond-tutorial.md) [askSpoke,](../saas-apps/askspoke-tutorial.md) [CCH Tagetik](../saas-apps/cch-tagetik-tutorial.md)

Du kan också hitta dokumentationen för alla program härifrån https://aka.ms/AppsTutorial .

För att lista ditt program i Azure AD App-galleriet läser du informationen här https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Endast rapport läge för villkorlig åtkomst är nu allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet

Med [endast rapport läge för villkorlig åtkomst i Azure AD](../conditional-access/concept-conditional-access-report-only.md) kan du utvärdera resultatet av en princip utan att tvinga åtkomst kontroller. Du kan testa endast rapport principer i organisationen och förstå deras inverkan innan du aktiverar dem, vilket gör distributionen säkrare och enklare. Under de senaste månaderna har vi sett ett starkt införande av enbart rapport läge – över 26M-användare är redan en omfattning av en rapport princip. Med meddelandet idag skapas nya Azure AD-principer för villkorlig åtkomst i endast rapport läge som standard. Det innebär att du kan övervaka påverkan av dina principer från den tidpunkt då de skapas. Och för de som använder MS Graph API: er kan du [hantera endast rapport principer program mässigt](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Registrera dig för gäst användare med självbetjäning

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 
Med externa identiteter i Azure AD kan du tillåta att personer utanför organisationen kan komma åt dina appar och resurser samtidigt som de kan logga in med den identitet de föredrar. När du delar ett program med externa användare är det inte säkert att du alltid vet i förväg vilka som behöver åtkomst till programmet. Med [självbetjänings registrering](../external-identities/self-service-sign-up-overview.md)kan du göra det möjligt för gäst användare att registrera sig och få ett gäst konto för dina branschspecifika appar (LOB). Registrerings flödet kan skapas och anpassas för att ge stöd för Azure AD och sociala identiteter. Du kan också samla in ytterligare information om användaren under registreringen.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Arbets boken för villkorlig åtkomst insikter och rapportering är allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet

[Arbets boken insikter och rapportering](../conditional-access/howto-conditional-access-insights-reporting.md) ger administratörer en översikt över villkorlig åtkomst för Azure AD i sin klient organisation. Med möjligheten att välja en enskild princip kan administratörer bättre förstå vad varje princip gör och övervaka ändringar i real tid. Arbets boken strömma data som lagras i Azure Monitor, som du kan konfigurera på några minuter [efter dessa instruktioner](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). För att instrument panelen ska bli mer synlig har vi flyttat den till fliken nya insikter och rapporter på menyn för villkorlig åtkomst i Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Bladet princip information för villkorlig åtkomst finns i offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet

I [bladet ny princip information](../conditional-access/troubleshoot-conditional-access.md) visas de tilldelningar, villkor och kontroller som är uppfyllda under utvärderingen av principen för villkorlig åtkomst. Du kan komma åt bladet genom att välja en rad i flikarna villkorlig åtkomst eller endast rapporter i inloggnings informationen.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Nya fråge funktioner för katalog objekt i Microsoft Graph finns i offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** MS Graph **Product Capability:** Developer Experience

Nya funktioner introduceras för Microsoft Graph Directory-objekt-API: er, vilket möjliggör antal, Sök, filtrera och sortera-åtgärder. Detta ger utvecklare möjlighet att snabbt fråga våra katalog objekt utan att det sker några lösningar, till exempel minnes filtrering och sortering. Lär dig mer i det här [blogg inlägget](https://aka.ms/CountFilterMSGraphAAD).

Vi är för närvarande en offentlig för hands version, som söker efter feedback. Skicka dina kommentarer med den här [korta undersökningen](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Konfigurera SAML-baserad enkel inloggning med Microsoft Graph API (beta)

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Nu finns stöd för att skapa och konfigurera ett program från Azure AD-galleriet med MS Graph API: er i Beta versionen. Om du behöver konfigurera SAML-baserad enkel inloggning för flera instanser av ett program sparar du tid genom att använda Microsoft Graph API: er för att [Automatisera konfigurationen av SAML-baserad enkel inloggning](/graph/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nya etablerings anslutningar i Azure AD-programgalleriet – maj 2020

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

* [8 x 8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic efter organisation](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial.md)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="saml-token-encryption-is-generally-available"></a>Kryptering av SAML-token är allmänt tillgängligt

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Med [SAML token Encryption](../manage-apps/howto-saml-token-encryption.md) kan program konfigureras för att ta emot krypterade SAML-kontroller. Funktionen är nu allmänt tillgänglig i alla moln.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Grupp namns anspråk i Application-token är allmänt tillgängliga

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Grupp anspråk som utfärdats i en token kan nu begränsas till bara de grupper som tilldelats programmet.  Detta är särskilt viktigt när användare är medlemmar i ett stort antal grupper och det uppstod en risk för att överskrida storleks gränser för token. När den här nya funktionen är på plats är möjligheten att [lägga till grupp namn i tokens](../hybrid/how-to-connect-fed-group-claims.md) allmänt tillgänglig.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Tillbakaskrivning av workday har nu stöd för att ange attribut för telefonnummer till arbetet

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Vi har förbättrat programetablerings appen för Workday-installation så att den nu stöder tillbakaskrivning av arbetstelefonnummer och attribut för mobil nummer. Förutom e-post och användar namn kan du nu konfigurera appen för tillbakaskrivning av arbets dagar för att flöda telefonnummer värden från Azure AD till Workday. Mer information om hur du konfigurerar tillbakaskrivning av telefonnummer finns i själv studie kursen om [tillbakaskrivning av workday](../saas-apps/workday-writeback-tutorial.md) -appar. 

---

### <a name="publisher-verification-preview"></a>Utgivarens verifiering (för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Utvecklings miljö
 
Utgivarens verifiering (för hands version) hjälper administratörer och slutanvändare att förstå äktheten hos programutvecklare som integreras med Microsoft Identity Platform. Mer information finns i [utgivar verifiering (för hands version)](../develop/publisher-verification-overview.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Authorization Code Flow för appar på en sida

**Typ:** Ändrad funktions **tjänst kategori:** autentisering **produkt kapacitet:** utvecklings miljö

På grund av moderna webb läsar [begränsningar från tredje part, till exempel Safari ITP](../develop/reference-third-party-cookies-spas.md), måste SPAs använda kod flödet i stället för det implicita flödet för att upprätthålla SSO. MSAL.js v 2. x stöder nu Authorization Code Flow. Det finns motsvarande uppdateringar av Azure Portal så att du kan uppdatera din SPA och skriva "Spa" och använda kod flödet för autentisering. Anvisningar finns i [snabb start: Logga in användare och hämta en åtkomsttoken i ett Java Script spa med hjälp av kod flödet för autentisering](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Förbättrad filtrering för enheter finns i offentlig för hands version

**Typ:** Ändrad funktion   
**Tjänste kategori:** **Produkt kapacitet** för enhets hantering: hantering av enhetens livs cykel
 
Tidigare var de enda filter som du kan använda "aktiverade" och "aktivitets datum". Nu kan du [filtrera listan över enheter på fler egenskaper](../devices/device-management-azure-portal.md#device-list-filtering-preview), inklusive OS-typ, kopplings typ, efterlevnad med mera. De här tilläggen bör förenkla lokalisering av en viss enhet.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Den nya Appregistreringar upplevelsen för Azure AD B2C är nu allmänt tillgänglig

**Typ:** Ändrad funktion   
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Den nya Appregistreringar upplevelsen för Azure AD B2C är nu allmänt tillgänglig. 

Tidigare var du tvungen att hantera dina B2C-klientbaserade program separat från resten av dina appar med hjälp av den äldre program upplevelsen. Detta innebar att olika appar skapas på olika platser i Azure.

Den nya upplevelsen visar alla B2C app-registreringar och Azure AD App-registreringar på ett och samma ställe och ger ett konsekvent sätt att hantera dem. Oavsett om du behöver hantera en app som riktas mot kund eller en app som har åtkomst till Microsoft Graph för program mässigt Hantera Azure AD B2C resurser behöver du bara lära dig ett sätt att göra saker.

Du kan komma igång med den nya upplevelsen genom att navigera i Azure AD B2Cs tjänsten och välja bladet Appregistreringar. Upplevelsen är också tillgänglig från Azure Active Directorys tjänsten.

Azure AD B2C Appregistreringar-upplevelsen baseras på den allmänna [appens registrerings upplevelse](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) för Azure AD-klienter, men är skräddarsydd för Azure AD B2C. Den äldre program upplevelsen är inaktuell i framtiden.

Mer information finns [i den nya appens registrerings upplevelse för Azure AD B2C](../../active-directory-b2c/app-registrations-training-guide.md).

---

## <a name="april-2020"></a>April 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Den kombinerade registreringen av säkerhets information är nu allmänt tillgänglig

**Typ:** Ny funktion

**Tjänste kategori:** Autentiseringar (inloggningar)

**Produkt kapacitet:** & skydd för identitets säkerhet

Den kombinerade registrerings upplevelsen för Multi-Factor Authentication (MFA) och Self-Service återställning av lösen ord (SSPR) är nu allmänt tillgänglig. Den här nya registreringen gör det möjligt för användare att registrera sig för MFA-och SSPR i en enda steg-för-steg-process. När du distribuerar den nya upplevelsen för din organisation kan användarna registrera sig på kortare tid och med färre krångel. Kolla in blogg inlägget [här](https://bit.ly/3etiRyQ).

---

### <a name="continuous-access-evaluation"></a>Utvärdering av kontinuerlig åtkomst

**Typ:** Ny funktion

**Tjänste kategori:** Autentiseringar (inloggningar)

**Produkt kapacitet:** & skydd för identitets säkerhet

Utvärdering av kontinuerlig åtkomst är en ny säkerhetsfunktion som gör det möjligt att tillämpa principer på förlitande parter i nära real tid när händelser inträffar i Azure AD (t. ex. borttagning av användar konto). Vi rullar ut den här funktionen först för team-och Outlook-klienter. Läs vår [blogg](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) och  [dokumentation](../conditional-access/concept-continuous-access-evaluation.md)om du vill ha mer information.

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS-inloggning: firstline-anställda kan logga in till Azure AD-program med sina telefonnummer och inget lösen ord

**Typ:** Ny funktion

**Tjänste kategori:** Autentiseringar (inloggningar)

**Produkt kapacitet:** Användarautentisering

Office lanserar en serie mobila appar som går till icke-traditionella organisationer och anställda i stora organisationer som inte använder e-post som primär kommunikations metod. De här apparna riktar Frontline anställda, Deskless-arbetskrafter, fält agenter eller detalj handels anställda som inte får en e-postadress från sin arbetsgivare, har åtkomst till en dator eller till den. Med det här projektet kan de anställda logga in på företags program genom att ange ett telefonnummer och roundtripping en kod. Mer information finns i vår dokumentation om [administratör](../authentication/howto-authentication-sms-signin.md) [och slutanvändare](../user-help/sms-sign-in-explainer.md).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Bjud in interna användare att använda B2B-samarbete

**Typ:** Ny funktion

**Tjänste kategori:** Business

**Produkt kapacitet:**

Vi utökar B2B-Inbjudnings funktionen så att befintliga interna konton kan bjudas in att använda B2B-samarbets uppgifter som skickas framåt. Detta görs genom att skicka användarobjektet till inbjudans-API: t Förutom vanliga parametrar som den inbjudna e-postadressen. Användarens objekt-ID, UPN, grupp medlemskap, app-tilldelning etc. förblir intakt, men fortsätter att använda B2B för att autentisera med sina autentiseringsuppgifter för hem klienten i stället för de interna autentiseringsuppgifter som de använde före inbjudan. Mer information finns i [dokumentationen](../external-identities/invite-internal-users.md).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Endast rapport läge för villkorlig åtkomst är nu allmänt tillgänglig

**Typ:** Ny funktion

**Tjänste kategori:** Villkorlig åtkomst

**Produkt kapacitet:** & skydd för identitets säkerhet

Med [endast rapport läge för villkorlig åtkomst i Azure AD](../conditional-access/concept-conditional-access-report-only.md) kan du utvärdera resultatet av en princip utan att tvinga åtkomst kontroller. Du kan testa endast rapport principer i organisationen och förstå deras inverkan innan du aktiverar dem, vilket gör distributionen säkrare och enklare. Under de senaste månaderna har vi sett ett starkt införande av enbart rapport läge, med över 26M-användare som redan är i omfattning av en rapport princip. Med det här meddelandet skapas nya Azure AD-principer för villkorlig åtkomst i endast rapport läge som standard. Det innebär att du kan övervaka påverkan av dina principer från den tidpunkt då de skapas. Och för de som använder MS Graph API: er kan du även [Hantera rapport principer program mässigt](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Arbets boken för villkorlig åtkomst insikter och rapportering är allmänt tillgänglig

**Typ:** Ny funktion

**Tjänste kategori:** Villkorlig åtkomst

**Produkt kapacitet:** & skydd för identitets säkerhet

Arbets boken för villkorlig åtkomst [insikter och rapportering](../conditional-access/howto-conditional-access-insights-reporting.md) ger administratörer en översikt över villkorlig åtkomst för Azure AD i klienten. Med möjligheten att välja en enskild princip kan administratörer bättre förstå vad varje princip gör och övervaka ändringar i real tid. Arbets boken strömma data som lagras i Azure Monitor, som du kan konfigurera på några minuter [efter dessa instruktioner](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). För att instrument panelen ska bli mer synlig har vi flyttat den till fliken nya insikter och rapporter på menyn för villkorlig åtkomst i Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Bladet princip information för villkorlig åtkomst finns i offentlig för hands version

**Typ:** Ny funktion

**Tjänste kategori:** Villkorlig åtkomst

**Produkt kapacitet:** & skydd för identitets säkerhet

I [bladet ny princip information](../conditional-access/troubleshoot-conditional-access.md) visas vilka tilldelningar, villkor och kontroller som var uppfyllda under utvärderingen av principen för villkorlig åtkomst. Du kan komma åt bladet genom att välja en rad i flikarna **villkorlig åtkomst** eller **endast rapporter** i inloggnings informationen.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nya federerade appar är tillgängliga i Azure AD App Galleri – april 2020

**Typ:** Ny funktion

**Tjänste kategori:** Företags program

**Produkt kapacitet:** integration från tredje part

I april 2020 har vi lagt till dessa 31 nya appar med stöd för federation i app-galleriet: 

[SincroPool-appar](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [IWT anskaffnings Svit](../saas-apps/iwt-procurement-suite-tutorial.md) [Trend Micro Web Security(TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial) , Lunni, [EasySSO för JIRA](../saas-apps/easysso-for-jira-tutorial.md), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md), [Microsoft 365 flytta](https://app.mover.io/login), [högtalare](https://speakerengage.com/login.php), [belys](../saas-apps/honestly-tutorial.md) [, Ally, DutyFlow](../saas-apps/ally-tutorial.md) [, AlertMedia](https://app.dutyflow.nl/) [, gr8](../saas-apps/alertmedia-tutorial.md), Pendo [,](../saas-apps/highground-tutorial.md)Highground [,](../saas-apps/harmony-tutorial.md)Timetabling [, SynchroNet](../saas-apps/timetabling-solutions-tutorial.md), [Fortes](../saas-apps/synchronet-click-tutorial.md), [Litmus, GroupTalk](../saas-apps/gr8-people-tutorial.md) [, Frontify](../saas-apps/pendo-tutorial.md), MongoDB, TickitLMS, Coco [, Nitro](../saas-apps/frontify-tutorial.md), TMWS [empower](https://www.made-in-office.com/en/) [,](../saas-apps/nitro-productivity-suite-tutorial.md) [,](../saas-apps/fortes-change-cloud-tutorial.md), [GroupTalk](https://recorder.grouptalk.com/),, [,](../saas-apps/mongodb-cloud-tutorial.md) [TickitLMS Learn](../saas-apps/tickitlms-learn-tutorial.md) [,,](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [,](../saas-apps/litmus-tutorial.md), [Lunni](https://lunni.fi/)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](../azuread-dev/howto-app-gallery-listing.md).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Stöd för Microsoft Graph delta frågor för oAuth2PermissionGrant tillgängligt för offentlig för hands version

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings miljö

Delta fråga för oAuth2PermissionGrant är tillgänglig för offentlig för hands version! Nu kan du spåra ändringar utan att behöva söka Microsoft Graph. [Läs mer.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Stöd för Microsoft Graph delta frågor för organisations kontakt är allmänt tillgänglig

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings miljö

Delta frågor för organisatoriska kontakter är allmänt tillgängliga! Nu kan du spåra ändringar i produktions program utan att behöva avsöka Microsoft Graph kontinuerligt. Ersätt eventuell befintlig kod som kontinuerligt avsöker orgContact data efter delta-fråga för att förbättra prestanda avsevärt. [Läs mer.](/graph/api/orgcontact-delta?tabs=http&view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Stöd för Microsoft Graph delta frågor för programmet är allmänt tillgängligt

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings miljö

Delta fråga för program är allmänt tillgänglig! Nu kan du spåra ändringar i produktions program utan att behöva avsöka Microsoft Graph kontinuerligt. Ersätt eventuell befintlig kod som kontinuerligt avsöker program data efter delta-fråga för att förbättra prestanda avsevärt. [Läs mer.](/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Stöd för Microsoft Graph delta frågor för administrativa enheter som är tillgängliga för offentlig för hands version

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings upplevelse delta fråga för administrativa enheter är tillgänglig för offentlig för hands version! Nu kan du spåra ändringar utan att behöva söka Microsoft Graph. [Läs mer.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Hantera telefonnummer för autentisering och mer i nya Microsoft Graph beta-API: er

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings miljö

Dessa API: er är ett nyckel verktyg för att hantera dina användares autentiseringsmetoder. Nu kan du programmatiskt för att registrera och hantera de autentiserare som används för MFA och lösen ords återställning via självbetjäning (SSPR). Detta har varit en av de mest efterfrågade funktionerna i Azure MFA, SSPR och Microsoft Graph Spaces. De nya API: er som vi har lanserat i den här vågen ger dig möjlighet att:

- Läsa, lägga till, uppdatera och ta bort en användares telefoner för autentisering
- Återställa en användares lösen ord
- Aktivera och inaktivera SMS-inloggning

Mer information finns i [Översikt över Azure AD Authentication Methods API](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Den offentliga för hands versionen av administrativa enheter

**Typ:** Ny funktion

**Tjänste kategori:** Azure AD-roller

**Produkt kapacitet:** Access Control

Med administrativa enheter kan du bevilja administratörs behörigheter som är begränsade till en avdelning, region eller något annat segment i din organisation som du definierar. Du kan använda administrativa enheter för att delegera behörigheter till regionala administratörer eller ange en princip på en detaljerad nivå. En användar konto administratör kan till exempel uppdatera profil information, återställa lösen ord och tilldela licenser enbart för användare i sin administrativa enhet.

Med hjälp av administrativa enheter kan en central administratör:

- Skapa en administrativ enhet för decentraliserad hantering av resurser
- Tilldela en roll med administrativ behörighet enbart över Azure AD-användare i en administrativ enhet
- Fyll i de administrativa enheterna med användare och grupper efter behov

Mer information finns i [hantering av administrativa enheter i Azure Active Directory (för hands version)](../users-groups-roles/directory-administrative-units.md).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Inbyggda roller för skrivar administratör och skrivar tekniker

**Typ:** Ny funktion

**Tjänste kategori:** Azure AD-roller

**Produkt kapacitet:** Access Control

**Skrivar administratör** : användare med den här rollen kan registrera skrivare och hantera alla aspekter av alla skrivar konfigurationer i Microsofts Universal Print-lösning, inklusive inställningar för Universal Print Connector. De kan godkänna alla delegerade utskrifts behörighets begär Anden. Skrivar administratörer har även åtkomst till utskrifts rapporter. 

**Skrivar tekniker** : användare med den här rollen kan registrera skrivare och hantera skrivar status i Microsoft Universal Print-lösningen. De kan också läsa all anslutnings information. Viktiga aktiviteter en skrivare tekniker kan inte ange användar behörigheter för skrivare och dela skrivare. [Läs mer.](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Inbyggd rollen hybrid identitets administratör

**Typ:** Ny funktion

**Tjänste kategori:** Azure AD-roller

**Produkt kapacitet:** Access Control

Användare med den här rollen kan aktivera, konfigurera och hantera tjänster och inställningar för att aktivera hybrid identitet i Azure AD. Den här rollen ger möjlighet att konfigurera Azure AD till någon av de tre autentiseringsmetoder som stöds&#8212;(PHS), direktautentisering (PTA) eller Federation (AD FS eller tredjepartsleverantörer) &#8212;och för att distribuera relaterad lokal infrastruktur för att aktivera dem. Lokal infrastruktur innehåller etablerings-och PTA-agenter. Den här rollen ger möjlighet att aktivera sömlös enkel Sign-On (S-SSO) för att möjliggöra sömlös autentisering på icke-Windows 10-enheter eller datorer som inte är Windows Server 2016. Dessutom ger den här rollen möjlighet att se inloggnings loggar och för att få åtkomst till hälso-och analys funktioner för övervakning och fel sökning. [Läs mer.](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Inbyggd nätverks administratörs roll

**Typ:** Ny funktion

**Tjänste kategori:** Azure AD-roller

**Produkt kapacitet:** Access Control

Användare med den här rollen kan granska rekommendationer för nätverks perimeter arkitektur från Microsoft som baseras på telemetri från användarens platser. Nätverks prestanda för Microsoft 365 är beroende av en noggrann nätverks gräns arkitektur för företags nätverk, vilket vanligt vis är specifika för användare. Med den här rollen kan du redigera identifierade användar platser och konfigurera nätverks parametrar för de platserna för att under lätta förbättrad telemetri och bättre design rekommendationer. [Läs mer.](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Mass aktivitet och nedladdningar i Azure AD Admin Portal-upplevelsen

**Typ:** Ny funktion

**Tjänste kategori:** Användar hantering

**Produkt kapacitet:** Katalogen

Nu kan du utföra Mass aktiviteter för användare och grupper i Azure AD genom att ladda upp en CSV-fil i Azure AD Admin Portal-upplevelsen. Du kan skapa användare, ta bort användare och bjuda in gäst användare. Och du kan lägga till och ta bort medlemmar från en grupp.

Du kan också hämta listor över Azure AD-resurser från Azure AD Admin Portal-upplevelsen. Du kan hämta listan över användare i katalogen, listan över grupper i katalogen och medlemmarna i en viss grupp.

Om du vill ha mer information kan du läsa följande:

- [Skapa användare](../enterprise-users/users-bulk-add.md) eller [Bjud in gäst användare](../external-identities/tutorial-bulk-invite.md)
- [Ta bort användare](../enterprise-users/users-bulk-delete.md) eller [Återställ borttagna användare](../enterprise-users/users-bulk-restore.md)
- [Hämta lista över användare](../enterprise-users/users-bulk-download.md) eller [Hämta lista över grupper](../enterprise-users/groups-bulk-download.md)
- [Lägg till (importera) medlemmar](../enterprise-users/groups-bulk-import-members.md) eller [ta bort medlemmar](../enterprise-users/groups-bulk-remove-members.md) eller [Hämta en lista över medlemmar](../enterprise-users/groups-bulk-download-members.md) för en grupp

---

### <a name="my-staff-delegated-user-management"></a>Min personal har delegerat användar hantering

**Typ:** Ny funktion

**Tjänste kategori:** Användar hantering

**Produkt kapacitet:**

Min personal ger firstline-ansvariga, till exempel en butiks chef, för att säkerställa att deras personal medlemmar kan komma åt sina Azure AD-konton. I stället för att förlita dig på en central helpdesk kan organisationer delegera vanliga uppgifter, till exempel att återställa lösen ord eller ändra telefonnummer till en firstline Manager. Med min personal kan en användare som inte har åtkomst till sitt konto återfå åtkomst på bara några klick, utan supportavdelningen eller IT-personal som krävs. Mer information finns i [Hantera dina användare med min personal (för hands version)](../users-groups-roles/my-staff-configure.md) och [delegera användar hantering med min personal (för hands version)](../user-help/my-staff-team-manager.md).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>En uppgraderad slut användar upplevelse i åtkomst granskningar

**Typ:** Ändrad funktion

**Tjänste kategori:** Åtkomst granskningar

**Produkt kapacitet:** Identitets styrning

Vi har uppdaterat gransknings upplevelsen för åtkomst granskningar av Azure AD i portalen Mina appar. I slutet av april visas en banderoll som gör det möjligt för dina granskare som är inloggade i Azure AD Access granskare att se en banderoll som gör det möjligt för dem att testa den uppdaterade upplevelsen i min åtkomst. Observera att de uppdaterade åtkomst granskningarna har samma funktioner som den aktuella upplevelsen, men med ett förbättrat användar gränssnitt ovanpå nya funktioner som gör att användarna kan vara produktiva. [Du kan lära dig mer om den uppdaterade upplevelsen här](../governance/perform-access-review.md). Den här allmänna för hands versionen kommer att gälla till slutet av juli 2020. I slutet av juli kommer granskare som inte har valt förhands gransknings upplevelsen automatiskt att dirigeras till min åtkomst för att utföra åtkomst granskningar. Om du vill att granskarna ska växlas över till för hands versionen i min åtkomst nu, gör du [en begäran här](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Arbets dag för inkommande användar etablering och tillbakaskrivning av appar stöder nu de senaste versionerna av webb tjänstens API för arbets dagar

**Typ:** Ändrad funktion

**Tjänste kategori:** App-etablering

**Produkt kapacitet:** 

Baserat på kundfeedback har vi nu uppdaterat inetablerings-och tillbakaskrivning av workday-appar i Enterprise App-galleriet för att stödja de senaste versionerna av WWS-API: et för webb tjänster (Workday). Med den här ändringen kan kunderna ange den WWS-API-version som de vill använda i anslutnings strängen. Detta ger kunderna möjlighet att hämta fler HR-attribut som är tillgängliga i alla versioner av arbets dagen. Tillbakaskrivning-appen för Workday använder nu den rekommenderade Change_Work_Contact_Info Workday-webbtjänsten för att lösa Maintain_Contact_Infos begränsningar.

Om ingen version anges i anslutnings strängen fortsätter inkommande Provisioning-appar som standard att använda WWS v-21.1 för att växla till de senaste Workday-API: erna för inkommande användar etablering, kunder måste uppdatera anslutnings strängen som dokumenteras [i självstudien](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) och även uppdatera de XPath-attribut som används för Workday-attribut enligt beskrivningen i [referens hand boken för Workday-attributet](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30). 

Om du vill använda det nya API: t för tillbakaskrivning behöver du inte göra några ändringar i programetablerings appen för Workday-tillbakaskrivning. På sidan arbets dag ser du till att användar kontot för ISU-kontot (Workday) har behörighet att anropa Change_Work_Contact affärs process som dokumenteras i avsnittet självstudie, [Konfigurera säkerhets princip för affärs processer](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions). 

Vi har uppdaterat vår [själv studie guide](../saas-apps/workday-inbound-tutorial.md) för att återspegla den nya stöd för API-versionen.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Användare med rollen standard åtkomst är nu inom omfånget för etablering

**Typ:** Ändrad funktion

**Tjänste kategori:** App-etablering

**Produkt kapacitet:** Hantering av identitets livs cykel

Tidigare har användare med standard åtkomst rollen utanför definitions området för etablering. Vi har hört feedback om att kunderna vill att användare med den här rollen ska omfattas av etableringen. Från och med 16 april 2020 låter alla nya etablerings konfigurations användare med standard åtkomst rollen tillhandahållas. Gradvis kommer vi att ändra beteendet för befintliga etablerings konfigurationer som stöder etablering av användare med den här rollen. [Läs mer.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>Uppdaterat etablerings gränssnitt

**Typ:** Ändrad funktion

**Tjänste kategori:** App-etablering

**Produkt kapacitet:** Hantering av identitets livs cykel

Vi har uppdaterat vår etablerings upplevelse för att skapa en mer fokuserad hanterings vy. När du navigerar till etablerings bladet för ett företags program som redan har kon figurer ATS kan du enkelt övervaka förloppet för etablering och hantering av åtgärder som att starta, stoppa och starta om etableringen. [Läs mer.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Verifiering av dynamisk grupp regel är nu tillgängligt för offentlig för hands version

**Typ:** Ändrad funktion

**Tjänste kategori:** Grupp hantering

**Produkt kapacitet:** Samarbete

Azure Active Directory (Azure AD) ger nu möjlighet att verifiera dynamiska grupp regler. På fliken **validera regler** kan du verifiera din dynamiska regel mot exempel grupp medlemmar för att bekräfta att regeln fungerar som förväntat. När du skapar eller uppdaterar dynamiska grupp regler vill administratörer veta om en användare eller enhet kommer att vara medlem i gruppen. Detta hjälper till att utvärdera om en användare eller enhet uppfyller regel villkoren och hjälper till med fel sökning när medlemskap inte förväntas.

Mer information finns i [Validera en regel för dynamisk grupp medlemskap (för hands version)](../enterprise-users/groups-dynamic-rule-validation.md).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Identifiera säkra Poäng – säkerhets inställningar och uppdatering av MFA-förbättringar

**Typ:** Ändrad funktion

**Tjänste kategori:** EJ TILLÄMPLIGT

**Produkt kapacitet:** & skydd för identitets säkerhet

**Stöd för säkerhets inställningar för Azure AD-förbättringar:** Microsofts säkra poäng kommer att uppdatera förbättrings åtgärder för att ge stöd för [säkerhets inställningar i Azure AD](./concept-fundamentals-security-defaults.md), vilket gör det enklare att skydda din organisation med förkonfigurerade säkerhets inställningar för vanliga attacker. Detta kommer att påverka följande förbättrings åtgärder:

- Se till att alla användare kan slutföra Multi-Factor Authentication för säker åtkomst
- Kräv MFA för administrativa roller
- Aktivera princip för att blockera äldre autentisering
 
**Uppdatering av MFA-förbättringar:** För att avspegla behovet av företag för att säkerställa den högsta säkerheten vid användning av principer som fungerar med deras verksamhet har Microsoft Secure score tagit bort tre förbättringar av förbättringarna i mitten av Multi-Factor Authentication och lagt till två.

Borttagna förbättrings åtgärder:

- Registrera alla användare för Multi-Factor Authentication
- Kräv MFA för alla användare
- Kräv MFA för privilegierade Azure AD-roller

Ytterligare förbättrings åtgärder:

- Se till att alla användare kan slutföra Multi-Factor Authentication för säker åtkomst
- Kräv MFA för administrativa roller

Dessa nya förbättringar kräver att du registrerar dina användare eller administratörer för Multi-Factor Authentication (MFA) i din katalog och upprättar rätt uppsättning principer som passar organisationens behov. Huvud målet är att ha flexibilitet och samtidigt se till att alla användare och administratörer kan autentisera med flera faktorer eller riskfyllda identitets verifierings meddelanden. Det kan vara form av att ha flera principer som tillämpar definitions områden, eller ställa in säkerhets inställningar (från och med den 16 mars) som gör att Microsoft bestämmer sig för att utmana användare för MFA. [Läs mer om vad som är nytt i Microsofts säkra Poäng](/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---
