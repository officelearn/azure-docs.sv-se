---
title: Uppdatera maskininlärningsmodeller med Azure Data Factory
description: Beskriver hur du skapar skapa förutsägande pipelines med Hjälp av Azure Data Factory och maskininlärning
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 3313c9c362a9b82cf7ed8db63479aaa5cf0c777e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73683239"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Uppdatera Azure Machine Learning-modeller med hjälp av aktiviteten Uppdatera resurs
Den här artikeln kompletterar huvudartikeln för Azure Data Factory – Azure Machine Learning-integrering: [Skapa förutsägande pipelines med Azure Machine Learning och Azure Data Factory](transform-data-using-machine-learning.md). Om du inte redan har gjort det, granska huvudartikeln innan du läser igenom den här artikeln.

## <a name="overview"></a>Översikt
Som en del av processen att operationalisera Azure Machine Learning-modeller tränas och sparas din modell. Du använder den sedan för att skapa en förutsägande webbtjänst. Webbtjänsten kan sedan förbrukas på webbplatser, instrumentpaneler och mobilappar.

Modeller som du skapar med maskininlärning är vanligtvis inte statiska. När nya data blir tillgängliga eller när konsumenten av API:et har sina egna data måste modellen omskolas. Mer information om hur du kan träna om en modell i Azure Machine Learning finns [i Omskola en maskininlärningsmodell.](../machine-learning/machine-learning-retrain-machine-learning-model.md)

Omskolning kan förekomma ofta. Med batchkörningsaktivitet och uppdateringsresursaktivitet kan du operationalisera omskolningsmodellen för Azure Machine Learning och uppdatera den prediktiva webbtjänsten med Data Factory.

Följande bild visar förhållandet mellan utbildning och prediktiva webbtjänster.

![Webbtjänster](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Azure Machine Learning uppdatera resursaktivitet

Följande JSON-kodavsnitt definierar en Azure Machine Learning Batch Execution-aktivitet.

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```

| Egenskap                      | Beskrivning                              | Krävs |
| :---------------------------- | :--------------------------------------- | :------- |
| namn                          | Namnet på den verksamhet som är på gång     | Ja      |
| description                   | Text som beskriver vad aktiviteten gör.  | Inga       |
| typ                          | För Azure Machine Learning Update Resource-aktivitet är aktivitetstypen **AzureMLUpdateResource**. | Ja      |
| linkedServiceName             | Azure Machine Learning länkad tjänst som innehåller egenskapen updateResourceEndpoint. | Ja      |
| tränatModelName              | Namn på modulen Tränad modell i webbtjänstexperimentet som ska uppdateras | Ja      |
| tränatModelLinkedServiceName | Namn på Azure Storage-länkad tjänst som innehåller ilearnerfilen som överförs av uppdateringsåtgärden | Ja      |
| tränadeModelFilePath          | Den relativa filsökvägen i trainedModelLinkedService för att representera ilearnerfilen som överförs av uppdateringsåtgärden | Ja      |

## <a name="end-to-end-workflow"></a>Arbetsflödet slutpunkt till slutpunkt

Hela processen med att operationalisera omskolning av en modell och uppdatera de prediktiva webbtjänsterna omfattar följande steg:

- Anropa **webbtjänsten för utbildning** med hjälp av **aktiviteten Batchkörning**. Att anropa en webbtjänst för utbildning är detsamma som att anropa en förutsägande webbtjänst som beskrivs i [Skapa prediktiva pipelines med hjälp av Azure Machine Learning och Data Factory Batch Execution- aktivitet](transform-data-using-machine-learning.md). Utdata från utbildningswebbtjänsten är en iLearner-fil som du kan använda för att uppdatera den prediktiva webbtjänsten.
- Anropa **slutpunkten för uppdateringsresurser** för **den prediktiva webbtjänsten** med hjälp av **aktiviteten Uppdatera resurs** för att uppdatera webbtjänsten med den nyligen utbildade modellen.

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst

För att det ovan nämnda arbetsflödet från slutna till slutna verk ska fungera måste du skapa två Azure Machine Learning-länkade tjänster:

1. En Azure Machine Learning-länkad tjänst till utbildningswebbtjänsten, den här länkade tjänsten används av batchkörningsaktivitet på samma sätt som det som nämns i [Skapa prediktiva pipelines med hjälp av Azure Machine Learning och Data Factory Batch Execution- aktivitet](transform-data-using-machine-learning.md). Skillnaden är utdata från utbildningswebbtjänsten är en iLearner-fil som sedan används av aktiviteten Uppdatera resurs för att uppdatera den prediktiva webbtjänsten.
2. En Azure Machine Learning-länkad tjänst till uppdateringsresursslutpunkten för den prediktiva webbtjänsten. Den här länkade tjänsten används av aktiviteten Uppdatera resurs för att uppdatera den prediktiva webbtjänsten med hjälp av iLearner-filen som returneras från ovanstående steg.

För den andra Azure Machine Learning-länkade tjänsten är konfigurationen annorlunda när din Azure Machine Learning Web Service är en klassisk webbtjänst eller en ny webbtjänst. Skillnaderna diskuteras separat i följande avsnitt.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Webbtjänsten är ny webbtjänst för Azure Resource Manager

Om webbtjänsten är den nya typen av webbtjänst som exponerar en Azure Resource **Manager-slutpunkt** behöver du inte lägga till den andra slutpunkten som inte är standard. **UpdateResourceEndpoint** i den länkade tjänsten är av formatet:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Du kan hämta värden för platsinnehavare i URL:en när du frågar webbtjänsten på [Azure Machine Learning Web Services Portal](https://services.azureml.net/).

Den nya typen av slutpunkt för uppdateringsresurser kräver autentisering av tjänstens huvudnamn. Om du vill använda autentisering av tjänstens huvudnamn registrerar du en programentitet i Azure Active Directory (Azure AD) och ger den **rollen deltagare** eller **ägare** för prenumerationen eller resursgruppen där webbtjänsten tillhör. Se [hur du skapar tjänstens huvudnamn och tilldelar behörigheter för att hantera Azure-resurs](../active-directory/develop/howto-create-service-principal-portal.md). Anteckna följande värden, som du använder för att definiera den länkade tjänsten:

- Program-ID:t
- Programnyckel
- Klient-ID:t

Här är ett exempel länkad tjänst definition:

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
                "type": "SecureString",
                "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

Följande scenario innehåller mer information. Den har ett exempel för omskolning och uppdatering av Azure Machine Learning studio-modeller från en Azure Data Factory pipeline.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Exempel: Omskolning och uppdatering av en Azure Machine Learning-modell

Det här avsnittet innehåller ett exempel pipeline som använder **Azure Machine Learning studio Batch Execution aktivitet** för att omskola en modell. Pipelinen använder också **Azure Machine Learning studio Update Resource-aktiviteten** för att uppdatera modellen i bedömningswebbtjänsten. Avsnittet innehåller också JSON-utdrag för alla länkade tjänster, datauppsättningar och pipeline i exemplet.

### <a name="azure-blob-storage-linked-service"></a>Azure Blob storage-länkad tjänst:
Azure Storage innehåller följande data:

* utbildningsdata. Indata för webbtjänsten för Azure Machine Learning studio training.
* iLearner-fil. Utdata från webbtjänsten för utbildning i Azure Machine Learning studio. Den här filen är också indata till aktiviteten Uppdatera resurs.

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Slutpunkt för länkad tjänst för Azure Machine Learning studio-utbildningsslutpunkt
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

I **Azure Machine Learning Studio**gör du följande för att hämta värden för **mlEndpoint** och **apiKey:**

1. Klicka på **WEBBTJÄNSTER** på den vänstra menyn.
2. Klicka på **webbtjänsten för utbildning** i listan över webbtjänster.
3. Klicka på kopiera bredvid textrutan **för API-tangenten.** Klistra in nyckeln i Urklipp i Data Factory JSON-redigeraren.
4. Klicka på länken BATCH **EXECUTION** i **Azure Machine Learning-studion.**
5. Kopiera **URI-begäran från** avsnittet **Begär** och klistra in den i Data Factory JSON-redigeraren.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Länkad tjänst för Azure Machine Learning studio uppdateringsbara bedömning slutpunkt:
Följande JSON-kodavsnitt definierar en Azure Machine Learning-länkad tjänst som pekar på den uppdateringsbara slutpunkten för den poängsättningswebbtjänsten.

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

### <a name="pipeline"></a>Pipeline
Pipelinen har två aktiviteter: **AzureMLBatchExecution** och **AzureMLUpdateResource**. Aktiviteten Fördränlig körning tar utbildningsdata som indata och producerar en iLearner-fil som utdata. Aktiviteten Uppdatera resurs tar sedan den här iLearner-filen och använder den för att uppdatera den prediktiva webbtjänsten.

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        },
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        }
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/output"
                        }
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                        "type": "LinkedServiceReference",
                        "referenceName": "StorageLinkedService"
                    },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [
                    {
                        "activity": "amlbeGetilearner",
                        "dependencyConditions": [ "Succeeded" ]
                    }
                ]
            }
        ]
    }
}
```
## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omvandlar data på andra sätt:

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Grisaktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
