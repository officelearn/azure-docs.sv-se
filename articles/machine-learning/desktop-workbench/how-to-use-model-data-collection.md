---
title: Funktionen modellen data samling i Azure Machine Learning arbetsstationen | Microsoft Docs
description: Den här artikeln handlar om hur du använder funktionen modellen samling i Azure Machine Learning arbetsstationen
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 2251f8d241f3ec47d46c04160caf997bcd025124
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="collect-model-data-by-using-data-collection"></a>Samla in modelldata med hjälp av datainsamling

Du kan använda funktionen modellen samling i Azure Machine Learning för att arkivera modellen indata och förutsägelser från en webbtjänst.

## <a name="install-the-data-collection-package"></a>Installera paket för insamling av data
Du kan installera data collection biblioteket internt i Linux och Windows.

### <a name="windows"></a>Windows
Installera modulen data collector i Windows, med hjälp av följande kommando:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
På Linux, installerar du först libxml ++-biblioteket. Kör följande kommando, som måste utfärdas under sudo:

    sudo apt-get install libxml++2.6-2v5

Kör följande kommando:

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>Ange miljövariabler

Insamling av modellen beror på två miljövariabler. AML_MODEL_DC_STORAGE_ENABLED måste anges till **SANT** (gemener) och AML_MODEL_DC_STORAGE måste anges i anslutningssträngen för Azure Storage-konto där du vill lagra data.

Miljövariablerna har redan angetts för dig när webbtjänsten körs på ett kluster i Azure. När körs lokalt måste du ange dem själv. Om du använder Docker, använder du parametern -e för docker Kör kommando för att skicka miljövariabler.

## <a name="collect-data"></a>Samla in data

Om du vill använda modellen datainsamling gör följande ändringar i filen bedömningsprofil:

1. Lägg till följande kod högst upp i filen:
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. Lägg till följande rader med kod till den `init()` funktionen:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. Lägg till följande rader med kod till den `run(input_df)` funktionen:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Se till att variablerna `input_df` och `pred` (förutsägelse värde från `model.predict()`) initieras innan du anropar den `collect()` funktionen på dem.

4. Använd den `az ml service create realtime` kommandot med de `--collect-model-data true` växel för att skapa en realtid webbtjänst. Det här steget säkerställer att modelldata samlas in när tjänsten körs.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. Om du vill testa datainsamlingen kör den `az ml service run realtime` kommando:

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>Visa insamlade data
Visa insamlade data i blob storage:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster**.
3. I sökrutan skriver **lagringskonton** och välj på Enter.
4. Från den **lagringskonton** Sök bladet väljer den **lagringskonto** resurs. Använd följande steg för att fastställa ditt lagringskonto:

    a. Gå till Azure Machine Learning arbetsstationen, Välj projekt du arbetar på och öppna en kommandotolk från den **filen** menyn.
    
    b. Ange `az ml env show -v` och kontrollera den *storage_account* värde. Det här är namnet på ditt lagringskonto.

5. Välj **behållare** på resursen bladet-menyn och sedan behållaren som kallas **modeldata**. För att se data starta sprider till lagringskontot, kan du behöva vänta upp till 10 minuter efter den första webbtjänstens begäranden. Data flödar till blobar med följande behållarsökväg:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Data kan användas från Azure-blobbar i en mängd olika sätt via både Microsoft-program och verktyg med öppen källkod. Här följer några exempel:
- Azure Machine Learning-arbetsstationen: Öppna CSV-filen i Azure Machine Learning arbetsstationen genom att lägga till CSV-filen som en datakälla.
- Excel: Öppna dagliga CSV-filer som ett kalkylblad.
- [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/): skapa diagram med data som hämtas från CSV-data i BLOB.
- [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): skapa en data-ram med en stor del av CSV-data.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): Läs in CSV-data till en annan registreringsdatafil tabell och utföra SQL-frågor direkt mot blob.

