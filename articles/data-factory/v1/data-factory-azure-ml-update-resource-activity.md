---
title: Uppdatera Machine Learning-modeller med hjälp av Azure Data Factory | Microsoft Docs
description: Beskriver hur du skapar skapa förutsägande pipelines med hjälp av Azure Data Factory och Azure Machine Learning
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
ms.openlocfilehash: d1d4c847916b76785cbee40a12b59387498fb5c5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621225"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Uppdatera Azure Machine Learning-modeller med Uppdateringsresursaktivitet

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-aktivitet](data-factory-hive-activity.md) 
> * [Pig-aktivitet](data-factory-pig-activity.md)
> * [MapReduce Activity](data-factory-map-reduce.md)
> * [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Machine Learning Batch-körningsaktivitet](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning-uppdateringsresursaktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [Anpassad aktivitet för .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [uppdatera machine learning-modeller i Data Factory version 2](../update-machine-learning-models.md).

Den här artikeln kompletterar den huvudsakliga version av Azure Data Factory - Azure Machine Learning integration artikel: [skapa förutsägande pipelines med hjälp av Azure Machine Learning och Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Om du inte redan har gjort granska huvudsakliga artikeln innan du läser igenom den här artikeln. 

## <a name="overview"></a>Översikt
Förutsägelsemodeller i Azure ML bedömningen experiment måste vara retrained med nya indatauppsättningar med tiden. När du är klar med omtränings som du vill uppdatera bedömningsprofil webbtjänsten med retrained ML-modell. Vanliga åtgärder för att aktivera omtränings och uppdaterar Azure ML-modeller via webbtjänster är:

1. Skapa ett experiment i [Azure ML Studio](https://studio.azureml.net).
2. När du är nöjd med modellen, använda Azure ML Studio för att publicera webbtjänster för både den **träningsexperiment** och poängsättning /**prediktivt experiment**.

I följande tabell beskrivs de webbtjänster som används i det här exemplet.  Se [träna om Machine Learning-modeller via programmering](../../machine-learning/machine-learning-retrain-models-programmatically.md) mer information.

- **Utbildning webbtjänsten** – tar emot träningsdata och producerar tränade modeller. Utdata från den omtränings är en .ilearner-fil i ett Azure Blob storage. Den **standard endpoint** skapas automatiskt för dig när du publicerar utbildningen experiment som en webbtjänst. Du kan skapa flera slutpunkter, men i exemplet används endast standardslutpunkten.
- **Bedömningen webbtjänsten** – tar emot exempel omärkta data och gör förutsägelser. Utdata för förutsägelse kan ha olika former, till exempel en CSV-fil eller rader i en Azure SQL database, beroende på konfigurationen av experimentet. Standardslutpunkten skapas automatiskt för dig när du publicerar prediktivt experiment som en webbtjänst. 

Följande bild visar relationen mellan utbildning och poängberäkningen slutpunkter i Azure ML.

![Webbtjänster](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Du kan anropa den **utbildning webbtjänsten** med hjälp av den **Azure ML-Batchkörningsaktivitet**. Anropa en webbtjänst för utbildning är samma som anropar en Azure ML-webbtjänst (bedömningen webbtjänsten) för bedömningsprofil data. I föregående avsnitt beskriver hur du anropa en Azure ML-webbtjänst från ett Azure Data Factory-pipelinen i detalj. 

Du kan anropa den **bedömningen webbtjänsten** med hjälp av den **Azure ML uppdatera resurs aktiviteten** uppdatera webbtjänsten med den nyligen tränade modellen. I följande exempel innehåller definitioner av länkade tjänsten: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>Bedömningen webbtjänst är en klassiska webbtjänst
Om bedömningsprofil webbtjänsten är en **klassiska webbtjänsten**, skapa andra **inte är standard och uppdateras endpoint** med hjälp av Azure portal. Se [skapa slutpunkter](../../machine-learning/machine-learning-create-endpoint.md) artikel anvisningar. När du har skapat icke-uppdateringsbar standardslutpunkten gör du följande steg:

* Klicka på **BATCH EXECUTION** att hämta URI-värdet för den **mlEndpoint** JSON-egenskapen.
* Klicka på **uppdatering resurs** länken för att visa URI-värdet för den **updateResourceEndpoint** JSON-egenskapen. API-nyckeln finns på sidan endpoint sig själv (i det nedre högra hörnet).

![Uppdatera slutpunkten](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

Följande exempel visar ett exempel JSON-definitionen för länkad AzureML-tjänst. Den länkade tjänsten använder apiKey för autentisering.  

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

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Bedömningen webbtjänsten är Azure Resource Manager-webbtjänst 
Om webbtjänsten är den nya typen av webbtjänsten som Exponerar en Azure Resource Manager-slutpunkt, behöver du inte lägga till andra **icke-förvalt** slutpunkt. Den **updateResourceEndpoint** i den länkade tjänsten är i formatet: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Du kan hämta värden för platshållare i Webbadressen när du frågar webbtjänsten på den [Azure Machine Learning Web Services-portalen](https://services.azureml.net/). Ny typ av uppdatering resurs slutpunkten kräver ett token för AAD (Azure Active Directory). Ange **servicePrincipalId** och **servicePrincipalKey**i AzureML länkade tjänsten. Se [så att skapa tjänstens huvudnamn och tilldela behörigheter för att hantera Azure-resurs](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Här är ett exempel på en länkad AzureML-tjänstedefinition: 

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

Följande scenario visar mer information. Det finns ett exempel för omtränings och uppdatera Azure ML modeller från ett Azure Data Factory-pipelinen.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scenario: omtränings och uppdaterar en Azure ML-modell
Det här avsnittet innehåller en exempel-pipeline som använder den **Azure ML-batchkörning aktiviteten** att träna om en modell. Pipeline använder också den **Azure ML uppdatera resurs aktiviteten** att uppdatera modellen i bedömningsprofil webbtjänsten. Avsnittet innehåller också kodstycken JSON för alla länkade tjänster, datauppsättningar och pipeline i exemplet.

Här är diagramvyn för exempel pipeline. Som du kan se Azure ML-Batchkörningsaktivitet tar utbildning indata och skapar en utbildning utdata (iLearner-fil). Azure ML-Uppdateringsresursaktivitet tar den här utbildning utdata och uppdaterar modellen i bedömningsprofil webbtjänstslutpunkt. Den Uppdateringsresursaktivitet genererar inga utdata. PlaceholderBlob är bara en dummy datamängd för utdata som krävs av Azure Data Factory-tjänsten för att köra pipelinen.

![Pipeline-diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Azure Blob storage länkade tjänsten:
Azure Storage innehåller följande information:

* utbildningsdata. Indata för webbtjänsten Azure ML utbildning.  
* iLearner-fil. Utdata från Azure ML utbildning webbtjänsten. Den här filen är också indata till aktiviteten uppdatera resurs.  

Här är exempel JSON-definitionen för den länkade tjänsten:

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

### <a name="training-input-dataset"></a>Utbildning inkommande datauppsättningen:
Följande datauppsättningen representerar inkommande utbildning data för webbtjänsten Azure ML utbildning. Azure ML-batchkörning aktiviteten tar den här datauppsättningen som indata.

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

### <a name="training-output-dataset"></a>Utbildning datamängd för utdata:
Följande datauppsättningen representerar iLearner utdatafilen från Azure ML utbildning webbtjänsten. Azure ML-Batchkörningsaktivitet ger denna dataset. Den här datauppsättningen är också indata till aktiviteten Azure ML Update resurs.

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

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Länkad tjänst för Azure ML utbildning slutpunkt
Följande JSON-fragment definierar en Azure Machine Learning länkade tjänst som pekar på standardslutpunkten av utbildning web service.

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

I **Azure ML Studio**, gör följande för att hämta värden för **mlEndpoint** och **apiKey**:

1. Klicka på **WEB SERVICES** på den vänstra menyn.
2. Klicka på den **utbildning webbtjänsten** i listan över webbtjänster.
3. Klicka på Kopiera bredvid **API-nyckel** textruta. Klistra in nyckeln i Urklipp i Data Factory JSON-redigerare.
4. I den **Azure ML studio**, klickar du på **BATCH EXECUTION** länk.
5. Kopiera den **Begärd URI** från den **begära** avsnittet och klistra in den i Data Factory JSON-redigerare.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Länkad tjänst för Azure ML uppdateras bedömningsprofil slutpunkt:
Följande JSON-fragment definierar en Azure Machine Learning länkade tjänst som pekar på icke-uppdateras standardslutpunkten av webbtjänsten för bedömningsprofil.  

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

### <a name="placeholder-output-dataset"></a>Platshållare för utdatauppsättningen:
Azure ML-uppdatering resurshanteraren aktiviteten genererar inte inga utdata. Azure Data Factory kräver dock en datamängd för utdata för att ge schemat för en pipeline. Därför kan använder vi en datamängd dummy/platshållare i det här exemplet.  

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
Pipelinen har två aktiviteter: **AzureMLBatchExecution** och **AzureMLUpdateResource**. Azure ML-batchkörning aktiviteten hämtar utbildning data som indata och genererar en iLearner-fil som utdata. Aktiviteten anropar webbtjänsten utbildning (träningsexperiment visas som en webbtjänst) med data som indata utbildning och tar emot den ilearner-fil från den här webbtjänsten. PlaceholderBlob är bara en dummy datamängd för utdata som krävs av Azure Data Factory-tjänsten för att köra pipelinen.

![Pipeline-diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

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
