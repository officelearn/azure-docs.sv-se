---
title: Felsökningsguide för Azure Stream Analytics
description: Den här artikeln beskrivs metoder för att felsöka Azure Stream Analytics-jobb, anslutningar, indata, utdata, frågor och data.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: b1b5d0af3f2b149959bcb97ddaf29ba2fe1f4668
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702229"
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Felsökningsguide för Azure Stream Analytics

Felsökning av Azure Stream Analytics kan verka vara besvärligt vid första anblicken. När du arbetar med många användare, har vi skapat den här guiden för att effektivisera processen och ta bort felsökning om indata, utdata, frågor och funktioner.

## <a name="troubleshoot-your-stream-analytics-job"></a>Felsöka ditt Stream Analytics-jobb

Använd följande riktlinjer för bästa resultat vid felsökning av ditt Stream Analytics-jobb:

1.  Testa anslutningen:
    - Kontrollera anslutningen till in- och utdata med hjälp av den **Testanslutningen** knappen för varje indata och utdata.

2.  Kontrollera dina indata:
    - Använd för att kontrollera att data flödar till Event Hub, [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) att ansluta till Azure Event Hub (om Händelsehubben indata används).  
    - Använd den [ **exempeldata** ](stream-analytics-sample-data-input.md) för varje indata och ladda ned exempeldata för ingången.
    - Gå igenom dina exempeldata för att förstå formen: schemat och [datatyper](https://msdn.microsoft.com/library/azure/dn835065.aspx).

3.  Testa frågan:
    - På den **fråga** fliken den **testa** för att testa frågan och använda nedladdade exempeldata för att [testa frågan](stream-analytics-test-query.md). Granska eventuella fel och försöka åtgärda dem.
    - Om du använder [ **Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), kontrollera att händelserna har tidsstämplar som är större än den [jobbet starttid](stream-analytics-out-of-order-and-late-events.md).

4.  Felsöka din fråga:
    - Återskapa frågan progressivt från enkla select-instruktioner till mer komplexa aggregeringar med hjälp av stegen. För att bygga upp frågelogiken steg för steg, använda [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) satser.
    - Använd [SELECT INTO](stream-analytics-select-into.md) felsöka frågesteg.

5.  Eliminera vanliga fallgropar, till exempel:
    - En [ **där** ](https://msdn.microsoft.com/library/azure/dn835048.aspx) sats i frågan filtrerade bort alla händelser som förhindrar att några utdata som genereras.
    - En [ **CAST** ](https://msdn.microsoft.com/azure/stream-analytics/reference/cast-azure-stream-analytics) misslyckas, vilket gör jobbet misslyckas. För att undvika fel av typen cast kan använda [ **TRY_CAST** ](https://msdn.microsoft.com/azure/stream-analytics/reference/try-cast-azure-stream-analytics) i stället.
    - När du använder fönsterfunktioner, vänta tills hela fönstret-tid för att se utdata från frågan.
    - Tidsstämpel för händelser som kommer före jobbets starttid och därför att händelser utelämnas.

6.  Använd Händelseordning:
    - Om de föregående stegen fungerade bra ska du gå till den **inställningar** bladet och välj [ **Händelseordning**](stream-analytics-out-of-order-and-late-events.md). Kontrollera att den här principen är konfigurerad för vad passar i ditt scenario. Principen är *inte* tillämpas när du använder den **testa** knappen för att testa frågan. Resultatet är en skillnad mellan att testa i webbläsaren och att köra jobbet i produktion.

7.  Felsöka med mått:
    - Om du hämtar inga utdata efter den förväntade tiden (baserat på frågan) kan du prova följande:
        - Titta på [ **Övervakningsmåtten** ](stream-analytics-monitoring.md) på den **övervakaren** fliken. Eftersom värdena aggregeras, fördröjs mått med några få minuter.
            - Om Indatahändelser > 0, jobbet ska kunna läsa indata. Om Indatahändelser inte är > 0:
                - Om du vill se om datakällan har giltiga data, kontrollera den med hjälp av [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Den här kontrollen utförs om jobbet använder Event Hub som indata.
                - Kontrollera om dataserialiseringsformatet och datakodning är som förväntat.
                - Om jobbet använder en Event Hub, kontrollerar du om meddelandets brödtext är *Null*.
            - Om datakonverteringsfel > 0 och stiger kan det bero på följande:
                - Jobbet kan inte deserialisera händelserna.
                - Händelseschemat kanske inte överensstämmer med den definierade eller förväntade händelseschemat i frågan.
                - Datatyper i vissa fält kanske i händelsen inte matchar förväntningar.
            - Om körningsfel > 0, innebär det att jobbet kan ta emot data men genererar fel när frågan bearbetas.
                - Du hittar felen genom att gå till den [granskningsloggar](../azure-resource-manager/resource-group-audit.md) och filtrerar på *misslyckades* status.
            - Om InputEvents > 0 och OutputEvents = 0, innebär det att något av följande är sant:
                - Frågebearbetningen resulterade i noll utdata-händelser.
                - Händelser eller dess fält kan vara felaktig, vilket resulterar i noll utdata efter frågebearbetning.
                - Jobbet kunde inte skicka data till den [utdatamottagare](stream-analytics-select-into.md) anslutning eller autentisering skäl.
        - I alla de tidigare nämnda fel fall loggmeddelandena förklara ytterligare information (inklusive vad som händer), utom i fall där frågans logik har filtrerat bort alla händelser. Om bearbetningen av flera händelser genererar fel loggar Stream Analytics de första tre felmeddelandena av samma typ inom 10 minuter till åtgärdsloggar. Den ignorerar sedan ytterligare identiska fel med meddelandet ”fel händer alltför snabbt dessa är dolt”.

8. Felsöka med hjälp av gransknings- och diagnostikloggar:
    - Använd [granskningsloggar](../azure-resource-manager/resource-group-audit.md), och filter för att identifiera och felsöka fel.
    - Använd [jobbet diagnostikloggar](stream-analytics-job-diagnostic-logs.md) att identifiera och felsöka fel.

9. Granska utdata:
    - När jobbet har statusen är *kör*, beroende på varaktigheten som stipulerats i frågan, du kan se utdata i datakällan mottagare.
    - Om du inte kan se utdata som håller på att en specifik Utdatatyp, omdirigera dem till en Utdatatyp som är mindre komplex, till exempel en Azure-Blob. Kontrollera att utdata kan ses genom att använda Storage Explorer. Kontrollera om begränsningar gränser i utdata att förhindra att data tas emot.

10. Använd dataflödet analys med jobbmått diagram:
    - Du kan analysera dataflödet och identifiera problem genom att använda den [jobbdiagram med mått](stream-analytics-job-diagram-with-metrics.md).

11. Öppna ett supportärende:
    - Slutligen, om allt annat misslyckas, öppna ett supportärende för Microsoft med hjälp av prenumerations-ID som innehåller ditt jobb.

## <a name="get-help"></a>Få hjälp

För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
