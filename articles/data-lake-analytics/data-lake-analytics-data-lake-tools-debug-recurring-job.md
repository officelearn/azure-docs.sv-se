---
title: Felsöka återkommande jobb | Microsoft Docs
description: Lär dig hur du använder Azure Data Lake-verktyg för Visual Studio för att felsöka ett onormalt återkommande jobb.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/20/2018
ms.author: yanacai
ms.openlocfilehash: 02f33fed7f8758066e34ee4e643efd9b696a0e8d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Felsöka ett onormalt återkommande jobb

Den här artikeln visar hur du använder [Azure Data Lake-verktyg för Visual Studio](http://aka.ms/adltoolsvs) att felsöka problem med återkommande jobb. Mer information om pipeline- och återkommande jobb från de [Azure Data Lake och Azure HDInsight blogg](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).

Återkommande jobb delar vanligtvis samma fråga logik och liknande indata. Anta exempelvis att du har ett återkommande jobb som körs varje måndag morgon klockan 8. Räkna förra veckan veckovisa aktiv användare. Skript för dessa jobb delar en mall för skript som innehåller logiken som frågan. Indata för dessa jobb är användningsdata för föregående vecka. Dela samma fråga logik och liknande indata vanligtvis innebär att prestanda jobb liknande och stabil. Om en av dina återkommande jobb plötsligt utför onormalt, misslyckas, eller långsammare mycket, kanske du vill:

- Se statistikrapporter för tidigare körningar av återkommande jobb att se vad som hänt.
- Jämför onormal jobbet med en normal som ska ta reda på vad som har ändrats.

**Relaterade jobbet** i Azure Data Lake-verktyg för Visual Studio hjälper dig att öka felsökning förloppet med båda fallen.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Steg 1: Hitta återkommande jobb och öppna relaterade jobb

Om du vill använda den relaterade jobb visa felsökning av en återkommande jobb problem måste du först hittas återkommande projekt i Visual Studio och sedan öppna relaterade jobb.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Fall 1: Du har rätt Webbadress för återkommande jobb

Via **verktyg** > **Datasjö** > **jobbet**, kan du klistra in Webbadressen jobbet för att öppna projektet i Visual Studio. Välj **Visa relaterade jobb** öppna vyn för relaterade jobb.

![Visa relaterade jobb länken i Data Lake Analytics-verktyg](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Fall 2: Du har pipeline för återkommande jobb, men inte URL-Adressen

Du kan öppna Pipeline webbläsare via Server Explorer i Visual Studio > ditt Azure Data Lake Analytics-konto > **Pipelines**. (Om du inte hittar den här noden i Server Explorer [hämta det senaste](http://aka.ms/adltoolsvs).) 

![Att välja noden Pipelines](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

I Pipeline-webbläsare visas alla rörledningar för Data Lake Analytics-kontot till vänster. Du kan expandera pipelines för att hitta alla återkommande jobb och sedan välja en som har problem. Relaterade jobb öppnas till höger.

![Att välja en pipeline och öppna den relaterade jobb vyn](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Steg 2: Analysera en rapport

En översikt och en rapport visas överst i vyn för relaterade jobb. Där hittar du den eventuella grundorsaken till problemet. 

1.  I rapporten visas x-axeln sändningstid för jobbet. Du kan använda den för att hitta onormal jobbet.
2.  Använd processen i följande diagram om du vill kontrollera statistik och få insikter om problemet och möjliga lösningar.

![Processdiagram för att kontrollera statistik](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Steg 3: Jämföra onormal jobbet till ett normala jobb

Du hittar alla skickade återkommande jobb genom jobblistan längst ned i vyn för relaterade jobb. Högerklicka på onormal jobbet för att hitta mer information och möjliga lösningar. Använd vyn jobb Diff för att jämföra onormal jobbet med en föregående normal.

![Snabbmenyn för att jämföra jobb](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Vara uppmärksam på stora skillnaderna mellan dessa två jobb. Dessa skillnader orsakar förmodligen problem med prestanda. Om du vill kontrollera ytterligare använder du stegen i följande diagram:

![Processdiagram för kontroll av skillnaderna mellan jobb](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Nästa steg

* [Lösa problem med en förskjutning av data](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Felsöka användardefinierade C#-kod för misslyckade U-SQL-jobb](data-lake-analytics-debug-u-sql-jobs.md)
