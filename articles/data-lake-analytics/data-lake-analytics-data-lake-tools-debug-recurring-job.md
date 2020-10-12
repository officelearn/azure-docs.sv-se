---
title: Felsöka återkommande jobb i Azure Data Lake Analytics
description: Lär dig hur du använder Azure Data Lake verktyg för Visual Studio för att felsöka ett onormalt återkommande jobb.
services: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 05/20/2018
ms.openlocfilehash: 86d5134e257d2dae642eceb933a78047773b25a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87129958"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Felsöka ett avvikande, återkommande jobb

Den här artikeln visar hur du använder [Azure Data Lake verktyg för Visual Studio](https://aka.ms/adltoolsvs) för att felsöka problem med återkommande jobb. Lär dig mer om pipeline och återkommande jobb från [Azure Data Lake och Azure HDInsight-bloggen](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Återkommande jobb delar vanligt vis samma fråge logik och liknande indata. Anta till exempel att du har ett återkommande jobb som körs varje måndag morgon kl. 8.00 för att räkna den senaste veckans veckovis aktiva användare. Skripten för dessa jobb delar en certifikatmall som innehåller fråge logiken. Indata för dessa jobb är användnings data för den senaste veckan. Att dela samma fråge logik och liknande inblandning innebär vanligt vis att prestandan för dessa jobb är liknande och stabil. Om ett av dina återkommande jobb plötsligt fungerar onormalt, Miss lyckas eller saktar ned mycket, kan du vilja:

- Se statistik rapporterna för de tidigare körningarna av det återkommande jobbet för att se vad som hände.
- Jämför det onormala jobbet med en normal för att ta reda på vad som har ändrats.

I **vyn relaterade jobb** i Azure Data Lake verktyg för Visual Studio kan du påskynda fel söknings processen med båda fallen.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Steg 1: hitta återkommande jobb och öppna vyn relaterade jobb

Om du vill använda vyn relaterad jobb för att felsöka ett återkommande jobb problem måste du först hitta det återkommande jobbet i Visual Studio och sedan öppna vyn relaterad jobb.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Fall 1: du har URL: en för det återkommande jobbet

Med hjälp av **verktygen**  >  **data Lake**  >  **jobb**kan du klistra in jobb-URL: en för att öppna jobb visning i Visual Studio. Välj **Visa relaterade jobb** för att öppna vyn relaterade jobb.

![Visa relaterade jobb länkar i Data Lake Analytics verktyg](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Fall 2: du har pipelinen för det återkommande jobbet, men inte webb adressen

I Visual Studio kan du öppna pipeline-webbläsare genom Server Explorer > ditt Azure Data Lake Analytics konto > **pipelines**. (Om du inte hittar den här noden i Server Explorer [laddar du ned det senaste plugin-programmet](https://aka.ms/adltoolsvs).) 

![Välja noden pipelines](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

I pipeline-webbläsare visas alla pipeliner för Data Lake Analytics konto till vänster. Du kan expandera pipelinen för att hitta alla återkommande jobb och sedan välja det som har problem. Vyn relaterade jobb öppnas till höger.

![Välja en pipeline och öppna relaterad jobb-vy](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Steg 2: analysera en statistik rapport

En sammanfattning och en statistik rapport visas överst i vyn relaterade jobb. Där kan du hitta den potentiella rotor saken till problemet. 

1.  I rapporten visar X-axeln jobbets överförings tid. Använd den för att hitta det onormala jobbet.
2.  Använd processen i följande diagram för att kontrol lera statistik och få insikter om problemet och möjliga lösningar.

![Process diagram för att kontrol lera statistik](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Steg 3: jämför det onormala jobbet med ett normalt jobb

Du kan hitta alla skickade återkommande jobb via jobb listan längst ned i vyn relaterade jobb. Om du vill ha mer insikter och potentiella lösningar högerklickar du på det onormala jobbet. Använd vyn jobb skillnad för att jämföra det onormala jobbet med en tidigare normal.

![Snabb meny för att jämföra jobb](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Var uppmärksam på de stora skillnaderna mellan de två jobben. Dessa skillnader orsakar förmodligen prestanda problemen. Om du vill kontrol lera ytterligare följer du stegen i följande diagram:

![Process diagram för att kontrol lera skillnader mellan jobb](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Nästa steg

* [Lös problem med data förvrängning](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Felsök användardefinierad C#-kod för misslyckade U-SQL-jobb](data-lake-analytics-debug-u-sql-jobs.md)
