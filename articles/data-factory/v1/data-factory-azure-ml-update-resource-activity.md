---
title: Uppdatera Machine Learning-modeller med Azure Data Factory
description: Beskriver hur du skapar förutsägande pipelines med Azure Data Factory och Azure Machine Learning
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: afc79badd19fa180e631f1f8fa9735567a0b1e33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978721"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Uppdatera Azure Machine Learning-modeller med hjälp av Uppdateringsresursaktivitet

> [!div class="op_single_selector" title1="Omvandlingsaktiviteter"]
> * [Hive-aktivitet](data-factory-hive-activity.md) 
> * [Grisaktivitet](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop streaming aktivitet](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [.NET anpassad aktivitet](data-factory-use-custom-activities.md)


> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [uppdatera maskininlärningsmodeller i Data Factory](../update-machine-learning-models.md).

Den här artikeln kompletterar huvudartikeln för Azure Data Factory – Azure Machine Learning-integrering: [Skapa förutsägande pipelines med Azure Machine Learning och Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Om du inte redan har gjort det, granska huvudartikeln innan du läser igenom den här artikeln. 

## <a name="overview"></a>Översikt
Med tiden måste de prediktiva modellerna i Azure ML-bedömningsexperimenten tränas om med hjälp av nya indatauppsättningar. När du är klar med omskolning, vill du uppdatera scoring webbtjänst med omskolad ML-modellen. De vanligaste stegen för att möjliggöra omskolning och uppdatering av Azure ML-modeller via webbtjänster är:

1. Skapa ett experiment i [Azure Machine Learning Studio (klassiskt)](https://studio.azureml.net).
2. När du är nöjd med modellen kan du använda Azure Machine Learning Studio (klassisk) för att publicera webbtjänster för både **träningsexperimentet** och bedömnings-/förutsägelseexperimentet .**predictive experiment**

I följande tabell beskrivs de webbtjänster som används i det här exemplet.  Se [Omskolning Machine Learning modeller programmässigt](../../machine-learning/machine-learning-retrain-models-programmatically.md) för mer information.

- **Utbildning webbtjänst** - Tar emot utbildningsdata och producerar utbildade modeller. Utdata för omskolning är en .ilearner-fil i en Azure Blob-lagring. **Standardslutpunkten** skapas automatiskt åt dig när du publicerar utbildningsexperimentet som en webbtjänst. Du kan skapa fler slutpunkter, men i exemplet används bara standardslutpunkten.
- **Bedömning webbtjänst** - Tar emot omärkta dataexempel och gör förutsägelser. Utdata av förutsägelse kan ha olika formulär, till exempel en CSV-fil eller rader i en Azure SQL-databas, beroende på konfigurationen av experimentet. Standardslutpunkten skapas automatiskt åt dig när du publicerar förutsägelseexperimentet som en webbtjänst. 

Följande bild visar förhållandet mellan utbildning och bedömning slutpunkter i Azure ML.

![Webbtjänster](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Du kan anropa **webbtjänsten för utbildning** med hjälp av Azure ML Batch Execution **Activity**. Att anropa en utbildningswebbtjänst är samma sak som att anropa en Azure ML-webbtjänst (poängsättningswebbtjänst) för bedömning av data. De föregående avsnitten beskriver hur du anropar en Azure ML-webbtjänst från en Azure Data Factory-pipeline i detalj. 

Du kan anropa **bedömningswebbtjänsten** med hjälp av **Azure ML Update Resource Activity** för att uppdatera webbtjänsten med den nyligen utbildade modellen. Följande exempel innehåller länkade tjänstdefinitioner: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Scoring webbtjänst är en klassisk webbtjänst
Om bedömningswebbtjänsten är en **klassisk webbtjänst**skapar du den andra slutpunkten som **inte är standard och uppdateringsbar** med hjälp av Azure-portalen. Se Artikeln Skapa [slutpunkter](../../machine-learning/machine-learning-create-endpoint.md) för steg. NÃ¤r du har skapat den ã¤nde uppdateringsbara ändpunkten som inte är standard:

* Klicka på **BATCH-KÖRNING** för att hämta URI-värdet för egenskapen **mlEndpoint** JSON.
* Klicka på **LÄNKEN UPPDATERA RESURS** om du vill hämta URI-värdet för egenskapen **updateResourceEndpoint** JSON. API-nyckeln finns på själva slutpunktssidan (i det nedre högra hörnet).

![uppdateringsbar slutpunkt](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

Följande exempel innehåller ett exempel på JSON-definition för azureml-länkade tjänsten. Den länkade tjänsten använder apiKey för autentisering.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Bedömningswebbtjänst är webbtjänsten Azure Resource Manager 
Om webbtjänsten är den nya typen av webbtjänst som exponerar en Azure Resource **Manager-slutpunkt** behöver du inte lägga till den andra slutpunkten som inte är standard. **UpdateResourceEndpoint** i den länkade tjänsten är av formatet: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Du kan hämta värden för platsinnehavare i URL:en när du frågar webbtjänsten på [Azure Machine Learning Web Services Portal](https://services.azureml.net/). Den nya typen av slutpunkt för uppdateringsresurser kräver en AAD-token (Azure Active Directory). Ange **servicePrincipalId** och **servicePrincipalKey** i den länkade azure machine learning-tjänsten. Se [hur du skapar tjänstens huvudnamn och tilldelar behörigheter för att hantera Azure-resurs](../../active-directory/develop/howto-create-service-principal-portal.md). Här är ett exempel på AzureML-länkad tjänstdefinition: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

Följande scenario innehåller mer information. Den har ett exempel för omskolning och uppdatering av Azure ML-modeller från en Azure Data Factory-pipeline.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scenario: omskolning och uppdatering av en Azure ML-modell
Det här avsnittet innehåller ett exempel pipeline som använder **Azure ML Batch Execution-aktiviteten** för att omskola en modell. Pipelinen använder också **azure ML Update Resource-aktiviteten** för att uppdatera modellen i bedömningswebbtjänsten. Avsnittet innehåller också JSON-utdrag för alla länkade tjänster, datauppsättningar och pipeline i exemplet.

Här är diagramvyn för exempelpipelinen. Som du kan se tar Azure ML Batch Execution Activity utbildningsindata och producerar en träningsutdata (iLearner-fil). Azure ML Update Resource Activity tar den här utbildningsutdatan och uppdaterar modellen i slutpunkten för bedömning av webbtjänsten. Uppdateringsresursaktiviteten ger inga utdata. PlatshållarenBlob är bara en dummy utdatauppsättning som krävs av Azure Data Factory-tjänsten för att köra pipelinen.

![pipelinediagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Azure Blob storage-länkad tjänst:
Azure Storage innehåller följande data:

* utbildningsdata. Indata för Azure ML-utbildningswebbtjänsten.  
* iLearner-fil. Utdata från Azure ML-utbildningswebbtjänsten. Den här filen är också indata till aktiviteten Uppdatera resurs.  

Här är exemplet JSON definition av den länkade tjänsten:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Indatauppsättning för utbildning:
Följande datauppsättning representerar indatautbildningsdata för webbtjänsten azure Machine Learning training. Azure Machine Learning Batch Execution-aktiviteten tar den här datauppsättningen som indata.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Informationsuppsättning för träningsutdata:
Följande datauppsättning representerar utdata-iLearner-filen från Azure ML-utbildningswebbtjänsten. Azure ML Batch Execution Activity producerar den här datauppsättningen. Den här datauppsättningen är också indata till Azure ML Update Resource-aktiviteten.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-machine-learning-training-endpoint"></a>Slutpunkt för länkad tjänst för Azure Machine Learning-utbildning
Följande JSON-kodavsnitt definierar en Azure Machine Learning-länkad tjänst som pekar på standardslutpunkten för utbildningswebbtjänsten.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

I **Azure Machine Learning Studio (klassisk)** gör du följande för att hämta värden för **mlEndpoint** och **apiKey:**

1. Klicka på **WEBBTJÄNSTER** på den vänstra menyn.
2. Klicka på **webbtjänsten för utbildning** i listan över webbtjänster.
3. Klicka på kopiera bredvid textrutan **för API-tangenten.** Klistra in nyckeln i Urklipp i Data Factory JSON-redigeraren.
4. Klicka på länken **BATCH EXECUTION** i Azure Machine Learning **Studio (klassiskt).**
5. Kopiera **URI-begäran från** avsnittet **Begär** och klistra in den i Data Factory JSON-redigeraren.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Slutpunkt för länkad tjänst för Azure ML-uppdateringsbara poäng:
Följande JSON-kodavsnitt definierar en Azure Machine Learning-länkad tjänst som pekar på den icke-standarduppdatbara slutpunkten för bedömningswebbtjänsten.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Platshållareutdatadatauppsättning:
Azure ML Update Resource-aktiviteten genererar ingen utdata. Azure Data Factory kräver dock en utdatauppsättning för att köra schemat för en pipeline. Därför använder vi en dummy/platshållare datauppsättning i det här exemplet.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
Pipelinen har två aktiviteter: **AzureMLBatchExecution** och **AzureMLUpdateResource**. Azure ML Batch Execution-aktiviteten tar utbildningsdata som indata och producerar en iLearner-fil som en utdata. Aktiviteten anropar utbildningswebbtjänsten (utbildningsexperiment exponeras som en webbtjänst) med indatautbildningsdata och tar emot ilearnerfilen från webbtjänsten. PlatshållarenBlob är bara en dummy utdatauppsättning som krävs av Azure Data Factory-tjänsten för att köra pipelinen.

![pipelinediagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```
