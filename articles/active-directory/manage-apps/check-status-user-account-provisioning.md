---
title: Rapportera automatisk användar konto etablering till SaaS-program | Microsoft Docs
description: Lär dig hur du kontrollerar status för automatiska etablerings jobb för användar konton och hur du felsöker etablering av enskilda användare.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda7654ca2d825ae4112dd06021c7e83ed6867cd
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381258"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Självstudier: Rapportering om automatisk etablering av användar konto

Azure Active Directory (Azure AD) innehåller en [tjänst för användar konto etablering](user-provisioning.md) som hjälper till att automatisera etableringen av inetablering av användar konton i SaaS-appar och andra system, i syfte att hantera livs cykel hantering från slut punkt till slut punkt. Azure AD stöder för hands integrerade användar etablerings anslutningar för alla program och system i avsnittet "aktuella" i [program galleriet för Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

Den här artikeln beskriver hur du kontrollerar status för etablerings jobb när de har kon figurer ATS och hur du felsöker etablering av enskilda användare och grupper.

## <a name="overview"></a>Översikt

Etablerings anslutningar konfigureras och konfigureras med hjälp av [Azure Portal](https://portal.azure.com), genom att följa den [angivna dokumentationen](../saas-apps/tutorial-list.md) för programmet som stöds. När du har konfigurerat och kört kan etablerings jobb rapporteras på ett av två sätt:

* **Azure Portal** – den här artikeln beskriver främst hämtning av rapportinformation från [Azure Portal](https://portal.azure.com), vilket ger både en sammanfattnings rapport för etablering och detaljerade etablerings loggar för ett angivet program.
* **Gransknings-API** – Azure Active Directory innehåller också ett gransknings-API som möjliggör program mässig hämtning av detaljerade gransknings loggar för etablering. Se [Azure Active Directory audit API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) för dokumentation som är särskilt för att använda detta API. Även om den här artikeln inte särskilt beskriver hur du använder API: et, innehåller den information om vilka typer av etablerings händelser som registreras i gransknings loggen.

### <a name="definitions"></a>Definitioner

I den här artikeln används följande villkor, som definieras nedan:

* **Käll system** – databasen med användare som Azure AD Provisioning-tjänsten synkroniserar från. Azure Active Directory är käll systemet för merparten av förintegrerade etablerings anslutningar, men det finns vissa undantag (exempel: Inkommande Workday-synkronisering).
* **Mål system** – databasen med användare som Azure AD Provisioning-tjänsten synkroniserar till. Detta är vanligt vis ett SaaS-program (exempel: Salesforce, ServiceNow, G Suite, Dropbox for Business), men i vissa fall kan ett lokalt system, till exempel Active Directory (exempel: Inkommande Workday-synkronisering till Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Hämtar etablerings rapporter från Azure Portal

Starta genom att starta [Azure Portal](https://portal.azure.com) och bläddra till företags programmet för vilken etablering har kon figurer ATS för att få information om etablerings rapporter för ett angivet program. Om du till exempel konfigurerar användare till LinkedIn-höjning är navigerings Sök vägen till program informationen:

**Azure Active Directory > företags program > alla program > LinkedIn-höjning**

Härifrån kan du komma åt både etablerings sammanfattnings rapporten och etablerings loggarna som beskrivs nedan.

## <a name="provisioning-summary-report"></a>Sammanfattnings rapport för etablering

Sammanfattnings rapporten för etablering visas på fliken **etablering** för angivet program. Den finns i avsnittet **information om synkronisering** under **Inställningar**och innehåller följande information:

* Det totala antalet användare och/grupper som har synkroniserats och som för närvarande ingår i omfånget för etablering mellan käll systemet och mål systemet.
* Den senaste gången som synkroniseringen kördes. Synkroniseringar sker vanligt vis var 20-40 minut, efter att en [inledande synkronisering](user-provisioning.md#what-happens-during-provisioning) har slutförts.
* Huruvida en [inledande synkronisering](user-provisioning.md#what-happens-during-provisioning) har slutförts eller inte.
* Huruvida etablerings processen har placerats i karantän och vad orsaken till karantän status är (till exempel fel vid kommunikation med mål systemet på grund av ogiltiga autentiseringsuppgifter för admin).

Sammanfattnings rapporten för etablering bör vara den första plats administratören ser för att kontrol lera det operativa hälso tillståndet för etablerings jobbet.

 ![Sammanfattningsrapport](./media/check-status-user-account-provisioning/summary_report.PNG)

## <a name="provisioning-audit-logs"></a>Etablering av gransknings loggar

Alla aktiviteter som utförs av etablerings tjänsten registreras i gransknings loggarna i Azure AD, som kan visas på fliken **gransknings loggar** under kategorin **konto etablering** . Händelse typer för loggade aktiviteter är:

* **Importera händelser** – händelsen "Importera" registreras varje gången Azure AD Provisioning-tjänsten hämtar information om en enskild användare eller grupp från ett käll system eller mål system. Under synkroniseringen hämtas användare från käll systemet först, med de resultat som registrerats som "Importera"-händelser. De matchande ID: na för de hämtade användarna frågas sedan mot mål systemet för att kontrol lera om de finns, med resultaten även registrerade som "Importera"-händelser. Dessa händelser registrerar alla mappade användarattribut och deras värden som visades i Azure AD Provisioning-tjänsten vid tidpunkten för händelsen.
* **Händelser för synkroniseringsregeln** – dessa händelser rapporterar om resultatet av reglerna för attribut mappning och eventuella konfigurerade omfångs filter, efter att användar data har importer ATS och utvärderats från käll-och mål systemen. Om en användare i ett käll system till exempel anses vara inom omfånget för etablering och anses saknas i mål systemet, registrerar den här händelsen att användaren kommer att tillhandahållas i mål systemet.
* **Exportera händelser** – en "export"-händelse registreras varje gången Azure AD Provisioning-tjänsten skriver ett användar konto eller grupp objekt till ett mål system. Dessa händelser registrerar alla användarattribut och deras värden som har skrivits av Azure AD Provisioning-tjänsten vid tidpunkten för händelsen. Om ett fel uppstod när användar kontot eller grupp objekt skrevs till mål systemet visas det här.
* **Process depositions Events** -process escrows inträffar när etablerings tjänsten påträffar ett fel vid försök att utföra en åtgärd och börjar försöka utföra åtgärden på nytt under ett tidsintervall. En "depositions"-händelse registreras varje gången en etablerings åtgärd gjordes om.

När du tittar på etablering av händelser för en enskild användare inträffar händelserna vanligt vis i den här ordningen:

1. Import händelse: Användaren hämtas från käll systemet.
1. Import händelse: Mål systemet har frågor om att söka efter förekomsten av den hämtade användaren.
1. Regel för Synkroniseringsregel: Användar data från käll-och mål system utvärderas mot de konfigurerade reglerna för attribut mappning och omfångs filter för att fastställa vilka åtgärder som ska utföras.
1. Export händelse: Om den Synkroniseringsregel som styrs av en åtgärd som ska utföras (Lägg till, uppdatera, ta bort), registreras resultatet av åtgärden i en export händelse.

   ![Exempel: Gransknings logg sida som visar aktiviteter och status](./media/check-status-user-account-provisioning/audit_logs.PNG)

### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Leta upp etablerings händelser för en speciell användare

Det vanligaste användnings fallet för etablerings gransknings loggarna är att kontrol lera etablerings statusen för ett enskilt användar konto. Så här söker du efter de senaste etablerings händelserna för en speciell användare:

1. Gå till avsnittet **gransknings loggar** .
1. Välj **konto etablering**på menyn **kategori** .
1. På menyn **datum intervall** väljer du det datum intervall som du vill söka.
1. I **Sök** fältet anger du användar-ID för den användare som du vill söka efter. Formatet på ID-värdet ska matcha det som du har valt som primärt matchnings-ID i konfigurationen för attributmappning (till exempel userPrincipalName eller anställnings-ID-nummer). Det ID-värde som krävs visas i kolumnen mål (er).
1. Tryck på RETUR för att söka. De senaste etablerings händelserna kommer att returneras först.
1. Om händelser returneras noterar du aktivitets typerna och om de lyckades eller misslyckades. Om inga resultat returneras innebär det att användaren antingen inte finns eller inte har identifierats av etablerings processen ännu, om en fullständig synkronisering ännu inte har slutförts.
1. Klicka på enskilda händelser om du vill visa utökad information, inklusive alla användar egenskaper som hämtats, utvärderats eller skrivits som en del av händelsen.

En demonstration om hur du använder gransknings loggarna finns i videon nedan. Gransknings loggarna presenteras runt 5:30-märket:

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Tips för att Visa gransknings loggar för etablering

För bästa läsbarhet i Azure Portal väljer du knappen **kolumner** och väljer följande kolumner:

* **Datum** – visar det datum då händelsen inträffade.
* **Mål (er)** – visar appens namn och användar-ID som är ämne för händelsen.
* **Aktivitet** – aktivitets typ, enligt beskrivningen ovan.
* **Status** – om händelsen lyckades eller inte.
* **Status orsak** – en sammanfattning av vad som hände i etablerings händelsen.

## <a name="troubleshooting"></a>Felsökning

Sammanfattnings rapporten för etablering och gransknings loggar spelar en nyckel roll som hjälper administratörer att felsöka olika problem med etablering av användar konto.

För scenariobaserade vägledning om hur du felsöker automatisk användar etablering, se problem med [att konfigurera och konfigurera användare i ett program](application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](what-is-single-sign-on.md)
