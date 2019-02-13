---
title: Hantera åtkomst till appar med hjälp av Azure AD | Microsoft Docs
description: Beskriver hur Azure Active Directory kan organisationer att ange de appar som varje användare har åtkomst till.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14957a94dbe8330553a4090e22d80d6cc2ee06eb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217334"
---
# <a name="managing-access-to-apps"></a>Hantera åtkomst till appar
Pågående hantering, användning utvärdering och rapportering fortsätta att vara en utmaning att när en app har integrerats i din organisations identitetssystem. I många fall kan måste IT-administratörer eller supportavdelningen vidta en pågående aktiv roll i hanteringen av åtkomst till dina appar. Ibland utförs tilldelning av en allmän eller avdelningar IT-team. Ofta tilldelning beslutet är avsedd att delegeras till beslutsfattare företag som kräver godkännande innan IT gör tilldelningen.  Andra organisationer investera i integrering med ett befintligt automatiserade identitets- och -system, som rollbaserad åtkomstkontroll (RBAC) eller attributbaserad åtkomstkontroll (ABAC). Både integrering och utveckling av regeln brukar vara särskilda och dyra. Övervakning eller rapporter på antingen hanteringsstrategi är en egen separat, kostsamma och komplexa investering.

## <a name="how-does-azure-active-directory-help"></a>Hur hjälper Azure Active Directory?
 Azure AD stöder omfattande åtkomsthantering för konfigurerade program att organisationer kan enkelt uppnå rätt åtkomstprinciper som sträcker sig från automatisk, attributbaserade tilldelning (ABAC eller RBAC-scenario) via delegering och inklusive administratörshantering. Med Azure AD, du kan enkelt utnyttja avancerade principer, kombinera flera management modeller för ett enskilt program och kan även återanvända management regler i flera program med samma målgrupper.

* [Lägger till nya eller befintliga program](configure-single-sign-on-portal.md)

 Azure Active Directorys programtilldelning fokuserar på två primära tilldelning lägen:

* **Enskild tilldelning** en IT-administratören med directory behörigheter som Global administratör kan välja enskilda användarkonton och ge dem åtkomst till programmet.
* **Gruppbaserad tilldelning (betalda Azure AD)** en IT-administratören med directory behörigheter som Global administratör kan tilldela en grupp till programmet. Specifika användare åtkomst bestäms av om de är medlemmar i gruppen när de försöker komma åt programmet. En administratör kan med andra ord ett effektivt sätt att skapa en tilldelningsregel om ”alla aktuella medlemmar i den tilldelade gruppen har åtkomst till programmet”. Med det här alternativet om tilldelning kan administratörer kan dra nytta av någon av Azure AD grupphanteringsalternativ, inklusive [attributbaserade dynamiska grupper](../fundamentals/active-directory-groups-create-azure-portal.md), externt systemgrupper (till exempel lokalt Active Directory eller Workday) eller hanteras av en administratör eller klientportal självservice-hanterade grupper. En grupp kan enkelt tilldelas till flera appar att se till att program med tilldelning tillhörighet kan dela tilldelningsreglerna, vilket minskar det övergripande komplexitet. Observera att kapslade gruppmedlemskap inte stöds för gruppbaserad tilldelning till program just nu.

Med dessa två tilldelning lägen kan kan administratörer få alla önskvärt tilldelning hanteringsstrategi.

Med Azure AD, är användning och tilldelning reporting helt integrerat, vilket gör att administratörer att enkelt rapportera om tilldelningen av, Tilldelningsfel och även användning.

## <a name="complex-application-assignment-with-azure-ad"></a>Programtilldelning av komplexa med Azure AD
Tänk dig ett program som Salesforce. I många organisationer används huvudsakligen Salesforce teamen för marknadsföring och försäljning. Medlemmar i marknadsföringsgruppen har ofta högt privilegierade åtkomst till Salesforce, medlemmarna i säljgruppen har begränsad åtkomst. I många fall har en bred uppsättning informationsarbetare begränsad åtkomst till programmet. Undantag till dessa regler ting. Det är ofta förmånsrätt marknadsföring eller försäljning ledarskap team att ge en användaråtkomst eller ändra deras roller oberoende av dessa allmänna regler.

Med Azure AD kan program som Salesforce vara redan konfigurerats för enkel inloggning (SSO) och automatisk etablering. När programmet har konfigurerats kan kan en administratör bli engångsåtgärd du skapar och tilldelar lämpliga grupper. I det här exemplet kan en administratör kör följande tilldelningar:

* [Dynamiska grupper](../fundamentals/active-directory-groups-create-azure-portal.md) kan definieras för att representera automatiskt alla medlemmar i teamen för marknadsföring och försäljning med attribut som avdelning eller roll:
  
  * Alla medlemmar i marknadsföring grupper tilldelas till rollen ”marknadsföring” i Salesforce
  * Alla medlemmar i grupper tilldelas till rollen ”försäljning” i Salesforce-säljteam. En ytterligare förfining kan använda flera grupper som representerar regionala försäljningsteam som tilldelas till olika Salesforce-roller.
* Om du vill aktivera mekanismen för undantag kunde en självbetjäning grupp skapas för varje roll. Gruppen ”Salesforce marknadsföring undantag” kan till exempel skapas som en grupp med självbetjäning. Gruppen kan tilldelas till rollen Salesforce marknadsföring och marknadsföring ledning kan göras ägare. Medlemmar i gruppen ledarskap kan lägga till eller ta bort användare, ange en join-principen, eller även Godkänn eller neka begäranden för enskilda användare att ansluta till. Den här mekanismen stöds via en information worker lämplig upplevelse som inte kräver specialiserade utbildning för ägare eller medlemmar.

I det här fallet skulle alla tilldelade användare etableras automatiskt till Salesforce, när de läggs till olika grupper sina rolltilldelning skulle uppdateras i Salesforce. Användare kommer att kunna upptäcka och komma åt Salesforce via Microsoft programåtkomstpanelen, Office web-klienter, eller även genom att gå till sin organisations Salesforce-inloggningssidan. Administratörer kommer att kunna lätt visa användning och tilldelning status med hjälp av Azure AD-rapportering.

Administratörer kan använda [Azure AD villkorsstyrd åtkomst](../active-directory-conditional-access-azure-portal.md) att ställa in åtkomstprinciper för specifika roller. Dessa principer kan innehålla om åtkomst tillåts utanför företagets miljö och även Multi-Factor Authentication eller enhet kraven för att få åtkomst i olika fall.

## <a name="next-steps"></a>Nästa steg
* [Skydda appar med villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md)
* [Självbetjäning management/SSAA](../users-groups-roles/groups-self-service-management.md)
