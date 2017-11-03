---
title: "Så här felsöker du ett onormalt återkommande jobb | Microsoft Docs"
description: "Lär dig hur du använder Azure Data Lake-verktyg för Visual Studio för att felsöka ett onormalt återkommande jobb."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2017
ms.author: yanacai
ms.openlocfilehash: a358f94b117c12511028a875e56b5c9dba8d3382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-an-abnormal-recurring-job"></a>Så här felsöker du ett onormalt återkommande jobb

I detta dokument, kommer vi hur du använder [Azure Data Lake-verktyg för Visual Studio](http://aka.ms/adltoolsvs) att felsöka problem med återkommande jobb. Mer information om pipeline- och återkommande jobb från [här](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).
Återkommande jobb delar vanligtvis samma fråga logik och liknande indata. Du till exempel har ett återkommande jobb som körs på varje måndag morgon klockan 8. Om du vill räkna förra veckan veckovisa aktiv användare skript för dessa jobb delar en mall för skript som innehåller logiken som frågan och indata för dessa jobb är användningsdata för föregående vecka. Dela samma fråga logik och liknande indatatagg vanligtvis innebär prestanda jobb är liknande och stabil, om någon av dina återkommande jobb plötsligt utför onormalt, misslyckade eller långsamma ned mycket, du kanske vill:

1.  Se statistikrapporter för förhandsversioner körningar av återkommande jobb för att se vad som hänt.
2.  Jämför onormal jobbet med en normal som ska ta reda på vad som har ändrats.

**Relaterade jobbet** i Azure Data Lake-verktyg för Visual Studio hjälper dig att öka felsökning förloppet med båda fallen.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Steg 1: Hitta återkommande jobb och öppna relaterade jobb

Visa felsöka återkommande jobb problem för att använda relaterade jobb, måste du först hittas återkommande projekt i Visual Studio och sedan öppna relaterade jobb.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Fall 1: Du har rätt Webbadress för återkommande jobb

Via **Verktyg > Data Lake > jobbet**, kan du klistra in Webbadressen jobbet för att öppna projektet i Visual Studio och via Visa relaterade jobb att öppna relaterade jobbet.

![Verktyg för data Lake Analytics Visa relaterade jobb](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Fall 2: Du har pipeline för återkommande jobb, men inte URL-Adressen

I Visual Studio kan du öppna Pipeline webbläsare via **Server Explorer > ditt Data Lake Analytics-konto > Pipelines** (om du inte hittar den här noden i Server Explorer kan du skaffa verktyget lasted [här](http://aka.ms/adltoolsvs)). I Pipeline-webbläsare, visas alla pipelines för kontot ADLA längst till vänster, du kan expandera pipelines du hittar alla återkommande jobb genom att klicka på som har problem, vyn relaterade jobb som öppnas till höger.

![Verktyg för data Lake Analytics Visa relaterade jobb](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

![Verktyg för data Lake Analytics Visa relaterade jobb](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-statistics-report"></a>Steg 2: Analysera rapport

En översikt och en rapport visas överst i vyn för relaterade jobb, där du får den eventuella grundorsaken till det onormala. 

1.  Du måste först hittar onormal jobbet i rapporten. X-axeln visas sändningstid för jobbet som du kan hitta onormal jobbet.
2.  Följ under processen för att kontrollera statistik och få insikter i den onormal och möjliga lösningar.

![Verktyg för data Lake Analytics Visa relaterade jobb](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-recurring-job-to-a-normal-job"></a>Steg 3: Jämföra onormal återkommande jobbet till ett normala jobb

Du hittar alla skickade återkommande jobb genom jobblistan längst ned i vyn för relaterade jobb. Du kan jämföra onormal jobbet med en föregående normalt att hitta fler insikter och möjliga lösningar i jobbet Diff vy via högerklickar du på.

![Verktyg för data Lake Analytics Visa relaterade jobb](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Vanligtvis måste du vara uppmärksam på stora skillnaderna mellan dessa 2 jobb som de är förmodligen av skäl som orsakar prestandaproblem och du kan även följa nedanstående steg för att göra ytterligare kontroller.

![Verktyg för data Lake Analytics Visa relaterade jobb](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Nästa steg

* [Hur du felsöker och löser problem med skeva](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Felsöka U-SQL-jobbet misslyckades för användardefinierade kodfel](data-lake-analytics-debug-u-sql-jobs.md)