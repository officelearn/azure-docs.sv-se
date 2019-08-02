---
title: Anpassade aviseringsregler i Azure Security Center | Microsoft Docs
description: I det här avsnittet berättar vi hur du skapar anpassade aviseringsregler i Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: 8845cb0a91edefa0350558f35488519ec37d064d
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663576"
---
# <a name="custom-alert-rules-in-azure-security-center-retired"></a>Anpassade aviserings regler i Azure Security Center (dras tillbaka)
I det här dokumentet får du hjälp med att skapa anpassade aviserings regler (för hands version) i Azure Security Center.

> [!NOTE]
> Anpassade aviseringar drogs tillbaka från Azure Security Center den 30 juni 2019. 

## <a name="retirement-of-custom-alert-rules-in-azure-security-center"></a>Borttagning av anpassade aviserings regler i Azure Security Center

Den anpassade aviserings upplevelsen drogs tillbaka den 30 juni 2019, på grund av att den underliggande infrastrukturen förbrukades. Eventuella anpassade aviseringar som definierats före retriement börjar inte tillämpas och säkerhets aviseringar som baseras på dessa anpassade aviserings regler skapas inte. Du kan fortfarande Visa egna aviserings reglers frågor i Security Center för att återskapa dem i de alternativ som anges nedan:

Användarna uppmanas antingen att:
- Aktivera [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) och använd dess inbyggda [analys](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) funktioner för att återskapa sina aviserings regler
- Återskapa sina aviseringar med Azure Monitor logg aviseringar
                                     
Om du vill behålla dina befintliga aviseringar och återskapa dem i Azure Sentinel [startar du Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview). I det första steget väljer du den arbets yta där dina anpassade aviseringar lagras och väljer sedan meny alternativet analys för att ställa in dina anpassade aviserings regler. Mer information finns i [dokumentationen](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) .

> [!NOTE]
> Anpassade aviseringar med hjälp av [Sök](https://docs.microsoft.com/azure/azure-monitor/log-query/search-queries) -eller [union](https://docs-analytics-eus.azurewebsites.net/queryLanguage/query_language_unionoperator.html) -uttryck-frågor stöds inte i Azure Sentinel. Redigera de här aviseringarna innan du utför migreringen.

Information om hur du återskapar aviseringar med hjälp av Azure Monitor logg aviseringar finns i [skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log). En allmän översikt finns [i logg aviseringar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

## <a name="what-are-custom-alert-rules-in-security-center"></a>Vad är anpassade aviseringsregler i Security Center?

Security Center innehåller en uppsättning fördefinierade [säkerhetsaviseringar](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts), som löses ut när ett hot eller en misstänkt aktivitet inträffar. I vissa fall kanske du vill skapa en anpassad avisering som bemöter specifika behov i miljön.

Med hjälp av anpassade aviseringsregler i Security Center kan du definiera nya säkerhetsaviseringar baserat på data som redan har samlats in från miljön. Du kan skapa frågor och resultatet av dessa frågor kan användas som kriterier för den anpassade regeln, och regeln körs när dessa kriterier matchas. Du kan använda säkerhetshändelser för datorer, loggar från en partners säkerhetslösning eller data som inhämtas med hjälp av API:er för att skapa de anpassade frågorna.

> [!NOTE]
> Anpassade aviseringar stöds inte i Security Centers [undersöknings funktion](security-center-investigation.md).
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Hur skapar man en anpassad aviseringsregel i Security Center?

Öppna instrumentpanelen för **Security Center** och gör så här för att skapa en anpassad aviseringsregel:

1.  I den vänstra rutan under **Identifiering** klickar du på **Anpassade aviseringsregler (förhandsversion)** .
2.  På sidan **Security Center – Anpassade aviseringsregler (förhandsversion)** klickar du på **Ny anpassad aviseringsregel**.

    ![Anpassad avisering](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)

3.  Sidan Skapa anpassad aviseringsregel visas med följande alternativ:

    ![Skapa](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  I fältet **Namn** skriver du namnet på den här anpassade regeln.
5.  Ange en kort beskrivning som återspeglar syftet med denna regel i fältet **Beskrivning**.
6.  Välj allvarlighetsgrad (hög, medel, låg) efter behov i fältet **Allvarlighetsgrad**.
7.  I fältet **Prenumeration** väljer du den prenumeration där denna regel ska gälla.
8.  Välj den arbets yta som du vill övervaka med den här regeln i fältet **arbets yta** och i fältet **Sök fråga** anger du den fråga som du vill använda för att hämta resultatet.

    > [!NOTE]
    > Du behöver Skriv behörighet i arbets ytan som du väljer för att lagra din anpassade avisering.
    >
    >

    Frågans resultat utlöser aviseringen. Tänk på att när du skriver in en giltig fråga visas en grön bockmarkering i det högra hörnet av detta fält:

    ![Söka i data](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. Välj det tidsintervall då frågan ovan ska köras i fältet **Period**. Tänk på att sökresultatet längst ned i detta fält ändras beroende på det tidsintervall som du väljer.

    ![Period](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. I fältet **Utvärdering** väljer du frekvensen för när denna regel ska utvärderas och köras.
12. I fältet **Antal resultat** väljer du operator (större än eller mindre än).
13. I fältet **Tröskel** anger du ett nummer som ska användas som referens för operatorn som valdes tidigare.
14. Aktivera alternativet **Visa inga aviseringar** om du vill ange en väntetid innan Security Center skickar ännu en avisering för denna regel.
15. Klicka på **OK** för att slutföra.

När den nya aviseringsregeln har skapats visas den i listan över anpassade aviseringsregler. När villkoren i regeln uppfylls utlöses en ny avisering och du kan se den i instrumentpanelen **Säkerhetsaviseringar**.

![Varning](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

Tänk på att parametrarna (sökfråga, tröskel osv.) som fastställdes när regeln skapades är tillgängliga i aviseringen för den här anpassade regeln.

## <a name="see-also"></a>Se också
I det här avsnittet har vi berättat hur du skapar en anpassad aviseringsregel i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
