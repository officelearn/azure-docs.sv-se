---
title: Väderprognos med Azure Machine Learning med IoT Hub-data
description: Använd Azure Machine Learning för att förutsäga risken för regn baserat på temperatur- och fuktighetsdata som din IoT-hubb samlar in från en sensor.
author: robinsh
manager: philmea
keywords: väderprognos maskininlärning
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/10/2020
ms.author: robinsh
ms.openlocfilehash: b71b86c14c55c312ef420a4d8517140fdded4072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122297"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Väderprognos med hjälp av sensordata från din IoT-hubb i Azure Machine Learning

![Diagram från på sluten tid](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Maskininlärning är en teknik för datavetenskap som hjälper datorer att lära av befintliga data för att förutse framtida beteenden, resultat och trender. Azure Machine Learning är en molnbaserad tjänst för förutsägelseanalys som gör det möjligt att snabbt skapa och distribuera förutsägelsemodeller som analyslösningar.

## <a name="what-you-learn"></a>Detta får du får lära dig

Du lär dig hur du använder Azure Machine Learning för att göra väderprognos (risk för regn) med hjälp av temperatur- och fuktighetsdata från din Azure IoT-hubb. Risken för regn är produktionen av en förberedd väderprognosmodell. Modellen bygger på historiska data för att prognostisera risken för regn baserat på temperatur och luftfuktighet.

## <a name="what-you-do"></a>Vad du gör

- Distribuera väderprognosmodellen som en webbtjänst.
- Gör din IoT-hubb redo för dataåtkomst genom att lägga till en konsumentgrupp.
- Skapa ett Stream Analytics-jobb och konfigurera jobbet till:
  - Läs temperatur- och fuktighetsdata från din IoT-hubb.
  - Ring webbtjänsten för att få regnchansen.
  - Spara resultatet i en Azure blob-lagring.
- Använd Microsoft Azure Storage Explorer för att visa väderprognosen.

## <a name="what-you-need"></a>Vad du behöver

- Slutför [Raspberry Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) handledning eller en av enheten tutorials; till exempel [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Dessa omfattar följande krav:
  - En aktiv Azure-prenumeration.
  - En Azure IoT-hubb under din prenumeration.
  - Ett klientprogram som skickar meddelanden till din Azure IoT-hubb.
- Ett [Azure Machine Learning Studio -konto (klassiskt).](https://studio.azureml.net/)

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Distribuera väderprognosmodellen som en webbtjänst

I det här avsnittet får du väderprognosmodellen från Azure AI-biblioteket. Sedan lägger du till en R-script modul till modellen för att rengöra temperatur och luftfuktighet data. Slutligen distribuerar du modellen som en förutsägande webbtjänst.

### <a name="get-the-weather-prediction-model"></a>Hämta väderprognosmodellen

I det här avsnittet får du väderprognosmodellen från Azure AI Gallery och öppnar den i Azure Machine Learning Studio (klassisk).

1. Gå till [modellsidan för väderprognoser](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Öppna modellsidan för väderprognoser i Azure AI Gallery](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Klicka på **Öppna i Studio (klassiskt)** för att öppna modellen i Microsoft Azure Machine Learning Studio (klassisk).

   ![Öppna väderprognosmodellen i Azure Machine Learning Studio (klassisk)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Lägg till en R-skriptmodul för att rengöra temperatur- och fuktighetsdata

För att modellen ska fungera korrekt måste temperatur- och fuktighetsdata vara konvertibla till numeriska data. I det här avsnittet lägger du till en R-script-modul i väderförutsägelsemodellen som tar bort alla rader som har datavärden för temperatur eller fuktighet som inte kan konverteras till numeriska värden.

1. Klicka på pilen till vänster i Azure Machine Learning Studio-fönstret. Skriv "Kör" i sökrutan. Välj modulen **Kör R-skript.**

   ![Välj Kör R-skriptmodul](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Dra modulen **Kör R-skript** nära modulen **Rensa data som saknas** och den befintliga **modulen Kör R-skript** i diagrammet. Ta bort anslutningen mellan **clean missing data** och **modulen Kör R-skript** och anslut sedan in- och utgångarna för den nya modulen som visas.

   ![Lägg till R-skriptmodul för körning](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Välj den nya **kör R-skriptmodulen** för att öppna dess egenskapsfönster. Kopiera och klistra in följande kod i rutan **R-skript.**

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   När du är klar bör egenskapsfönstret se ut ungefär så här:

   ![Lägga till kod för att köra R-skriptmodul](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Distribuera prediktiv webbtjänst

I det här avsnittet validerar du modellen, ställer in en prediktiv webbtjänst baserat på modellen och distribuerar sedan webbtjänsten.

1. Klicka på **Kör** för att validera stegen i modellen. Det här steget kan ta några minuter att slutföra.

   ![Kör experimentet för att validera stegen](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Klicka på **KONFIGURERA WEBBTJÄNST** > **Prediktiv webbtjänst**. Det prediktiva experimentdiagrammet öppnas.

   ![Distribuera väderprognosmodellen i Azure Machine Learning Studio (klassisk)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. I det prediktiva experimentdiagrammet tar du bort anslutningen mellan **indatamodulen** för webbtjänsten och **väderdatauppsättningen** högst upp. Dra sedan **inmatningsmodulen för webbtjänsten** någonstans nära modulen **Poängmodell** och anslut den enligt bilden:

   ![Anslut två moduler i Azure Machine Learning Studio (klassisk)](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)

1. Klicka på **KÖR** för att validera stegen i modellen.

1. Klicka på **DISTRIBUERA WEBBTJÄNST** om du vill distribuera modellen som en webbtjänst.

1. Hämta **Excel 2010 eller tidigare arbetsbok** för **REQUEST/RESPONSE**på instrumentpanelen i modellen.

   > [!Note]
   > Se till att du hämtar **Excel 2010 eller tidigare arbetsbok** även om du kör en senare version av Excel på datorn.

   ![Hämta slutpunkten För BEGÄRAN OM SVAR](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Öppna Excel-arbetsboken, anteckna **webbadressen till webbtjänsten** och **ÅTKOMSTNYCKELn**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Skapa, konfigurera och köra ett Stream Analytics-jobb

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I [Azure Portal](https://portal.azure.com/) klickar du på **Skapa en resurs** > **Sakernas Internet** > **Stream Analytics-jobb**.
1. Ange följande information för jobbet.

   **Jobbnamn**: Jobbets namn. Namnet måste vara globalt unikt.

   **Resursgrupp**: Använd samma resursgrupp som IoT-hubben använder.

   **Plats**: Använd samma plats som resursgruppen.

   **Fäst på instrumentpanelen**: Välj det här alternativet för enkel åtkomst till IoT-hubben från instrumentpanelen.

   ![Skapa ett Stream Analytics-jobb i Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

1. Öppna stream analytics-jobbet.
1. Under **Jobbtopologi** klickar du på **Indata**.
1. Klicka på **Lägg till**i fönstret **Indata** och ange sedan följande information:

   **Indataalias**: Det unika aliaset för indata.

   **Källa**: Välj **IoT-hubb**.

   **Konsumentgrupp**: Välj den konsumentgrupp du skapade.

   ![Lägga till en indata till Stream Analytics-jobbet i Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Under **Jobbtopologi** klickar du på **Utdata**.
1. Klicka på **Lägg till**i fönstret **Utdata** och ange sedan följande information:

   **Utdataalias**: Utdatas unika alias.

   **Sink:** Välj **Blob Storage**.

   **Lagringskonto**: Lagringskontot för din blob-lagring. Du kan skapa ett lagringskonto eller använda ett befintligt konto.

   **Behållare**: Behållaren där blobben sparas. Du kan skapa en behållare eller använda en befintlig.

   **Format för händelseserier:** Välj **CSV**.

   ![Lägga till en utdata till Stream Analytics-jobbet i Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Klicka på **Skapa**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Lägga till en funktion i Stream Analytics-jobbet för att ringa den webbtjänst du distribuerade

1. Klicka på **Funktioner** > **lägg till**under **Jobbtopologi**.
1. Ange följande information:

   **Funktion alias:** Ange `machinelearning`.

   **Funktionstyp:** Välj **Azure ML**.

   **Alternativet Importera**: Välj **Importera från en annan prenumeration**.

   **URL:** Ange webbadressen till webbtjänsten som du noterade ned från Excel-arbetsboken.

   **Nyckel:** Ange den ACCESS-nyckel som du noterade ned från Excel-arbetsboken.

   ![Lägga till en funktion i Stream Analytics-jobbet i Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

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

Klicka på Starta > **nu** > i stream **analytics-jobbet**.**Start** När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

![Köra Stream Analytics-jobbet](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Använda Microsoft Azure Storage Explorer för att visa väderprognosen

Kör klientprogrammet för att börja samla in och skicka temperatur- och fuktighetsdata till din IoT-hubb. För varje meddelande som din IoT-hubb tar emot anropar Stream Analytics-jobbet webbtjänsten väderprognos för att skapa risk för regn. Resultatet sparas sedan i din Azure blob-lagring. Azure Storage Explorer är ett verktyg som du kan använda för att visa resultatet.

1. [Hämta och installera Microsoft Azure Storage Explorer](https://storageexplorer.com/).
1. Öppna Azure Storage Explorer.
1. Logga in på ditt Azure-konto.
1. Välj din prenumeration.
1. Klicka på din prenumeration > **lagringskonton** > ditt lagringskonto > **Blob Containers** > din behållare.
1. Ladda ner en CSV-fil för att se resultatet. Den sista kolumnen registrerar risken för regn.

   ![Få väderprognosresultat med Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Sammanfattning

Du har använt Azure Machine Learning för att skapa risk för regn baserat på temperatur- och fuktighetsdata som din IoT-hubb tar emot.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]