---
title: Uppdatera Machine Learning-modeller med Azure Data Factory | Microsoft Docs
description: Beskriver hur du skapar skapa förutsägande pipelines med Azure Data Factory och Azure Machine Learning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 7a5c0866bc08c5a73888d9baca41980106a62ae2
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954953"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Uppdaterar Azure Machine Learning-modeller med Uppdateringsresursaktivitet

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-aktivitet](data-factory-hive-activity.md) 
> * [Piggningsåtgärd](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [.NET-anpassad aktivitet](data-factory-use-custom-activities.md)


> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [uppdatera machine learning-modeller i Data Factory](../update-machine-learning-models.md).

Den här artikeln kompletterar huvudsakliga Azure Data Factory - artikeln för Azure Machine Learning-integrering: [skapa förutsägande pipelines med Azure Machine Learning och Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Om du inte redan gjort det, kan du läsa den huvudsakliga artikeln innan du läser igenom den här artikeln. 

## <a name="overview"></a>Översikt
Framöver kommer måste förutsägande modeller i Azure ML bedömning experiment vara modellkomponenten med hjälp av nya indatauppsättningar. När du är klar med att träna, som du vill uppdatera bedömning av webbtjänsten med retrained ML-modellen. Vanliga stegen för att aktivera omtränings och uppdatera Azure ML-modeller via webbtjänster är:

1. Skapa ett experiment i [Azure ML Studio](https://studio.azureml.net).
2. När du är nöjd med modellen kan du också använda Azure ML Studio för att publicera webbtjänster för både den **träningsexperiment** och bedömning /**förutsägelseexperiment**.

I följande tabell beskrivs de webbtjänster som används i det här exemplet.  Se [träna om Machine Learning-modeller via programmering](../../machine-learning/machine-learning-retrain-models-programmatically.md) mer information.

- **Utbildning webbtjänsten** – tar emot träningsdata och producerar tränade modeller. Utdata från omtrimning är en .ilearner-fil i Azure Blob storage. Den **standard endpoint** skapas automatiskt för dig när du publicerar utbildningen experiment som en webbtjänst. Du kan skapa fler slutpunkter, men i exemplet används bara standardslutpunkten.
- **Bedömning av webbtjänsten** – tar emot omärkta data exempel och förutsägelser. Utdata för förutsägelse kan ha olika former, till exempel en .csv-fil eller rader i en Azure SQL-databas, beroende på konfigurationen av experimentet. Standardslutpunkten skapas automatiskt åt dig när du publicerar förutsägelseexperiment som en webbtjänst. 

Följande bild visar relationen mellan träning och bedömning slutpunkter i Azure ML.

![Webbtjänster](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Du kan anropa den **utbildning webbtjänsten** med hjälp av den **Azure ML-Batchkörningsaktivitet**. Anropa en webbtjänst för utbildning är samma som anropar en Azure Machine Learning-webbtjänst (värdera webbtjänst) för bedömnings data. I föregående avsnitt beskriver hur du anropar en Azure Machine Learning-webbtjänst från en Azure Data Factory-pipeline i detalj. 

Du kan anropa den **värdera webbtjänst** med hjälp av den **Azure ML uppdatera resurs aktivitet** att uppdatera webbtjänsten med den nyligen tränade modellen. I följande exempel innehåller definitioner för länkad tjänst: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Bedömning av webbtjänsten är en klassiska webbtjänst
Om bedömning av webbtjänsten är en **klassisk webbtjänst**, skapa andra **inte är standard och uppdateras endpoint** med hjälp av Azure portal. Se [skapa slutpunkter](../../machine-learning/machine-learning-create-endpoint.md) i artikeln. När du har skapat den uppdatera inte är standard-slutpunkten, gör du följande:

* Klicka på **BATCHKÖRNING** att hämta URI-värdet för den **mlEndpoint** JSON-egenskap.
* Klicka på **RESURSUPPDATERING** länk till URI-värdet för den **updateResourceEndpoint** JSON-egenskap. API-nyckel är på slutpunkten för själva sidan (i det nedre högra hörnet).

![uppdateringsbara slutpunkt](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

I följande exempel innehåller en exempel-JSON-definition för länkad AzureML-tjänst. Den länkade tjänsten använder apiKey för autentisering.  

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

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Bedömning av webbtjänsten är Azure Resource Manager-webbtjänst 
Om webbtjänsten är den nya typen av webbtjänsten som Exponerar en Azure Resource Manager-slutpunkt, behöver du inte lägga till andra **avviker från standarden** slutpunkt. Den **updateResourceEndpoint** i den länkade tjänsten har följande format: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Du kan hämta värden för platshållare i URL: en vid frågor till webbtjänsten på den [Azure Machine Learning Web Services-portalen](https://services.azureml.net/). Den nya typen av slutpunkt för resursen kräver en AAD (Azure Active Directory)-token. Ange **servicePrincipalId** och **servicePrincipalKey**i AzureML länkad tjänst. Se [skapa tjänstens huvudnamn och tilldela behörigheter för att hantera Azure-resurs](../../active-directory/develop/howto-create-service-principal-portal.md). Här är ett exempel AzureML länkade tjänstdefinitionen: 

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

Följande scenario innehåller mer information. Den innehåller ett exempel för att träna och uppdatera Azure ML-modeller från en Azure Data Factory-pipeline.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scenario: träna och uppdatera en Azure ML-modell
Det här avsnittet innehåller ett exempel på pipeline som använder den **Azure ML-batchkörningsaktivitet** att träna en modell. Pipelinen använder också den **aktivitet för Azure ML uppdatera resurs** att uppdatera modellen i bedömning av webbtjänsten. Avsnittet ger också JSON-kodfragment för alla länkade tjänster, datauppsättningar och pipeline i det här exemplet.

Här är i diagramvyn på exempel-pipeline. Som du ser Azure ML-Batchkörningsaktivitet tar utbildning indata och producerar en utbildning utdata (iLearner-fil). Azure ML-Uppdateringsresursaktivitet tar den här utdatan för utbildning och uppdaterar modellen i bedömnings webbtjänstslutpunkt. Den Uppdateringsresursaktivitet producerar inte några utdata. PlaceholderBlob är bara en dummy utdata-datauppsättning som krävs av Azure Data Factory-tjänsten att köra en pipeline.

![diagram med datapipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Azure Blob storage-länkade tjänst:
Azure Storage innehåller följande data:

* träningsdata. Indata för webbtjänsten för Azure ML-utbildning.  
* iLearner-fil. Utdata från webbtjänsten för Azure ML-utbildning. Den här filen är också indata till aktiviteten resursuppdatering.  

Här är exempel JSON-definition för den länkade tjänsten:

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

### <a name="training-input-dataset"></a>Utbildning datauppsättningen för indata:
Följande datauppsättning representerar inkommande träningsdata för webbtjänsten för Azure ML-utbildning. Azure ML-batchkörningsaktivitet tar den här datauppsättningen som indata.

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

### <a name="training-output-dataset"></a>Datauppsättning för träning utdata:
Följande datauppsättningen representerar den iLearner-fil med utdata från webbtjänsten för Azure ML-utbildning. Azure ML-Batchkörningsaktivitet skapar den här datauppsättningen. Den här datauppsättningen är också indata till aktiviteten Azure ML-resursuppdatering.

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

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Länkad tjänst för Azure ML-slutpunkten för utbildning
Följande JSON-kodfragmentet definierar en Azure Machine Learning-länkade tjänst som pekar på standardslutpunkten för webbtjänsten utbildning.

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

I **Azure ML Studio**, gör du följande för att hämta värden för **mlEndpoint** och **apiKey**:

1. Klicka på **WEBBTJÄNSTER** på den vänstra menyn.
2. Klicka på den **utbildning webbtjänsten** i listan över webbtjänster.
3. Klicka på Kopiera bredvid **API-nyckel** textrutan. Klistra in nyckeln i Urklipp i Data Factory JSON-redigerare.
4. I den **Azure ML studio**, klickar du på **BATCHKÖRNING** länk.
5. Kopiera den **begär URI** från den **begära** avsnittet och klistra in den i Data Factory JSON-redigerare.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Länkad tjänst för Azure ML uppdateras bedömnings-slutpunkten:
Följande JSON-kodfragmentet definierar en Azure Machine Learning-länkade tjänst som pekar på den icke-standard uppdatera slutpunkten för bedömning av webbtjänsten.  

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

### <a name="placeholder-output-dataset"></a>Platshållaren datauppsättningen för utdata:
Azure ML-resursuppdatering aktiviteten genererar inte några utdata. Azure Data Factory kräver dock en utdatauppsättning att driva schemat för en pipeline. Därför använder vi en dummy/platshållaren datauppsättning i det här exemplet.  

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
Pipelinen har två aktiviteter: **AzureMLBatchExecution** och **AzureMLUpdateResource**. Azure ML-batchkörningsaktivitet tar träningsdata som indata och genererar en iLearner-fil som utdata. Aktiviteten anropar webbtjänsten utbildning (träningsexperiment visas som en webbtjänst) med utbildningsdata som indata och tar emot den ilearner-fil från webbtjänsten. PlaceholderBlob är bara en dummy utdata-datauppsättning som krävs av Azure Data Factory-tjänsten att köra en pipeline.

![diagram med datapipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

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
