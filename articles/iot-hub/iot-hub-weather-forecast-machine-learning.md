---
title: Väderprognoser med hjälp av Azure Machine Learning med data från IoT Hub | Microsoft Docs
description: Använd Azure Machine Learning för att förutsäga risken för regn baserat på informationen temperatur- och IoT-hubben som samlar in från en sensor.
author: robinsh
manager: philmea
keywords: väderprognos maskininlärning
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: a203018cb1a27c17b9c7162b50a380f9edae2e60
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273200"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Väderprognoser med sensordata från IoT hub i Azure Machine Learning

![Slutpunkt till slutpunkt-diagram](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine learning är en datavetenskapsteknik som hjälper datorer att lära sig från befintliga data för att skapa prognoser för framtida beteenden, resultat och trender. Azure Machine Learning är en molnbaserad tjänst för förutsägelseanalys som gör det möjligt att snabbt skapa och distribuera förutsägelsemodeller som analyslösningar.

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig hur du använder Azure Machine Learning för att väder prognosen (risken för regn) med hjälp av temperatur och fuktighet data från Azure IoT hub. Risken för regn är utdata från en förberedd väder förutsägelsemodell. Modellen bygger på historiska data till att prognostisera risken för regn utifrån temperatur och fuktighet.

## <a name="what-you-do"></a>Vad du gör

- Distribuera väder förutsägelsemodell som en webbtjänst.
- Förbereda din IoT-hubb för åtkomst till data genom att lägga till en konsumentgrupp.
- Skapa ett Stream Analytics-jobb och konfigurera jobbet är:
  - Läsa temperatur- och data från din IoT-hubb.
  - Anropa webbtjänsten för att få regnchans.
  - Spara resultatet i en Azure blob storage.
- Använda Microsoft Azure Storage Explorer för att visa väderprognosen.

## <a name="what-you-need"></a>Vad du behöver

- Självstudien [konfigurera enheten](iot-hub-raspberry-pi-kit-node-get-started.md) slutförts som omfattar följande krav:
  - En aktiv Azure-prenumeration.
  - Azure IoT hub i din prenumeration.
  - Ett klientprogram som skickar meddelanden till din Azure IoT hub.
- Ett Azure Machine Learning Studio-konto. ([Testa Machine Learning Studio kostnadsfritt](https://studio.azureml.net/)).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Distribuera väder förutsägelsemodell som en webbtjänst

1. Gå till den [väder förutsägelse sidan](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Klicka på **öppna i Studio** i Microsoft Azure Machine Learning Studio.
   ![Öppna sidan väder förutsägande modell i Cortana Intelligence-galleriet](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Klicka på **kör** att validera stegen i modellen. Det här steget kan ta 2 minuter att slutföra.
   ![Öppna väder förutsägelse modellen i Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Klicka på **konfigurera WEBBTJÄNSTEN** > **Förutsägelsewebbtjänst**.
   ![Distribuera väder förutsägande modell i Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. I diagrammet, drar den **Web service indata** modulen någonstans nära den **poängsätta modell** modulen.
1. Anslut den **Web service indata** modul till den **poängsätta modell** modulen.
   ![Ansluta två moduler i Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Klicka på **kör** att validera stegen i modellen.
1. Klicka på **DISTRIBUERA WEBBTJÄNSTEN** för distribution av modellen som en webbtjänst.
1. På instrumentpanelen för modellen, ladda ned den **Excel 2010 eller tidigare arbetsbok** för **begäran/svar**.

   > [!Note]
   > Se till att du hämtar den **Excel 2010 eller tidigare arbetsbok** även om du använder en senare version av Excel på datorn.

   ![Ladda ned Excel för slutpunkten som svar på begäran](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Öppna Excel-arbetsboken, notera den **Webbtjänstadress** och **ÅTKOMSTNYCKEL**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Skapa, konfigurera och köra ett Stream Analytics-jobb

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I [Azure Portal](https://portal.azure.com/) klickar du på **Skapa en resurs** > **Sakernas Internet** > **Stream Analytics-jobb**.
1. Ange följande information för jobbet.

   **Jobbnamn**: Namnet på jobbet. Namnet måste vara globalt unikt.

   **Resursgrupp**: Använd samma resursgrupp som din IoT-hubb använder.

   **Plats**: Använd samma plats som resursgruppen.

   **Fäst på instrumentpanelen**: Markera det här alternativet för enkel åtkomst till IoT-hubben från instrumentpanelen.

   ![Skapa ett Stream Analytics-jobb i Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

1. Öppna Stream Analytics-jobb.
1. Klicka på **Indata** under **Jobbtopologi**.
1. I den **indata** fönstret klickar du på **Lägg till**, och ange sedan följande information:

   **Indataalias**: Det unika aliaset för indata.

   **Källa**: Välj **IoT-hubb**.

   **Konsumentgrupp**: Välja konsumentgrupp som du skapade.

   ![Lägg till indata för Stream Analytics-jobb i Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Under **Jobbtopologi** klickar du på **Utdata**.
1. I den **utdata** fönstret klickar du på **Lägg till**, och ange sedan följande information:

   **Utdataalias**: Utdatas unika alias.

   **Mottagare**: Välj **Blob-lagring**.

   **Lagringskonto**: Storage-konto för blob storage. Du kan skapa ett lagringskonto eller Använd en befintlig.

   **behållaren**: Behållaren där bloben har sparats. Du kan skapa en behållare eller Använd en befintlig.

   **Händelseserialiseringsformat**: Välj **CSV**.

   ![Lägg till utdata till Stream Analytics-jobbet i Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Lägga till en funktion till Stream Analytics-jobbet för att anropa webbtjänsten som du har distribuerat

1. Under **Jobbtopologi**, klickar du på **Functions** > **Lägg till**.
1. Ange följande information:

   **Funktionen Alias**: Ange `machinelearning`.

   **Typen**: Välj **Azure ML**.

   **Importalternativ**: Välj **Import från en annan prenumeration**.

   **URL**: Ange WEBBTJÄNSTENS URL som du antecknade från Excel-arbetsboken.

   **nyckeln**: Ange ÅTKOMSTNYCKEL som du antecknade från Excel-arbetsboken.

   ![Lägga till en funktion till Stream Analytics-jobbet i Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurera frågan för Stream Analytics-jobbet

1. Under **Jobbtopologi** klickar du på **Fråga**.
1. Ersätt den befintliga koden med följande kod:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Ersätt `[YourInputAlias]` med inmatat alias för jobbet.

   Ersätt `[YourOutputAlias]` med utdataalias för jobbet.

1. Klicka på **Spara**.

### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

I Stream Analytics-jobbet klickar du på **Starta** > **Nu** > **Starta**. När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

![Köra Stream Analytics-jobbet](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Använd Microsoft Azure Storage Explorer för att visa väderprognosen

Kör klientprogrammet att börja samla in och skicka temperatur och fuktighet data till din IoT-hubb. Stream Analytics-jobbet anropar väderprognos webbtjänsten för att generera risken för regn för varje meddelande som din IoT-hubb tar emot. Resultatet sparas sedan i ditt Azure blob storage. Azure Storage Explorer är ett verktyg som du kan använda för att visa resultatet.

1. [Ladda ned och installera Microsoft Azure Lagringsutforskaren](https://storageexplorer.com/).
1. Öppna Azure Storage Explorer.
1. Logga in på ditt Azure-konto.
1. Välj din prenumeration.
1. Klicka på din prenumeration > **Lagringskonton** > ditt lagringskonto > **Blobbehållare** > din behållare.
1. Öppna en .csv-fil för att se resultatet. Den sista kolumnen innehåller information om risken för regn.

   ![Få väderprognoser resultat med Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Sammanfattning

Du har har använt Azure Machine Learning för att producera risken för regn utifrån temperatur- och data som din IoT-hubb tar emot.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]