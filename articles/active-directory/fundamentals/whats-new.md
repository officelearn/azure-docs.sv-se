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
ms.date: 12/10/2018
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.openlocfilehash: b5f3c406996e47792d0a4b907d542066cf6f6e0f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55168048"
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?

>Håll dig informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` i din ![RSS-ikonen](./media/whats-new/feed-icon-16x16.png) feed läsare.

Azure AD får förbättringar med jämna mellanrum. Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

- Den senaste versionen
- Kända problem
- Felkorrigeringar
- Inaktuell funktion
- Planer för ändringar

Den här sidan uppdateras varje månad, så gå tillbaka till den regelbundet. Om du letar efter objekt som är äldre än 6 månader, kan du hitta dem i den [Arkiveringsjobb för vad som är nytt i Azure Active Directory](whats-new-archive.md).

---
## <a name="novemberdecember-2018"></a>November/December 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Ta bort användare från synkronisering omfång någon längre växel till molnbaserad konton

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

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Identity Protection förbättringar av övervakade machine learning-modellen och risken poäng motor

**Typ:** Ändrad funktion  
**Tjänstekategori:** Identity Protection  
**Produkten kapacitet:** Riskpoäng

Förbättringar av Identity Protection-relaterade användare och logga in risken utvärdering av motorn kan hjälpa till att förbättra användaren risk Precision och täckning. Administratörer kanske märker att risknivån är inte längre direkt kopplad till risknivån för specifika identifieringar och att det finns en ökning av antalet och andelen riskfyllda inloggningshändelser.

Risk identifieringar är nu utvärdera de övervakade machine learning-modell som beräknar användarrisk genom att använda ytterligare funktioner i användarens inloggningar och ett mönster av identifieringar. Baserat på den här modellen kan kanske administratören användare med hög riskpoäng, även om identifieringar som är associerade med användaren är för låg eller medelhög risk. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Administratörer kan återställa sina egna lösenord med hjälp av Microsoft Authenticator-appen (offentlig förhandsversion)

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

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Ny Molnenhetsadministratör för Azure AD-roll (offentlig förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** Hantering och registrering av enhet  
**Produkten kapacitet:** Åtkomstkontroll

Administratörer kan tilldela användare till den nya Molnenhetsadministratör rollen att utföra administrationsåtgärder i cloud enhet. Användare som har tilldelats rollen Cloud Enhetsadministratörer kan aktivera, inaktivera och ta bort enheter i Azure AD, tillsammans med ska kunna läsa Windows 10 BitLocker-nycklar (om sådan finns) i Azure-portalen.

Mer information om roller och behörigheter finns i [Tilldela administratörsroller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Hantera dina enheter med hjälp av den nya aktivitet tidsstämpeln i Azure AD (förhandsversion)

**Typ:** Ny funktion  
**Tjänstekategori:** Hantering och registrering av enhet  
**Produkten kapacitet:** Hantering av enhetslivscykel

Vi förstår att tiden du måste uppdatera och dra tillbaka din organisations enheter i Azure AD för att förhindra att inaktuella enheter hängande i din miljö. För att hjälpa till med den här processen, uppdaterar Azure AD nu dina enheter med en ny aktivitet tidsstämpel, vilket hjälper dig att hantera enhetslivscykeln för din.

Läs mer om hur du hämtar och använder den här tidsstämpeln [How To: Hantera inaktuella enheter i Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Administratörer kan kräva att användarna ska acceptera användningsvillkor på varje enhet

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning
 
Administratörer kan nu aktivera den **användare måste samtycka på alla enheter** att kräva användarna godkänna dina användningsvillkor på varje enhet de använder på din klient.

Mer information finns i den [Per enhet användningsvillkor Använd avsnittet Azure Active Directory villkoren för användning funktionen](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Administratörer kan konfigurera användningsvillkor att gå ut baserat på ett återkommande schema

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

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nya Azure AD Privileged Identity Management (PIM) e-postmeddelanden för Azure Active Directory-roller

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

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nya federerade appar är tillgängliga i appgalleriet för Azure AD - November 2018

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

## <a name="july-2018"></a>Juli 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Förbättringar av e-postfunktionen i Azure Active Directory

**Typ:** Ändrad funktion  
**Tjänstekategori:** Annat  
**Produkten kapacitet:** Identitetslivcykelhantering
 
Azure Active Directory (Azure AD) e-postmeddelanden har nu en uppdaterad design, samt ändringar i avsändarens e-postadress och visningsnamn för avsändaren, när de skickas från följande tjänster:
 
- Azure AD-Åtkomstgranskningar
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Enterprise-appen upphör att gälla certifikat-meddelanden
- Etablering tjänstmeddelanden i Enterprise-App
 
E-postmeddelanden skickas från följande e-postadress och ditt visningsnamn:

- E-postadress: azure-noreply@microsoft.com
- Visningsnamn: Microsoft Azure
 
Ett exempel på några av de nya e-designerna och mer information finns i [e-postmeddelanden i Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD-aktivitetsloggar är nu tillgängliga på Azure Monitor

**Typ:** Ny funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering

Azure AD-aktivitetsloggar finns nu i offentlig förhandsversion för Azure Monitor (Azures plattform hela övervakningstjänsten). Azure Monitor erbjuder långsiktig kvarhållning och smidig integrering, utöver dessa förbättringar:

- Långsiktig kvarhållning av Routning din loggfiler till din egen Azure storage-konto.

- Sömlös SIEM-integrering, utan att behöva skriva eller underhålla anpassade skript.

- Sömlös integrering med dina egna anpassade lösningar, analysverktyg eller lösningar för hantering av incidenter.

Mer information om de här nya funktionerna finns i vår blogg [Azure AD-aktivitetsloggar i Azure Monitor diagnostik finns nu i offentlig förhandsversion](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) och dokumentationen för [Azure Active Directory-aktivitetsloggar i Azure Övervakning (förhandsversion)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Information för villkorlig åtkomst har lagts till iAzure AD-inloggningsrapporten

**Typ:** Ny funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Identitetssäkerhet och skydd
 
Den här uppdateringen kan du se vilka principer utvärderas när en användare loggar in tillsammans med principen resultatet. Dessutom kan innehåller rapporten nu typ av klientapp som används av användaren, så att du kan identifiera äldre protokolltrafik. Rapporten poster kan nu även genomsökas för ett Korrelations-ID som finns i felmeddelandet användarinriktade och kan användas för att identifiera och Felsök matchande inloggningsbegäran.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Visa äldre autentiseringar via inloggningsaktivitetsloggar

**Typ:** Ny funktion  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering
 
Med introduktionen av den **Klientappen** fält i inloggningsaktivitet loggar, kunder kan nu se användare som använder äldre autentiseringar. Kunder kommer att kunna komma åt informationen med inloggningar MS Graph API eller via inloggningen aktivitetsloggar i Azure AD-portalen där du kan använda den **Klientappen** kontroll för att filtrera på äldre autentiseringar. Läs igenom dokumentationen för mer information.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nya federerade appar finns tillgängliga i Azure AD-appgalleriet – juni 2018

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration
 
I juli 2018 har vi lagt till dessa 16 nya appar med stöd för app-galleriet:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [vissa Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC mellanlagring [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [skärmbild-O-till](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial) , PowerSchool Unified klassrum, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial) , [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [kunskaper Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Ny användarförsörjning för SaaS-appintegreringar – juli 2018

**Typ:** Ny funktion  
**Tjänstekategori:** App-etablering  
**Produkten kapacitet:** Tredjepartsintegration
 
Azure AD kan du automatisera den genereringen, underhållet och borttagningen av användaridentiteter i SaaS-program, till exempel Dropbox, Salesforce, ServiceNow med mera. Vi har lagt till stöd för följande program i Azure AD-appgalleriet för användaretablering för juli 2018:

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

En lista över alla program som stöder etableringen av användare i Azure AD-galleriet finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health för synkronisering – ett enklare sätt att korrigera överblivna och dubblettattributsynkroniseringsfel på

**Typ:** Ny funktion  
**Tjänstekategori:** AD Connect  
**Produkten kapacitet:** Övervakning och rapportering
 
Azure AD Connect Health introducerar hjälpåtgärder för att du kan markera och åtgärda synkroniseringsfel. Den här funktionen felsöker Duplicerat attribut synkroniseringsfel och korrigeringar objekt som kopplas från Azure AD. Den här diagnos har följande fördelar:

- Begränsar Duplicerat attribut synkroniseringsfel att tillhandahålla specifikt korrigeringar

- Gäller en korrigering för särskilda scenarier för Azure AD, lösa problem i ett enda steg

- Ingen uppgradering eller konfiguration krävs för att aktivera och använda den här funktionen

Mer information finns i [diagnostisera och åtgärda synkroniseringsfel Duplicerat attribut](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Visuella uppdateringar av Azure AD och MSA-inloggningsupplevelserna

**Typ:** Ändrad funktion  
**Tjänstekategori:** Azure AD  
**Produkten kapacitet:** Användarautentisering

Vi har uppdaterat i Användargränssnittet för Microsoft online services-inloggningen, till exempel för Office 365 och Azure. Den här ändringen gör skärmarna mindre rörig och enklare. Mer information om den här ändringen finns i den [kommande förbättringar till Azure AD-inloggningen](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) blogg.

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>En ny version av Azure AD Connect – juli 2018

**Typ:** Ändrad funktion  
**Tjänstekategori:** App-etablering  
**Produkten kapacitet:** Livscykelhantering för identiteter

Den senaste versionen av Azure AD Connect innehåller: 

- Felkorrigeringar och support-uppdateringar 

- Allmän tillgänglighet för Ping-federera-integrering

- Uppdateringar till den senaste SQL 2012-klienten 

Mer information om den här uppdateringen finns i [Azure AD Connect: Versionshistorik](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-tou-end-user-ui"></a>Uppdateringar av användningsvillkoren för slutanvändargränssnittet

**Typ:** Ändrad funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning

Vi uppdaterar godkännande-strängen i Gränssnittet Användningsvillkoren för slutanvändare.

**Aktuell text.** För att komma åt resurser i [tenantName], måste du godkänna användningsvillkoren.<br>**Ny text.** Du måste läsa användningsvillkoren för att komma åt [tenantName] resurs.

**Aktuell text:** Om du väljer att acceptera innebär det att du godkänner alla ovanstående användningsvillkor.<br>**Ny text:** Klicka på Godkänn för att bekräfta att du har läst och förstått användningsvillkoren.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Direktautentisering stöder äldre protokoll och program

**Typ:** Ändrad funktion  
**Tjänstekategori:** Autentiseringar (inloggningar)  
**Produkten kapacitet:** Användarautentisering
 
Direktautentisering nu stöder äldre protokoll och appar. Följande begränsningar finns nu fullständigt stöd:

- Användarinloggningar till äldre Office-program, Office 2010 och Office 2013, utan modern autentisering.

- Åtkomst till delade kalendrar och ledig eller upptagen i Exchange hybridmiljöer på Office 2010 endast.

- Användarinloggningar till Skype för företag-klientprogram utan modern autentisering.

- Användarinloggningar till PowerShell version 1.0.

- I Apples DEP (Apple DEP), med hjälp av iOS-Installationsassistenten. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Hantering av konvergerad säkerhetsinformation för återställning av lösenord genom självbetjäning och Multi-Factor Authentication

**Typ:** Ny funktion  
**Tjänstekategori:** SSPR  
**Produkten kapacitet:** Användarautentisering

Den här nya funktionen kan användarna hantera deras säkerhetsinformation (till exempel telefonnummer, e-postadress, mobilapp och så vidare) för lösenordsåterställning via självbetjäning (SSPR) och Multi-Factor Authentication (MFA) i en enskild upplevelse. Användare kommer inte längre att registrera samma säkerhetsinformationen för SSPR och MFA i två olika upplevelser. Den här nya upplevelsen gäller även för användare som har SSPR eller MFA.

Om en organisation inte är tvingande MFA eller SSPR-registrering, användare kan registrera deras säkerhetsinformation via den **Mina appar** portal. Därifrån kan användare registrera andra metoder som aktiverats för MFA eller SSPR. 

Det här är en valbar offentlig förhandsversion. Administratörer kan aktivera den nya upplevelsen (om det behövs) för en grupp av användare eller alla användare i en klient.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Använd Microsoft Authenticator-appen när du ska verifiera din identitet då du återställer ditt lösenord

**Typ:** Ändrad funktion  
**Tjänstekategori:** SSPR  
**Produkten kapacitet:** Användarautentisering

Den här funktionen kan icke-administratörer verifiera sin identitet när du återställer ett lösenord med hjälp av en avisering eller kod från Microsoft Authenticator (eller andra authenticator-appen). När administratörer har aktiverat den här lösenordsåterställning via självbetjäning metod, användare som har registrerat en mobil app via aka.ms/mfasetup eller aka.ms/setupsecurityinfo kan använda sina mobila appar som en verifieringsmetod vid återställning av lösenordet.

Mobilappavisering kan endast aktiveras som en del av en princip som kräver två metoder för att återställa ditt lösenord.

---

## <a name="june-2018"></a>Juni 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Ändringsmeddelande: Säkerhetskorrigering till delegerad auktorisering flödet för appar som använder Azure AD-aktivitet loggar API

**Typ:** Förändringsplan  
**Tjänstekategori:** Rapportering  
**Produkten kapacitet:** Övervakning och rapportering

På grund av våra starkare säkerhet tvingande som vi har haft att göra en ändring i behörigheter för appar som använder ett flöde för delegerad auktorisering för att komma åt [Azure AD-aktivitet loggar API: er](https://aka.ms/aadreportsapi). Den här ändringen utförs av **26 juni 2018**.

Om någon av dina appar använder Azure AD-aktivitet Log API: er följer du stegen nedan för att se till att appen inte bryta när ändringen har skett.

**Uppdatera app-behörigheter**

1. Logga in på Azure portal, Välj **Azure Active Directory**, och välj sedan **Appregistreringar**.
2. Välj din app som använder Azure AD-aktivitet loggar API, Välj **inställningar**väljer **nödvändiga behörigheter**, och välj sedan den **Windows Azure Active Directory** API.
3. I den **delegerade behörigheter** område i den **Aktivera åtkomst** bladet markerar du kryssrutan bredvid **Läs directory** data och välj sedan **spara**.
4. Välj **bevilja**, och välj sedan **Ja**.
    
    >[!Note]
    >Du måste vara en Global administratör för att bevilja behörigheter till appen.

Mer information finns i den [bevilja](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) tänkbara kraven för att få åtkomst till Azure AD reporting API-artikel.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurera TLS för att ansluta till Azure AD-tjänster för PCI DSS-kompatibilitet

**Typ:** Ny funktion  
**Tjänstekategori:** Gäller inte  
**Produkten kapacitet:** Plattform

Transport Layer Security (TLS) är ett protokoll som ger sekretess och integritet mellan två kommunicerande program och är den vanligaste säkerhetsprotokoll som används idag.

Den [PCI Security Standards Council](https://www.pcisecuritystandards.org/) har fastställt att tidiga versioner av TLS och Secure Sockets Layer (SSL) måste inaktiveras av aktiverar nya och säkrare protokoll med efterlevnad som börjar på **den 30 juni 2018**. Den här ändringen innebär att om du ansluter till Azure AD-tjänster och kräva PCI DSS-efterlevnad, måste du inaktivera TLS 1.0. Flera versioner av TLS finns, men TLS 1.2 är den senaste versionen som är tillgängliga för Azure Active Directory Services. Vi rekommenderar starkt att flytta direkt till TLS 1.2 för både klient/server och webbläsaren/server kombinationer.

Inaktuella webbläsare stöder inte nyare TLS-versioner, till exempel TLS 1.2. Om du vill se vilka versioner av TLS som stöds av webbläsaren, gå till den [Qualys SSL Labs](https://www.ssllabs.com/) och klicka **testa din webbläsare**. Vi rekommenderar att du uppgraderar till den senaste versionen av din webbläsare och aktivera helst endast TLS 1.2.

**Aktivera TLS 1.2 i webbläsare**

- **Microsoft Edge och Internet Explorer (båda anges med hjälp av Internet Explorer)**

    1. Öppna Internet Explorer, Välj **verktyg** > **Internetalternativ** > **Avancerat**.
    2. I den **Security** Välj **använda TLS 1.2**, och välj sedan **OK**.
    3. Stänga alla webbläsarfönster och starta om Internet Explorer. 

- **Google Chrome**

    1. Öppna Google Chrome, typ *chrome://settings/* i adressfältet och tryck på **RETUR**.
    2. Expandera den **Avancerat** alternativ, gå till den **System** område och välj **öppna proxyinställningar**.
    3. I den **Internetegenskaper** väljer den **Avancerat** fliken, gå till den **Security** Välj **använda TLS 1.2**, och välj sedan  **OK**.
    4. Stänga alla webbläsarfönster och starta om Google Chrome.

- **Mozilla Firefox**

    1. Öppna Firefox, typ *om: config* i adressfältet och tryck sedan på **RETUR**.
    2. Sök efter period, *TLS*, och välj sedan den **security.tls.version.max** posten.
    3. Ange värdet till **3** att tvinga webbläsaren att använda upp till version TLS 1.2 och välj sedan **OK**.

        >[!NOTE]
        >Firefox version 60,0 stöder TLS 1.3 så att du kan också ange security.tls.version.max-värdet **4**.

    4. Stänga alla webbläsarfönster och starta om Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nya federerade appar är tillgängliga i appgalleriet för Azure AD - juni 2018

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Tredjepartsintegration
 
I juni 2018 har vi lagt till stöd för dessa 15 nya appar med Federation i app-galleriet:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [reglera musik](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 Token aktiverat LOB-App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [ Säkerhetskopiering](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh nätverk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho en](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [ Lokal SharePoint-](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [förutser CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD-lösenordsskydd är allmänt tillgänglig förhandsversion

**Typ:** Ny funktion  
**Tjänstekategori:** Identity Protection  
**Produkten kapacitet:** Användarautentisering

Använd Azure AD-lösenordsskydd för att eliminera enkelt att gissa lösenord från din miljö. Eliminera lösenorden hjälper till att minska risken för angrepp mot ett lösenord besprutningsmedel typen av attacker.

Mer specifikt Azure AD-lösenordsskydd hjälper dig att:

- Skydda din organisations konton i både Azure AD och Windows Server Active Directory (AD). 
- Stoppar dina användare använder lösenord på en lista med mer än 500 av de vanligaste lösenorden och över 1 miljon tecken ersättningen varianter av dessa lösenord.
- Administrera Azure AD-lösenord skydd från en enda plats i Azure AD-portalen för både Azure AD och lokala Windows Server AD.

Läs mer om Azure AD-lösenordsskydd [eliminera felaktiga lösenord i din organisation](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Ny ”alla gäster” villkorlig åtkomst Principmall när användningsvillkor (ToU) skapades

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning

När du skapar dina användningsvillkor (ToU) skapas också en ny mall för princip för villkorlig åtkomst för ”alla gäster” och ”alla appar”. Den här nya principmallen gäller den nyligen skapade ToU effektivisera skapandet och tvingande processen för gäster.

Mer information finns i [Azure Active Directory-villkoren i Använd funktion](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Nya principmallen för ”anpassad” villkorlig åtkomst när användningsvillkor (ToU) skapades

**Typ:** Ny funktion  
**Tjänstekategori:** Användningsvillkor  
**Produkten kapacitet:** Styrning

När du skapar dina användningsvillkor (ToU) skapas också en ny ”anpassad” Principmall för villkorlig åtkomst. Den här nya principmallen kan du skapa användningsvillkoren och sedan omedelbart gå till principbladet skapa villkorlig åtkomst utan att behöva gå manuellt via portalen.

Mer information finns i [Azure Active Directory-villkoren i Använd funktion](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nya och heltäckande vägledning om hur du distribuerar Azure Multi-Factor Authentication

**Typ:** Ny funktion  
**Tjänstekategori:** Annat  
**Produkten kapacitet:** Identitetssäkerhet och skydd
 
Vi har släppt nya stegvisa anvisningar om hur du distribuerar Azure Multi-Factor Authentication (MFA) i din organisation.

Om du vill visa i distributionsguiden för MFA, går du till den [identitet distributionsguider](https://aka.ms/DeploymentPlans) lagringsplatsen på GitHub. Om du vill ge feedback om distributionsguiden, använda den [distributionen planera feedbackformulär](https://aka.ms/deploymentplanfeedback). Om du har frågor om distributionsguiden kontaktar du oss på [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD delegerad apphantering roller finns i offentlig förhandsversion

**Typ:** Ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapacitet:** Åtkomstkontroll

Administratörer kan nu delegera hanteringsuppgifter utan att tilldela rollen som Global administratör. De nya roller och funktioner är:

- **Nya standarden administratörsroller i Azure AD:**

    - **Programadministratör.** Ger möjlighet att hantera alla aspekter av alla appar, inklusive registrering, inställningar för enkel inloggning, tilldelning av appar och licensiering, App proxy-inställningar och godkännande (förutom till Azure AD-resurser).

    - **Molnprogramadministratör.** Beviljar alla programadministratör funktioner, förutom App proxy eftersom det inte ger åtkomst till lokala.

    - **Programutvecklare.** Ger möjlighet att skapa appregistreringar, även om den **Tillåt användare att registrera appar** alternativet är inaktiverat.

- **Ägarskap (Konfigurera per app-registrering och per företag-appen, liknar grupp ägarskapet processen:**
 
    - **Appregistrering ägare.** Ger möjlighet att hantera alla aspekter av ägda appregistrering, inklusive appmanifestet och lägga till ytterligare ägare.

    - **Enterprise Appägaren.** Ger dig möjlighet att hantera många aspekter av ägda enterprise-appar, inklusive inställningar för enkel inloggning, tilldelning av appar och godkännande (förutom till Azure AD-resurser).

Läs mer om offentlig förhandsversion, den [Azure AD delegerad programhantering roller finns i offentlig förhandsversion!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blogg. Mer information om roller och behörigheter finns i [Tilldela administratörsroller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---
