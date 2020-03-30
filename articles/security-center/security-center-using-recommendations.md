---
title: Använd Azure Security Center-rekommendationer för att öka säkerheten | Microsoft-dokument
description: " Lär dig hur du använder säkerhetsprinciper och rekommendationer i Azure Security Center för att minska en säkerhetsattack. "
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
ms.openlocfilehash: a1034eb47010da2b0e795ee8c79646f06151cac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603276"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Använda Azure Security Center-rekommendationer för att förbättra säkerheten
Du kan minska risken för en betydande säkerhetshändelse genom att konfigurera en säkerhetsprincip och sedan implementera rekommendationerna från Azure Security Center. Den här artikeln visar hur du använder säkerhetsprinciper och rekommendationer i Security Center för att minska en säkerhetsattack. 

Security Center kör automatiskt kontinuerliga genomsökningar för att analysera säkerhetstillståndet för dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer som hjälper dig att konfigurera de nödvändiga säkerhetskontrollerna. Security Center uppdaterar sina rekommendationer inom 24 timmar, med följande undantag:

- Konfigurationsrekommendationer för operativsystemssäkerhet uppdateras inom 48 timmar
- Rekommendationer för slutpunktsskydd uppdateras inom 8 timmar

## <a name="scenario"></a>Scenario
Det här scenariot visar hur du använder Security Center för att minska risken för en säkerhetsincident genom att övervaka Security Center-rekommendationer och vidta åtgärder. Scenariot använder det fiktiva företaget, Contoso och roller som presenteras i [planerings- och driftsguiden för](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)Security Center . I det här fallet fokuserar vi på rollerna för följande personer:

![Scenarioroller](./media/security-center-using-recommendations/scenario-roles.png)

Contoso har nyligen migrerat några av sina lokala resurser till Azure. Contoso vill skydda sina resurser och minska sårbarheten för sina resurser i molnet.

## <a name="use-azure-security-center"></a>Använda Azure Security Center
David, från Contosos IT-säkerhet, har redan valt att gå ombord på Security Center på Contosos prenumerationer på Azure Security Center för att förhindra och identifiera säkerhetsproblem. 

Security Center analyserar automatiskt säkerhetstillståndet för Contosos Azure-resurser och tillämpar standardsäkerhetsprinciper. När Security Center identifierar potentiella säkerhetsproblem skapas **rekommendationer** baserat på de kontroller som anges i säkerhetsprincipen. 

David kör Azure Security-standardnivå, över alla sina prenumerationer för att få alla tillgängliga rekommendationer och säkerhetsfunktioner. Jeff ombordar också alla sina befintliga lokala servrar som ännu inte har migrerats till molnet så att de kan dra nytta av Security Centers hybridsupport över sina [Windows-](quick-onboard-windows-computer.md) och [Linux-servrar.](quick-onboard-linux-computer.md)

Jeff är ägare till molnarbetsbelastning. Jeff ansvarar för att tillämpa säkerhetskontroller i enlighet med Contosos säkerhetsprinciper. 

Jeff utför följande uppgifter:

- Övervaka säkerhetsrekommendationer från Security Center
- Utvärdera säkerhetsrekommendationer och besluta om de ska tillämpa eller avvisa rekommendationerna.
- Tillämpa säkerhetsrekommendationer

### <a name="remediate-threats-using-recommendations"></a>Åtgärda hot med hjälp av rekommendationer
Som en del av deras dagliga övervakningsaktiviteter loggar Jeff in på Azure och öppnar Security Center. 

1. Jeff väljer arbetsbelastningens prenumerationer.

2. Jeff kontrollerar **Secure Score** för att få en helhetsbild av hur säkra prenumerationerna är och ser att poängen är 548.

3. Jeff måste bestämma vilka rekommendationer som ska hanteras först. Så Jeff klickar på Secure Score och börjar hantera rekommendationer baserat på hur mycket det förbättrar hans [Secure Score effekt](security-center-secure-score.md).

4. Eftersom Jeff har massor av anslutna virtuella datorer och servrar, jeff beslutar att fokusera på **Beräkning och apps**.

5. När Jeff klickar på **Beräkning och appar**visas en lista med rekommendationer och hanterar dem enligt effekten Säker poäng.

6. Jeff har många Internet inför virtuella datorer, och eftersom deras portar är utsatta, de är oroliga för att en angripare kan få kontroll över servrarna. Så Jeff väljer att använda [**just-in-time VM-åtkomst**](security-center-just-in-time.md).

Jeff fortsätter att gå igenom högprioriterade och medelprioriterade rekommendationer, och fattar beslut om genomförandet. För varje rekommendation tittar Jeff på den detaljerade informationen från Security Center för att förstå vilka resurser som påverkas, vad secure score-effekten är, vad varje rekommendation innebär och åtgärder för hur du kan minska varje problem.

## <a name="conclusion"></a>Slutsats
Övervakningsrekommendationer i Security Center hjälper dig att eliminera säkerhetsproblem innan en attack inträffar. När du åtgärdar rekommendationer förbättras din säkra poäng och dina arbetsbelastningars säkerhetsposition. Security Center identifierar automatiskt nya resurser som du distribuerar, bedömer dem mot din säkerhetsprinciper och ger nya rekommendationer för att skydda dem.


## <a name="next-steps"></a>Nästa steg
Se till att du har en övervakningsprocess på plats, där du regelbundet kontrollerar rekommendationerna i Security Center så att du kan se till att hålla dina resurser säkra över tid.

Det här scenariot visade hur du använder säkerhetsprinciper och rekommendationer i Security Center för att minska en säkerhetsattack.

Läs om hur du svarar på hot med [att hantera och svara på säkerhetsaviseringar](security-center-managing-and-responding-alerts.md).
