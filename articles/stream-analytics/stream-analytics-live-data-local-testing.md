---
title: Testa livedata med Azure Stream Analytics för Visual Studio
description: Lär dig hur du testar ditt Azure Stream Analytics-jobb lokalt med hjälp av direktuppspelningsdata.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: f2876ea32bdcd900a454ae6b7ac58c11b8ec67c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840493"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Testa live-data lokalt med Hjälp av Azure Stream Analytics-verktyg för Visual Studio (förhandsversion)

Med Azure Stream Analytics-verktyg för Visual Studio kan du testa jobb lokalt från IDE med hjälp av livehändelseströmmar från Azure Event Hub, IoT Hub och Blob Storage. Lokala tester av livedata kan inte ersätta de [prestanda- och skalbarhetstester](stream-analytics-streaming-unit-consumption.md) som du kan utföra i molnet, men du kan spara tid under funktionstestning genom att inte behöva skicka till molnet varje gång du vill testa ditt Stream Analytics-jobb. Den här funktionen är i förhandsversion och bör inte användas för produktionsarbetsbelastningar.

## <a name="testing-options"></a>Testa alternativ

Följande lokala testalternativ stöds:

|**Input**  |**Produktionen**  |**Jobbtyp**  |
|---------|---------|---------|
|Lokala statiska data   |  Lokala statiska data   |   Moln/kant |
|Live-indata   |  Lokala statiska data   |   Molnet |
|Live-indata   |  Data för liveutdata   |   Molnet |

## <a name="local-testing-with-live-data"></a>Lokal testning med livedata

1. När du har skapat ett [Azure Stream Analytics-molnprojekt i Visual Studio](stream-analytics-quick-create-vs.md)öppnar du **script.asaql**. Den lokala testningen använder lokala indata och lokala utdata som standard.

   ![Azure Stream Analytics Visual Studio lokal indata och lokal utdata](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Om du vill testa realtidsdata väljer du **Använd molnindata** i listrutan.

   ![Azure Stream Analytics Visual Studio live molnindata](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)

3. Ange **starttid** för att definiera när jobbet ska börja bearbeta indata. Jobbet kan behöva läsa indata i förväg för att säkerställa korrekta resultat. Standardtiden är inställd på 30 minuter före den aktuella tiden.

   ![Starttid för Azure Stream Analytics Visual Studio live data](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Klicka på **Kör lokalt**. Ett konsolfönster visas med statusvärden för körning och jobb. Om du vill stoppa processen kan du göra det manuellt. 

   ![Fönstret Azure Stream Analytics Visual Studio live data process fönster](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Utdataresultaten uppdateras var tredje sekund med de första 500 utdataraderna i det lokala körningsresultatfönstret och utdatafilerna placeras i projektsökvägen **ASALocalRun-mappen.** Du kan också öppna utdatafilerna genom att klicka på Knappen **Öppna resultatmapp** i det lokala resultatfönstret.

   ![Azure Stream Analytics Visual Studio live data öppna resultat mapp](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Om du vill mata ut resultaten till dina molnutdatamottagare väljer du **Utdata till molnet** från den andra listrutan. Power BI och Azure Data Lake Storage stöds inte utdatamottagare.

   ![Azure Stream Analytics Visual Studio live datautdata till molnet](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Begränsningar

* Power BI och Azure Data Lake Storage stöds inte utdatamottagare på grund av begränsningar för autentiseringsmodell.

* Endast molnindataalternativ har stöd för [tidsprinciper,](stream-analytics-out-of-order-and-late-events.md) medan lokala inmatningsalternativ inte gör det.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Stream Analytics-jobb med hjälp av Azure Stream Analytics-verktygen för Visual Studio](stream-analytics-quick-create-vs.md)
* [Testa Stream Analytics-frågor lokalt med Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
