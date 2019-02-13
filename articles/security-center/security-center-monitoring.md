---
title: Förbättra din säkerhetsposition med Azure Security Center | Microsoft Docs
description: Den här artikeln hjälper dig förbättra din säkerhetsposition genom att övervaka dina resurser i Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 28b4667a9ceb4b3534d85ba28668f06c750e22c5
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112265"
---
# <a name="strengthen-your-security-posture-with-azure-security-center"></a>Förbättra din säkerhetsposition med Azure Security Center
Den här artikeln hjälper dig förbättra din säkerhetsposition. Använd övervakningsfunktionerna i Azure Security Center för att säkerställa att din säkerhet är så nära som möjligt och övervaka efterlevnaden med principer.

## <a name="how-do-you-strengthen-your-security-posture"></a>Hur du för att förbättra din säkerhetsposition?
Ofta tänker vi oss att övervakning är att sitta och titta och vänta på att något ska hända som vi sedan ska åtgärda. Stärka din säkerhetsposition handlar om att ha en proaktiv strategi där granskar dina resurser för att identifiera system som inte uppfyller organisationens normer och bästa praxis.

När du har aktiverat [säkerhetsprinciper](tutorial-security-policy.md) för resurser i en prenumeration analyserar Security Center resursernas säkerhet för upptäckt av eventuella säkerhetsrisker. Information om nätverkskonfigurationen är tillgänglig direkt. Beroende på antalet virtuella datorer och datorer som du har med agenten installerad kan det ta en timme eller mer att samla in information om de virtuella datorerna och datorns konfiguration, t.ex. status för säkerhetsuppdatering och operativsystemkonfiguration, så att informationen blir tillgänglig. Du kan visa en fullständig lista över problem och hur du kan förstärka nätverket och åtgärda risker i den **rekommendationer** panelen.

Du kan se säkerhetsstatus för dina resurser och eventuella problem per resurstyp:

- Om du vill övervaka hälsotillståndet för dina datorresurser och dina appar och få rekommendationer för att förbättra deras säkerhet, se [skydda dina datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
- Om du vill övervaka dina nätverksresurser, till exempel virtuella datorer, nätverkssäkerhetsgrupper och slutpunkter, och få rekommendationer för att förbättra deras säkerhet, se [skydda ditt nätverk i Azure Security Center](security-center-network-recommendations.md) mer information. 
- Om du vill övervaka dina data- och lagringsresurser, till exempel SQL-servrar och storage-konton och få rekommendationer för att förbättra deras säkerhet, se [skydda Azure SQL-tjänst och data i Azure Security Center](security-center-sql-service-recommendations.md) för mer information . 
- Om du vill övervaka dina identitets- och resurser, inklusive behörigheter för MFA och konto, och få rekommendationer för att förbättra deras säkerhet, se [övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md) för mer information. 
- För att övervaka precis i tid åtkomst till resurser, se [hantera VM-åtkomst med hjälp av just-in-time](security-center-just-in-time.md) för mer information. 


Mer information om hur du utför rekommendationerna finns i artikeln [Utföra säkerhetsrekommendationerna i Azure Security Center](security-center-recommendations.md).



![Panelen resurssäkerhetshälsa](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>Se också
I den här artikeln har du fått lära dig hur du använder övervakningsfunktionerna i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md): Lär dig hur du konfigurerar säkerhetsinställningar i Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Vanliga frågor om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om att använda tjänsten.
* [Azures Säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
