---
title: Väder prognos med data från IoT-hubb Azure Machine Learning | Microsoft Docs
description: Använd Azure Machine Learning att förutsäga risken för regn baserat på din IoT-hubb som samlar in från en sensor temperatur- och fuktighetskonsekvens data.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: väder prognos machine learning
ms.assetid: 8ba7d9e7-699c-4448-b353-0f3e1429d198
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 453b4de8a93e897b4455403855438d7705945514
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Väder prognos använder sensordata från IoT-hubb i Azure Machine Learning

![Diagram för slutpunkt till slutpunkt](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine learning är en teknik för datavetenskap som hjälper datorer lära sig från befintliga data att förutsäga framtida beteenden, resultat och trender. Azure Machine Learning är en molnbaserad tjänst för förutsägelseanalys som gör det möjligt att snabbt skapa och distribuera förutsägelsemodeller som analyslösningar.

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig hur du använder Azure Machine Learning att väder prognos (risken för regn) med hjälp av temperatur- och fuktighetskonsekvens data från Azure IoT-hubben. Risken för regn är resultatet av en modell för förberedda väder förutsägelse. Modellen bygger på historiska data för att bedöma risken för regn baserat på temperatur- och fuktighetskonsekvens.

## <a name="what-you-do"></a>Vad du gör

- Distribuera väder förutsägelse modellen som en webbtjänst.
- Förbereda din IoT-hubb för åtkomst till data genom att lägga till en konsumentgrupp.
- Skapa ett Stream Analytics-jobb och konfigurera jobbet är:
  - Läsa temperatur- och fuktighetskonsekvens data från IoT-hubb.
  - Anropa webbtjänsten för att få regn risken.
  - Spara resultatet till en Azure blob storage.
- Använda Microsoft Azure Lagringsutforskaren för att visa väder prognos.

## <a name="what-you-need"></a>Vad du behöver

- Kursen [konfigurera enheten](iot-hub-raspberry-pi-kit-node-get-started.md) slutförts som omfattar följande krav:
  - En aktiv Azure-prenumeration.
  - En Azure IoT-hubb i din prenumeration.
  - Ett klientprogram som skickar meddelanden till din Azure IoT-hubb.
- Ett konto i Azure Machine Learning Studio. ([Försök Machine Learning Studio gratis](https://studio.azureml.net/)).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Distribuera väder förutsägelse modellen som en webbtjänst

1. Gå till den [väder förutsägelse modellen sidan](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Klicka på **öppna i Studio** i Microsoft Azure Machine Learning Studio.
   ![Öppna sidan väder förutsägelse modellen i Cortana Intelligence Gallery](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Klicka på **kör** att validera stegen i modellen. Det här steget kan ta 2 minuter att slutföra.
   ![Öppna väder förutsägelse modellen i Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Klicka på **konfigurera WEBBTJÄNSTEN** > **förutsägande webbtjänsten**.
   ![Distribuera väder förutsägelse modellen i Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Dra i den den **Web service indata** modulen någonstans nära den **Poängmodell** modul.
1. Ansluta den **Web service indata** modulen till den **Poängmodell** modul.
   ![Ansluta två moduler i Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Klicka på **kör** att validera stegen i modellen.
1. Klicka på **DISTRIBUERA WEBBTJÄNSTEN** att distribuera modellen som en webbtjänst.
1. På instrumentpanelen i modellen, ladda ned den **Excel 2010 eller tidigare arbetsboken** för **frågor och svar**.

   > [!Note]
   > Se till att du hämtar den **Excel 2010 eller tidigare arbetsboken** även om du kör en senare version av Excel på datorn.

   ![Hämta Excel för REQUEST RESPONSE-slutpunkt](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Öppna Excel-arbetsboken, notera den **URL för WEBBTJÄNSTEN** och **ÅTKOMSTNYCKELN**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Skapa, konfigurera och köra ett Stream Analytics-jobb

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **skapar du en resurs** > **Sakernas Internet** > **Stream Analytics-jobbet**.
1. Ange följande information för jobbet.

   **Jobbnamnet**: namnet på jobbet. Namnet måste vara globalt unikt.

   **Resursgruppen**: använda samma resursgrupp som använder din IoT-hubb.

   **Plats**: använda samma plats som resursgruppen.

   **Fäst på instrumentpanelen**: Välj det här alternativet för enkel åtkomst till IoT-hubben från instrumentpanelen.

   ![Skapa ett Stream Analytics-jobb i Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

1. Öppna Stream Analytics-jobbet.
1. Under **jobbet topologi**, klickar du på **indata**.
1. I den **indata** rutan klickar du på **Lägg till**, och ange följande information:

   **Ett inmatat alias**: unika alias för indata.

   **Källan**: Välj **IoT-hubb**.

   **Konsumentgrupp**: Välj konsumentgrupp som du skapade.

   ![Lägga till indata till Stream Analytics-jobbet i Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Under **jobbet topologi**, klickar du på **utdata**.
1. I den **utdata** rutan klickar du på **Lägg till**, och ange följande information:

   **Kolumnalias**: unika alias för utdata.

   **Sink**: Välj **Blob Storage**.

   **Lagringskontot**: storage-konto för blobblagring. Du kan skapa ett lagringskonto eller använda en befintlig.

   **Behållaren**: behållaren där blob sparas. Du kan skapa en behållare eller använda en befintlig.

   **Händelsen serialiseringsformat**: Välj **CSV**.

   ![Lägga till utdata till Stream Analytics-jobbet i Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Lägga till en funktion i Stream Analytics-jobbet för att anropa webbtjänsten som du distribuerat

1. Under **jobbet topologi**, klickar du på **funktioner** > **Lägg till**.
1. Ange följande information:

   **Funktionen Alias**: Ange `machinelearning`.

   **Funktionen typen**: Välj **Azure ML**.

   **Importera alternativet**: Välj **importera från en annan prenumeration**.

   **URL: en**: Ange URL för WEBBTJÄNSTEN som du antecknade ned från Excel-arbetsboken.

   **Nyckeln**: Ange ÅTKOMSTNYCKEL som du antecknade ned från Excel-arbetsboken.

   ![Lägga till en funktion i Stream Analytics-jobbet i Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurera frågan i Stream Analytics-jobbet

1. Under **jobbet topologi**, klickar du på **frågan**.
1. Ersätt den befintliga koden med följande kod:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Ersätt `[YourInputAlias]` med indata alias för jobbet.

   Ersätt `[YourOutputAlias]` med utdataalias för jobbet.

1. Klicka på **Spara**.

### <a name="run-the-stream-analytics-job"></a>Kör Stream Analytics-jobbet

Klicka på i Stream Analytics-jobbet **starta** > **nu** > **starta**. När jobbet har startar jobbets status har ändrats från **stoppad** till **kör**.

![Kör Stream Analytics-jobbet](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Använda Microsoft Azure Lagringsutforskaren för att visa väder prognos

Köra klientprogram att börja samla in och skicka temperatur- och fuktighetskonsekvens data till din IoT-hubb. För varje meddelande som tar emot din IoT-hubb anropar Stream Analytics-jobbet väder prognos webbtjänsten för att skapa risken för regn. Resultatet sparas sedan till Azure blob storage. Azure Lagringsutforskaren är ett verktyg som du kan använda för att visa resultatet.

1. [Hämta och installera Microsoft Azure Lagringsutforskaren](http://storageexplorer.com/).
1. Öppna Utforskaren i Azure Storage.
1. Logga in på ditt Azure-konto.
1. Välj din prenumeration.
1. Klicka på din prenumeration > **Lagringskonton** > ditt lagringskonto > **Blob-behållare** > din behållare.
1. Öppna en CSV-fil om du vill se resultatet. Den sista kolumnen registrerar risken för regn.

   ![Få väder prognos resultat med Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Sammanfattning

Du har använt Azure Machine Learning risken för regn baserat på de temperatur- och fuktighetskonsekvens som tar emot din IoT-hubb gav inga.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]