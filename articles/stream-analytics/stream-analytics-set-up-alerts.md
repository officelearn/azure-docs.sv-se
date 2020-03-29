---
title: Konfigurera övervakningsaviseringar för Azure Stream Analytics-jobb
description: I den här artikeln beskrivs hur du använder Azure-portalen för att konfigurera övervakning och aviseringar för Azure Stream Analytics-jobb.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 836b7a489e3c73d745b128cbbc0c3566220ac409
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458735"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Konfigurera aviseringar för Azure Stream Analytics-jobb

Det är viktigt att övervaka ditt Azure Stream Analytics-jobb för att säkerställa att jobbet körs kontinuerligt utan problem. I den här artikeln beskrivs hur du ställer in aviseringar för vanliga scenarier som ska övervakas. 

Du kan definiera regler för mått från operationsloggar data via portalen, samt [programmässigt](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a).

## <a name="set-up-alerts-in-the-azure-portal"></a>Konfigurera aviseringar i Azure-portalen
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Bli aviserad när ett jobb stoppas oväntat

I följande exempel visas hur du ställer in aviseringar för när jobbet går in i ett misslyckat tillstånd. Den här aviseringen rekommenderas för alla jobb.

1. Öppna det Stream Analytics-jobb som du vill skapa en avisering för i Azure-portalen.

2. Navigera till avsnittet **Övervakning** på sidan **Jobb.**  

3. Välj Mått och sedan **Ny varningsregel**. **Metrics**

   ![Installation av azure portal Stream Analytics-aviseringar](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. Ditt Stream Analytics-jobbnamn ska visas automatiskt under **RESURS**. Klicka på **Lägg till villkor**och välj Alla administrativa **åtgärder** under **Konfigurera signallogik**.

   ![Välj signalnamn för Stream Analytics-avisering](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. Under **Konfigurera signallogik**ändrar du **händelsenivå** till **alla** och ändrar **status** till **Misslyckades**. Lämna **händelsen initierad av** tom och välj **Klar**.

   ![Konfigurera signallogik för Stream Analytics-avisering](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Välj en befintlig åtgärdsgrupp eller skapa en ny grupp. I det här exemplet skapades en ny åtgärdsgrupp med namnet **TIDashboardGroupActions** med en **e-poståtgärd** som skickar ett e-postmeddelande till användare med **rollen Owner** Azure Resource Manager.

   ![Konfigurera en avisering för ett Azure Streaming Analytics-jobb](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. **Resurs-,** **VILLKORs-** och **ÅTGÄRDSGRUPPERna** ska ha varsin post. Observera att för att larmen ska kunna avfyras måste de definierade villkoren vara uppfyllda. Du kan till exempel mäta medelvärdet för ett mått för de senaste 15 minuterna, var femte minut.

   ![Varningsregel för Skapa Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Lägg till ett **aviseringsregelnamn,** **Beskrivning**och **din resursgrupp** i **AVISERINGSINFORMATIONEN** och klicka på **Skapa aviseringsregel** för att skapa regeln för ditt Stream Analytics-jobb.

   ![Varningsregel för Skapa Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Scenarier att övervaka

Följande aviseringar rekommenderas för att övervaka resultatet för ditt Stream Analytics-jobb. Dessa mått bör utvärderas varje minut under den senaste 5-minutersperioden.

|Mått|Villkor|Tid aggregering|Tröskelvärde|Korrigerande åtgärder|
|-|-|-|-|-|
|SU% Utnyttjande|Större än|Maximal|80|Det finns flera faktorer som ökar SU% Utnyttjande. Du kan skala med frågeparallalisering eller öka antalet strömningsenheter. Mer information finns i [Utnyttja frågeparallellisering i Azure Stream Analytics](stream-analytics-parallelization.md).|
|Körningsfel|Större än|Totalt|0|Undersök aktivitets- eller diagnostikloggarna och gör lämpliga ändringar i indata, frågor eller utdata.|
|Fördröjning av vattenstämpel|Större än|Maximal|När det genomsnittliga värdet för det här måttet under de senaste 15 minuterna är större än toleransen för sen ankomst (i sekunder). Om du inte har ändrat toleransen för sen ankomst är standardinställningen 5 sekunder.|Försök att öka antalet SUs eller parallellisera din fråga. Mer information om SUs finns i [Förstå och justera strömningsenheter](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Mer information om hur du parallelliserar frågan finns [i Utnyttja frågeallallalisering i Azure Stream Analytics](stream-analytics-parallelization.md).|
|Fel vid indataserialisering|Större än|Totalt|0|Undersök aktiviteten eller diagnostikloggarna och gör lämpliga ändringar i indata. Mer information om diagnostikloggar finns i [Felsöka Azure Stream Analytics med hjälp av diagnostikloggar](stream-analytics-job-diagnostic-logs.md)|

## <a name="get-help"></a>Få hjälp

Mer information om hur du konfigurerar aviseringar i Azure-portalen finns i [Ta emot aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

Om du vill ha mer hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-get-started.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)

