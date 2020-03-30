---
title: Felsöka Azure Stream Analytics-frågor lokalt med hjälp av jobbdiagram i Visual Studio
description: I den här artikeln beskrivs hur du felsöker frågor lokalt med hjälp av jobbdiagram i Azure Stream Analytics Tools för Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 106b1f0b765700803d2cd55b5e049fae5be3dfad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76847204"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Felsöka Azure Stream Analytics-frågor lokalt med hjälp av jobbdiagram i Visual Studio

Jobb som inte ger ut något resultat eller oväntade resultat är vanliga felsökningsscenarier för direktuppspelningsfrågor. Du kan använda jobbdiagrammet när du testar frågan lokalt i Visual Studio för att undersöka mellanliggande resultatuppsättning och mått för varje steg. Jobbdiagram kan hjälpa dig att snabbt isolera källan till ett problem när du felsöker problem.

## <a name="debug-a-query-using-job-diagram"></a>Felsöka en fråga med hjälp av jobbdiagram

Ett Azure Stream Analytics-skript används för att omvandla indata till utdata. Jobbdiagrammet visar hur data flödar från indatakällor (Event Hub, IoT Hub, etc.) genom flera frågesteg och slutligen till utdatamottagare. Varje frågesteg mappas till en tillfällig resultatuppsättning `WITH` som definieras i skriptet med hjälp av en sats. Du kan visa data samt mått för varje frågesteg i varje mellanliggande resultatuppsättning för att hitta källan till ett problem.

> [!NOTE]
> Det här jobbdiagrammet visar bara data och mått för lokal testning i en enda nod. Den bör inte användas för prestandajustering och felsökning.

### <a name="start-local-testing"></a>Starta lokala tester

Använd den här [snabbstarten](stream-analytics-quick-create-vs.md) om du vill lära dig hur du skapar ett Stream Analytics-jobb med Visual Studio eller [exporterar ett befintligt jobb till ett lokalt projekt](stream-analytics-vs-tools.md#export-jobs-to-a-project). Om du vill testa frågan med lokala indata följer du dessa [instruktioner](stream-analytics-live-data-local-testing.md). Om du vill testa med live-indata går du vidare till nästa steg.

> [!NOTE]
> Om du exporterar ett jobb till ett lokalt projekt och vill testa mot en direkt indataström måste du ange autentiseringsuppgifterna för alla indata igen.  

Välj in- och utdatakällan från skriptredigeraren och välj **Kör lokalt**. Jobbdiagrammet visas till höger.

### <a name="view-the-intermediate-result-set"></a>Visa mellanliggande resultatuppsättning  

1. Markera frågesteget för att navigera till skriptet. Du dirigeras automatiskt till motsvarande skript i redigeraren till vänster.

   ![Jobbdiagram navigera skript](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Markera frågesteget och välj **Förhandsgranska** i dialogrutan poppade. Resultatuppsättningen visas på en flik i det nedre resultatfönstret.

   ![Förhandsgranskningsresultat för jobbdiagram](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Visa stegmått

I det här avsnittet utforskar du de mått som är tillgängliga för varje del av diagrammet.

#### <a name="input-sources-live-stream"></a>Indatakällor (Live stream)

![Live-indatakällor för jobbdiagram](./media/debug-locally-using-job-diagram/live-input.png)

|Mått|Beskrivning|
|-|-|
|**TaxiRide (TaxiRide)**| Namnet på indata.|
|**Händelsehubben** | Typ av indatakälla.|
|**Händelser**|Antalet händelser som läss.|
|**Eftersläpade händelsekällor**|Hur många fler meddelanden måste läsas för eventhubbar och IoT Hub-indata.|
|**Händelser i byte**|Antalet lästa byte.|
| **Försämrade händelser**|Antalet händelser som hade ett annat problem än med deserialisering.|
|**Tidiga evenemang**| Antalet händelser som har en programtidsstämpel före den höga vattenstämpeln.|
|**Sena händelser**| Antalet händelser som har en programtidsstämpel efter den höga vattenstämpeln.|
|**Händelsekällor**| Antalet lästa dataenheter. Till exempel antalet blobbar.|

#### <a name="input-sources-local-input"></a>Indatakällor (Lokal inmatning)

![Lokala indatakällor för jobbdiagram](./media/debug-locally-using-job-diagram/local-input.png)

|Mått|Beskrivning|
|-|-|
|**TaxiRide (TaxiRide)**| Namnet på indata.|
|**Radantal**| Antalet rader som genereras från steget.|
|**Datastorlek**| Storleken på data som genereras från det här steget.|
|**Lokal inmatning**| Använd lokala data som indata.|

#### <a name="query-steps"></a>Frågesteg

![Frågesteg för jobbdiagram](./media/debug-locally-using-job-diagram/query-step.png)

|Mått|Beskrivning|
|-|-|
|**TripData (På ett år)**|Namnet på den tillfälliga resultatuppsättningen.|
|**Radantal**| Antalet rader som genereras från steget.|
|**Datastorlek**| Storleken på data som genereras från det här steget.|
  
#### <a name="output-sinks-live-output"></a>Utdatamottagare (direktutgång)

![Lokal utdatamottagare för jobbdiagram](./media/debug-locally-using-job-diagram/live-output.png)

|Mått|Beskrivning|
|-|-|
|**regionaggEH**|Namnet på utdata.|
|**Händelser**|Antalet händelser som matas ut till sänkor.|

#### <a name="output-sinks-local-output"></a>Utdatamottagare (lokal utdata)

![Lokal utdatamottagare för jobbdiagram](./media/debug-locally-using-job-diagram/local-output.png)

|Mått|Beskrivning|
|-|-|
|**regionaggEH**|Namnet på utdata.|
|**Lokal utdata**| Resultatutdata till en lokal fil.|
|**Radantal**| Antalet rader som matas ut till den lokala filen.|
|**Datastorlek**| Storleken på datautdata till den lokala filen.|

### <a name="close-job-diagram"></a>Stäng jobbdiagram

Om du inte längre behöver jobbdiagrammet väljer du **Stäng** i det övre högra hörnet. När du har stängt diagramfönstret måste du starta lokala tester igen för att se det.

### <a name="view-job-level-metrics-and-stop-running"></a>Visa projektnivåmått och sluta köra

Andra jobbnivåmått visas i popup-konsolen. Tryck på **Ctrl+C** i konsolen om du vill stoppa jobbet.

![Stoppjobb för jobbdiagram](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Begränsningar

* Power BI- och Azure Data Lake Storage Gen1-uttännmottagare stöds inte på grund av begränsningar för autentiseringsmodell.

* Endast molnindataalternativ har stöd för [tidsprinciper,](stream-analytics-out-of-order-and-late-events.md) medan lokala inmatningsalternativ inte gör det.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Stream Analytics-jobb med Visual Studio](stream-analytics-quick-create-vs.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
* [Testa live-data lokalt med Hjälp av Azure Stream Analytics-verktyg för Visual Studio (förhandsversion)](stream-analytics-live-data-local-testing.md)
