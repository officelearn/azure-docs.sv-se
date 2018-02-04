---
title: "Uppgradera Azure logganalys till ny logg sökning | Microsoft Docs"
description: "Du kan delta i public preview Log Analytics-frågespråket i nya är nästan här.  Den här artikeln beskriver fördelarna med det nya språket och konvertera din arbetsyta."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 3bb54f7897876d656da6f1a4b349c9db202a142d
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="azure-log-analytics-upgrade-to-new-log-search"></a>Azure logganalys uppgradera till ny logg-sökning

Nya Log Analytics-frågespråket är här och din arbetsyta måste uppgraderas för att dra nytta av den.  Du kan uppgradera din arbetsyta själv eller vänta tills den uppgraderas automatiskt under installationen som startar i sen oktober och passerar slutet på året.  Den här artikeln beskriver fördelarna med det nya språket och konvertera din arbetsyta.  

## <a name="why-the-new-language"></a>Varför det nya språket?
Vi förstår att smärta finns i alla övergångar och vi inte ändra frågespråk för skojs av den.  Det finns flera orsaker som den här ändringen ger betydande värde för våra logganalys-kunder.

- **Enkel men kraftfull.** Det nya språket är lättare att förstå och liknar SQL med konstruktioner mer som naturligt språk än det äldra språket.
- **Fullständig rörnät språk.**  Det nya språket har mer omfattande rörnät funktioner än äldre språk.  Valfri utdata kan skickas till ett annat kommando för att skapa mer komplexa frågor än var möjligt tidigare.
- **Sök tidsfält extractions.**  Det nya språket stöder mer avancerade runtime beräknade fält än äldre språk.  Du kan använda komplexa beräkningar för utökade fält och sedan använda de beräknade fälten för ytterligare kommandon, inklusive kopplingar och aggregeringar.
- **Avancerade kopplingar.**  Det nya språket ger mer avancerade kopplingar än det äldra språk, inklusive möjligheten att ansluta till tabeller på flera fält, använda inre och yttre kopplingar och delta i utökade fält.
- **Tidsvärdet funktioner.**  Det nya språket har mer avancerade funktioner för datum/tid än det äldra språket.
- **Smart Analytics.**  Det nya språket har avancerade algoritmer för att utvärdera mönster i datauppsättningar och jämför olika datauppsättningar.
- **Avancerade Analytics-portalen.**  Avancerade analyser portal erbjuder analysfunktioner som är inte tillgängligt i logganalys-portalen inklusive multiline redigering av frågor, ytterligare grafik och avancerad diagnostik.
- **Konsekvens med andra program.**  Det nya språket och Advanced Analytics-portalen används redan för analyser i Application Insights.  Implementera för Log Analytics ger konsekvens över Azure-tjänster.
- **Bättre integration med Power BI.** Frågorna i det nya språket kan exporteras till Power BI Desktop, så du kan använda dess omfattande data transformation funktioner.
- **Mycket mer.** Referera till den [Azure Log Analytics-frågespråket](https://docs.loganalytics.io) plats för fullständig information och om det nya språket.


## <a name="when-can-i-upgrade"></a>När kan jag uppgradera?
Uppgraderingen lyfts över alla Azure-regioner så det kan vara tillgängliga i vissa regioner före andra.  Du vet när din arbetsyta är tillgänglig för att uppgradera när du ser en banderoll överst på arbetsytan bjuda in dig att uppgradera.

![Uppgradering 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

Om arbetsytan uppgraderas automatiskt, ser du en banderoll som anger som uppgraderas med en sammanfattning om eventuella problem som har påträffats.

 ![Automatisk uppgradering](media/log-analytics-log-search-upgrade/auto-upgrade.png)


## <a name="what-happens-after-the-upgrade"></a>Vad händer efter uppgraderingen?
Följande ändringar görs i din arbetsyta när konverteras:

- Alla sparade sökningar, Varningsregler och vyer som du har skapat med Vydesigner konverteras automatiskt till det nya språket.  Söker i lösningar konverteras inte automatiskt, men de är i stället konverteras direkt när du öppnar dem.  
- [Min instrumentpanel](log-analytics-dashboards.md) är inaktuell för [Vydesigner](log-analytics-view-designer.md) och [Azure instrumentpaneler](../azure-portal/azure-portal-dashboards.md).  Paneler som du lade till min instrumentpanel finns kvar, men de är skrivskyddad.
- [Power BI-integration](log-analytics-powerbi.md) ersätts med en ny process.  Kommer att inaktiveras alla befintliga Power BI-scheman som du skapade och du måste ersätta dem med den nya processen.
- Svar från [Varna åtgärder](log-analytics-alerts-actions.md) med webhooks och runbooks har ett nytt format och du kan behöva uppdateras din Varningsregler.
- Ta en titt på den [loggen Sök vanliga frågor om](log-analytics-log-search-faq.md) för vanliga frågor om uppgraderingen.

## <a name="how-do-i-know-if-there-were-any-issues-from-the-upgrade"></a>Hur vet jag om det fanns problem från uppgraderingen?
När uppgraderingen har slutförts kan det finnas en **uppgradera sammanfattning** avsnitt i inställningarna för arbetsytan.  Kontrollera det här avsnittet för information om uppgraderingen och visa eventuella problem som har påträffats.

 ![Uppgradera sammanfattning](media/log-analytics-log-search-upgrade/upgrade-summary.png)

## <a name="how-do-i-manually-perform-the-upgrade"></a>Hur gör jag uppgraderingen manuellt?
Du kan uppgradera din arbetsyta när du ser banderoll överst i portalen.  

1.  Starta uppgraderingen genom att klicka på listen som säger **mer och uppgradera**.

    ![Uppgradera 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>

2.  Läs igenom informationen om uppgraderingen på sidan information om uppgradering.

    ![Uppgradera 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>

3.  Klicka på **uppgradera nu** starta uppgraderingen.

    ![Uppgradera 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>Ett meddelande i rutan i det övre högra hörnet visas status.
    
    ![Uppgradera 5](media/log-analytics-log-search-upgrade/upgrade-05.png)

4.  Klart!  Gå till loggen söksidan och titta på uppgraderade arbetsytan.

    ![Uppgradera 6](media/log-analytics-log-search-upgrade/upgrade-06.png)

Om det uppstår ett problem som orsakar uppgraderingen misslyckas kan du gå till den [diskussionsforum](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) och ställa din fråga eller [skapa en supportbegäran](../azure-supportability/how-to-create-azure-support-request.md) från Azure-portalen.

## <a name="how-do-i-learn-the-new-language"></a>Hur kan jag ta reda på nytt språk
Eftersom den används av flera tjänster har vi skapat ett [extern webbplats som värd för dokumentationen](https://docs.loganalytics.io/) för det nya språket.  Detta inkluderar självstudier, exempel och en fullständig referens som hjälper dig att komma igång. Du kan gå igenom en självstudiekurs för det nya språket på [komma igång med frågor](https://go.microsoft.com/fwlink/?linkid=856078) och komma åt Språkreferens på [Log Analytics-frågespråket](https://go.microsoft.com/fwlink/?linkid=856079).  

Om du vill testa det nya språket i en miljö för demonstration inklusive en massa exempeldata ta en titt på den [playground miljö](https://portal.loganalytics.io/demo#/discover/home).

Om du redan är bekant med det äldre Log Analytics-frågespråket men kan använda du konverteraren språk som läggs till din arbetsyta som en del av uppgraderingen.  Skriv i äldre frågan och sedan på **konvertera** att se översatt version.  Därefter kan du antingen klicka på Sök för att köra den sökning eller kopiera och klistra in den konverterade frågan om du vill använda någon annanstans, till exempel en aviseringsregel.  Du kan också ta en titt på vårt [fusklapp](log-analytics-log-search-transition.md) som jämför direkt vanliga frågor från äldre språk.

![Språk konverterare](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>Nästa steg
- Checka ut en [självstudiekurs om det nya språket](https://go.microsoft.com/fwlink/?linkid=856078).
- Gå igenom en [självstudier om hur du använder portalen loggen Sök](log-analytics-log-search-log-search-portal.md) med det nya språket i fråga.
- Få en introduktion till den nya [Advanced Analytics portal](https://go.microsoft.com/fwlink/?linkid=856587).
