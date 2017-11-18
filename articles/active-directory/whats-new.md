---
title: "Senaste nytt Viktig information för Azure Active Directory | Microsoft Docs"
description: "Läs mer om nyheterna i Azure Active Directory (AD Azure) inklusive senaste viktig information, kända problem, felkorrigeringar, föråldrade funktioner och kommande ändringar."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: a44faec6c21c338dfc6b1507af6f716e089c7038
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?




> Hålla dig uppdaterad med vad är nytt i Azure Active Directory genom att prenumerera på detta [feed](https://docs.microsoft.com/api/search/rss?search=%22what%27s%20new%20in%20azure%20active%20directory%3F%22&locale=en-us) i din favorit RSS-feed läsare.



Vi förbättrar Azure Active Directory med jämna mellanrum. Om du vill aktivera dig att hålla dig uppdaterad med den senaste utvecklingen finns i det här avsnittet information om:

-   De senaste versionerna 
-   Kända problem 
-   Felkorrigeringar 
-   Föråldrade funktioner 
-   Planer för ändringar 

Du besöker sidan regelbundet vi uppdateras varje månad.

## <a name="november-2017"></a>November 2017

**Typ:** föråldrade funktioner  
**Tjänstekategori:** ACS  
**Produkten kapaciteten:** åtkomst till tjänsten 

<a name="acs-retirement"></a>

Microsoft Azure Active Directory-åtkomstkontroll (även kallat åtkomstkontrolltjänsten eller ACS) dras i November 2018.  Ytterligare information, inklusive en detaljerad schema & hög nivå migrering vägledning finns [på den här sidan](./develop/active-directory-acs-migration.md).

---


## <a name="october-2017"></a>Oktober 2017

**Typ:** ändringsplan  
**Tjänstekategori:** rapportering  
**Produkten kapaciteten:** Identitetshantering livscykel  


**Sluta Azure AD-rapporter (betaversion) API: er under den `https://graph.windows.net/<tenant-name>/reports/` nod**

Azure-portalen ger dig:

- En ny Azure Active Directory-Administrationskonsol 
- Nya API: er för aktiviteten och säkerhet
 
På grund av de nya funktionerna i rapporten API: er under den **/rapporter** slutpunkten ska tas bort på 10 December 2017. 

---

**Typ:** fast   
**Tjänstekategori:** Mina appar  
**Produkten kapaciteten:** enkel inloggning  


Azure Active Directory har stöd för automatisk inloggning fältet identifiering för program som renderas ett HTML-fält för användarnamn och lösenord.  De här stegen finns dokumenterade i [så att automatiskt samla in inloggning fält för ett program](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Du hittar den här funktionen genom att lägga till en *icke-galleriet* på den **företagsprogram** sidan i den [Azure-portalen](http://aad.portal.azure.com). Du kan också konfigurera den **enkel inloggning** läge för den här nya programmet till **lösenordsbaserade enkel inloggning**, ange en URL och sedan spara sidan.
 
Den här funktionen har tillfälligt inaktiverats för en viss tidsperiod på grund av problem med en tjänst. Problemet har lösts och fältet för automatisk inloggning identifieringen är tillgänglig igen.

---

**Typ:** ny funktion  
**Tjänstekategori:** MFA  
**Produkten kapaciteten:** identitet säkerhet och skydd  


I vi live i världen, är multifaktorautentisering (MFA) en viktig del av att skydda din organisation. Identity-team på Microsoft är under utveckling multifaktorautentisering för att göra mer anpassningsbar autentiseringsuppgifter och erfarenhet smidigare. Jag är idag glädjen att tillkännage två viktiga steg i den här resa: 

- Integrering av Multi-Factor challenge resultat direkt i Azure AD-inloggning rapporten, inklusive Programmeringsåtkomst till MFA-resultat

- Djupare integrering av MFA-konfigurationen till Azure AD-konfiguration core upplevelse i Azure-portalen

Är en integrerad del av grundläggande Azure AD configuration upplevelse, så att du kan hantera MFA Management portal funktionerna i Azure AD-upplevelse med den här offentliga förhandsversionen MFA hantering och rapportering.

Mer information finns i [referens för multifaktorautentisering rapportering i Azure-portalen](active-directory-reporting-activity-sign-ins-mfa.md) 


---
**Typ:** ny funktion  
**Tjänstekategori:** villkor för användning  
**Produkten kapaciteten:** styrning  


**Azure AD-villkor för användning av** ger dig en enkel metod för att presentera information till slutanvändare. Detta säkerställer att användarna ser relevanta FRISKRIVNINGAR för juridiska eller efterlevnadskrav.

Du kan använda Azure AD-villkor för användning i följande scenarier:

- Allmänna villkor för användningsvillkor för alla användare i din organisation. 

- Särskilda villkor för användning baserat på användarattribut (t.ex. läkare vs sjuksköterskor eller nationella vs internationella anställda, utförs av dynamiska grupper). 

- Särskilda villkor för användning av åtkomst till affärskritiska påverkan appar, t.ex. Salesforce.

Mer information finns i [Azure Active Directory användningsvillkoren](active-directory-tou.md).


---
**Typ:** ny funktion  
**Tjänstekategori:** PIM  
**Produkten kapaciteten:** Privileged Identity Management  


Med Azure Active Directory Privileged Identity Management (PIM), kan du nu hantera, kontrollera och övervaka åtkomst till resurser i Azure (förhandsversion) inom din organisation. Detta inkluderar prenumerationer och resursgrupper även virtuella datorer. Alla resurser i Azure-portalen som utnyttjar Azure rollbaserad åtkomstkontroll (RBAC)-funktioner kan dra nytta av alla bra säkerhets- och hanteringsfunktioner för livscykel Azure AD PIM har att erbjuda och vissa nya funktionerna som vi planerar att göra Azure AD-roller snart.

Mer information finns i [PIM för Azure-resurser](privileged-identity-management/azure-pim-resource-rbac.md).


---
**Typ:** ny funktion  
**Tjänstekategori:** åtkomst till granskningar  
**Produkten kapaciteten:** styrning  


Åtkomst granskningar (förhandsgranskning) gör att organisationer att effektivt hantera gruppmedlemskap och åtkomst till företagsprogram: 

- Du kan recertify gästen användaråtkomst med hjälp av åtkomst granskning av deras åtkomst till program och medlemskap i grupper. De insikter som tillhandahålls av access granskningar aktivera granskare effektivt besluta om gäster ska ha fortsatt åtkomst.

- Du kan certifiera om medarbetares åtkomst till program och gruppmedlemskap med åtkomstgranskningar.

Du kan samla in åtkomstgranskningskontroller i program som är relevanta för din organisation för att spåra granskningar för efterlevnad eller riskkänsliga program.

Mer information finns i [åtkomst till Azure AD granskar](active-directory-azure-ad-controls-access-reviews-overview.md).


---
**Typ:** ny funktion  
**Tjänstekategori:** Mina appar  
**Produkten kapaciteten:** enkel inloggning  


**Möjligheten att dölja tredjepartsprogram från Mina appar och Office 365-starta**

Du kan nu hantera appar som visas på din användare portaler via en ny bättre **dölja app** egenskapen. Detta hjälper med fall där appen paneler visas för backend-tjänster eller dubbla paneler och hamna fyller hela användarens app startprogram. Att växlingsknappen finns i egenskapsavsnittet för appen från tredje part och etiketteras **synligt för användaren?**. Du kan även dölja en app programmässigt med hjälp av PowerShell. 

Mer information finns i [dölja tredjepartsprogram från användarens upplevelse i Azure Active Directory](active-directory-coreapps-hide-third-party-app.md). 


**Vad är tillgängliga?**

 Vi har gjort 2 nya API: er för att hämta Azure AD-aktivitetsloggar tillgänglig som en del av övergången till en ny Administratörskonsol. Ny uppsättning API: er ger bättre filtrera och sortera funktioner förutom att tillhandahålla bättre gransknings- och logga in aktiviteter. Data som tidigare var tillgängliga via säkerhetsrapporter kan nu nås via identitetsskydd riskhändelser API i Microsoft Graph.


## <a name="september-2017"></a>September 2017

**Typ:** ändrade funktionen  
**Tjänstekategori:** Microsoft Identity Manager  
**Produkten kapaciteten:** Identitetshantering livscykel  


En samlad snabbkorrigering (build 4.4.1642.0) är tillgängliga från och med 25 September 2017 för Microsoft Identity Manager (MIM) 2016 2016 servicepack 1 (SP1). Det här paketet:

- Löser problem och lägger till förbättringar
- Är en kumulativ uppdatering som ersätter alla uppdateringar i MIM 2016 SP1 upp till build 4.4.1459.0 för Microsoft Identity Manager 2016. 
- Du måste ha **Microsoft Identity Manager 2016 skapa 4.4.1302.0.** 

Mer information finns i [samlade paketet (build 4.4.1642.0) är tillgängligt för Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/en-us/help/4021562). 

---
