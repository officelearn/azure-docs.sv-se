---
title: Felsöka återkommande jobb i Azure Data Lake Analytics
description: Lär dig hur du använder Azure Data Lake Tools för Visual Studio för att felsöka ett avvikande, återkommande jobb.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 5a2935d559a967151c5bdc01c4b0806fe52179b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629788"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Felsöka ett avvikande, återkommande jobb

Den här artikeln visar hur du använder [Azure Data Lake Tools för Visual Studio](https://aka.ms/adltoolsvs) att felsöka problem med återkommande jobb. Mer information om pipeline och återkommande jobb från den [Azure Data Lake och Azure HDInsight blogg](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Återkommande jobb delar vanligtvis samma fråga logik och liknande indata. Anta exempelvis att du har ett återkommande jobb som körs varje måndag morgon kl 8. att räkna förra veckan veckovisa aktiv användare. Skript för dessa jobb delar en mall för skript som innehåller frågelogiken som. Indata för dessa jobb är användningsdata för förra veckan. Dela samma fråga logik och liknande indata vanligtvis innebär att prestandan för dessa jobb är liknande och stabil. Om en av dina återkommande jobb plötsligt utför onormalt, misslyckas, eller saktar ned mycket, kan du:

- Se statistikrapporter för tidigare körningar av återkommande jobb att se vad som hände.
- Jämför onormalt jobbet med en normal som ska ta reda på vad som har ändrats.

**Relaterade Jobbvy** i Azure Data Lake Tools för Visual Studio hjälper dig att öka felsökning förloppet med båda fallen.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Steg 1: Leta upp återkommande jobb och öppna relaterade Jobbvy

Om du vill använda den relaterade jobb vyn för att felsöka ett problem med återkommande jobb, måste du först hitta återkommande jobb i Visual Studio och öppna sedan relaterade Jobbvy.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Fall 1: Du har rätt Webbadress för det återkommande jobbet

Via **verktyg** > **Datasjö** > **Jobbvy**, du kan klistra in jobb-URL för att öppna jobbet i Visual Studio. Välj **Visa relaterade jobb** öppna relaterade jobb vyn.

![Visa relaterade jobb länk i Data Lake Analytics-verktyg](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Fall 2: Du har pipelinen för återkommande jobb, men inte URL: en

Du kan öppna Pipeline webbläsare via Server Explorer i Visual Studio > ditt Azure Data Lake Analytics-konto > **Pipelines**. (Om du inte hittar den här noden i Server Explorer [hämta det senaste](https://aka.ms/adltoolsvs).) 

![Att välja noden Pipelines](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

I Pipeline webbläsare visas alla pipelines för Data Lake Analytics-kontot till vänster. Du kan expandera pipelines för att hitta alla återkommande jobb och välj sedan det som har problem. Relaterade Jobbvy öppnas till höger.

![Att välja en pipeline och öppna relaterade Jobbvy](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Steg 2: Analysera en rapport

En sammanfattning och en rapport visas längst upp i relaterade Jobbvy. Där hittar du den eventuella grundorsaken till problemet. 

1.  I rapporten visas x-axeln sändningstid för jobbet. Du kan använda den för att hitta onormalt jobbet.
2.  Använd processen i följande diagram för att kontrollera statistik och få insikter om problemet och möjliga lösningar.

![Diagram över för att kontrollera statistik](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Steg 3: Jämför onormalt jobbet till ett normala jobb

Du hittar alla återkommande jobb i jobb listan längst ned på relaterade Jobbvy som skickats. Högerklicka på onormalt jobbet för att hitta mer insikter och möjliga lösningar. Använd jobbet Diff-vyn för att jämföra onormalt jobbet med en föregående normal.

![Snabbmenyn för att jämföra jobb](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Ta hänsyn till stora skillnaderna mellan dessa två jobb. Dessa skillnader orsakar förmodligen problem med prestanda. Kontrollera ytterligare genom att använda stegen i följande diagram:

![Diagram över för att kontrollera skillnaderna mellan jobb](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Nästa steg

* [Lösa datasnedställning problem](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Felsöka en användardefinierad C#-kod för misslyckade U-SQL-jobb](data-lake-analytics-debug-u-sql-jobs.md)
