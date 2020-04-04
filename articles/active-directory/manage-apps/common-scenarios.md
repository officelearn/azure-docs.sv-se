---
title: Vanliga scenarier för programhantering för Azure Active Directory | Microsoft-dokument
description: Centralisera programhantering med Azure AD
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d21ff820470765b82e397e56a2458603b8e5a7c7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657954"
---
# <a name="centralize-application-management-with-azure-ad"></a>Centralisera programhantering med Azure AD

Lösenord, både en IT-mardröm och en smärta för anställda över hela världen. Det är därför allt fler företag vänder sig till Azure Active Directory, Microsofts Identity and Access Management-lösning för molnet och alla dina andra resurser. Hoppa från program till program utan att behöva ange ett lösenord för var och en. Hoppa från Outlook, till Arbetsdag, till ADP så fort du kan öppna dem, snabbt och säkert. Samarbeta sedan med partners och även andra utanför organisationen utan att behöva ringa IT. Dessutom hjälper Azure AD till att hantera risker genom att skydda de appar du använder med saker som multifaktorautentisering för att verifiera vem du är, med hjälp av kontinuerligt anpassad maskininlärning och säkerhetsinformation för att upptäcka misstänkta inloggningar som ger dig säker åtkomst till de appar du behöver, var du än befinner dig. Det är inte bara bra för användarna utan även för IT. Med just-in-time-åtkomstgranskningar och en fullskalig styrningssvit hjälper Azure AD dig att hålla dig i efterlevnad och tillämpa principer också. Och få detta, du kan även automatisera etablering användarkonton, vilket gör åtkomsthantering en barnlek. kolla in några av de vanliga scenarier som kunden använder Azure Active Directorys programhanteringsfunktioner för.

**Vanliga scenarier**


> [!div class="checklist"]
> * SSO för alla dina program
> * Automatisera etablering och avetablering 
> * Skydda dina program
> * Styr åtkomsten till dina program
> * Hybrid säker åtkomst

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Scenario 1: Konfigurera SSO för alla dina program

Inget mer hantera lösenord. Få åtkomst till alla resurser du behöver med dina företagsautentiseringsuppgifter. 

|Funktion  | Beskrivning | Rekommendation |
|---------|---------|---------|
|Enkel inloggning|Standardbaserad federerad SSO med hjälp av tillförlitliga branschstandarder.|Använd alltid [SAML / OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) för att aktivera SSO när ditt program stöder det.|
|Åtkomstpanel|Erbjud användarna ett enkelt nav för att upptäcka och komma åt alla sina program. Gör det möjligt för dem att bli mer produktiva med självbetjäningsfunktioner, till exempel begära åtkomst till appar och grupper eller hantera åtkomst till resurser för andras räkning.| Distribuera [åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) i din organisation när du har integrerat dina appar med Azure AD för SSO.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Scenario 2: Automatisera etablering och avetablering 


De flesta program kräver att en användare etableras i programmet innan de får åtkomst till de resurser de behöver. Använda CSV filer eller komplexa skript kan vara dyrt och svårt att hantera. Dessutom måste kunderna se till att konton tas bort när någon inte längre ska ha åtkomst. Utnyttja verktygen nedan för att automatisera etablering och avetablering. 


|Funktion  |Beskrivning|Rekommendation |
|---------|---------|---------|
|SCIM-etablering|[SCIM](https://aka.ms/SICMOverview) är en bransch bästa praxis för att automatisera användare etablering. Alla SCIM-kompatibla program kan integreras med Azure AD. Skapa, uppdatera och ta bort användarkonton automatiskt utan att behöva underhålla CSV-filer, anpassade skript eller on-prem-lösningar.|Kolla in den växande listan över [förintegrerade](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) appar i Azure AD-appgalleriet|
|Microsoft Graph|Utnyttja datainandan och djupet som Azure AD har för att berika ditt program med de data som behövs.|Utnyttja [Microsoft-diagrammet](https://developer.microsoft.com/graph/) för att hämta data från hela Microsofts ekosystem. |


## <a name="scenario-3-secure-your-applications"></a>Scenario 3: Skydda dina program
Identitet är grundbulten för säkerheten. Om en identitet äventyras, är det otroligt svårt att stoppa dominoeffekten innan det är för sent. I genomsnitt över 100 dagar passera innan organisationer upptäcker att det fanns en kompromiss. Använd verktygen som tillhandahålls av Azure AD för att förbättra säkerhetspositionen för dina program. 

|Funktion  |Beskrivning| Rekommendation |
|---------|---------| ---------|
|Azure MFA|Azure Multi-Factor Authentication (MFA) är Microsofts verifieringslösning i två steg. Med hjälp av administratörsgodkända autentiseringsmetoder hjälper Azure MFA till att skydda åtkomsten till dina data och program samtidigt som du uppfyller kravet på en enkel inloggningsprocess.| [Aktivera MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) för användarna.  |
|Villkorlig åtkomst|Med villkorlig åtkomst kan du implementera beslut om automatisk åtkomstkontroll för vem som kan komma åt dina molnappar, baserat på villkor.| Granska [säkerhetsstandarder](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) och [vanliga principer](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) som kunderna använder. | 
|Identity Protection|Identity Protection använder de lärdomar som Microsoft har förvärvat från sin position i organisationer med Azure AD, konsumentutrymmet med Microsoft-konton och i spel med Xbox för att skydda dina användare. Microsoft analyserar 6,5 biljoner signaler per dag för att identifiera och skydda kunder från hot.|Aktivera [standardprinciperna för identitetsskydd](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) som tillhandahålls av vår tjänst. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Scenario 4: Styra åtkomsten till dina program
Identitetsstyrning hjälper organisationer att uppnå en balans mellan produktivitet – Hur snabbt kan en person ha tillgång till de program de behöver, till exempel när de går med i min organisation? Och säkerhet - Hur ska deras tillgång förändras med tiden, till exempel på grund av förändringar i den personens anställningsstatus? 

|Funktion  |Beskrivning|Rekommendation |
|---------|---------| ---------|
|Elm|Hantering av Azure AD-rättigheter kan hjälpa användare både inom och utanför organisationen att hantera åtkomsten till sina program.| Tillåt icke-administratörer att hantera åtkomst till sina program med [åtkomstpaket](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first).|
|Åtkomstgranskningar|Användarens åtkomst till appar kan granskas regelbundet för att se till att endast rätt personer har fortsatt åtkomst.| [Granska åtkomsten](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) till dina känsligaste program. |
|Log Analytics|Generera rapporter om vem som har åtkomst till vilka program och lagra dem i ditt SIEM-verktyg för att korrelera data mellan datakällor och över tid.| Aktivera [logganalys](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) och konfigurera aviseringar för kritiska händelser relaterade till dina program. |


## <a name="scenario-5-hybrid-secure-access"></a>Scenario 5: Hybrid säker åtkomst
Identitet kan bara vara ditt kontrollplan om det kan ansluta allt över molnet och lokala program. Utnyttja de verktyg som tillhandahålls av Azure AD och dess partner för att skydda åtkomsten till äldre autentiseringsbaserade program.

|Funktion  |Beskrivning|Rekommendation |
|---------|---------|---------|
|Programproxy|I dag vill anställda vara produktiva var som helst, när som helst och från valfri enhet. De måste komma åt SaaS-appar i molnet och företagsappar lokalt. Azure AD Application proxy möjliggör denna robusta åtkomst utan kostsamma och komplexa virtuella privata nätverk (VPN) eller demilitariserade zoner (DMZs).|Konfigurera [fjärråtkomst](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) för dina on-prem-appar. |
|F5, Akamai, Zscaler|Med hjälp av din befintliga nätverks- och leveransstyrenhet kan du enkelt skydda äldre program som fortfarande är viktiga för dina affärsprocesser men som du inte kunde skydda tidigare med Azure AD. Det är troligt att du redan har allt du behöver för att börja skydda dessa program.| Använder du Akamai, Citrix, F5 eller Zscaler? Kolla in våra [färdiga lösningar.](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access) | 

## <a name="related-articles"></a>Relaterade artiklar

- [Programhantering](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [Programetablering](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [Hybrid säker åtkomst]()
- [Identitetsstyrning](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft-identitetsplattform](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Identitetssäkerhet](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
