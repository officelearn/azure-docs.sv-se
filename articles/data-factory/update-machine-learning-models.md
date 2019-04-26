---
title: Uppdatera machine learning-modeller med Azure Data Factory | Microsoft Docs
description: Beskriver hur du skapar skapa förutsägande pipelines med Azure Data Factory och maskininlärning
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: 8f1320db0af85f6c83a9daf8e17a691336c9b251
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60335492"
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Uppdatera Azure Machine Learning-modeller med hjälp av resursuppdatering aktivitet
Den här artikeln kompletterar huvudsakliga Azure Data Factory - artikeln för Azure Machine Learning-integrering: [Skapa förutsägande pipelines med Azure Machine Learning och Azure Data Factory](transform-data-using-machine-learning.md). Om du inte redan gjort det, kan du läsa den huvudsakliga artikeln innan du läser igenom den här artikeln.

## <a name="overview"></a>Översikt
Som en del av processen för att operationalisera Azure Machine Learning-modeller, är din modell tränas och sparas. Du sedan använda den för att skapa en förutsägbar webbtjänst. Webbtjänsten kan sedan användas i webbplatser, instrumentpaneler och mobila appar.

Modeller som du skapar med Machine Learning är vanligtvis inte statiska. När nya data blir tillgängliga eller när användaren av API: et har sina egna data måste vara modellkomponenten modellen. Referera till [träna en Maskininlärningsmodell](../machine-learning/machine-learning-retrain-machine-learning-model.md) mer information om hur du kan träna en modell i Azure Machine Learning.

Träna kan inträffa ofta. Med Batch-körningsaktivitet och resursuppdatering aktivitet kan driftsätta du modellen Azure Machine Learning retraining och uppdatera förutsägande webbtjänsten med Data Factory.

Följande bild visar förhållandet mellan upplärning och förutsägande webbtjänster.

![Webbtjänster](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Azure Machine Learning-uppdateringsresursaktivitet

Följande JSON-kodfragmentet definierar en Azure Machine Learning Batch Execution-aktivitet.

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

| Egenskap                       | Beskrivning                              | Obligatoriskt |
| :---------------------------- | :--------------------------------------- | :------- |
| namn                          | Namnet på aktiviteten i pipelinen     | Ja      |
| description                   | Text som beskriver hur aktiviteten ska hantera.  | Nej       |
| typ                          | För Azure Machine Learning-resursuppdatering aktivitet, är aktivitetstypen **AzureMLUpdateResource**. | Ja      |
| linkedServiceName             | Azure Machine Learning länkade tjänst som innehåller egenskapen updateResourceEndpoint. | Ja      |
| trainedModelName              | Namnet på Trained Model-modul i webbtjänstexperimentet som ska uppdateras | Ja      |
| trainedModelLinkedServiceName | Namnet på Azure Storage-länkade tjänst som håller den ilearner-fil som överförs av uppdateringsåtgärden | Ja      |
| trainedModelFilePath          | Den relativa filsökvägen i trainedModelLinkedService som representerar den ilearner-fil som överförs av uppdateringsåtgärden | Ja      |

## <a name="end-to-end-workflow"></a>Arbetsflödet slutpunkt till slutpunkt

Hela processen med att operationalisera träna en modell och uppdatera förutsägande webbtjänster omfattar följande steg:

- Anropa den **utbildning webbtjänsten** med hjälp av den **batchkörningsaktivitet**. Anropar ett utbildnings-webbtjänsten är detsamma som att anropa en förutsägbar webbtjänst som beskrivs i [skapa förutsägande pipelines med Azure Machine Learning och Data Factory-batchkörning aktivitet](transform-data-using-machine-learning.md). Utdata från utbildning Web Service är en iLearner-fil som du kan använda för att uppdatera förutsägande webbtjänsten.
- Anropa den **uppdatera resursslutpunkt** av den **förutsägande webbtjänsten** med hjälp av den **aktivitet uppdatera resurs** att uppdatera webbtjänsten med den nyligen tränade modellen.

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst

Av ovan nämnda arbetsflödet slutpunkt till slutpunkt ska fungera måste du skapa två Azure Machine Learning länkade tjänster:

1. En Azure Machine Learning-länkad tjänst till webbtjänsten utbildning, den här länkade tjänsten används av Batch-körningsaktivitet på samma sätt som vad som nämns i [skapa förutsägande pipelines med Azure Machine Learning och Data Factory Batch Körningsaktivitet](transform-data-using-machine-learning.md). Skillnaden är utdata från webbtjänsten utbildning är en iLearner-fil som sedan används av aktivitet uppdatera resurs för att uppdatera förutsägbar webbtjänst.
2. En Azure Machine Learning länkad tjänst för att uppdatera resurs slutpunkten för förutsägbar webbtjänst. Den här länkade tjänsten används av aktivitet uppdatera resurs för att uppdatera den förutsägbar webbtjänst med hjälp av den iLearner-fil returneras ovanför steget.

Konfigurationen är olika för andra Azure Machine Learning-länkade tjänsten, när din Azure Machine Learning-webbtjänsten är en klassisk webbtjänst eller en ny webbtjänst. Skillnaderna beskrivs separat i följande avsnitt.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Webbtjänsten är nya Azure Resource Manager-webbtjänst

Om webbtjänsten är den nya typen av webbtjänsten som Exponerar en Azure Resource Manager-slutpunkt, behöver du inte lägga till andra **avviker från standarden** slutpunkt. Den **updateResourceEndpoint** i den länkade tjänsten har följande format:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Du kan hämta värden för platshållare i URL: en vid frågor till webbtjänsten på den [Azure Machine Learning Web Services-portalen](https://services.azureml.net/).

Den nya typen av slutpunkt för resursen kräver autentisering av tjänstens huvudnamn. Att använda autentisering av tjänstens huvudnamn, registrera ett program-entiteten i Azure Active Directory (Azure AD) och bevilja den **deltagare** eller **ägare** rollen för prenumerationen eller resursen gruppen where webbtjänsten tillhör. Se [skapa tjänstens huvudnamn och tilldela behörigheter för att hantera Azure-resurs](../active-directory/develop/howto-create-service-principal-portal.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

- Program-ID:t
- Programnyckel
- Klient-ID:t

Här är en länkad tjänst exempeldefinition:

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

Följande scenario innehåller mer information. Den innehåller ett exempel för att träna och uppdatera Azure Machine Learning studio-modeller från en Azure Data Factory-pipeline.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Exempel: Träna och uppdatera en Azure Machine Learning-modell

Det här avsnittet innehåller ett exempel på pipeline som använder den **Azure Machine Learning studio-batchkörningsaktivitet** att träna en modell. Pipelinen använder också den **Azure Machine Learning studio aktivitet uppdatera resurs** att uppdatera modellen i bedömning av webbtjänsten. Avsnittet ger också JSON-kodfragment för alla länkade tjänster, datauppsättningar och pipeline i det här exemplet.

### <a name="azure-blob-storage-linked-service"></a>Azure Blob storage-länkade tjänst:
Azure Storage innehåller följande data:

* träningsdata. Indata för Azure Machine Learning-studio utbildning för webbtjänsten.
* iLearner-fil. Utdata från Azure Machine Learning studio utbildning för webbtjänsten. Den här filen är också indata till aktiviteten resursuppdatering.

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Länkad tjänst för Azure Machine Learning studio utbildning-slutpunkt
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

I **Azure Machine Learning studio**, gör du följande för att hämta värden för **mlEndpoint** och **apiKey**:

1. Klicka på **WEBBTJÄNSTER** på den vänstra menyn.
2. Klicka på den **utbildning webbtjänsten** i listan över webbtjänster.
3. Klicka på Kopiera bredvid **API-nyckel** textrutan. Klistra in nyckeln i Urklipp i Data Factory JSON-redigerare.
4. I den **Azure Machine Learning studio**, klickar du på **BATCHKÖRNING** länk.
5. Kopiera den **begär URI** från den **begära** avsnittet och klistra in den i Data Factory JSON-redigerare.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Länkad tjänst för Azure Machine Learning studio uppdateras bedömnings slutpunkt:
Följande JSON-kodfragmentet definierar en Azure Machine Learning-länkade tjänst som pekar på uppdateras slutpunkten för bedömning av webbtjänsten.

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
Pipelinen har två aktiviteter: **AzureMLBatchExecution** och **AzureMLUpdateResource**. Batch-körningsaktivitet tar träningsdata som indata och genererar en iLearner-fil som utdata. Uppdatera resurs-aktiviteten sedan tar den här iLearner-fil och använda den för att uppdatera förutsägbar webbtjänst.

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
Se följande artiklar som beskriver hur du omvandlar data på andra sätt:

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Piggningsåtgärd](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
