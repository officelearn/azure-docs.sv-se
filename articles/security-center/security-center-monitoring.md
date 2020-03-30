---
title: Stärka din säkerhetsposition med Azure Security Center | Microsoft-dokument
description: Den här artikeln hjälper dig att stärka din säkerhetsposition genom att övervaka dina resurser i Azure Security Center.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603764"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Förbättra din säkerhetshållning med Azure Security Center
Den här artikeln hjälper dig att stärka din säkerhetsposition. Använd övervakningsfunktionerna i Azure Security Center för att se till att resurssäkerheten är så snäv som möjligt och övervaka efterlevnaden av principer.

## <a name="how-do-you-strengthen-your-security-posture"></a>Hur stärker du din säkerhetsposition?
Ofta tänker vi oss att övervakning är att sitta och titta och vänta på att något ska hända som vi sedan ska åtgärda. Att stärka din säkerhetsposition innebär att ha en proaktiv strategi som granskar dina resurser för att identifiera system som inte uppfyller organisationens standarder eller bästa praxis.

När du har aktiverat [säkerhetsprinciper](tutorial-security-policy.md) för resurser i en prenumeration analyserar Security Center resursernas säkerhet för upptäckt av eventuella säkerhetsrisker. Information om nätverkskonfigurationen är tillgänglig direkt. Beroende på antalet virtuella datorer och datorer som du har med agenten installerad kan det ta en timme eller mer att samla in information om de virtuella datorerna och datorns konfiguration, t.ex. status för säkerhetsuppdatering och operativsystemkonfiguration, så att informationen blir tillgänglig. Du kan visa en fullständig lista över problem och sätt att härda nätverket och åtgärda risker i panelen **Rekommendationer.**

Du kan visa säkerhetstillståndet för dina resurser och eventuella problem per resurstyp:

- Information om hur du övervakar hälsotillståndet för datorresurser och dina appar och får rekommendationer för att förbättra deras säkerhet finns [i Skydda dina datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
- Information om hur du övervakar nätverksresurserna, till exempel virtuella datorer, nätverkssäkerhetsgrupper och slutpunkter, och får rekommendationer för att förbättra deras säkerhet finns [i Skydda nätverket i Azure Security Center](security-center-network-recommendations.md) för mer information. 
- Information om hur du övervakar dina data- och lagringsresurser, till exempel SQL-servrar och lagringskonton, och får rekommendationer för att förbättra deras säkerhet finns [i Skydda Azure SQL-tjänst och data i Azure Security Center](security-center-sql-service-recommendations.md) för mer information. 
- Information om hur du övervakar dina identitets- och åtkomstresurser, inklusive MFA- och kontobehörigheter, och får rekommendationer för att förbättra deras säkerhet finns [i Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md) för mer information. 
- Information om hur du övervakar just-in-time-åtkomst till dina resurser finns [i Hantera åtkomst till virtuella datorer med just-in-time](security-center-just-in-time.md) för mer information. 


Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).



![Panelen resurssäkerhetshälsa](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Se även
I den här artikeln har du fått lära dig hur du använder övervakningsfunktionerna i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md): Här får du lära dig hur du ställer in säkerhetsprinciper i Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.