---
title: Hotinformationsrapporter i Azure Security Center | Microsoft Docs
description: "Det här dokumentet beskriver hur du använder hotinformationsrapporter i Azure Security Center i samband med en undersökning för att få fram mer information om en säkerhetsvarning."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 3cba38d95535ff5ed3cd62aac5c0aa04a310f48c
ms.openlocfilehash: 99c5ea06a0970c360eb711a8b10894d416146693


---
# <a name="azure-security-center-threat-intelligence-report"></a>Hotinformationsrapporter i Azure Security Center
Det här dokumentet beskriver hur du kan lära dig mer om ett hot som genererat en säkerhetsvarning med hjälp av hotinformationsrapporter i Azure Security Center.

## <a name="what-is-a-threat-intelligence-report"></a>Vad är en hotinformationsrapport?
Hotidentifieringen i Security Center sker genom övervakning av säkerhetsinformation från dina Azure-resurser, nätverket och anslutna partnerlösningar. Tjänsten analyserar den här informationen, och korrelerar ofta information från flera källor för att identifiera hot. Den här processen är en del av [identifieringsfunktionerna](security-center-detection-capabilities.md) i Security Center.

När Security Center identifierar ett hot utlöses en [säkerhetsvarning](security-center-managing-and-responding-alerts.md), som innehåller detaljerad information om en viss händelse, inklusive förslag på åtgärder. Security Center hjälper incidenthanteringsteamet att undersöka och åtgärda hot genom att tillhandahålla hotinformationsrapporter med information om hotet som har identifierats, inklusive information som:

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

Den här typen av information är mycket användbar under [incidenthanteringsprocessen](security-center-incident-response.md), där man genom kontinuerliga undersökningar försöker förstå attackens källa, angriparens avsikter och hur problemet kan åtgärdas.

## <a name="how-to-access-the-threat-intelligence-report"></a>Hur kommer du åt hotinformationsrapporten?
Du kan se aktuella aviseringar i rutan **Security alerts (Säkerhetsaviseringar)**. Öppna Azure Portal och följ stegen nedan om du vill ha mer information om varje typ av varning:

1. På instrumentpanelen i Security Center hittar du rutan **Security alerts (Säkerhetsaviseringar)**.
2. Klicka på ikonen för att öppna bladet **Säkerhetsvarningar** som innehåller mer information om aviseringarna och klicka sedan på den säkerhetsvarning som du vill visa mer information om.

    ![Säkerhetsaviseringar](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. I vårt exempel visar bladet **Suspicious process executed** (Misstänkt process körs) information om aviseringen, som du ser i bilden nedan:

    ![Utförlig information om säkerhetsaviseringar](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. Mängden information som är tillgänglig för varje säkerhetsvarning varierar beroende på typen av avisering. Fältet **RAPPORTER** innehåller en länk till hotinformationsrapporten. Klicka på länken så öppnas ett nytt webbläsarfönster med en PDF-fil.

   ![Val av lagringsutrymme](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Härifrån kan du hämta PDF-filen för den här rapporten och läsa mer om säkerhetsproblemet som har identifierats samt vidta åtgärder baserat på informationen.

## <a name="see-also"></a>Se även
I det här dokumentet har vi tittat på hur hotinformationsrapporterna i Azure Security Center kan hjälpa dig när du undersöker säkerhetsaviseringar. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Använda Azure Security Center vid incidenthantering](security-center-incident-response.md)
* [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md)
* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md). Lär dig mer om planering och viktiga designaspekter när du ska börja använda Azure Security Center.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md). Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Hantera säkerhetsincidenter i Azure Security Center](security-center-incident.md)
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.



<!--HONumber=Jan17_HO5-->


