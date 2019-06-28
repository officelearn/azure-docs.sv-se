---
title: Status för Azure Stream Analytics-jobb
description: Den här artikeln beskrivs olika lägen för ett Stream Analytics-jobb
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: bef21dc35bbd2b9b50cf7b362624321866773bfe
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331343"
---
# <a name="azure-stream-analytics-job-states"></a>Status för Azure Stream Analytics-jobb

Ett Stream Analytics-jobb kan vara i något av fyra tillstånd vid en given tidpunkt. Du hittar tillstånd jobbets på översiktssidan för ditt Stream Analytics-jobb i Azure-portalen. 

| Status | Beskrivning | Rekommenderade åtgärder |
| --- | --- | --- |
| **Körs** | Jobbet körs på Azure läsa händelser som kommer från de definierade indatakällorna, bearbeta dem och skriver resultatet till de konfigurerade utdatamottagarna. | Det är en bra idé att spåra prestanda på ditt jobb genom att övervaka [viktiga mått](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Stoppad** | Jobbet har stoppats och bearbeta inte händelser. | Saknas | 
| **Försämrad** | Det kan finnas återkommande problem med dina inkommande och utgående anslutningar. Dessa fel kallas tillfälligt fel som skulle kunna göra jobbet anger statusen degraderad. Stream Analytics att omedelbart försöka återställa sådana fel och återgå till en Körstatus (inom några minuter). De här felen kan utföras på grund av nätverksproblem, tillgängligheten för andra Azure-resurser, deserialiseringsfel fel osv. Jobbets prestanda kan påverkas när jobbet är i degraderat tillstånd.| Du kan titta på den [diagnostik- eller aktivitet loggar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) mer information om orsaken till sådana tillfälliga fel. I fall, till exempel fel vid deserialisering av rekommenderas att vidta åtgärder för att säkerställa att händelser inte är felaktig. Om jobbet ser till att resursen användning gränsen, försöker öka antalet SU eller [parallellisera jobbet](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). I annat fall där du inte kan vidta några åtgärder, Stream Analytics kommer att försöka att återställa till en *kör* tillstånd. <br> Du kan använda [vattenstämpel fördröjning](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) mått att förstå om sådana tillfälliga fel som påverkar prestanda för jobbets.|
| **Misslyckades** | Jobbet påträffade ett allvarligt fel, vilket resulterar i ett felaktigt tillstånd. Händelser inte läsa och bearbeta. Körningsfel är en vanlig orsak till jobb som slutar i ett felaktigt tillstånd. | Du kan [konfigurera aviseringar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) så att du meddelas när jobbet försätts i tillståndet misslyckad. <br> <br>Du kan felsöka med hjälp av [aktivitet och diagnostikloggar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) identifiera orsaken och åtgärda problemet.|

## <a name="next-steps"></a>Nästa steg
* [Konfigurera aviseringar för Azure Stream Analytics-jobb](stream-analytics-set-up-alerts.md)
* [Mått som är tillgängliga i Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Felsök med hjälp av aktivitet och diagnostikloggar](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
