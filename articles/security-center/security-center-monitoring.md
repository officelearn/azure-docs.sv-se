---
title: Resource security hygien i Azure Security Center | Microsoft Docs
description: Den här artikeln hjälper dig att komma igång med övervakning av din resurs security hygien i Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/21/2018
ms.author: rkarlin
ms.openlocfilehash: 361fc2c6931dcfaed062bd39b75be7bef059b467
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122082"
---
# <a name="monitoring-resource-security-hygiene-in-azure-security-center"></a>Övervaka resource security hygien i Azure Security Center
Den här artikeln får du använder övervakningsfunktionerna i Azure Security Center för att säkerställa att din säkerhet är så nära som möjligt och övervaka efterlevnaden med principer.

## <a name="what-is-resource-security-hygiene"></a>Vad är resource security hygien?
Ofta tänker vi oss att övervakning är att sitta och titta och vänta på att något ska hända som vi sedan ska åtgärda. Resource security hygien avser ha en proaktiv strategi som granskar dina resurser för att identifiera system som inte uppfyller organisationens normer och bästa praxis.

## <a name="resource-security-hygiene"></a>Resource security hygien
När du har aktiverat [säkerhetsprinciper](security-center-policies.md) för resurser i en prenumeration analyserar Security Center resursernas säkerhet för upptäckt av eventuella säkerhetsrisker. Information om nätverkskonfigurationen är tillgänglig direkt. Beroende på antalet virtuella datorer och datorer som du har med agenten installerad kan det ta en timme eller mer att samla in information om de virtuella datorerna och datorns konfiguration, t.ex. status för säkerhetsuppdatering och operativsystemkonfiguration, så att informationen blir tillgänglig. Du kan visa en fullständig lista över problem och hur du kan förstärka nätverket och åtgärda risker i den **rekommendationer** panelen.

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

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md): Här får du lära dig hur du ställer in säkerhetsprinciper i Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
