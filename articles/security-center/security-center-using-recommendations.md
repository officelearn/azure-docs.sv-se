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
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: 5b496523f6205532ea7a4278671e3be6986935a7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355287"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Använd Azure Security Center rekommendationer för att förbättra säkerheten
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

David kör Azure Security Standard-nivån, över alla prenumerationer för att få en fullständig uppsättning rekommendationer och säkerhetsfunktioner. Jeff registrerar också alla befintliga lokala servrar som ännu inte har migrerats till molnet så att de kan dra nytta av Security Center hybrid support över sina [Windows](quick-onboard-windows-computer.md) -och [Linux](quick-onboard-linux-computer.md) -servrar.

Jeff är en moln arbets belastnings ägare. Jeff ansvarar för att tillämpa säkerhets kontroller i enlighet med Contosos säkerhets principer. 

Jeff utför följande uppgifter:

- Övervaka säkerhets rekommendationer som tillhandahålls av Security Center
- Utvärdera säkerhets rekommendationer och bestäm om de ska använda eller stänga av rekommendationerna.
- Tillämpa säkerhets rekommendationer

### <a name="remediate-threats-using-recommendations"></a>Åtgärda hot med hjälp av rekommendationer
Som en del av de dagliga övervaknings aktiviteterna loggar Jeff in på Azure och öppnar Security Center. 

1. Jeff väljer arbets Belastningens prenumerationer.

2. Jeff kontrollerar de **säkra poängen** för att få en övergripande bild av hur säkra prenumerationerna är och ser att poängen är 548.

3. Jeff måste bestämma vilka rekommendationer som ska hanteras först. Så Jeff klickar du på säkra poäng och börjar hantera rekommendationer baserat på hur mycket det förbättrar sin [säkra Poäng påverkan](security-center-secure-score.md).

4. Eftersom Jeff har många anslutna virtuella datorer och servrar, bestämmer Jeff att fokusera på data **behandling och appar**.

5. När Jeff klickar på **Compute och appar**, ser de en lista över rekommendationer och hanterar dem enligt den säkra Poäng effekten.

6. Jeff har flera virtuella datorer som är riktade mot Internet och eftersom deras portar exponeras, så är de oroar att en angripare kan få kontroll över servrarna. Så Jeff väljer att använda [**just-in-Time VM-åtkomst**](security-center-just-in-time.md).

Jeff fortsätter att gå igenom rekommendationerna med hög prioritet och medelhög prioritet och fatta beslut om implementeringen. För varje rekommendation tittar Jeff på den detaljerade informationen som tillhandahålls av Security Center för att förstå vilka resurser som påverkas, vad den säkra poängen påverkar, vad varje rekommendation innebär och åtgärder för att minimera varje problem.

## <a name="conclusion"></a>Slutsats
Övervaknings rekommendationer i Security Center hjälper dig att undvika säkerhets risker innan ett angrepp uppstår. När du reparerar rekommendationer kan du förbättra dina säkra poäng och dina arbets belastningar position. Security Center identifierar automatiskt nya resurser som du distribuerar, utvärderar dem mot din säkerhets princip och ger nya rekommendationer för att skydda dem.


## <a name="next-steps"></a>Nästa steg
Se till att du har en övervaknings process på plats, där du regelbundet kontrollerar rekommendationerna i Security Center så att du kan se till att dina resurser är säkra över tid.

I det här scenariot visar vi hur du använder säkerhets principer och rekommendationer i Security Center för att minimera en säkerhets attack.

Lär dig hur du svarar på hot med [att hantera och svara på säkerhets aviseringar](security-center-managing-and-responding-alerts.md).
