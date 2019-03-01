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
ms.openlocfilehash: 98f4724e419e0ad07c7d01f1cfe95bbde001e236
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194596"
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?

>Håll dig informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` i din ![RSS-ikonen](./media/whats-new/feed-icon-16x16.png) feed läsare.

Azure AD får förbättringar med jämna mellanrum. Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

- Den senaste versionen
- Kända problem
- Felkorrigeringar
- Inaktuell funktion
- Planer för ändringar

Den här sidan uppdateras varje månad, så gå tillbaka till den regelbundet. Om du letar efter objekt som är äldre än sex månader kan du hitta dem i den [Arkiveringsjobb för vad som är nytt i Azure Active Directory](whats-new-archive.md).

---
## <a name="february-2019"></a>Februari 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurerbara Azure AD SAML-tokenkryptering (offentlig förhandsversion) 

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Enkel inloggning
 
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
**Produkten kapacitet:** Tredjepartsintegration
 
I januari 2019 har vi lagt till stöd för dessa 27 nya appar med Federation i app-galleriet:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), [ Klicka på behörighet](https://docs.microsoft.com/azure/active-directory/saas-apps/permissionclick-tutorial), [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [ Seismiska](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [resurs A dröm](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods integrering molnet](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Knowledge var som helst LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope Data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud av Genesys ](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp produktivitet plattform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Förbättrad kombinerade MFA/SSPR-registrering

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

2.  Om den **användare som kan använda funktionerna förhandsgranskning för att registrera och hantera säkerhetsinformation** alternativet är inställt på **ingen**, alternativet tas bort från din klient.

Förhandsgranska upplevelse för användare, oavsett om du tidigare har aktiverat den gamla kombinerade MFA/SSPR-registreringen eller inte, den gamla upplevelsen kommer att stängas av för ett datum i framtiden. På grund av detta föreslår starkt vi att du flyttar till den nya och förbättrade upplevelsen så snart som möjligt.

Läs mer om registrering av förbättrad upplevelse, den [lågfrekvent förbättringar av Azure AD kombineras MFA och återställning av lösenord registrering](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Uppdaterade policyn hanteringsupplevelse för användarflöden

**Typ:** Ändrad funktion  
**Tjänstekategori:** B2C – konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C

Vi har uppdaterat principen skapandet och hanteringen processen för användarflöden (tidigare kallat, inbyggda principer) enklare. Den här nya upplevelsen är nu standard för alla Azure AD-klienter.

Du kan ange ytterligare feedback och förslag med hjälp av Leende eller frown ikonerna i det **Skicka oss feedback** överst i portalen skärmen.

Mer information om den nya princip hanteringsupplevelsen finns i den [Azure AD B2C har nu JavaScript anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogg.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Välj specifik sida elementet versioner som tillhandahålls av Azure AD B2C

**Typ:** Ny funktion  
**Tjänstekategori:** B2C – konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C

Du kan nu välja en specifik version av sidelement som tillhandahålls av Azure AD B2C. Genom att välja en specifik version, kan du testa uppdateringarna innan de visas på en sida och du kan få förutsägbara beteende. Dessutom kan du nu välja för att framtvinga specifik sida versioner om du vill tillåta JavaScript anpassningar. Om du vill aktivera den här funktionen går du till den **egenskaper** sida i dina användarflöden.

Läs mer om hur du väljer specifika versioner för sidelement i [Azure AD B2C har nu JavaScript anpassning och många fler nya funktioner](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogg.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurerbara slutanvändarens lösenordskrav för B2C (GA)

**Typ:** Ny funktion  
**Tjänstekategori:** B2C – konsumentidentitetshantering  
**Produkten kapacitet:** B2B/B2C

Du kan nu uttryckligen ställa in din organisations lösenordskomplexitet för slutanvändarna, i stället för att använda din ursprungliga Azure AD-lösenordsprincip. Från den **egenskaper** bladet för dina användare flöden (kallades tidigare din inbyggda principer), som du kan välja ett lösenordskomplexitet **enkel** eller **stark**, eller så kan du Skapa en **anpassad** kraven.

Mer information om konfiguration av lösenord komplexiteten krav finns i [konfigurera komplexitetskrav för lösenord i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Ny standardmallarna för anpassade företagsanpassad autentiseringsupplevelse

**Typ:** Ny funktion  
**Tjänstekategori:** B2C – konsumentidentitetshantering  
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

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nya cookieinställningar för Azure AD-programproxyn

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
|Kategori|Det var den **tjänstnamn** fält. Nu är det den **Audit kategorier** fält. **Tjänstnamnet** har bytt namn till den **loggedByService** fält.|<ul><li>Kontoetablering</li><li>Kärnkatalog</li><li>Lösenordsåterställning via självbetjäning</li></ul>|<ul><li>Användarhantering</li><li>Grupphantering</li><li>Apphantering</li></ul>|
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

Vi förstår att tiden du måste uppdatera och dra tillbaka din organisations enheter i Azure AD för att förhindra att inaktuella enheter hängande i din miljö. För att hjälpa till med den här processen, uppdaterar Azure AD nu dina enheter med en ny aktivitet tidsstämpel, vilket hjälper dig att hantera enhetslivscykeln för din.

Läs mer om hur du hämtar och använder den här tidsstämpeln [How To: Hantera inaktuella enheter i Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratörer kan kräva att användare att godkänna användningsvillkor på varje enhet

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning
 
Administratörer kan nu aktivera den **användare måste samtycka på alla enheter** att kräva användarna godkänna dina användningsvillkor på varje enhet de använder på din klient.

Mer information finns i den [Per enhet användningsvillkor Använd avsnittet Azure Active Directory villkoren för användning funktionen](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratörer kan konfigurera användningsvillkor att förfalla enligt ett återkommande schema

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning
 

Administratörer kan nu aktivera den **upphör att gälla medgivanden** alternativ för att göra användningsvillkor upphör att gälla för alla användare baserat på ditt angivna återkommande schema. Schemat kan vara årligen, bi årligen, Kvartalsvis eller varje månad. Användningsvillkoren förnyas och måste användarna godkänna.

Mer information finns i den [lägga till användningsvillkor Använd avsnittet Azure Active Directory villkoren för användning funktionen](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Administratörer kan konfigurera användningsvillkor att gå ut baserat på varje användares schema

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning

Administratörer kan nu ange en varaktighet som användaren måste godkänna användningsvillkor. Administratörer kan till exempel ange att användare måste godkänna användningsvillkoren efter 90 dagar.

Mer information finns i den [lägga till användningsvillkor Använd avsnittet Azure Active Directory villkoren för användning funktionen](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use).
 
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

## <a name="september-2018"></a>September 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Uppdaterade rolladministratörsbehörigheter för dynamiska grupper

**Typ:** Korrigerat  
**Tjänstekategori:** Grupphantering  
**Produkten kapacitet:** Samarbete

Vi har ett problem har åtgärdats så att specifika administratörsroller kan nu skapa och uppdatera regler för dynamiskt medlemskap, utan att behöva vara ägare av gruppen.

Rollerna är:

- Global administratör eller företagets skrivare

- Administratör för Intune-tjänsten

- Användarkonto-administratör

Mer information finns i [skapa en dynamisk grupp och kontrollera status](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Konfigurationsinställningar för förenklad enkel inloggning (SSO) för vissa tredjepartsappar

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Enkel inloggning

Vi förstår att ställa in enkel inloggning (SSO) för programvara som en tjänst (SaaS)-appar kan vara svårt på grund av den unika natur varje apps-konfiguration. Vi har skapat en förenklad konfigurationsupplevelse för att automatiskt fylla SSO-konfigurationsinställningarna för följande tredje parts SaaS-appar:

- Zendesk

- ArcGis Online

- Jamf Pro

Om du vill börja använda den här upplevelsen med ett klick, går du till den **Azure-portalen** > **SSO-konfiguration** för appen. Mer information finns i [SaaS-programintegration med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – Sidan Var finns dina data?

**Typ:** Ny funktion  
**Tjänstekategori:** Annat  
**Produkten kapacitet:** GoLocal

Välj ditt företags region från den **Azure Active Directory – var finns dina data** sidan för att visa vilket Azure-datacenter innehåller din Azure AD-data i vila för alla tjänster som Azure AD. Du kan filtrera informationen efter specifika Azure AD-tjänster för ditt företags region.

Att komma åt den här funktionen och mer information finns i [Azure Active Directory – var finns dina data](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>En ny distributionsplan är tillgänglig för panelen Min appåtkomst

**Typ:** Ny funktion  
**Tjänstekategori:** Mina appar  
**Produkten kapacitet:** Enkel inloggning

Kolla in den nya distributionsplanen som är tillgänglig för Mina appar åtkomstpanelen (https://aka.ms/deploymentplans).
Mina appar åtkomstpanelen ger användare en och samma plats för att hitta och komma åt sina appar. Portalen innehåller också användare med självbetjäning möjligheter som begär åtkomst till appar och grupper, eller hantera åtkomst till dessa resurser å andras vägnar.

Mer information finns i [vad är portalen Mina appar?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>En ny flik för felsökning och support på sidan Inloggningsloggar i Azure Portal

**Typ:** Ny funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering

Den nya **felsökning och Support** fliken på den **inloggningar** sidan på Azure-portalen är avsedd att hjälpa administratörer och supporttekniker felsöka problem med Azure AD-inloggningar. Den här nya fliken visar felkod, felmeddelande och rekommenderade åtgärder (i förekommande fall) för att lösa problemet. Om det inte går att lösa problemet, vi även ger dig ett nytt sätt att skapa en support biljett med den **kopiera till Urklipp** uppstår som fyller på **ID för begäran** och **datum (UTC)** fält för loggfilen i ditt supportärende.  

![Logga in loggar med den nya fliken](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Förbättrad support för anpassade tilläggsegenskaper som används för att skapa dynamiska medlemskapsregler

**Typ:** Ändrad funktion  
**Tjänstekategori:** Grupphantering  
**Produkten kapacitet:** Samarbete

Med den här uppdateringen kan du nu klickar på den **hämta anpassade tilläggsegenskaper** länka från dynamisk användare grupp regeln builder, ange din unika app-ID och få en fullständig lista över anpassat tilläggs-egenskaper som ska användas när du skapar en dynamisk medlemskapsregel för användare. Den här listan kan också uppdateras för att hämta alla nya anpassade tilläggsegenskaper för appen.

Läs mer om hur du använder för anpassade tilläggsegenskaper för regler för dynamiskt medlemskap [tilläggsegenskaper och anpassade tilläggsegenskaper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för Microsoft Azure Active Directory-appbaserad villkorlig åtkomst

**Typ:** Förändringsplan  
**Tjänstekategori:** Villkorlig åtkomst  
**Produkten kapacitet:** Identitetssäkerhet och skydd

Följande appar är i listan över [godkända klientappar](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Mer information finns i:

- [Azure AD-appbaserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Ny support återställning av lösenord från låsskärmen i Windows 7/8/8.1

**Typ:** Ny funktion  
**Tjänstekategori:** SSPR  
**Produkten kapacitet:** Användarautentisering

När du har konfigurerat den här nya funktionen kan användarna ser en länk för att återställa sina lösenord från den **Lås** skärmen i en enhet som kör Windows 7, Windows 8 eller Windows 8.1. Genom att klicka på länken, leds användaren genom samma flöde för återställning av lösenord som via en webbläsare.

Mer information finns i [hur du aktiverar lösenordsåterställning från Windows 7, 8 och 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Ändringsmeddelande: Auktoriseringskoder längre inte tillgängliga för återanvändning 

**Typ:** Förändringsplan  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Användarautentisering

Från och med 15 November 2018, att Azure AD sluta acceptera tidigare autentisering-koder för appar. Ändringen security hjälper dig att ta med Azure AD i enlighet med OAuth-specifikationen och tillämpas på både v1 och v2-slutpunkter.

Om din app återanvänder auktoriseringskoder för att hämta token för flera resurser, rekommenderar vi att du använda koden för att få en uppdateringstoken och sedan använda den uppdateringstoken för att hämta ytterligare token för andra resurser. Auktoriseringskoder kan bara användas en gång, men uppdateringstoken kan användas flera gånger i flera resurser. En app som försöker att återanvända en Autentiseringskod under flödet för OAuth-kod får ett invalid_grant-fel.

Det här och andra protokoll-relaterade ändringar finns i [en fullständig lista över vad som är nytt för autentisering](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nya federerade appar finns tillgängliga i Microsoft Azure Active Directory-appgalleriet – september 2018

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration
 
I September 2018 har vi lagt till stöd för dessa 16 nya appar med Federation i app-galleriet:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet rekrytering programvara](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [ JDA molnet](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO för Azure, SurveyMonkey [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Support för ytterligare anspråksomvandlingsmetoder

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Enkel inloggning

Vi har introducerat nya anspråk omvandling-metoder, ToLower() och ToUpper() som kan tillkomma för SAML-tokens från SAML-baserad **konfigurationen för enkel inloggning** sidan.

Mer information finns i [anpassa anspråk som utfärdats i SAML-token för företagsprogram i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Uppdaterat användargränssnitt för SAML-baserad appkonfiguration (förhandsversion)

**Typ:** Ändrad funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Enkel inloggning

Som en del av vår uppdaterade SAML-baserad konfiguration av Användargränssnittet får du:

- En uppdaterad genomgång för att konfigurera din SAML-baserade appar.

- Bättre synbarhet om vad som är saknas eller är felaktig i konfigurationen.

- Möjligheten att lägga till flera e-postadresser för meddelande om utgången certifikat.

- Nya metoder för omvandling av anspråk, ToLower() och ToUpper() med mera.

- Ett sätt att överföra din egen token signeringscertifikatet för dina företagsappar.

- Ett sätt att ange NameID-Format för SAML-appar och ett sätt att ange NameID-värde som Katalogtillägg.

Om du vill aktivera den här uppdaterade vyn klickar du på den **testa vår nya upplevelsen** länken högst upp på den **enkel inloggning** sidan. Mer information finns i [Självstudie: Konfigurera SAML-baserad enkel inloggning för ett program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).

---

## <a name="august-2018"></a>Augusti 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Ändringar i Azure Active Directory IP-adressintervall

**Typ:** Förändringsplan  
**Tjänstekategori:** Annat  
**Produkten kapacitet:** Plattform

Vi presenterar större IP-adressintervall till Azure AD, vilket innebär att om du har konfigurerat Azure AD-IP-adressintervall för brandväggar, routrar eller Nätverkssäkerhetsgrupperna, måste du uppdatera dem. Vi gör den här uppdateringen så att du inte behöver ändra din brandvägg eller router Network Security grupper IP-konfigurationer för intervallet igen när Azure AD lägger till nya slutpunkter. 

Nätverkstrafik flyttas till dessa nya områden under de kommande två månaderna. Om du vill fortsätta med undvika avbrott i tjänsten, måste du lägga till de här uppdaterade värden till IP-adresser innan den 10 September 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

Vi rekommenderar starkt att inte ta bort de gamla IP-adressintervall tills all din nätverkstrafik har flyttats till de nya intervall. Uppdateringar om flytten och vill veta när du tar bort de gamla intervall, se [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Ändringsmeddelande: Auktoriseringskoder längre inte tillgängliga för återanvändning 

**Typ:** Förändringsplan  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Användarautentisering

Från och med 15 November 2018, att Azure AD sluta acceptera tidigare autentisering-koder för appar. Ändringen security hjälper dig att ta med Azure AD i enlighet med OAuth-specifikationen och tillämpas på både v1 och v2-slutpunkter.

Om din app återanvänder auktoriseringskoder för att hämta token för flera resurser, rekommenderar vi att du använda koden för att få en uppdateringstoken och sedan använda den uppdateringstoken för att hämta ytterligare token för andra resurser. Auktoriseringskoder kan bara användas en gång, men uppdateringstoken kan användas flera gånger i flera resurser. En app som försöker att återanvända en Autentiseringskod under flödet för OAuth-kod får ett invalid_grant-fel.

Det här och andra protokoll-relaterade ändringar finns i [en fullständig lista över vad som är nytt för autentisering](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Hantering av konvergerad säkerhetsinformation för återställning av självbetjäningslösenord och Multi-Factor Authentication (MFA)

**Typ:** Ny funktion  
**Tjänstekategori:** SSPR  
**Produkten kapacitet:** Användarautentisering
 
Den här nya funktionen hjälper människor att hantera deras säkerhetsinformation (till exempel, telefonnummer, mobilapp och så vidare) för SSPR och MFA i en enda plats och erfarenheter. jämfört med tidigare, där den har utförts på två olika platser.

Den här konvergerade upplevelsen fungerar även för personer som använder SSPR eller MFA. Även om organisationen inte framtvinga MFA eller SSPR-registrering, kan personer fortfarande registrera MFA eller SSPR info säkerhetsmetoder tillåts av din organisation från portalen Mina appar.

Det här är en valbar offentlig förhandsversion. Administratörer kan aktivera den nya upplevelsen (om det behövs) för en vald grupp eller för alla användare i en klient. Läs mer om konvergerade upplevelse, den [konvergerat experience-bloggen](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nya HTTP-Only-cookies i Microsoft Azure Active Directory-programproxyappar

**Typ:** Ny funktion  
**Tjänstekategori:** App Proxy  
**Produkten kapacitet:** Åtkomstkontroll

Det finns en ny inställning som kallas, **HTTP-Only Cookies** i dina appar i programproxyn. Den här inställningen ger extra säkerhet, inklusive flaggan HTTPOnly i HTTP-Svarsrubrik för båda Application Proxy åtkomst- och sessionsprinciper cookies, Stopp åtkomst till cookien från ett skript på klientsidan och förhindrar ytterligare åtgärder som att kopiera eller Ändra cookien. Även om den här flaggan inte har använts tidigare, cookies har alltid varit krypterat och överförs med hjälp av en SSL-anslutning för att skydda mot felaktig ändringar.

Den här inställningen är inte kompatibelt med appar som använder ActiveX-kontroller, till exempel Remote Desktop. Om du är i det här fallet rekommenderar vi att du stänger av den här inställningen.

Mer information om inställningen HTTP-Only Cookies finns i [publicera program med Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) för Azure-resurser stöder hanteringsgruppsresurstyper

**Typ:** Ny funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapacitet:** Privileged Identity Management
 
Inställningar för aktivering och tilldelning av just-In-Time kan nu användas Hanteringsgruppen resurstyper, precis som du redan har gjort för prenumerationer, resursgrupper och resurser (till exempel virtuella datorer, Apptjänster med mera). Vem som helst med en roll med administratörsåtkomst för en Hanteringsgrupp kan dessutom identifiera och hantera resursen i PIM.

Mer information om PIM och Azure-resurser finns i [identifiera och hantera Azure-resurser med hjälp av Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Programåtkomst (förhandsversion) ger snabbare åtkomst till Microsoft Azure Active Directory-portalen

**Typ:** Ny funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapacitet:** Privileged Identity Management
 
När du aktiverar en roll med hjälp av PIM idag, kan det ta över 10 minuter för behörigheterna som ska börja gälla. Om du väljer att använda programåtkomst, som finns för närvarande i förhandsversion, Administratörer kan komma åt Azure AD-portalen när aktiveringsbegäran har slutförts.

Programåtkomst stöder för närvarande bara Azure AD-portalen och Azure-resurser. Läs mer om PIM och programåtkomst [vad är Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nya federerade appar finns tillgängliga i appgalleriet för Microsoft Azure Active Directory – augusti 2018

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration
 
I augusti 2018 har vi lagt till dessa 16 nya appar med stöd för app-galleriet:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline som har delats upp](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [sås Labs - Mobile- och Web testning](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta nätverk Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [sätt att göra](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (genom Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentation](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - telekommunikation rapporter](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live-chatt](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [ Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Inbyggd Tableau-support finns nu i Microsoft Azure Active Directory-programproxy

**Typ:** Ändrad funktion  
**Tjänstekategori:** App Proxy  
**Produkten kapacitet:** Åtkomstkontroll

Med vår uppdateringen från OpenID Connect beviljande via OAuth 2.0-protokollet för vår förautentisering-protokollet kan behöver du inte längre någon ytterligare konfiguration för att använda Tableau med Application Proxy. Den här ändringen av protokollet hjälper också till Application Proxy bättre stöd för mer moderna appar med hjälp av endast HTTP-omdirigeringar, som ofta stöds i JavaScript och HTML-taggar.

Läs mer om våra inbyggt stöd för bland annat Tableau, [Azure AD-programproxyn nu med inbyggt stöd för bland annat Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Ny support för att lägga till Google som en identitetsprovider för B2B-gästanvändare i Azure Active Directory (förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** B2B  
**Produkten kapacitet:** B2B/B2C

Genom att konfigurera federation med Google i din organisation, kan du låta inbjudna Gmail användarna loggar in din delade appar och resurser med hjälp av sina befintliga Google-konto utan att behöva skapa en personlig Account (MSA: er) eller ett Azure AD-konto.

Det här är en valbar offentlig förhandsversion. Läs mer om Google federation [lägga till Google som en identitetsleverantör för B2B-gästanvändare](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---
