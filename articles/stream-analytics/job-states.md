---
title: Status för Azure Stream Analytics-jobb
description: Den här artikeln beskrivs olika lägen för ett Stream Analytics-jobb
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/06/2019
ms.openlocfilehash: 28e0e69d3a6a4d3a38146cbf2c49426b3b16c784
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961592"
---
# <a name="azure-stream-analytics-job-states"></a>Status för Azure Stream Analytics-jobb

Ett Stream Analytics-jobb kan vara i något av fyra tillstånd vid en given tidpunkt. Du hittar tillstånd jobbets på översiktssidan för ditt Stream Analytics-jobb i Azure-portalen. 

| Status | Beskrivning | Rekommenderade åtgärder |
| --- | --- | --- |
| **Körs** | Jobbet körs på Azure läsa händelser som kommer från de definierade indatakällorna, bearbeta dem och skriver resultatet till de konfigurerade utdatamottagarna. | Det är en bra idé att spåra prestanda på ditt jobb genom att övervaka [viktiga mått](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Stoppad** | Jobbet har stoppats och bearbeta inte händelser. | Ej tillämpligt | 
| **Försämrad** | Tillfälliga fel som sannolikt påverkar ditt jobb. Stream Analytics att omedelbart försöka återställa sådana fel och återgå till en Körstatus (inom några minuter). De här felen kan utföras på grund av nätverksproblem, tillgängligheten för andra Azure-resurser, deserialiseringsfel fel osv. Jobbets prestanda kan påverkas när jobbet är i degraderat tillstånd.| Du kan titta på den [diagnostik- eller aktivitet loggar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) mer information om orsaken till sådana tillfälliga fel. I fall, till exempel fel vid deserialisering av rekommenderas att vidta åtgärder för att säkerställa att händelser inte är felaktig. Om jobbet ser till att resursen användning gränsen, försöker öka antalet SU eller [parallellisera jobbet](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). I annat fall där du inte kan vidta några åtgärder, Stream Analytics kommer att försöka att återställa till en *kör* tillstånd.  |
| **Misslyckades** | Jobbet påträffade ett allvarligt fel, vilket resulterar i ett felaktigt tillstånd. Händelser inte läsa och bearbeta. Körningsfel är en vanlig orsak till jobb som slutar i ett felaktigt tillstånd. | Du kan [konfigurera aviseringar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) så att du meddelas när jobbet försätts i tillståndet misslyckad. <br> <br>Du kan felsöka med hjälp av [aktivitet och diagnostikloggar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) identifiera orsaken och åtgärda problemet.|

## <a name="next-steps"></a>Nästa steg
* [Konfigurera aviseringar för Azure Stream Analytics-jobb](stream-analytics-set-up-alerts.md)
* [Mått som är tillgängliga i Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Felsök med hjälp av aktivitet och diagnostikloggar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
