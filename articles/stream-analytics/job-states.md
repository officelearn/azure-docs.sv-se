---
title: Azure Stream Analytics jobb tillstånd
description: I den här artikeln beskrivs de fyra olika tillstånden för ett Stream Analytics jobb. körs, stoppad, degraderas och misslyckades.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 008493c6eb8840752be3815d30a5df5bad42aff2
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129226"
---
# <a name="azure-stream-analytics-job-states"></a>Azure Stream Analytics jobb tillstånd

Ett Stream Analytics jobb kan ha ett av fyra tillstånd vid en specifik tidpunkt: körs, stoppas, degraderas eller misslyckades. Du hittar jobbets status på sidan Översikt för Stream Analytics jobb i Azure Portal. 

| Stat | Beskrivning | Rekommenderade åtgärder |
| --- | --- | --- |
| **Körs** | Ditt jobb körs på Azures Läs händelser som kommer från de angivna inmatnings källorna, bearbetar dem och skriver resultaten till de konfigurerade utgående sinkarna. | Det är en bra idé att spåra jobbets prestanda genom att övervaka [viktiga mått](./stream-analytics-set-up-alerts.md#scenarios-to-monitor). |
| **Stoppad** | Jobbet har stoppats och bearbetar inte händelser. | Ej tillämpligt | 
| **Degraderad** | Det kan finnas tillfälliga problem med dina indata-och utmatnings anslutningar. Dessa fel kallas tillfälliga fel, vilket kan göra att jobbet går in i degraderat tillstånd. Stream Analytics försöker omedelbart återställa från sådana fel och återgå till ett körnings tillstånd (inom några minuter). Felen kan inträffa på grund av nätverks problem, tillgänglighet för andra Azure-resurser, deserialiserings fel osv. Jobbets prestanda kan påverkas när jobbet är i degraderat tillstånd.| Du kan titta på [diagnostik-eller aktivitets loggarna](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs) om du vill veta mer om orsaken till de tillfälliga felen. I sådana fall som deserialiserings fel rekommenderar vi att du vidtar åtgärder för att säkerställa att händelserna inte är felaktiga. Om jobbet håller på att nå gränsen för resursanvändning kan du försöka öka ditt SU-nummer eller [parallellisera ditt jobb](./stream-analytics-parallelization.md). I andra fall där du inte kan vidta någon åtgärd försöker Stream Analytics återställa till ett *Kör* tillstånd. <br> Du kan använda [vattenstämpelns fördröjnings](./stream-analytics-set-up-alerts.md#scenarios-to-monitor) mått om du vill förstå om de här tillfälliga felen påverkar jobbets prestanda.|
| **Bröt** | Jobbet påträffade ett kritiskt fel som resulterade i ett felaktigt tillstånd. Händelser läses och bearbetas inte. Körnings fel är en vanlig orsak till att jobben slutar att vara i fel tillstånd. | Du kan [Konfigurera aviseringar](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) så att du får ett meddelande när jobbet går till felaktigt tillstånd. <br> <br>Du kan felsöka med hjälp av [aktivitets-och resurs loggar](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs) för att identifiera rotor saken och åtgärda problemet.|

## <a name="next-steps"></a>Nästa steg
* [Installations aviseringar för Azure Stream Analytics jobb](stream-analytics-set-up-alerts.md)
* [Mått som är tillgängliga i Stream Analytics](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)
* [Felsöka med hjälp av aktivitets-och resurs loggar](./stream-analytics-job-diagnostic-logs.md)