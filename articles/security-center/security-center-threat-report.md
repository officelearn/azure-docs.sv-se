---
title: Hotinformationsrapporter i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver hur du använder hotinformationsrapporter i Azure Security Center i samband med en undersökning för att få fram mer information om en säkerhetsvarning.
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
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: f8b4063d87fa9a89dccd42eddea644609bd6ff27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921257"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Hotinformationsrapporter i Azure Security Center
Det här dokumentet beskriver hur du kan lära dig mer om ett hot som genererat en säkerhetsvarning med hjälp av hotinformationsrapporter i Azure Security Center.

## <a name="what-is-a-threat-intelligence-report"></a>Vad är en hotinformationsrapport?
Skydd av hothantering i Security Center fungerar genom att övervaka säkerhetsinformation från dina Azure-resurser, nätverket och anslutna partnerlösningar. Tjänsten analyserar den här informationen, och korrelerar ofta information från flera källor för att identifiera hot. Mer information finns i [Hur Azure Security Center identifierar och svarar på hot](security-center-alerts-overview.md#detect-threats).

När Security Center identifierar ett hot utlöses en [säkerhetsvarning](security-center-managing-and-responding-alerts.md), som innehåller detaljerad information om en viss händelse, inklusive förslag på åtgärder. För att hjälpa incidenthanteringsgrupper, undersöka och åtgärda hot, innehåller Security Center en rapport om hotinformation som innehåller information om hotet som upptäcktes, inklusive information som:

* Angriparens identitet eller associationer (om den här informationen är tillgänglig)
* Angriparens mål
* Aktuella och historiska attackkampanjer (om den här informationen är tillgänglig)
* Angriparnas taktik, verktyg och procedurer
* Associerade indikatorer för kompromettering, till exempel URL:er och filhashvärden
* Viktimologi, vilket syftar på information om utbredningen inom en bransch och geografisk region som kan hjälpa dig att avgöra om dina Azure-resurser är utsatta för risk
* Information om rekommenderade åtgärder

> [!NOTE]
> Mängden information i en viss rapport varierar. Detaljnivån beror på den skadliga kodens aktivitet och spridning.
>
>

Security Center tillhandahåller tre typer av hotrapporter, som kan variera beroende på attacken. De tillgängliga rapporterna är:

* **Aktivitetsgruppsrapport**: Tillhandahåller detaljerad information om angripare, deras mål och metoder.
* **Kampanjrapport**: Fokuserar på information om specifika attackkampanjer.
* **Hotsammanfattningsrapport**: Omfattar alla objekt i de föregående två rapporterna.

Den här typen av information är användbar under incidentsvarsprocessen, där det pågår en utredning för att förstå källan till attacken, angriparens motiv och vad du ska göra för att minska problemet framåt.

## <a name="how-to-access-the-threat-intelligence-report"></a>Hur kommer du åt hotinformationsrapporten?
Du kan se aktuella aviseringar i rutan **Security alerts (Säkerhetsaviseringar)**. Öppna Azure-portalen och följ stegen nedan för att se mer information om varje avisering:

1. På instrumentpanelen i Security Center hittar du rutan **Security alerts (Säkerhetsaviseringar)**.
2. Klicka på ikonen för att öppna bladet **Säkerhetsvarningar** som innehåller mer information om aviseringarna och klicka sedan på den säkerhetsvarning som du vill visa mer information om.

    ![Säkerhetsaviseringar](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. I det här fallet visar det **misstänkta bearbetade utförda** bladet information om aviseringen enligt bilden nedan:

    ![Utförlig information om säkerhetsaviseringar](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. Mängden information som är tillgänglig för varje säkerhetsvarning varierar beroende på typen av avisering. I fältet **RAPPORTER** har du en länk till rapporten om hotinformation. Klicka på länken så öppnas ett nytt webbläsarfönster med en PDF-fil.

   ![Val av lagringsutrymme](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Härifrån kan du hämta PDF-filen för den här rapporten och läsa mer om säkerhetsproblemet som har identifierats samt vidta åtgärder baserat på informationen.

## <a name="see-also"></a>Se även
I det här dokumentet har vi tittat på hur hotinformationsrapporterna i Azure Security Center kan hjälpa dig när du undersöker säkerhetsaviseringar. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Planerings- och driftsguide för Azure Security Center](security-center-planning-and-operations-guide.md). Lär dig mer om planering och viktiga designaspekter när du ska börja använda Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md). Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Hantera säkerhetsincidenter i Azure Security Center](security-center-incident.md)