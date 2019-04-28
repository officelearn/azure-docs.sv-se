---
title: Testa realtidsdata med Azure Stream Analytics för Visual Studio
description: Lär dig hur du testar din Azure Stream Analytics-jobb som lokalt med liveuppspelningsdata.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: ea55d2f96a87503d43a69d288ce85dcff32a39ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479838"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Testa realtidsdata lokalt med hjälp av Azure Stream Analytics-verktyg för Visual Studio (förhandsversion)

Azure Stream Analytics-verktyg för Visual Studio kan du testa jobb lokalt från IDE med live-händelse dataströmmar från Azure Event Hub, IoT Hub och Blob Storage. Realtidsdata lokal testning inte kan byta ut den [testa skalbarhet och prestanda](stream-analytics-streaming-unit-consumption.md) du kan utföra i molnet, men du kan spara tid under funktionell testning genom att inte behöva skicka till molnet varje gång som du vill testa din Stream Analytics-jobb. Den här funktionen är en förhandsversion och bör inte användas för produktionsarbetsbelastningar.

## <a name="testing-options"></a>Alternativ för test

Följande alternativ för lokal testning stöds:

|**Indata**  |**Resultat**  |**Jobbtyp**  |
|---------|---------|---------|
|Lokala statiska data   |  Lokala statiska data   |   Molnet/Edge |
|Live-indata   |  Lokala statiska data   |   Molnet |
|Live-indata   |  Live-utdata   |   Molnet |

## <a name="local-testing-with-live-data"></a>Lokal testning med realtidsdata

1. När du har skapat en [Azure Stream Analytics cloud-projekt i Visual Studio](stream-analytics-quick-create-vs.md)öppnar **script.asaql**. Lokal testning använder lokal indata och utdata för lokala som standard.

   ![Azure Stream Analytics Visual Studio lokal indata och utdata för lokala](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Om du vill testa realtidsdata, Välj **Använd Cloud indata** från listrutan.

   ![Azure Stream Analytics Visual Studio cloud live-inmatning](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)


3. Ange den **starttid** att definiera när jobbet börjar bearbeta inmatade data. Jobbet kan behöva läsa indata i tid för att se till att korrekta resultat. Standardtid har angetts till 30 minuter före aktuell tid.

   ![Azure Stream Analytics Visual Studio-realtidsdata starttid](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Klicka på **kör lokalt**. Ett konsolfönster visas med pågående förlopp och jobbet mått. Om du vill stoppa processen kan göra du det manuellt. 

   ![Azure Stream Analytics Visual Studio realtidsdata processen fönster](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Resultatet uppdateras var tredje sekund med de första 500 utdata raderna i fönstret Lokal körningen och utdatafilerna placeras i din Projektsökväg **ASALocalRun** mapp. Du kan också öppna utdatafilerna genom att klicka på **öppna resultat-mappen** knappen i fönstret Lokal körningen.

   ![Azure Stream Analytics Visual Studio-realtidsdata öppna resultat-mappen](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Om du vill spara resultaten till din cloud utdatamottagarna väljer **utdata till molnet** från den andra listrutan. Powerbi och Azure Data Lake Storage är inte stöds utdatamottagarna.

   ![Azure Stream Analytics Visual Studio-realtidsdata som utdata till molnet](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Begränsningar

* Powerbi och Azure Data Lake Storage är inte stöds utdatamottagarna på grund av begränsningar för autentisering-modellen.

* Endast inkommande molnalternativ har [tid principer](stream-analytics-out-of-order-and-late-events.md) stöd, medan lokala inkommande alternativ inte.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Stream Analytics-jobb med hjälp av Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-quick-create-vs.md)
* [Installera Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-tools-for-visual-studio-install.md)
* [Testa Stream Analytics-frågor lokalt med Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)