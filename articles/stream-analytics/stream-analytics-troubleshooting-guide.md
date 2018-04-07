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
ms.openlocfilehash: 44777946fdc829da222ffdd67dfecfa3bf240be7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Felsökningsguide för Azure Stream Analytics

Felsökning av Azure Stream Analytics kan verka vara besvärligt vid en första titt. Vi har skapat den här guiden för att förenkla processen och ta bort gissningar om indata, utdata, frågor och funktioner när du arbetar med många användare.

## <a name="troubleshoot-your-stream-analytics-job"></a>Felsöka Stream Analytics-jobbet

Använd följande riktlinjer för bästa resultat vid felsökning Stream Analytics-jobbet:

1.  Testa anslutningen:
    - Kontrollera anslutningen till in- och utdataenheter med hjälp av den **Testanslutningen** knappen för varje indata och utdata.

2.  Granska dina indata:
    - Använd för att kontrollera att indata flödar till Händelsehubben [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) att ansluta till Azure Event Hub (om Event Hub indata används).  
    - Använd den [ **exempeldata** ](stream-analytics-sample-data-input.md) knappen för varje indata och hämta inkommande exempeldata.
    - Inspektera exempeldata för att förstå formen av data: schemat och [datatyper](https://msdn.microsoft.com/library/azure/dn835065.aspx).

3.  Testa din fråga:
    - På den **frågan** använder den **testa** knappen Testa frågan och använda hämtade exempeldata till [testa frågan](stream-analytics-test-query.md). Granska eventuella fel och försöka korrigera dem.
    - Om du använder [ **Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), kontrollera att händelserna har tidsstämplar som är större än den [jobbet starttid](stream-analytics-out-of-order-and-late-events.md).

4.  Felsöka din fråga:
    - Återskapa frågan progressivt från enkla select-instruktion för mer komplexa mängder med hjälp av stegen. Du kan skapa fråga logiken steg för steg, Använd [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) satser.
    - Använd [SELECT INTO](stream-analytics-select-into.md) felsöka frågesteg.

5.  Undvika vanliga fallgropar som:
    - En [ **där** ](https://msdn.microsoft.com/library/azure/dn835048.aspx) -sats i frågan har filtrerats ut alla händelser som förhindrar att inga utdata som genereras.
    - När du använder Windows-funktioner, vänta tills hela fönstret varaktigheten Se utdata från frågan.
    - Tidsstämpel för händelser som föregår jobbets starttid och därför händelser släpps.

6.  Använd händelsen ordning:
    - Om de föregående stegen fungerade bra, går du till den **inställningar** och välj [ **händelse ordning**](stream-analytics-out-of-order-and-late-events.md). Kontrollera att den här principen är konfigurerad för vad klokt i ditt scenario. Principen är *inte* tillämpas när du använder den **testa** knappen för att testa frågan. Resultatet är en skillnaden mellan testning i webbläsaren och kör jobbet i produktion.

7.  Felsöka med hjälp av mätvärden:
    - Om du hämtar inga utdata när förväntad varaktighet (baserat på frågan) kan du testa följande:
        - Titta på [ **övervakningen mätvärdena** ](stream-analytics-monitoring.md) på den **övervakaren** fliken. Eftersom värdena aggregeras fördröjs mätvärdena med ett par minuter.
            - Om inkommande händelser > 0 jobbet kan läsa indata. Om händelser för indata inte är > 0:
                - Om du vill se om datakällan har giltiga data, kontrollerar du den med [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Den här kontrollen utförs om jobbet använder Event Hub som indata.
                - Kontrollera om data serialiseringsformat och datakodning är som förväntat.
                - Om jobbet använder en Händelsehubb, kontrollera om innehållet i meddelandet är *Null*.
            - Om Data Konverteringsfel > 0 och skapa följande kan vara sant:
                - Jobbet kan inte deserialisera händelser.
                - Händelseschemat överensstämmer inte med definierade eller förväntade schemat för händelser i frågan.
                - Datatyper i vissa fält kanske i inte stämmer förväntningar.
            - Om körningsfel > 0, innebär det att jobbet kan ta emot data men det uppstår fel under bearbetning av frågan.
                - Om du vill söka efter fel, gå till den [granskningsloggar](../azure-resource-manager/resource-group-audit.md) och filtrera på *misslyckades* status.
            - Om InputEvents > 0 och OutputEvents = 0, betyder det att något av följande är sant:
                - Frågebearbetningen resulterade i noll utdata-händelser.
                - Händelser eller dess fält kan vara felaktig, vilket resulterar i noll utdata efter bearbetning.
                - Jobbet kunde inte pusha data till den [utdatamottagaren](stream-analytics-select-into.md) av anslutning eller autentisering skäl.
        - I alla de tidigare nämnda fel fall operations loggmeddelanden beskrivs ytterligare information (inklusive vad som händer), förutom i fall där frågan logiken filtreras bort alla händelser. Om bearbetningen av flera händelser genererar fel, loggar Stream Analytics de första tre felmeddelandena av samma typ inom 10 minuter att Operations logs. Sedan undertrycks ytterligare identiska fel med meddelandet ”fel sker för snabbt, dessa är dolt”.

8. Felsöka med hjälp av gransknings- och diagnostikloggar:
    - Använd [granskningsloggar](../azure-resource-manager/resource-group-audit.md), och filter för att identifiera och felsöka fel.
    - Använd [jobbet diagnostikloggar](stream-analytics-job-diagnostic-logs.md) att identifiera och felsöka fel.

9. Granska utdata:
    - När jobbet har statusen är *kör*, beroende på den varaktighet som anges i frågan, kan du se utdata i datakällan mottagare.
    - Om du inte kan se utdata som kommer att en specifik utdatatypen omdirigera dem till Utdatatyp som är mindre komplex, till exempel en Azure-Blob. Kontrollera med hjälp av Lagringsutforskaren utdata kan visas. Kontrollera om bandbreddsbegränsning gränser på utdata förhindrar data tas emot.

10. Använd dataflöde analys med jobbet diagram:
    - Analysera dataflöde och identifiera problem genom att använda den [jobbet diagram med](stream-analytics-job-diagram-with-metrics.md).

11. Öppna ett supportärende:
    - Slutligen, om allt annat misslyckas, öppna ett supportärende för Microsoft använder prenumerations-ID som innehåller ditt jobb.

## <a name="get-help"></a>Få hjälp

För ytterligare hjälp försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
