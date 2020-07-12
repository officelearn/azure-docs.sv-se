---
title: Nyheter Viktig information – Azure Active Directory | Microsoft Docs
description: Lär dig mer om vad som är nytt med Azure Active Directory, till exempel senaste versions information, kända problem, fel korrigeringar, inaktuella funktioner och kommande ändringar.
services: active-directory
author: msaburnley
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbf811fdb8ff051ec1bb782eb3da12ea45b29bdf
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86256824"
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?

>Bli informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL: en `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` i din ![ RSS feed reader-ikon ](./media/whats-new/feed-icon-16x16.png) feed reader.

Azure AD tar emot förbättringar kontinuerligt. För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar
- Föråldrade funktioner
- Planer för ändringar

Den här sidan uppdateras varje månad, så du kan uppdatera den regelbundet. Om du letar efter objekt som är äldre än sex månader kan du hitta dem i [arkivet för nyheter i Azure Active Directory](whats-new-archive.md).

---

## <a name="june-2020"></a>Juni 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Villkor för användar risk i princip för villkorlig åtkomst

**Typ:** Planera för ändring  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet
 

Med stöd för användar risker i Azure AD-princip för villkorlig åtkomst kan du skapa flera principer för användar risk. Olika minimi nivåer för användar risk kan krävas för olika användare och appar. Utifrån användar risk kan du skapa principer för att blockera åtkomst, kräva Multi-Factor Authentication, säkra lösen ords ändringar eller omdirigera till Microsoft Cloud App Security för att framtvinga en replikeringsprincip, till exempel ytterligare granskning.

Användar risk villkoret kräver Azure AD Premium P2 eftersom det använder Azure Identity Protection, som är ett P2-erbjudande. Mer information om villkorlig åtkomst finns i dokumentationen för [villkorlig åtkomst för Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO stöder nu appar som kräver att SPNameQualifier anges vid begäran

**Typ:** Fastsatt  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Vissa SAML-program kräver att SPNameQualifier returneras i intygets ämne när det begärs. Azure AD svarar nu korrekt när en SPNameQualifier begärs i NameID-principen för begäran. Detta fungerar även för SP-initierad inloggning och IdP initierad inloggning kommer att följa.  Mer information om SAML-protokoll i Azure Active Directory finns i [SAML-protokoll för enkel inloggning](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B-samarbete har stöd för att bjuda in MSA och Google-användare i Azure Government-klienter

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 

Azure Government klienter som använder B2B-samarbets funktionerna kan nu bjuda in användare som har ett Microsoft-eller Google-konto. Om du vill ta reda på om din klient kan använda dessa funktioner följer du anvisningarna på [Hur vet jag om B2B-samarbete är tillgängligt i min Azure amerikanska myndighets klient?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Användar objekt i MS Graph v1 innehåller nu externalUserState-och externalUserStateChangedDateTime-egenskaper

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 

Egenskaperna externalUserState och externalUserStateChangedDateTime kan användas för att hitta inbjudna B2B-gäster som inte har accepterat sina inbjudningar än och som skapar automatisering, som att ta bort användare som inte har accepterat sina inbjudningar efter ett visst antal dagar. De här egenskaperna är nu tillgängliga i MS Graph v1. Vägledning om hur du använder dessa egenskaper finns i [användar resurs typ](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Hantera autentiseringsbegäranden i Azure AD villkorlig åtkomst är nu allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Med hanterings funktionerna för autentisering kan du konfigurera hur ofta dina användare måste ange inloggnings uppgifter och om de behöver ange autentiseringsuppgifter efter att de har stängt och öppnat om webbläsare för att få större säkerhet och flexibilitet i din miljö.
 
Dessutom används hantering av autentisering för att endast använda den första Factor Authentication på Azure AD-anslutna, hybrid Azure AD-anslutna och registrerade Azure AD-enheter. Nu gäller hantering av autentiserings-sessioner även för MFA. Mer information finns i [Konfigurera hantering av autentisering med villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – juni 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I juni 2020 har vi lagt till följande 29 nya program i vårt app-galleri med stöd för federation:

[Shopify plus](https://docs.microsoft.com/azure/active-directory/saas-apps/shopify-plus-tutorial), [Ekarda](https://docs.microsoft.com/azure/active-directory/saas-apps/ekarda-tutorial), [grindar](https://docs.microsoft.com/azure/active-directory/saas-apps/mailgates-tutorial), [BullseyeTDP](https://docs.microsoft.com/azure/active-directory/saas-apps/bullseyetdp-tutorial), [Raketa](https://docs.microsoft.com/azure/active-directory/saas-apps/raketa-tutorial), [segment](https://docs.microsoft.com/azure/active-directory/saas-apps/segment-tutorial), [AI revisor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.io](https://docs.microsoft.com/azure/active-directory/saas-apps/proto.io-tutorial), [Gatekeeper](https://www.gatekeeperhq.com/), [hubb Planner](https://docs.microsoft.com/azure/active-directory/saas-apps/hub-planner-tutorial), [Ansira-partner go-to-Marketing-verktygslåda](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation i molnet](https://docs.microsoft.com/azure/active-directory/saas-apps/ibm-digital-business-automation-on-cloud-tutorial), [kisi fysisk säkerhet](https://docs.microsoft.com/azure/active-directory/saas-apps/kisi-physical-security-tutorial), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](https://docs.microsoft.com/azure/active-directory/saas-apps/intelligencebank-tutorial), [pymetrics](https://docs.microsoft.com/azure/active-directory/saas-apps/pymetrics-tutorial), [noll](https://www.teamzero.com/), [instation](https://instation.invillia.com/), [EDX för Business SAML 2,0-integration](https://docs.microsoft.com/azure/active-directory/saas-apps/edx-for-business-saml-integration-tutorial), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](https://docs.microsoft.com/azure/active-directory/saas-apps/smartkargo-tutorial), [PKIsigning-plattform](https://platform.pkisigning.nl/), [SiteIntel](https://docs.microsoft.com/azure/active-directory/saas-apps/siteintel-tutorial), [fält-ID](https://docs.microsoft.com/azure/active-directory/saas-apps/field-id-tutorial) [, kurs utbud](https://smallstep.com/sso-ssh/) [SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/curricula-saml-tutorial), [perforce Helix Core-Helix Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/perforce-helix-core-tutorial) [,](https://cloud.metacompliance.com/)  

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

Mer information om alla upplevelser som är möjliga med API-kopplingar finns i [använda API-kopplingar för att anpassa och utöka](https://docs.microsoft.com/azure/active-directory/b2b/api-connectors-overview)självbetjänings registrering eller [Anpassa extern identitet självbetjänings registrering med Web API-integreringar](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
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
 
En ny delegerad behörighet EntitlementManagement. Read. all är nu tillgänglig för användning med rättighets hanterings-API: et i Microsoft Graph beta. Mer information om tillgängliga API: er finns i [arbeta med API för hantering av Azure AD-hantering](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>API: er för identitets skydd som är tillgängliga i v 1.0

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
RiskyUsers-och riskDetections-Microsoft Graph-API: er är nu allmänt tillgängliga. Nu när de är tillgängliga på v 1.0-slutpunkten, bjuder vi in dig att använda dem i produktionen. Mer information finns i [Microsoft Graph-dokumenten](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Känslighets etiketter för att tillämpa principer på Microsoft 365 grupper är nu allmänt tillgängliga

**Typ:** Ny funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete
 

Nu kan du skapa känslighets etiketter och använda etikett inställningar för att tillämpa principer på Microsoft 365 grupper, inklusive sekretess (offentlig eller privat) och åtkomst princip för externa användare. Du kan skapa en etikett med sekretess policyn som privat och extern användar åtkomst princip för att inte tillåta att gäst användare läggs till. När en användare använder den här etiketten i en grupp, kommer gruppen att vara privat och inga gäst användare får läggas till i gruppen. 

Känslighets etiketter är viktiga för att skydda affärs kritiska data och gör att du kan hantera grupper i skala på ett kompatibelt och säkert sätt. Vägledning om hur du använder känslighets etiketter finns i [tilldela känslighets etiketter till Office 365-grupper i Azure Active Directory (för hands version)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Uppdateringar som stöder Microsoft Identity Manager för Azure AD Premium kunder

**Typ:** Ändrad funktion  
**Tjänste kategori:** Microsoft Identity Manager  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Azure-support är nu tillgängligt för Azure AD integration-komponenter i Microsoft Identity Manager 2016, genom slutet av det utökade stödet för Microsoft Identity Manager 2016. Läs mer på [support uppdatering för Azure AD Premium kunder som använder Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Användningen av grupp medlemskaps villkor i SSO-anspråks konfigurationen höjs

**Typ:** Ändrad funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Tidigare var antalet grupper som du kan använda när du villkorligt ändrar anspråk baserat på grupp medlemskap i en enskild program konfiguration begränsad till 10. Användningen av grupp medlemskaps villkor i SSO-anspråks konfigurationen har nu ökat till högst 50 grupper. Mer information om hur du konfigurerar anspråk finns i konfiguration av [SSO-anspråk för företags program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Aktivera grundläggande formatering på inloggnings sidans text komponent i företags anpassning.

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Företags anpassnings funktionen på Azure AD/Microsoft 365 inloggnings upplevelsen har uppdaterats så att kunden kan lägga till hyperlänkar och enkel formatering, inklusive fetstil, understrykning och kursiv stil. Vägledning om hur du använder den här funktionen finns i [lägga till anpassning till din organisations Azure Active Directory inloggnings sida](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="provisioning-performance-improvements"></a>Prestanda förbättringar för etablering

**Typ:** Ändrad funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Etablerings tjänsten har uppdaterats för att minska tiden då en [stegvis cykel](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles) slutförs. Det innebär att användare och grupper kommer att tillhandahållas i sina program snabbare än tidigare. Alla nya etablerings jobb som skapats efter 6/10/2020 kommer automatiskt att dra nytta av prestanda förbättringarna. Alla program som kon figurer ATS för etablering före 6/10/2020 måste starta om en gång efter 6/10/2020 för att dra nytta av prestanda förbättringarna. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Att presentera utfasningen av ADAL-och MS Graph-paritet

**Typ:** Föråldrad  
**Tjänste kategori:** EJ TILLÄMPLIGT  
**Produkt kapacitet:** Hantering av enhetens livs cykel

Nu när Microsoft Authentication libraries (MSAL) är tillgängligt kommer vi inte längre att lägga till nya funktioner i Azure Active Directory Authentication libraries (ADAL) och säkerhets korrigeringarna upphör den 30 juni 2022. Mer information om hur du migrerar till MSAL finns i [migrera program till Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-migration).

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
 

För närvarande kan användare som finns i domäner federerade i Azure AD, men som inte har synkroniserats till klienten, inte komma åt team. Med början i slutet av juni gör den här nya funktionen att de kan göra det genom att utöka den befintliga e-postverifierade inloggnings funktionen. Detta gör det möjligt för användare som kan logga in till en federerad IdP, men som ännu inte har ett användar objekt i Azure-ID, för att skapa ett användar objekt automatiskt och autentiseras för team. Deras användar objekt kommer att markeras som "självbetjänings registrering". Det här är en utökning av den befintliga funktionen för att verifiera e-postverifiering som användare i hanterade domäner kan göra och kan kontrol leras med hjälp av samma flagga. Den här ändringen kommer att slutföras under följande två månader. Titta efter dokumentations uppdateringar [här](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Kommande korrigering: OIDC identifierings dokument för Azure Government molnet uppdateras för att referera till rätt graf-slutpunkter.

**Typ:** Planera för ändring  
**Tjänste kategori:** Suveräna moln  
**Produkt kapacitet:** Användarautentisering
 
Från och med juni börjar OIDC identifierings dokument [Microsoft Identity Platform och OpenID Connect-protokollet](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) på [Azure Government moln](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) slut punkten (login.microsoftonline.us) att returnera rätt [nationell moln](https://docs.microsoft.com/graph/deployments) slut punkt ( https://graph.microsoft.us eller https://dod-graph.microsoft.us) , baserat på den klient som tillhandahölls).  Den ger för närvarande den felaktiga graph.microsoft.com msgraph_host-fältet (graf endpoint).  

Den här fel korrigeringen distribueras gradvis över cirka 2 månader.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government användare kommer inte längre att kunna logga in på login.microsoftonline.com

**Typ:** Planera för ändring  
**Tjänste kategori:** Suveräna moln  
**Produkt kapacitet:** Användarautentisering
 
Den 1 juni 2018, den officiella Azure Active Directory (AAD) som Azure Government ändrats från https://login-us.microsoftonline.com till https://login.microsoftonline.us . Om du äger ett program i en Azure Government-klient måste du uppdatera ditt program för att kunna logga in användare i en. us-slutpunkt.

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
 
IT-administratörer kan börja använda den nya rollen "hybrid administratör" som den minst privilegierade rollen för att konfigurera Azure ADConnect Cloud-etablering. Med den nya rollen behöver du inte längre använda den globala administratörs rollen för att konfigurera och konfigurera moln etablering. [Läs mer](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-by-task#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Nya federerade appar som är tillgängliga i Azure AD Application Gallery – maj 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I maj 2020 har vi lagt till följande 36 nya program i vårt app-galleri med stöd för federation:

[Moula](https://moula.com.au/pay/merchants), [surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-for-digital-workplace/), [TackleBox](http://www.tacklebox.app/)engagerande, [Powell-team](https://powell-software.com/en/powell-teams-en/), [TalentSoft Assistant](https://msteams.talent-soft.com/), [ASC Recording Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-engagerad](https://b-engaged.se/), [Competella kontakt Center Workgroup](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft-identitet](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [insvit](https://docs.microsoft.com/azure/active-directory/saas-apps/insuite-tutorial), [ändrings process hantering](https://docs.microsoft.com/azure/active-directory/saas-apps/change-process-management-tutorial), [cyara CX Assurance-plattform](https://docs.microsoft.com/azure/active-directory/saas-apps/cyara-cx-assurance-platform-tutorial), [Smart global styrning](https://docs.microsoft.com/azure/active-directory/saas-apps/smart-global-governance-tutorial), [Prezi](https://docs.microsoft.com/azure/active-directory/saas-apps/prezi-tutorial), [Mapbox](https://docs.microsoft.com/azure/active-directory/saas-apps/mapbox-tutorial), [Datava Enterprise Service Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/datava-enterprise-service-platform-tutorial), [WHimsical](https://docs.microsoft.com/azure/active-directory/saas-apps/whimsical-tutorial) [, Trelica,](https://docs.microsoft.com/azure/active-directory/saas-apps/trelica-tutorial) [EasySSO for Confluence](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-confluence-tutorial), [EasySSO för BitBucket](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bitbucket-tutorial), EasySSO [för Bamboo](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bamboo-tutorial), [Torii](https://docs.microsoft.com/azure/active-directory/saas-apps/torii-tutorial), [Axiad](https://docs.microsoft.com/azure/active-directory/saas-apps/axiad-cloud-tutorial) [, ColorTokens](https://docs.microsoft.com/azure/active-directory/saas-apps/humanage-tutorial) [, ZTNA](https://docs.microsoft.com/azure/active-directory/saas-apps/anyone-home-crm-tutorial) [CCH](https://docs.microsoft.com/azure/active-directory/saas-apps/colortokens-ztna-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/ice-contact-center-tutorial) Tagetik [, ShareVault](https://docs.microsoft.com/azure/active-directory/saas-apps/sharevault-tutorial) [, Vyond](https://docs.microsoft.com/azure/active-directory/saas-apps/textexpander-tutorial) [, TextExpander,](https://docs.microsoft.com/azure/active-directory/saas-apps/vyond-tutorial) [askSpoke,](https://docs.microsoft.com/azure/active-directory/saas-apps/askspoke-tutorial) [CCH Tagetik](https://docs.microsoft.com/azure/active-directory/saas-apps/cch-tagetik-tutorial)

Du kan också hitta dokumentationen för alla program härifrån https://aka.ms/AppsTutorial .

För att lista ditt program i Azure AD App-galleriet läser du informationen här https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Endast rapport läge för villkorlig åtkomst är nu allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet

Med [endast rapport läge för villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) kan du utvärdera resultatet av en princip utan att tvinga åtkomst kontroller. Du kan testa endast rapport principer i organisationen och förstå deras inverkan innan du aktiverar dem, vilket gör distributionen säkrare och enklare. Under de senaste månaderna har vi sett ett starkt införande av enbart rapport läge – över 26M-användare är redan en omfattning av en rapport princip. Med meddelandet idag skapas nya Azure AD-principer för villkorlig åtkomst i endast rapport läge som standard. Det innebär att du kan övervaka påverkan av dina principer från den tidpunkt då de skapas. Och för de som använder MS Graph API: er kan du [hantera endast rapport principer program mässigt](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Registrera dig för gäst användare med självbetjäning

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 
Med externa identiteter i Azure AD kan du tillåta att personer utanför organisationen kan komma åt dina appar och resurser samtidigt som de kan logga in med den identitet de föredrar. När du delar ett program med externa användare är det inte säkert att du alltid vet i förväg vilka som behöver åtkomst till programmet. Med [självbetjänings registrering](https://docs.microsoft.com/azure/active-directory/b2b/self-service-sign-up-overview)kan du göra det möjligt för gäst användare att registrera sig och få ett gäst konto för dina branschspecifika appar (LOB). Registrerings flödet kan skapas och anpassas för att ge stöd för Azure AD och sociala identiteter. Du kan också samla in ytterligare information om användaren under registreringen.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Arbets boken för villkorlig åtkomst insikter och rapportering är allmänt tillgänglig

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet

[Arbets boken insikter och rapportering](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) ger administratörer en översikt över villkorlig åtkomst för Azure AD i sin klient organisation. Med möjligheten att välja en enskild princip kan administratörer bättre förstå vad varje princip gör och övervaka ändringar i real tid. Arbets boken strömma data som lagras i Azure Monitor, som du kan konfigurera på några minuter [efter dessa instruktioner](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). För att instrument panelen ska bli mer synlig har vi flyttat den till fliken nya insikter och rapporter på menyn för villkorlig åtkomst i Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Bladet princip information för villkorlig åtkomst finns i offentlig för hands version

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & skydd för identitets säkerhet

I [bladet ny princip information](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) visas de tilldelningar, villkor och kontroller som är uppfyllda under utvärderingen av principen för villkorlig åtkomst. Du kan komma åt bladet genom att välja en rad i flikarna villkorlig åtkomst eller endast rapporter i inloggnings informationen.

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
 
Nu finns stöd för att skapa och konfigurera ett program från Azure AD-galleriet med MS Graph API: er i Beta versionen. Om du behöver konfigurera SAML-baserad enkel inloggning för flera instanser av ett program sparar du tid genom att använda Microsoft Graph API: er för att [Automatisera konfigurationen av SAML-baserad enkel inloggning](https://docs.microsoft.com/azure/active-directory/manage-apps/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nya etablerings anslutningar i Azure AD-programgalleriet – maj 2020

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** integration från tredje part
 
Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

* [8 x 8](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8-provisioning-tutorial)
* [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-provisioning-tutorial)
* [MediusFlow](https://docs.microsoft.com/azure/active-directory/saas-apps/mediusflow-provisioning-tutorial)
* [New Relic efter organisation](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-by-organization-provisioning-tutorial)
* [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="saml-token-encryption-is-generally-available"></a>Kryptering av SAML-token är allmänt tillgängligt

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Med [SAML token Encryption](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption) kan program konfigureras för att ta emot krypterade SAML-kontroller. Funktionen är nu allmänt tillgänglig i alla moln.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Grupp namns anspråk i Application-token är allmänt tillgängliga

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION
 
Grupp anspråk som utfärdats i en token kan nu begränsas till bara de grupper som tilldelats programmet.  Detta är särskilt viktigt när användare är medlemmar i ett stort antal grupper och det uppstod en risk för att överskrida storleks gränser för token. När den här nya funktionen är på plats är möjligheten att [lägga till grupp namn i tokens](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) allmänt tillgänglig.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Tillbakaskrivning av workday har nu stöd för att ange attribut för telefonnummer till arbetet

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Vi har förbättrat programetablerings appen för Workday-installation så att den nu stöder tillbakaskrivning av arbetstelefonnummer och attribut för mobil nummer. Förutom e-post och användar namn kan du nu konfigurera appen för tillbakaskrivning av arbets dagar för att flöda telefonnummer värden från Azure AD till Workday. Mer information om hur du konfigurerar tillbakaskrivning av telefonnummer finns i själv studie kursen om [tillbakaskrivning av workday](https://aka.ms/WorkdayWriteback) -appar. 

---

### <a name="publisher-verification-preview"></a>Utgivarens verifiering (för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Utvecklings miljö
 
Med utgivar verifieringen (för hands version) kan administratörer och slutanvändare förstå äktheten hos programutvecklare som integreras med Microsoft Identity Platform. Mer information finns i [utgivar verifiering (för hands version)](https://docs.microsoft.com/azure/active-directory/develop/publisher-verification-overview).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Authorization Code Flow för appar på en sida

**Typ:** Ändrad funktions **tjänst kategori:** autentisering **produkt kapacitet:** utvecklings miljö

På grund av moderna webb läsar [begränsningar från tredje part, till exempel Safari ITP](https://docs.microsoft.com/azure/active-directory/develop/reference-third-party-cookies-spas), måste SPAs använda kod flödet i stället för det implicita flödet för att upprätthålla SSO. MSAL.js v 2. x stöder nu Authorization Code Flow. Det finns motsvarande uppdateringar av Azure Portal så att du kan uppdatera din SPA och skriva "Spa" och använda kod flödet för autentisering. Anvisningar finns i [snabb start: Logga in användare och hämta en åtkomsttoken i ett Java Script spa med hjälp av kod flödet för autentisering](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Förbättrad filtrering för enheter finns i offentlig för hands version

**Typ:** Ändrad funktion   
**Tjänste kategori:** **Produkt kapacitet** för enhets hantering: hantering av enhetens livs cykel
 
Tidigare var de enda filter som du kan använda "aktiverade" och "aktivitets datum". Nu kan du [filtrera listan över enheter på fler egenskaper](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal#device-list-filtering-preview), inklusive OS-typ, kopplings typ, efterlevnad med mera. De här tilläggen bör förenkla lokalisering av en viss enhet.

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

Mer information finns [i den nya appens registrerings upplevelse för Azure AD B2C](https://aka.ms/b2cappregtraining).

---

## <a name="april-2020"></a>April 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Den kombinerade registreringen av säkerhets information är nu allmänt tillgänglig

**Typ:** Ny funktion

**Tjänste kategori:** Autentiseringar (inloggningar)

**Produkt kapacitet:** & skydd för identitets säkerhet

Den kombinerade registrerings upplevelsen för Multi-Factor Authentication (MFA) och lösen ords återställning via självbetjäning (SSPR) är nu allmänt tillgänglig. Den här nya registreringen gör det möjligt för användare att registrera sig för MFA-och SSPR i en enda steg-för-steg-process. När du distribuerar den nya upplevelsen för din organisation kan användarna registrera sig på kortare tid och med färre krångel. Kolla in blogg inlägget [här](https://bit.ly/3etiRyQ).

---

### <a name="continuous-access-evaluation"></a>Utvärdering av kontinuerlig åtkomst

**Typ:** Ny funktion

**Tjänste kategori:** Autentiseringar (inloggningar)

**Produkt kapacitet:** & skydd för identitets säkerhet

Utvärdering av kontinuerlig åtkomst är en ny säkerhetsfunktion som gör det möjligt att tillämpa principer på förlitande parter i nära real tid när händelser inträffar i Azure AD (t. ex. borttagning av användar konto). Vi rullar ut den här funktionen först för team-och Outlook-klienter. Läs vår [blogg](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) och [dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation)om du vill ha mer information.

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS-inloggning: firstline-anställda kan logga in till Azure AD-program med sina telefonnummer och inget lösen ord

**Typ:** Ny funktion

**Tjänste kategori:** Autentiseringar (inloggningar)

**Produkt kapacitet:** Användarautentisering

Office lanserar en serie mobila appar som går till icke-traditionella organisationer och anställda i stora organisationer som inte använder e-post som primär kommunikations metod. De här apparna riktar Frontline anställda, Deskless-arbetskrafter, fält agenter eller detalj handels anställda som inte får en e-postadress från sin arbetsgivare, har åtkomst till en dator eller till den. Med det här projektet kan de anställda logga in på företags program genom att ange ett telefonnummer och roundtripping en kod. Mer information finns i vår dokumentation om [administratör](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin) [och slutanvändare](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Bjud in interna användare att använda B2B-samarbete

**Typ:** Ny funktion

**Tjänste kategori:** Business

**Produkt kapacitet:**

Vi utökar B2B-Inbjudnings funktionen så att befintliga interna konton kan bjudas in att använda B2B-samarbets uppgifter som skickas framåt. Detta görs genom att skicka användarobjektet till inbjudans-API: t Förutom vanliga parametrar som den inbjudna e-postadressen. Användarens objekt-ID, UPN, grupp medlemskap, app-tilldelning etc. förblir intakt, men fortsätter att använda B2B för att autentisera med sina autentiseringsuppgifter för hem klienten i stället för de interna autentiseringsuppgifter som de använde före inbjudan. Mer information finns i [dokumentationen](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Endast rapport läge för villkorlig åtkomst är nu allmänt tillgänglig

**Typ:** Ny funktion

**Tjänste kategori:** Villkorlig åtkomst

**Produkt kapacitet:** & skydd för identitets säkerhet

Med [endast rapport läge för villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) kan du utvärdera resultatet av en princip utan att tvinga åtkomst kontroller. Du kan testa endast rapport principer i organisationen och förstå deras inverkan innan du aktiverar dem, vilket gör distributionen säkrare och enklare. Under de senaste månaderna har vi sett ett starkt införande av enbart rapport läge, med över 26M-användare som redan är i omfattning av en rapport princip. Med det här meddelandet skapas nya Azure AD-principer för villkorlig åtkomst i endast rapport läge som standard. Det innebär att du kan övervaka påverkan av dina principer från den tidpunkt då de skapas. Och för de som använder MS Graph API: er kan du även [Hantera rapport principer program mässigt](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Arbets boken för villkorlig åtkomst insikter och rapportering är allmänt tillgänglig

**Typ:** Ny funktion

**Tjänste kategori:** Villkorlig åtkomst

**Produkt kapacitet:** & skydd för identitets säkerhet

Arbets boken för villkorlig åtkomst [insikter och rapportering](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) ger administratörer en översikt över villkorlig åtkomst för Azure AD i klienten. Med möjligheten att välja en enskild princip kan administratörer bättre förstå vad varje princip gör och övervaka ändringar i real tid. Arbets boken strömma data som lagras i Azure Monitor, som du kan konfigurera på några minuter [efter dessa instruktioner](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). För att instrument panelen ska bli mer synlig har vi flyttat den till fliken nya insikter och rapporter på menyn för villkorlig åtkomst i Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Bladet princip information för villkorlig åtkomst finns i offentlig för hands version

**Typ:** Ny funktion

**Tjänste kategori:** Villkorlig åtkomst

**Produkt kapacitet:** & skydd för identitets säkerhet

I [bladet ny princip information](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) visas vilka tilldelningar, villkor och kontroller som var uppfyllda under utvärderingen av principen för villkorlig åtkomst. Du kan komma åt bladet genom att välja en rad i flikarna **villkorlig åtkomst** eller **endast rapporter** i inloggnings informationen.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nya federerade appar är tillgängliga i Azure AD App Galleri – april 2020

**Typ:** Ny funktion

**Tjänste kategori:** Företags program

**Produkt kapacitet:** integration från tredje part

I april 2020 har vi lagt till dessa 31 nya appar med stöd för federation i app-galleriet: 

[SincroPool Apps](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial), [LMS365](https://lms.365.systems/), [IWT anskaffning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial), [Lunni](https://lunni.fi/), [EasySSO för JIRA](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial), [Office 365-arbetskrafter](https://app.mover.io/login), [talare engagera](https://speakerengage.com/login.php), [belys](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial), Ally, DutyFlow, AlertMedia, gr8, Pendo [, Highground](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial), Timetabling, SynchroNet, Fortes [empower](https://www.made-in-office.com/en/), Litmus, [DutyFlow](https://app.dutyflow.nl/) [GroupTalk](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial), Frontify, MongoDB, TickitLMS, Coco [, Nitro,](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial)TMWS, [gr8 People](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial) [GroupTalk](https://recorder.grouptalk.com/), [AlertMedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial) [Timetabling Solutions](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial) [Frontify](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial),, [TickitLMS Learn](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial), [,](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial) [Pendo](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial), [Trend Micro Web Security(TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial) [Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial) [SynchroNet CLICK](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial) [Nitro Productivity Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Stöd för Microsoft Graph delta frågor för oAuth2PermissionGrant tillgängligt för offentlig för hands version

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings miljö

Delta fråga för oAuth2PermissionGrant är tillgänglig för offentlig för hands version! Nu kan du spåra ändringar utan att behöva söka Microsoft Graph. [Läs mer.](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Stöd för Microsoft Graph delta frågor för organisations kontakt är allmänt tillgänglig

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings miljö

Delta frågor för organisatoriska kontakter är allmänt tillgängliga! Nu kan du spåra ändringar i produktions program utan att behöva avsöka Microsoft Graph kontinuerligt. Ersätt eventuell befintlig kod som kontinuerligt avsöker orgContact data efter delta-fråga för att förbättra prestanda avsevärt. [Läs mer.](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Stöd för Microsoft Graph delta frågor för programmet är allmänt tillgängligt

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings miljö

Delta fråga för program är allmänt tillgänglig! Nu kan du spåra ändringar i produktions program utan att behöva avsöka Microsoft Graph kontinuerligt. Ersätt eventuell befintlig kod som kontinuerligt avsöker program data efter delta-fråga för att förbättra prestanda avsevärt. [Läs mer.](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Stöd för Microsoft Graph delta frågor för administrativa enheter som är tillgängliga för offentlig för hands version

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings upplevelse delta fråga för administrativa enheter är tillgänglig för offentlig för hands version! Nu kan du spåra ändringar utan att behöva söka Microsoft Graph. [Läs mer.](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Hantera telefonnummer för autentisering och mer i nya Microsoft Graph beta-API: er

**Typ:** Ny funktion

**Tjänste kategori:** MS Graph

**Produkt kapacitet:** Utvecklings miljö

Dessa API: er är ett nyckel verktyg för att hantera dina användares autentiseringsmetoder. Nu kan du programmatiskt för att registrera och hantera de autentiserare som används för MFA och lösen ords återställning via självbetjäning (SSPR). Detta har varit en av de mest efterfrågade funktionerna i Azure MFA, SSPR och Microsoft Graph Spaces. De nya API: er som vi har lanserat i den här vågen ger dig möjlighet att:

- Läsa, lägga till, uppdatera och ta bort en användares telefoner för autentisering
- Återställa en användares lösen ord
- Aktivera och inaktivera SMS-inloggning

Mer information finns i [Översikt över Azure AD Authentication Methods API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Den offentliga för hands versionen av administrativa enheter

**Typ:** Ny funktion

**Tjänste kategori:** RBAC

**Produkt kapacitet:** Access Control

Med administrativa enheter kan du bevilja administratörs behörigheter som är begränsade till en avdelning, region eller något annat segment i din organisation som du definierar. Du kan använda administrativa enheter för att delegera behörigheter till regionala administratörer eller ange en princip på en detaljerad nivå. En användar konto administratör kan till exempel uppdatera profil information, återställa lösen ord och tilldela licenser enbart för användare i sin administrativa enhet.

Med hjälp av administrativa enheter kan en central administratör:

- Skapa en administrativ enhet för decentraliserad hantering av resurser
- Tilldela en roll med administrativ behörighet enbart över Azure AD-användare i en administrativ enhet
- Fyll i de administrativa enheterna med användare och grupper efter behov

Mer information finns i [hantering av administrativa enheter i Azure Active Directory (för hands version)](https://aka.ms/AdminUnitsDocs).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Inbyggda roller för skrivar administratör och skrivar tekniker

**Typ:** Ny funktion

**Tjänste kategori:** RBAC

**Produkt kapacitet:** Access Control

**Skrivar administratör**: användare med den här rollen kan registrera skrivare och hantera alla aspekter av alla skrivar konfigurationer i Microsofts Universal Print-lösning, inklusive inställningar för Universal Print Connector. De kan godkänna alla delegerade utskrifts behörighets begär Anden. Skrivar administratörer har även åtkomst till utskrifts rapporter. 

**Skrivar tekniker**: användare med den här rollen kan registrera skrivare och hantera skrivar status i Microsoft Universal Print-lösningen. De kan också läsa all anslutnings information. Viktiga aktiviteter en skrivare tekniker kan inte ange användar behörigheter för skrivare och dela skrivare. [Läs mer.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Inbyggd rollen hybrid identitets administratör

**Typ:** Ny funktion

**Tjänste kategori:** RBAC

**Produkt kapacitet:** Access Control

Användare med den här rollen kan aktivera, konfigurera och hantera tjänster och inställningar för att aktivera hybrid identitet i Azure AD. Den här rollen ger möjlighet att konfigurera Azure AD till någon av de tre autentiseringsmetoder som stöds&#8212;(PHS), direktautentisering (PTA) eller Federation (AD FS eller tredjepartsleverantörer) &#8212;och för att distribuera relaterad lokal infrastruktur för att aktivera dem. Lokal infrastruktur innehåller etablerings-och PTA-agenter. Den här rollen ger möjlighet att aktivera sömlös enkel inloggning (S-SSO) för att möjliggöra sömlös autentisering på icke-Windows 10-enheter eller datorer som inte är Windows Server 2016. Dessutom ger den här rollen möjlighet att se inloggnings loggar och för att få åtkomst till hälso-och analys funktioner för övervakning och fel sökning. [Läs mer.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Inbyggd nätverks administratörs roll

**Typ:** Ny funktion

**Tjänste kategori:** RBAC

**Produkt kapacitet:** Access Control

Användare med den här rollen kan granska rekommendationer för nätverks perimeter arkitektur från Microsoft som baseras på telemetri från användarens platser. Nätverks prestanda för Office 365 förlitar sig på noggrann nätverks perimeter arkitektur i företags nätverk, vilket vanligt vis är specifika för användare. Med den här rollen kan du redigera identifierade användar platser och konfigurera nätverks parametrar för de platserna för att under lätta förbättrad telemetri och bättre design rekommendationer. [Läs mer.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Mass aktivitet och nedladdningar i Azure AD Admin Portal-upplevelsen

**Typ:** Ny funktion

**Tjänste kategori:** Användar hantering

**Produkt kapacitet:** Katalogen

Nu kan du utföra Mass aktiviteter för användare och grupper i Azure AD genom att ladda upp en CSV-fil i Azure AD Admin Portal-upplevelsen. Du kan skapa användare, ta bort användare och bjuda in gäst användare. Och du kan lägga till och ta bort medlemmar från en grupp.

Du kan också hämta listor över Azure AD-resurser från Azure AD Admin Portal-upplevelsen. Du kan hämta listan över användare i katalogen, listan över grupper i katalogen och medlemmarna i en viss grupp.

Om du vill ha mer information kan du läsa följande:

- [Skapa användare](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add) eller [Bjud in gäst användare](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [Ta bort användare](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete) eller [Återställ borttagna användare](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [Hämta lista över användare](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download) eller [Hämta lista över grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- [Lägg till (importera) medlemmar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members) eller [ta bort medlemmar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) eller [Hämta en lista över medlemmar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members) för en grupp

---

### <a name="my-staff-delegated-user-management"></a>Min personal har delegerat användar hantering

**Typ:** Ny funktion

**Tjänste kategori:** Användar hantering

**Produkt kapacitet:**

Min personal ger firstline-ansvariga, till exempel en butiks chef, för att säkerställa att deras personal medlemmar kan komma åt sina Azure AD-konton. I stället för att förlita dig på en central helpdesk kan organisationer delegera vanliga uppgifter, till exempel att återställa lösen ord eller ändra telefonnummer till en firstline Manager. Med min personal kan en användare som inte har åtkomst till sitt konto återfå åtkomst på bara några klick, utan supportavdelningen eller IT-personal som krävs. Mer information finns i [Hantera dina användare med min personal (för hands version)](https://aka.ms/MyStaffAdminDocs) och [delegera användar hantering med min personal (för hands version)](https://aka.ms/MyStaffUserDocs).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>En uppgraderad slut användar upplevelse i åtkomst granskningar

**Typ:** Ändrad funktion

**Tjänste kategori:** Åtkomst granskningar

**Produkt kapacitet:** Identitets styrning

Vi har uppdaterat gransknings upplevelsen för åtkomst granskningar av Azure AD i portalen Mina appar. I slutet av april visas en banderoll som gör det möjligt för dina granskare som är inloggade i Azure AD Access granskare att se en banderoll som gör det möjligt för dem att testa den uppdaterade upplevelsen i min åtkomst. Observera att de uppdaterade åtkomst granskningarna har samma funktioner som den aktuella upplevelsen, men med ett förbättrat användar gränssnitt ovanpå nya funktioner som gör att användarna kan vara produktiva. [Du kan lära dig mer om den uppdaterade upplevelsen här](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review). Den här allmänna för hands versionen kommer att gälla till slutet av juli 2020. I slutet av juli kommer granskare som inte har valt förhands gransknings upplevelsen automatiskt att dirigeras till min åtkomst för att utföra åtkomst granskningar. Om du vill att granskarna ska växlas över till för hands versionen i min åtkomst nu, gör du [en begäran här](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Arbets dag för inkommande användar etablering och tillbakaskrivning av appar stöder nu de senaste versionerna av webb tjänstens API för arbets dagar

**Typ:** Ändrad funktion

**Tjänste kategori:** App-etablering

**Produkt kapacitet:** 

Baserat på kundfeedback har vi nu uppdaterat inetablerings-och tillbakaskrivning av workday-appar i Enterprise App-galleriet för att stödja de senaste versionerna av WWS-API: et för webb tjänster (Workday). Med den här ändringen kan kunderna ange den WWS-API-version som de vill använda i anslutnings strängen. Detta ger kunderna möjlighet att hämta fler HR-attribut som är tillgängliga i alla versioner av arbets dagen. Tillbakaskrivning-appen för Workday använder nu den rekommenderade Change_Work_Contact_Info Workday-webbtjänsten för att lösa Maintain_Contact_Infos begränsningar.

Om ingen version anges i anslutnings strängen fortsätter inkommande Provisioning-appar som standard att använda WWS v-21.1 för att växla till de senaste Workday-API: erna för inkommande användar etablering, kunder måste uppdatera anslutnings strängen som dokumenteras [i självstudien](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) och även uppdatera de XPath-attribut som används för Workday-attribut enligt beskrivningen i [referens hand boken för Workday-attributet](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30). 

Om du vill använda det nya API: t för tillbakaskrivning behöver du inte göra några ändringar i programetablerings appen för Workday-tillbakaskrivning. På sidan arbets dag ser du till att användar kontot för ISU-kontot (Workday) har behörighet att anropa Change_Work_Contact affärs process som dokumenteras i avsnittet självstudie, [Konfigurera säkerhets princip för affärs processer](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions). 

Vi har uppdaterat vår [själv studie guide](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) för att återspegla den nya stöd för API-versionen.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Användare med rollen standard åtkomst är nu inom omfånget för etablering

**Typ:** Ändrad funktion

**Tjänste kategori:** App-etablering

**Produkt kapacitet:** Hantering av identitets livs cykel

Tidigare har användare med standard åtkomst rollen utanför definitions området för etablering. Vi har hört feedback om att kunderna vill att användare med den här rollen ska omfattas av etableringen. Från och med 16 april 2020 låter alla nya etablerings konfigurations användare med standard åtkomst rollen tillhandahållas. Gradvis kommer vi att ändra beteendet för befintliga etablerings konfigurationer som stöder etablering av användare med den här rollen. [Läs mer.](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>Uppdaterat etablerings gränssnitt

**Typ:** Ändrad funktion

**Tjänste kategori:** App-etablering

**Produkt kapacitet:** Hantering av identitets livs cykel

Vi har uppdaterat vår etablerings upplevelse för att skapa en mer fokuserad hanterings vy. När du navigerar till etablerings bladet för ett företags program som redan har kon figurer ATS kan du enkelt övervaka förloppet för etablering och hantering av åtgärder som att starta, stoppa och starta om etableringen. [Läs mer.](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Verifiering av dynamisk grupp regel är nu tillgängligt för offentlig för hands version

**Typ:** Ändrad funktion

**Tjänste kategori:** Grupp hantering

**Produkt kapacitet:** Samarbete

Azure Active Directory (Azure AD) ger nu möjlighet att verifiera dynamiska grupp regler. På fliken **validera regler** kan du verifiera din dynamiska regel mot exempel grupp medlemmar för att bekräfta att regeln fungerar som förväntat. När du skapar eller uppdaterar dynamiska grupp regler vill administratörer veta om en användare eller enhet kommer att vara medlem i gruppen. Detta hjälper till att utvärdera om en användare eller enhet uppfyller regel villkoren och hjälper till med fel sökning när medlemskap inte förväntas.

Mer information finns i [Validera en regel för dynamisk grupp medlemskap (för hands version)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Identifiera säkra Poäng – säkerhets inställningar och uppdatering av MFA-förbättringar

**Typ:** Ändrad funktion

**Tjänste kategori:** EJ TILLÄMPLIGT

**Produkt kapacitet:** & skydd för identitets säkerhet

**Stöd för säkerhets inställningar för Azure AD-förbättringar:** Microsofts säkra poäng kommer att uppdatera förbättrings åtgärder för att ge stöd för [säkerhets inställningar i Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), vilket gör det enklare att skydda din organisation med förkonfigurerade säkerhets inställningar för vanliga attacker. Detta kommer att påverka följande förbättrings åtgärder:

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

Dessa nya förbättringar kräver att du registrerar dina användare eller administratörer för Multi-Factor Authentication (MFA) i din katalog och upprättar rätt uppsättning principer som passar organisationens behov. Huvud målet är att ha flexibilitet och samtidigt se till att alla användare och administratörer kan autentisera med flera faktorer eller riskfyllda identitets verifierings meddelanden. Det kan vara form av att ha flera principer som tillämpar definitions områden, eller ställa in säkerhets inställningar (från och med den 16 mars) som gör att Microsoft bestämmer sig för att utmana användare för MFA. [Läs mer om vad som är nytt i Microsofts säkra Poäng](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## <a name="march-2020"></a>Mars 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Ohanterade Azure Active Directory konton i B2B-uppdatering för mars 2021

**Typ:** Planera för ändring  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 
**Från och med den 31 mars 2021**kommer Microsoft inte längre att stödja inlösen av inbjudningar genom att skapa ohanterade Azure Active Directory-konton (Azure AD) och klienter för B2B-samarbets scenarier. Vi rekommenderar att du väljer att [e-posta autentisering med eng ång slö sen ord](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode).

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Användare med standard åtkomst rollen är inom omfånget för etablering

**Typ:** Planera för ändring  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Tidigare har användare med standard åtkomst rollen utanför definitions området för etablering. Vi har hört feedback om att kunderna vill att användare med den här rollen ska omfattas av etableringen. Vi arbetar med att distribuera en ändring så att alla nya etablerings konfigurationer tillåter användare med standard åtkomst rollen som ska tillhandahållas. Gradvis kommer vi att ändra beteendet för befintliga etablerings konfigurationer som stöder etablering av användare med den här rollen. Ingen kund åtgärd krävs. Vi publicerar en uppdatering av vår [dokumentation](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) när den här ändringen är på plats.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B-samarbete är tillgängligt i Microsoft Azure som drivs av 21Vianet-klienter (Azure Kina 21Vianet)

**Typ:** Planera för ändring  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 
Funktionerna i Azure AD B2B-samarbete kommer att göras tillgängliga i Microsoft Azure som drivs av 21Vianet (Azure Kina 21Vianet), vilket gör det möjligt för användare i en Azure Kina 21Vianet-klient att samar beta sömlöst med användare i andra Azure Kina 21Vianet-klienter. [Lär dig mer om Azure AD B2B-samarbete](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Inbjudan till Azure AD B2B-samarbete e-postdesign

**Typ:** Planera för ändring  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 
[E-postmeddelanden](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) som skickas av Azure AD B2B Collaboration-Inbjudnings tjänsten för att bjuda in användare till katalogen, kommer att omutformas för att göra Inbjudnings informationen och användarens nästa steg tydligare.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>HomeRealmDiscovery princip ändringar visas i gransknings loggarna

**Typ:** Fastsatt  
**Tjänste kategori:** Händelse  
**Produkt kapacitet:** Övervaka & rapportering
 
Vi har åtgärdat ett fel där ändringar i [HomeRealmDiscovery-principen](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) inte inkluderades i gransknings loggarna. Du kommer nu att kunna se när och hur principen ändrades, och av vem. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – mars 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I mars 2020 har vi lagt till dessa 51 nya appar med stöd för federation i app-galleriet: 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho, en Kina](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [profit.co SAML-App](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial), [iPoint-tjänsteleverantör](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), [Contexxt.AI-sfär](https://contexxt-sphere.com/login), [ingengörskunskap av Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), [överstrålning, digitalt signerat](https://spark-dev.pixelnebula.com/login), [Logz.io – molnets användarvänlighet för ingenjörer](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial), [SPECTRUMU](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial), [BizzContact](https://bizzcontact.app/), [Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial), [MarketSignShare](http://www.signshare.com/), CrossKnowledge [Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [Netvision kompositioner](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial), [FCM Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial), [revben A/S Byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757), [GoLinks](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial), [Datadog](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial), [Zscaler B2B User Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial), [Aster](https://demo.asterapp.io/login) [hiss](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial), [Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial) [, WatchTeams och Aster](https://www.devfinition.com/), [kompetens arbets flöde](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial) [,](https://admin.nodeinsight.com/AADLogin.aspx) [IP-plattform](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [insikt](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [PipeDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial), [Showcase-workshop](https://app.showcaseworkshop.com/), [GreenLight integration Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial), [GreenLight-kompatibel åtkomst hantering](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [grok Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Miradore online](https://login.online.miradore.com/), [Khoros Care](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial) [, AskYourTeam, TruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial) [, Smartwaiver](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial), [Bizagi Studio för digital process Automation](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [insuiteX](https://www.insuite.jp/) [, Sybo](https://www.systexsoftware.com.tw/) [, Britive](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial) [WhosOffice,](https://www.smartwaiver.com/m/user/sw_login.php?wms_login) [E-dagar](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial), [Kollective SDN](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [PlayVox](https://my.playvox.com/login), [korn fartyg 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [Campus kafé](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial) [, Catchpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial) [, Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B-samarbete tillgängligt i Azure Government-klienter

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** B2B/B2C
 
Samarbets funktionerna i Azure AD B2B är nu tillgängliga mellan vissa Azure Government klienter.  Om du vill ta reda på om din klient kan använda de här funktionerna följer du anvisningarna på [Hur vet jag om B2B-samarbete är tillgängligt i min Azure amerikanska myndighets klient?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor integration för Azure-loggar är nu tillgänglig i Azure Government

**Typ:** Ny funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering
 
Azure Monitor integrering med Azure AD-loggar finns nu i Azure Government. Du kan skicka Azure AD-loggar (gransknings-och inloggnings loggar) till ett lagrings konto, Event Hub och Log Analytics. Läs igenom den [detaljerade dokumentationen](https://aka.ms/aadlogsinamd) [och distributions planer för rapportering och övervakning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) av Azure AD-scenarier.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Uppdatera identitets skydd i Azure Government

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & skydd för identitets säkerhet

Vi är glada att kunna dela att vi nu har lanserat den uppdaterade [Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs)   upplevelsen på [Microsoft Azure Government portalen](https://portal.azure.us/). Mer information finns i [blogg inlägget för meddelande](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Haveri beredskap: Hämta och lagra etablerings konfigurationen

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel
 
Azure AD Provisioning-tjänsten innehåller en omfattande uppsättning konfigurations funktioner. Kunderna måste kunna spara sin konfiguration så att de kan referera till den senare eller återställa till en känd version. Vi har lagt till möjligheten att ladda ned din etablerings konfiguration som en JSON-fil och ladda upp den när du behöver den. [Läs mer](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (lösen ords återställning via självbetjäning) kräver nu två portar för administratörer i Microsoft Azure som drivs av 21Vianet (Azure Kina 21Vianet) 

**Typ:** Ändrad funktion  
**Tjänste kategori:** Lösen ords återställning via självbetjäning  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Tidigare i Microsoft Azure som drivs av 21Vianet (Azure Kina 21Vianet), administratörer som använder självbetjäning för återställning av lösen ord (SSPR) för att återställa sina egna lösen ord behövde bara en "grind" (Challenge) för att bevisa sin identitet. I offentliga och andra nationella moln måste administratörerna i allmänhet använda två portar för att bevisa sin identitet när de använder SSPR. Men eftersom vi inte har stöd för SMS eller telefonsamtal i Azure Kina 21Vianet, tillät vi ett-Gate lösen ords återställning av administratörer.

Vi skapar SSPR funktions paritet mellan Azure Kina 21Vianet och det offentliga molnet. Administratörerna måste använda två portar när de använder SSPR. SMS, telefonsamtal och meddelandeautentisering och-koder kommer att stödjas. [Läs mer](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>Lösen ords längden är begränsad till 256 tecken

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
För att säkerställa tillförlitligheten för Azure AD-tjänsten är användar lösen ord nu begränsade till 256 tecken. Användare med lösen ord som är längre än detta kommer att uppmanas att ändra sina lösen ord vid efterföljande inloggningar, antingen genom att kontakta deras administratör eller genom att använda funktionen för lösen ords återställning via självbetjäning.

Den här ändringen aktiverades den 13 mars 2020, 10 PST (18:00 UTC) och felet är AADSTS 50052, InvalidPasswordExceedsMaxLength. Mer information finns i meddelandet om att [bryta ändring](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) .

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Inloggnings loggarna för Azure AD är nu tillgängliga för alla kostnads fria klienter via Azure Portal

**Typ:** Ändrad funktion  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering
 
Nu kan kunder som har kostnads fria klient organisationer komma åt [inloggnings loggarna för Azure AD från Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) i upp till 7 dagar. Tidigare var inloggnings loggar bara tillgängliga för kunder med Azure Active Directory Premium licenser. Med den här ändringen kan alla klienter komma åt dessa loggar via portalen.

> [!NOTE]
> Kunderna behöver fortfarande en Premium licens (Azure Active Directory Premium P1 eller P2) för att komma åt inloggnings loggarna via Microsoft Graph-API och Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Utfasning av alternativ för katalog över hela gruppen från grupper allmänna inställningar på Azure Portal

**Typ:** Föråldrad  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete

För att ge kunderna ett mer flexibelt sätt att skapa katalog grupper som bäst uppfyller deras behov har vi ersatt alternativet för **katalog grupper** **från gruppen**  >  **allmänna** inställningar i Azure Portal med en länk till [dynamisk grupp dokumentation](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership). Vi har förbättrat vår dokumentation för att inkludera fler instruktioner så att administratörer kan skapa grupper med alla användare som inkluderar eller undantar gäst användare.

---

## <a name="february-2020"></a>Februari 2020

### <a name="upcoming-changes-to-custom-controls"></a>Kommande ändringar i anpassade kontroller

**Typ:** Planera för ändring  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Vi planerar att ersätta den aktuella för hands versionen av anpassade kontroller med en metod som gör det möjligt att samar beta med de funktioner som tillhandahålls av partner för att arbeta smidigt med Azure Active Directory administratör och slutanvändarens upplevelse. I dag är partner MFA-lösningarna riktade mot följande begränsningar: de fungerar bara när ett lösen ord har angetts. de fungerar inte som MFA för stegvis autentisering i andra nyckel scenarier. och de integreras inte med funktionerna för hantering av slutanvändare och administration av autentiseringsuppgifter. Den nya implementeringen tillåter att samarbets faktorer som tillhandahålls av partner arbetar tillsammans med inbyggda faktorer för viktiga scenarier, inklusive registrering, användning, MFA-anspråk, stegvis autentisering, rapportering och loggning. 

Anpassade kontroller fortsätter att stödjas i för hands versionen tillsammans med den nya designen tills den når allmän tillgänglighet. Vid det här skedet ger vi kunderna tid att migrera till den nya designen. På grund av begränsningarna i den aktuella metoden kommer vi inte att publicera nya leverantörer förrän den nya designen är tillgänglig. Vi arbetar nära kunder och leverantörer och kommer att kommunicera med tids linjen medan vi får närmare. [Läs mer](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identifiera säkra Poäng – MFA Improvement åtgärd uppdateringar

**Typ:** Planera för ändring  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
För att avspegla behovet av företag för att säkerställa den högsta säkerheten vid användning av principer som fungerar med deras verksamhet, tar Microsoft Secures score bort tre förbättrings åtgärder som är centrerade kring Multi-Factor Authentication (MFA) och lägger till två.

Följande förbättrings åtgärder kommer att tas bort:

- Registrera alla användare för MFA
- Kräv MFA för alla användare
- Kräv MFA för privilegierade Azure AD-roller

Följande förbättrings åtgärder kommer att läggas till:

- Se till att alla användare kan slutföra MFA för säker åtkomst
- Kräv MFA för administrativa roller

Dessa nya förbättringar kräver att du registrerar användare eller administratörer för MFA i din katalog och hur du skapar rätt uppsättning principer som passar organisationens behov. Huvud målet är att ha flexibilitet och samtidigt se till att alla användare och administratörer kan autentisera med flera faktorer eller riskfyllda identitets verifierings meddelanden. Detta kan vara en form av att ställa in säkerhets inställningar som gör att Microsoft kan avgöra när användare ska kunna använda MFA eller ha flera principer som tillämpar besluts fattandet i definitions området. Som en del av dessa uppdateringar av förbättrings åtgärden kommer principer för bas linje skydd inte längre att ingå i beräknings beräkningar. [Läs mer om vad som kommer i Microsofts säkra Poäng](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Val av Azure AD Domain Services SKU

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services
 
Vi har hört feedback som Azure AD Domain Services kunder vill ha mer flexibilitet när de väljer prestanda nivåer för sina instanser. Från och med den 1 februari 2020 bytte vi från en dynamisk modell (där Azure AD fastställer prestanda-och pris nivån baserat på antal objekt) till en egen markerings modell. Nu kan kunderna välja en prestanda nivå som matchar deras miljö. Den här ändringen gör det också möjligt för oss att aktivera nya scenarier som resurs skogar och Premium funktioner som dagliga säkerhets kopieringar. Antalet objekt är nu obegränsat för alla SKU: er, men vi fortsätter att erbjuda förslag på antal objekt för varje nivå.

**Ingen omedelbar kund åtgärd krävs.** För befintliga kunder fastställer den dynamiska nivån som användes den 1 februari 2020 den nya standard nivån. Det finns inga priser eller prestanda som påverkar resultatet av den här ändringen. Azure AD DS-kunder kommer att behöva utvärdera prestanda krav när deras katalog storlek och arbets belastnings egenskaper ändras. Växling mellan tjänst nivåer fortsätter att vara en åtgärd utan avbrott och vi kommer inte längre att automatiskt flytta kunder till nya nivåer baserat på katalogens tillväxt. Dessutom kommer det inte att finnas några prisökningar och den nya prissättningen kommer att justeras mot vår aktuella fakturerings modell. Mer information finns i dokumentationen för [Azure AD DS SKU](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) och [sidan Azure AD Domain Services prissättning](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – februari 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I februari 2020 har vi lagt till dessa 31 nya appar med stöd för federation i app-galleriet: 

[IamIP patent Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [ns1 SSO för Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda e-mail Security Service](https://ess.barracudanetworks.com/sso/azure), [ABA repor ting](https://myaba.co.uk/client-access/signin/auth/msad), [i händelse av kris – online portal](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC Cloud design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [Biodlings Azure AD data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [Korns](https://www.kornferry.com/solutions/kf-digital/kf-assess) [Verkada-kommando](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB navigera](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [New Relic (begränsad utgåva)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), [Tick Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [mall väljare för team](https://links.officeatwork.com/templatechooser-download-teams), [bin](https://www.beesy.me/index.php/site/login), [hälso support system](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [MURAL](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), Wakelet, [Wakelet](https://wakelet.com/login)Firmex [vdr](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink för lärare och skolor](https://www.thinglink.com/) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial) [CODA](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial) [, InvitePeople,](https://invitepeople.com/login) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial)

 
Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nya etablerings anslutningar i Azure AD Application Gallery – februari 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD-stöd för FIDO2 säkerhets nycklar i hybrid miljöer

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Vi presenterar den offentliga för hands versionen av Azure AD-stöd för FIDO2-säkerhetsnycklar i hybrid miljöer. Användare kan nu använda FIDO2-säkerhetsnycklar för att logga in på sina hybrid Azure AD-anslutna Windows 10-enheter och få sömlös inloggning till sina lokala och molnbaserade resurser. Stöd för Hybrid miljöer har varit den mest efterfrågade funktionen från våra kunder med lösen ords skydd eftersom vi ursprungligen startade den offentliga för hands versionen för FIDO2-stöd i Azure AD-anslutna enheter. Autentisering utan lösen ord med avancerade tekniker som biometrik och offentlig/privat nyckel kryptering ger bekvämlighet och enkel användning medan de är säkra. Med den här offentliga för hands versionen kan du nu använda modern autentisering som FIDO2-säkerhetsnycklar för att komma åt traditionella Active Directory-resurser. Mer information finns [på SSO till lokala resurser](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises). 

Kom igång genom att gå till [Aktivera FIDO2-säkerhetsnycklar för din klient](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) för stegvisa anvisningar. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Den nya mitt konto upplevelsen är nu allmänt tillgänglig

**Typ:** Ändrad funktion  
**Tjänste kategori:** Min profil/konto  
**Produkt kapacitet:** Slut användar upplevelser
 
Mitt konto, det enda steget för att hantera slut användar kontots behov, är nu allmänt tillgängligt! Slutanvändare kan komma åt den här nya webbplatsen via URL eller i rubriken för den nya Mina appar-upplevelsen. Lär dig mer om alla självbetjänings funktioner som den nya upplevelsen erbjuder i [min konto Portal översikt](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Min konto webbplats-URL uppdateras till myaccount.microsoft.com

**Typ:** Ändrad funktion  
**Tjänste kategori:** Min profil/konto  
**Produkt kapacitet:** Slut användar upplevelser
 
Den nya slut användar upplevelsen för mitt konto kommer att uppdatera URL: en till `https://myaccount.microsoft.com` Nästa månad. Hitta mer information om upplevelsen och alla självbetjänings funktioner som det erbjuder för slutanvändare i [mitt konto Portal hjälp](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
## <a name="january-2020"></a>Januari 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Den nya portalen för Mina appar är nu allmänt tillgänglig

**Typ:** Planera för ändring  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** Slut användar upplevelser
 
Uppgradera din organisation till den nya My Apps-portalen som nu är allmänt tillgänglig! Mer information om den nya portalen och samlingar finns i [skapa samlingar på mina apps-portalen](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Arbets ytor i Azure AD har bytt namn till samlingar

**Typ:** Ändrad funktion  
**Tjänste kategori:** Mina appar   
**Produkt kapacitet:** Slut användar upplevelser
 
Arbets ytor, filter administratörer kan konfigurera för att organisera sina användares appar, kommer nu att kallas samlingar. Mer information om hur du konfigurerar dem finns i [skapa samlingar på mina apps-portalen](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C telefonin loggning och inloggning med anpassad princip (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
Med telefonnummer för registrering och inloggning kan utvecklare och företag låta sina kunder registrera sig och logga in med ett eng ång slö sen ord som skickas till användarens telefonnummer via SMS. Den här funktionen gör det också möjligt för kunden att ändra sina telefonnummer om de förlorar åtkomsten till sin telefon. Med hjälp av anpassade principer kan du använda telefonin loggning och inloggning för att kommunicera med sitt varumärke genom sidan anpassning. Ta reda på hur du [ställer in telefonin loggning och inloggning med anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nya etablerings anslutningar i Azure AD Application Gallery – januari 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – januari 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I januari 2020 har vi lagt till dessa 33 nya appar med stöd för federation i app-galleriet: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [snabbt Edge-moln](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [LeaveBot](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions, SmartWork](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [Dotcom, SSOGEN](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [EBS,](https://leavebot.io/#home),,, [,](https://www.intumit.com/english/SmartWork.html)- [Azure AD SSO Gateway för Oracle E-Business Suite-, JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [VÄRDBASERAD MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), driver- [Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [TalentSoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB för Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient Driver Manager-programvara](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/) [,,](https://portal.coreview.com/) [Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login), [PingFlow Authentication](https://app-staging.pingview.io/), PrinterLogic [SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), Akari [Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Två nya identifieringar av identitets skydd

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & skydd för identitets säkerhet
 
Vi har lagt till två nya inloggnings typer som är länkade till identitets skydd: misstänkta regler för att ändra Inkorgen och omöjlig resa. De här offline-identifieringarna upptäcks av Microsoft Cloud App Security (MCAS) och påverkar användaren och inloggnings risken i identitets skyddet. Mer information om dessa identifieringar finns i våra [typer av inloggnings risker](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Bryta ändring: URI-fragment kommer inte att transporteras via omdirigeringen för inloggning

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Från och med den 8 februari 2020 lägger tjänsten till ett tomt fragment i begäran när en begäran skickas till login.microsoftonline.com för att logga in en användare.  Detta förhindrar en klass av omdirigerings attacker genom att se till att webbläsaren rensar alla befintliga fragment i begäran. Inget program bör ha ett beroende på detta beteende. Mer information finns i avsnittet om att [dela upp ändringar](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) i dokumentationen för Microsoft Identity Platform.

