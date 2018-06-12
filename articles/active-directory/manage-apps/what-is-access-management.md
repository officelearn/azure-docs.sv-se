---
title: Hantera åtkomst till appar med Azure AD | Microsoft Docs
description: Beskriver hur Azure Active Directory gör det möjligt att ange de appar som varje användare har åtkomst till.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: barbkess
ms.openlocfilehash: 019bdecd315f6da91fef8e7ed5d088e328253ef6
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35304086"
---
# <a name="managing-access-to-apps"></a>Hantera åtkomst till appar
Pågående hantering, användning utvärdering och rapportering fortsätter att vara en utmaning när en app har integrerats i din organisation identitetssystem. I många fall kan måste IT-administratörer eller supportavdelningen vidta en pågående aktiv roll i att hantera åtkomst till dina appar. Ibland utförs tilldelning av en allmän eller avdelningar IT-teamet. Ofta tilldelning beslutet är avsedd att vara delegerade till beslutsfattare företag, som kräver godkännande innan IT gör tilldelningen.  Andra organisationer investera i integrering med ett befintligt automatiserade identitets- och -system, t.ex. rollbaserad åtkomstkontroll (RBAC) eller attributbaserad åtkomstkontroll (ABAC). Både integrering och regeln development brukar vara specialiserade och dyrt. Övervakning och rapportering om antingen metoden är en egen separat kostsamma och komplexa investering.

## <a name="how-does-azure-active-directory-help"></a>Hur hjälper Azure Active Directory?
 Azure AD stöder omfattande åtkomsthantering för konfigurerade program, vilket ger företag att enkelt åstadkomma rätt åtkomstprinciper som sträcker sig från automatisk, attributbaserad tilldelning (ABAC eller RBAC-scenario) genom delegering och inklusive administratörshantering. Du kan lätt få komplexa principer kombinera flera management modeller för ett program och kan även återanvända management regler för program med samma målgrupper med Azure AD.

* [Lägger till nya eller befintliga program](configure-single-sign-on-portal.md)

 Azure AD application tilldelning fokuserar på två primära tilldelning lägen:

* **Tilldelning av enskilda** en IT-administratör med globala administratörsbehörigheter för directory välja enskilda användarkonton och ge dem åtkomst till programmet.
* **Gruppbaserad tilldelning (betald Azure AD)** en IT-administratör med directory globala administratörsbehörigheter kan tilldela en grupp till programmet. Specifika användare åtkomst bestäms av om de är medlemmar i gruppen när de försöker komma åt programmet. Med andra ord kan en administratör effektivt skapa en tilldelningsregel anger ”alla aktuella medlemmar i en grupp har åtkomst till programmet”. Med det här alternativet om tilldelning kan administratörer kan dra nytta av någon av Azure AD grupphanteringsalternativ, inklusive [attributbaserad dynamiska grupper](../active-directory-groups-create-azure-portal.md), externt system (exempelvis lokala Active Directory eller Workday), eller hanteras av en administratör eller egen service-hanterade grupper. En grupp kan enkelt tilldelas till flera appar säkerställer att program med tilldelning tillhörighet kan dela tilldelningsregler, vilket minskar den övergripande hanteringen. Observera att kapslad grupp medlemskap inte stöds för gruppbaserad tilldelning till program just nu.

Med dessa tilldelning av två lägen kan kan administratörer uppnå metoden alla önskvärt tilldelning.

Med Azure AD, är användning och tilldelning reporting helt integrerade, så att administratörer enkelt rapportera om tilldelningen, Tilldelningsfel och även användning.

## <a name="complex-application-assignment-with-azure-ad"></a>Programtilldelning av komplexa med Azure AD
Överväg att ett program som Salesforce. I många organisationer används främst Salesforce av marknadsförings- och organisationer. Medlemmar i gruppen har ofta mycket privilegierad åtkomst till Salesforce, när medlemmarna i säljgruppen har begränsad åtkomst. I många fall har en bred uppsättning informationsarbetare begränsad åtkomst till programmet. Undantag från reglerna göra det svårare för frågor. Det är ofta förmånsrätt marknadsföring eller försäljning ledande team att ge en tillgång eller ändra deras roller oberoende av dessa allmänna regler.

Med Azure AD kan program som Salesforce vara förkonfigurerade för enkel inloggning (SSO) och automatisk etablering. När programmet har konfigurerats kan kan en administratör bli engångsåtgärd att skapa och tilldela rätt grupper. I det här exemplet kan en administratör kör du följande tilldelningar:

* [Dynamiska grupper](../active-directory-groups-create-azure-portal.md) kan definieras som representerar alla medlemmar i de marknadsförings- och grupper med hjälp av attribut som avdelningen eller rollen automatiskt:
  
  * Alla medlemmar i marknadsföring grupper skulle tilldelas rollen ”marknadsföring” i Salesforce
  * Alla medlemmar i säljgruppen grupper skulle tilldelas rollen ”Säljare” i Salesforce. En ytterligare förfining kan använda flera grupper som representerar regionala försäljning team som tilldelas olika Salesforce-roller.
* Om du vill aktivera mekanismen undantag, kunde en grupp med självbetjäning skapas för varje roll. Gruppen ”Salesforce marknadsföring undantag” kan till exempel skapas som en grupp för självbetjäning. Gruppen kan tilldelas rollen Salesforce försäljning och marknadsföring ledning kan göras ägare. Medlemmar i marknadsföring ledning kan lägga till eller ta bort användare, ange en princip för koppling eller även godkänna eller neka förfrågningar för enskilda användare att ansluta till. Detta stöds via en information worker lämpliga upplevelse som inte kräver särskilda utbildning för ägare eller medlemmar.

I så fall måste skulle alla tilldelade användare automatiskt etableras till Salesforce, när de läggs till olika användargrupper, deras rolltilldelning skulle uppdateras i Salesforce. Användare skulle kunna upptäcka och åtkomst till Salesforce via Microsoft-program åtkomstpanelen, Office webbklienter eller även genom att gå till sin organisations Salesforce-inloggningssidan. Administratörer skulle kunna enkelt visa status för användning och tilldelning med hjälp av Azure AD-rapportering.

Administratörer kan använda [villkorlig åtkomst i Azure AD](../active-directory-conditional-access-azure-portal.md) att ange åtkomstprinciper för specifika roller. Dessa principer kan innehålla om åtkomst tillåts utanför företagsmiljö och även Multifaktorautentisering krav eller enhet för att få åtkomst i olika fall.

## <a name="next-steps"></a>Nästa steg

* [Artikelindex för programhantering i Azure Active Directory](../active-directory-apps-index.md)
* [Skydda appar med villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md)
* [Självbetjäning hantering/SSAA](../active-directory-accessmanagement-self-service-group-management.md)
