---
title: Azure Stream Analytics-jobbtillstånd
description: I den här artikeln beskrivs de fyra olika tillstånden för ett Stream Analytics-jobb. körs, stoppas, försämras och misslyckades.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 454474333cac94dc25deae8196e9ba45bcb3a574
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75359774"
---
# <a name="azure-stream-analytics-job-states"></a>Azure Stream Analytics-jobbtillstånd

Ett Stream Analytics-jobb kan vara i ett av fyra tillstånd vid en given tidpunkt: löpning, stoppad, försämrad eller misslyckad. Du hittar jobbets tillstånd på översiktssidan för Stream Analytics-jobbet i Azure-portalen. 

| Status | Beskrivning | Rekommenderade åtgärder |
| --- | --- | --- |
| **Körs** | Jobbet körs på Azure-läsning av händelser som kommer från de definierade indatakällorna, bearbetar dem och skriver resultaten till de konfigurerade utdatamottagarena. | Det är en bra idé att spåra jobbets prestanda genom att övervaka [viktiga mätvärden](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Stoppad** | Jobbet stoppas och bearbetar inte händelser. | Ej tillämpligt | 
| **Degraderad** | Det kan vara återkommande problem med in- och utdataanslutningarna. Dessa fel kallas tillfälliga fel som kan göra ditt jobb i ett försämrat tillstånd. Stream Analytics försöker omedelbart återställa från sådana fel och återgå till ett körtillstånd (inom några minuter). Dessa fel kan inträffa på grund av nätverksproblem, tillgänglighet för andra Azure-resurser, avserialiseringsfel etc. Jobbets prestanda kan påverkas när jobbet är i försämrat tillstånd.| Du kan titta på [diagnostik- eller aktivitetsloggarna](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) om du vill veta mer om orsaken till dessa tillfälliga fel. I fall som deserialiseringsfel rekommenderar vi att du vidtar korrigerande åtgärder för att säkerställa att händelser inte är felbildade. Om jobbet fortsätter att nå resursutnyttjandegränsen kan du försöka öka SU-numret eller [parallellisera jobbet](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). I andra fall där du inte kan vidta några åtgärder försöker Stream Analytics återställa till ett *tillstånd som körs.* <br> Du kan använda [mätvärden för vattenstämpelfördröjning](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) för att förstå om dessa tillfälliga fel påverkar jobbets prestanda.|
| **Misslyckades** | Jobbet uppstod på ett kritiskt fel som resulterade i ett misslyckat tillstånd. Händelser läses och bearbetas inte. Körningsfel är en vanlig orsak till att jobb hamnar i ett misslyckat tillstånd. | Du kan [konfigurera aviseringar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) så att du får ett meddelande när jobbet går till Tillståndet Misslyckades. <br> <br>Du kan felsöka med [aktivitets- och diagnostikloggar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) för att identifiera grundorsaken och åtgärda problemet.|

## <a name="next-steps"></a>Nästa steg
* [Installationsaviseringar för Azure Stream Analytics-jobb](stream-analytics-set-up-alerts.md)
* [Tillgängliga mätvärden i Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Felsöka med hjälp av aktivitets- och diagnostikloggar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
