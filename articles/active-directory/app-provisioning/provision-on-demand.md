---
title: Etablera en användare på begäran med hjälp av Azure Active Directory
description: Framtvinga synkronisering
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/23/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 78a56b6a848139c47d7934a47decb126afe00b7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85297536"
---
# <a name="on-demand-provisioning"></a>Etablering på begäran
Med etablering på begäran kan du etablera en användare i ett program på några sekunder. Du kan använda funktionen för att snabbt felsöka konfigurations problem, validera uttryck som du har definierat, testa omfångs filter och mycket mer. 

## <a name="how-to-use-on-demand-provisioning"></a>Anvisningar om hur du använder etablering på begäran 

1. Logga in på **Azure Portal**.
2. Navigera till **företags program**.
3. Välj ditt program och gå till etablerings konfigurations sidan.
4. Konfigurera etablering genom att ange autentiseringsuppgifter för din administratör.
5. Klicka på **etablera på begäran**.
6. Sök efter en användare efter förnamn, efter namn, visnings namn, User Principal Name eller e-post.
7. Välj etablera längst ned på sidan.

## <a name="understanding-the-provisioning-steps"></a>Förstå etablerings stegen
Etablerings funktionen på begäran försöker visa de steg som etablerings tjänsten vidtar vid etablering av en användare. Det finns vanligt vis fem steg för att konfigurera en användare, och ett eller flera av stegen nedan visas i etablerings upplevelsen vid behov.

### <a name="step-1-test-connection"></a>Steg 1: Testa anslutning
Etablerings tjänsten försöker auktorisera åtkomst till mål programmet genom att göra en begäran om en "test användare". Etablerings tjänsten förväntar sig ett svar som anger att det har behörighet att fortsätta med etablerings stegen. Det här steget visas bara när det uppstår ett problem i steget. Den visas inte i etablerings upplevelsen på begäran när steget har slutförts. 

**Felsökningstips**
* Se till att du har angett giltiga autentiseringsuppgifter för mål programmet, till exempel den hemliga token och klient-URL: en. De autentiseringsuppgifter som krävs varierar beroende på program. Detaljerade konfigurations guider finns [här](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list). 
* Se till att mål programmet stöder filtrering av matchande attribut som definierats i bladet mappningar för attribut. Du kan behöva kontrol lera API-dokumentationen från programutvecklaren för att förstå de filter som de stöder.  
* För SCIM-program kan du använda ett verktyg som Postman för att se till att programmet svarar på auktoriserings begär anden när Azure AD Provisioning-tjänsten förväntar sig. En exempel förfrågan hittar du [här](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#request-3).

### <a name="step-2-import-user"></a>Steg 2: importera användare
Därefter hämtar etablerings tjänsten användaren från käll systemet. Användarattribut som tjänsten hämtar används senare för att utvärdera om användaren är omfånget för etablering, kontroll av mål systemet för en befintlig användare och för att fastställa vilka användarattribut som ska exporteras till mål systemet. 

**Visa detaljer**

I avsnittet Visa information visas egenskaperna för den användare som har importer ATS från käll systemet (t. ex. Azure AD).

**Felsökningstips**
* Det går inte att importera användaren när det matchande attributet saknas i användarobjektet i käll systemet. Du kan lösa det här felet genom att uppdatera användarobjektet med ett värde för det matchande attributet eller ändra det matchande attributet i etablerings konfigurationen.  
* Om ett attribut som du förväntar dig saknas i listan som har importer ATS, kontrollerar du att attributet har ett värde för användarobjektet i käll systemet. Etablerings tjänsten stöder för närvarande inte etablering av null-attribut. 
* Se till att sidan för etablering av konfigurations attribut innehåller attributet som du förväntar dig. 

### <a name="step-3-determine-if-user-is-in-scope"></a>Steg 3: kontrol lera om användaren är inom omfånget
Sedan fastställer etablerings tjänsten om användaren är inom [omfånget](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#scoping) för etablering. Tjänsten tar hänsyn till aspekter, till exempel om användaren är tilldelad till programmet, om omfattningen är inställd på synkronisera tilldelad eller synkronisera alla, och de omfångs filter som definierats i etablerings konfigurationen.  

**Visa detaljer**

I avsnittet Visa information visas de omfångs villkor som utvärderades. Du kan se en av följande egenskaper:
* **Aktiv i käll systemet** anger att användaren har egenskapen aktive rad till sant i Azure AD.
* **Tilldelat till program** anger att användaren är tilldelad till programmet i Azure AD
* **Omfångs synkronisering alla** anger att inställningen omfång tillåter alla användare och grupper i klienten.
* **Användaren har en obligatorisk roll** indikerar att användaren har de roller som krävs för att kunna tillhandahållas i programmet. 
* **Omfångs filter** visas också om du har definierat omfångs filter för ditt program. Filtret visas med följande format: {scope filter title} {scope filter-attribut} {scope filter operator} {omfångs filter värde}. 

**Felsökningstips**
* Se till att du har definierat en giltig omfattnings roll. Undvik till exempel att använda operatorn ["större än"](https://docs.microsoft.com/azure/active-directory/app-provisioning/define-conditional-rules-for-provisioning-user-accounts#create-a-scoping-filter) med ett värde som inte är heltal. 
* Läs igenom tipsen som beskrivs [här](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned#provisioning-users-assigned-to-the-default-access-role)om användaren inte har den roll som krävs. 

### <a name="step-4-match-user-between-source-and-target"></a>Steg 4: matcha användare mellan källa och mål
I det här steget. tjänsten försöker matcha den användare som hämtades i import steget med en användare i mål systemet. 

**Visa detaljer**

På sidan Visa information visas egenskaperna för den eller de användare som matchades i mål systemet. De egenskaper som visas i kontext fönstret varierar på följande sätt:
* Om det inte finns några matchade användare i mål systemet ser du inga egenskaper.
* Om en användare matchas i mål systemet visas egenskaperna för den matchade användaren från mål systemet.
* Om flera användare matchas visas egenskaperna för båda matchade användare.
* Om flera matchande attribut är en del av dina mappningar för attribut utvärderas varje matchande attribut sekventiellt och de matchade användarna visas. 

**Fel söknings information**
* Etablerings tjänsten kan inte unikt matcha en användare i källan med en användare i målet. Detta kan lösas genom att se till att det matchande attributet är unikt. 
* Kontrol lera att mål programmet stöder filtrering av attributet som har definierats som matchande attribut.  

### <a name="step-5-perform-action"></a>Steg 5: utför åtgärd
Slutligen vidtar etablerings tjänsten en åtgärd som att skapa, uppdatera, ta bort eller hoppa över användaren. 

**Visa detaljer**

I avsnittet Visa information visas de attribut som har ändrats i mål programmet. Detta representerar den slutliga utmatningen av etablerings tjänstens aktivitet och de attribut som exporterades. Om det här steget Miss lyckas representerar de attribut som visas de attribut som etablerings tjänsten försökte ändra.  

**Felsökningstips**
* Felen för att exportera ändringar kan variera mycket. Se [dokumentationen](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs#error-codes) för etablerings loggar för vanliga haverier.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
**Behöver du inaktivera etablering för att använda etablering på begäran?** Inga ytterligare steg krävs för program som använder sig av en token med lång livs längd eller användar namn och lösen ord för auktorisering. Program som använder OAuth för auktorisering kräver för närvarande att etablerings jobbet stoppas innan du använder etablering på begäran. Program som G Suite, Box, arbets yta efter Facebook och slack omfattas av den här kategorin. Arbetet pågår för att tillåta att du kör etablering på begäran för alla program utan att behöva avbryta etableringen. 

**Hur lång tid tar etableringen på begäran?** Det tar vanligt vis mindre än 30 sekunder. 

## <a name="known-limitations"></a>Kända begränsningar
Det finns några kända begränsningar idag. Publicera på [UserVoice](https://aka.ms/appprovisioningfeaturerequest) så att vi bättre kan prioritera vilka förbättringar som ska utföras härnäst. Observera att de här begränsningarna är begränsade till etablerings funktionen på begäran. Mer information om hur ett program stöder etablering av grupper, borttagningar osv. finns i själv studie kursen om program. 

* Programmen Workday, AWS och SuccessFactors har inte stöd för etablering på begäran.
* Etablering av grupper och roller på begäran stöds inte.
* Det finns inte stöd för att inaktivera eller ta bort användare och grupper.

## <a name="next-steps"></a>Nästa steg

* [Felsöka etablering](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem)
