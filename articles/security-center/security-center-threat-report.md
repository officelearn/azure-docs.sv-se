---
title: Hotinformationsrapporter i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver hur du använder hotinformationsrapporter i Azure Security Center i samband med en undersökning för att få fram mer information om en säkerhetsvarning.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: ba5ab7ce85933545a41f23e2ecd913acbb7e72d1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112741"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Hotinformationsrapporter i Azure Security Center
Det här dokumentet beskriver hur du kan lära dig mer om ett hot som genererat en säkerhetsvarning med hjälp av hotinformationsrapporter i Azure Security Center.

## <a name="what-is-a-threat-intelligence-report"></a>Vad är en hotinformationsrapport?
Hotidentifieringen i Security Center sker genom övervakning av säkerhetsinformation från dina Azure-resurser, nätverket och anslutna partnerlösningar. Tjänsten analyserar den här informationen, och korrelerar ofta information från flera källor för att identifiera hot. Den här processen är en del av [identifieringsfunktionerna](security-center-detection-capabilities.md) i Security Center.

När Security Center identifierar ett hot utlöses en [säkerhetsvarning](security-center-managing-and-responding-alerts.md), som innehåller detaljerad information om en viss händelse, inklusive förslag på åtgärder. För att hjälpa incidenter team, undersöka och åtgärda hot, Security Center innehåller en hotinformationsrapport som innehåller information om hotet som har identifierats, inklusive information som den:

* Angriparens identitet eller associationer (om den här informationen är tillgänglig)
* Angriparens mål
* Aktuella och historiska attackkampanjer (om den här informationen är tillgänglig)
* Angriparens metoder, verktyg och procedurer
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

Den här typen av information är användbart vid de [incidenthantering](security-center-incident-response.md) processer, där det finns en pågående undersökning för att förstå orsaken angreppet, angriparens avsikter och vad du gör för att lösa problemet kan åtgärdas .

## <a name="how-to-access-the-threat-intelligence-report"></a>Hur kommer du åt hotinformationsrapporten?
Du kan se aktuella aviseringar i rutan **Security alerts (Säkerhetsaviseringar)**. Öppna Azure portal och följ stegen nedan för att se mer information om varje avisering:

1. På instrumentpanelen i Security Center hittar du rutan **Security alerts (Säkerhetsaviseringar)**.
2. Klicka på ikonen för att öppna bladet **Säkerhetsvarningar** som innehåller mer information om aviseringarna och klicka sedan på den säkerhetsvarning som du vill visa mer information om.

    ![Säkerhetsaviseringar](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. I det här fallet den **misstänkt process kördes** bladet visar detaljerna om aviseringen som visas i bilden nedan:

    ![Utförlig information om säkerhetsaviseringar](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. Mängden information som är tillgänglig för varje säkerhetsvarning varierar beroende på typen av avisering. I den **rapporter** fältet du har en länk till hotinformationsrapporten. Klicka på länken så öppnas ett nytt webbläsarfönster med en PDF-fil.

   ![Val av lagringsutrymme](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Härifrån kan du hämta PDF-filen för den här rapporten och läsa mer om säkerhetsproblemet som har identifierats samt vidta åtgärder baserat på informationen.

## <a name="see-also"></a>Se också
I det här dokumentet har vi tittat på hur hotinformationsrapporterna i Azure Security Center kan hjälpa dig när du undersöker säkerhetsaviseringar. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Använda Azure Security Center vid incidenthantering](security-center-incident-response.md)
* [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md)
* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md). Lär dig mer om planering och viktiga designaspekter när du ska börja använda Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md). Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Hantera säkerhetsincidenter i Azure Security Center](security-center-incident.md)
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
