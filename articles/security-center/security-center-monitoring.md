---
title: Förbättra din säkerhets position med Azure Security Center | Microsoft Docs
description: Den här artikeln hjälper dig att förstärka din säkerhets position genom att övervaka dina resurser i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 40352b7ae1f3fb6b1178f2dfe70cdca9871c76b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77603764"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Förbättra din säkerhetshållning med Azure Security Center
Den här artikeln hjälper dig att förstärka din säkerhets position. Använd övervaknings funktionerna i Azure Security Center för att se till att resurs säkerheten är så tätt som möjligt och övervaka efterlevnaden av principer.

## <a name="how-do-you-strengthen-your-security-posture"></a>Hur stärker du din säkerhets position?
Ofta tänker vi oss att övervakning är att sitta och titta och vänta på att något ska hända som vi sedan ska åtgärda. Att förstärka din säkerhets position syftar på en proaktiv strategi som granskar dina resurser för att identifiera system som inte uppfyller organisationens standarder eller bästa praxis.

När du har aktiverat [säkerhetsprinciper](tutorial-security-policy.md) för resurser i en prenumeration analyserar Security Center resursernas säkerhet för upptäckt av eventuella säkerhetsrisker. Information om nätverkskonfigurationen är tillgänglig direkt. Beroende på antalet virtuella datorer och datorer som du har med agenten installerad kan det ta en timme eller mer att samla in information om de virtuella datorerna och datorns konfiguration, t.ex. status för säkerhetsuppdatering och operativsystemkonfiguration, så att informationen blir tillgänglig. Du kan visa en fullständig lista över problem och sätt att skärpa nätverket och åtgärda risker i rutan **rekommendationer** .

Du kan visa säkerhets status för dina resurser och eventuella problem per resurs typ:

- Om du vill övervaka hälso tillståndet för dina dator resurser och dina appar och få rekommendationer för att förbättra säkerheten, se [skydda dina datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
- För att övervaka dina nätverks resurser, till exempel virtuella datorer, nätverks säkerhets grupper och slut punkter och få rekommendationer för att förbättra säkerheten, se [skydda nätverket i Azure Security Center](security-center-network-recommendations.md) för mer information. 
- För att övervaka dina data och lagrings resurser, till exempel SQL-servrar och lagrings konton, samt få rekommendationer för att förbättra säkerheten, se [skydda Azure SQL-tjänsten och data i Azure Security Center](security-center-sql-service-recommendations.md) för mer information. 
- För att övervaka dina identitets-och åtkomst resurser, inklusive MFA-och konto behörigheter, och få rekommendationer för att förbättra säkerheten, se [övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md) för mer information. 
- Information om hur du övervakar just-in-Time-åtkomst till dina resurser finns i [Hantera åtkomst till virtuella datorer med just-in-Time](security-center-just-in-time.md) för mer information. 


Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).



![Panelen resurssäkerhetshälsa](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Se även
I den här artikeln har du fått lära dig hur du använder övervakningsfunktionerna i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md): Här får du lära dig hur du ställer in säkerhetsprinciper i Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.