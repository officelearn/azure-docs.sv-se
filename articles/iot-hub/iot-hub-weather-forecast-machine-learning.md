---
title: Väder prognoser med Azure Machine Learning med IoT Hub data
description: Använd Azure Machine Learning för att förutsäga risken för regn utifrån temperatur-och fuktighets data som din IoT Hub samlar in från en sensor.
author: robinsh
manager: philmea
keywords: Väder prognoser maskin inlärning
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: d26ccd47ada4f1f1fd87f315e05f822bb2463114
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976187"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Väder prognoser med sensor data från din IoT Hub i Azure Machine Learning

![Diagram från slut punkt till slut punkt](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine Learning är en teknik för data vetenskap som hjälper datorer att lära sig från befintliga data för att förutse framtida beteenden, resultat och trender. Azure Machine Learning är en molnbaserad tjänst för förutsägelseanalys som gör det möjligt att snabbt skapa och distribuera förutsägelsemodeller som analyslösningar.

## <a name="what-you-learn"></a>Detta får du får lära dig

Du får lära dig hur du använder Azure Machine Learning för att göra väder prognoser (en chans för regn) med hjälp av temperatur-och fuktighets data från din Azure IoT Hub. Risken för regn är resultatet av en beredd väder förutsägelse modell. Modellen bygger på historiska data och prognoserar risken för regn utifrån temperatur och fuktighet.

## <a name="what-you-do"></a>Vad du gör

- Distribuera väder förutsägelse modellen som en webb tjänst.
- Få din IoT Hub redo för data åtkomst genom att lägga till en konsument grupp.
- Skapa ett Stream Analytics jobb och konfigurera jobbet för att:
  - Läsa temperatur-och fuktighets data från din IoT Hub.
  - Ring webb tjänsten för att få en regn chans.
  - Spara resultatet i Azure Blob Storage.
- Använd Microsoft Azure Storage Explorer för att Visa väder prognosen.

## <a name="what-you-need"></a>Vad du behöver

- Slutför själv studie kursen om [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) eller någon av enhets självstudierna. till exempel [Raspberry Pi med Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Detta beskriver följande krav:
  - En aktiv Azure-prenumeration.
  - En Azure IoT-hubb under din prenumeration.
  - Ett klient program som skickar meddelanden till Azure IoT Hub.
- Ett [Azure Machine Learning Studio (klassiskt)](https://studio.azureml.net/) konto.

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Distribuera väder förutsägelse modellen som en webb tjänst

1. Gå till [sidan för väder förutsägelse modellen](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Klicka på **Öppna i Studio** i Microsoft Azure Machine Learning Studio (klassisk).
   ![öppna sidan för väder förutsägelse modell i Cortana Intelligence Gallery](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Verifiera stegen i modellen genom att klicka på **Kör** . Det här steget kan ta 2 minuter att slutföra.
   ![öppna väder förutsägelse modellen i Azure Machine Learning Studio (klassisk)](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Klicka på **Konfigurera webb tjänsten** > **förutsägbar webb tjänst**.
   ![distribuera väder förutsägelse modellen i Azure Machine Learning Studio (klassisk)](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. I diagrammet drar du modulen för **webb tjänst Indataporten** någonstans nära **Poäng modell** .
1. Anslut **webb tjänstens indataport** till **score modell** -modulen.
   ![ansluta två moduler i Azure Machine Learning Studio (klassisk)](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Verifiera stegen i modellen genom att klicka på **Kör** .
1. Klicka på **distribuera webb tjänst** för att distribuera modellen som en webb tjänst.
1. På instrument panelen för modellen laddar du ned **Excel 2010 eller tidigare arbets bok** för **begäran/svar**.

   > [!Note]
   > Se till att du hämtar **excel 2010 eller tidigare arbets bok** även om du kör en senare version av Excel på datorn.

   ![Ladda ned Excel för slut punkten för begär ande svar](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Öppna Excel-arbetsboken och anteckna **webb tjänstens URL** och **åtkomst nyckeln**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Skapa, konfigurera och köra ett Stream Analytics jobb

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I [Azure Portal](https://portal.azure.com/) klickar du på **Skapa en resurs** > **Sakernas Internet** > **Stream Analytics-jobb**.
1. Ange följande information för jobbet.

   **Jobbnamn**: Jobbets namn. Namnet måste vara globalt unikt.

   **Resurs grupp**: Använd samma resurs grupp som din IoT Hub använder.

   **Plats**: Använd samma plats som din resurs grupp.

   **Fäst på instrumentpanelen**: Välj det här alternativet för enkel åtkomst till IoT-hubben från instrumentpanelen.

   ![Skapa ett Stream Analytics jobb i Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

1. Öppna Stream Analyticss jobbet.
1. Klicka på **Indata** under **Jobbtopologi**.
1. I rutan **indata** klickar du på **Lägg till**och anger följande information:

   **Indataområde**: det unika aliaset för indatamängden.

   **Källa**: Välj **IoT Hub**.

   **Konsument grupp**: Välj den konsument grupp som du skapade.

   ![Lägg till inmatade Stream Analytics jobb i Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Under **Jobbtopologi** klickar du på **Utdata**.
1. I fönstret **utdata** klickar du på **Lägg till**och anger följande information:

   **Utdataalias**: Utdatas unika alias.

   **Mottagare**: Välj **Blob Storage**.

   **Lagrings konto**: lagrings kontot för blob-lagringen. Du kan skapa ett lagrings konto eller använda ett befintligt.

   **Container**: den behållare där blobben sparas. Du kan skapa en behållare eller använda en befintlig.

   **Format för händelse serialisering**: Välj **CSV**.

   ![Lägga till utdata till Stream Analytics jobb i Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Lägg till en funktion i Stream Analytics jobbet för att anropa den distribuerade webb tjänsten

1. Under **jobb sto pol Ogin**klickar du på **Functions** > **Lägg till**.
1. Ange följande information:

   **Funktions Ali Aset**: Ange `machinelearning`.

   **Funktions typ**: Välj **Azure ml**.

   **Import alternativ**: Välj **Importera från en annan prenumeration**.

   **URL**: Ange webb tjänst-URL: en som du antecknade från Excel-arbetsboken.

   **Nyckel**: Ange den åtkomst nyckel som du antecknade från Excel-arbetsboken.

   ![Lägga till en funktion i Stream Analytics jobbet i Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

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

1. Klicka på **Save** (Spara).

### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

I Stream Analytics-jobbet klickar du på **Starta** > **Nu** > **Starta**. När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

![Köra Stream Analytics-jobbet](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Använd Microsoft Azure Storage Explorer för att Visa väder prognosen

Kör klient programmet för att börja samla in och skicka temperatur-och fuktighets data till din IoT-hubb. För varje meddelande som IoT-hubben tar emot anropar Stream Analytics-jobbet väder prognosens webb tjänst för att skapa en chans för regn. Resultatet sparas sedan i Azure Blob Storage. Azure Storage Explorer är ett verktyg som du kan använda för att visa resultatet.

1. [Hämta och installera Microsoft Azure Storage Explorer](https://storageexplorer.com/).
1. Öppna Azure Storage Explorer.
1. Logga in på ditt Azure-konto.
1. Välj din prenumeration.
1. Klicka på prenumerationen > **lagrings konton** > ditt lagrings konto > **Blob-behållare** > din behållare.
1. Öppna en CSV-fil för att se resultatet. Den sista kolumnen registrerar risken för regn.

   ![Få resultat för väder prognoser med Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Sammanfattning

Du har använt Azure Machine Learning för att skapa en chans att ta regn baserat på den temperatur och de fuktighets data som din IoT hub tar emot.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]