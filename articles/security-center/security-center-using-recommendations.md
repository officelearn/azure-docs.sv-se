---
title: Använd Azure Security Center rekommendationer för att förbättra säkerheten | Microsoft Docs
description: " Lär dig hur du använder säkerhets principer och rekommendationer i Azure Security Center för att minimera en säkerhets attack. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 4c6b8b0bfa78dca5ca32c8c72edcc463ebb9057a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322146"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Använda Azure Security Center-rekommendationer för att förbättra säkerheten

Du kan minska risken för en betydande säkerhets händelse genom att konfigurera en säkerhets princip och sedan implementera de rekommendationer som tillhandahålls av Azure Security Center. Den här artikeln visar hur du använder säkerhets principer och rekommendationer i Security Center för att minimera en säkerhets attack. 

Security Center kör automatiskt kontinuerliga genomsökningar för att analysera säkerhets statusen för dina Azure-resurser. När Security Center identifierar potentiella säkerhets problem skapas rekommendationer som vägleder dig genom processen med att konfigurera de säkerhets kontroller som krävs. Security Center uppdaterar sina rekommendationer inom 24 timmar, med följande undantag:

- Rekommendationer för säkerhets konfiguration av operativ system uppdateras inom 48 timmar
- Rekommendationer för Endpoint Protection problem uppdateras inom 8 timmar

## <a name="scenario"></a>Scenario
Det här scenariot visar hur du använder Security Center för att minska risken för en säkerhets incident genom att övervaka Security Center rekommendationer och vidta åtgärder. Scenariot använder det fiktiva företaget, Contoso och roller som presenteras i Security Center [planerings-och drift guide](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). I det här scenariot fokuserar vi på rollerna för följande personer:

![Scenario roller](./media/security-center-using-recommendations/scenario-roles.png)

Contoso migrerade nyligen några av sina lokala resurser till Azure. Contoso vill skydda sina resurser och minska risken för deras resurser i molnet.

## <a name="use-azure-security-center"></a>Använda Azure Security Center
David, från Contosos IT-säkerhet, har redan valt att publicera Security Center på Contosos prenumerationer för att Azure Security Center för att förhindra och identifiera säkerhets problem. 

Security Center analyserar automatiskt säkerhets läget för Contosos Azure-resurser och tillämpar standard säkerhets principer. När Security Center identifierar potentiella säkerhets problem skapas **rekommendationer** baserat på de kontroller som anges i säkerhets principen. 

David kör Azure-säkerhet med Azure Defender aktiverat, över alla prenumerationer för att få en fullständig uppsättning rekommendationer och säkerhetsfunktioner. Jeff registrerar också alla befintliga lokala servrar som ännu inte har migrerats till molnet så att de kan dra nytta av Security Center hybrid support över sina [Windows-och Linux-servrar](quickstart-onboard-machines.md).

Jeff är en moln arbets belastnings ägare. Jeff ansvarar för att tillämpa säkerhets kontroller i enlighet med Contosos säkerhets principer. 

Jeff utför följande uppgifter:

- Övervaka säkerhets rekommendationer som tillhandahålls av Security Center
- Utvärdera säkerhets rekommendationer och bestäm om de ska använda eller stänga av rekommendationerna.
- Tillämpa säkerhets rekommendationer

### <a name="remediate-threats-using-recommendations"></a>Åtgärda hot med hjälp av rekommendationer
Som en del av de dagliga övervaknings aktiviteterna loggar Jeff in på Azure och öppnar Security Center. 

1. Jeff väljer arbets Belastningens prenumerationer.

2. Jeff kontrollerar de **säkra poängen** för att få en övergripande bild av hur säkra prenumerationerna är och ser att poängen är 548.

3. Jeff måste bestämma vilka rekommendationer som ska hanteras först. Jeff klickar på säkra poäng och börjar hantera rekommendationer baserat på hur mycket det förbättrar sina [säkra Poäng](secure-score-security-controls.md).

4. Eftersom Jeff har många anslutna virtuella datorer, bestämmer Jeff att fokusera på sina datorer i [till gångs inventering](asset-inventory.md).

5. När Jeff öppnar till gångs inventeringen visas en lista över rekommendationer. Jeff hanterar dem enligt den säkra poängen.

6. Jeff har flera virtuella datorer som är riktade mot Internet och eftersom deras portar exponeras, så är de oroar att en angripare kan få kontroll över servrarna. Så Jeff väljer att använda [**just-in-Time VM-åtkomst**](security-center-just-in-time.md).

Jeff fortsätter att gå igenom rekommendationerna med hög prioritet och medelhög prioritet och fatta beslut om implementeringen. För varje rekommendation tittar Jeff på den detaljerade informationen som tillhandahålls av Security Center för att förstå vilka resurser som påverkas, vad den säkra poängen påverkar, vad varje rekommendation innebär och åtgärder för att minimera varje problem.

### <a name="enforce-recommendations-to-prevent-security-misconfigurations"></a>Använd rekommendationer för att förhindra felaktig säkerhets konfiguration

För att se till att användarna inte skapar resurser som har negativ inverkan på Jeffs poäng, konfigurerar de alternativen Genomdriv och neka på de rekommendationer som är viktigast för dem. Läs mer i [förhindra felaktig konfiguration med tvinga/neka-rekommendationer](prevent-misconfigurations.md).


## <a name="conclusion"></a>Slutsats
Övervaknings rekommendationer i Security Center hjälper dig att undvika säkerhets risker innan ett angrepp uppstår. När du reparerar rekommendationer kan du förbättra dina säkra poäng och dina arbets belastningar position. Security Center identifierar automatiskt nya resurser som du distribuerar, utvärderar dem mot din säkerhets princip och ger nya rekommendationer för att skydda dem.


## <a name="next-steps"></a>Nästa steg
Se till att du har en övervaknings process på plats, där du regelbundet kontrollerar rekommendationerna i Security Center så att du kan se till att dina resurser är säkra över tid.

I det här scenariot visar vi hur du använder säkerhets principer och rekommendationer i Security Center för att minimera en säkerhets attack.

Lär dig hur du svarar på hot med [att hantera och svara på säkerhets aviseringar](security-center-managing-and-responding-alerts.md).
