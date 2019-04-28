---
title: Skapa datapipelines med Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar en förutsägande pipeline med hjälp av Azure Machine Learning - Batchkörningsaktivitet i Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: aaf1d72a0c9c56e7d140fb615caf014507ebf263
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60928090"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Skapa förutsägande pipelines med Azure Machine Learning och Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [Aktuell version](transform-data-using-machine-learning.md)

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) gör att du kan skapa, testa och distribuera lösningar för förutsägelseanalys. Från en övergripande synsätt, är det klart i tre steg:

1. **Skapa ett träningsexperiment**. Du kan göra det här steget med hjälp av Azure Machine Learning studio. Azure Machine Learning studio är en gemensam visual utvecklingsmiljö som används för att träna och testa en modell för förutsägelseanalys med hjälp av träningsdata.
2. **Konvertera den till ett förutsägelseexperiment**. När din modell har tränats med befintliga data och du är redo att använda den för att samla in nya data, förbereda och effektivisera experimentet för bedömning.
3. **Distribuera den som en webbtjänst**. Du kan publicera ditt bedömnings experiment som en Azure-webbtjänst. Du kan skicka data till din modell via den här web service-slutpunkt och få resultatet förutsägelser från modellen.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory och Machine Learning tillsammans
Azure Data Factory kan du enkelt skapa pipelines som använder en publiceringswebbkod [Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning) webbtjänsten för förutsägande analys. Med hjälp av den **Batchkörningsaktivitet** i en Azure Data Factory-pipeline kan du anropa en Azure Machine Learning studio-webbtjänst för att göra förutsägelser på data i batch.

Framöver kommer måste förutsägande modeller i Azure Machine Learning studio bedömning experiment vara modellkomponenten med hjälp av nya indatauppsättningar. Du kan träna en modell från Data Factory-pipeline genom att göra följande:

1. Publicera träningsexperiment (inte förutsägelseexperiment) som en webbtjänst. Du kan göra det här steget i Azure Machine Learning studio som du gjorde för att exponera förutsägelseexperiment som en webbtjänst i scenariot ovan.
2. Använda Azure Machine Learning studio-Batchkörningsaktivitet för att anropa webbtjänsten för träningsexperimentet. I princip kan du använda Azure Machine Learning studio-batchkörningsaktivitet för att anropa både webbtjänst för utbildning och bedömning av webbtjänsten.

När du är klar med att träna, uppdatera bedömning av webbtjänsten (förutsägelseexperiment visas som en webbtjänst) med den nyligen tränade modellen med hjälp av den **Azure Machine Learning studio aktivitet uppdatera resurs**. Se [uppdaterar modeller med Uppdateringsresursaktivitet](update-machine-learning-models.md) nedan för information.

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst

Du skapar en **Azure Machine Learning** länkade tjänst som länkar en Azure Machine Learning-webbtjänst till en Azure-datafabrik. Den länkade tjänsten används av Azure Machine Learning-Batchkörningsaktivitet och [Uppdateringsresursaktivitet](update-machine-learning-models.md).

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Se [länkade tjänster för Compute](compute-linked-services.md) artikeln beskrivningar om egenskaper i JSON-definitionen.

Azure Machine Learning har stöd för både klassiska webbtjänsterna och nya Web Services för din förutsägbart experiment. Du kan välja rätt som ska användas från Data Factory. För att få den information som krävs för att skapa den länkade tjänsten för Azure Machine Learning kan du gå till https://services.azureml.net, där alla webbtjänster (nytt) och klassiska webbtjänster visas. Klicka på den webbtjänst som du vill och klicka på **förbruka** sidan. Kopiera **primärnyckel** för **apiKey** egenskapen och **gruppbegäranden** för **mlEndpoint** egenskapen.

![Azure Machine Learning Web Services](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-batch-execution-activity"></a>Azure Machine Learning-batchkörningsaktivitet

Följande JSON-kodfragmentet definierar en Azure Machine Learning Batch Execution-aktivitet. Aktivitetsdefinitionen innehåller en referens till tjänsten Azure Machine Learning länkad som du skapade tidigare.

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| Egenskap           | Beskrivning                              | Obligatoriskt |
| :---------------- | :--------------------------------------- | :------- |
| namn              | Namnet på aktiviteten i pipelinen     | Ja      |
| description       | Text som beskriver hur aktiviteten ska hantera.  | Nej       |
| typ              | För Data Lake Analytics U-SQL-aktivitet, är aktivitetstypen **AzureMLBatchExecution**. | Ja      |
| linkedServiceName | Länkade tjänster till Azure Machine Learning länkad tjänst. Mer information om den här länkade tjänsten, se [länkade tjänster för Compute](compute-linked-services.md) artikeln. | Ja      |
| webServiceInputs  | Nyckel, värde-par, mappa namnen på Azure Machine Learning Web Service indata. Nyckeln måste matcha de indataparametrar som definierats i publicerade Azure Machine Learning-webbtjänsten. Värdet är ett Azure Storage-länkade tjänster nyckelpar och en FilePath egenskaper anger de inkommande Blob-platserna. | Nej       |
| webServiceOutputs | Nyckel, värde-par, mappa namnen på Azure Machine Learning Web Service utdata. Nyckeln måste matcha utdataparametrar som definierats i publicerade Azure Machine Learning-webbtjänsten. Värdet är ett Azure Storage-länkade tjänster och FilePath egenskaper-par anger utdata Blob-platser. | Nej       |
| globalParameters  | Nyckel, värde-par som ska skickas till Azure Machine Learning studio Batch Execution Service-slutpunkt. Nycklar måste matcha namnen på webbtjänstparametrar som definierats i den publicerade Azure Machine Learning studio-webbtjänsten. Värden angavs i egenskapen GlobalParameters i Azure Machine Learning studio batchbegäran körning | Nej       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenario 1: Experiment med hjälp av Web service indata/utdata som refererar till data i Azure Blob Storage

I det här scenariot, Azure Machine Learning-webbtjänsten gör förutsägelser med data från en fil i Azure blob storage och lagrar de förutsagda resultaten i blob storage. Följande JSON definierar en Data Factory-pipeline med en AzureMLBatchExecution aktivitet. Inkommande och utgående data i Azure BLOB-lagring refererar till med hjälp av ett LinkedName och FilePath par. I exemplet länkade tjänsten av indata och utdata är olika, du kan använda olika länkade tjänster för var och en av dina indata/utdata för Data Factory för att kunna hämta filerna som är rätt och skicka till Azure Machine Learning studio-webbtjänst.

> [!IMPORTANT]
> I Azure Machine Learning studio-experiment, web service indata och utdata har portar och globala parametrar du standardnamnen (”indata1”, ”indata2”) som du kan anpassa. De namn som du använder för webServiceInputs, webServiceOutputs och globalParameters inställningar måste exakt matcha namnen i experiment. Du kan visa nyttolasten i begäran av exemplet på hjälpsidan för batchkörning för din Azure Machine Learning studio-slutpunkt att verifiera förväntade mappningen.
>
>

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenario 2: Experiment som använder Reader/Writer moduler för att referera till data i olika lagringsutrymmen
Ett annat vanligt scenario när du skapar Azure Machine Learning studio-experiment är att använda modulerna importera Data och utdata. Modulen importera Data som används för att läsa in data i ett experiment och utdata-modulen är att spara data från dina experiment. Mer information om importera Data och utdata finns i [importdata](https://msdn.microsoft.com/library/azure/dn905997.aspx) och [utdata](https://msdn.microsoft.com/library/azure/dn905984.aspx) ämnen i MSDN Library.

När du använder Import och utdata-moduler, är det bra att använda en Web service-parameter för varje egenskap i dessa moduler. Dessa webb-parametrar kan du konfigurera värden under körning. Du kan till exempel skapa ett experiment med en importera Data-modul som använder en Azure SQL Database: XXX.database.windows.net. När webbtjänsten har distribuerats kan du vill aktivera konsumenter av webbtjänsten att ange en annan Azure SQL-servern med namnet `YYY.database.windows.net`. Du kan använda en Web service-parameter så att det här värdet som ska konfigureras.

> [!NOTE]
> Skiljer sig från webbtjänstparametrar Web service indata och utdata. I det första scenariot har du sett hur indata och utdata kan anges för en Azure Machine Learning studio-webbtjänst. I det här scenariot kan skicka du parametrar för en webbtjänst som motsvarar till egenskaperna för Import Data-och utdata-moduler.
>
>

Låt oss titta på ett scenario för att använda webbtjänstparametrar. Du har en distribuerad Azure Machine Learning-webbtjänst som använder en modul för dataläsare för att läsa data från en av de datakällor som stöds av Azure Machine Learning (till exempel: Azure SQL Database). När batchkörningen utförs, skrivs resultaten med hjälp av en skrivarmodul (Azure SQL Database).  Inga web service indata och utdata har definierats i experiment. I det här fallet rekommenderar vi att du konfigurerar relevanta webbtjänstparametrar för läsare och skrivare moduler. Den här konfigurationen gör att läsaren/skrivaren moduler konfigureras när du använder aktiviteten AzureMLBatchExecution. Du anger webbtjänstparametrar i den **globalParameters** avsnittet i JSON-aktiviteten på följande sätt.

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> Web service-parametrar är skiftlägeskänsliga, så se till att de namn som du anger i aktiviteten JSON matchar de som visas av webbtjänsten.
>

När du är klar med att träna, uppdatera bedömning av webbtjänsten (förutsägelseexperiment visas som en webbtjänst) med den nyligen tränade modellen med hjälp av den **Azure Machine Learning studio aktivitet uppdatera resurs**. Se [uppdaterar modeller med Uppdateringsresursaktivitet](update-machine-learning-models.md) nedan för information.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som beskriver hur du omvandlar data på andra sätt:

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Piggningsåtgärd](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
