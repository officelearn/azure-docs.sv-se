---
title: Hotinformationsrapporter i Azure Security Center | Microsoft Docs
description: På den här sidan får du hjälp att använda Azure Security Center hot informations rapporter under en undersökning för att hitta mer information om säkerhets aviseringar
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: a4fdbab4a69fac1376779f37d5fa69fef587bf52
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84888252"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Azure Security Center hot informations rapport

På den här sidan förklaras hur Azure Security Centers hot informations rapporter kan hjälpa dig att lära dig mer om ett hot som utlöser en säkerhets avisering.


## <a name="what-is-a-threat-intelligence-report"></a>Vad är en hotinformationsrapport?

Security Center hot skydd fungerar genom att övervaka säkerhets information från dina Azure-resurser, nätverket och anslutna partner lösningar. Tjänsten analyserar den här informationen, och korrelerar ofta information från flera källor för att identifiera hot. Mer information finns i [hur Azure Security Center identifierar och svarar på hot](security-center-alerts-overview.md#detect-threats).

När Security Center identifierar ett hot utlöser det en [säkerhets avisering](security-center-managing-and-responding-alerts.md)som innehåller detaljerad information om händelsen, inklusive förslag på åtgärder. För att hjälpa incident hanterings team att undersöka och åtgärda hot, ger Security Center Hot information-rapporter som innehåller information om identifierade hot. Rapporten innehåller information som:

* Angriparens identitet eller associationer (om den här informationen är tillgänglig)
* Angriparens mål
* Aktuella och historiska attackkampanjer (om den här informationen är tillgänglig)
* Angripares taktiker, verktyg och procedurer
* Associerade indikatorer för kompromettering, till exempel URL:er och filhashvärden
* Viktimologi, vilket syftar på information om utbredningen inom en bransch och geografisk region som kan hjälpa dig att avgöra om dina Azure-resurser är utsatta för risk
* Information om rekommenderade åtgärder

> [!NOTE]
> Mängden information i en viss rapport varierar. Detaljnivån beror på den skadliga kodens aktivitet och spridning.

Security Center tillhandahåller tre typer av hotrapporter, som kan variera beroende på attacken. De tillgängliga rapporterna är:

* **Aktivitets grupps rapport**: ger djup dykningar till angripare, deras mål och taktiker.
* **Kampanjrapport**: Fokuserar på information om specifika attackkampanjer.
* **Hotsammanfattningsrapport**: Omfattar alla objekt i de föregående två rapporterna.

Den här typen av information är användbar under incident svars processen, där det finns en pågående undersökning för att förstå Angreppets källa, angriparens motivation och vad du behöver göra för att undvika det här problemet i framtiden.



## <a name="how-to-access-the-threat-intelligence-report"></a>Hur kommer du åt hotinformationsrapporten?

1. Från Security Centers sid panelen öppnar du sidan **säkerhets aviseringar** .
1. Välj en avisering. 
    Sidan aviserings information öppnas med mer information om aviseringen. Nedan visas sidan med **Utpressnings** bara aviserings information.

    [![Aviserings information om utpressnings visare identifierad](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. Välj länken till rapporten så öppnas en PDF-fil i din standard webbläsare.

    [![Informations sidan om potentiellt osäker åtgärds avisering](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    Du kan också hämta PDF-rapporten. 

    >[!TIP]
    > Mängden information som är tillgänglig för varje säkerhetsvarning varierar beroende på typen av avisering.



## <a name="next-steps"></a>Nästa steg

Den här sidan förklaras hur du öppnar hot informations rapporter när du undersöker säkerhets aviseringar. Relaterad information finns på följande sidor:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md). Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Hantera säkerhets incidenter i Azure Security Center](security-center-incident.md)