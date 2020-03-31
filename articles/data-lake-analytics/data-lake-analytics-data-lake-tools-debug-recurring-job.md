---
title: Felsöka återkommande jobb i Azure Data Lake Analytics
description: Lär dig hur du använder Azure Data Lake Tools för Visual Studio för att felsöka ett onormalt återkommande jobb.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 5a2935d559a967151c5bdc01c4b0806fe52179b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60629788"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Felsöka ett avvikande, återkommande jobb

Den här artikeln visar hur du använder [Azure Data Lake Tools för Visual Studio för](https://aka.ms/adltoolsvs) att felsöka problem med återkommande jobb. Läs mer om pipeline och återkommande jobb från [bloggen Azure Data Lake och Azure HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Återkommande jobb delar vanligtvis samma frågelogik och liknande indata. Anta till exempel att du har ett återkommande jobb som körs varje måndag morgon klockan 8.00. för att räkna förra veckans aktiva användare varje vecka. Skripten för dessa jobb delar en skriptmall som innehåller frågelogiken. Indata för dessa jobb är användningsdata för förra veckan. Att dela samma frågelogik och liknande indata innebär vanligtvis att prestanda för dessa jobb är liknande och stabilt. Om ett av dina återkommande jobb plötsligt fungerar onormalt, misslyckas eller saktar ner mycket kanske du vill:

- Se statistikrapporterna för tidigare körningar av det återkommande jobbet för att se vad som hände.
- Jämför det onormala jobbet med ett normalt för att ta reda på vad som har ändrats.

**Relaterade jobbvyer** i Azure Data Lake Tools för Visual Studio hjälper dig att påskynda felsökningsframstegen med båda fallen.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Steg 1: Hitta återkommande jobb och öppna relaterad jobbvy

Om du vill använda Relaterad jobbvy för att felsöka ett återkommande jobbproblem måste du först hitta det återkommande jobbet i Visual Studio och sedan öppna Relaterad jobbvy.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Fall 1: Du har webbadressen till det återkommande jobbet

Via**Jobbvyn****i Verktygsdatasjö** >  **Tools** > kan du klistra in jobbadressen för att öppna jobbvyn i Visual Studio. Välj **Visa relaterade jobb** om du vill öppna Relaterad jobbvy.

![Visa länken Relaterade jobb i DataSjöanalysverktyg](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Fall 2: Du har pipelinen för det återkommande jobbet, men inte webbadressen

I Visual Studio kan du öppna Pipeline Browser via Server Explorer > ditt Azure Data Lake Analytics-konto > **Pipelines**. (Om du inte hittar den här noden i Server Explorer [hämtar du det senaste plugin-programmet](https://aka.ms/adltoolsvs).) 

![Välja noden Pipelines](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

I Pipeline Browser visas alla pipelines för DataSjöanalyskontot till vänster. Du kan expandera pipelines för att hitta alla återkommande jobb och sedan välja den som har problem. Relaterade jobbvyn öppnas till höger.

![Välja en pipeline och öppna relaterad jobbvy](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Steg 2: Analysera en statistikrapport

En sammanfattning och en statistikrapport visas överst i Relaterad jobbvy. Där kan du hitta den potentiella orsaken till problemet. 

1.  I rapporten visar X-axeln tiden för inlämnande av jobb. Använd den för att hitta det onormala jobbet.
2.  Använd processen i följande diagram för att kontrollera statistik och få insikter om problemet och möjliga lösningar.

![Processdiagram för kontroll av statistik](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Steg 3: Jämför det onormala jobbet med ett normalt jobb

Du hittar alla inskickade återkommande jobb via jobblistan längst ned i Relaterad jobbvy. Om du vill hitta fler insikter och potentiella lösningar högerklickar du på det onormala jobbet. Använd vyn Projektdiff om du vill jämföra det onormala jobbet med ett tidigare normalt jobb.

![Snabbmeny för att jämföra jobb](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Var uppmärksam på de stora skillnaderna mellan dessa två jobb. Dessa skillnader orsakar förmodligen prestandaproblem. Kontrollera ytterligare genom att använda stegen i följande diagram:

![Processdiagram för att kontrollera skillnader mellan jobb](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Nästa steg

* [Lösa problem med datasnedställning](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Felsök användardefinierad C#-kod för misslyckade U-SQL-jobb](data-lake-analytics-debug-u-sql-jobs.md)
