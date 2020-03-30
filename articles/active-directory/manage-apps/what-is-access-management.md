---
title: Hantera åtkomst till appar med Azure AD | Microsoft-dokument
description: Beskriver hur Azure Active Directory gör det möjligt för organisationer att ange de appar som varje användare har åtkomst till.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 489b15423add03d69070bc32057af97396a85309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409074"
---
# <a name="managing-access-to-apps"></a>Hantera åtkomst till appar

Löpande åtkomsthantering, användningsutvärdering och rapportering fortsätter att vara en utmaning när en app har integrerats i organisationens identitetssystem. I många fall måste IT-administratörer eller helpdesk ta en kontinuerlig aktiv roll i hanteringen av åtkomsten till dina appar. Ibland utförs uppdraget av ett allmänt eller divisionellt IT-team. Ofta är tilldelningsbeslutet avsett att delegeras till beslutsfattaren, vilket kräver deras godkännande innan IT gör uppdraget.  Andra organisationer investerar i integration med ett befintligt automatiserat identitets- och åtkomsthanteringssystem, till exempel Roll-Based Access Control (RBAC) eller Attribute-Based Access Control (ABAC). Både integration och regelutveckling tenderar att vara specialiserade och dyra. Övervakning eller rapportering om antingen förvaltningsstrategi är dess egen separata, kostsamma och komplexa investeringar.

## <a name="how-does-azure-active-directory-help"></a>Hur hjälper Azure Active Directory till?

Azure AD stöder omfattande åtkomsthantering för konfigurerade program, vilket gör det möjligt för organisationer att enkelt uppnå rätt åtkomstprinciper som sträcker sig från automatiska, attributbaserade tilldelningar (ABAC- eller RBAC-scenarier) via delegering och inklusive delegering och inklusive delegering administratörshantering. Med Azure AD kan du enkelt uppnå komplexa principer, kombinera flera hanteringsmodeller för ett enda program och även återanvända hanteringsregler över program med samma målgrupper.

Med Azure AD är användnings- och tilldelningsrapportering helt integrerad, vilket gör det möjligt för administratörer att enkelt rapportera om tilldelningstillstånd, tilldelningsfel och till och med användning.

### <a name="assigning-users-and-groups-to-an-app"></a>Tilldela användare och grupper till en app

Azure AD:s programtilldelning fokuserar på två primära tilldelningslägen:

* **Individuellt uppdrag** En IT-administratör med global administratörsbehörighet för katalog kan välja enskilda användarkonton och ge dem åtkomst till programmet.

* **Gruppbaserad tilldelning (kräver Azure AD Premium P1 eller P2)** En IT-administratör med global administratörsbehörighet för katalog kan tilldela en grupp till programmet. Specifika användares åtkomst bestäms av om de är medlemmar i gruppen när de försöker komma åt programmet. Med andra ord kan en administratör effektivt skapa en tilldelningsregel som anger "alla aktuella medlemmar i den tilldelade gruppen har åtkomst till programmet". Med det här tilldelningsalternativet kan administratörer dra nytta av något av Azure AD-grupphanteringsalternativ, inklusive [attributbaserade dynamiska grupper,](../fundamentals/active-directory-groups-create-azure-portal.md)externa systemgrupper (till exempel lokala Active Directory eller Workday) eller administratörshanterade eller självbetjäningshanterade grupper. En enskild grupp kan enkelt tilldelas flera appar och se till att program med tilldelningstillhörighet kan dela tilldelningsregler, vilket minskar den övergripande hanteringskomplexiteten. Observera att kapslade gruppmedlemskap inte stöds för gruppbaserad tilldelning till program just nu.

Med hjälp av dessa två tilldelningslägen kan administratörer uppnå alla önskvärda tilldelningshanteringsmetoder.

### <a name="requiring-user-assignment-for-an-app"></a>Kräver användartilldelning för en app

Med vissa typer av program har du möjlighet [att kräva att användare ska tilldelas programmet](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment). På så sätt hindrar du alla från att logga in förutom de användare som du uttryckligen tilldelar programmet. Följande typer av program stöder det här alternativet:

* Program som konfigurerats för federerad enkel inloggning (SSO) med SAML-baserad autentisering
* Programproxyprogram som använder Azure Active Directory före autentisering
* Program som bygger på Azure AD-programplattformen som använder OAuth 2.0 / OpenID Connect-autentisering efter att en användare eller administratör har samtyckt till det programmet. Vissa företagsprogram ger ytterligare kontroll över vem som får logga in.

När användartilldelning *inte krävs*visas inte appen på åtkomstpanelen Mina appar, men de kan fortfarande logga in på själva programmet (kallas även SP-initierad inloggning) eller så kan de använda **url:en** för användaråtkomst på programmets **egenskapssida** (kallas även IDP-initierad inloggning).

För vissa program är alternativet att kräva användartilldelning inte tillgängligt i programmets egenskaper. I dessa fall kan du använda PowerShell för att ange egenskapen appRoleAssignmentRequired på tjänstens huvudnamn.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Bestämma användarupplevelsen för åtkomst till appar

Azure AD innehåller [flera anpassningsbara sätt att distribuera program](end-user-experiences.md) till slutanvändare i organisationen:

* Åtkomstpanelen för Azure AD My Apps
* Startprogram för Office 365
* Direkt inloggning till federerade appar (service-pr)
* Djuplänkar till federerade, lösenordsbaserade eller befintliga appar

Du kan avgöra om användare som tilldelats ett företagsapp kan se det i åtkomstpanelen och Programstartprogrammet för Office 365.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Exempel: Komplex programtilldelning med Azure AD
Överväg ett program som Salesforce. I många organisationer används Salesforce främst av marknadsförings- och säljteamen. Ofta har medlemmar i marknadsföringsteamet mycket privilegierad åtkomst till Salesforce, medan medlemmar i säljteamet har begränsad åtkomst. I många fall har en bred population av informationsarbetare begränsat tillgången till programmet. Undantag från dessa regler komplicerar saken. Det är ofta förmånen för marknadsföring eller försäljning ledningsgrupper att ge en användare tillgång eller ändra sina roller oberoende av dessa generiska regler.

Med Azure AD kan program som Salesforce förkonfigureras för enkel inloggning (SSO) och automatisk etablering. När programmet har konfigurerats kan en administratör vidta en engångsåtgärd för att skapa och tilldela lämpliga grupper. I det här exemplet kan en administratör utföra följande tilldelningar:

* [Dynamiska grupper](../fundamentals/active-directory-groups-create-azure-portal.md) kan definieras för att automatiskt representera alla medlemmar i marknadsförings- och säljteamen med hjälp av attribut som avdelning eller roll:
  
  * Alla medlemmar i marknadsföringsgrupper skulle tilldelas rollen "marknadsföring" i Salesforce
  * Alla medlemmar i säljteamgrupper skulle tilldelas rollen "försäljning" i Salesforce. En ytterligare förfining kan använda flera grupper som representerar regionala säljteam som tilldelats olika Salesforce-roller.

* För att aktivera undantagsmekanismen kan en självbetjäningsgrupp skapas för varje roll. Gruppen "Salesforce marketing exception" kan till exempel skapas som en självbetjäningsgrupp. Gruppen kan tilldelas salesforce-marknadsföringsrollen och marknadsledarteamet kan göras ägare. Medlemmar i marknadsföringsledarteamet kan lägga till eller ta bort användare, ange en anslutningspolicy eller till och med godkänna eller neka enskilda användares begäranden om att gå med. Den här mekanismen stöds genom en lämplig informationsarbetare som inte kräver specialiserad utbildning för ägare eller medlemmar.

I det här fallet etableras alla tilldelade användare automatiskt till Salesforce, eftersom de läggs till i olika grupper som deras rolltilldelning uppdateras i Salesforce. Användare kan upptäcka och komma åt Salesforce via Åtkomstpanelen för Microsoft-program, Office-webbklienter eller till och med genom att navigera till sin organisationsinloggningssida för Salesforce. Administratörer kan enkelt visa användnings- och tilldelningsstatus med Hjälp av Azure AD-rapportering.

Administratörer kan använda [Azure AD-villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) för att ange åtkomstprinciper för specifika roller. Dessa principer kan omfatta om åtkomst tillåts utanför företagsmiljön och till och med multifaktorautentisering eller enhetskrav för att uppnå åtkomst i olika fall.

## <a name="access-to-microsoft-applications"></a>Tillgång till Microsoft-program

Microsoft-program (som Office 365 Exchange, SharePoint, Yammer, etc.) tilldelas och hanteras lite annorlunda än SaaS-program från tredje part eller andra program som du integrerar med Azure AD för enkel inloggning.

Det finns tre huvudsakliga sätt att en användare kan få tillgång till ett Microsoft-publicerat program.

- För program i Office 365 eller andra betalda sviter beviljas användare åtkomst via **licenstilldelning** antingen direkt till sitt användarkonto eller via en grupp med hjälp av vår gruppbaserade licenstilldelningsfunktion.
- För program som Microsoft eller en tredje part publicerar fritt för vem som helst att använda, kan användare beviljas åtkomst via [användarens medgivande](configure-user-consent.md). Det innebär att de loggar in på programmet med sitt Azure AD Work- eller School-konto och tillåter att de har åtkomst till vissa begränsade data för sitt konto.
- För program som Microsoft eller en tredje part publicerar fritt för alla att använda, kan användare också beviljas åtkomst via [administratörsmedgivande](manage-consent-requests.md). Det innebär att en administratör har fastställt att programmet kan användas av alla i organisationen, så att de loggar in på programmet med ett globalt administratörskonto och ger åtkomst till alla i organisationen.

Vissa program kombinerar dessa metoder. Vissa Microsoft-program ingår till exempel i en Office 365-prenumeration, men kräver fortfarande medgivande.

Användare kan komma åt Office 365-program via sina Office 365-portaler. Du kan också visa eller dölja Office 365-program på åtkomstpanelen Mina program med [synligheten för Office 365](hide-application-from-user-portal.md) i katalogens **användarinställningar**. 

Precis som med företagsappar kan du [tilldela användare](assign-user-or-group-access-portal.md) till vissa Microsoft-program via Azure-portalen eller, om portalalternativet inte är tillgängligt, med hjälp av PowerShell.

## <a name="next-steps"></a>Nästa steg
* [Skydda appar med villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md)
* [Gruppledning för självbetjäning/SSAA](../users-groups-roles/groups-self-service-management.md)
