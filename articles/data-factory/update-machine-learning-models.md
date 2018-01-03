---
title: "Uppdatera machine learning-modeller med hjälp av Azure Data Factory | Microsoft Docs"
description: "Beskriver hur du skapar skapa förutsägande pipelines med Azure Data Factory och maskininlärning"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: a33855213c4bd3a677c8ebbed6624c85138d8ea6
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="update-azure-machine-learning-models-by-using-update-resource-activity"></a>Uppdatera Azure Machine Learning-modeller med hjälp av uppdateringen resurs aktivitet
Den här artikeln kompletterar den huvudsakliga version av Azure Data Factory - Azure Machine Learning integration artikel: [skapa förutsägande pipelines med hjälp av Azure Machine Learning och Azure Data Factory](transform-data-using-machine-learning.md). Om du inte redan har gjort granska huvudsakliga artikeln innan du läser igenom den här artikeln. 

## <a name="overview"></a>Översikt
Som en del av processen för operationalizing Azure Machine Learning-modeller, din modell tränas och sparas. Du sedan använda den för att skapa en predicative webbtjänst. Webbtjänsten kan sedan användas i webbplatser, instrumentpaneler och mobilappar.

Modeller som du skapar med Machine Learning finns vanligtvis inte statisk. När nya data blir tillgängliga eller när konsumenten API har sina egna data måste vara retrained modellen. Referera till [träna om en Maskininlärningsmodell](../machine-learning/machine-learning-retrain-machine-learning-model.md) mer information om hur du kan träna om en modell i Azure Machine Learning. 

Omtränings kan inträffa ofta. Med Batch Execution och uppdatera resurs aktiviteten, kan du operationalisera Azure Machine Learning-modellen omtränings och uppdaterar förutsägande webbtjänsten med hjälp av Data Factory. 

Följande bild visar relationen mellan utbildning och förutsägbara webbtjänster. 

![Webbtjänster](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-update-resource-activity"></a>Azure Machine Learning-uppdateringsresursaktivitet 

Följande JSON-fragment definierar en Azure Machine Learning Batch Execution-aktivitet.

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
| namn                          | Namnet på aktivitet i pipelinen     | Ja      |
| description                   | Text som beskriver hur aktiviteten ska hantera.  | Nej       |
| typ                          | För Azure Machine Learning Update resurs aktiviteten aktivitetstypen är **AzureMLUpdateResource**. | Ja      |
| linkedServiceName             | Azure Machine Learning länkade tjänst som innehåller egenskapen updateResourceEndpoint. | Ja      |
| trainedModelName              | Namnet på den Trained Model-modulen i webbtjänstexperimentet som ska uppdateras | Ja      |
| trainedModelLinkedServiceName | Namn på länkad Azure Storage-tjänst som håller den ilearner-fil som överförs av uppdateringsåtgärden | Ja      |
| trainedModelFilePath          | Den relativa sökvägen i trainedModelLinkedService som representerar den ilearner-fil som överförs av uppdateringsåtgärden | Ja      |


## <a name="end-to-end-workflow"></a>Arbetsflödet slutpunkt till slutpunkt

Hela processen med operationalizing omtränings en modell och uppdatera förutsägande webbtjänster omfattar följande steg: 

- Anropa den **utbildning webbtjänsten** med hjälp av den **Batch Execution aktiviteten**. Anropar utbildning webbtjänsten är samma som anropar en förutsägbar webbtjänst som beskrivs i [skapa förutsägande pipelines som använder Azure Machine Learning och Data Factory Batch Execution aktiviteten](transform-data-using-machine-learning.md). Utdata från utbildning Web Service är en iLearner-fil som du kan använda för att uppdatera förutsägande webbtjänsten. 
- Anropa den **uppdatera resurs slutpunkten** av den **förutsägande webbtjänsten** med hjälp av den **uppdatera resurs aktiviteten** uppdatera webbtjänsten med den nyligen tränade modellen. 

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst

För ovan nämnda slutpunkt till slutpunkt arbetsflödet ska fungera måste du skapa två Azure Machine Learning länkade tjänster: 

1. En Azure Machine Learning länkad tjänst för utbildning web service, den här länkade tjänsten används av Batch Execution aktiviteten på samma sätt som vad som nämns i [skapa förutsägande pipelines med hjälp av Azure Machine Learning och Data Factory Batch Körning av aktiviteten](transform-data-using-machine-learning.md). Skillnaden är resultatet av utbildning web service är en iLearner-fil som sedan används genom att uppdatera resursen aktiviteten för att uppdatera förutsägande webbtjänsten. 
2. En Azure Machine Learning länkad tjänst för uppdatering resurs slutpunkten för förutsägande webbtjänsten. Den här länkade tjänsten används genom att uppdatera resursen aktiviteten för att uppdatera förutsägande webbtjänsten med hjälp av filen iLearner returnerade ovan steg. 

Konfigurationen är olika för andra Azure Machine Learning länkad tjänst när din Azure Machine Learning-webbtjänst är en klassiska webbtjänst eller en ny webbtjänst. Skillnaderna beskrivs separat i följande avsnitt. 

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Webbtjänsten är nya Azure Resource Manager-webbtjänst 

Om webbtjänsten är den nya typen av webbtjänsten som Exponerar en Azure Resource Manager-slutpunkt, behöver du inte lägga till andra **icke-förvalt** slutpunkt. Den **updateResourceEndpoint** i den länkade tjänsten är i formatet: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Du kan hämta värden för platshållare i Webbadressen när du frågar webbtjänsten på den [Azure Machine Learning Web Services-portalen](https://services.azureml.net/). 

Ny typ av uppdatering resurs slutpunkten kräver huvudnamn autentiseringen av tjänsten. Du använder service principal autentisering, registrera en Programenhet i Azure Active Directory (Azure AD) och bevilja den **deltagare** eller **ägare** rollen för prenumerationen eller resursen gruppen var webbtjänsten tillhör. Finns [så att skapa tjänstens huvudnamn och tilldela behörigheter för att hantera Azure-resurs](../azure-resource-manager/resource-group-create-service-principal-portal.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

- Program-ID:t
- Nyckeln för programmet 
- Klient-ID:t

Här är ett exempel länkade tjänstdefinitionen: 

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
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. ",
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

Följande scenario visar mer information. Det finns ett exempel för omtränings och uppdatera Azure ML modeller från ett Azure Data Factory-pipelinen.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Exempel: Omtränings och uppdaterar en Azure Machine Learning-modell

Det här avsnittet innehåller en exempel-pipeline som använder den **Azure ML-batchkörning aktiviteten** att träna om en modell. Pipeline använder också den **Azure ML uppdatera resurs aktiviteten** att uppdatera modellen i bedömningsprofil webbtjänsten. Avsnittet innehåller också kodstycken JSON för alla länkade tjänster, datauppsättningar och pipeline i exemplet.

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
Följande JSON-fragment definierar en Azure Machine Learning länkade tjänst som pekar på uppdateras slutpunkten bedömningsprofil webbtjänsten.  

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
Pipelinen har två aktiviteter: **AzureMLBatchExecution** och **AzureMLUpdateResource**. Batch Execution aktiviteten hämtar utbildning data som indata och genererar en iLearner-fil som utdata. Uppdatera resurs-aktiviteten sedan tar iLearner-fil och använda den för att uppdatera förutsägande webbtjänsten. 

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
Se följande artiklar som förklarar hur du Transformera data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Pig-aktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Aktiviteten lagrad procedur](transform-data-using-stored-procedure.md)
