---
title: Använda modellen data collection-funktionen i Azure Machine Learning Workbench | Microsoft Docs
description: Den här artikeln pratar om hur du använder modellen data collection-funktionen i Azure Machine Learning Workbench
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 5c1a884ebe6216c4e8099f2ada2182ccff68b63e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449787"
---
# <a name="collect-model-data-by-using-data-collection"></a>Samla in modelldata genom att använda datainsamling

Du kan använda modellen data collection-funktionen i Azure Machine Learning för att arkivera modellindata och förutsägelser från en webbtjänst.

## <a name="install-the-data-collection-package"></a>Installera paket för insamling av data
Du kan installera data collection biblioteket internt på Linux och Windows.

### <a name="windows"></a>Windows
Installera modulen data collector på Windows, med hjälp av följande kommando:

    pip install azureml.datacollector

### <a name="linux"></a>Linux
På Linux, installerar du först libxml ++-biblioteket. Kör följande kommando, som måste utfärdas under sudo:

    sudo apt-get install libxml++2.6-2v5

Kör sedan följande kommando:

    pip install azureml.datacollector

## <a name="set-environment-variables"></a>Ange miljövariabler

Insamling av modelldata är beroende av två miljövariabler. AML_MODEL_DC_STORAGE_ENABLED måste anges till **SANT** (endast gemener) och AML_MODEL_DC_STORAGE måste anges till anslutningssträngen för Azure Storage-konto där du vill lagra data.

Dessa miljövariabler är redan angetts för dig när webbtjänsten körs på ett kluster i Azure. Du måste ange dem själv när du kör lokalt. Om du använder Docker, kan du använda parametern -e docker kör kommandot för att skicka miljövariabler.

## <a name="collect-data"></a>Samla in data

Om du vill använda modelldatasamling, gör du följande ändringar i din bedömnings fil:

1. Lägg till följande kod högst upp i filen:
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

1. Lägg till följande rader med kod till den `init()` funktionen:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1. Lägg till följande rader med kod till den `run(input_df)` funktionen:
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Se till att variablerna `input_df` och `pred` (förutsägelse värde från `model.predict()`) initieras innan du anropar den `collect()` funktionen på dem.

1. Använd den `az ml service create realtime` med den `--collect-model-data true` växel för att skapa en realtidswebbtjänst. Det här steget säkerställer att modelldata samlas in när tjänsten körs.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
1. Om du vill testa insamling av data, kör den `az ml service run realtime` kommando:

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="view-the-collected-data"></a>Visa insamlade data
Visa insamlade data i blob storage:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **alla tjänster**.
1. I sökrutan skriver **lagringskonton** och välj RETUR-tangenten.
1. Från den **lagringskonton** Sök, bladet och välja den **lagringskonto** resurs. Använd följande steg för att fastställa ditt storage-konto:

    a. Gå till Azure Machine Learning Workbench, välja det projekt du arbetar på och öppna en kommandotolk från den **filen** menyn.
    
    b. Ange `az ml env show -v` och kontrollera den *storage_account* värde. Det här är namnet på ditt lagringskonto.

1. Välj **behållare** på resursen bladets meny och behållaren heter **modeldata**. Om du vill se data börjar spridas till lagringskontot, kan du behöva vänta upp till 10 minuter efter den första Webbtjänstbegäran. Data flödar till blobar med följande containersökväg:

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Data kan användas från Azure-blobar i en mängd olika sätt via både Microsoft-programvara och verktyg med öppen källkod. Här följer några exempel:
- Azure Machine Learning Workbench: Öppna CSV-filen i Azure Machine Learning Workbench genom att lägga till CSV-filen som en datakälla.
- Excel: Öppna de dagliga CSV-filerna som ett kalkylblad.
- [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/): skapa diagram med data från CSV-data i blobar.
- [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview): skapa en dataram med en stor del av CSV-data.
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started): CSV-data till en Hive-tabell och kör SQL-frågor direkt mot bloben.

