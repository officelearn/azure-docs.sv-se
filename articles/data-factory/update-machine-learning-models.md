---
title: Uppdatera Machine Learning-modeller med hjälp av Azure Data Factory
description: Beskriver hur du skapar skapa förutsägande pipelines med Azure Data Factory-och Machine Learning
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
ms.openlocfilehash: e8fb39e8762d31f00029a0eeea33f1e630fb15a6
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927419"
---
# <a name="update-ml-studio-classicv-models-by-using-update-resource-activity"></a>Uppdatera ML Studio (klassiska) v-modeller genom att använda uppdatering av resurs aktivitet

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln kompletterar huvud artikeln Azure Data Factory-ML Studio (klassisk): [skapa förutsägbara pipelines med hjälp av Azure Machine Learning och Azure Data Factory](transform-data-using-machine-learning.md). Läs igenom huvud artikeln innan du läser igenom den här artikeln, om du inte redan gjort det.

## <a name="overview"></a>Översikt
Som en del av processen att använda ML Studio (klassiska) modeller, tränas modellen och sparas. Sedan kan du använda den för att skapa en förutsägbar webb tjänst. Webb tjänsten kan sedan förbrukas på webbplatser, instrument paneler och mobila appar.

Modeller som du skapar med hjälp av Machine Learning är vanligt vis inte statiska. När nya data blir tillgängliga eller när klienten för API: et har sina egna data måste modellen omtränas. 

Omskolning kan ske ofta. Med aktivitet för batch-körning och uppdatering av resurser kan du operationalisera Azure Machine Learning modell omträningen och uppdatera den förutsägbara webb tjänsten med hjälp av Data Factory.

Följande bild illustrerar förhållandet mellan utbildning och förutsägbara webb tjänster.

![Webbtjänster](./media/update-machine-learning-models/web-services.png)

## <a name="ml-studio-classic-update-resource-activity"></a>ML Studio (klassisk) uppdatera resurs aktivitet

Följande JSON-kodfragment definierar en ML Studio (klassisk) batch execution-aktivitet.

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
| name                          | Namn på aktiviteten i pipelinen     | Ja      |
| description                   | Text som beskriver vad aktiviteten gör.  | Inga       |
| typ                          | För Azure Machine Learning uppdatera resurs aktivitet är aktivitets typen **AzureMLUpdateResource**. | Ja      |
| linkedServiceName             | Azure Machine Learning länkad tjänst som innehåller egenskapen updateResourceEndpoint. | Ja      |
| trainedModelName              | Namnet på den tränade modell modulen i webb tjänst experimentet som ska uppdateras | Ja      |
| trainedModelLinkedServiceName | Namnet på Azure Storage länkade tjänsten som innehåller den ilearner-fil som överförs av uppdaterings åtgärden | Ja      |
| trainedModelFilePath          | Den relativa fil Sök vägen i trainedModelLinkedService för att representera ilearner-filen som överförs av uppdaterings åtgärden | Ja      |

## <a name="end-to-end-workflow"></a>Arbetsflödet slutpunkt till slutpunkt

Hela processen med att använda återträna en modell och uppdatera de förutsägbara webb tjänsterna omfattar följande steg:

- Anropa **webb tjänsten utbildning** med hjälp av **aktiviteten för batch-körning**. Att anropa en utbildnings webb tjänst är detsamma som att anropa en förutsägbar webb tjänst som beskrivs i [skapa förutsägande pipelines med hjälp av Azure Machine Learning och Data Factory batch-körnings aktivitet](transform-data-using-machine-learning.md). Resultatet av webb tjänsten utbildning är en iLearner-fil som du kan använda för att uppdatera den förutsägbara webb tjänsten.
- Anropa **uppdaterings resursens slut punkt** för den **förutsägbara webb tjänsten** med hjälp av **aktiviteten uppdatera resurs** för att uppdatera webb tjänsten med den nyligen utbildade modellen.

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning länkad tjänst

För att det ovan nämnda arbets flödet från slut punkt till slut punkt ska fungera måste du skapa två Azure Machine Learning länkade tjänster:

1. En Azure Machine Learning länkad tjänst till webb tjänsten Training används den här länkade tjänsten av aktivitet för batch-körning på samma sätt som i [skapa förutsägande pipelines med hjälp av Azure Machine Learning och Data Factory batch-körnings aktivitet](transform-data-using-machine-learning.md). Skillnad är resultatet av webb tjänsten utbildning är en iLearner-fil som sedan används av uppdatera resurs aktivitet för att uppdatera den förutsägbara webb tjänsten.
2. En Azure Machine Learning länkad tjänst till resurs slut punkten för uppdateringen av den förutsägbara webb tjänsten. Den här länkade tjänsten används av uppdatera resurs aktivitet för att uppdatera den förutsägbara webb tjänsten med hjälp av iLearner-filen som returneras från föregående steg.

För den andra Azure Machine Learning länkade tjänsten är konfigurationen annorlunda när Azure Machine Learning-webbtjänsten är en klassisk webb tjänst eller en ny webb tjänst. Skillnaderna beskrivs separat i följande avsnitt.

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Webb tjänsten är ny Azure Resource Manager-webbtjänst

Om webb tjänsten är den nya typen av webb tjänst som exponerar en Azure Resource Manager-slutpunkt behöver du inte lägga till den andra slut punkten som **inte är standard** . **UpdateResourceEndpoint** i den länkade tjänsten har formatet:

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

Du kan hämta värden för plats hållare i URL: en när du frågar webb tjänsten på [Azure Machine Learning Web Services-portalen](https://services.azureml.net/).

Den nya typen av resurs slut punkt för uppdateringar kräver tjänstens huvud namns autentisering. Om du vill använda tjänstens huvud namns autentisering registrerar du en programentitet i Azure Active Directory (Azure AD) och ger rollen som **deltagare** eller **ägare** till den prenumeration eller resurs grupp där webb tjänsten tillhör. Se [hur du skapar tjänstens huvud namn och tilldelar behörigheter för att hantera Azure-resurser](../active-directory/develop/howto-create-service-principal-portal.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

- Program-ID:t
- Program nyckel
- Klientorganisations-ID

Här är en exempel på en länkad tjänst definition:

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

Följande scenario innehåller mer information. Det finns ett exempel på att omträna och uppdatera Azure Machine Learning Studio-modeller från en Azure Data Factory-pipeline.


## <a name="sample-retraining-and-updating-an-azure-machine-learning-model"></a>Exempel: omträning och uppdatering av en Azure Machine Learning modell

Det här avsnittet innehåller en exempel pipeline som använder **batch-körningen Azure Machine Learning Studio** för att omträna en modell. Pipelinen använder också **resurs aktiviteten Azure Machine Learning Studio-uppdatering** för att uppdatera modellen i poängsättnings webb tjänsten. Avsnittet innehåller också JSON-kodfragment för alla länkade tjänster, data uppsättningar och pipeline i exemplet.

### <a name="azure-blob-storage-linked-service"></a>Länkad Azure Blob Storage-tjänst:
Azure Storage innehåller följande data:

* tränings data. Indata för webb tjänsten Azure Machine Learning Studio Training.
* iLearner-fil. Utdata från webb tjänsten Azure Machine Learning Studio Training. Den här filen är också indata för resurs aktiviteten uppdatera.

Här är exempel-JSON-definitionen för den länkade tjänsten:

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

### <a name="linked-service-for-azure-machine-learning-studio-training-endpoint"></a>Länkad tjänst för Azure Machine Learning Studio-utbildnings slut punkt
Följande JSON-kodfragment definierar en Azure Machine Learning länkad tjänst som pekar på standard slut punkten för utbildnings webb tjänsten.

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

I **Azure Machine Learning Studio**gör du följande för att hämta värden för **mlEndpoint** och **apiKey**:

1. Klicka på **webb tjänster** på den vänstra menyn.
2. Klicka på **webb tjänsten utbildning** i listan med webb tjänster.
3. Klicka på Kopiera bredvid text rutan **API-nyckel** . Klistra in nyckeln i Urklipp i Data Factory JSON-redigeraren.
4. I **Azure Machine Learning Studio**klickar du på länken för **batch-körning** .
5. Kopiera **begärd URI** från avsnittet **begäran** och klistra in den i Data Factory JSON-redigeraren.

### <a name="linked-service-for-azure-machine-learning-studio-updatable-scoring-endpoint"></a>Länkad slut punkt för den länkade tjänsten för Azure Machine Learning Studio:
Följande JSON-kodfragment definierar en Azure Machine Learning länkad tjänst som pekar på den uppdaterings bara slut punkten i bedömnings webb tjänsten.

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
Pipelinen har två aktiviteter: **AzureMLBatchExecution** och **AzureMLUpdateResource**. Aktiviteten kör batch-körning använder tränings data som indata och skapar en iLearner-fil som utdata. Uppdaterings resursens aktivitet tar sedan den här iLearner-filen och använder den för att uppdatera den förutsägbara webb tjänsten.

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
Se följande artiklar som förklarar hur du omformar data på andra sätt:

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Aktivitet i gris](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Lagrad procedur aktivitet](transform-data-using-stored-procedure.md)
